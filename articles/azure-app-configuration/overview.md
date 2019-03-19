---
title: Co je konfigurace aplikace pro Azure | Dokumentace Microsoftu
description: Přehled konfigurace Azure App service.
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
ms.openlocfilehash: 3e96a1af4746c6344f9565d0f0de8ab2b2b8d8bc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58009394"
---
# <a name="what-is-azure-app-configuration"></a>Co je konfigurace aplikace pro Azure

Konfigurace aplikace pro Azure poskytuje službu pro správu nastavení aplikace centrálně. Moderní aplikace, zejména těch, které jsou spuštěny v cloudu, mají obvykle spousta komponent, které jsou ze své podstaty distribuovány. Rozšíření nastavení konfigurace pro tyto součásti může vést k chybám obtížné vyřešit během nasazení aplikace. Konfigurace aplikace umožňuje uložit všechna nastavení pro vaši aplikaci a zabezpečení jejich přístupy na jednom místě.

Služba konfigurace aplikace je aktuálně ve **ve verzi preview**. Je pro použití během tohoto období preview zdarma. Neplatí žádné zvláštní schvalovací proces pro přístupu ke službě, [zaregistrovat](https://aka.ms/azconfig/register) s námi tak, že jsme vás bude informovat o zásadní změny předem.

## <a name="why-use-app-configuration"></a>Proč používat konfiguraci aplikací

Cloudové aplikace často spustit na několika virtuálních počítačích nebo kontejnery ve více oblastech a použít více externích služeb. Vytvoření distribuované aplikace, která je robustní a škálovatelné skutečnou výzvou. Různé programovací metody mají vzrostla usnadňuje vývojářům práci s rostoucí složitostí vytváření těchto aplikací. Například 12-factor app podrobně popisuje mnoho dobře otestovaný architektury a osvědčeným postupům pro použití s cloudovým aplikacím. Jedním z klíčových doporučení z této příručky je do samostatné konfigurace z kódu. To znamená, že konfigurace aplikace, jako je například nastavení externí, omezit na její spustitelný soubor a načtených z jeho běhové prostředí nebo externího zdroje.

Při jakékoli aplikace mohli využít, tady jsou dobrým příkladem typy aplikací, by měl používat konfiguraci aplikací:

* Mikroslužby na základě AKS, Service Fabric nebo jiných kontejnerizované aplikace nasazené v jedné nebo více zeměpisných oblastech.
* Aplikace bez serveru, včetně Azure Functions nebo jiných aplikací založené na událostech bezstavové výpočetní prostředky.
* Kanál pro průběžné nasazování.

Konfigurace aplikací nabízí následující výhody:

* Plně spravovaná služba, která můžete nastavit během několika minut.
* Mapování a flexibilní reprezentace klíče.
* Označování pomocí popisků.
* Opakování bodu v čase nastavení.
* Porovnání dvou sad konfigurace na vlastní dimenze.
* Rozšířené zabezpečení prostřednictvím identity spravované v Azure.
* Dokončení šifrování pomocí dat v klidovém stavu nebo při přenosu.
* Nativní integraci se sadou oblíbených architektur.

Konfigurace aplikace doplňuje [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) se používá k ukládání tajných klíčů aplikací. Konfigurace aplikace usnadňují implementovat následující scénáře:

* Centralizovaná správa a distribuce hierarchické konfigurační data pro různá prostředí a zeměpisných oblastech.
* Změny konfigurace dynamické bez opětovného nasazení nebo restartování aplikace.
* Funkce správy.

## <a name="how-to-use-app-configuration"></a>Jak používat konfiguraci aplikací

Nejjednodušší způsob, jak přidat, je obchod s aplikacemi konfigurace do vaší aplikace pomocí klientských knihoven, které Microsoft poskytuje. V závislosti na programovacím jazyce a framework tady zobrazit nejlepší metody dostupné pro vás:

| Programovací jazyk a rozhraní framework | Jak se připojit |
|---|---|
| **.NET core** a **ASP.NET Core** | Poskytovatel konfigurace konfigurace aplikace pro .NET Core. |
| **.NET** a **technologie ASP.NET** | Tvůrce konfigurace konfigurace aplikací pro .NET. |
| **Java Spring** | Klienta Konfigurace konfigurace aplikace pro Spring cloudem. |
| Ostatní | Konfigurace aplikace rozhraní REST API. |

## <a name="next-steps"></a>Další postup

* [Rychlé zprovoznění: Vytvoření webové aplikace ASP.NET](quickstart-aspnet-core-app.md)  
