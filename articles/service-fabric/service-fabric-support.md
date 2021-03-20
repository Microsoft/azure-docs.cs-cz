---
title: Další informace o možnostech podpory Azure Service Fabric
description: Podporované verze clusteru Azure Service Fabric a odkazy na lístky podpory souborů
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: 977cd79de629670cef526f072340f8897fa6446e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92316504"
---
# <a name="azure-service-fabric-support-options"></a>Možnosti podpory pro Azure Service Fabric

Vytvořili jsme spoustu možností žádosti o podporu, které vám poslouží ke správě Service Fabric clusterů a úloh aplikací. V závislosti na naléhavosti potřeby podpory a závažnosti problému si můžete vybrat možnost, která je pro vás nejvhodnější.

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Nahlásit problémy s výrobou nebo požádat o placenou podporu pro Azure

Pokud chcete ohlásit problémy související s vaším Service Fabricm clusterem běžícím na Azure, otevřete lístek podpory [na Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) nebo na [portálu podpory Microsoftu](https://support.microsoft.com/oas/default.aspx?prid=16146).

Přečtěte si další informace:
 
- [Podpora Microsoftu pro Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Microsoft Premier Support](https://support.microsoft.com/en-us/premier).

> [!Note]
> Clustery běžící na bronzové úrovni spolehlivosti nebo v clusteru s jedním uzlem vám umožní spustit pouze zkušební zatížení. Pokud dochází k problémům s clusterem, který běží v případě bronzové nebo clustery s jedním uzlem, tým podpory společnosti Microsoft vám pomůže při zmírnění tohoto problému, ale neprovede analýzu původní příčiny. Další informace najdete [v tématu charakteristiky spolehlivosti clusteru](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster).
>
> Další informace o tom, co je potřeba pro cluster připravený pro produkční prostředí, najdete v [kontrolním seznamu připravenosti na produkci](./service-fabric-production-readiness-checklist.md).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Nahlásit problémy s výrobou nebo požádat o placenou podporu pro samostatné Service Fabric clustery

Pokud chcete nahlásit problémy související s Service Fabric clustery v místním prostředí nebo v jiných cloudech, můžete na [portálu podpory Microsoftu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)otevřít lístek pro profesionální podporu.

Přečtěte si další informace:

- [Professional Support od Microsoftu pro místní podporu](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Microsoft Premier Support](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Nahlášení problémů s Service Fabric Azure

Nastavili jsme úložiště GitHub pro vytváření sestav Service Fabric problémů.  Aktivně sledujeme i následující fóra.

### <a name="github-repo"></a>Úložiště GitHub 

Nahlaste problémy s Azure Service Fabric v [Service Fabric GitHubu](https://github.com/microsoft/service-fabric/issues). Toto úložiště je určené pro vytváření sestav a sledování problémů a vytváření malých požadavků na funkce, které souvisejí s Azure Service Fabric. **Nepoužívejte tuto střední hodnotu k nahlášení problémů s živým webem**.

### <a name="stackoverflow-and-msdn-forums"></a>Fóra StackOverflow a MSDN

[Značka Service Fabric v StackOverflow][stackoverflow] a [Fórum Service Fabric na webu MSDN][msdn-forum] se nejlépe využije při dotazování na obecné otázky týkající se toho, jak platforma funguje a jak ji můžete použít k provádění určitých úloh.

### <a name="azure-feedback-forum"></a>Fórum pro názory na Azure

[Fórum Azure Feedback pro Service Fabric][uservoice-forum] je nejlepším místem pro odesílání důležitých nápadů na funkce produktu. Prozkoumáme nejoblíbenější žádosti a posuzujeme je pro naše středně dlouhodobé plánování. Doporučujeme vám, abyste rally podporu vašich návrhů v rámci komunity.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Verze Preview Service Fabric – nepodporované pro produkční použití

V některých případech využijeme speciální verze Preview, které obsahují významné změny funkcí, které bychom chtěli prošetřit zpětnou vazbu. Verze Preview byste měli používat jenom v izolovaných testovacích prostředích, která neobsluhují produkční úlohy. Provozní cluster by měl vždycky provozovat podporovanou, stabilní a Service Fabric verzi. Pro tyto verze Preview nenabízíme možnost placené podpory.

Verze Preview vždy začíná číslem hlavní a dílčí verze 255. Pokud se například zobrazí Service Fabric verze 255.255.5703.949, tato verze je ve verzi Preview a je určena pouze pro použití v testovacích clusterech. Tato vydání verze Preview jsou také oznámena na [blogu týmu Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) a budou mít podrobné informace o funkcích, které jsou k dispozici. Pomocí jedné z možností uvedených v části [nahlásit problémy s Azure Service Fabric](#report-azure-service-fabric-issues) můžete klást otázky nebo poskytovat zpětnou vazbu.

## <a name="next-steps"></a>Další kroky

[Podporované verze Service Fabric](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: /answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: ./index.yml
[sample-repos]: /samples/browse/?products=azure
[msdn-forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?category=windowsazureplatform