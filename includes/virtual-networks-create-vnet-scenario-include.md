---
title: bestand opnemen
description: bestand opnemen
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 2b1f9990985951a4e6ef260954968c0e1466c298
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175951"
---
## <a name="scenario"></a>Scenario

Ter illustratie van het maken van een VNet en subnetten, in dit document worden de volgende scenario:

![VNet-scenario](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

In dit scenario maakt u een VNet met de naam **TestVNet**, met een gereserveerde CIDR-blok van **192.168.0.0./16**. Het VNet bevat de volgende subnetten: 

* **FrontEnd**, met **192.168.1.0/24** als CIDR-blok.
* **BackEnd**, met **192.168.2.0/24** als CIDR-blok.

