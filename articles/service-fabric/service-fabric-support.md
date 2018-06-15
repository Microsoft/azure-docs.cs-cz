---
title: Další informace o možnostech podpory infrastruktury služby Azure | Microsoft Docs
description: Azure podporované verze clusteru Service Fabric a odkazy na soubor lístky podpory
services: service-fabric
documentationcenter: .net
author: pkcsf
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/14/2018
ms.author: pkc
ms.openlocfilehash: 7a00b72f0b8d52ae0ced5f44784c2a61d40c8ac2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208953"
---
# <a name="azure-service-fabric-support-options"></a>Možnosti podpory Azure Service Fabric

Zajistit odpovídající podpora pro vaše clusterů Service Fabric, které běží vaše pracovní zatížení aplikace na jsme nastavili různé možnosti pro vás. V závislosti na úroveň podpory potřebné a závažnost problému, získat vyberte správné možnosti. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Hlášení problémů produkční nebo požádejte placené podporu pro Azure.

Hlášení problémů v clusteru Service Fabric nasazené v Azure, otevřete lístek podpory [na portálu Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) nebo [portál podpory Microsoft](http://support.microsoft.com/oas/default.aspx?prid=16146).

Další informace o:
 
- [Podpora společnosti Microsoft pro Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Microsoft premier support](https://support.microsoft.com/en-us/premier).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Hlášení problémů produkční nebo požádejte podporu placené pro samostatnou službu, kterou clusterů Service Fabric

Pro vytváření sestav problémy v clusteru Service Fabric nasadit místně nebo na ostatních cloudů, otevřete lístek pro odborníky v oblasti podpory na [portál podpory Microsoft](http://support.microsoft.com/oas/default.aspx?prid=16146).

Další informace o:

- [Profesionální podporu společnosti Microsoft pro místní](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Microsoft premier support](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Problémy sestavy Azure Service Fabric 
Nastavili jsme úložiště GitHub pro hlášení problémů Service Fabric.  Také aktivně monitorujete následující fóra.

### <a name="github-repo"></a>Úložiště GitHub 
Hlášení problémů prostředků infrastruktury služby Azure na [problémů služby prostředků infrastruktury úložiště git](https://github.com/Azure/service-fabric-issues). Toto úložiště je určený pro generování sestav a sledování problémů s Azure Service Fabric a pro provedení žádosti o malé funkce. **Nepoužívejte tato sestava web live problémy**.

### <a name="stackoverflow-and-msdn-forums"></a>Fóra StackOverflow a MSDN
[Značky Service Fabric na StackOverflow] [ stackoverflow] a [Service Fabric fórum na webu MSDN] [ msdn-forum] jsou nejlepší pro klást otázky o tom, jak použít Platforma funguje a jak může provést určité úlohy s ním.

### <a name="azure-feedback-forum"></a>Azure fóru pro zpětnou vazbu
[Azure fóru pro zpětnou vazbu pro Service Fabric] [ uservoice-forum] je nejlepší místo pro odesílání big funkce nápady bylo k dispozici pro produkt jsme zkontrolujte nejoblíbenější žádosti jsou součástí naší středně dlouhodobé plánování. Doporučujeme vám technologie rally podporu pro vaše návrhy v rámci komunity.


## <a name="supported-service-fabric-versions"></a>Podporované verze Service Fabric.

Ujistěte se, že cluster bude vždy spuštěn na podporovanou verzi Service Fabric. Jak a kdy jsme oznamujeme vydání nové verze Service Fabric, předchozí verze je označena k ukončení podpory po 60 dnů od tohoto dne. Nové verze není zmínka [na blogu týmu Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

Naleznete v následujících dokumentech na podrobnosti o tom, jak udržovat clusteru spuštěna podporovaná verze Service Fabric.

- [Upgrade verze Service Fabric v clusteru služby Azure ](service-fabric-cluster-upgrade.md)
- [Upgrade verze Service Fabric na samostatný server clusteru systému windows ](service-fabric-cluster-upgrade-windows-server.md)
 
Tady najdete seznam verzí Service Fabric, které jsou podporovány a koncovém datu jejich podporu.

| **Modulu runtime Service Fabric v clusteru** | **Můžete upgradovat přímo z verze clusteru** |**Kompatibilní SDK / verze balíčku NuGet** | **Koncové datum podpory** |
| --- | --- |--- | --- |
| Všechny verze clusteru před 5.3.121 | 5.1.158* |Menší než nebo rovna verze 2.3 |20. ledna 2017 |
| 5.3.* | 5.1.158.* |Menší než nebo rovna verze 2.3 |24. února 2017 |
| 5.4.* | 5.1.158.* |Menší než nebo rovna verze 2.4 |Může 10,2017       |
| 5.5.* | 5.4.164.* |Menší než nebo rovna verze 2.5 |Srpen 10,2017    |
| 5.6.* | 5.4.164.* |Menší než nebo rovna verzi 2.6 |Říjen 13,2017   |
| 5.7.* | 5.4.164.* |Menší než nebo rovna verze 2.7 |Prosinec 15,2017  |
| 6.0.* | 5.6.205.* |Menší než nebo rovna verze 2.8 |30,2018 dne.     | 
| 6.1.* | 5.7.221.* |Menší než nebo rovna verze 3.0 |Července 15,2018      |
| 6.2. * | 6.0.232.* |Menší než nebo rovna verze 3.1 |Aktuální verze a proto žádné datum ukončení |

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Verze Preview prostředků infrastruktury služby - nepodporovaný pro použití v provozním prostředí.
Čas od času jsme vydání verzí, které mají významných funkcí, které chceme zpětné vazby, které vydávají jako verze Preview. Tato verze preview lze používat pouze pro účely testování. Provozní cluster měli vždycky používat verzi podporován, stabilní, Service Fabric. Ve verzi preview vždycky začíná číslem hlavní verze a podverze 255. Například pokud se zobrazí Service Fabric verze 255.255.5703.949, tuto verzi je jenom pro použití v testovacích clusterů a je ve verzi preview. Tato verze preview také odesílány zpět [blog týmu Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric) a bude mít podrobné informace o funkcích, které jsou.

Neexistuje žádná možnost placené podporu pro tyto verze preview. Použijte jednu z možností uvedených v části [sestavy Azure Service Fabric problémy](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) k nim máte nějaké otázky nebo poskytnout zpětnou vazbu.

## <a name="next-steps"></a>Další postup

- [Verze fabric upgradu služby v Azure clusteru ](service-fabric-cluster-upgrade.md)
- [Upgrade verze Service Fabric na samostatný server clusteru systému windows ](service-fabric-cluster-upgrade-windows-server.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: http://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: http://aka.ms/servicefabricdocs
[sample-repos]: http://aka.ms/servicefabricsamples
