---
title: SNAT voor uitgaande verbindingen
description: Beschrijft hoe Azure Load Balancer wordt gebruikt voor het uitvoeren van SNAT voor uitgaande internet connectiviteit
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: d0fad3a257b6d1b3723cdf337179573b4667b054
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780107"
---
# <a name="using-snat-for-outbound-connections"></a>SNAT gebruiken voor uitgaande verbindingen

De frontend-Ip's van een open bare Azure-load balancer kunnen worden gebruikt om uitgaande verbinding met Internet te bieden voor back-end-exemplaren. Deze configuratie maakt gebruik van **bron Network Address Translation (SNAT)**. SNAT schrijft het IP-adres van de back-end naar het open bare IP-adres van uw load balancer. 

SNAT maakt **IP-maskering** van het back-end-exemplaar mogelijk. Hierdoor wordt voor komen dat externe bronnen een direct adres hebben voor de back-end-exemplaren. Het delen van een IP-adres tussen back-end-instanties reduceert de kosten van statische open bare Ip's en ondersteunt scenario's zoals het vereenvoudigen van IP-lijsten met verkeer van bekende open bare Ip's. 

>[!Note]
> [Virtual Network NAT](../virtual-network/nat-overview.md) is de aanbevolen oplossing voor toepassingen waarvoor een groot aantal uitgaande verbindingen of zakelijke klanten vereist zijn die een enkele set IP-adressen nodig hebben om te kunnen worden gebruikt vanuit een bepaald virtueel netwerk. De dynamische toewijzing maakt eenvoudige configuratie mogelijk en > het meest efficiënte gebruik van de SNAT-poorten van elk IP-adres. Ook kunnen alle bronnen in het virtuele netwerk een set IP-adressen delen zonder dat ze > een load balancer hoeven te delen.

>[!Important]
> Zelfs als er geen uitgaande SNAT is geconfigureerd, zijn Azure Storage-accounts binnen dezelfde regio nog steeds toegankelijk en hebben back-endservers nog steeds toegang tot micro soft-Services, zoals Windows-updates.

>[!NOTE] 
>In dit artikel worden alleen Azure Resource Manager-implementaties behandeld. Controleer de [uitgaande verbindingen (klassiek)](/previous-versions/azure/load-balancer/load-balancer-outbound-connections-classic) voor alle klassieke implementatie Scenario's in Azure.

## <a name="sharing-frontend-ip-address-across-backend-resources"></a><a name ="snat"></a> Front-end-IP-adres delen via back-upbronnen

Als de back-end-bronnen van een load balancer geen ILPIP-adressen op exemplaar niveau hebben, wordt er een uitgaande verbinding tot stand gebracht via het frontend-IP-adres van de open bare load balancer. Poorten worden gebruikt voor het genereren van unieke id's die worden gebruikt voor het onderhouden van afzonderlijke stromen. Het Internet maakt gebruik van een vijf tuple om dit onderscheid te bieden.

De vijf-tuple bestaat uit:

* Doel-IP
* Doelpoort
* Bron-IP
* Bron poort en-protocol om dit onderscheid te bieden.

Als een poort wordt gebruikt voor binnenkomende verbindingen, heeft deze een **listener** voor binnenkomende verbindings aanvragen op die poort en kan deze niet worden gebruikt voor uitgaande verbindingen. Als u een uitgaande verbinding wilt maken, moet u een **tijdelijke poort** gebruiken om de bestemming aan te bieden met een poort waarop u een afzonderlijke verkeers stroom wilt communiceren en onderhouden. Wanneer deze tijdelijke poorten worden gebruikt voor het uitvoeren van SNAT, worden deze **SNAT-poorten** genoemd. 

Elk IP-adres heeft per definitie 65.535 poorten. Elke poort kan worden gebruikt voor binnenkomende of uitgaande verbindingen voor TCP (Transmission Control Protocol) en UDP (User Data gram Protocol). Wanneer een openbaar IP-adres wordt toegevoegd als een frontend-IP aan een load balancer, geeft Azure 64.000 in aanmerking voor gebruik als SNAT-poorten. 

