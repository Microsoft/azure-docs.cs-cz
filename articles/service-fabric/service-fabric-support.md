---
title: Další informace o možnostech podpory Azure Service Fabric | Dokumentace Microsoftu
description: Podporované verze clusteru Service Fabric a odkazy na soubor Azure lístky podpory
services: service-fabric
documentationcenter: .net
author: pkcsf
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: e22a2431ecae68278276b069bc199cfea60cd413
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44347354"
---
# <a name="azure-service-fabric-support-options"></a>Možnosti podpory Azure Service Fabric

K zajištění příslušné podporu clusterů Service Fabric, na kterých běží vaše pracovní zatížení aplikací na jsme nastavili různé možnosti pro vás. V závislosti na úrovni podporu potřebné a závažnost problému, získáte k výběru správné možnosti. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Oznámit problémy v produkčním prostředí nebo požádat o placené podpory pro Azure

Hlášení problémů s ve vašem clusteru Service Fabric, nasadit do Azure, otevřete lístek podpory [na portálu Azure portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) nebo [Microsoft support portal](http://support.microsoft.com/oas/default.aspx?prid=16146).

Další informace:
 
- [Podporu od společnosti Microsoft pro Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Podpora programu Microsoft premier](https://support.microsoft.com/en-us/premier).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Oznámit problémy v produkčním prostředí nebo požádat o placené podpory pro samostatnou službu, kterou clusterů Service Fabric

Pro vytváření sestav místně nasadili problémy ve vašem clusteru Service Fabric nebo v jiných cloudech, otevřete lístek pro profesionální podporu na [Microsoft support portal](http://support.microsoft.com/oas/default.aspx?prid=16146).

Další informace:

- [Profesionální podporu od společnosti Microsoft pro místní](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Podpora programu Microsoft premier](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Problémy s sestavy Azure Service Fabric

Nastavíme úložiště GitHub pro hlášení problémů s Service Fabric.  Můžeme také aktivně monitorovaných následujících fór.

### <a name="github-repo"></a>Úložiště GitHub 

Nahlásit problémy s Azure Service Fabric na [úložiště git Service-Fabric problémy](https://github.com/Azure/service-fabric-issues). Toto úložiště je určený pro vytváření sestav a sledování problémů s Azure Service Fabric a k provádění žádostí o malé funkce. **Nepoužívejte toto umožňuje hlásit problémy živého webu**.

### <a name="stackoverflow-and-msdn-forums"></a>Fórech StackOverflow a MSDN

[Značky Service Fabric na StackOverflow] [ stackoverflow] a [Service Fabric fórum na webu MSDN] [ msdn-forum] jsou nejvhodnější pro kladení otázek o tom, jak použít Platforma funguje a jak může provádět určité úlohy s ní.

### <a name="azure-feedback-forum"></a>Fórum Azure pro názory

[Fóru pro zpětnou vazbu Azure Service Fabric] [ uservoice-forum] je nejlepším místem pro odeslání nápady týkající se funkcí velký máte pro produkt, jako je kontrola nejoblíbenější požadavky jsou součástí naší střední až dlouhodobé plánování. Doporučujeme vám technologie rally podporu pro vaše návrhy v rámci komunity.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Service Fabric ve verzi Preview verze - nepodporovaný pro použití v produkčním prostředí

Čas od času vydaných verzí, které mají významných funkcí, které chceme zpětné vazby, které se vydávají jako verze Preview. Tato verze preview by měla sloužit pouze pro účely testování. Produkční cluster by měl vždycky spouštět podporované a stabilní verze Service Fabric. Verze preview se vždy začíná službami číslo hlavní verze a podverze 255. Například pokud se Service Fabric verze 255.255.5703.949, tuto verzi je pouze pro použití v testovacích clusterů a je ve verzi preview. Tato verze preview také budou oznámeny na [blog týmu Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric) a bude obsahovat podrobnosti o funkcích zahrnutých.
Neexistuje žádná možnost placená odborná pomoc pro tyto verze preview. Použijte jednu z možností uvedených v části [vydá zprávu Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) klást otázky nebo poskytnout zpětnou vazbu.

## <a name="next-steps"></a>Další postup

[Podporované verze Service Fabric](service-fabric-versions.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: http://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: http://aka.ms/servicefabricdocs
[sample-repos]: http://aka.ms/servicefabricsamples