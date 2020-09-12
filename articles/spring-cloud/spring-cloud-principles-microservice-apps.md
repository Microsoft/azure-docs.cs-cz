---
title: Java a základní operační systém pro aplikace mikroslužeb pro jarní cloudy v Azure
description: Principy udržování zdravého a základního operačního systému Java pro aplikace mikroslužeb pro práci v cloudu Azure
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/27/2020
ms.custom: devx-track-java
ms.openlocfilehash: 1c403398f7320cefa16a4a570645a6d7d750acc7
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297497"
---
# <a name="java-and-base-os-for-spring-microservice-apps"></a>Java a základní operační systém pro aplikace mikroslužeb Spring
Níže jsou uvedené zásady pro udržení zdravého a základního operačního systému Java pro aplikace pro jarní mikroslužby.
## <a name="principles-for-healthy-java-and-base-os"></a>Principy zdravého Java a základního operačního systému
* Musí se jednat o stejný základní operační systém napříč vrstvami – Basic | Standardní | Nárok.
    * V současné době aplikace v Azure jaře Cloud používají kombinaci Debian 10 a Ubuntu 18,04.
    * Služba sestavení VMware používá Ubuntu 18,04.
* Musí se jednat o stejný základní operační systém bez ohledu na počáteční body nasazení – zdroj | JAR
    * V současné době aplikace v Azure jaře Cloud používají kombinaci Debian 10 a Ubuntu 18,04.
* Základní operační systém musí být bez chyb zabezpečení.
    * Základní operační systém Debian 10 má 147 Open CVEs.
    * Základní operační systém Ubuntu 18,04 má 132 Open CVEs.
* Musí používat JRE – bez periferních zařízení.
    * V současné době aplikace v Azure jaře Cloud používají JDK. JRE – bezobslužný obrázek je menší.
* Musí používat nejnovější sestavení Java.
    * V současné době aplikace v Azure jaře Cloud používají Java 8 Build 242. Toto je zastaralé sestavení.
 
Systémy Azul budou nepřetržitě vyhledávat změny v základních operačních systémech a udržují poslední sestavené image aktuální v aktuálním stavu. Jarní cloud Azure vyhledává změny imagí a průběžně je aktualizuje napříč nasazeními.
 
## <a name="faq-for-azure-spring-cloud"></a>Nejčastější dotazy ke službě Azure Spring Cloud

* Které verze Java jsou podporovány? Hlavní verze a číslo buildu.
    * Podporuje verze LTS – Java 8 a 11.
    * Používá nejnovější sestavení – například hned teď, Java 8 Build 252 a Java 11 Build 7.
* Kdo tyto moduly Runtime Java vytvořil?
    * Systémy Azul.
* Jaký je základní operační systém pro Image?
    * Ubuntu 20,04 LTS (kontaktní fossa). Aplikace budou dál zůstat na nejnovější verzi LTS Ubuntu.
    * Viz [Ubuntu 20,04 LTS (kontaktní fossa)](http://releases.ubuntu.com/focal/)
* Jak můžu stáhnout podporovaný běhový modul Java pro místní vývoj? 
    * Přečtěte si téma [instalace JDK pro Azure a Azure Stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install)
* Jak získám podporu pro problémy na úrovni běhového prostředí Java?
    * Otevřete lístek podpory s podporou Azure.
 
## <a name="default-deployment-on-azure-spring-cloud"></a>Výchozí nasazení v jarním cloudu Azure

> ![Výchozí nasazení](media/spring-cloud-principles/spring-cloud-default-deployment.png)
 
## <a name="next-steps"></a>Další kroky
* [Rychlý Start: spuštění stávající cloudové aplikace Azure na jaře pomocí Azure Portal](spring-cloud-quickstart.md)
* [Dlouhodobá podpora jazyka Java pro Azure a Azure Stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support)
