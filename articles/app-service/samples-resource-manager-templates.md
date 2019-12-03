---
title: Ukázky šablon Azure Resource Manager
description: V některých běžných scénářích App Service najdete ukázky šablon Azure Resource Manager. Naučte se automatizovat nasazení App Service nebo úlohy správy.
author: tfitzmac
tags: azure-service-management
ms.topic: sample
ms.date: 01/04/2019
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 58871035eac89931dce0c1cd289cf3fab97c0c3e
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688419"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>Šablony Azure Resource Manager pro App Service

Následující tabulka obsahuje odkazy na šablony Azure Resource Manager pro Azure App Service. Doporučení k zamezení běžným chybám při vytváření šablon aplikací najdete v tématu [pokyny k nasazení aplikací pomocí šablon Azure Resource Manager](deploy-resource-manager-template.md).

Další informace o syntaxi a vlastnostech JSON pro App Services prostředky najdete v tématu [typy prostředků Microsoft. Web](/azure/templates/microsoft.web/allversions).

| | |
|-|-|
|**Nasazení aplikace**||
| [App Service plánování a základní aplikace pro Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Nasadí aplikaci App Service, která je nakonfigurovaná pro Linux. |
| [App Service plánování a základní aplikace pro Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Nasadí aplikaci App Service, která je nakonfigurovaná pro Windows. |
| [Aplikace propojená s úložištěm GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Nasadí App Service aplikaci, která načte kód z GitHubu. |
| [Aplikace s vlastními sloty nasazení](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Nasadí aplikaci App Service s vlastními sloty pro nasazení nebo prostředími. |
|**Konfigurace aplikace**||
| [Certifikát aplikace z Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Nasadí App Service certifikát aplikace z Azure Key Vault tajného klíče a použije ho pro vazbu SSL. |
| [Aplikace s vlastní doménou](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Nasadí aplikaci App Service s vlastním názvem hostitele. |
| [Aplikace s vlastní doménou a SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Nasadí aplikaci App Service s vlastním názvem hostitele a získá certifikát aplikace z Key Vault vazby SSL. |
| [Aplikace s rozšířením GoLang](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Nasadí aplikaci App Service s rozšířením webu golang. Pak můžete v Azure spouštět webové aplikace vyvinuté v jazyce Golang. |
| [Aplikace s Java 8 a Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Nasadí aplikaci App Service s povoleným jazykem Java 8 a Tomcat 8. Pak můžete v Azure spouštět aplikace v Javě. |
|**Aplikace pro Linux s připojenými prostředky**||
| [Aplikace v systému Linux s MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Nasadí App Service aplikaci v systému Linux pomocí Azure Database for MySQL. |
| [Aplikace na platformě Linux s PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Nasadí App Service aplikaci v systému Linux pomocí Azure Database for PostgreSQL. |
|**Aplikace s připojenými prostředky**||
| [Aplikace s MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Nasadí aplikaci App Service ve Windows pomocí Azure Database for MySQL. |
| [Aplikace s PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Nasadí aplikaci App Service ve Windows pomocí Azure Database for PostgreSQL. |
| [Aplikace s databází SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Nasadí aplikaci App Service a SQL Database na úrovni služeb Basic. |
| [Aplikace s připojením k úložišti objektů BLOB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Nasadí aplikaci App Service s připojovacím řetězcem úložiště objektů BLOB v Azure. Z aplikace pak můžete použít úložiště objektů BLOB. |
| [Aplikace s mezipamětí Azure pro Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Nasadí aplikaci App Service s mezipamětí Azure pro Redis. |
|**App Service Environment pro PowerApps**||
| [Vytvoření prostředí App Service v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Vytvoří ve vaší virtuální síti prostředí App Service v2. |
| [Vytvoření prostředí App Service v2 s adresou interního nástroje pro vyrovnávání zatížení](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Vytvoří ve vaší virtuální síti prostředí App Service v2 s privátní adresou interního nástroje pro vyrovnávání zatížení. |
| [Konfigurace výchozího certifikátu SSL pro prostředí App Service nebo prostředí App Service v2 s interním nástrojem pro vyrovnávání zatížení](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Nakonfiguruje výchozí certifikát SSL pro prostředí App Service nebo prostředí App Service v2 s interním nástrojem pro vyrovnávání zatížení. |
| | |
