---
title: Rychlý Start – seznámení s ukázkovou aplikací metrik Piggy – jarní cloud Azure
description: Popisuje ukázkovou aplikaci metrik Piggy, která se používá v nasazení Azure jaře cloudu.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: d8eee715a3ac2256838b37c1c6722aa1307f6dd0
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2020
ms.locfileid: "89046778"
---
# <a name="introduction-to-piggy-metrics-sample-app"></a>Seznámení s ukázkovou aplikací metrik Piggy

V tomto rychlém startu používáme osobní finanční vzorek s názvem metriky Piggy, který vám ukáže, jak nasadit aplikaci do jarní cloudové služby Azure. Metriky Piggy ukazují vzor architektury mikroslužeb a zvýrazňují rozpis služeb. Uvidíte, jak se nasazuje v Azure s využitím výkonných cloudových možností Azure, včetně zjišťování služeb, konfiguračního serveru, protokolů, metrik a distribuovaného trasování.

Pokud chcete postupovat podle příkladů nasazení v cloudu Azure, stačí pouze umístění zdrojového kódu, který je k dispozici v případě potřeby.

## <a name="functional-services"></a>Funkční služby
Metriky Piggy se rozloží na tři základní mikroslužby. Všechny z nich jsou nezávisle nasaditelné aplikace uspořádané podle obchodních domén.

* **Služba účtu (která se má nasadit)**: obsahuje obecné logiky vstupu a ověřování uživatelů: položky, úspory a nastavení výdajů, úspory a nastavení účtu.
* **Služba statistiky (nepoužívá se v tomto rychlém startu)**: provádí výpočty hlavních parametrů statistiky a zachycuje časovou řadu pro každý účet. DataPoint obsahuje hodnoty normalizované na základní měnu a časové období. Tato data se používají ke sledování dynamiky peněžního toku v době životnosti účtu.
* **Služba oznamování (nepoužívá se v tomto rychlém startu)**: ukládá uživatelům kontaktní informace a nastavení oznámení, jako je například připomenutí a četnost zálohování. Naplánované pracovní procesy shromažďují požadované informace z jiných služeb a odesílají e-mailové zprávy předplatitelům předplacených zákazníků.

## <a name="infrastructure-services"></a>Služby infrastruktury
V distribuovaných systémech je několik běžných vzorů, které vám pomůžou zajistit fungování základních služeb. Jarní cloud Azure poskytuje výkonné nástroje, které zlepšují chování aplikací pro spouštění pomocí pružiny k implementaci těchto vzorů: 

* **Služba konfigurace (hostovaná pomocí jarního cloudu Azure)**: Azure pro konfiguraci jarního cloudu je horizontálně škálovatelná centralizovaná služba Konfigurace distribuovaných systémů. Používá připojitelné úložiště, které aktuálně podporuje místní úložiště, Git a podverze.
* **Zjišťování služeb (hostované v Azure jaře Cloud)**: umožňuje automatické zjišťování síťových umístění pro instance služby, které by mohly mít dynamicky přiřazené adresy kvůli automatickému škálování, selhání a upgradům.
* **Ověřovací služba (k nasazení)** Zodpovědnost za autorizaci jsou kompletně extrahovány na samostatný server, který uděluje OAuth2 tokeny pro back-endové služby prostředků. Auth server provádí autorizaci uživatelů a zabezpečenou komunikaci mezi počítačem a počítačem v hraniční síti.
* **Brána API (k nasazení)**: tři základní služby zveřejňují externí rozhraní API pro klienta. V reálných systémech se počet funkcí může velmi rychle zvětšovat pomocí složitosti systému. K vykreslování jedné komplexní webové stránky se můžou zapojit stovky služeb. Brána API je jedním vstupním bodem systému, který slouží ke zpracování požadavků a jejich směrování do příslušné služby back-end nebo k vyvolání více služeb back-end, k agregaci výsledků. 

## <a name="sample-usage-of-piggy-metrics"></a>Ukázkové použití metrik Piggy
Podrobnosti o úplné implementaci najdete v tématu [metriky Piggy](https://github.com/Azure-Samples/piggymetrics). Ukázky odkazují na zdrojový kód podle potřeby.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Zřízení instance Azure jaře cloudu](spring-cloud-quickstart-provision-service-instance.md)
