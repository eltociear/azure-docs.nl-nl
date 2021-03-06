---
title: Container vereisten en aanbevelingen
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/23/2020
ms.author: aahi
ms.openlocfilehash: 4697be519eee96778eecdf37f7b358a88ad886c6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006901"
---
> [!NOTE]
> De vereisten en aanbevelingen zijn gebaseerd op benchmarks met één aanvraag per seconde, met behulp van een afbeelding van 8 MB van een gescande zakelijke brief met 29 regels en een totaal van 803 tekens.

In de volgende tabel wordt de minimale en aanbevolen toewijzing van resources voor elke Read-container beschreven.

| Container | Minimum | Aanbevolen |
|-----------|---------|-------------|
| Lees 2,0-Preview | 1 kern geheugen van 8 GB |  8 kernen, 16 GB geheugen |
| Lees 3,2-Preview | 8 kernen, 16 GB geheugen | 8 kernen, 24 GB geheugen |

* Elke kern moet ten minste 2,6 gigahertz (GHz) of sneller zijn.

Core en geheugen komen overeen met `--cpus` de `--memory` instellingen en, die worden gebruikt als onderdeel van de `docker run` opdracht.
