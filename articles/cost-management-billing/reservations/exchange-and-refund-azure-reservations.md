---
title: Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen
description: Meer informatie over hoe u Azure-reserveringen kunt inwisselen of retourneren. U moet eigenaarsrechten voor de reserveringsorder hebben om een reservering te kunnen inwisselen of er een restitutie voor te kunnen krijgen.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 89b01205c08216b3ce1ec2d36cce48335b49eb47
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96344280"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Selfserviceopties voor inruilen en retourneren voor Azure Reservations

Azure-reserveringen bieden flexibiliteit om te voldoen aan uw evoluerende behoeften. U kunt een reservering inwisselen voor een andere reservering van hetzelfde type. U kunt bijvoorbeeld een virtuele machinereservering omruilen om een andere reservering te kopen voor elke andere VM-grootte of -regio. Op vergelijkbare wijze kan een SQL PaaS Database-reservering worden ingeruild om elke reservering aan te schaffen voor een SQL PaaS Database van welk type of in welke regio dan ook. U kunt ook reserveringen terugbetalen, maar het totaal van alle geannuleerde reserveringen in uw factureringsbereik (zoals EA, Microsoft Customer Agreement en Microsoft Partner-klantovereenkomst) mag niet hoger zijn dan USD 50.000 in een doorlopende periode van 12 maanden. Gereserveerde Azure Databricks-capaciteit, Azure VMware-oplossingen via een CloudSimple-reservering, Azure Red Hat Open Shift-reserveringen, Red Hat-plannen en SUSE Linux-plannen komen niet in aanmerking voor restitutie.

De selfserviceoptie voor inwisselen en annuleren is niet beschikbaar voor Enterprise Agreement-klanten van de Amerikaanse overheid. Andere abonnementstypen voor de Amerikaanse overheid, zoals Betalen per gebruik en CSP, (Cloud Solution Provider) worden wel ondersteund.

