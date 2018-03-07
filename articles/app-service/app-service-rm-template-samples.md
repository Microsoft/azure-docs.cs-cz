---
title: "Ukázkové šablony Azure Resource Manageru – App Service | Microsoft Docs"
description: "Ukázkové šablony Azure Resource Manageru – App Service"
services: app-service
documentationcenter: app-service
author: tfitzmac
manager: timlt
editor: ggailey777
tags: azure-service-management
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 02/26/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: e28a27b9a00164fcbba6eb5d3e5435548486ffa4
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2018
---
# <a name="azure-resource-manager-templates-for-azure-web-apps"></a>Šablony Azure Resource Manageru pro Azure Web Apps

Následující tabulka obsahuje odkazy na šablony Azure Resource Manageru. Doporučení, jak se vyhnout běžným chybám při vytváření šablon webových aplikací, najdete v tématu [Doprovodné materiály k nasazování webových aplikací pomocí šablon Azure Resource Manageru](web-sites-rm-template-guidance.md).

| | |
|-|-|
|**Nasazení webové aplikace**||
| [Webová aplikace propojená s úložištěm GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Nasadí webovou aplikaci Azure, která si přetáhne kód z GitHubu. |
| [Webová aplikace s vlastními sloty nasazení](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Nasadí webovou aplikaci Azure s vlastními sloty nebo prostředími nasazení. |
|**Konfigurace webové aplikace**||
| [Certifikát webové aplikace z trezoru klíčů](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Nasadí certifikát webové aplikace Azure z tajného kódu trezoru klíčů a použije ho k vytvoření vazby SSL. |
| [Webová aplikace s vlastní doménou](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Nasadí webovou aplikaci Azure s vlastním názvem hostitele. |
| [Webová aplikace s vlastní doménou a SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Nasadí webovou aplikaci Azure s vlastním názvem hostitele a z trezorů klíčů získá certifikát webové aplikace pro vytvoření vazby SSL. |
| [Webová aplikace s rozšířením Golang](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Nasadí webovou aplikaci Azure s rozšířením webu Golang, které umožňuje spouštět v Azure webové aplikace vyvinuté v jazyce Go. |
| [Webová aplikace s Javou 8 a Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Nasadí webovou aplikaci Azure s povolenou Javou 8 a serverem Tomcat 8, které umožňují spouštět v Azure aplikace v Javě. |
|**Webová aplikace v Linuxu**||
| [Web App on Linux s MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Nasadí webovou aplikaci Azure v Linuxu se službou Azure Database for MySQL. |
| [Web App on Linux s PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Nasadí webovou aplikaci Azure v Linuxu se službou Azure Database for PostgreSQL. |
|**Webová aplikace s připojenými prostředky**||
| [Webová aplikace s MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Nasadí webovou aplikaci Azure ve Windows se službou Azure Database for MySQL. |
| [Webová aplikace s PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Nasadí webovou aplikaci Azure ve Windows se službou Azure Database for PostgreSQL. |
| [Webová aplikace se službou SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Nasadí webovou aplikaci Azure se službou SQL Database na úrovni Basic. |
| [Webová aplikace s připojením ke službě Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Nasadí webovou aplikaci Azure s připojovacím řetězcem úložiště objektů blob, který umožňuje využívat z webové aplikace službu Azure Blob Storage. |
| [Webová aplikace se službou Redis Cache](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Nasadí webovou aplikaci Azure se službou Redis Cache. |
|**App Service Environment**||
| [Vytvoření služby App Service Environment v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Vytvoří ve vaší virtuální síti službu App Service Environment v2. |
| [Vytvoření služby App Service Environment v2 s adresou interního nástroje pro vyrovnávání zatížení](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Vytvoří ve vaší virtuální síti službu App Service Environment v2 s privátní adresou interního nástroje pro vyrovnávání zatížení. |
| [Konfigurace výchozího certifikátu SSL pro službu ASE nebo ASE v2 s interním nástrojem pro vyrovnávání zatížení](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Nakonfiguruje výchozí certifikát SSL pro službu App Service Environment nebo App Service Environment v2 s interním nástrojem pro vyrovnávání zatížení. |
| | |
