---
title: 'Zelfstudie: een privécloud schalen'
description: In deze zelfstudie gebruikt u de Azure-portal voor het schalen van een privécloud van Azure VMware Solution.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: d49d973cc6d97280dc0c7ea6681f2602b871e1ba
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791236"
---
# <a name="tutorial-scale-an-azure-vmware-solution-private-cloud"></a>Zelfstudie: een privécloud van Azure VMware Solution schalen

Als u optimaal wilt profiteren van uw privécloud van Azure VMware Solution, kunt u de clusters en hosts schalen aan de hand van wat u nodig hebt voor geplande workloads. U kunt de clusters en hosts in een privécloud schalen naar de vereisten voor de workload van uw toepassing. De beperkingen voor de prestaties en de beschikbaarheid van specifieke services moeten per geval worden bekeken. De limieten voor cluster en host vindt u in het artikel over [het concept van privéclouds](concepts-private-clouds-clusters.md).

In deze zelfstudie gebruikt u de Azure-portal voor het volgende:

> [!div class="checklist"]
> * Een cluster toevoegen aan een bestaande privécloud
> * Hosts toevoegen aan een bestaand cluster

## <a name="prerequisites"></a>Vereisten

Een privécloud om deze zelfstudie te voltooien. Als u nog geen privécloud hebt gemaakt, gebruikt u de [Zelfstudie Een privécloud maken](tutorial-create-private-cloud.md) om een cloud te maken. Configureer netwerken voor uw VMware-privécloud in Azure om het vereiste virtuele netwerk in te stellen.

## <a name="add-a-new-cluster"></a>Een nieuw cluster toevoegen

1. Selecteer op de overzichtspagina van een bestaande privécloud onder **Beheren** de optie **Privécloud schalen**. Selecteer vervolgens **+ Een cluster toevoegen**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="een cluster toevoegen selecteren" border="true":::

1. Gebruik op de pagina **Cluster toevoegen** de schuifregelaar om het aantal hosts te selecteren. Selecteer **Opslaan**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Gebruik op de pagina Cluster toevoegen de schuifregelaar om het aantal hosts te selecteren. Selecteer Opslaan." border="true":::

   De implementatie van het nieuwe cluster wordt gestart.

## <a name="scale-a-cluster"></a>Een cluster schalen 

1. Selecteer op de overzichtspagina van een bestaande privécloud de optie **Privécloud schalen** en selecteer het potloodpictogram om het cluster te bewerken.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Privécloud schalen selecteren in Overzicht" border="true":::

1. Gebruik op de pagina **Cluster bewerken** de schuifregelaar om het aantal hosts te selecteren. Selecteer **Opslaan**.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Gebruik op de pagina Cluster bewerken de schuifregelaar om het aantal hosts te selecteren. Selecteer Opslaan." border="true":::

   Het toevoegen van hosts aan het cluster wordt gestart.

## <a name="next-steps"></a>Volgende stappen

Als u nog een privécloud van Azure VMware Solution nodig hebt, kunt u [nog een privécloud maken](tutorial-create-private-cloud.md) met dezelfde netwerkvereisten en cluster- en hostlimieten.

<!-- LINKS - external-->

<!-- LINKS - internal -->
