---
title: Een NSX-T-netwerksegment toevoegen
description: Stappen om een NSX-T-netwerksegment toe te voegen voor Azure VMware Solution.
ms.topic: include
ms.date: 11/09/2020
ms.openlocfilehash: 5b97f0b280fa12eff39c9601bb73e439dba8e9fd
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335045"
---
<!-- Used in manage-dhcp.md and tutorial-nsx-t-network-segment.md -->

1. Selecteer **Netwerken** > **Segmenten** in NSX-T Manager en selecteer vervolgens **Segment toevoegen**. 

   :::image type="content" source="../media/nsxt/nsxt-segments-overview.png" alt-text="Schermopname van het toevoegen van een nieuw segment":::

1. Selecteer **Segment toevoegen** en voer een naam in voor dit segment.

1. Selecteer de Tier1 Gateway (TNTxx-T1) als de **Verbonden gateway** en laat het **Type** ingesteld op Flexibel.

1. Selecteer de vooraf geconfigureerde overlay **Transportzone** (TNTxx-OVERLAY-TZ) en selecteer vervolgens **Subnetten instellen**. 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-specs.png" alt-text="Stel de segmentnaam, de verbonden gateway, het type en de transportzone in en selecteer vervolgens Set Subnets.":::

1. Voer het IP-adres van de gateway in en selecteer vervolgens **Toevoegen**. 

   >[!IMPORTANT]
   >Het IP-adres moet zich op een niet-overlappend RFC1918-adresblok bevinden, zodat u verbinding kunt maken met de VM's op het nieuwe segment.

   :::image type="content" source="../media/nsxt/nsxt-create-segment-gateway.png" alt-text="Stel het IP-adres van de gateway in voor het nieuwe segment en selecteer ADD.":::

1. Selecteer **Apply** en vervolgens **Save**.

1. Selecteer **Nee** om de optie te weigeren om het segment te gaan configureren. 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-continue-no.png" alt-text="U kunt weigeren om nieuwe netwerksegment verder te configureren door NO te selecteren.":::

1. Bevestig de aanwezigheid van het nieuwe netwerksegment. In dit voorbeeld is **Is01** het nieuwe netwerksegment.

   1. Selecteer **Netwerken** > **Segmenten** in de NSX-T Manager. 

      :::image type="content" source="../media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Controleer of het nieuwe netwerksegment aanwezig is in NSX-T.":::

   1. Selecteer **Networking > SDDC-datacentrum** in vCenter.

      :::image type="content" source="../media/nsxt/vcenter-with-ls01-2.png" alt-text="Controleer of het nieuwe netwerksegment aanwezig is in vCenter.":::