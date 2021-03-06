---
ms.openlocfilehash: ea30fed81a7f97b6577f41692274036d2714e0c1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88690978"
---
1. Kloon de opslagplaats vanuit deze locatie: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp.
1. Open in Visual Studio Code de map waarin de opslagplaats is gedownload.
1. Ga in Visual Studio Code naar de map *src/cloud-to-device-console-app*. Maak daar een bestand en geef het de naam *appsettings.json*. Dit bestand bevat de instellingen die nodig zijn om het programma uit te voeren.
1. Kopieer de inhoud van het bestand *~/clouddrive/lva-sample/appsettings.json* dat u eerder in deze quickstart hebt gegenereerd.

    De tekst moet lijken op de volgende uitvoer.

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Ga naar de map *src/edge* en maak een bestand met de naam *.env*.
1. Kopieer de inhoud van het bestand */clouddrive/lva-sample/edge-deployment/.env*. De tekst moet lijken op de volgende code.

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"      
    ```
    