---
title: Ukázkové šablony Azure Resource Manageru – App Service | Microsoft Docs
description: Ukázkové šablony Azure Resource Manageru pro funkci Web Apps služby App Service
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
ms.openlocfilehash: 155b47fc4c664701ec0f21bdc5ae34f3d7f666ff
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
ms.locfileid: "29933064"
---
# <a name="azure-resource-manager-templates-for-web-apps"></a>Šablony Azure Resource Manageru pro Web Apps

Následující tabulka obsahuje odkazy na šablony Azure Resource Manageru pro funkci Web Apps služby Azure App Service. Doporučení, jak se vyhnout běžným chybám při vytváření šablon webových aplikací, najdete v tématu [Doprovodné materiály k nasazování webových aplikací pomocí šablon Azure Resource Manageru](web-sites-rm-template-guidance.md).

| | |
|-|-|
|**Nasazení webové aplikace**||
| [Webová aplikace propojená s úložištěm GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Nasadí webovou aplikaci Azure, která si přetáhne kód z GitHubu. |
| [Webová aplikace s vlastními sloty nasazení](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Nasadí webovou aplikaci Azure s vlastními sloty nebo prostředími nasazení. |
|**Konfigurace webové aplikace**||
| [Certifikát webové aplikace z trezoru klíčů](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Nasadí certifikát webové aplikace Azure z tajného kódu služby Azure Key Vault a použije ho k vytvoření vazby SSL. |
| [Webová aplikace s vlastní doménou](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Nasadí webovou aplikaci Azure s vlastním názvem hostitele. |
| [Webová aplikace s vlastní doménou a SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Nasadí webovou aplikaci Azure s vlastním názvem hostitele a ze služby Key Vault získá certifikát webové aplikace pro vytvoření vazby SSL. |
| [Webová aplikace s rozšířením jazyka Go](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Nasadí webovou aplikaci Azure s rozšířením webu pro jazyk Go. Pak můžete v Azure spouštět webové aplikace vyvinuté v jazyce Go. |
| [Webová aplikace s Javou 8 a Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Nasadí webovou aplikaci Azure s povolenou Javou 8 a Tomcat 8. Pak můžete v Azure spouštět aplikace v Javě. |
|**Webová aplikace v Linuxu**||
| [Webová aplikace v Linuxu s MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Nasadí webovou aplikaci Azure v Linuxu se službou Azure Database for MySQL. |
| [Webová aplikace v Linuxu s PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Nasadí webovou aplikaci Azure v Linuxu se službou Azure Database for PostgreSQL. |
|**Webová aplikace s připojenými prostředky**||
| [Webová aplikace s MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Nasadí webovou aplikaci Azure ve Windows se službou Azure Database for MySQL. |
| [Webová aplikace s PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Nasadí webovou aplikaci Azure ve Windows se službou Azure Database for PostgreSQL. |
| [Webová aplikace s databází SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Nasadí webovou aplikaci Azure a databázi SQL na úrovni služby Basic. |
| [Webová aplikace s připojením k úložišti objektů blob](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Nasadí webovou aplikaci Azure s připojovacím řetězcem úložiště objektů blob v Azure. Pak můžete ve webové aplikaci využívat úložiště objektů blob. |
| [Webová aplikace s mezipamětí Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Nasadí webovou aplikaci Azure s mezipamětí Redis. |
|**App Service Environment pro PowerApps**||
| [Vytvoření prostředí App Service v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Vytvoří ve vaší virtuální síti prostředí App Service v2. |
| [Vytvoření prostředí App Service v2 s adresou interního nástroje pro vyrovnávání zatížení](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Vytvoří ve vaší virtuální síti prostředí App Service v2 s privátní adresou interního nástroje pro vyrovnávání zatížení. |
| [Konfigurace výchozího certifikátu SSL pro prostředí App Service nebo prostředí App Service v2 s interním nástrojem pro vyrovnávání zatížení](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Nakonfiguruje výchozí certifikát SSL pro prostředí App Service nebo prostředí App Service v2 s interním nástrojem pro vyrovnávání zatížení. |
| | |
