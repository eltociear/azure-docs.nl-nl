---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: d567a4f7d9b9429887d6396cb2b03dfc34c6ac93
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88602570"
---
<!-- Create a resource group -->

Gebruik de volgende opdracht om een resource groep te maken. Selecteer de geografische regio die wordt gebruikt om de media-en meta gegevens records voor uw Media Services-account op te slaan. Deze regio wordt gebruikt om uw media te verwerken en te streamen.

```azurecli
az group create --name amsResourceGroup --location westus2
```
