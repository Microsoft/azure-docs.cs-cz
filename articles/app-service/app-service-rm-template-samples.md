---
title: Ukázkové šablony Azure Resource Manageru – App Service | Microsoft Docs
description: Ukázkové šablony Azure Resource Manageru pro funkci Web Apps služby App Service
services: app-service
documentationcenter: app-service
author: tfitzmac
tags: azure-service-management
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 10/15/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: a7d97546d6691c5aa25010c680d37be18eb561d1
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53015003"
---
# <a name="azure-resource-manager-templates-for-web-apps"></a>Šablony Azure Resource Manageru pro Web Apps

Následující tabulka obsahuje odkazy na šablony Azure Resource Manageru pro funkci Web Apps služby Azure App Service. Doporučení, jak se vyhnout běžným chybám při vytváření šablon webových aplikací, najdete v tématu [Doprovodné materiály k nasazování webových aplikací pomocí šablon Azure Resource Manageru](web-sites-rm-template-guidance.md).

| | |
|-|-|
|**Nasazení webové aplikace**||
| [Plán služby App Service a základní webová aplikace pro Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) | Nasadí webovou aplikaci Azure nakonfigurovanou pro Linux. |
| [Plán služby App Service a základní webová aplikace pro Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) | Nasadí webovou aplikaci Azure nakonfigurovanou pro Windows. |
| [Webová aplikace propojená s úložištěm GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Nasadí webovou aplikaci Azure, která si přetáhne kód z GitHubu. |
| [Webová aplikace s vlastními sloty nasazení](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Nasadí webovou aplikaci Azure s vlastními sloty nebo prostředími nasazení. |
|**Konfigurace webové aplikace**||
| [Certifikát webové aplikace z trezoru klíčů](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Nasadí certifikát webové aplikace Azure z tajného kódu služby Azure Key Vault a použije ho k vytvoření vazby SSL. |
| [Webová aplikace s vlastní doménou](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Nasadí webovou aplikaci Azure s vlastním názvem hostitele. |
| [Webová aplikace s vlastní doménou a SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Nasadí webovou aplikaci Azure s vlastním názvem hostitele a ze služby Key Vault získá certifikát webové aplikace pro vytvoření vazby SSL. |
| [Webová aplikace s rozšířením jazyka Go](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Nasadí webovou aplikaci Azure s rozšířením webu Golang. Pak můžete v Azure spouštět webové aplikace vyvinuté v jazyce Golang. |
| [Webová aplikace s Javou 8 a Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Nasadí webovou aplikaci Azure s povolenou Javou 8 a Tomcat 8. Pak můžete v Azure spouštět aplikace v Javě. |
|**Webová aplikace pro Linux s připojenými prostředky**||
| [Webová aplikace v Linuxu s MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Nasadí webovou aplikaci Azure v Linuxu se službou Azure Database for MySQL. |
| [Webová aplikace v Linuxu s PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Nasadí webovou aplikaci Azure v Linuxu se službou Azure Database for PostgreSQL. |
|**Webová aplikace s připojenými prostředky**||
| [Webová aplikace s MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Nasadí webovou aplikaci Azure ve Windows se službou Azure Database for MySQL. |
| [Webová aplikace s PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Nasadí webovou aplikaci Azure ve Windows se službou Azure Database for PostgreSQL. |
| [Webová aplikace s databází SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Nasadí webovou aplikaci Azure a databázi SQL na úrovni služby Basic. |
| [Webová aplikace s připojením k úložišti objektů blob](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Nasadí webovou aplikaci Azure s připojovacím řetězcem úložiště objektů blob v Azure. Pak můžete ve webové aplikaci využívat úložiště objektů blob. |
| [Webové aplikace pomocí mezipaměti Azure redis Cache](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Nasadí webovou aplikaci Azure s Azure Cache pro Redis. |
|**App Service Environment pro PowerApps**||
| [Vytvoření prostředí App Service v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Vytvoří ve vaší virtuální síti prostředí App Service v2. |
| [Vytvoření prostředí App Service v2 s adresou interního nástroje pro vyrovnávání zatížení](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Vytvoří ve vaší virtuální síti prostředí App Service v2 s privátní adresou interního nástroje pro vyrovnávání zatížení. |
| [Konfigurace výchozího certifikátu SSL pro prostředí App Service nebo prostředí App Service v2 s interním nástrojem pro vyrovnávání zatížení](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Nakonfiguruje výchozí certifikát SSL pro prostředí App Service nebo prostředí App Service v2 s interním nástrojem pro vyrovnávání zatížení. |
| | |
