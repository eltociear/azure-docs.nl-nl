---
title: Een HDInsight-cluster verwijderen-Azure
description: Informatie over de verschillende manieren waarop u een Azure HDInsight-cluster kunt verwijderen
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive, devx-track-azurecli
ms.date: 11/29/2019
ms.openlocfilehash: 49a43f821a159af6944abb9509c24a8dd081be43
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92748806"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Een HDInsight-cluster verwijderen met behulp van uw browser, Power shell of de Azure CLI

De facturering voor het gebruik van HDInsight-clusters begint zodra er een cluster is gemaakt en stopt als een cluster wordt verwijderd. Facturering is Pro-beoordeeld per minuut. u moet dus altijd uw cluster verwijderen wanneer het niet langer in gebruik is. In dit document vindt u informatie over het verwijderen van een cluster met behulp van de [Azure Portal](https://portal.azure.com), [Azure PowerShell AZ-module](/powershell/azure/)en de [Azure cli](/cli/azure/).

> [!IMPORTANT]  
> Als u een HDInsight-cluster verwijdert, worden de Azure Storage accounts of Data Lake Storage die aan het cluster zijn gekoppeld, niet verwijderd. U kunt in de toekomst gegevens die in deze services zijn opgeslagen, opnieuw gebruiken.

## <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Ga in het menu links naar **alle services**  >  **Analytics**  >  **HDInsight-clusters** en selecteer uw cluster.

3. Selecteer in de standaard weergave het pictogram **verwijderen** . Volg de prompt om uw cluster te verwijderen.

    ![De knop voor HDInsight verwijderen van het cluster](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Vervang door `CLUSTERNAME` de naam van uw HDInsight-cluster in de onderstaande code. Voer de volgende opdracht uit vanaf een Power shell-prompt om het cluster te verwijderen:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

Vervang door `CLUSTERNAME` de naam van uw HDInsight-cluster en `RESOURCEGROUP` met de naam van uw resource groep in de onderstaande code.  Voer vanaf een opdracht prompt het volgende in om het cluster te verwijderen:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
