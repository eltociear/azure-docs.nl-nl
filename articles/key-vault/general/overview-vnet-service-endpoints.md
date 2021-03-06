---
title: Virtuele netwerk service-eind punten voor Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Meer informatie over hoe u met virtuele netwerk service-eind punten voor Azure Key Vault de toegang tot een opgegeven virtueel netwerk kunt beperken, met inbegrip van gebruiks scenario's.
services: key-vault
author: amitbapat
ms.author: ambapat
manager: rkarlin
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 9cbce00e2c2743aec57cd857b6f38d20bce33698
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532904"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Virtuele netwerk service-eind punten voor Azure Key Vault

Met de service-eind punten voor virtuele netwerken voor Azure Key Vault kunt u de toegang tot een opgegeven virtueel netwerk beperken. Met de eind punten kunt u ook de toegang beperken tot een lijst met IPv4-adresbereiken (Internet Protocol versie 4). Gebruikers die verbinding maken met uw sleutel kluis van buiten deze bronnen, krijgen geen toegang.

Er is een belang rijke uitzonde ring op deze beperking. Als een gebruiker is aangemeld om vertrouwde micro soft-Services toe te staan, kunnen verbindingen van deze services via de firewall worden toegestaan. Deze services omvatten bijvoorbeeld Office 365 Exchange Online, Office 365 share point online, Azure compute, Azure Resource Manager en Azure Backup. Dergelijke gebruikers moeten nog steeds een geldig Azure Active Directory token presen teren en moeten machtigingen hebben (geconfigureerd als toegangs beleid) om de aangevraagde bewerking uit te voeren. Zie [service-eind punten voor virtuele netwerken](../../virtual-network/virtual-network-service-endpoints-overview.md)voor meer informatie.

## <a name="usage-scenarios"></a>Gebruiksscenario's

U kunt [Key Vault firewalls en virtuele netwerken](network-security.md) configureren om de toegang tot verkeer van alle netwerken (met inbegrip van Internet verkeer) standaard te weigeren. U kunt toegang verlenen aan verkeer van specifieke virtuele Azure-netwerken en open bare IP-adresbereiken voor het Internet, zodat u een beveiligde netwerk grens voor uw toepassingen maakt.

