---
title: Meer informatie over de rollen die zijn vereist voor het uitvoeren van algemene taken in Synapse
description: In dit artikel wordt beschreven welke ingebouwde Synapse RBAC-rol (len) vereist zijn om specifieke taken uit te voeren
author: billgib
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: aadc8e817eb2b5de856ac73cfd010b48d0531bfc
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523434"
---
# <a name="understand-the-roles-required-to-perform-common-tasks-in-synapse"></a>Meer informatie over de rollen die zijn vereist voor het uitvoeren van algemene taken in Synapse

Dit artikel helpt u te begrijpen welke Synapse RBAC (op rollen gebaseerd toegangs beheer) of Azure RBAC-rollen u nodig hebt om aan de slag te gaan in Synapse Studio.  

## <a name="synapse-studio-access-control-and-workflow-summary"></a>Synapse Studio-toegangs beheer en werk stroom overzicht 

### <a name="accessing-synapse-studio-and-viewing-its-content"></a>Toegang krijgen tot Synapse Studio en de inhoud weer geven

- U kunt Synapse Studio openen en de details van de werk ruimte weer geven en een lijst met alle Azure-resources (SQL-groepen, Spark-Pools of Integration Runtimes) bekijken als u een Synapse RBAC-rol hebt toegewezen of de rol Azure owner, Inzender of lezer hebt ingesteld in de werk ruimte.

### <a name="resource-management"></a>Resourcebeheer

- U kunt SQL-groepen, Apache Spark Pools en integratie-Runtimes maken als u een Azure-eigenaar of Inzender bent in de werk ruimte.
- U kunt een toegewezen SQL-groep onderbreken of schalen, een Spark-groep of een Integration runtime configureren als u een Azure-eigenaar of Inzender bent in de werk ruimte of de resource.

### <a name="viewing-and-editing-code-artifacts"></a>Code artefacten weer geven en bewerken

- Met toegang tot Synapse Studio kunt u nieuwe code artefacten maken, zoals SQL-scripts, notitie blokken, Spark-taken, gekoppelde services, pijp lijnen, gegevens stromen, triggers en referenties.  (Deze artefacten kunnen worden gepubliceerd of opgeslagen met extra machtigingen.)  
- Als u een Synapse-artefact gebruiker bent, Synapse artefact Uitgever, Synapse Inzender of Synapse-beheerder kunt u al gepubliceerde code artefacten weer geven, openen en bewerken.

### <a name="executing-your-code"></a>De code wordt uitgevoerd

- U kunt SQL-scripts uitvoeren op SQL-groepen als u de benodigde SQL-machtigingen hebt gedefinieerd in de SQL-groepen.  
- U kunt notitie blokken en Spark-taken uitvoeren als u machtigingen voor Synapse Compute-operator hebt voor de werk ruimte of specifieke Apache Spark groepen.  
- Met reken operator machtigingen voor de werk ruimte of specifieke Integration Runtimes en de juiste referentie machtigingen kunt u pijp lijnen uitvoeren.

### <a name="monitoring-and-managing-execution"></a>Uitvoering controleren en beheren
- U kunt de status van het uitvoeren van notitie blokken en taken in Apache Spark Pools controleren als u een Synapse-gebruiker bent.
- U kunt logboeken bekijken en actieve taken en pijp lijnen annuleren als u een Synapse Compute-operator in de werk ruimte bent of voor een specifieke Spark-pool of-pijp lijn.  

### <a name="publishing-and-saving-your-code"></a>Uw code publiceren en opslaan

- U kunt nieuwe of bijgewerkte code artefacten publiceren naar de service als u een Synapse artefact Uitgever, Synapse Inzender of Synapse-beheerder bent. 
- U kunt code artefacten vastleggen in een werkende vertakking van een Git-opslag plaats als de werk ruimte Git-ingeschakeld is en u Git-machtigingen hebt. Als Git is ingeschakeld, is publiceren alleen toegestaan vanuit de vertakking voor samen werking.
- Als u Synapse Studio sluit zonder wijzigingen in code artefacten te publiceren of door te voeren, gaan deze wijzigingen verloren.


## <a name="tasks-and-required-roles"></a>Taken en vereiste rollen

De volgende tabel bevat algemene taken en voor elke taak, de Synapse RBAC of de vereiste Azure RBAC-rollen.  

