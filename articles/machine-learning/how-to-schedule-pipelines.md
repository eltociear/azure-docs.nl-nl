---
title: Azure Machine Learning pijp lijnen plannen
titleSuffix: Azure Machine Learning
description: Met geplande pijp lijnen kunt u routines automatiseren, tijdrovende taken zoals gegevens verwerking,-training en-bewaking.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/12/2019
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 2c931aa6cd170c72654eb9ced6813d26e21ce5d0
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630308"
---
# <a name="schedule-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>machine learning-pijp lijnen plannen met Azure Machine Learning SDK voor python

In dit artikel leert u hoe u een pijp lijn programmatisch kunt plannen om uit te voeren op Azure. U kunt ervoor kiezen om een planning te maken op basis van verstreken tijd of wijzigingen in het bestands systeem. Op tijd gebaseerde schema's kunnen worden gebruikt om routine taken uit te voeren, zoals het controleren op gegevens drift. Planningen op basis van wijzigingen kunnen worden gebruikt om te reageren op onregelmatige of onvoorspelbare wijzigingen, zoals nieuwe gegevens die worden geüpload of oude gegevens die worden bewerkt. Nadat u hebt geleerd hoe u schema's kunt maken, leert u hoe u deze kunt ophalen en deactiveren.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://aka.ms/AMLFree).

* Een python-omgeving waarin de Azure Machine Learning SDK voor python is geïnstalleerd. Zie voor meer informatie [herbruikbare omgevingen maken en beheren voor training en implementatie met Azure machine learning.](how-to-use-environments.md)

* Een Machine Learning-werk ruimte met een gepubliceerde pijp lijn. U kunt het ingebouwde [machine learning-pijp lijnen maken en uitvoeren met Azure machine learning SDK](how-to-create-your-first-pipeline.md)gebruiken.

## <a name="initialize-the-workspace--get-data"></a>De werk ruimte initialiseren & gegevens ophalen

Als u een pijp lijn wilt plannen, moet u een verwijzing naar uw werk ruimte, de id van uw gepubliceerde pijp lijn en de naam van het experiment waarin u de planning wilt maken, hebben. U kunt deze waarden ophalen met de volgende code:

```Python
import azureml.core
from azureml.core import Workspace
from azureml.pipeline.core import Pipeline, PublishedPipeline
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

experiments = Experiment.list(ws)
for experiment in experiments:
    print(experiment.name)

published_pipelines = PublishedPipeline.list(ws)
for published_pipeline in  published_pipelines:
    print(f"{published_pipeline.name},'{published_pipeline.id}'")

experiment_name = "MyExperiment" 
pipeline_id = "aaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" 
```

## <a name="create-a-schedule"></a>Een planning maken

Als u een pijp lijn op een terugkerende basis wilt uitvoeren, maakt u een planning. A `Schedule` koppelt een pijp lijn, een experiment en een trigger. De trigger kan een waarde zijn `ScheduleRecurrence` die de wacht tijd tussen uitvoeringen of een Data Store-pad beschrijft dat een directory opgeeft die moet worden gecontroleerd op wijzigingen. In beide gevallen hebt u de pijp lijn-id en de naam van het experiment nodig om de planning te maken.

Importeer boven aan uw python-bestand de `Schedule` `ScheduleRecurrence` klassen en:

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>Een op tijd gebaseerde planning maken

De `ScheduleRecurrence` constructor bevat een vereist `frequency` argument dat een van de volgende teken reeksen moet zijn: minutes, hours, Day, week of month (maand). Er is ook een argument integer vereist voor `interval` het opgeven van het aantal `frequency` eenheden dat moet worden verstrijkt tussen planning wordt gestart. Optionele argumenten bieden u meer specifieke informatie over de begin tijd, zoals beschreven in de [documenten van de SCHEDULERECURRENCE SDK](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?preserve-view=true&view=azure-ml-py).

Maken van een `Schedule` die elke 15 minuten een uitvoer begint:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>Een planning op basis van wijzigingen maken

