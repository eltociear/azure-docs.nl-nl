---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: roy-har
manager: diberry
ms.service: cognitive-services
ms.date: 06/03/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: roy-har
ms.openlocfilehash: 8e67a6d0c98a3839922a79e9b452465087da1b69
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "84418011"
---
1. Selecteer [pizza-app-for-luis-v6.json](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-app-for-luis-v6.json) om de GitHub-pagina voor het `pizza-app-for-luis.json`-bestand te openen.
1. Klik met de rechtermuisknop of tik lang op de knop **Onbewerkt** en selecteer **Koppeling opslaan als** om de `pizza-app-for-luis.json` op uw computer op te slaan.
1. Meld u aan bij de [LUIS-portal](https://www.luis.ai).
1. Selecteer [Mijn apps](https://www.luis.ai/applications).
1. Selecteer op de pagina **Mijn apps** de optie **+ Nieuwe app voor conversatie**.
1. Selecteer **Importeren als JSON**.
1. Selecteer in het dialoogvenster **Nieuwe app importeren** de knop **Bestand kiezen**.
1. Selecteer het `pizza-app-for-luis.json`-bestand dat u hebt gedownload en selecteer **Openen**.
1. Voer in het veld **Naam** in het dialoogvenster **Nieuwe app importeren** een naam in voor uw Pizza-app en selecteer vervolgens de knop **Gereed**.

De app wordt geïmporteerd.

Sluit het dialoogvenster **Een effectieve LUIS-app maken** zodra dit wordt weergegeven.

## <a name="train-and-publish-the-pizza-app"></a>De Pizza-app trainen en publiceren

Als het goed is, wordt de pagina **Intenties** weergegeven, met een lijst van de intenties in de Pizza-app.

[!INCLUDE [How to train](howto-train.md)]

[!INCLUDE [How to publish](howto-publish.md)]

Uw Pizza-app is nu klaar voor gebruik.

## <a name="record-the-app-id-prediction-key-and-prediction-endpoint-of-your-pizza-app"></a>De app-id, de voorspellingssleutel en het voorspellingseindpunt van uw Pizza-app vastleggen

Als u uw nieuwe Pizza-app wilt gebruiken, hebt u de app-id, een voorspellingssleutel en een voorspellingseindpunt van uw Pizza-app nodig.

Deze waarden zoeken:

1. Selecteer op de pagina **Intenties** de optie **BEHEREN**.
1. Noteer de **app-id** op de pagina **Toepassingsinstellingen**.
1. Selecteer **Azure-resources**.
1. Noteer de **primaire sleutel** op de pagina **Azure-resources**. Deze waarde is uw voorspellingssleutel.
1. Noteer de **eindpunt-URL**. Deze waarde is uw voorspellingseindpunt.
