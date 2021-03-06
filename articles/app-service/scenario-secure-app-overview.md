---
title: 'Zelfstudie: Een veilige web-app ontwikkelen in Azure App Service | Azure'
description: In deze zelfstudie leert u hoe u een web-app ontwikkelt met Azure App Service, verificatie inschakelt, Azure Storage aanroept en Microsoft Graph aanroept.
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: ef7782c68746d4c20df5a9ae5e27ffca3e60efbe
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024123"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>Zelfstudie: Verificatie inschakelen in App Service en toegang tot opslag en Microsoft Graph krijgen

In deze zelfstudie wordt een veelvoorkomend toepassingsscenario beschreven en hier leert u procedures om het volgende te doen:

- [Verificatie configureren voor een web-app](scenario-secure-app-authentication-app-service.md) en de toegang beperken tot gebruikers in uw organisatie. Bekijk A in het diagram.
- [Veilige toegang tot Azure Storage](scenario-secure-app-access-storage.md) voor de web-app met behulp van beheerde identiteiten. Bekijk B in het diagram.
- Toegang tot gegevens in Microsoft Graph krijgen [voor de aangemelde gebruiker](scenario-secure-app-access-microsoft-graph-as-user.md) of [voor de web-app](scenario-secure-app-access-microsoft-graph-as-app.md) met behulp van beheerde identiteiten. Bekijk C in het diagram.
- [De resources opschonen](scenario-secure-app-clean-up-resources.md) die u voor deze zelfstudie hebt gemaakt.

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="Diagram dat de toepassingsscenario's in het Microsoft-identiteitsplatform toont." border="false":::

Om te beginnen leert u hoe u verificatie inschakelt voor een web-app.

> [!div class="nextstepaction"]
> [Verificatie configureren voor een web-app](scenario-secure-app-authentication-app-service.md)
