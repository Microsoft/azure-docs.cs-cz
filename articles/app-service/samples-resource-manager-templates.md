---
title: Ukázky šablon Azure Resource Manageru
description: Najděte ukázky šablon Azure Resource Manager pro některé běžné scénáře služby App Service. Přečtěte si, jak automatizovat úlohy nasazení nebo správy služby App Service.
author: tfitzmac
tags: azure-service-management
ms.topic: sample
ms.date: 01/04/2019
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: b1d5f20ccd2f2c637d7db668af10ef331947d018
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74971192"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>Šablony Azure Resource Manageru pro službu App Service

Následující tabulka obsahuje odkazy na šablony Azure Resource Manageru pro azure app service. Doporučení, jak se vyhnout běžným chybám při vytváření šablon aplikací, najdete v [tématu Pokyny k nasazování aplikací pomocí šablon Azure Resource Manageru](deploy-resource-manager-template.md).

Informace o syntaxi JSON a vlastnostech prostředků Služby App Services naleznete v [tématu Typy prostředků Microsoft.Web](/azure/templates/microsoft.web/allversions).

| | |
|-|-|
|**Nasazení aplikace**||
| [Plán služby App Service a základní aplikace pro Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Nasadí aplikaci služby App Service, která je nakonfigurovaná pro Linux. |
| [Plán služby App Service a základní aplikace pro Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Nasadí aplikaci Služby aplikací, která je nakonfigurovaná pro Windows. |
| [Aplikace propojená s úložištěm GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Nasadí aplikaci služby App Service, která získává kód z GitHubu. |
| [Aplikace s vlastními sloty pro nasazení](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Nasazuje aplikaci služby App Service s vlastními sloty/prostředími nasazení. |
|**Konfigurace aplikace**||
| [Certifikát aplikace z trezoru klíčů](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Nasazuje certifikát aplikace služby App Service z tajného klíče Azure Key Vault a používá jej pro vazbu SSL. |
| [Aplikace s vlastní doménou](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Nasazuje aplikaci Služby aplikací s vlastním názvem hostitele. |
| [Aplikace s vlastní doménou a SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Nasadí aplikaci služby App Service s vlastním názvem hostitele a získá certifikát aplikace z trezoru klíčů pro vazbu SSL. |
| [Aplikace s rozšířením GoLang](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Nasazuje aplikaci služby App Service s rozšířením webu Golang. Pak můžete v Azure spouštět webové aplikace vyvinuté v jazyce Golang. |
| [Aplikace s Javou 8 a Tomcat8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Nasazuje aplikaci App Service s povolenou Javou 8 a Tomcat 8. Pak můžete v Azure spouštět aplikace v Javě. |
|**Ochrana aplikace**||
| [Aplikace integrovaná s Azure Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2)| Nasazuje aplikaci služby App Service a aplikační bránu a izoluje provoz pomocí koncového bodu služby a omezení přístupu. |
|**Aplikace pro Linux s připojenými prostředky**||
| [Aplikace na Linuxu s MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Nasazuje aplikaci app service na Linuxu s Azure Database for MySQL. |
| [Aplikace na Linuxu s PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Nasazuje aplikaci služby App Service na Linuxu s Databází Azure pro PostgreSQL. |
|**Aplikace s připojenými prostředky**||
| [Aplikace s MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Nasazuje aplikaci app service ve Windows s Databází Azure pro MySQL. |
| [Aplikace s PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Nasazuje aplikaci služby App Service ve Windows s Databází Azure pro PostgreSQL. |
| [Aplikace s databází SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Nasazuje aplikaci služby App Service a databázi SQL na úrovni základní služby. |
| [Aplikace s připojením k úložišti objektů Blob](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Nasazuje aplikaci služby App Service s připojovacím řetězcem úložiště Azure blob. Potom můžete použít úložiště objektů blob z aplikace. |
| [Aplikace s Azure Cache pro Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Nasazuje aplikaci služby App Service s mezipaměť Azure pro Redis. |
|**App Service Environment**||
| [Vytvoření prostředí App Service v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Vytvoří ve vaší virtuální síti prostředí App Service v2. |
| [Vytvoření prostředí App Service v2 s adresou interního nástroje pro vyrovnávání zatížení](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Vytvoří ve vaší virtuální síti prostředí App Service v2 s privátní adresou interního nástroje pro vyrovnávání zatížení. |
| [Konfigurace výchozího certifikátu SSL pro prostředí App Service nebo prostředí App Service v2 s interním nástrojem pro vyrovnávání zatížení](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Nakonfiguruje výchozí certifikát SSL pro prostředí App Service nebo prostředí App Service v2 s interním nástrojem pro vyrovnávání zatížení. |
| | |
