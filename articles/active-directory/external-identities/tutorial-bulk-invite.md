---
title: Zelfstudie voor het bulksgewijs uitnodigen van gebruikers voor Microsoft Azure AD B2B-samenwerking
description: In deze zelfstudie leert u hoe u PowerShell en een CSV-bestand gebruikt voor het verzenden van bulk-uitnodigingen naar externe gebruikers van de Microsoft Azure AD B2B-samenwerking.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 05/07/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0f88b310bc00881e66ee8e8b5f2d40616d60315
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87906846"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users"></a>Zelfstudie: Bulksgewijs gebruikers uitnodigen voor Microsoft Azure AD B2B-samenwerking

Als u Azure Active Directory (Azure AD) B2B-samenwerking gebruikt om te werken met externe partners, kunt u tegelijkertijd meerdere gastgebruikers uitnodigen voor uw organisatie. In deze zelfstudie leert u hoe u Azure Portal gebruikt voor het verzenden van bulk-uitnodigingen naar externe gebruikers. Ga als volgt te werk:

> [!div class="checklist"]
> * Gebruik **Gebruikers bulksgewijs uitnodigen** voor het voorbereiden van een bestand met door komma's gescheiden waarden (.csv) voor de gebruikersgegevens en de voorkeuren voor uitnodigingen
> * Upload het CSV-bestand naar Azure AD
> * Controleer of de gebruikers zijn toegevoegd aan de map

Als u geen Azure Active Directory-account hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="understand-the-csv-template"></a>Inzicht in de CSV-sjabloon

Download de CSV-sjabloon voor bulksgewijs uploaden en vul deze in om Azure AD-gastgebruikers bulksgewijs te kunnen uitnodigen. De CSV-sjabloon die u downloadt, kan eruitzien als in dit voorbeeld:

![Werkblad voor uploaden en aanroepen, waarin het doel en de waarden voor elke rij en kolom worden uitgelegd](media/tutorial-bulk-invite/understand-template.png)

### <a name="csv-template-structure"></a>CSV-sjabloonstructuur

De rijen in een gedownloade CSV-sjabloon zijn als volgt:

- **Versienummer**: De eerste rij met het versienummer moet worden opgenomen in het CSV-uploadbestand.
- **Kolomkoppen**: De indeling van de kolomkoppen is &lt;*itemnaam*&gt; [eigenschapsnaam] &lt;*Required of leeg*&gt;. Bijvoorbeeld `Email address to invite [inviteeEmail] Required`. Sommige oudere versies van de sjabloon kunnen iets afwijken.
- **Rij met voorbeelden**: We hebben in de sjabloon een rij met voorbeelden van acceptabele waarden voor elke kolom opgenomen. U moet de rij met voorbeelden verwijderen en vervangen door uw eigen invoerwaarden.

### <a name="additional-guidance"></a>Aanvullende richtlijnen

- De eerste twee rijen van de uploadsjabloon mogen niet worden verwijderd of gewijzigd, anders kan de upload niet worden verwerkt.
- De vereiste kolommen worden het eerst weergegeven.
- Het is niet raadzaam om nieuwe kolommen aan de sjabloon toe te voegen. Eventuele extra kolommen die u toevoegt, worden genegeerd en niet verwerkt.
- U wordt aangeraden altijd de meest recente versie van de CSV-sjabloon te downloaden.

## <a name="prerequisites"></a>Vereisten

U moet twee of meer test e-mailaccounts hebben waarnaar u uitnodigingen kunt verzenden. De accounts moet van buiten uw organisatie zijn. U kunt elk type account gebruiken, met inbegrip van sociale accounts, bijvoorbeeld met een adres van gmail.com of outlook.com.

## <a name="invite-guest-users-in-bulk"></a>Gastgebruikers bulksgewijs uitnodigen

1. Meld u bij Azure Portal aan met een account met beheerdersrechten in de organisatie.
2. Selecteer in het navigatiedeelvenster de service **Azure Active Directory**.
3. Selecteer onder **Beheren** de optie **Gebruikers** > **Bulksgewijs uitnodigen**.
4. Selecteer op de pagina **Gebruikers bulksgewijs uitnodigen** de optie **Downloaden** om een geldige CSV-sjabloon met uitnodigingseigenschappen op te halen.

    ![Knop Downloaden voor bulksgewijs uitnodigen](media/tutorial-bulk-invite/bulk-invite-button.png)

