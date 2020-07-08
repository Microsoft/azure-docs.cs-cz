---
title: Další informace o možnostech podpory Azure Service Fabric
description: Podporované verze clusteru Azure Service Fabric a odkazy na lístky podpory souborů
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: f2da9fbb04f9d1fa696250967db404864a3cce82
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921815"
---
# <a name="azure-service-fabric-support-options"></a>Možnosti podpory pro Azure Service Fabric

Pro zajištění vhodné podpory pro Service Fabric clusterů, na kterých běží vaše aplikace, jsme pro vás nastavili různé možnosti. V závislosti na úrovni potřeb podpory a závažnosti problému se dostanete k výběru správných možností. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Nahlásit problémy s výrobou nebo požádat o placenou podporu pro Azure

Pokud chcete nahlásit problémy v clusteru Service Fabric nasazeném v Azure, otevřete lístek pro podporu [Azure Portal nebo na](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) [portálu podpory Microsoftu](https://support.microsoft.com/oas/default.aspx?prid=16146).

Přečtěte si další informace:
 
- [Podpora Microsoftu pro Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Microsoft Premier Support](https://support.microsoft.com/en-us/premier).

> [!Note]
> Clustery běžící na bronzové úrovni spolehlivosti nebo v clusteru s jedním uzlem vám umožní spustit pouze zkušební zatížení. Pokud dochází k problémům s clusterem, který běží v případě bronzové nebo clustery s jedním uzlem, tým podpory společnosti Microsoft vám pomůže při zmírnění tohoto problému, ale neprovede analýzu původní příčiny. Další podrobnosti najdete [v tématu charakteristiky spolehlivosti clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#reliability-characteristics-of-the-cluster) .
>
> Další informace o tom, co je potřeba pro cluster připravený pro produkční prostředí, najdete v [kontrolním seznamu připravenosti na produkci](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Nahlásit problémy s výrobou nebo požádat o placenou podporu pro samostatné Service Fabric clustery

Pokud chcete nahlásit problémy s nasazeným clusterem Service Fabric místně nebo v jiných cloudech, otevřete lístek pro profesionální podporu na [portálu podpory Microsoftu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Přečtěte si další informace:

- [Professional Support od Microsoftu pro místní podporu](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Microsoft Premier Support](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Nahlášení problémů s Service Fabric Azure

Nastavili jsme úložiště GitHub pro vytváření sestav Service Fabric problémů.  Aktivně sledujeme i následující fóra.

### <a name="github-repo"></a>Úložiště GitHub 

Nahlaste se Service Fabric problémy s Azure [Service-Fabric – problémy v úložišti Git](https://github.com/Azure/service-fabric-issues). Toto úložiště je určené pro vytváření sestav a sledování problémů s Azure Service Fabric a pro vytváření malých žádostí o funkce. **Nepoužívejte to k hlášení problémů s živým webem**.

### <a name="stackoverflow-and-msdn-forums"></a>Fóra StackOverflow a MSDN

[Service Fabric značka v StackOverflow][stackoverflow] a [Fórum Service Fabric na webu MSDN][msdn-forum] se nejlépe hodí pro dotazování na to, jak platforma funguje a jak se s ní můžete provádět určité úlohy.

### <a name="azure-feedback-forum"></a>Fórum pro názory na Azure

[Fórum Azure Feedback pro Service Fabric][uservoice-forum] je nejlepší místo pro odeslání nápadů s velkými funkcemi, které máte v produktu, při kontrole nejoblíbenějších požadavků je součástí našeho středně dlouhého plánování. Doporučujeme vám, abyste rally podporu vašich návrhů v rámci komunity.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Verze Preview Service Fabric – nepodporované pro produkční použití

V době od času vydáváme verze, které mají významné funkce, pro které chceme mít k dispozici zpětnou vazbu, která je vydaná jako verze Preview. Tyto verze Preview by se měly používat jenom pro testovací účely. Provozní cluster by měl vždycky provozovat podporovanou, stabilní a Service Fabric verzi. Verze Preview vždy začíná číslem hlavní a dílčí verze 255. Pokud se například zobrazí Service Fabric verze 255.255.5703.949, vydaná verze se bude používat jenom v testovacích clusterech a je ve verzi Preview. Tato vydání verze Preview jsou také oznámena na [blogu týmu Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric) a budou mít podrobné informace o funkcích, které jsou k dispozici.
Pro tyto verze Preview se nepoužívá žádná možnost placené podpory. Pomocí jedné z možností uvedených v části [nahlásit problémy s Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) můžete klást otázky nebo poskytovat zpětnou vazbu.

## <a name="next-steps"></a>Další kroky

[Podporované verze Service Fabric](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: https://docs.microsoft.com/answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples
[msdn-forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?category=windowsazureplatform
