---
title: Verbindingsreeksen voor Synapse SQL (preview)
description: Verbindingsreeksen voor Synapse SQL (preview)
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: e34563a312c5a248e97c4aac72d06030a836727b
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684831"
---
# <a name="connection-strings-for-synapse-sql-preview"></a>Verbindingsreeksen voor Synapse SQL (preview)

U kunt via verschillende toepassingsprotocollen verbinding maken met Synapse SQL (preview), bijvoorbeeld via [ADO.NET](https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx), [ODBC](https://msdn.microsoft.com/library/jj730314.aspx), [PHP](https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396) of [JDBC](https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx). Hieronder ziet u enkele voorbeelden van verbindingsreeksen voor elk protocol. 

U kunt ook de Azure-portal gebruiken om een verbindingsreeks te bouwen.  Als u een verbindingsreeks wilt bouwen met behulp van de Azure-portal, gaat u naar uw databaseblade en selecteert u *Databaseverbindingsreeksen weergeven* onder *Essentials*.

## <a name="sample-adonet-connection-string"></a>Voorbeeld van ADO.NET-verbindingsreeks

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>Voorbeeld van ODBC-verbindingsreeks

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>Voorbeeld van PHP-verbindingsreeks

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>Voorbeeld van JDBC-verbindingsreeks

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

> [!NOTE]
> Overweeg de verbindingstime-out in te stellen op 300 seconden. De verbinding blijft dan in stand tijdens korte perioden van niet-beschikbaarheid.

## <a name="recommendations"></a>Aanbevelingen

[Azure Data Studio](get-started-azure-data-studio.md) en Azure Synapse Studio zijn de aanbevolen hulpprogramma's voor het uitvoeren van query's van een **serverloze SQL-pool**.

## <a name="next-steps"></a>Volgende stappen

Zie [Query’s uitvoeren met Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) als u wilt beginnen met het uitvoeren van query’s voor uw analyses met Visual Studio en andere toepassingen.
