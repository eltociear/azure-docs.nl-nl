---
title: Facturen-formulier herkenning
titleSuffix: Azure Cognitive Services
description: Leer concepten met betrekking tot factuur analyse met de formulier Recognizer API-gebruik en limieten.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: pafarley
ms.openlocfilehash: 9a3a6bd6489baea90ed4143b42a09e7d697bbc50
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602441"
---
# <a name="form-recognizer-prebuilt-invoice-model"></a>Vooraf gebouwd factuur model voor formulier herkenning

De Azure-formulier herkenner kan gegevens uit verkoop facturen analyseren en extra heren met behulp van de vooraf gemaakte factuur modellen. Met de factuur-API kunnen klanten facturen in verschillende indelingen nemen en gestructureerde gegevens retour neren om de factuur verwerking te automatiseren. Het combineert onze krachtige functies voor [optische teken herkenning (OCR)](../computer-vision/concept-recognizing-text.md) met factuur uitgebreide leer modellen voor het extra heren van belang rijke informatie uit facturen in het Engels. Hiermee worden de tekst, tabellen en gegevens, zoals klant, leverancier, factuur-ID, verval datum van factuur, totaal, factuur bedrag, belasting bedrag, verzen ding, factuur en meer geëxtraheerd. De vooraf gemaakte factuur-API is openbaar beschikbaar in de preview-versie van de formulier Recognizer v 2.1.

## <a name="what-does-the-invoice-service-do"></a>Wat doet de factuur service?

De factuur-API extraheert sleutel velden van facturen en retourneert deze in een geordend Structured JSON-antwoord. Facturen kunnen uit verschillende indelingen en kwaliteit bestaan, inclusief door de telefoon vastgelegde installatie kopieën, gescande documenten en digitale Pdf's. De factuur-API haalt de gestructureerde uitvoer op uit al deze facturen. 

![Voor beeld van Contoso-factuur](./media/invoice-example.jpg)

## <a name="try-it-out"></a>Probeer het eens

Als u de factuur service voor formulier herkenning wilt uitproberen, gaat u naar het hulp programma online voor beeld-UI:

