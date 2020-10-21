---
title: Další kroky při vytváření projektu Service Fabric
description: Seznamte se s projektem aplikace, který jste právě vytvořili v aplikaci Visual Studio.  Naučte se vytvářet služby pomocí kurzů a získat další informace o vývoji služeb pro Service Fabric.
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 01a69016e0c299fba0365fab5332b572fd2ca87a
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92314475"
---
# <a name="your-service-fabric-application-and-next-steps"></a>Vaše aplikace Service Fabric a další kroky
Vaše aplikace Azure Service Fabric se vytvořila. Tento článek popisuje některé kurzy, které si vyzkoušíte, strukturu projektu, další informace, které vás mohou zajímat, a další možné kroky.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Začínáme s kurzy, návodi a ukázkami
Jste připraveni začít?  

Pracujte v kurzu aplikace .NET. Naučte se, jak [vytvořit aplikaci](service-fabric-tutorial-create-dotnet-app.md) s ASP.NET Core front-end a stavovým back-endu, [nasadit aplikaci](service-fabric-tutorial-deploy-app-to-party-cluster.md) do clusteru, [nakonfigurovat CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)a [nastavit monitorování a diagnostiku](service-fabric-tutorial-monitoring-aspnet.md).

Nebo si vyzkoušejte jeden z následujících podrobných pokynů a vytvořte si první...
- [Služba Reliable Services C# ve Windows](service-fabric-reliable-services-quick-start.md) 
- [Služba Reliable Actors C# ve Windows](service-fabric-reliable-actors-get-started.md) 
- [Služba spustitelných souborů hosta ve Windows](quickstart-guest-app.md) 
- [Aplikace typu kontejner pro Windows](service-fabric-get-started-containers.md) 

Může vás taky zajímat, jak vyzkoušet naše [ukázkové aplikace](/samples/browse/?products=azure).

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Máte otázky nebo připomínky?  Potřebujete nahlásit problém?
Přečtěte si [Nejčastější dotazy](service-fabric-common-questions.md) a vyhledejte odpovědi, které Service Fabric můžou dělat a jak se mají použít.

[Možnosti podpory](service-fabric-support.md) vypisuje fóra na STACKOVERFLOW a MSDN, kde můžete klást otázky a také možnosti vytváření sestav, získat podporu a odesílat zpětnou vazbu k produktu.

## <a name="the-application-project"></a>Projekt aplikace
Každá nová aplikace zahrnuje projekt aplikace. V závislosti na zvoleném typu služby může existovat jeden nebo dva další projekty.

Projekt aplikace se skládá z těchto:

* Sada odkazů na služby, které tvoří vaši aplikaci.
* Tři publikační profily (místní a 5 uzlů, místní a cloudové), které můžete použít k údržbě předvoleb pro práci s různými prostředími – jako jsou třeba předvolby související s koncovým bodem clusteru a zda se ve výchozím nastavení provádí nasazení upgradu.
* Tři soubory parametrů aplikace (stejné jako výše), které můžete použít k údržbě konfigurací aplikací specifických pro prostředí, jako je počet oddílů, které se mají pro službu vytvořit. Naučte se [Konfigurovat aplikaci pro více prostředí](service-fabric-manage-multiple-environment-app-configuration.md).
* Skript nasazení, který můžete použít k nasazení aplikace z příkazového řádku nebo v rámci automatizovaného kanálu pro průběžnou integraci a nasazení. Přečtěte si další informace o [nasazování aplikací pomocí PowerShellu](service-fabric-deploy-remove-applications.md).
* Manifest aplikace, který popisuje aplikaci. Manifest najdete ve složce ApplicationPackageRoot. Přečtěte si další informace o [manifestech aplikací a služeb](service-fabric-application-model.md).



## <a name="learn-more-about-the-programming-models"></a>Další informace o programovacích modelech
Service Fabric nabízí několik způsobů, jak psát a spravovat vaše služby.  Zde najdete přehled a koncepční informace o [bezstavovém a stavovém Reliable Services](service-fabric-reliable-services-introduction.md), [Reliable Actors](service-fabric-reliable-actors-introduction.md), [kontejnerech](service-fabric-containers-overview.md), [spustitelných souborech hosta](service-fabric-guest-executables-introduction.md)a [bezstavových a stavových ASP.NET Core službách](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="learn-about-service-communication"></a>Další informace o komunikaci se službou
Service Fabric aplikace se skládá z různých služeb, kde každá služba provádí specializované úkoly. Tyto služby mohou vzájemně komunikovat a klientské aplikace mohou být mimo cluster, které se připojují ke službám a komunikují s nimi. Naučte se, jak [nastavit komunikaci s a mezi vašimi službami](service-fabric-connect-and-communicate-with-services.md) v Service Fabric. 

## <a name="learn-about-configuring-application-security"></a>Informace o konfiguraci zabezpečení aplikací
V rámci různých uživatelských účtů můžete zabezpečit aplikace spuštěné v clusteru. Service Fabric také pomáhá zabezpečit prostředky používané aplikacemi v době nasazení v rámci uživatelských účtů, například soubory, adresáře a certifikáty. Díky tomu jsou aplikace spuštěné, i ve sdíleném hostovaném prostředí, od druhé.  Přečtěte si, jak [nakonfigurovat zásady zabezpečení pro vaši aplikaci](service-fabric-application-runas-security.md).

Vaše aplikace může obsahovat citlivé informace, jako jsou například připojovací řetězce úložiště, hesla nebo jiné hodnoty, které by neměly být zpracovány v prostém textu. Naučte se [spravovat tajné klíče ve vaší aplikaci](service-fabric-application-secret-management.md).

## <a name="learn-about-the-application-lifecycle"></a>Další informace o životním cyklu aplikace
Stejně jako u jiných platforem aplikace Service Fabric obvykle prochází následujícími fázemi: návrh, vývoj, testování, nasazení, upgrade, údržba a odebrání. [Tento článek](service-fabric-application-lifecycle.md) poskytuje přehled rozhraní API a způsob jejich používání různými rolemi v průběhu fáze životního cyklu Service Fabric aplikací.

## <a name="next-steps"></a>Další kroky
- [Vytvořte v Azure cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Pomocí [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)Vizualizujte cluster, včetně nasazených aplikací a fyzického rozložení.
- [Verze a upgrade služeb](service-fabric-application-upgrade-tutorial.md)