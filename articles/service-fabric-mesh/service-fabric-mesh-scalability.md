---
title: Aplikace sítě škálovatelnost Azure Service Fabric | Dokumentace Microsoftu
description: Další informace o škálování služby Azure Service Fabric mřížky.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 10/26/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: c58961011231f74b09abdf313b8624ddc8ad52c1
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2018
ms.locfileid: "52892182"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Škálování aplikací Service Fabric mřížky

Jednou z hlavních výhod nasazení aplikací do služby Service Fabric Mesh je možnost snadného horizontálního snížení nebo navýšení kapacity služeb. Tuto možnost byste měli používat ke zvládnutí různých objemů zatížení vašich služeb nebo ke zlepšení dostupnosti. Můžete ručně škálovat služby snížení nebo navýšení kapacity nebo zásady nastavení automatického škálování.

## <a name="manual-scaling-instances"></a>Ruční škálování instance

V šabloně nasazení pro prostředek aplikace má každá služba vlastnost *replicaCount*, kterou můžete použít k nastavení toho, kolikrát chcete tuto službu nasadit. Aplikace se může skládat z několika služeb, z nichž každá má jedinečný počet *replicaCount* a které jsou společně nasazovány a spravovány. Pokud chcete škálovat počet replik služby, upravte hodnotu *replicaCount* pro každou službu, kterou chcete škálovat, v šabloně nasazení nebo souboru parametrů. Potom aplikaci upgradujte.

Příklady ruční škálování instance služby, najdete v článku [ručně škálovat služby snížení nebo navýšení kapacity](service-fabric-mesh-tutorial-template-scale-services.md).

## <a name="autoscaling-service-instances"></a>Automatické škálování instance služby
Automatické škálování je další schopností Service Fabric dynamicky škálovat počet instancí služby (horizontální škálování). Automatické škálování poskytuje skvělé pružnost a umožňuje zřizování nebo odebrání instancí služby na základě využití procesoru nebo paměti.  Automatické škálování umožňuje spustit správný počet instancí služby pro vaše úlohy a optimalizovat náklady.

Automatické škálování zásad je definován na službu v souboru prostředků služby. Každé zásady škálování se skládá ze dvou částí:

- Škálování aktivační událost, která popisuje při škálování služby se provede. Existují tři faktory, které určují, kdy se bude škálovat službu. *Nižší prahová hodnota načtení* je hodnota, která určuje, kdy služba bude možné horizontálně snížit. Pokud je průměrné zatížení všech instancí oddílů je nižší než tato hodnota, bude v škálovat službu. *Horní prahová hodnota zatížení* je hodnota, která určuje, kdy se škálovat službu. Pokud je průměrné zatížení všech instancí oddílu je vyšší než tato hodnota, bude služba horizontálním navýšení kapacity. *Interval škálování* Určuje, jak často (v sekundách) bude trigger kontrolovat. Po zaškrtnutí aktivační událost, pokud škálování je potřeba mechanismu, který se použije. Podle potřeby škálování není, bude provedena žádná akce. V obou případech se aktivační událost se znovu nekontroluje před vypršením platnosti interval škálování.

- Škálování mechanismus, který popisuje, jak škálování proběhne při aktivaci. *Zvýšení škálování* Určuje, kolik instancí budou přidány nebo odebrány při aktivaci mechanismus. *Maximální počet instancí* definuje horní mez pro škálování. Pokud je počet instancí dosáhne tohoto limitu, pak služba nebude škálovat bez ohledu na to, zatížení. *Minimální počet instancí* definuje dolní mez pro škálování. Pokud počet instancí oddílu dosáhne tohoto limitu, nebude možné službu škálovat ve bez ohledu na to, zatížení.

Zjistěte, jak nastavit zásadu automatického škálování pro vaši službu, přečtěte si téma [automatického škálování služby](service-fabric-mesh-howto-auto-scale-services.md).

## <a name="next-steps"></a>Další postup

Informace o modelu aplikací najdete v tématu [prostředky Service Fabric](service-fabric-mesh-service-fabric-resources.md)
