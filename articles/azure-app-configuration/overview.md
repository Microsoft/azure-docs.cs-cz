---
title: Co je Azure App Configuration?
description: Přehled služby Konfigurace aplikací Azure.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: overview
ms.date: 02/19/2020
ms.openlocfilehash: 1f1cec68813d33e7fa19a414a30adfc9a41df91f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77523471"
---
# <a name="what-is-azure-app-configuration"></a>Co je Azure App Configuration?

Azure App Configuration poskytuje službu pro centrální správu nastavení aplikací a příznaků funkcí. Moderní programy, zejména programy spuštěné v cloudu, mají obecně mnoho součástí, které jsou distribuovány v přírodě. Rozprostření nastavení konfigurace mezi tyto součásti může vést k těžko řešit chyby během nasazení aplikace. Pomocí konfigurace aplikace můžete ukládat všechna nastavení aplikace a zabezpečit jejich přístupy na jednom místě.

## <a name="why-use-app-configuration"></a>Proč používat konfiguraci aplikací?

Cloudové aplikace často běží na více virtuálních počítačích nebo kontejnerech ve více oblastech a používají více externích služeb. Vytvoření robustní a škálovatelné aplikace v distribuovaném prostředí představuje významnou výzvu.

Různé metodiky programování pomáhají vývojářům vypořádat se s rostoucí složitostí vytváření aplikací. [Například aplikace Twelve-Factor](https://12factor.net/) popisuje mnoho dobře testovaných architektonických vzorů a osvědčených postupů pro použití s cloudovými aplikacemi. Jedním z klíčových doporučení z této příručky je oddělit konfiguraci od kódu. Nastavení konfigurace aplikace by měla být zachována mimo jeho spustitelný soubor a číst v z jeho runtime prostředí nebo externí zdroj.

Zatímco každá aplikace může využít konfigurace aplikace, následující příklady jsou typy aplikací, které těží z jeho použití:

* Mikroslužby založené na službě Azure Kubernetes Service, Azure Service Fabric nebo jiných kontejnerizovaných aplikacích nasazených v jedné nebo více zeměpisných oblastech
* Aplikace bez serveru, které zahrnují Funkce Azure nebo jiné bezstavové výpočetní aplikace založené na událostech
* Kanál průběžného nasazení

Konfigurace aplikace nabízí následující výhody:

* Plně spravovaná služba, kterou lze nastavit během několika minut
* Flexibilní reprezentace a mapování klíčů
* Označování popisky
* Přehrání nastavení v čase
* Vyhrazené uI pro správu příznaku funkce
* Porovnání dvou sad konfigurací na vlastních dimenzích
* Vylepšené zabezpečení díky identitám spravovaným Azure
* Šifrování citlivých informací v klidovém stavu a při přepravě
* Nativní integrace s oblíbenými rámci

Konfigurace aplikace doplňuje [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), který se používá k ukládání tajných kódů aplikací. Konfigurace aplikace usnadňuje implementaci následujících scénářů:

* Centralizace správy a distribuce hierarchických konfiguračních dat pro různá prostředí a zeměpisné oblasti
* Dynamicky měnit nastavení aplikace bez nutnosti opětovného nasazení nebo restartování aplikace
* Řízení dostupnosti funkcí v reálném čase

## <a name="use-app-configuration"></a>Použití konfigurace aplikace

Nejjednodušší způsob, jak přidat úložiště konfigurace aplikací do aplikace, je prostřednictvím klientské knihovny poskytované společností Microsoft. Následující metody jsou k dispozici pro připojení k vaší aplikaci, v závislosti na zvoleném jazyce a rámci

| Programovací jazyk a rámec | Jak se připojit |
|---|---|
| .NET Core a ASP.NET Core | Zprostředkovatel konfigurace aplikace pro jádro .NET Core |
| Rozhraní .NET Framework a ASP.NET | Tvůrce konfigurace aplikace pro rozhraní .NET |
| Java Spring | Klient konfigurace aplikace pro Spring Cloud |
| Ostatní | Rozhraní REST konfigurace aplikace |

## <a name="next-steps"></a>Další kroky

* [ASP.NET základní rychlý start](./quickstart-aspnet-core-app.md)
* [Rychlý start jádra rozhraní .NET](./quickstart-dotnet-core-app.md)
* [Rychlý start rozhraní .NET Framework](./quickstart-dotnet-app.md)
* [Rychlý start funkce Azure](./quickstart-azure-functions-csharp.md)
* [Java jarní rychlý start](./quickstart-java-spring-app.md)
* [Rychlý start příznaku funkce ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)
* [Rychlý start příznaku funkce jarního spuštění](./quickstart-feature-flag-spring-boot.md)
