---
title: Azure CLI-voorbeeldscript - een toepassing toevoegen in Batch
description: Dit voorbeeldscript laat zien hoe u een toepassing kunt toevoegen voor gebruik met een Azure Batch-groep of een taak.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 414a401168b1b378ed33f4904607243de0267d22
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565803"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>CLI-voorbeeld: Een toepassing toevoegen aan een Azure Batch-account

Dit script laat zien hoe u een toepassing kunt toevoegen voor gebruik met een Azure Batch-groep of -taak. Als u een toepassing wilt instellen om aan uw Batch-account toe te voegen, verpakt u uw uitvoerbare bestand samen met eventuele afhankelijkheden in een .zip-bestand. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Voor deze zelfstudie is versie 2.0.20 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd. 

## <a name="example-script"></a>Voorbeeldscript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep en alle resources die er aan zijn gekoppeld te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt.
Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Hiermee maakt u een opslagaccount. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Hiermee wordt de Batch-account gemaakt. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Hiermee wordt authenticatie uitgevoerd met het opgegeven Batch-account voor verdere interactie met de CLI.  |
| [az batch application create](/cli/azure/batch/application#az-batch-application-create) | Hiermee wordt een toepassing gemaakt.  |
| [az batch application package create](/cli/azure/batch/application/package#az-batch-application-package-create) | Hiermee voegt u een toepassingspakket toe aan de opgegeven toepassing.  |
| [az batch application set](/cli/azure/batch/application#az-batch-application-set) | Hiermee worden de eigenschappen van een toepassing bijgewerkt.  |
| [az group delete](/cli/azure/group#az-group-delete) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.
