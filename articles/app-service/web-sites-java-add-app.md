---
title: Přidání aplikace v jazyce Java do Azure App Service Web Apps
description: V tomto kurzu se dozvíte, jak přidat stránky nebo aplikace na instanci služby Azure App Service Web Apps, která již byla konfigurována pro použití Java.
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: mbaldwin
ms.assetid: 9b46528b-e2d0-4f26-b8d7-af94bd8c31ef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/11/2018
ms.author: robmcm
ms.openlocfilehash: 71009370282fcfbd71c00b30d4ea22802035714d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="add-a-java-application-to-azure-app-service-web-apps"></a>Přidání aplikace v jazyce Java do Azure App Service Web Apps
Když jste inicializovali webové aplikace Java v [Azure App Service] [ Azure App Service] podle postupu uvedeného v [vytvoření webové aplikace Java v Azure App Service](app-service-web-get-started-java.md), můžete nahrát aplikaci tím, že vaše WAR v **webapps** složky.

Cesta k navigační **webapps** složky liší v závislosti na tom, jak nastavit instanci webové aplikace.

* Pokud jste nastavili vaší webové aplikace pomocí Azure Marketplace, cesta k **webapps** složka je ve formátu **d:\home\site\wwwroot\bin\application\_server\webapps**, kde **aplikace\_server** je název serveru aplikace platí pro instanci vaší webové aplikace. 
* Pokud jste nastavili vaší webové aplikace pomocí uživatelského rozhraní, cesta ke konfiguraci Azure **webapps** složka je ve formátu **d:\home\site\wwwroot\webapps**. 

Správa zdrojového kódu můžete použít k nahrání aplikace nebo webové stránky, včetně [scénáře průběžnou integraci](app-service-continuous-deployment.md). FTP je také možnost pro nahrávání aplikace nebo webové stránky; Další informace o nasazení aplikací přes FTP, najdete v části [nasazení vaší aplikace pomocí protokolu FTP](app-service-deploy-ftp.md).

Poznámka pro Tomcat webové aplikace: Jakmile jste odeslali soubor WAR **webapps** složky, aplikační server Tomcat zjistí, že jste jej přidali a automaticky ho načte. Všimněte si, že při kopírování souborů (jiné než soubory WAR) do kořenového adresáře, aplikační server bude nutné restartovat předtím, než se používají tyto soubory. Funkce autoload pro webové aplikace Tomcat Java, spuštěné v Azure je založena na nový soubor WAR přidávané nebo nové soubory nebo adresáře přidat do **webapps** složky. 

<a name="see-also"></a>

## <a name="see-also"></a>Viz také
Další informace o používání Javy v Azure najdete na webu [Středisko pro vývojáře Java].

[Application-insights-App-insights-Java-Get-Started](../application-insights/app-insights-java-get-started.md)

<!-- URL List -->

[Středisko pro vývojáře Java]: https://azure.microsoft.com/develop/java/
[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
