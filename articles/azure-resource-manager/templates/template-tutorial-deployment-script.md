---
title: Sjabloonimplementatiescripts gebruiken | Microsoft Docs
description: Meer informatie over het gebruik van implementatiescripts in Azure Resource Manager-sjablonen.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 08/25/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: e1094befcc6b3a6e9d56ba3b603dc45fcb91ba13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88825491"
---
# <a name="tutorial-use-deployment-scripts-to-create-a-self-signed-certificate-preview"></a>Zelfstudie: Implementatiescripts gebruiken om een zelfondertekend certificaat te maken (preview)

Meer informatie over het gebruik van implementatiescripts in ARM-sjablonen (Azure Resource Manager). Implementatiescripts kunnen worden gebruikt voor het uitvoeren van aangepaste stappen die niet kunnen worden uitgevoerd door ARM-sjablonen. Zo kunt u bijvoorbeeld een zelfondertekend certificaat maken.  In deze zelfstudie maakt u een sjabloon voor het implementeren van een Azure-sleutelkluis en gebruikt u vervolgens een `Microsoft.Resources/deploymentScripts`-resource in dezelfde sjabloon om een certificaat te maken en het certificaat toe te voegen aan de sleutelkluis. Zie [Implementatiescripts gebruiken in ARM-sjablonen](./deployment-script-template.md) voor meer informatie over implementatiescripts.