> [!NOTE]
> Key Vault firewalls en regels voor virtuele netwerken zijn alleen van toepassing op het [gegevens vlak](secure-your-key-vault.md#data-plane-access-control) van Key Vault. Key Vault besturings vlak bewerkingen (zoals het maken, verwijderen en wijzigen van bewerkingen, het instellen van toegangs beleid, firewalls en regels voor virtuele netwerken) worden niet beïnvloed door firewalls en regels voor virtuele netwerken.

Hier volgen enkele voor beelden van hoe u service-eind punten kunt gebruiken:

* U gebruikt Key Vault om versleutelings sleutels, toepassings geheimen en certificaten op te slaan, en u wilt de toegang tot uw sleutel kluis blok keren via het open bare Internet.
* U de toegang tot uw sleutel kluis wilt vergren delen zodat alleen uw toepassing of een korte lijst met aangewezen hosts verbinding kan maken met uw sleutel kluis.
* U hebt een toepassing die wordt uitgevoerd in uw virtuele Azure-netwerk en dit virtuele netwerk is vergrendeld voor al het binnenkomende en uitgaande verkeer. Uw toepassing moet nog steeds verbinding maken met Key Vault om geheimen of certificaten op te halen, of om cryptografische sleutels te gebruiken.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Key Vault-firewalls en virtuele netwerken configureren

Hier volgen de stappen die nodig zijn voor het configureren van firewalls en virtuele netwerken. Deze stappen zijn van toepassing, ongeacht of u Power shell, de Azure CLI of de Azure Portal gebruikt.

1. Schakel [Key Vault logboek registratie](logging.md) in om gedetailleerde toegangs logboeken te bekijken. Dit helpt in diagnostische gegevens, wanneer firewalls en regels voor virtuele netwerken toegang tot een sleutel kluis verhinderen. (Deze stap is optioneel, maar wordt nadrukkelijk aanbevolen.)
2. Schakel **service-eind punten in voor de sleutel kluis** voor virtuele netwerken en subnetten die doel zijn.
3. Stel firewalls en regels voor virtuele netwerken in voor een sleutel kluis om de toegang tot de sleutel kluis te beperken op basis van specifieke virtuele netwerken, subnetten en IPv4-adresbereiken.
4. Als deze sleutel kluis toegankelijk moet zijn voor vertrouwde micro soft-Services, schakelt u de optie voor het toestaan van **vertrouwde Azure-Services** om verbinding te maken met Key Vault.

Zie [Azure Key Vault firewalls en virtuele netwerken configureren](network-security.md)voor meer informatie.

> [!IMPORTANT]
> Als de firewallregels van kracht zijn, kunnen gebruikers alleen Key Vault-[gegevenslaagbewerkingen](secure-your-key-vault.md#data-plane-access-control) uitvoeren wanneer hun aanvragen afkomstig zijn van toegestane virtuele netwerken of IPv4-adresbereiken. Dit is tevens van toepassing voor toegang tot Key Vault vanuit Azure Portal. Hoewel gebruikers kunnen bladeren naar een sleutelkluis van Azure Portal, kunnen ze mogelijk geen sleutels, geheimen of certificaten weergeven als hun clientcomputer niet in de lijst met toegestane clients staat. Dit is ook van invloed op de Key Vault-kiezer door andere Azure-Services. Gebruikers zien mogelijk een lijst met sleutelkluizen, maar geen lijst met sleutels als firewallregels hun clientcomputer weigeren.


> [!NOTE]
> Houd rekening met de volgende configuratielimieten:
> * Er zijn maximaal 127 regels voor virtuele netwerken en 127 IPv4-regels toegestaan. 
> * Kleine adresbereiken die de voorvoegselgrootten /31 of /32 gebruiken, worden niet ondersteund. Configureer deze bereiken in plaats hiervan door afzonderlijke IP-adresregels te gebruiken.
> * IP-netwerkregels zijn alleen toegestaan voor openbare IP-adressen. IP-adresbereiken die zijn gereserveerd voor privénetwerken (zoals gedefinieerd in RFC 1918) zijn niet toegestaan in IP-regels. Privénetwerken omvatten adressen die beginnen met **10.** , **172.16-31**, en **192.168.** . 
> * Momenteel worden alleen IPv4-adressen ondersteund.

## <a name="trusted-services"></a>Vertrouwde services

Hier volgt een lijst met vertrouwde services die toegang mogen hebben tot een sleutel kluis als de optie **vertrouwde services toestaan** is ingeschakeld.

|Vertrouwde service|Ondersteunde gebruiks scenario's|
| --- | --- |
|Azure Virtual Machines Deployment-service|[Implementeer certificaten op vm's vanuit door de klant beheerde Key Vault](/archive/blogs/kv/updated-deploy-certificates-to-vms-from-customer-managed-key-vault).|
|Implementatie service voor Azure Resource Manager-sjabloon|[Beveiligde waarden door geven tijdens de implementatie](../../azure-resource-manager/templates/key-vault-parameter.md).|
|Azure-toepassing gateway v2 SKU|[TLS-beëindiging met Key Vault certificaten](../../application-gateway/key-vault-certs.md)|
|Volume Encryption-service Azure Disk Encryption|Toegang tot de BitLocker-sleutel (Windows-VM) of DM-wachtwoordzin (Linux-VM) en sleutel versleutelings sleutel toestaan tijdens de implementatie van de virtuele machine. Hiermee maakt u [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md)mogelijk.|
|Azure Backup|Het maken van back-ups en het herstellen van relevante sleutels en geheimen tijdens Azure Virtual Machines backup toestaan met behulp van [Azure backup](../../backup/backup-overview.md).|
|Exchange Online & share point online|Toegang tot de klant sleutel toestaan voor de code ring van Azure Storage-service met de code van de [klant](/microsoft-365/compliance/customer-key-overview).|
|Azure Information Protection|Toegang tot de Tenant sleutel voor [Azure Information Protection toestaan.](/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Implementeer het Azure web app-certificaat via Key Vault](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Azure SQL Database|[Transparent Data Encryption met Bring your own Key ondersteuning voor Azure SQL database en Azure Synapse Analytics](../../azure-sql/database/transparent-data-encryption-byok-overview.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current).|
|Azure Storage|[Storage service Encryption door de klant beheerde sleutels gebruiken in azure Key Vault](../../storage/common/customer-managed-keys-configure-key-vault.md).|
|Azure Data Lake Store|[Versleuteling van gegevens in azure data Lake Store](../../data-lake-store/data-lake-store-encryption.md) met een door de klant beheerde sleutel.|
|Azure Databricks|[Snelle, eenvoudige en samen werkende, Apache Spark-gebaseerde analyse service](/azure/databricks/scenarios/what-is-azure-databricks)|
|Azure API Management|[Certificaten voor een aangepast domein van Key Vault implementeren met behulp van MSI](../../api-management/api-management-howto-use-managed-service-identity.md#use-ssl-tls-certificate-from-azure-key-vault)|
|Azure Data Factory|[Referenties voor gegevens opslag ophalen in Key Vault van Data Factory](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure Event Hubs|[Toegang verlenen tot een sleutel kluis voor het scenario door de klant beheerde sleutels](../../event-hubs/configure-customer-managed-key.md)|
|Azure Service Bus|[Toegang verlenen tot een sleutel kluis voor het scenario door de klant beheerde sleutels](../../service-bus-messaging/configure-customer-managed-key.md)|
|Azure Import/Export| [Door de klant beheerde sleutels gebruiken in Azure Key Vault voor de import/export-service](../../storage/common/storage-import-export-encryption-key-portal.md)
|Azure Container Registry|[Register versleuteling met door de klant beheerde sleutels](../../container-registry/container-registry-customer-managed-keys.md)

> [!NOTE]
> U moet de relevante Key Vault toegangs beleid instellen zodat de bijbehorende services toegang krijgen tot Key Vault.

## <a name="next-steps"></a>Volgende stappen

* [Uw Key Vault beveiligen](secure-your-key-vault.md)
* [Azure Key Vault-firewalls en virtuele netwerken configureren](network-security.md)