> [!NOTE]
> - **U moet eigenaarsrechten voor de reserveringsorder hebben om een bestaande reservering in te wisselen of er een restitutie voor te krijgen.** U kunt [Gebruikers toevoegen of wijzigen die een reservering kunnen beheren](./manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
> - Microsoft brengt momenteel geen kosten in rekening voor vroegtijdige beëindiging voor restitutie van reserveringen. Mogelijk worden in de toekomst wel kosten voor restituties in rekening gebracht. Momenteel staat er geen datum voor het inschakelen van de kosten gepland.

## <a name="how-to-exchange-or-refund-an-existing-reservation"></a>Een bestaande reservering inwisselen of restitueren

U kunt uw reservering inwisselen via [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Selecteer de reserveringen waarvoor u een restitutie wilt aanvragen en selecteer **Inwisselen**.  
    [![Voorbeeldafbeelding van reserveringen om restitutie voor aan te vragen](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png#lightbox)
1. Selecteer het VM-product dat u wilt aanschaffen en geef een hoeveelheid op. Zorg ervoor dat het totaal van de nieuwe aankoop hoger is dan het restitutietotaal. [Bepaal de juiste grootte voordat u overgaat tot aanschaf](../../virtual-machines/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    [![Voorbeeldafbeelding van het te kopen VM-product bij een inwisseling](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png#lightbox)
1. Controleer en voltooi de transactie.  
    [![Voorbeeldafbeelding van het te kopen VM-product bij een inwisseling, waarbij de retournering wordt voltooid](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png#lightbox)

Voor restitutie voor een reservering gaat u naar **Reserveringsgegevens** en selecteert u **Restitutie**.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Niet-Premium Storage inwisselen voor Premium Storage

U kunt een reservering die is gekocht voor een VM-grootte die geen ondersteuning biedt voor Premium Storage inwisselen voor een bijbehorende VM-grootte die deze ondersteuning wel biedt. Bijvoorbeeld een _F1_ voor een _F1s_. Als u de inwisseling wilt maken, gaat u naar Reserveringsdetails en selecteert u **Inwisselen**. De periode van de gereserveerde instantie wordt niet opnieuw ingesteld door de inwisseling en er wordt ook geen nieuwe transactie gemaakt. 

## <a name="how-transactions-are-processed"></a>Hoe transacties worden verwerkt

Microsoft annuleert eerst de bestaande reservering en restitueert het aangepaste bedrag voor die reservering. Als er een inwisseling is, wordt de nieuwe aankoop verwerkt. Microsoft verwerkt restituties met behulp van een van de volgende methoden, afhankelijk van uw accounttype en betalingsmethode:

### <a name="enterprise-agreement-customers"></a>Enterprise Agreement-klanten

Geld wordt toegevoegd aan de financiële toezegging voor uitwisselingen en restituties als de oorspronkelijke aankoop hiermee is gedaan. Als de financiële toezeggingstermijn die gebruikmaakt van de reservering niet langer actief is, wordt tegoed toegevoegd aan uw huidige financiële toezeggingsperiode van de Enterprise Agreement. Het tegoed is 90 dagen vanaf de terugbetalingsdatum geldig. Ongebruikt tegoed verloopt na 90 dagen.

Als de oorspronkelijke aankoop is gedaan als een overschrijding, worden de oorspronkelijke factuur waarop de reservering is aangeschaft en alle latere facturen opnieuw geopend en opnieuw aangepast. Microsoft geeft een tegoednota uit voor de restituties.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Betalingen van betalen per gebruik-facturen en CSP-programma

De oorspronkelijke inkoopfactuur voor reserveringen wordt geannuleerd en er wordt een nieuwe factuur gemaakt voor de restitutie. Voor inwisselingen wordt in de nieuwe factuur de restitutie en de nieuwe aankoop weergegeven. Het restitutiebedrag wordt aangepast op basis van de aankoop. Als u alleen een reservering restitueert, blijft het bedrag naar rato bij Microsoft en wordt dit aangepast op basis van een toekomstige reserveringsaankoop. Als u een reservering hebt aangeschaft met de tarieven voor betalen per gebruik en later op een CSP overstapt, kunt u de reservering retourneren en opnieuw aanschaffen zonder dat hiervoor een boete in rekening wordt gebracht.

### <a name="pay-as-you-go-credit-card-customers"></a>Betalen per gebruik-creditcardklanten

De oorspronkelijke factuur wordt geannuleerd en er wordt een nieuwe factuur gemaakt. Het geld wordt gerestitueerd naar de creditcard die is gebruikt voor de oorspronkelijke aankoop. Als u uw kaart hebt gewijzigd, neemt u [contact op met ondersteuning](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="cancel-exchange-and-refund-policies"></a>Beleidsregels voor annuleren, inwisselen en restituties

Azure heeft de volgende beleidsregels voor annuleringen, inwisselingen en restituties.

**Beleid voor inwisselen**

- U kunt meerdere bestaande reserveringen retourneren om één nieuwe reservering van hetzelfde type aan te schaffen. U kunt geen reserveringen van het ene type inwisselen voor een andere. U kunt bijvoorbeeld geen VM-reservering retourneren om een SQL-reservering aan te schaffen. U kunt reserveringseigenschappen wijzigen zoals familie, serie, versie, SKU, regio, hoeveelheid en periode wijzigen met een inwisseling.
- Alleen eigenaars van reserveringen kunnen een inwisseling verwerken. [Meer informatie over het toevoegen of wijzigen van gebruikers die een reservering kunnen beheren](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Een inwisseling wordt verwerkt als een restitutie en een nieuwe aankoop: er worden verschillende transacties gemaakt voor de annulering en de aankoop van de nieuwe reservering. Het reserveringsbedrag naar rato wordt gerestitueerd voor de reserveringen die zijn ingeruild. De kosten voor de nieuwe aankoop worden volledig in rekening gebracht. Het reserveringsbedrag naar rato is de dagelijkse restwaarde naar rato van de reservering die wordt geretourneerd.
- U kunt reserveringen inwisselen of hier restitutie voor aanvragen zelfs als de Enterprise Agreement die is gebruikt om de reservering aan te schaffen is verlopen en is vernieuwd als een nieuwe overeenkomst.
- De levenslange toezegging van de nieuwe reservering moet gelijk zijn aan of groter zijn dan de resterende toezegging van de geretourneerde reservering. Bijvoorbeeld: voor een reservering van drie jaar à USD 100 per maand, die na de 18e betaling is geretourneerd, moet de toezegging voor de levensduur van de nieuwe reservering USD 1800 zijn of hoger (maandelijks of vooruit betaald).
- De nieuwe reservering die is gekocht als onderdeel van een inwisseling, heeft een nieuwe periode die start vanaf het moment van de inwisseling.
- Er is geen boete of jaarlijkse limieten voor inwisselingen.

**Restitutiebeleid**

- Momenteel worden geen kosten voor vroegtijdige beëindiging in rekening gebracht, maar mogelijk geldt in de toekomst bij annuleringen een tarief van 12% voor vroegtijdige beëindiging.
- De totale geannuleerde toezegging mag niet groter zijn dan 50.000 USD in een doorlopende periode van 12 maanden voor een factureringsprofiel of één inschrijving. Bijvoorbeeld: voor een reservering van drie jaar à USD 100 per maand en die in de 18e maand is terugbetaald, bedraagt de geannuleerde toezegging USD 1800. Na de terugbetaling is uw nieuwe beschikbare limiet voor restitutie USD 48.200. Binnen 365 dagen na de restitutie wordt de limiet van USD 48.200 met USD 1800 verhoogd en heeft uw nieuwe pool een waarde van USD 50.000. Elke andere annulering van een reservering voor het factureringsprofiel of de EA-inschrijving wordt van dezelfde pool afgetrokken en dezelfde aanvullingslogica wordt toegepast.
- In Azure wordt geen terugbetaling verwerkt die de limiet van 50.000 USD overschrijdt in een periode van twaalf maanden voor een factureringsprofiel of EA-inschrijving.
- Restituties worden berekend op basis van de laagste prijs, zijnde de aankoopprijs of de huidige prijs van de reservering, welke het laagste is.
- Alleen eigenaren van reserveringsorders kunnen een restitutie verwerken. [Meer informatie over het toevoegen of wijzigen van gebruikers die een reservering kunnen beheren](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure-reserveringen beheren](manage-reserved-vm-instance.md) voor meer informatie over het beheren van een reservering.
- Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:
    - [Wat zijn Azure-reserveringen?](save-compute-costs-reservations.md)
    - [Reserveringen beheren in Azure](manage-reserved-vm-instance.md)
    - [Begrijpen hoe de reserveringskorting wordt toegepast](../manage/understand-vm-reservation-charges.md)
    - [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](understand-reserved-instance-usage.md)
    - [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](understand-reserved-instance-usage-ea.md)
    - [Kosten van Windows-software zijn niet inbegrepen in reserveringen](reserved-instance-windows-software-costs.md)
    - [Azure-reserveringen in het CSP-programma](/partner-center/azure-reservations)