> [!IMPORTANT]
> Er worden in dezelfde resourcegroep twee resources van het implementatiescript gemaakt, een opslagaccount en een containerinstantie, voor het uitvoeren van scripts en probleemoplossing. Deze resources worden doorgaans door de scriptservice verwijderd in een eindfase van de uitvoering van het script. U wordt gefactureerd voor de resources totdat de resources zijn verwijderd. Zie [Resources van implementatiescripts opschonen](./deployment-script-template.md#clean-up-deployment-script-resources) voor meer informatie.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een snelstartsjabloon openen
> * De sjabloon bewerken
> * De sjabloon implementeren
> * Fouten in een mislukt script opsporen
> * Resources opschonen

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* **[Visual Studio Code](https://code.visualstudio.com/) met de Resource Manager Tools-extensie**. Zie [Quickstart: Azure Resource Manager-sjablonen maken met Visual Studio Code](./quickstart-create-templates-use-visual-studio-code.md).

* **Een door de gebruiker toegewezen beheerde identiteit met de rol van de inzender op abonnementsniveau maken**. Deze identiteit wordt gebruikt om implementatiescripts uit te voeren. Zie [Een door een gebruiker toegewezen beheerde identiteit maken](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) als u wilt weten hoe u er een maakt. U hebt de identiteits-id nodig wanneer u de sjabloon implementeert. De indeling van de identiteit is als volgt:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Gebruik het volgende CLI-script om de id op te halen door de naam van de resourcegroep en de naam van de identiteit op te geven.

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  az identity list -g $resourceGroupName
  ```

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

In plaats van een sjabloon helemaal opnieuw te maken, opent u een sjabloon in [Azure-snelstartsjablonen](https://azure.microsoft.com/resources/templates/). Snelstartsjablonen voor Azure is een opslagplaats voor ARM-sjablonen.

De sjabloon die in deze quickstart wordt gebruikt, wordt [Een Azure Key Vault en een geheim maken](https://azure.microsoft.com/resources/templates/101-key-vault-create/) genoemd. Met de sjabloon worden een sleutelkluis gemaakt en wordt een sluitelkluisgeheim aan de sleutelkluis toegevoegd.

1. Selecteer in Visual Studio Code **Bestand**>**Bestand openen**.
2. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-key-vault-create/azuredeploy.json
    ```

3. Selecteer **Openen** om het bestand te openen.
4. Selecteer **Bestand**>**Opslaan als** om het bestand op uw lokale computer op te slaan als **azuredeploy.json**.

## <a name="edit-the-template"></a>De sjabloon bewerken

Breng de volgende wijzigingen aan in de sjabloon:

### <a name="clean-up-the-template-optional"></a>De sjabloon opschonen (optioneel)

De oorspronkelijke sjabloon voegt een geheim toe aan de sleutelkluis.  Verwijder de volgende resource om de zelfstudie te vereenvoudigen:

* **Microsoft.KeyVault/vaults/secrets**

Verwijder de volgende twee parameterdefinities:

* **secretName**
* **secretValue**

Als u ervoor kiest om deze definities niet te verwijderen, moet u de parameterwaarden opgeven tijdens de implementatie.

### <a name="configure-the-key-vault-access-policies"></a>Het toegangsbeleid voor de sleutelkluis configureren

Het implementatiescript voegt een certificaat toe aan de sleutelkluis. Configureer het toegangsbeleid voor de sleutelkluis om de beheerde identiteit toestemming te geven:

1. Voeg een parameter toe om de id van de beheerde identiteits op te halen:

    ```json
    "identityId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the ID of the user-assigned managed identity."
      }
    },
    ```

    > [!NOTE]
    > De uitbreiding van de Resource Manager-sjabloon van Visual Studio Code kan nog geen implementatiescripts opmaken. Gebruik [Shift]+[Alt]+F niet om de deploymentScripts-resources op te maken, zoals hieronder.

1. Voeg een parameter toe voor het configureren van het toegangsbeleid voor de sleutelkluis, zodat de beheerde identiteit certificaten kan toevoegen aan de sleutelkluis.

    ```json
    "certificatesPermissions": {
      "type": "array",
      "defaultValue": [
        "get",
        "list",
        "update",
        "create"
      ],
      "metadata": {
      "description": "Specifies the permissions to certificates in the vault. Valid values are: all, get, list, update, create, import, delete, recover, backup, restore, manage contacts, manage certificate authorities, get certificate authorities, list certificate authorities, set certificate authorities, delete certificate authorities."
      }
    }
    ```

1. Werk het bestaande toegangsbeleid van de sleutelkluis bij naar:

    ```json
    "accessPolicies": [
      {
        "objectId": "[parameters('objectId')]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      },
      {
        "objectId": "[reference(parameters('identityId'), '2018-11-30').principalId]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      }
    ],
    ```

    Er zijn twee beleidsregels gedefinieerd: één voor de aangemelde gebruiker en de andere voor de beheerde identiteit.  De aangemelde gebruiker heeft alleen de machtiging *lijst* nodig om de implementatie te verifiëren.  Om de zelfstudie te vereenvoudigen, wordt hetzelfde certificaat toegewezen aan zowel de beheerde identiteit als de aangemelde gebruikers.

### <a name="add-the-deployment-script"></a>Het implementatiescript toevoegen

1. Voeg drie parameters toe die door het implementatiescript worden gebruikt.

    ```json
    "certificateName": {
      "type": "string",
      "defaultValue": "DeploymentScripts2019"
    },
    "subjectName": {
      "type": "string",
      "defaultValue": "CN=contoso.com"
    },
    "utcValue": {
      "type": "string",
      "defaultValue": "[utcNow()]"
    }
    ```

1. Een deploymentScripts-resource toevoegen:

    > [!NOTE]
    > Omdat de inline implementatiescripts tussen dubbele aanhalingstekens staan, moeten de tekenreeksen in de implementatiescripts tussen enkele aanhalingstekens worden geplaatst. Het escape-teken voor PowerShell is **&#92;** .

    ```json
    {
      "type": "Microsoft.Resources/deploymentScripts",
      "apiVersion": "2019-10-01-preview",
      "name": "createAddCertificate",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[parameters('identityId')]": {
          }
        }
      },
      "kind": "AzurePowerShell",
      "properties": {
        "forceUpdateTag": "[parameters('utcValue')]",
        "azPowerShellVersion": "3.0",
        "timeout": "PT30M",
        "arguments": "[format(' -vaultName {0} -certificateName {1} -subjectName {2}', parameters('keyVaultName'), parameters('certificateName'), parameters('subjectName'))]", // can pass an argument string, double quotes must be escaped
        "scriptContent": "
          param(
            [string] [Parameter(Mandatory=$true)] $vaultName,
            [string] [Parameter(Mandatory=$true)] $certificateName,
            [string] [Parameter(Mandatory=$true)] $subjectName
          )

          $ErrorActionPreference = 'Stop'
          $DeploymentScriptOutputs = @{}

          $existingCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

          if ($existingCert -and $existingCert.Certificate.Subject -eq $subjectName) {

            Write-Host 'Certificate $certificateName in vault $vaultName is already present.'

            $DeploymentScriptOutputs['certThumbprint'] = $existingCert.Thumbprint
            $existingCert | Out-String
          }
          else {
            $policy = New-AzKeyVaultCertificatePolicy -SubjectName $subjectName -IssuerName Self -ValidityInMonths 12 -Verbose

            # private key is added as a secret that can be retrieved in the Resource Manager template
            Add-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName -CertificatePolicy $policy -Verbose

            $newCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

            # it takes a few seconds for KeyVault to finish
            $tries = 0
            do {
              Write-Host 'Waiting for certificate creation completion...'
              Start-Sleep -Seconds 10
              $operation = Get-AzKeyVaultCertificateOperation -VaultName $vaultName -Name $certificateName
              $tries++

              if ($operation.Status -eq 'failed')
              {
                throw 'Creating certificate $certificateName in vault $vaultName failed with error $($operation.ErrorMessage)'
              }

              if ($tries -gt 120)
              {
                throw 'Timed out waiting for creation of certificate $certificateName in vault $vaultName'
              }
            } while ($operation.Status -ne 'completed')

            $DeploymentScriptOutputs['certThumbprint'] = $newCert.Thumbprint
            $newCert | Out-String
          }
        ",
        "cleanupPreference": "OnSuccess",
        "retentionInterval": "P1D"
      }
    }
    ```

    De `deploymentScripts`-resource is afhankelijk van de resource van de sleutelkluis en de resource van de roltoewijzing.  Deze heeft de volgende eigenschappen:

    * **identity**: Het implementatiescript maakt gebruik van een door de gebruiker toegewezen beheerde identiteit voor het uitvoeren van de scripts.
    * **kind**: Geef het type script op. Op dit moment wordt alleen het PowerShell-script ondersteund.
    * **forceUpdateTag**: Bepaal of het implementatiescript moet worden uitgevoerd, zelfs als de scriptbron niet is gewijzigd. Dit kan een actueel tijdstempel of een GUID zijn. Zie [Script meerdere keren uitvoeren](./deployment-script-template.md#run-script-more-than-once) voor meer informatie.
    * **azPowerShellVersion**: Hiermee geeft u de versie van de Azure PowerShell-module op die moet worden gebruikt. Op dit moment ondersteunt het implementatiescript versie 2.7.0, 2.8.0 en 3.0.0.
    * **timeout**: Geef de maximale toegestane uitvoeringstijd voor het script op, opgegeven in de [ISO 8601-indeling](https://en.wikipedia.org/wiki/ISO_8601). Standaardwaarde is **P1D**.
    * **arguments**: Geef de parameterwaarden op. De waarden worden gescheiden door een spatie.
    * **scriptContent**: Geef de scriptinhoud op. Als u een extern script wilt uitvoeren, gebruikt u in plaats daarvan **primaryScriptURI**. Zie [Extern script gebruiken](./deployment-script-template.md#use-external-scripts) voor meer informatie.
        U hoeft **$DeploymentScriptOutputs** alleen te declareren als u het script test op een lokale computer. Door de variabele te declareren, kan het script op een lokale computer en in een deploymentScript-resource worden uitgevoerd zonder wijzigingen te moeten aanbrengen. De waarde die is toegewezen aan $DeploymentScriptOutputs is beschikbaar als uitvoer in de implementaties. Zie [Werken met uitvoer van PowerShell-implementatiescripts](./deployment-script-template.md#work-with-outputs-from-powershell-script) of [Werken met uitvoer van CLI-implementatiescripts](./deployment-script-template.md#work-with-outputs-from-cli-script) voor meer informatie.
    * **cleanupPreference**: Geef de voorkeur op wanneer u de resources van het implementatiescript wilt verwijderen.  De standaardwaarde is **Altijd**, wat betekent dat de resources van het implementatiescript worden verwijderd ondanks de eindstatus (geslaagd, mislukt, geannuleerd). In deze zelfstudie wordt **OnSuccess** gebruikt zodat u de gelegenheid hebt om de resultaten van de uitvoering van het script te bekijken.
    * **retentionInterval**: Geef het interval op gedurende welke de service de scriptresources behoudt nadat deze de eindstatus heeft bereikt. Resources worden verwijderd wanneer deze tijd is verstreken. De duur is gebaseerd op het ISO 8601-patroon. In deze zelfstudie wordt P1D gebruikt. Dit betekent een dag.  Deze eigenschap wordt gebruikt wanneer **cleanupPreference** is ingesteld op **OnExpiration**. Deze eigenschap is momenteel niet ingeschakeld.

    Het implementatiescript gebruikt drie parameters: naam van sleutelkluis, certificaatnaam en onderwerpnaam.  Hiermee wordt een certificaat gemaakt en wordt het certificaat toegevoegd aan de sleutelkluis.

    **$DeploymentScriptOutputs** wordt gebruikt om de uitvoerwaarde op te slaan.  Voor meer informatie raadpleegt u [Werken met uitvoer van PowerShell-implementatiescripts](./deployment-script-template.md#work-with-outputs-from-powershell-script) of [Werken met uitvoer van CLI-implementatiescripts](./deployment-script-template.md#work-with-outputs-from-cli-script).

    De voltooide sjabloon kan [hier](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json) worden gevonden.

1. Als u het foutopsporingsproces wilt zien, plaatst u een fout in de code door de volgende regel aan het implementatiescript toe te voegen:

    ```powershell
    Write-Output1 $keyVaultName
    ```

    De juiste opdracht is **Write-Output** in plaats van **Write-Output1**.

1. Selecteer **Bestand**>**Opslaan** om het bestand op te slaan.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Meld u aan bij [Azure Cloud Shell](https://shell.azure.com)

1. Kies uw favoriete omgeving door in de linkerbovenhoek **PowerShell** of **Bash** (voor CLI) te selecteren.  U moet de shell opnieuw starten wanneer u overschakelt.

    ![Bestand uploaden in Cloud Shell in de Azure-portal](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Selecteer **Upload/download files** en selecteer **Uploaden**. Zie de vorige schermafbeelding.  Selecteer het bestand dat u in de vorige sectie hebt opgeslagen. Na het uploaden van het bestand kunt u de **ls**-opdracht en de **cat**-opdracht uitvoeren om te controleren of het bestand is geüpload.

1. Gebruik het volgende PowerShell-script om de sjabloon te implementeren.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
    $identityId = Read-Host -Prompt "Enter the user-assigned managed identity ID"

    $adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
    $resourceGroupName = "${projectName}rg"
    $keyVaultName = "${projectName}kv"

    New-AzResourceGroup -Name $resourceGroupName -Location $location

    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -identityId $identityId -keyVaultName $keyVaultName -objectId $adUserId

    Write-Host "Press [ENTER] to continue ..."
    ```

    De implementatiescriptservice moet aanvullende resources van het implementatiescript maken om een script uit te voeren. De voorbereiding en het opschoningsproces kan tot één minuut duren, naast de daadwerkelijke uitvoeringstijd van het script.

    De implementatie is mislukt vanwege de ongeldige opdracht, **Write-Output1** die in het script is gebruikt. Er wordt een foutbericht weergegeven met de melding:

    ```error
    The term 'Write-Output1' is not recognized as the name of a cmdlet, function, script file, or operable
    program.\nCheck the spelling of the name, or if a path was included, verify that the path is correct and try again.\n
    ```

    Het resultaat van de uitvoering van het implementatiescript wordt voor probleemoplossingsdoeleinden opgeslagen in de resources van het implementatiescript.

## <a name="debug-the-failed-script"></a>Fouten in een mislukt script opsporen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Open de resourcegroep. Het is de projectnaam waaraan **rg** is toegevoegd. U ziet twee extra resources in de resourcegroep. Deze resources worden *resources van het implementatiescript* genoemd.

    ![Resources van het implementatiescript van Resource Manager-sjabloon](./media/template-tutorial-deployment-script/resource-manager-template-deployment-script-resources.png)

    Beide bestanden hebben het achtervoegsel **azscripts**. Een is een opslagaccount en het andere is een containerinstantie.

    Selecteer **Verborgen typen weergeven** om de deploymentScripts-resource weer te geven.

1. Selecteer het opslagaccount met het achtervoegsel **azscripts**.
1. Selecteer de tegel **Bestandsshares**. De map **azscripts** wordt weergegeven.  De map bevat de uitvoeringsbestanden van het implementatiescript.
1. Selecteer **azscripts**. U ziet twee mappen: **azscriptinput** en **azscriptoutput**.  De input-map bevat een scriptbestand voor het PowerShell-systeem en de scriptbestanden voor de gebruikersimplementatie. De output-map bevat een **executionresult.json** en het uitvoerbestand van het script. U kunt het foutbericht bekijken in **executionresult.json**. Er is geen uitvoerbestand omdat de uitvoering is mislukt.

Verwijder de **Write-Output1**-regel en implementeer de sjabloon opnieuw.

Wanneer de tweede implementatie met succes wordt uitgevoerd, worden de resources van het implementatiescript verwijderd door de scriptservice, omdat de eigenschap **cleanupPreference** is ingesteld op **OnSuccess**.

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.  U ziet in totaal zes resources in de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een implementatiescript in ARM-sjablonen gebruikt. Informatie over het implementeren van Azure-resources op basis van voorwaarden vindt u in:

> [!div class="nextstepaction"]
> [Voorwaarden gebruiken](./template-tutorial-use-conditions.md)
