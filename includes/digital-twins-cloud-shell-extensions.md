---
author: baanders
description: bestand opnemen voor Azure Digital Twins - meest recente IoT-extensie instellen
ms.service: digital-twins
ms.topic: include
ms.date: 7/31/2020
ms.author: baanders
ms.openlocfilehash: 7e43ab6d419d6a08973864838c66aef0941d74f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606244"
---
Voer eerst deze opdracht uit om een lijst weer te geven met alle extensies die u al hebt geïnstalleerd.

```azurecli
az extension list
```

De uitvoer is een matrix van alle extensies die u momenteel hebt. Zoek naar het veld `"name"` voor elk vermelding in de lijst om de namen van de extensies weer te geven.

Gebruik de uitvoer om te bepalen welke van de volgende opdrachten moet worden uitgevoerd voor de extensie-instelling (u kunt er meer dan één uitvoeren).
* Als de lijst `azure-iot` bevat: U hebt de extensie al. Voer deze opdracht uit om te controleren of u over de meest recente update beschikt en er geen updates meer beschikbaar zijn:

   ```azurecli
   az extension update --name azure-iot
   ```

* Als de lijst `azure-iot` **niet** bevat: U moet de extensie installeren. Gebruik deze opdracht:

    ```azurecli
    az extension add --name azure-iot
    ```

* Als de lijst `azure-iot-cli-ext` bevat: Dit is de verouderde versie van de extensie. Er moet slechts één versie van de extensie tegelijkertijd zijn geïnstalleerd. U moet de oude extensie dus verwijderen. Gebruik deze opdracht:

   ```azurecli
   az extension remove --name azure-cli-iot-ext
   ```