---
title: Další kroky pro vytvoření projektu Service Fabric | Microsoft Docs
description: Další informace o projektu aplikace, kterou jste právě vytvořili, v sadě Visual Studio.  Naučte se vytvářet služby, pomocí kurzy a další informace o vývoji služeb pro Service Fabric.
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
ms.openlocfilehash: a87dd6f4afa152aebafdde24defcabe841ae2e9c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="your-service-fabric-application-and-next-steps"></a>Vaše aplikace Service Fabric a další kroky
Vaše aplikace Azure Service Fabric byla vytvořena. Tento článek popisuje některé kurzy k vyzkoušení, způsob vytvoření projektu, některé další informace, které je zajímají a potenciální další kroky.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Začínáme s ukázky, kurzy a návodů
Jste připravení začít?  

Fungovat prostřednictvím kurz .NET pro aplikace. Zjistěte, jak [sestavení aplikace](service-fabric-tutorial-create-dotnet-app.md) s ASP.NET Core, která je front-end a stavové back-end, [nasazení aplikace](service-fabric-tutorial-deploy-app-to-party-cluster.md) do clusteru, [konfigurace CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md), a [nastavení monitorovací a diagnostické](service-fabric-tutorial-monitoring-aspnet.md).

Vyzkoušejte jeden z následujících návodů a vytvořit první...
- [Služba jazyka C# spolehlivé služby v systému Windows](service-fabric-reliable-services-quick-start.md) 
- [C# Reliable Actors služby v systému Windows](service-fabric-reliable-actors-get-started.md) 
- [Spustitelný soubor služby hostované v systému Windows](quickstart-guest-app.md) 
- [Aplikace typu kontejner pro Windows](service-fabric-get-started-containers.md) 

Také může být zájem o vyzkoušení naše [ukázkové aplikace](http://aka.ms/servicefabricsamples).

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Máte dotazy nebo připomínky?  Je třeba ohlásit problém?
Pročtěte [běžné otázky](service-fabric-common-questions.md) a najděte odpovědi na co můžete udělat Service Fabric a jak se má použít.

[Možnosti podpory](service-fabric-support.md) uvádí fóra na StackOverflow a MSDN pro dotazy a také možnosti požadující hlášení problémů, podpoře a odeslání názoru produktu.

## <a name="the-application-project"></a>Projekt aplikace
Každé nové aplikace zahrnuje projekt aplikace. Může mít jeden nebo dva další projekty, v závislosti na typu služby vybrali.

Projekt aplikace se skládá z:

* Sada odkazů na služby, které tvoří vaši aplikaci.
* Tři publikační profily (1uzlu místní, 5uzlu místní a cloudové), které můžete použít ke správě předvoleb pro práci s různých prostředích – například předvolby týkající se koncový bod clusteru a zda k provedení upgradu nasazení ve výchozím nastavení.
* Soubory tři parametr aplikace (stejné jako výše), můžete použít ke správě konfigurace specifické pro prostředí aplikace, například počet oddílů pro vytvoření pro službu. Zjistěte, jak [konfiguraci vaší aplikace pro prostředí s více](service-fabric-manage-multiple-environment-app-configuration.md).
* Skript nasazení, který můžete použít k nasazení aplikace z příkazového řádku nebo jako součást automatizované průběžnou integraci a nasazení kanálu. Další informace o [nasazení aplikací pomocí prostředí PowerShell](service-fabric-deploy-remove-applications.md).
* Manifest aplikace, která popisuje aplikace. Ve složce ApplicationPackageRoot můžete najít v manifestu. Další informace o [manifestů aplikace a služby](service-fabric-application-model.md).



## <a name="learn-more-about-the-programming-models"></a>Další informace o programovací modely
Service Fabric nabízí několik způsobů, jak zapsat a spravovat vaše služby.  Tady je přehled a koncepční informace [bezzstavovými i stavovými službami spolehlivé](service-fabric-reliable-services-introduction.md), [Reliable Actors](service-fabric-reliable-actors-introduction.md), [kontejnery](service-fabric-containers-overview.md), [hosta spustitelné soubory ](service-fabric-guest-executables-introduction.md), a [bezzstavovými i stavovými službami ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="learn-about-service-communication"></a>Další informace o komunikace služby
Aplikace Service Fabric se skládá z různých služeb, kde každá služba provede specializované úlohu. Tyto služby mohou komunikovat navzájem a mohou být klientské aplikace mimo cluster, které se připojují k a komunikovat se službami. Zjistěte, jak [nastavení komunikace s a mezi vaší služby](service-fabric-connect-and-communicate-with-services.md) v Service Fabric. 

## <a name="learn-about-configuring-application-security"></a>Další informace o konfiguraci zabezpečení aplikací
Můžete zabezpečit aplikace, které jsou spuštěny v clusteru v rámci jiné uživatelské účty. Service Fabric také pomáhá zabezpečit prostředky, které jsou používány aplikací v době nasazení podle uživatelských účtů – například soubory, adresářů a certifikáty. Díky spuštěné aplikace, i v prostředí sdílené hostované bezpečnější od sebe navzájem.  Zjistěte, jak [konfigurovat zásady zabezpečení pro vaši aplikaci](service-fabric-application-runas-security.md).

Aplikace může obsahovat citlivé informace, jako je například úložiště připojovací řetězce, hesla nebo jiné hodnoty, které by neměly být zpracovány v prostém textu. Zjistěte, jak [spravovat tajných klíčů v aplikaci](service-fabric-application-secret-management.md).

## <a name="learn-about-the-application-lifecycle"></a>Další informace o životním cyklu aplikací
Jako s jinými platformami, aplikace Service Fabric obvykle projde má tyto fáze: návrh, vývoj, testování, nasazení, upgrade, údržbu a odebírání. [Tento článek](service-fabric-application-lifecycle.md) poskytuje přehled o rozhraní API a jak se používají různé role v průběhu fáze životního cyklu aplikace Service Fabric.

## <a name="next-steps"></a>Další postup
- [Vytvoření clusteru se systémem Windows v Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Vizualizace clusteru, včetně nasazené aplikace a fyzické rozložení s [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
- [Verze a upgradujte vašim službám](service-fabric-application-upgrade-tutorial.md)


