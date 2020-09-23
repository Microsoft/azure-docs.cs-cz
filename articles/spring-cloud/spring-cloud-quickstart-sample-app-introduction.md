---
title: Rychlý Start – Úvod do ukázkové aplikace – jarní Cloud v Azure
description: Popisuje ukázkovou aplikaci použitou v této sérii rychlých startů pro nasazení do jarního cloudu Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: dd36bb18e84ea299195b77286887a3b279f81469
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90903596"
---
# <a name="introduction-to-the-sample-app"></a>Seznámení s ukázkovou aplikací

::: zone pivot="programming-language-csharp"
Tato série rychlých startů používá ukázkovou aplikaci složenou ze dvou mikroslužeb k zobrazení způsobu nasazení aplikace .NET Core Steeltoe do služby jarní cloudu Azure. Použijete možnosti Azure jaře cloudu, jako je například zjišťování služeb, konfigurační server, protokoly, metriky a distribuované trasování.

## <a name="functional-services"></a>Funkční služby

Ukázková aplikace se skládá ze dvou mikroslužeb:

* `planet-weather-provider`Služba vrátí text v počasí jako odpověď na požadavek HTTP, který určuje název globálním. Například může vracet "" "velmi teplé" pro rtuť globálním. Získává data o počasí z konfiguračního serveru. Konfigurační server získá data o počasí ze souboru YAML v úložišti Git, například:

  ```yaml
  MercuryWeather: very warm
  VenusWeather: quite unpleasant
  MarsWeather: very cool
  SaturnWeather: a little bit sandy
  ```

* `solar-system-weather`Služba vrátí data pro čtyři planet v reakci na požadavek HTTP. Získává data tím, že do nich přivedou čtyři požadavky HTTP `planet-weather-provider` . Používá službu zjišťování serveru Eureka k volání `planet-weather-provider` . Vrátí JSON, například:

  ```json
  [{
      "Key": "Mercury",
      "Value": "very warm"
  }, {
      "Key": "Venus",
      "Value": "quite unpleasant"
  }, {
      "Key": "Mars",
      "Value": "very cool"
  }, {
      "Key": "Saturn",
      "Value": "a little bit sandy"
  }]
  ```

Následující diagram znázorňuje architekturu ukázkové aplikace:

:::image type="content" source="media/spring-cloud-quickstart-sample-app-introduction/sample-app-diagram.png" alt-text="Diagram ukázkové aplikace":::

## <a name="code-repository"></a>Úložiště kódu

Ukázková aplikace se nachází ve složce [steeltoe-Sample](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample) v úložišti Azure-Samples/Azure-jaře-Cloud-Samples na GitHubu.

Pokyny v následujících rychlých startech podle potřeby odkazují na zdrojový kód.

::: zone-end

::: zone pivot="programming-language-java"
V tomto rychlém startu používáme osobní finanční vzorek s názvem PiggyMetrics, který vám ukáže, jak nasadit aplikaci do jarní cloudové služby Azure. PiggyMetrics ukazuje vzor architektury mikroslužeb a zvýrazní rozpis služeb. Uvidíte, jak se nasazuje v Azure s využitím výkonných cloudových možností Azure, včetně zjišťování služeb, konfiguračního serveru, protokolů, metrik a distribuovaného trasování.

Pokud chcete postupovat podle příkladů nasazení v cloudu Azure, stačí pouze umístění zdrojového kódu, který je k dispozici v případě potřeby.

## <a name="functional-services"></a>Funkční služby

PiggyMetrics se rozloží na tři základní mikroslužby. Všechny z nich jsou nezávisle nasaditelné aplikace uspořádané podle obchodních domén.

* **Služba účtu (která se má nasadit)**: obsahuje obecné logiky vstupu a ověřování uživatelů: položky, úspory a nastavení výdajů, úspory a nastavení účtu.
* **Služba statistiky (nepoužívá se v tomto rychlém startu)**: provádí výpočty hlavních parametrů statistiky a zachycuje časovou řadu pro každý účet. DataPoint obsahuje hodnoty normalizované na základní měnu a časové období. Tato data se používají ke sledování dynamiky peněžního toku v době životnosti účtu.
* **Služba oznamování (nepoužívá se v tomto rychlém startu)**: ukládá uživatelům kontaktní informace a nastavení oznámení, jako je například připomenutí a četnost zálohování. Naplánované pracovní procesy shromažďují požadované informace z jiných služeb a odesílají e-mailové zprávy předplatitelům předplacených zákazníků.

## <a name="infrastructure-services"></a>Služby infrastruktury

V distribuovaných systémech je několik běžných vzorů, které vám pomůžou zajistit fungování základních služeb. Jarní cloud Azure poskytuje výkonné nástroje, které zlepšují chování aplikací pro spouštění pomocí pružiny k implementaci těchto vzorů: 

* **Služba konfigurace (hostovaná pomocí jarního cloudu Azure)**: Azure pro konfiguraci jarního cloudu je horizontálně škálovatelná centralizovaná služba Konfigurace distribuovaných systémů. Používá připojitelné úložiště, které aktuálně podporuje místní úložiště, Git a podverze.
* **Zjišťování služeb (hostované v Azure jaře Cloud)**: umožňuje automatické zjišťování síťových umístění pro instance služby, které by mohly mít dynamicky přiřazené adresy kvůli automatickému škálování, selhání a upgradům.
* **Ověřovací služba (k nasazení)** Zodpovědnost za autorizaci jsou kompletně extrahovány na samostatný server, který uděluje OAuth2 tokeny pro back-endové služby prostředků. Auth server provádí autorizaci uživatelů a zabezpečenou komunikaci mezi počítačem a počítačem v hraniční síti.
* **Brána API (k nasazení)**: tři základní služby zveřejňují externí rozhraní API pro klienta. V reálných systémech se počet funkcí může velmi rychle zvětšovat pomocí složitosti systému. K vykreslování jedné komplexní webové stránky se můžou zapojit stovky služeb. Brána API je jedním vstupním bodem systému, který slouží ke zpracování požadavků a jejich směrování do příslušné služby back-end nebo k vyvolání více služeb back-end, k agregaci výsledků. 

## <a name="sample-usage-of-piggymetrics"></a>Ukázkové použití PiggyMetrics

Podrobnosti o plné implementaci najdete v tématu [PiggyMetrics](https://github.com/Azure-Samples/piggymetrics). Ukázky odkazují na zdrojový kód podle potřeby.
::: zone-end

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zřízení instance Azure jaře cloudu](spring-cloud-quickstart-provision-service-instance.md)
