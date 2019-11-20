---
title: Co je konfigurace aplikací Azure? | Dokumenty Microsoft
description: Přehled služby Azure App Configuration Service.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 01644054868ffc08738e67654ba181c76e7aed39
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185066"
---
# <a name="what-is-azure-app-configuration"></a>Co je konfigurace aplikací Azure?

Azure App Configuration poskytuje službu pro centrální správu nastavení aplikace a příznaků funkcí. Moderní programy, zejména programy běžící v cloudu, mají obecně velký počet komponent, které jsou distribuovány v podstatě. Rozprostření nastavení konfigurace mezi tyto komponenty může vést k chybám, které mohou být obtížné při nasazení aplikace. Pomocí konfigurace aplikace můžete ukládat všechna nastavení aplikace a zabezpečit jejich přístup na jednom místě.

Konfigurace aplikace je aktuálně ve verzi Public Preview. Během období Preview je můžete použít zdarma. Můžete se k němu zaregistrovat v [Azure Portal](https://portal.azure.com).

## <a name="why-use-app-configuration"></a>Proč používat konfiguraci aplikace?

Cloudové aplikace se často spouštějí na několika virtuálních počítačích nebo kontejnerech ve více oblastech a využívají několik externích služeb. Vytváření takových distribuovaných aplikací, které jsou robustní a škálovatelné, je výzvou.

Různé programovací metodologie můžou vývojářům zabývat se rostoucí složitosti při sestavování aplikací. Například [aplikace v dvanácti faktorech](https://12factor.net/) popisuje mnoho dobře testovaných architektonických vzorů a osvědčené postupy pro použití s cloudových aplikací. Jedním z klíčových doporučení tohoto průvodce je oddělit konfiguraci od kódu. V takovém případě by se nastavení konfigurace aplikace měla uchovávat externě pro spustitelný soubor a číst z jeho běhového prostředí nebo z externího zdroje.

I když kterákoli z aplikací může používat konfiguraci aplikace, jsou v následujících příkladech typy aplikací, které využívají jejich používání:

* Mikroslužby založené na službě Azure Kubernetes, Azure Service Fabric nebo dalších kontejnerových aplikacích nasazených v jednom nebo několika geografických oblastech
* Aplikace bez serveru, které zahrnují Azure Functions nebo jiné bezstavové výpočetní aplikace založené na událostech
* Kanál průběžného nasazování

Konfigurace aplikace nabízí následující výhody:

* Plně spravovaná služba, kterou je možné nastavit během několika minut
* Flexibilní reprezentace klíčů a mapování
* Označování pomocí popisků
* Opětovné přehrání nastavení bodu v čase
* Vyhrazené uživatelské rozhraní pro správu příznaků funkcí
* Porovnání dvou sad konfigurací v uživatelsky definovaných dimenzích
* Rozšířené zabezpečení prostřednictvím identit spravovaných Azure
* Úplné šifrování dat v klidovém nebo přenášeném režimu
* Nativní integrace s oblíbenými rozhraními

Doplňková konfigurace aplikace [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), která se používá k ukládání tajných klíčů aplikací. Konfigurace aplikace usnadňuje implementaci těchto scénářů:

* Centralizace správy a distribuce hierarchických konfiguračních dat v různých prostředích a geografických oblastech
* Dynamicky měnit nastavení aplikace bez nutnosti opětovného nasazení nebo restartování aplikace
* Řízení dostupnosti funkcí v reálném čase

## <a name="use-app-configuration"></a>Použít konfiguraci aplikace

Nejjednodušší způsob, jak přidat úložiště konfigurace aplikace do vaší aplikace, je prostřednictvím klientské knihovny, kterou poskytuje společnost Microsoft. Na základě programovacího jazyka a architektury jsou pro vás k dispozici následující nejlepší metody.

| Programovací jazyk a architektura | Jak se připojit |
|---|---|
| .NET Core a ASP.NET Core | Poskytovatel konfigurace aplikace pro .NET Core |
| .NET Framework a ASP.NET | Tvůrce konfigurace aplikací pro .NET |
| Java Spring | Klient konfigurace aplikace pro jarní Cloud |
| Ostatní | REST API konfigurace aplikace |

## <a name="next-steps"></a>Další kroky

* [Rychlý Start ASP.NET Core](./quickstart-aspnet-core-app.md)
* [Rychlý Start .NET Core](./quickstart-dotnet-core-app.md)
* [Rychlý Start .NET Framework](./quickstart-dotnet-app.md)
* [Rychlý Start Azure Functions](./quickstart-azure-functions-csharp.md)
* [Rychlý Start pružiny Java](./quickstart-java-spring-app.md)
* [Rychlý Start – příznak funkce ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)
* [Rychlý Start příznaku funkce pro spuštění pružiny](./quickstart-feature-flag-spring-boot.md)
