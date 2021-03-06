---
title: Webservices beveiligen met behulp van TLS
titleSuffix: Azure Machine Learning
description: Meer informatie over het inschakelen van HTTPS met TLS-versie 1,2 om een webservice te beveiligen die via Azure Machine Learning wordt geïmplementeerd.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 11/18/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.openlocfilehash: 97017e104ecff38ebf4e475fb5f6ae42707ef10e
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94919587"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>TLS gebruiken om een webservice te beveiligen via Azure Machine Learning


Dit artikel laat u zien hoe u een webservice kunt beveiligen die via Azure Machine Learning wordt geïmplementeerd.

U gebruikt [https](https://en.wikipedia.org/wiki/HTTPS) om de toegang tot webservices te beperken en de gegevens te beveiligen die door clients worden verzonden. Met HTTPS kunt u de communicatie tussen een client en een webservice beveiligen door de communicatie tussen de twee te versleutelen. Versleuteling maakt gebruik van [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS wordt soms nog steeds aangeduid als *Secure Sockets Layer* (SSL). Dit is de voorafgaande taak van TLS.

> [!TIP]
> De Azure Machine Learning SDK gebruikt de term ' SSL ' voor eigenschappen die betrekking hebben op beveiligde communicatie. Dit betekent niet dat uw webservice geen gebruik maakt van *TLS*. SSL is slechts een vaker herken bare periode.
>
> Met name webservices die via Azure Machine Learning worden geïmplementeerd, ondersteunen TLS-versie 1,2 voor AKS en ACI-nieuwe implementaties. Als u een oudere TLS-versie hebt, wordt u aangeraden om in ACI-implementaties opnieuw te implementeren om de meest recente versie van TLS op te halen.

TLS en SSL zijn beide afhankelijk van *digitale certificaten*, die u helpen bij het versleutelen en verifiëren van de identiteit. Zie de [open bare-sleutel infrastructuur](https://en.wikipedia.org/wiki/Public_key_infrastructure)van het Wikipedia-onderwerp voor meer informatie over de werking van digitale certificaten.

> [!WARNING]
> Als u geen HTTPS gebruikt voor uw webservice, zijn gegevens die naar en van de service worden verzonden mogelijk zichtbaar voor anderen op internet.
>
> HTTPS zorgt er ook voor dat de client de authenticiteit van de server waarmee verbinding wordt gemaakt, verifieert. Deze functie beschermt clients tegen [man-in-the-Middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) -aanvallen.

Dit is het algemene proces voor het beveiligen van een webservice:

1. Haal een domein naam op.

2. Een digitaal certificaat ophalen.

3. De webservice implementeren of bijwerken met TLS ingeschakeld.

4. Werk uw DNS bij om naar de webservice te verwijzen.

> [!IMPORTANT]
> Als u implementeert in azure Kubernetes service (AKS), kunt u uw eigen certificaat kopen of een certificaat gebruiken dat door micro soft wordt verschaft. Als u een certificaat van micro soft gebruikt, hoeft u geen domein naam of TLS/SSL-certificaat op te halen. Zie de sectie [TLS en implementeren inschakelen](#enable) in dit artikel voor meer informatie.

Er zijn kleine verschillen bij het beveiligen van s in verschillende [implementatie doelen](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Een domeinnaam ophalen

Als u nog geen domein naam hebt, kunt u er een aanschaffen bij een *domein naam registratie*. Het proces en de prijs verschillen per registratie. De registratie service voorziet in hulpprogram ma's voor het beheren van de domein naam. U kunt deze hulpprogram ma's gebruiken om een Fully Qualified Domain Name (FQDN) (zoals www \. contoso.com) toe te wijzen aan het IP-adres dat als host fungeert voor uw webservice.

## <a name="get-a-tlsssl-certificate"></a>Een TLS/SSL-certificaat ophalen

Er zijn veel manieren om een TLS/SSL-certificaat (digitaal certificaat) te verkrijgen. Het meest voorkomende is om een van een certificerings *instantie* (CA) te kopen. Ongeacht waar u het certificaat krijgt, hebt u de volgende bestanden nodig:

* Een **certificaat**. Het certificaat moet de volledige certificaat keten bevatten en moet ' PEM-encoded ' zijn.
* Een **sleutel**. De sleutel moet ook worden PEM-gecodeerd.

Wanneer u een certificaat aanvraagt, moet u de FQDN-namen opgeven van het adres dat u wilt gebruiken voor de webservice (bijvoorbeeld www \. -contoso.com). Het adres dat is gestempeld in het certificaat en het adres dat de clients gebruiken, worden vergeleken om de identiteit van de webservice te controleren. Als deze adressen niet overeenkomen, wordt er een fout bericht weer gegeven.

> [!TIP]
> Als de certificerings instantie het certificaat en de sleutel niet kan leveren als met PEM gecodeerde bestanden, kunt u een hulp programma zoals [openssl](https://www.openssl.org/) gebruiken om de indeling te wijzigen.

> [!WARNING]
> Gebruik *zelfondertekende* certificaten alleen voor ontwikkeling. Gebruik deze niet in productie omgevingen. Zelfondertekende certificaten kunnen problemen veroorzaken in uw client toepassingen. Zie de documentatie voor de netwerk bibliotheken die uw client toepassing gebruikt voor meer informatie.

## <a name="enable-tls-and-deploy"></a><a id="enable"></a> TLS inschakelen en implementeren

Als u de service wilt implementeren (of opnieuw wilt implementeren) met TLS ingeschakeld, stelt u de para meter *ssl_enabled* in op ' True ', waar dit van toepassing is. Stel de para meter *ssl_certificate* in op de waarde van het *certificaat* bestand. Stel de *ssl_key* in op de waarde van het *sleutel* bestand.

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Implementeren op AKS en veld-Programmeer bare poort matrix (FPGA)

  > [!NOTE]
  > De informatie in deze sectie is ook van toepassing wanneer u een beveiligde webservice voor de ontwerp functie implementeert. Als u niet bekend bent met het gebruik van de python-SDK, raadpleegt u [Wat is de Azure machine learning SDK voor python?](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).

Wanneer u implementeert in AKS, kunt u een nieuw AKS-cluster maken of een bestaande toevoegen. Zie [een model implementeren in een Azure Kubernetes-service cluster](how-to-deploy-azure-kubernetes-service.md)voor meer informatie over het maken of koppelen van een cluster.
  
-  Als u een nieuw cluster maakt, gebruikt u **[AksCompute.provisioning_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#&preserve-view=trueprovisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)**.
- Als u een bestaand cluster koppelt, gebruikt u **[AksCompute.attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#&preserve-view=trueattach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)**. Beide retour neren een configuratie object dat een **enable_ssl** methode heeft.

De **enable_ssl** -methode kan gebruikmaken van een certificaat dat door micro soft wordt verschaft of een certificaat dat u aanschaft.

> [!WARNING]
> Als uw AKS-cluster is geconfigureerd met een interne load balancer, wordt het gebruik van een door micro soft opgegeven certificaat __niet ondersteund__. Voor het gebruik van een door micro soft opgegeven certificaat is een open bare IP-bron vereist in azure, die niet beschikbaar is voor AKS wanneer deze is geconfigureerd voor interne load balancer.

  * Wanneer u een certificaat van micro soft gebruikt, moet u de para meter *leaf_domain_label* gebruiken. Met deze para meter wordt de DNS-naam voor de service gegenereerd. Bijvoorbeeld: de waarde ' Contoso ' maakt de domein naam ' contoso \<six-random-characters> . \<azureregion> . cloudapp.azure.com ', waarbij \<azureregion> is de regio waarin de service is opgenomen. U kunt desgewenst de para meter *overwrite_existing_domain* gebruiken om de bestaande *leaf_domain_label* te overschrijven.

    Als u de service wilt implementeren (of opnieuw wilt implementeren) met TLS ingeschakeld, stelt u de para meter *ssl_enabled* in op ' True ', waar dit van toepassing is. Stel de para meter *ssl_certificate* in op de waarde van het *certificaat* bestand. Stel de *ssl_key* in op de waarde van het *sleutel* bestand.

    > [!IMPORTANT]
    > Wanneer u een certificaat van micro soft gebruikt, hoeft u geen eigen certificaat of domein naam aan te schaffen.

    In het volgende voor beeld ziet u hoe u een configuratie maakt waarmee een TLS/SSL-certificaat van micro soft wordt ingeschakeld:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```

  * Wanneer u *een certificaat gebruikt dat u hebt aangeschaft*, gebruikt u de para meters *ssl_cert_pem_file*, *ssl_key_pem_file* en *ssl_cname* . In het volgende voor beeld ziet u hoe u *. pem* -bestanden kunt gebruiken om een configuratie te maken die gebruikmaakt van een TLS/SSL-certificaat dat u hebt aangeschaft:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Zie [AksProvisioningConfiguration.enable_ssl ()](/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?preserve-view=true&view=azure-ml-py#&preserve-view=trueenable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) en [AksAttachConfiguration.enable_ssl ()](/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?preserve-view=true&view=azure-ml-py#&preserve-view=trueenable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-)voor meer informatie over *enable_ssl*.

### <a name="deploy-on-azure-container-instances"></a>Implementeren in Azure Container Instances

Wanneer u implementeert op Azure Container Instances, geeft u waarden op voor TLS-gerelateerde para meters, zoals in het volgende code fragment wordt weer gegeven:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Zie [AciWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)voor meer informatie.

## <a name="update-your-dns"></a>Uw DNS bijwerken

Vervolgens moet u uw DNS bijwerken zodat deze naar de webservice verwijst.

+ **Voor Container Instances:**

  Gebruik de hulpprogram ma's uit uw domein naam REGI ster om de DNS-record voor uw domein naam bij te werken. De record moet verwijzen naar het IP-adres van de service.

  Er kan een vertraging van minuten of uren zijn voordat clients de domein naam kunnen omzetten, afhankelijk van de registratie en de TTL (time to Live) die is geconfigureerd voor de domein naam.

+ **Voor AKS:**

  > [!WARNING]
  > Als u *leaf_domain_label* hebt gebruikt om de service te maken met behulp van een certificaat van micro soft, moet u de DNS-waarde voor het cluster niet hand matig bijwerken. De waarde moet automatisch worden ingesteld.
  >
  > Als uw AKS-cluster is geconfigureerd met een interne load balancer, wordt het gebruik van een door micro soft opgegeven certificaat (door *leaf_domain_label*) __niet ondersteund__. Voor het gebruik van een door micro soft opgegeven certificaat is een open bare IP-bron vereist in azure, die niet beschikbaar is voor AKS wanneer deze is geconfigureerd voor interne load balancer.
  Werk de DNS van het open bare IP-adres van de AKS-cluster op het tabblad **configuratie** onder **instellingen** in het linkerdeel venster. (Zie de volgende afbeelding.) Het open bare IP-adres is een resource type dat wordt gemaakt onder de resource groep die de AKS-agent knooppunten en andere netwerk bronnen bevat.

  [![Azure Machine Learning: webservices beveiligen met TLS](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-tlsssl-certificate"></a>Het TLS/SSL-certificaat bijwerken

TLS/SSL-certificaten verlopen en moeten worden vernieuwd. Dit gebeurt meestal elk jaar. Gebruik de informatie in de volgende secties om uw certificaat bij te werken en te vernieuwen voor modellen die zijn geïmplementeerd in azure Kubernetes service:

### <a name="update-a-microsoft-generated-certificate"></a>Een door micro soft gegenereerd certificaat bijwerken

Als het certificaat oorspronkelijk is gegenereerd door micro soft (als de *leaf_domain_label* wordt gebruikt om de service te maken), gebruikt u een van de volgende voor beelden om het certificaat bij te werken:

> [!IMPORTANT]
> * Als het bestaande certificaat nog geldig is, gebruikt u `renew=True` (SDK) of `--ssl-renew` (CLI) om de configuratie te forceren om deze te vernieuwen. Als het bestaande certificaat bijvoorbeeld 10 dagen nog geldig is en u niet gebruikt `renew=True` , kan het certificaat mogelijk niet worden verlengd.
> * Wanneer de service oorspronkelijk is geïmplementeerd, `leaf_domain_label` wordt het gebruikt om een DNS-naam te maken met behulp van het patroon `<leaf-domain-label>######.<azure-region>.cloudapp.azure.net` . Als u de bestaande naam (inclusief de oorspronkelijk gegenereerde zes cijfers) wilt behouden, gebruikt u de oorspronkelijke `leaf_domain_label` waarde. Neem niet de 6 cijfers op die zijn gegenereerd.

**De SDK gebruiken**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True, renew=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**De CLI gebruiken**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True --ssl-renew
```

Zie voor meer informatie de volgende naslag documenten:

* [SslConfiguration](/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?preserve-view=true&view=azure-ml-py)
* [AksUpdateConfiguration](/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?preserve-view=true&view=azure-ml-py)

### <a name="update-custom-certificate"></a>Aangepast certificaat bijwerken

Als het certificaat oorspronkelijk is gegenereerd door een certificerings instantie, gebruikt u de volgende stappen:

1. Gebruik de documentatie van de certificerings instantie om het certificaat te vernieuwen. Met dit proces worden nieuwe certificaat bestanden gemaakt.

1. Gebruik de SDK of CLI om de service bij te werken met het nieuwe certificaat:

    **De SDK gebruiken**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **De CLI gebruiken**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

Zie voor meer informatie de volgende naslag documenten:

* [SslConfiguration](/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?preserve-view=true&view=azure-ml-py)
* [AksUpdateConfiguration](/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?preserve-view=true&view=azure-ml-py)

## <a name="disable-tls"></a>TLS uitschakelen

Als u TLS wilt uitschakelen voor een model dat is geïmplementeerd in azure Kubernetes service, maakt u een `SslConfiguration` met `status="Disabled"` en voert u een update uit:

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable TLS
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>Volgende stappen
Leer hoe u het volgende doet:
+ [Een machine learning model gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md)
+ [Overzicht van virtuele netwerk isolatie en privacy](how-to-network-security-overview.md)