Pijp lijnen die worden geactiveerd door bestands wijzigingen zijn mogelijk efficiënter dan op tijd gebaseerde schema's. Het is bijvoorbeeld mogelijk dat u een voor verwerkings stap wilt uitvoeren wanneer een bestand wordt gewijzigd of wanneer een nieuw bestand wordt toegevoegd aan een Data Directory. U kunt wijzigingen in een gegevens opslag of wijzigingen bewaken in een specifieke map in het gegevens archief. Als u een specifieke directory bewaken, wordt een uitvoering _niet_ geactiveerd door wijzigingen in submappen van die map.

Als u een bestand wilt maken `Schedule` , moet u de `datastore` para meter instellen in de aanroep naar [Schedule. Create](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-). Als u een map wilt bewaken, stelt u het `path_on_datastore` argument in.

Met het `polling_interval` argument kunt u in minuten opgeven, de frequentie waarmee het gegevens archief wordt gecontroleerd op wijzigingen.

Als de pijp lijn is gemaakt met een [DataPath](/python/api/azureml-core/azureml.data.datapath.datapath?preserve-view=true&view=azure-ml-py) - [PipelineParameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?preserve-view=true&view=azure-ml-py), kunt u deze variabele instellen op de naam van het gewijzigde bestand door het argument in te stellen `data_path_parameter_name` .

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on input file change.",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Optionele argumenten bij het maken van een planning

Naast de argumenten die eerder zijn beschreven, kunt u het `status` argument instellen op `"Disabled"` om een inactieve planning te maken. Ten slotte `continue_on_step_failure` kunt u met de een Booleaanse waarde door geven die het standaard gedrag van de pijp lijn overschrijft.

### <a name="use-azure-logic-apps-for-more-complex-workflows"></a>Azure Logic Apps gebruiken voor complexere werk stromen

Azure Logic Apps ondersteunt complexere werk stromen en is veel meer breed geïntegreerd dan Azure Machine Learning pijp lijnen. Zie [een uitvoering van een machine learning-pijp lijn vanuit een logische app activeren](how-to-trigger-published-pipeline.md) voor meer informatie.

## <a name="view-your-scheduled-pipelines"></a>Uw geplande pijp lijnen weer geven

Navigeer in uw webbrowser naar Azure Machine Learning. Kies in het gedeelte **endpoints** van het navigatie venster **pijplijn eindpunten**. Hiermee gaat u naar een lijst met de pijp lijnen die zijn gepubliceerd in de werk ruimte.

![Pagina pijp lijnen van AML](./media/how-to-schedule-pipelines/scheduled-pipelines.png)

Op deze pagina kunt u samenvattings informatie weer geven over alle pijp lijnen in de werk ruimte: namen, beschrijvingen, status, enzovoort. Inzoomen door te klikken op de pijp lijn. Op de resulterende pagina vindt u meer informatie over de pijp lijn en kunt u inzoomen op afzonderlijke uitvoeringen.

## <a name="deactivate-the-pipeline"></a>De pijp lijn deactiveren

Als u een hebt `Pipeline` gepubliceerd, maar niet is gepland, kunt u deze uitschakelen met:

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

Als de pijp lijn is gepland, moet u de planning eerst annuleren. De schema-id ophalen uit de portal of door lopen:

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

Wanneer u de `schedule_id` u wilt uitschakelen, voert u de volgende handelingen uit:

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

Als u `Schedule.list(ws)` het opnieuw uitvoert, moet u een lege lijst ophalen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u de Azure Machine Learning SDK voor python gebruikt om een pijp lijn op twee verschillende manieren te plannen. Een planning wordt herhaald op basis van de verstreken klok tijd. Het andere schema wordt uitgevoerd als een bestand wordt gewijzigd op een opgegeven `Datastore` of in een map in dat archief. U hebt gezien hoe u de portal kunt gebruiken om de pijp lijn en de afzonderlijke uitvoeringen te onderzoeken. Ten slotte hebt u geleerd hoe u een schema kunt uitschakelen zodat de pijp lijn stopt met uitvoeren.

Zie voor meer informatie:

> [!div class="nextstepaction"]
> [Azure Machine Learning pijplijnen gebruiken voor batch scores](tutorial-pipeline-batch-scoring-classification.md)

* Meer informatie over [pijp lijnen](concept-ml-pipelines.md)
* Meer informatie over het [verkennen van Azure machine learning met Jupyter](samples-notebooks.md)