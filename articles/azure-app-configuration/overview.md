---
title: Co je konfigurace aplikace pro Azure? | Dokumenty Microsoft
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
ms.openlocfilehash: a76eab5d51dd73fb6b38ebebaa8421e789274f84
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60201286"
---
# <a name="what-is-azure-app-configuration"></a>Co je konfigurace aplikace pro Azure?

Konfigurace aplikace pro Azure poskytuje službu centrálně spravovat nastavení aplikace. Moderní programy, zejména programy spuštěny v cloudu, mají obvykle spousta komponent, které jsou ze své podstaty distribuovány. Rozšíření nastavení konfigurace pro tyto součásti může vést k chybám obtížné vyřešit během nasazení aplikace. Použijte konfiguraci aplikací k uložení všech nastavení pro vaše aplikace a zabezpečení jejich přístupy na jednom místě.

Konfigurace aplikace je pro použití během období preview zdarma. Pokud chcete vyzkoušet, [zaregistrovat](https://aka.ms/azconfig/register) ve verzi Preview.

## <a name="why-use-app-configuration"></a>Proč používat konfiguraci aplikací?

Cloudové aplikace často spustit na několika virtuálních počítačích nebo kontejnery ve více oblastech a použít více externích služeb. Vytvoření distribuované aplikace, který je robustní a škálovatelné výzvou je i. 

Různé programovací metody pomoci vývojářům při čelit rostoucím složitost vytváření aplikací. Například 12-factor app popisuje mnoho dobře otestovaný architektury a osvědčeným postupům pro použití s cloudovým aplikacím. Jedním z klíčových doporučení z této příručky je do samostatné konfigurace z kódu. Nastavení konfigurace aplikace v tomto případě by měla externí, omezit na její spustitelný soubor a načtených z jeho běhové prostředí nebo externího zdroje.

Všechny aplikace může využívat konfigurace aplikací, jsou následující příklady typy aplikací, které využívají samosprávné ho využít:

* Mikroslužby založené na službě Azure Kubernetes, Azure Service Fabric nebo ostatní kontejnerizované aplikace nasazené v jedné nebo více geografických poloh
* Aplikace bez serveru, mezi které patří Azure Functions nebo jiných aplikací výpočetní bezstavové prostředí řízené událostmi
* Kanál pro průběžné nasazování

Konfigurace aplikací nabízí následující výhody:

* Plně spravovaná služba, která můžete nastavit během několika minut
* Flexibilní reprezentace klíče a mapování
* Označování pomocí popisků
* Opakování bodu v čase nastavení
* Porovnání dvou sad konfigurace na vlastní rozměry
* Rozšířené zabezpečení prostřednictvím identity spravované v Azure
* Úplná data šifrování, v klidovém stavu nebo při přenosu
* Nativní integraci se sadou oblíbených architektur

Konfigurace aplikace doplňuje [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), který se používá k ukládání tajných klíčů aplikací. Konfigurace aplikace usnadňuje implementaci následující scénáře:

* Centralizovaná správa a distribuce hierarchické konfigurační data pro různá prostředí a geografickými oblastmi
* Změny konfigurace dynamické bez nutnosti znovu nasadit nebo restartování aplikace
* Funkce správy

## <a name="use-app-configuration"></a>Používat konfiguraci aplikací

Nejjednodušší způsob, jak přidat, je obchod s aplikacemi konfigurace do vaší aplikace pomocí klientských knihoven, které Microsoft poskytuje. Na základě programovací jazyk a rozhraní, jsou k dispozici, následující osvědčené metody.

| Programovací jazyk a rozhraní framework | Jak se připojit |
|---|---|
| .NET core a ASP.NET Core | Poskytovatel konfigurace aplikace pro .NET Core |
| .NET a ASP.NET | Tvůrce konfigurace aplikací pro .NET |
| Java Spring | Konfigurace klienta aplikace pro Spring cloudem |
| Ostatní | Konfigurace aplikace rozhraní REST API |

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace ASP.NET Core](./quickstart-aspnet-core-app.md)  