>[!Note]
>- De Synapse-beheerder wordt voor elke taak niet weer gegeven, tenzij dit de enige rol is die de benodigde machtiging biedt.  Een Synapse-beheerder kan alle taken uitvoeren die worden ingeschakeld door andere Synapse RBAC-rollen.</br>
>- Alle Synapse RBAC-rollen in elk bereik bieden u Synapse-gebruikers machtigingen in de werk ruimte
>- Alle Synapse RBAC-machtigingen/-acties die in de tabel worden weer gegeven, zijn voor voegsels van micro soft/Synapse/werk ruimten/... </br>


Taak (ik wil...) |Rol (ik moet zijn...)|Synapse RBAC-machtiging/actie
--|--|--
|Synapse Studio openen in een werk ruimte|Synapse-gebruiker of|lezen
| |Eigenaar, bijdrager of lezer van Azure in de werk ruimte|geen
|Een lijst met SQL-groepen, Apache Spark Pools, Integration Runtimes en toegang tot de configuratie gegevens|Synapse-gebruiker of|lezen|
||Eigenaar, bijdrager of lezer van Azure in de werk ruimte|geen
|Gekoppelde services, referenties, beheerde persoonlijke eind punten vermelden|Synapse-gebruiker|lezen
**SQL-pools**||
Een toegewezen SQL-groep of een serverloze SQL-groep maken|Azure-eigenaar of Inzender in de werk ruimte|geen
Een toegewezen SQL-groep beheren (onderbreken, schalen of verwijderen)|Azure-eigenaar of Inzender voor de SQL-groep of-werk ruimte|geen
Een SQL-script maken</br>|Synapse-gebruiker of </br>Azure-eigenaar of Inzender in de werk ruimte, </br>*Er zijn aanvullende SQL-machtigingen vereist om een SQL-script uit te voeren*.|
Een gepubliceerd SQL-script weer geven en openen| Synapse artefact gebruiker, artefact Uitgever, Synapse Inzender|artefacten/lezen
Een SQL-script uitvoeren op een serverloze SQL-groep|SQL-machtigingen voor de groep (automatisch verleend aan een Synapse-beheerder)|geen
Een SQL-script uitvoeren op een toegewezen SQL-groep|Vereist SQL-machtigingen voor de groep|geen
Een nieuw, bijgewerkt of verwijderd SQL-script publiceren|Synapse artefact Uitgever, Synapse-bijdrager|sqlScripts/schrijven, verwijderen
Wijzigingen door voeren in een SQL-script aan een Git-opslag plaats|Vereist Git-machtigingen op de opslag plaats|
Active Directory beheerder toewijzen in de werk ruimte (via de werkruimte eigenschappen in azure Portal)|Azure-eigenaar of Inzender in de werk ruimte |
**Apache Spark groepen**||
Een Apache Spark-pool maken|Azure-eigenaar of Inzender in de werk ruimte|
Apache Spark toepassingen bewaken| Synapse-gebruiker|lezen
De logboeken voor het uitvoeren van notebooks en taken weer geven |Synapse Compute-operator|
Een notitie blok of Spark-taak die wordt uitgevoerd op een Apache Spark groep annuleren|Synapse Compute-operator voor de Apache Spark groep.|bigDataPools/useCompute
Een notitie blok of taak definitie maken|Synapse gebruiker of Azure-eigenaar, Inzender of lezer in de werk ruimte</br> *Er zijn aanvullende machtigingen vereist om uit te voeren, te publiceren of op te slaan*|lezen
Een gepubliceerde notitie blok of taak definitie weer geven en openen, inclusief het controleren van de opgeslagen uitvoer|Synapse artefact gebruiker, Synapse artefact Uitgever, Synapse Inzender in de werk ruimte|artefacten/lezen
Een notitie blok uitvoeren en de uitvoer controleren|Synapse Apache Spark Administrator, Synapse Compute-operator voor de geselecteerde Apache Spark groep|bigDataPools/useCompute 
Een notitie blok of taak definitie (inclusief uitvoer) publiceren of verwijderen voor de service|Publisher voor artefacten in de werk ruimte, Synapse Apache Spark beheerder|notebooks/schrijven, verwijderen
Wijzigingen door voeren in een notitie blok of taak definitie voor de Git-werk vertakking|Git-machtigingen|geen
**Pijp lijnen, Integration runtimes, gegevens stromen, gegevens sets en triggers**||
Een Integration runtime maken, bijwerken of verwijderen|Azure-eigenaar of Inzender in de werk ruimte|
Runtime status van Integration bewaken|Synapse-gebruiker|lezen, pijp lijnen/viewOutputs
Pijplijn uitvoeringen controleren|Synapse artefact Uitgever/Synapse-bijdrager|lezen, pijp lijnen/viewOutputs 
Een pijplijn maken |Synapse-gebruiker </br>[**_onder overweging + Synapse Credential gebruiker op WorkspaceSystemIdentity_* _]</br>_Additional machtigingen zijn vereist voor het publiceren of opslaan van *|lezen, referenties/UseSecret/actie
Een gegevens stroom, gegevensset of trigger maken |Synapse-gebruiker</br>*Er zijn aanvullende machtigingen vereist om te publiceren of op te slaan*|lezen
Een gepubliceerde pijp lijn weer geven en openen |Synapse artefact gebruiker | artefacten/lezen
Gegevens van gegevensset bekijken|Synapse gebruiker + Synapse-referentie gebruiker op de WorkspaceSystemIdentity| 
Fouten opsporen in een pijp lijn met behulp van de standaard Integration runtime|Synapse gebruiker + Synapse-referentie gebruiker op de WorkspaceSystemIdentity referentie|wie </br>referenties/useSecret
Een trigger maken, inclusief trigger nu|Synapse gebruiker + Synapse-referentie gebruiker op de WorkspaceSystemIdentity|lezen, referenties/useSecret/actie
Gegevens kopiëren met het Gegevens kopiëren-hulp programma|Synapse gebruiker + Synapse-referentie gebruiker op de werkruimte systeem identiteit|lezen, referenties/useSecret/actie
Gegevens opnemen (met behulp van een schema)|Synapse Auteur + Synapse-referentie gebruiker op de werkruimte systeem identiteit|lezen, referenties/useSecret/actie
Een nieuwe, bijgewerkte of verwijderde pijp lijn, gegevens stroom of trigger publiceren naar de service|Synapse artefact uitgever in de werk ruimte|pijp lijnen/schrijven, verwijderen</br>gegevens stromen schrijven, verwijderen</br>Triggers/schrijven, verwijderen
Een nieuwe, bijgewerkte of verwijderde gegevensstroom, gegevensset of trigger naar de service publiceren|Uitgever van artefacten in de werk ruimte|Triggers/schrijven, verwijderen
Wijzigingen in pijp lijnen, gegevens stromen, gegevens sets, triggers (door Voer) opslaan in de Git-opslag plaats |Git-machtigingen|geen 
**Gekoppelde services**||
Een gekoppelde service maken (inclusief het toewijzen van een referentie)|Synapse-gebruiker</br>*Er zijn aanvullende machtigingen vereist om uit te voeren, te publiceren of op te slaan*|lezen
Een gepubliceerde gekoppelde service weer geven en openen|Synapse artefact gebruiker|linkedServices/schrijven, verwijderen  
Verbinding testen op een gekoppelde service die is beveiligd door een referentie|Gebruiker van Synapse-en Synapse-referentie|referenties/useSecret/actie|
Een gekoppelde service publiceren|Synapse artefact Uitgever|linkedServices/schrijven, verwijderen
Gekoppelde service definities opslaan (door voeren) aan het git-opslag plaats|Git-machtigingen|geen
**Toegangsbeheer**||
Synapse RBAC-roltoewijzingen in elk bereik controleren|Synapse-gebruiker|lezen
Synapse RBAC-roltoewijzingen toewijzen en verwijderen voor gebruikers, groepen en service-principals| Synapse-beheerder in de werk ruimte of op een specifiek bereik voor werk ruimte-items|roleAssignments/schrijven, verwijderen
Synapse RBAC-toegang tot code artefacten maken of verwijderen|Synapse-beheerder in het bereik van de werk ruimte|roleAssignments/schrijven, verwijderen   

>[!Note]
>Gast gebruikers van een andere Tenant kunnen geen roltoewijzingen controleren, toevoegen of wijzigen, ongeacht de rol waaraan ze zijn toegewezen. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie [over het controleren van Synapse RBAC-roltoewijzingen](./how-to-review-synapse-rbac-role-assignments.md)

Meer informatie [over het beheren van Synapse RBAC-roltoewijzingen](./how-to-manage-synapse-rbac-role-assignments.md). 