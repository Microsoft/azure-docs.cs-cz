---
title: Ukázky šablon Azure Resource Manager
description: V některých běžných scénářích App Service najdete ukázky šablon Azure Resource Manager. Naučte se automatizovat nasazení App Service nebo úlohy správy.
author: tfitzmac
tags: azure-service-management
ms.topic: sample
ms.date: 08/26/2020
ms.author: tomfitz
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: 5129fccceb633991767cdd3b52bbb5b6af067270
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88891041"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>Šablony Azure Resource Manager pro App Service

Následující tabulka obsahuje odkazy na šablony Azure Resource Manager pro Azure App Service. Doporučení k zamezení běžným chybám při vytváření šablon aplikací najdete v tématu [pokyny k nasazení aplikací pomocí šablon Azure Resource Manager](deploy-resource-manager-template.md).

Další informace o syntaxi a vlastnostech JSON pro App Services prostředky najdete v tématu [typy prostředků Microsoft. Web](/azure/templates/microsoft.web/allversions).

| Nasazení aplikace | Description |
|-|-|
| [App Service plánování a základní aplikace pro Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Nasadí aplikaci App Service, která je nakonfigurovaná pro Linux. |
| [App Service plánování a základní aplikace pro Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Nasadí aplikaci App Service, která je nakonfigurovaná pro Windows. |
| [Aplikace propojená s úložištěm GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Nasadí App Service aplikaci, která načte kód z GitHubu. |
| [Aplikace s vlastními sloty nasazení](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Nasadí aplikaci App Service s vlastními sloty pro nasazení nebo prostředími. |
| [Aplikace s privátním koncovým bodem](https://github.com/Azure/azure-quickstart-templates/tree/master/101-private-endpoint-webapp)| Nasadí App Service aplikaci s privátním koncovým bodem. |
|**Konfigurace aplikace**| **Popis** |
| [Certifikát aplikace z Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Nasadí App Service certifikát aplikace z Azure Key Vault tajného klíče a použije ho pro vazbu TLS/SSL. |
| [Aplikace s vlastní doménou a SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Nasadí aplikaci App Service s vlastním názvem hostitele a získá certifikát aplikace z Key Vault vazby TLS/SSL. |
| [Aplikace s rozšířením GoLang](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Nasadí aplikaci App Service s rozšířením webu golang. Pak můžete v Azure spouštět webové aplikace vyvinuté v jazyce Golang. |
| [Aplikace s Java 8 a Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Nasadí aplikaci App Service s povoleným jazykem Java 8 a Tomcat 8. Pak můžete v Azure spouštět aplikace v Javě. |
| [Aplikace s místní integrací virtuální sítě](https://github.com/Azure/azure-quickstart-templates/tree/master/101-app-service-regional-vnet-integration)| Nasadí aplikaci App Service s povolenou místní integrací virtuální sítě. |
|**Ochrana aplikace**| **Popis** |
| [Aplikace integrovaná s Azure Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2)| Nasadí aplikaci App Service a Application Gateway a izoluje provoz pomocí koncového bodu služby a omezení přístupu. |
|**Aplikace pro Linux s připojenými prostředky**| **Popis** |
| [Aplikace v systému Linux s MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Nasadí App Service aplikaci v systému Linux pomocí Azure Database for MySQL. |
| [Aplikace na platformě Linux s PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Nasadí App Service aplikaci v systému Linux pomocí Azure Database for PostgreSQL. |
|**Aplikace s připojenými prostředky**| **Popis** |
| [Aplikace s MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Nasadí aplikaci App Service ve Windows pomocí Azure Database for MySQL. |
| [Aplikace s PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Nasadí aplikaci App Service ve Windows pomocí Azure Database for PostgreSQL. |
| [Aplikace s databází v Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Nasadí aplikaci App Service a databázi v Azure SQL Database na úrovni služeb Basic. |
| [Aplikace s připojením k úložišti objektů BLOB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Nasadí aplikaci App Service s připojovacím řetězcem úložiště objektů BLOB v Azure. Z aplikace pak můžete použít úložiště objektů BLOB. |
| [Aplikace s mezipamětí Azure pro Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Nasadí aplikaci App Service s mezipamětí Azure pro Redis. |
| [Aplikace připojená k back-endu WebApp](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-privateendpoint-vnet-injection)| Nasadí dvě webové aplikace (front-end a back-end) bezpečně připojené společně se vkládáním virtuální sítě a soukromým koncovým bodem. |
|**App Service Environment**| **Popis** |
| [Vytvoření prostředí App Service v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Vytvoří ve vaší virtuální síti prostředí App Service v2. |
| [Vytvoření prostředí App Service v2 s adresou interního nástroje pro vyrovnávání zatížení](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Vytvoří ve vaší virtuální síti prostředí App Service v2 s privátní adresou interního nástroje pro vyrovnávání zatížení. |
| [Konfigurace výchozího certifikátu SSL pro prostředí App Service nebo prostředí App Service v2 s interním nástrojem pro vyrovnávání zatížení](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Nakonfiguruje výchozí certifikát TLS/SSL pro prostředí interního nástroje App Service nebo App Service prostředí interního nástroje verze 2. |
| | |
