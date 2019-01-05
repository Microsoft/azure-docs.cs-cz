---
title: Ukázkové šablony Azure Resource Manageru – App Service | Microsoft Docs
description: Ukázkové šablony Azure Resource Manageru pro službu App Service
services: app-service
documentationcenter: app-service
author: tfitzmac
tags: azure-service-management
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 01/04/2019
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 842ec98245522095334b9f17e8c12292b7c1dda8
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54035436"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>Šablony Azure Resource Manageru pro službu App Service

Následující tabulka obsahuje odkazy na šablony Azure Resource Manageru pro službu Azure App Service. Doporučení, jak se vyhnout běžným chybám při vytváření šablony aplikací, najdete v části [doprovodné materiály k nasazování aplikací pomocí šablon Azure Resource Manageru](deploy-resource-manager-template.md).

Další informace o syntaxi JSON a vlastnosti pro prostředky App Service najdete v tématu [typy prostředků Microsoft.Web](/azure/templates/microsoft.web/allversions).

| | |
|-|-|
|**Nasazení aplikace**||
| [Plán služby App Service a základní aplikace pro Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Nasadí aplikaci služby App Service, který je nakonfigurovaný pro Linux. |
| [Plán služby App Service a základní aplikace pro Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Nasadí aplikaci služby App Service, který je nakonfigurovaný pro Windows. |
| [Aplikace je propojená úložiště GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Nasadí aplikaci služby App Service, která si přetáhne kód z Githubu. |
| [Aplikace s vlastními sloty nasazení](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Nasazuje se sloty nebo prostředími nasazení vlastní aplikace služby App Service. |
|**Konfigurace aplikace**||
| [Certifikát aplikace ze služby Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Nasadí certifikát aplikace služby App Service ze Azure Key Vault tajný klíč a použije ho k vytvoření vazby SSL. |
| [Aplikace s vlastní doménou](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Nasadí aplikaci služby App Service s vlastním názvem hostitele. |
| [Aplikace s vlastní doménou a SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Nasadí aplikaci služby App Service s vlastním názvem hostitele a získá certifikát aplikace ze služby Key Vault k vytvoření vazby SSL. |
| [Aplikace s rozšířením GoLang](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Nasadí aplikaci služby App Service s rozšířením webu Golang. Pak můžete v Azure spouštět webové aplikace vyvinuté v jazyce Golang. |
| [Aplikace s Javou 8 a Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Nasadí aplikaci služby App Service s Javou 8 a Tomcat 8 povolené. Pak můžete v Azure spouštět aplikace v Javě. |
|**Aplikace pro Linux s připojenými prostředky**||
| [Aplikace v Linuxu s MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Nasadí aplikaci služby App Service v Linuxu se službou Azure Database for MySQL. |
| [Aplikace v Linuxu s PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Nasadí aplikaci služby App Service v Linuxu se službou Azure Database for PostgreSQL. |
|**Aplikace s připojenými prostředky**||
| [Aplikace s MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Nasadí aplikaci služby App Service ve Windows se službou Azure Database for MySQL. |
| [Aplikace s PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Nasadí aplikaci služby App Service ve Windows se službou Azure Database for PostgreSQL. |
| [Aplikace s SQL database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Nasadí aplikaci služby App Service a SQL database na úrovni služby Basic. |
| [Aplikace s připojením úložiště objektů Blob](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Nasadí aplikaci služby App Service pomocí připojovacího řetězce služby Azure Blob storage. Pak můžete použít úložiště objektů Blob z aplikace. |
| [Aplikace se službou Azure Cache pro redis Cache](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Nasadí aplikaci služby App Service se službou Azure Cache pro Redis. |
|**App Service Environment pro PowerApps**||
| [Vytvoření prostředí App Service v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Vytvoří ve vaší virtuální síti prostředí App Service v2. |
| [Vytvoření prostředí App Service v2 s adresou interního nástroje pro vyrovnávání zatížení](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Vytvoří ve vaší virtuální síti prostředí App Service v2 s privátní adresou interního nástroje pro vyrovnávání zatížení. |
| [Konfigurace výchozího certifikátu SSL pro prostředí App Service nebo prostředí App Service v2 s interním nástrojem pro vyrovnávání zatížení](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Nakonfiguruje výchozí certifikát SSL pro prostředí App Service nebo prostředí App Service v2 s interním nástrojem pro vyrovnávání zatížení. |
| | |
