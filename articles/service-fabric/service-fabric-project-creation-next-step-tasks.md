---
title: Další kroky vytvoření projektu Service Fabric
description: Přečtěte si o projektu aplikace, který jste právě vytvořili v sadě Visual Studio.  Zjistěte, jak vytvářet služby pomocí kurzů a další informace o vývoji služeb pro Service Fabric.
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: e8fc105657bda6114851f4819be4658926ad621c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349402"
---
# <a name="your-service-fabric-application-and-next-steps"></a>Aplikace Service Fabric a další kroky
Vaše aplikace Azure Service Fabric byla vytvořena. Tento článek popisuje některé kurzy vyzkoušet, make-up projektu, některé další informace, které by vás mohly zajímat, a potenciální další kroky.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Začínáme s kurzy, návody a ukázkami
Chcete začít?  

Práce prostřednictvím kurzu aplikace .NET. Naučte se [vytvářet aplikaci](service-fabric-tutorial-create-dotnet-app.md) s front-endem ASP.NET core a stavovým back-endem, [nasadit ji](service-fabric-tutorial-deploy-app-to-party-cluster.md) do clusteru, [nakonfigurovat CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)a [nastavit monitorování a diagnostiku](service-fabric-tutorial-monitoring-aspnet.md).

Nebo vyzkoušejte jednu z následujících procházek a vytvořte si první...
- [Služba Spolehlivé služby Jazyka C# v systému Windows](service-fabric-reliable-services-quick-start.md) 
- [Služba C# Reliable Actors v systému Windows](service-fabric-reliable-actors-get-started.md) 
- [Spustitelná služba hosta v systému Windows](quickstart-guest-app.md) 
- [Aplikace typu kontejner pro Windows](service-fabric-get-started-containers.md) 

Můžete také mít zájem vyzkoušet naše [ukázkové aplikace](https://aka.ms/servicefabricsamples).

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Máte otázky nebo připomínky?  Potřebujete nahlásit problém?
Přečtěte si [běžné otázky](service-fabric-common-questions.md) a najděte odpovědi na to, co service fabric může dělat a jak by měl být používán.

[Možnosti podpory](service-fabric-support.md) uvádí fóra o StackOverflow a MSDN pro kladení otázek, stejně jako možnosti pro hlášení problémů, získání podpory a odesílání zpětné vazby od produktu.

## <a name="the-application-project"></a>Aplikační projekt
Každá nová aplikace obsahuje aplikační projekt. V závislosti na zvoleném typu služby může existovat jeden nebo dva další projekty.

Aplikační projekt se skládá z:

* Sada odkazů na služby, které tvoří vaši aplikaci.
* Tři profily publikování (místní uzel 1 uzel, místní 5 uzlů a cloud), které můžete použít k udržování předvoleb pro práci s různými prostředími – například předvolby související s koncovým bodem clusteru a zda ve výchozím nastavení provést nasazení upgradu.
* Tři soubory parametrů aplikace (stejné jako výše), které můžete použít k údržbě konfigurace aplikací specifické pro prostředí, jako je například počet oddílů, které chcete vytvořit pro službu. Přečtěte si, jak [nakonfigurovat aplikaci pro více prostředí](service-fabric-manage-multiple-environment-app-configuration.md).
* Skript nasazení, který můžete použít k nasazení aplikace z příkazového řádku nebo jako součást automatizované průběžné integrace a nasazení kanálu. Další informace o [nasazování aplikací pomocí PowerShellu](service-fabric-deploy-remove-applications.md).
* Manifest aplikace, který popisuje aplikaci. Manifest najdete ve složce ApplicationPackageRoot. Další informace o [manifestech aplikací a služeb](service-fabric-application-model.md).



## <a name="learn-more-about-the-programming-models"></a>Další informace o programovacích modelech
Service Fabric nabízí několik způsobů, jak psát a spravovat své služby.  Zde je přehled a koncepční informace o [bezstavové a stavové spolehlivé služby](service-fabric-reliable-services-introduction.md), [spolehlivé objekty actor](service-fabric-reliable-actors-introduction.md), [kontejnery](service-fabric-containers-overview.md), [host spustitelné soubory](service-fabric-guest-executables-introduction.md)a [bezstavové a stavové ASP.NET základní služby](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="learn-about-service-communication"></a>Další informace o komunikaci se službami
Aplikace Service Fabric se skládá z různých služeb, kde každá služba provádí specializované úlohy. Tyto služby mohou vzájemně komunikovat a mimo cluster mohou existovat klientské aplikace, které se připojují ke službám a komunikují s nimi. Přečtěte si, jak [nastavit komunikaci s vašimi službami a mezi nimi](service-fabric-connect-and-communicate-with-services.md) ve službě Service Fabric. 

## <a name="learn-about-configuring-application-security"></a>Informace o konfiguraci zabezpečení aplikací
Můžete zabezpečit aplikace spuštěné v clusteru pod různými uživatelskými účty. Service Fabric také pomáhá zabezpečit prostředky, které jsou používány aplikacemi v době nasazení v rámci uživatelských účtů – například soubory, adresáře a certifikáty. Díky tomu jsou spuštěné aplikace, a to i ve sdíleném hostovaném prostředí, bezpečnější od sebe navzájem.  Přečtěte si, jak [nakonfigurovat zásady zabezpečení pro vaši aplikaci](service-fabric-application-runas-security.md).

Aplikace může obsahovat citlivé informace, jako jsou řetězce připojení úložiště, hesla nebo jiné hodnoty, které by neměly být zpracovány ve formátu prostého textu. Přečtěte si, jak [spravovat tajné klíče v aplikaci](service-fabric-application-secret-management.md).

## <a name="learn-about-the-application-lifecycle"></a>Informace o životním cyklu aplikace
Stejně jako u jiných platforem, aplikace Service Fabric obvykle prochází následující fáze: návrh, vývoj, testování, nasazení, inovace, údržba a odebrání. [Tento článek](service-fabric-application-lifecycle.md) obsahuje přehled rozhraní API a jak jsou používány různými rolemi v průběhu fází životního cyklu aplikace Service Fabric.

## <a name="next-steps"></a>Další kroky
- [Vytvořte cluster Windows v Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Vizualizujte svůj cluster, včetně nasazených aplikací a fyzického rozložení, pomocí [aplikace Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
- [Verze a upgrade služeb](service-fabric-application-upgrade-tutorial.md)