5. Open de CSV-sjabloon en voeg voor elke gastgebruiker een regel toe. Vereiste waarden zijn:

   * **E-mailadres voor uitnodiging**: de gebruiker die een uitnodiging ontvangt

   * **Omleidings-URL**: de URL waarnaar de uitgenodigde gebruiker wordt doorgestuurd na acceptatie van de uitnodiging

    ![Voorbeeld van een CSV-bestand met ingevoerde gastgebruikers](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > Gebruik geen komma's in het **aangepaste uitnodigingsbericht**, anders kan het bericht niet worden geparseerd.

6. Sla het bestand op.
7. Blader op de pagina **Gebruikers bulksgewijs uitnodigen** onder **CSV-bestand uploaden** naar het bestand. Wanneer u het bestand selecteert, wordt de validatie van het CSV-bestand gestart. 
8. Wanneer de bestandsinhoud is gevalideerd, ziet u **Het bestand is geüpload**. Als er fouten zijn, moet u deze corrigeren voordat u de taak kunt verzenden.
9. Wanneer het bestand is gevalideerd, selecteert u **Verzenden** om te beginnen met de Azure-bulkbewerking waarmee de uitnodigingen worden toegevoegd. 
10. Als u de taakstatus wilt weergeven, selecteert u **Klik hier om de status van elke bewerking weer te geven**. U kunt ook **Bulksgewijze bewerkingsresultaten** in de sectie **Activiteit** selecteren. Selecteer de waarden onder de kolommen **Aantal geslaagd**, **Aantal mislukt** of **Totaal aantal aanvragen** voor gedetailleerde informatie over elk regelitem in de kolommen voor de bulksgewijze bewerking. Als er fouten zijn opgetreden, worden de redenen voor de fouten weergegeven.

    ![Voorbeeld van bulksgewijze bewerkingsresultaten](media/tutorial-bulk-invite/bulk-operation-results.png)

11. Wanneer de taak is voltooid, ziet u een melding dat de bulksgewijze bewerking is geslaagd.

## <a name="verify-guest-users-in-the-directory"></a>Gastgebruikers in de map controleren

Controleer of de gastgebruikers die u hebt toegevoegd, in de map aanwezig zijn. U kunt dit controleren in Azure Portal of met behulp van PowerShell.

### <a name="view-guest-users-in-the-azure-portal"></a>Gastgebruikers weergeven in Azure Portal

1. Meld u bij Azure Portal aan met een account met beheerdersrechten in de organisatie.
2. Selecteer in het navigatiedeelvenster de service **Azure Active Directory**.
3. Onder **Beheren**, selecteer **Gebruikers**.
4. Onder **Weergeven**, selecteer **Alleen gastgebruikers** en controleer of de gebruikers die u hebt toegevoegd, worden weergegeven.

### <a name="view-guest-users-with-powershell"></a>Gastgebruikers weergeven met PowerShell

Voer de volgende opdracht uit:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

U moet nu de uitgenodigde gebruikers zien met een user principal name (UPN) in de indeling *e-mailadres*#EXT#\@*domein*. Bijvoorbeeld *Istokes_fabrikam.com#EXT#\@contoso.onmicrosoft.com*, waarbij contoso.onmicrosoft.com staat voor de organisatie waaruit u de uitnodigingen hebt verzonden.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, kunt u de testgebruikersaccounts in de map verwijderen door in Azure Portal op de pagina Gebruikers het selectievakje naast de gastgebruiker in te schakelen en vervolgens **Verwijderen** te selecteren. 

U kunt ook de volgende PowerShell-opdracht uitvoeren om een gebruikersaccount te verwijderen:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Bijvoorbeeld: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u bulk-uitnodigingen naar gastgebruikers buiten uw organisatie verzonden. Vervolgens leert u hoe het proces voor uitnodiging inwisselen werkt.

> [!div class="nextstepaction"]
> [Meer informatie over het proces voor uitnodiging inwisselen in Microsoft Azure AD B2B-samenwerking](redemption-experience.md)
