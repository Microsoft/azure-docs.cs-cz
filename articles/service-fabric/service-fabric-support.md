---
title: Informace o možnostech podpory azure service fabric
description: Podporované verze clusteru Azure Service Fabric a odkazy na lístky podpory souborů
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: 7494f0072f27f2c9b00db7070f19dfc05627eacf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282091"
---
# <a name="azure-service-fabric-support-options"></a>Možnosti podpory Azure Service Fabric

Chcete-li poskytovat odpovídající podporu pro clustery Service Fabric, které jsou spuštěny aplikace práce zatížení na, jsme nastavili různé možnosti pro vás. V závislosti na úrovni potřebné podpory a závažnosti problému můžete vybrat správné možnosti. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Nahlášení problémů s produkčním prostředím nebo vyžádání placené podpory pro Azure

Pokud chcete hlásit problémy s clusterem Service Fabric nasazeným v Azure, otevřete lístek pro podporu [na portálu Azure nebo na](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) [portálu podpory Microsoftu](https://support.microsoft.com/oas/default.aspx?prid=16146).

Další informace:
 
- [Podpora od Microsoftu pro Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Podpora společnosti Microsoft premier](https://support.microsoft.com/en-us/premier).

> [!Note]
> Clustery spuštěné na bronzové úrovni spolehlivosti nebo clusteru s jedním uzlem vám umožní spouštět pouze testovací úlohy. Pokud se setkáte s problémy s clusterem spuštěným na bronzové spolehlivosti nebo clusteru s jedním uzlem, tým podpory společnosti Microsoft vám pomůže s jeho zmírněním, ale neprovede analýzu hlavní příčiny. Další podrobnosti naleznete [v charakteristikách spolehlivosti clusteru.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster)
>
> Další informace o tom, co je požadováno pro cluster připravený k výrobě, naleznete v [kontrolním seznamu připravenosti výroby](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Nahlásit problémy s výrobou nebo požádat o placenou podporu pro samostatné clustery Service Fabric

Pokud chcete nahlásit problémy v clusteru Service Fabric nasazeném místně nebo v jiných cloudech, otevřete lístek pro odbornou podporu na [portálu podpory Microsoftu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Další informace:

- [Odborná podpora od společnosti Microsoft pro místní .](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)
- [Podpora společnosti Microsoft premier](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Nahlásit problémy s azure service fabric

Nastavili jsme úložiště GitHub pro hlášení problémů service fabric.  Aktivně také sledujeme následující fóra.

### <a name="github-repo"></a>Úložiště GitHub 

Nahlásit problémy azure service fabric na [service-fabric problémy git repo](https://github.com/Azure/service-fabric-issues). Toto repo je určené pro vytváření přehledů a sledování problémů s Azure Service Fabric a pro vytváření požadavků na malé funkce. **Nepoužívejte tuto položku k hlášení problémů s živým webem**.

### <a name="stackoverflow-and-msdn-forums"></a>Fóra StackOverflow a MSDN

[Značka Service Fabric na StackOverflow][stackoverflow] a [fórum Service Fabric na MSDN][msdn-forum] se nejlépe používají k kladení otázek o tom, jak platforma funguje a jak s ní můžete provádět určité úkoly.

### <a name="azure-feedback-forum"></a>Fórum pro zpětnou vazbu Azure

[Fórum pro zpětnou vazbu Azure pro service fabric][uservoice-forum] je nejlepším místem pro odesílání velkých nápadů na funkce, které máte pro produkt, když kontrolujeme nejoblíbenější požadavky, které jsou součástí našeho střednědobého až dlouhodobého plánování. Doporučujeme vám, abyste shromáždili podporu pro vaše návrhy v rámci komunity.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Service Fabric Preview verze - nepodporované pro produkční použití

Čas od času vydáváme verze, které mají významné funkce, na které chceme zpětnou vazbu, které jsou vydány jako náhledy. Tyto verze náhledu by měly být používány pouze pro testovací účely. Produkční cluster by měl vždy spouštět podporovanou, stabilní verzi Service Fabric. Verze preview vždy začíná číslem hlavní a dílčí verze 255. Například pokud se zobrazí Service Fabric verze 255.255.5703.949, tato verze je pouze pro použití v testovacích clusterech a je ve verzi preview. Tyto verze preview jsou také oznámeny na [blogu týmu Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric) a budou mít podrobnosti o funkcích.
Pro tyto verze preview neexistuje žádná možnost placené podpory. Pomocí jedné z možností uvedených v části [Report Azure Service Fabric problémy](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) klást otázky nebo poskytnout zpětnou vazbu.

## <a name="next-steps"></a>Další kroky

[Podporované verze service fabric](service-fabric-versions.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples
