---
title: Konfigurace Windows aplikace v Javě – služba Azure App Service | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat aplikace v Javě pro spuštění na výchozí instance Windows ve službě Azure App Service.
keywords: služby Azure app service, web Apps, windows, oss, java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe;cephalin
ms.custom: seodec18
ms.openlocfilehash: efeab014c7d92a6c19d516a121fbc4436925be75
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850984"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Konfigurace Windows aplikace v Javě pro Azure App Service

Azure App Service umožňuje vývojářům rychle sestavit, nasadit a škálovat své Tomcat v jazyce Java nebo Java Standard Edition (SE) zabalené webové aplikace na plně spravované služby založené na Windows. Nasazujte aplikace s moduly plug-in Maven z příkazového řádku nebo v editoru, jako je IntelliJ, Eclipse nebo Visual Studio Code.

Tato příručka obsahuje klíčové koncepty a pokyny pro vývojáře v Javě pomocí ve službě App Service. Pokud jste nikdy použili službu Azure App Service, byste si měli přečíst [Java quickstart](app-service-web-get-started-java.md) první. Obecné dotazy ohledně používání služby App Service, které nejsou specifické pro Java development v odpovědi [nejčastější dotazy týkající se App Service Windows](faq-configuration-and-management.md).

> [!NOTE]
> Nemůžete najít, co hledáte? Podrobnosti najdete [nejčastější dotazy k Windows OSS](faq-configuration-and-management.md) nebo [Průvodce konfigurací Java Linux](containers/app-service-linux-java.md) informace o nasazení a zabezpečení vaší aplikace v Javě.

## <a name="configuring-tomcat"></a>Konfigurace Tomcat

Chcete-li upravit Tomcatu `server.xml` nebo další konfigurační soubory, nejprve poznamenejte si váš hlavní verzi Tomcat na portálu.

1. Najít domovský adresář Tomcat pro vaši verzi spuštěním `env` příkazu. Vyhledejte proměnnou prostředí, který začíná `AZURE_TOMCAT`a odpovídá hlavní verze. Například `AZURE_TOMCAT85_HOME` odkazuje na adresář Tomcat pro Tomcat 8.5.
1. Po zjištění domovský adresář Tomcat pro vaši verzi, zkopírujte konfigurační adresář k `D:\home`. Například pokud `AZURE_TOMCAT85_HOME` měla hodnotu `D:\Program Files (x86)\apache-tomcat-8.5.37`, úplná cesta zkopírovaný konfigurační adresář bude `D:\home\tomcat\conf`.

A konečně restartujte službu App Service. Nasazení se měli zúčastnit `D:\home\site\wwwroot\webapps` stejně jako v minulosti.

## <a name="java-runtime-statement-of-support"></a>Java runtime prohlášení k podpoře

### <a name="jdk-versions-and-maintenance"></a>JDK verze a údržba

Azure podporovaná Java Development Kit (JDK) je [Zulu](https://www.azul.com/downloads/azure-only/zulu/) zajišťována [Azul Systems](https://www.azul.com/).

Hlavní verze aktualizace bude poskytovat prostřednictvím nové možnosti modulu runtime ve službě Azure App Service pro Windows. Zákazníci, aktualizujte na tyto novější verze Javy podle konfigurace nasazení služby App Service a jsou zodpovědné za testování a zajištění hlavní aktualizace vyhovují jejich potřebám.

Podporované JDK jsou automaticky opravit čtvrtletně v lednu, dubnu, červenci a říjnu každý rok.

### <a name="security-updates"></a>Aktualizace zabezpečení

Opravy chyb a opravy pro chyby zabezpečení hlavní vydá Jakmile budou k dispozici od Azul Systems. "Hlavní" ohrožení zabezpečení je definována základní skóre 9.0 nebo vyšší na [NIST běžné ohrožení zabezpečení hodnocení systému, verze 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Vyřazení a vyřazení z provozu

Pokud podporovaného modulu runtime Java se vyřadí z provozu, vývojáře v Azure pomocí ovlivněné runtime dostanou oznámení o zastarání aspoň šest měsíců před modul runtime byl vyřazen z provozu.

### <a name="local-development"></a>Místní vývoj

Vývojáři mohou stáhnout produkční verzi z Azul Zulu Enterprise sadu JDK pro místní vývoj z [serveru pro stahování společnosti Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Podpora vývoje

Odborná pomoc pro [podporovaných systémem Azure Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) je dostupné prostřednictvím společnosti Microsoft při vývoji pro Azure nebo [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) s [kvalifikovaný plán podpory Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Podpora modulu CLR

Mohou vývojáři [otevřete problém](/azure/azure-supportability/how-to-create-azure-support-request) s JDK Azul Zulu prostřednictvím podpory Azure, pokud mají [plán podpory kvalifikovaný](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Další postup

Toto téma obsahuje modul Runtime Java prohlášení k podpoře pro Azure App Service ve Windows.

- Další informace o hostování webových aplikací pomocí služby Azure App Service najdete v tématu [Přehled služby App Service](overview.md).
- Informace o jazyce Java v Azure – vývoj naleznete v tématu [Azure pro vývojáře Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
