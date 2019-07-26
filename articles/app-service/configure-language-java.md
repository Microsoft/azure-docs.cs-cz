---
title: Konfigurace aplikací pro Windows Java – Azure App Service | Microsoft Docs
description: Naučte se konfigurovat aplikace Java tak, aby běžely na výchozích instancích Windows v Azure App Service.
keywords: Azure App Service, Web App, Windows, OSS, Java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: c1ea306d8a6b5c1876ac6a9288820e1592dbfda6
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498517"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Konfigurace aplikace pro Windows Java pro Azure App Service

Azure App Service umožňuje vývojářům Java rychle sestavovat, nasazovat a škálovat balíčky webových aplikací standardu Java Standard (SE) na plně spravovanou službu pro Windows. Nasaďte aplikace pomocí modulů plug-in Maven z příkazového řádku nebo v editorech, jako je IntelliJ, zatmění nebo Visual Studio Code.

Tato příručka poskytuje klíčové koncepty a pokyny pro vývojáře v jazyce Java, které využívají v App Service. Pokud jste nikdy Azure App Service nepoužili, měli byste si nejdřív projít modulem [rychlý Start Java](app-service-web-get-started-java.md) . Obecné otázky týkající se použití App Service, které nejsou specifické pro vývoj v jazyce Java, jsou zodpovězeny v tématu [Nejčastější dotazy k App Service Windows](faq-configuration-and-management.md).

> [!NOTE]
> Nemůžete najít, co hledáte? Informace o nasazení a zabezpečení vaší aplikace Java najdete v části [Nejčastější dotazy k Windows OSS](faq-configuration-and-management.md) nebo v [příručce pro konfiguraci Java Linux](containers/configure-language-java.md) .

## <a name="configuring-tomcat"></a>Konfigurace Tomcat

Pokud chcete upravit Tomcat `server.xml` nebo jiné konfigurační soubory, nejdřív si poznamenejte hlavní verzi Tomcat na portálu.

1. Najděte domovský adresář Tomcat pro vaši verzi spuštěním `env` příkazu. Vyhledejte proměnnou prostředí, která začíná `AZURE_TOMCAT`a odpovídá vaší hlavní verzi. Například `AZURE_TOMCAT85_HOME` odkazuje na adresář Tomcat pro Tomcat 8,5.
1. Po identifikaci domovského adresáře Tomcat pro vaši verzi zkopírujte konfigurační adresář do `D:\home`. Například pokud `AZURE_TOMCAT85_HOME` má `D:\Program Files (x86)\apache-tomcat-8.5.37`hodnotu, nová cesta kopírovaného adresáře `D:\home\apache-tomcat-8.5.37`bude.

Nakonec restartujte App Service. Vaše nasazení by mělo jít `D:\home\site\wwwroot\webapps` stejně jako dřív.

## <a name="java-runtime-statement-of-support"></a>Příkaz Java Runtime pro podporu

### <a name="jdk-versions-and-maintenance"></a>Verze a údržba JDK

Podporovaná sada Java Development Kit (JDK) pro Azure je zajištěná [Zulu](https://www.azul.com/downloads/azure-only/zulu/) prostřednictvím [systémů Azul](https://www.azul.com/).

Hlavní aktualizace verze se poskytnou prostřednictvím nových možností modulu runtime v Azure App Service pro Windows. Zákazníci aktualizují tyto novější verze Java tím, že nakonfigurují nasazení App Service a zodpovídá za testování a zajištění významné aktualizace, které vyhovují jejich potřebám.

Podporované sady JDK se na čtvrtletní bázi automaticky opravují v lednu, dubnu, červenci a říjnu každého roku.

### <a name="security-updates"></a>Aktualizace zabezpečení

Opravy a opravy pro hlavní slabá místa zabezpečení budou vydány, jakmile budou dostupné ze systémů Azul. "Hlavní" ohrožení zabezpečení je definováno základním skóre 9,0 nebo vyšším v [systému NIST Common zranitelnost Standard, verze 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Vyřazení a vyřazení z provozu

Pokud bude vyřazení podporované běhové prostředí Java, budou se vývojáři Azure, kteří používají modul runtime, předávat oznámení o zastaralosti alespoň šest měsíců před vyřazením modulu runtime.

### <a name="local-development"></a>Místní vývoj

Vývojáři mohou stáhnout provozní edici Azul Zulu Enterprise JDK pro místní vývoj z [webu pro stažení Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Vývojová podpora

Podpora produktů pro [JDK Zulu s podporou Azure Azul](https://www.azul.com/downloads/azure-only/zulu/) je dostupná prostřednictvím Microsoftu při vývoji pro Azure nebo [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) s kvalifikovaným [plánem podpory Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Podpora modulu CLR

Vývojáři mohou [otevřít problém](/azure/azure-supportability/how-to-create-azure-support-request) s Azul Zulu sady JDK prostřednictvím podpory Azure, pokud mají [kvalifikovaný plán podpory](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Další postup

Toto téma poskytuje příkaz Java Runtime, který podporuje Azure App Service ve Windows.

- Další informace o hostování webových aplikací pomocí Azure App Service naleznete v tématu [App Service Overview](overview.md).
- Informace o vývoji Java v prostředí Azure najdete v tématu [Azure for Java Dev Center](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
