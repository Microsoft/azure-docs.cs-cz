---
title: Další kroky vytvoření projektu Service Fabric | Dokumentace Microsoftu
description: Další informace o projekt aplikace, kterou jste právě vytvořili v sadě Visual Studio.  Naučte se sestavovat služby pomocí kurzy a další informace o vývoji služeb pro Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 299d1f97-1ca9-440d-9f81-d1d0dd2bf4df
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: rwike77
ms.openlocfilehash: 4d5e74b9ecffbf8f1161cf6c5ef948cd154d993f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51233152"
---
# <a name="your-service-fabric-application-and-next-steps"></a>Aplikace Service Fabric a další kroky
Aplikace Azure Service Fabric se vytvořil. Tento článek popisuje některé kurzy k vyzkoušení, strukturu projektu, některé další informace, které byste měli zájem a potenciální další kroky.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Začínáme s kurzy, kurzy a ukázky
Chcete začít?  

Seznámení se základními kurzů k .NET aplikace. Zjistěte, jak [sestavit aplikaci](service-fabric-tutorial-create-dotnet-app.md) s front-endu ASP.NET Core a stavovým back endem, [nasazení aplikace](service-fabric-tutorial-deploy-app-to-party-cluster.md) do clusteru, [. konfigurace CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md), a [nastavení monitorování a Diagnostika](service-fabric-tutorial-monitoring-aspnet.md).

Vyzkoušejte si jednu z následujících návodů a první vytvoření...
- [C#Služby Reliable Services na Windows](service-fabric-reliable-services-quick-start.md) 
- [C#Služby Reliable Actors na Windows](service-fabric-reliable-actors-get-started.md) 
- [Služby spustitelné hostem ve Windows](quickstart-guest-app.md) 
- [Aplikace typu kontejner pro Windows](service-fabric-get-started-containers.md) 

Můžete mít také zájem o vyzkoušení navýšení kapacity našich [ukázkové aplikace](https://aka.ms/servicefabricsamples).

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Máte otázky nebo připomínky?  Je potřeba nahlásit problém?
Přečtěte si [běžné otázky](service-fabric-common-questions.md) a najít odpovědi na co můžete dělat Service Fabric a jak by měla sloužit.

[Možnosti podpory](service-fabric-support.md) uvádí fóra na StackOverflow a MSDN pro kladení otázek, jakož i možnosti pro vytváření sestav problémů, získání podpory a odesílání názor na produkt.

## <a name="the-application-project"></a>Projekt aplikace
Každá nová aplikace obsahuje projekt aplikace. Může být jeden nebo dva další projekty, v závislosti na typu zvolené služby.

Projekt aplikace se skládá ze:

* Sada odkazů na služby, které tvoří vaši aplikaci.
* Tři profily (1 uzel místní 5 uzlu místní a cloudové), které vám umožní spravovat předvolby pro práci s různými prostředími – například preference týkající se koncový bod clusteru a zda chcete provést upgrade nasazení ve výchozím nastavení publikování.
* Parametr tři aplikace soubory (stejně jako výše), že vám pomůže udržovat konfigurace specifických pro prostředí aplikací, jako je počet oddílů, chcete-li vytvořit pro službu. Zjistěte, jak [konfigurace aplikace pro víc prostředí](service-fabric-manage-multiple-environment-app-configuration.md).
* Skript nasazení, který můžete použít k nasazení aplikace z příkazového řádku nebo jako součást automatizovaný kanál průběžné integrace a nasazování. Další informace o [nasazení aplikací pomocí prostředí PowerShell](service-fabric-deploy-remove-applications.md).
* Manifest aplikace, která popisuje aplikace. Manifest můžete najít ve složce ApplicationPackageRoot. Další informace o [manifesty aplikace a služby](service-fabric-application-model.md).



## <a name="learn-more-about-the-programming-models"></a>Další informace o programovací modely
Service Fabric nabízí několik způsobů, jak zapisovat a spravovat vaše služby.  Tady je přehled a koncepční informace [bezstavové a stavové služby Reliable](service-fabric-reliable-services-introduction.md), [Reliable Actors](service-fabric-reliable-actors-introduction.md), [kontejnery](service-fabric-containers-overview.md), [spustitelné soubory hosta ](service-fabric-guest-executables-introduction.md), a [bezstavové a stavové služby ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="learn-about-service-communication"></a>Další informace o komunikaci služby
Aplikace Service Fabric se skládá z různých služeb, kde každá služba provádí specializované úlohy. Tyto služby mohou komunikovat mezi sebou a může být klientské aplikace mimo cluster připojit k a komunikace se službami. Zjistěte, jak [nastavit komunikaci s a mezi službami](service-fabric-connect-and-communicate-with-services.md) v Service Fabric. 

## <a name="learn-about-configuring-application-security"></a>Další informace o konfiguraci zabezpečení aplikací
Můžete zabezpečit aplikace, které jsou spuštěny v clusteru pod různými uživatelskými účty. Service Fabric také pomáhá je zabezpečit prostředky používané aplikacemi v okamžiku nasazení podle uživatelských účtů – například soubory, adresáře a certifikáty. Díky spouštění aplikací, i do sdíleného hostovaného prostředí, bezpečnější od sebe.  Zjistěte, jak [konfigurovat zásady zabezpečení pro vaši aplikaci](service-fabric-application-runas-security.md).

Vaše aplikace může obsahovat citlivé informace, jako je například úložiště připojovací řetězce, hesla nebo jiné hodnoty, které by neměly být zpracovat ve formátu prostého textu. Zjistěte, jak [spravovat tajné kódy ve vaší aplikaci](service-fabric-application-secret-management.md).

## <a name="learn-about-the-application-lifecycle"></a>Další informace o životním cyklu aplikace
Podle jiných platforem aplikací Service Fabric obvykle prochází následující fáze: návrh, vývoj, testování, nasazení, upgrade, údržbu a odebírání. [Tento článek](service-fabric-application-lifecycle.md) obsahuje základní informace o rozhraní API a jak se používají v různých rolích v průběhu fáze životního cyklu aplikací Service Fabric.

## <a name="next-steps"></a>Další postup
- [Vytvoření clusteru s Windows v Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Vizualizace clusteru, včetně nasazených aplikací a fyzické rozložení s [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
- [Verze a upgrade služby](service-fabric-application-upgrade-tutorial.md)


