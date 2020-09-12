---
title: Správa a monitorování aplikací s využitím ovladače Azure Spring Boot
description: Naučte se spravovat a monitorovat aplikace pomocí jarního spouštěcího válce.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 1c1ea19c22d003ed1aa7c3d3191ff20a69513bc5
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299503"
---
# <a name="manage-and-monitor-app-with-azure-spring-boot-actuator"></a>Správa a monitorování aplikací s využitím ovladače Azure Spring Boot

Po nasazení nového binárního souboru do aplikace budete možná chtít zjistit funkčnost a zobrazit informace o spuštěné aplikaci. Tento článek vysvětluje, jak získat přístup k rozhraní API z testovacího koncového bodu, který poskytuje Azure jarní Cloud, a zpřístupňuje funkce připravené pro produkční prostředí pro vaši aplikaci.

## <a name="prerequisites"></a>Požadavky
V tomto článku se předpokládá, že máte aplikaci s pružinou spuštění 2. x, která se dá úspěšně nasadit a spustit v Azure jaře Cloud Service.  Další informace najdete v tématu [rychlý Start: spuštění stávající cloudové aplikace Azure pro jaře pomocí Azure Portal](spring-cloud-quickstart.md)

## <a name="verify-app-through-test-endpoint"></a>Ověření aplikace prostřednictvím testovacího koncového bodu
1. Přejděte na **řídicí panel aplikace** a kliknutím na aplikaci zadejte stránku s přehledem aplikace.

1. V podokně **Přehled** byste měli vidět **testovací koncový bod**.  Přistoupit k tomuto koncovému bodu z příkazového řádku nebo prohlížeče a sledujte odpověď rozhraní API.

1. Poznamenejte si identifikátor URI **testovacího koncového bodu** , který se použije v části přichází.

>[!TIP]
> * Pokud aplikace vrátí front-end stránku a odkazuje na jiné soubory prostřednictvím relativní cesty, zkontrolujte, že koncový bod testu končí lomítkem (/). Tím se zajistí, že se soubor CSS správně načte.
> * Pokud si své rozhraní API zobrazíte z cookies a váš prohlížeč vyžaduje zadání přihlašovacích údajů pro zobrazení stránky, použijte k dekódování testovacího koncového bodu [adresu URL](https://www.urldecoder.org/) . Dekódování adresy URL vrátí adresu URL ve formátu "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/ \<app-name> / \<deployment-name> ".  Pomocí tohoto formuláře můžete získat přístup ke koncovému bodu.

## <a name="add-actuator-dependency"></a>Přidat závislost poháněcí poháněcí

Chcete-li přidat poháněcí hodnotu do projektu založeného na Maven, přidejte závislost Starter:

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
```

Zkompilujte nový binární soubor a nasaďte ho do vaší aplikace.

## <a name="enable-production-ready-features"></a>Povolit funkce připravené pro produkční prostředí
Koncové body poháněcí umožňují monitorovat a pracovat s aplikací. Ve výchozím nastavení zpřístupňuje aplikace pružiny `health` a `info` koncové body pro zobrazení informací o libovolné aplikaci a stavu.

Abychom mohli sledovat konfiguraci a konfigurovatelné prostředí, musíme povolit i `env` `configgrops` koncové body.

1. Přejděte do podokna **Přehled** aplikace, v nabídce nastavení klikněte na **Konfigurace** , přejděte na stránku konfigurace **proměnných prostředí** .
1. Do formuláře "klíč: hodnota" přidejte následující vlastnosti. V tomto prostředí se otevře koncový bod pružinového válce "ENV", "Health", "info".

   ```
   management.endpoints.web.exposure.include: env,health,info
   ```
1. Klikněte na tlačítko **Save (Uložit** ) aplikace se automaticky restartuje a načte nové proměnné prostředí.

Nyní se můžete vrátit do podokna Přehled aplikace a počkat na změnu stavu zřizování na "úspěšné".  Bude existovat více než jedna spuštěná instance.

> [!Note] 
> Jakmile aplikaci zpřístupníte veřejnosti, tyto koncové body tohoto pohánějícího jsou také zveřejněny. Všechny koncové body můžete skrýt odstraněním proměnných prostředí `management.endpoints.web.exposure.include` a nastavením `management.endpoints.web.exposure.exclude=*`

## <a name="view-the-actuator-endpoint-to-view-application-information"></a>Zobrazit koncový bod pohánějícího pro zobrazení informací o aplikaci
1. Teď můžete získat přístup k adrese URL `"<test-endpoint>/actuator/"` , abyste viděli všechny koncové body vystavené pružinovým spouštěcím poháněcí.
1. Adresa URL `"<test-endpoint>/actuator/env"` pro přístup vám umožní zobrazit aktivní profily používané aplikací a všechny načtené proměnné prostředí.
1. Pokud chcete vyhledat konkrétní prostředí, můžete získat přístup k adrese URL,  `"<test-endpoint>/actuator/env/{toMatch}"` abyste ji mohli zobrazit.

Pokud chcete zobrazit všechny předdefinované koncové body, přečtěte si téma vystavení [koncových bodů](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready-endpoints-exposing-endpoints) .

## <a name="next-steps"></a>Další kroky
* [Principy metrik pro jarní cloud Azure](spring-cloud-concept-metrics.md)
* [Principy stavu aplikace v jarním cloudu Azure](spring-cloud-concept-app-status.md))