> [!div class="nextstepaction"]
> [Vooraf gebouwde modellen uitproberen](https://fott-preview.azurewebsites.net/)

U hebt een Azure-abonnement nodig ([Maak er een gratis](https://azure.microsoft.com/free/cognitive-services)) en een [Recognzier resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) -eind punt en-sleutel van het formulier om de formulier Recognizer-factuur service uit te proberen. 

![Voor beeld van analyseerde facturen](./media/analyze-invoice.png)


### <a name="input-requirements"></a>Vereisten voor invoer 

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-invoice-operation"></a>De bewerking factuur analyseren

Met de bewerking voor het [analyseren van facturen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9843c2794cbb1a96291) wordt een afbeelding of PDF van een factuur als invoer gebruikt en worden de waarden van de rente opgehaald. De aanroep retourneert een veld met een antwoord header met de naam `Operation-Location` . De `Operation-Location` waarde is een URL die de resultaat-id bevat die in de volgende stap moet worden gebruikt.

|Reactie header| Resultaten-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-invoice-result-operation"></a>De resultaat bewerking analyse van factuur ophalen

De tweede stap bestaat uit het aanroepen van de bewerking [analyse van factuur resultaat ophalen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9acb78c40a2533aee83) . Met deze bewerking wordt de resultaat-ID gebruikt die is gemaakt door de bewerking voor het analyseren van een factuur. Er wordt een JSON-antwoord geretourneerd dat een **status** veld met de volgende mogelijke waarden bevat. U roept deze bewerking iteratief aan tot deze met de **geslaagde** waarde wordt geretourneerd. Gebruik een interval van 3 tot 5 seconden om te voor komen dat het aantal aanvragen per seconde (RPS) wordt overschreden.

|Veld| Type | Mogelijke waarden |
|:-----|:----:|:----|
|status | tekenreeks | notStarted: de analyse bewerking is niet gestart.<br /><br />uitvoeren: de analyse bewerking wordt uitgevoerd.<br /><br />mislukt: de analyse bewerking is mislukt.<br /><br />geslaagd: de analyse bewerking is voltooid.|

Wanneer het veld **status** de waarde **geslaagd** heeft, omvat het JSON-antwoord de factuur resultaten, tabellen geëxtraheerde en optionele tekst herkennings resultaten, indien aangevraagd. Het resultaat van de factuur overeenkomst is ingedeeld als een woorden lijst met benoemde veld waarden, waarbij elke waarde de geëxtraheerde tekst, genormaliseerde waarde, begrenzingsvak, betrouw baarheid en bijbehorende woord elementen bevat. Het resultaat van de tekst herkenning is ingedeeld als een hiërarchie van lijnen en woorden, met tekst, selectie kader en betrouwbaarheids informatie.

### <a name="sample-json-output"></a>Voor beeld van JSON-uitvoer

Het antwoord op de bewerking analyse van factuur resultaat ophalen is de gestructureerde weer gave van de factuur met alle gegevens die worden geëxtraheerd. Hier ziet u een voor beeld van een [factuur bestand](./media/sample-invoice.jpg) en de bijbehorende gestructureerde uitvoer [voorbeeld factuur uitvoer](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice-output.json).

De JSON-uitvoer heeft drie delen: 
* `"readResults"` het knoop punt bevat alle herkende tekst-en selectie markeringen. De tekst wordt geordend op pagina, vervolgens per regel en vervolgens op afzonderlijke woorden. 
* `"pageResults"` het knoop punt bevat de tabellen en cellen die zijn geëxtraheerd met hun begrenzingsvak, betrouw baarheid en een verwijzing naar de regels en woorden in ' readResults '.
* `"documentResults"` het knoop punt bevat de specifieke factuur waarden die het model heeft gedetecteerd. Hier vindt u alle velden van de factuur, zoals factuur-ID, verzen ding, factuur naar, klant, totaal en nog veel meer.

## <a name="example-output"></a>Voorbeelduitvoer

Met de factuur service worden de velden tekst, tabellen en 26 factuur geëxtraheerd. Hieronder vindt u de velden die zijn geëxtraheerd uit een factuur in het JSON-uitvoer antwoord (de onderstaande uitvoer gebruikt deze [voorbeeld factuur](./media/sample-invoice.jpg))  

|Naam| Type | Beschrijving | Tekst | Waarde (gestandaardiseerde uitvoer) |
|:-----|:----|:----|:----| :----|
| CustomerName | tekenreeks | Klant wordt gefactureerd | Micro soft Corp |  |
| CustomerId | tekenreeks | Referentie-ID voor de klant | CID-12345 |  |
| PurchaseOrder | tekenreeks | Een referentie nummer van een inkoop order | IO-3333 | |  |
| InvoiceId | tekenreeks | Id voor deze specifieke factuur (vaak factuur nummer) | INV-100 | |  |
| InvoiceDate | datum | Datum waarop de factuur is verzonden | 11/15/2019 | 
| DueDate | datum | De verval datum van de betaling voor deze factuur | 12/15/2019 | 2019-12-15 | 2019-11-15 |
| Leveranciers naam | tekenreeks | Leverancier die deze factuur heeft gemaakt | CONTOSO LTD. | |
| VendorAddress | tekenreeks | E-mail adres voor de leverancier | 123 456th St New York, NY, 10001 | |
| VendorAddressRecipient | tekenreeks | De naam die is gekoppeld aan de VendorAddress | Contoso Headquarters | |
| CustomerAddress | tekenreeks | E-mail adres voor de klant | 123 overige st, Redmond WA, 98052 | |
| CustomerAddressRecipient | tekenreeks | De naam die is gekoppeld aan de CustomerAddress | Micro soft Corp | |
| BillingAddress | tekenreeks | Expliciet factuur adres voor de klant | 123 factuur KT, Redmond WA, 98052 | |
| BillingAddressRecipient | tekenreeks | De naam die is gekoppeld aan de BillingAddress | Micro soft-Services | |
| ShippingAddress | tekenreeks | Expliciete verzend adres voor de klant | 123 verzen ding St, Redmond WA, 98052 | |
| ShippingAddressRecipient | tekenreeks | De naam die is gekoppeld aan de ShippingAddress | Micro soft Delivery | |
| Subtotaal | getal | Subtotaal veld dat is geïdentificeerd op deze factuur | $100,00 | 100 | 
| TotalTax | getal | Het totale BTW-veld dat op deze factuur is vermeld | $10,00 | 10 |
| InvoiceTotal | getal | Totaal aantal nieuwe kosten gekoppeld aan deze factuur | $110,00 | 110 |
| AmountDue |  getal | Totaal bedrag als gevolg van de leverancier | $610,00 | 610 |
| ServiceAddress | tekenreeks | Expliciet service adres of eigenschaps adres voor de klant | 123 service St, Redmond WA, 98052 | |
| ServiceAddressRecipient | tekenreeks | De naam die is gekoppeld aan de ServiceAddress | Micro soft-Services | |
| RemittanceAddress | tekenreeks | Expliciete remise of betalings adres voor de klant | 123 remitte St New York, NY, 10001 |  |
| RemittanceAddressRecipient | tekenreeks | De naam die is gekoppeld aan de RemittanceAddress | Contoso-facturering |  |
| ServiceStartDate | datum | Eerste datum voor de service periode (bijvoorbeeld een service periode van het hulp programma) | 14-10-2019 | 2019-10-14 |
| ServiceEndDate | datum | De eind datum voor de service periode (bijvoorbeeld een service periode van het hulp programma) | 11/14/2019 | 2019-11-14 |
| PreviousUnpaidBalance | getal | Expliciet eerder onbetaald saldo | $500,00 | 500 |


## <a name="next-steps"></a>Volgende stappen

- Probeer uw eigen facturen en voor beelden in de voor [beeld-UI voor formulier herkenning](https://fott-preview.azurewebsites.net/).
- Voltooi de [Snelstartgids van de client bibliotheek](quickstarts/client-library.md) van een formulier om te beginnen met het schrijven van een app voor het verwerken van facturen met de formulier Recognizer in de taal van uw keuze.
- U kunt ook de Snelstartgids voor het [ophalen van factuur gegevens](./quickstarts/python-invoices.md) gebruiken om het ophalen van factuur gegevens te implementeren met behulp van python en de rest API.
## <a name="see-also"></a>Zie ook

* [Wat is Form Recognizer?](./overview.md)
* [REST API referentie documenten](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9843c2794cbb1a96291)