>[!NOTE]
> Elke poort die wordt gebruikt voor een taak verdeling of binnenkomende NAT-regel, gebruikt een bereik van acht poorten van deze 64.000-poorten, waardoor het aantal poorten dat in aanmerking komt voor SNAT, wordt verminderd. Als een taakverdelings-of NAT-regel van een load-> zich in hetzelfde bereik van acht bevindt als een andere, worden er geen extra poorten gebruikt. 

Via [Uitgaande regels](./outbound-rules.md) en taakverdelings regels kunnen deze SNAT-poorten worden gedistribueerd naar back-end-exemplaren zodat ze de open bare IP-adressen van de Load Balancer voor uitgaande verbindingen kan delen.

Wanneer [scenario 2](#scenario2) hieronder is geconfigureerd, wordt door de host voor elk back-end-exemplaar een SNAT uitgevoerd op pakketten die deel uitmaken van een uitgaande verbinding. Bij het uitvoeren van een SNAT op een uitgaande verbinding van een back-end-exemplaar, wordt het bron-IP-adres door de host opnieuw naar een van de frontend-Ip's genoteerd. Om unieke stromen te behouden, schrijft de host de bron poort van elk uitgaand pakket naar een van de toegewezen SNAT-poorten voor de back-end-instantie.

## <a name="outbound-connection-behavior-for-different-scenarios"></a>Gedrag van uitgaande verbinding voor verschillende scenario's
  * Virtuele machine met een openbaar IP-adres.
  * Virtuele machine zonder openbaar IP-adres.
  * Virtuele machine zonder openbaar IP-adres en zonder standaard load balancer.
        

 ### <a name="scenario-1-virtual-machine-with-public-ip"></a><a name="scenario1"></a> Scenario 1: virtuele machine met openbaar IP-adres


 | Lidkoppelingen | Methode | IP-protocollen |
 | ---------- | ------ | ------------ |
 | Openbaar load balancer of zelfstandig | [SNAT (bron netwerk adres omzetting)](#snat) </br> niet gebruikt. | TCP (Transmission Control Protocol) </br> UDP (User Data gram Protocol) </br> ICMP (Internet Control Message Protocol) </br> ESP (Encapsulating Security Payload) |


 #### <a name="description"></a>Beschrijving


 Azure gebruikt het open bare IP-adres dat is toegewezen aan de IP-configuratie van de NIC van het exemplaar voor alle uitgaande stromen. Alle tijdelijke poorten zijn beschikbaar voor het exemplaar. Het maakt niet uit of de virtuele machine gelijkmatig is verdeeld of niet. Dit scenario heeft voor rang op de andere. 


 Een openbaar IP-adres dat is toegewezen aan een virtuele machine is een 1:1-relatie (in plaats van 1: veel) en geïmplementeerd als een stateless 1:1 NAT.


 ### <a name="scenario-2-virtual-machine-without-public-ip-and-behind-standard-public-load-balancer"></a><a name="scenario2"></a>Scenario 2: virtuele machine zonder openbaar IP-adres en achter standaard open bare Load Balancer


 | Lidkoppelingen | Methode | IP-protocollen |
 | ------------ | ------ | ------------ |
 | Openbare load balancer | Het gebruik van load balancer frontend-IP-adressen voor [SNAT](#snat).| TCP </br> UDP |


 #### <a name="description"></a>Beschrijving


 De load balancer resource is geconfigureerd met een regel voor uitgaande verbindingen of een taakverdelings regel die standaard SNAT mogelijk maakt. Deze regel wordt gebruikt om een koppeling tussen de open bare IP-frontend te maken met de back-end-pool. 


 Als u deze regel configuratie niet voltooit, wordt het gedrag in scenario 3 beschreven. 


 Een regel met een listener is niet vereist voor het slagen van de status test.


 Wanneer een virtuele machine een uitgaande stroom maakt, wordt het IP-adres van de bron door Azure vertaald naar het open bare IP-adres van de open bare frontend van load balancer. Deze vertaling wordt uitgevoerd via [SNAT](#snat). 


 Tijdelijke poorten van het open bare IP-adres van de load balancer frontend worden gebruikt om onderscheid te maken tussen afzonderlijke stromen die afkomstig zijn van de virtuele machine. SNAT maakt dynamisch gebruik van [vooraf toegewezen tijdelijke poorten](#preallocatedports) wanneer uitgaande stromen worden gemaakt. 


 In deze context worden de tijdelijke poorten die worden gebruikt voor SNAT de SNAT-poorten genoemd. Het wordt nadrukkelijk aanbevolen om een [uitgaande regel](./outbound-rules.md) expliciet te configureren. Als u een standaard-SNAT gebruikt via een taakverdelings regel, worden de SNAT-poorten vooraf toegewezen, zoals beschreven in de [standaard toewijzings tabel](#snatporttable)voor de SNAT-poorten.


 ### <a name="scenario-3-virtual-machine-without-public-ip-and-behind-basic-load-balancer"></a><a name="scenario3"></a>Scenario 3: virtuele machine zonder openbaar IP-adres en achter basis Load Balancer


 | Lidkoppelingen | Methode | IP-protocollen |
 | ------------ | ------ | ------------ |
 |Geen </br> Basis load balancer | [SNAT](#snat) met dynamisch IP-adres op exemplaar niveau| TCP </br> UDP | 

 #### <a name="description"></a>Beschrijving


 Wanneer de virtuele machine een uitgaande stroom maakt, vertaalt Azure het bron-IP-adres naar een dynamisch toegewezen IP-adres voor de open bare bron. Dit open bare IP-adres kan **niet worden geconfigureerd** en kan niet worden gereserveerd. Dit adres telt niet op basis van de open bare IP-resource limiet van het abonnement. 


 Het open bare IP-adres wordt vrijgegeven en er is een nieuwe open bare IP aangevraagd als u de volgende handelingen opnieuw implementeert: 


 * Virtuele machine
 * Beschikbaarheidsset
 * Schaalset voor virtuele machines 


 Gebruik dit scenario niet voor het toevoegen van IP-adressen aan een acceptatie lijst. Gebruik scenario 1 of 2 waar u het uitgaande gedrag expliciet declareert. De [SNAT](#snat) -poorten zijn vooraf toegewezen, zoals wordt beschreven in de [standaard toewijzings tabel](#snatporttable)voor de SNAT-poorten.


## <a name="exhausting-ports"></a><a name="scenarios"></a> Poorten uitgeputen

Elke verbinding met hetzelfde doel-IP-adres en deze doel poort maakt gebruik van een SNAT-poort. Deze verbinding houdt een afzonderlijke **verkeers stroom** bij van het back-end-exemplaar of de **client** naar een **Server**. Dit proces geeft de server een afzonderlijke poort waarop verkeer kan worden geadresseerd. Zonder dit proces is de client computer niet op de hoogte van welke stroom een pakket deel uitmaakt.

Stel dat u meerdere browsers gaat https://www.microsoft.com :

* Doel-IP = 23.53.254.142
* Doel poort = 443
* Protocol = TCP

Zonder verschillende doel poorten voor het retour verkeer (de SNAT-poort die wordt gebruikt om de verbinding tot stand te brengen), heeft de client geen enkele manier om één query resultaat van een andere te scheiden.

Uitgaande verbindingen kunnen burst zijn. Aan een back-end-exemplaar kunnen onvoldoende poorten worden toegewezen. Als het **opnieuw gebruiken** van de verbinding is ingeschakeld, wordt het risico van de SNAT- **poort uitputting** verhoogd.

Nieuwe uitgaande verbindingen met een doel-IP mislukken als er een poort uitvalt. Verbindingen worden uitgevoerd wanneer een poort beschikbaar wordt. Deze uitputting treedt op wanneer de 64.000-poorten van een IP-adres dun zijn verdeeld over veel backend-exemplaren. Raadpleeg de [hand leiding](./troubleshoot-outbound-connection.md)voor het oplossen van problemen voor meer informatie over het beperken van de uitputting van de SNAT-poort.  

Voor TCP-verbindingen gebruikt de load balancer één SNAT-poort voor elke doel-IP en poort. Met deze Multiuse kunnen meerdere verbindingen met dezelfde doel-IP worden met dezelfde SNAT-poort. Deze Multiuse is beperkt als de verbinding niet is met verschillende doel poorten.

Voor UDP-verbindingen gebruikt de load balancer een door de **poort beperkte kegel-NAT** -algoritme, die een SNAT-poort per doel-IP gebruikt, ongeacht de doel poort. 

Een poort wordt opnieuw gebruikt voor een onbeperkt aantal verbindingen. De poort wordt alleen opnieuw gebruikt als het doel-IP of de poort verschilt.

## <a name="default-port-allocation"></a><a name="preallocatedports"></a> Standaard poort toewijzing

Elk openbaar IP-adres dat is toegewezen als frontend-IP van uw load balancer krijgt 64.000 SNAT-poorten voor de back-endadresgroep. Poorten kunnen niet worden gedeeld met leden van de back-endadresgroep-groep. Een bereik aan SNAT-poorten kan alleen worden gebruikt door één back-end-instantie om ervoor te zorgen dat retour pakketten correct worden gerouteerd. 

U kunt het beste een expliciete uitgaande regel gebruiken om de toewijzing van de SNAT-poort te configureren. Met deze regel wordt het aantal SNAT-poorten voor elk back-end-exemplaar dat beschikbaar is voor uitgaande verbindingen, gemaximaliseerd. 

Als u de automatische toewijzing van uitgaande SNAT via een taakverdelings regel gebruikt, wordt in de toewijzings tabel uw poort toewijzing gedefinieerd.

In de volgende <a name="snatporttable"></a> tabel ziet u de SNAT-poort voortoewijzingen voor lagen van back-endadresgroep-Pools:

| Pool grootte (VM-exemplaren) | Vooraf toegewezen SNAT-poorten per IP-configuratie |
| --- | --- |
| 1-50 | 1\.024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 | 

>[!NOTE]
> Als u een back-end-groep hebt met een maximale grootte van 10, kan elke instantie 64000/10 = 6.400 poorten bevatten als u een expliciete uitgaande regel definieert. Volgens de bovenstaande tabel is er slechts 1.024 als u automatische toewijzing kiest.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> Uitgaande regels en Virtual Network NAT

Azure Load Balancer regels voor uitgaande verbindingen en Virtual Network NAT zijn opties beschikbaar voor het afbreken van een virtueel netwerk.

Zie voor meer informatie over uitgaande regels [Uitgaande regels](outbound-rules.md).

Zie [Wat is azure Virtual Network NAT](../virtual-network/nat-overview.md)? voor meer informatie over Azure Virtual Network nat.

## <a name="constraints"></a>Beperkingen

*   Wanneer een verbinding niet-actief is zonder dat er nieuwe pakketten worden verzonden, worden de poorten na 4 tot 120 minuten vrijgegeven.
  * Deze drempel waarde kan worden geconfigureerd via uitgaande regels.
*   Elk IP-adres biedt 64.000 poorten die kunnen worden gebruikt voor SNAT.
*   Elke poort kan worden gebruikt voor TCP-en UDP-verbindingen met een doel-IP-adres
  * Er is een UDP SNAT-poort nodig, ongeacht of de doel poort uniek is of niet. Voor elke UDP-verbinding met een bestemmings-IP wordt één UDP SNAT-poort gebruikt.
  * Een TCP SNAT-poort kan worden gebruikt voor meerdere verbindingen met dezelfde doel-IP als de doel poorten verschillen.
*   SNAT-uitputting treedt op wanneer een back-end-exemplaar wordt uitgevoerd met de opgegeven SNAT-poorten. Een load balancer kan nog steeds ongebruikte SNAT-poorten hebben. Als de gebruikte SNAT-poorten van een back-end-exemplaar de opgegeven SNAT-poorten overschrijden, kunnen er geen nieuwe uitgaande verbindingen tot stand worden gebracht.

## <a name="next-steps"></a>Volgende stappen

*   [Problemen met uitgaande verbindingen oplossen vanwege een SNAT-uitputting](./troubleshoot-outbound-connection.md)
*   [Bekijk de metrische gegevens](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) over de SNAT en lees hoe u deze kunt filteren, splitsen en bekijken.
