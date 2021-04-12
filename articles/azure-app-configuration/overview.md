---
title: Co je Azure App Configuration?
description: Přečtěte si přehled služby Azure App Configuration. Zjistěte, proč byste chtěli použít konfiguraci aplikace, a Naučte se, jak ji můžete použít.
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: overview
ms.date: 02/19/2020
ms.openlocfilehash: 9786ff20c1d3f467e217d5fe34518fcc9494863e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96930105"
---
# <a name="what-is-azure-app-configuration"></a>Co je Azure App Configuration?

Azure App Configuration poskytuje službu pro centrální správu nastavení aplikace a příznaků funkcí. Moderní programy, zejména programy běžící v cloudu, mají obecně velký počet komponent, které jsou distribuovány v podstatě. Rozprostření nastavení konfigurace mezi tyto komponenty může vést k chybám, které mohou být obtížné při nasazení aplikace. Pomocí konfigurace aplikace můžete ukládat všechna nastavení aplikace a zabezpečit jejich přístup na jednom místě.

## <a name="why-use-app-configuration"></a>Proč používat konfiguraci aplikace?

Cloudové aplikace se často spouštějí na několika virtuálních počítačích nebo kontejnerech ve více oblastech a využívají několik externích služeb. Vytvoření robustní a škálovatelné aplikace v distribuovaném prostředí představuje významnou výzvu.

Různé programovací metodologie můžou vývojářům zabývat se rostoucí složitosti při sestavování aplikací. Například [aplikace v dvanácti faktorech](https://12factor.net/) popisuje mnoho dobře testovaných architektonických vzorů a osvědčené postupy pro použití s cloudových aplikací. Jedním z klíčových doporučení tohoto průvodce je oddělit konfiguraci od kódu. Konfigurační nastavení aplikace by mělo být zachováno mimo spustitelný soubor a číst z jeho běhového prostředí nebo z externího zdroje.

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
* Šifrování citlivých informací v klidovém provozu a přenosu
* Nativní integrace s oblíbenými rozhraními

Doplňková konfigurace aplikace [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), která se používá k ukládání tajných klíčů aplikací. Konfigurace aplikace usnadňuje implementaci těchto scénářů:

* Centralizace správy a distribuce hierarchických konfiguračních dat v různých prostředích a geografických oblastech
* Dynamicky měnit nastavení aplikace bez nutnosti opětovného nasazení nebo restartování aplikace
* Řízení dostupnosti funkcí v reálném čase

## <a name="use-app-configuration"></a>Použít konfiguraci aplikace

Nejjednodušší způsob, jak přidat úložiště konfigurace aplikace do vaší aplikace, je prostřednictvím klientské knihovny poskytnuté společností Microsoft. Následující metody jsou k dispozici pro připojení k aplikaci v závislosti na zvoleném jazyce a architektuře.

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
