---
title: Škálovatelnost aplikací Azure Service Fabric Mesh
description: Jednou z výhod nasazení aplikací do sítě Service Fabric je možnost snadno škálovat vaše služby, a to buď ručně, nebo pomocí zásad automatického škálování.
author: dkkapur
ms.author: dekapur
ms.date: 10/26/2018
ms.topic: conceptual
ms.openlocfilehash: 474eda904df653d514fd2ee59fa046f1f87a66aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259172"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Škálování service fabric mesh aplikace

Jednou z hlavních výhod nasazení aplikací do sítě Service Fabric mesh je možnost snadnéškálování služeb v aplikaci nebo venku. To by mělo být použito pro zpracování různé množství zatížení vašich služeb nebo zlepšení dostupnosti. Můžete ručně škálovat služby v nebo mimo nebo nastavení zásad automatického škálování.

## <a name="manual-scaling-instances"></a>Ruční škálování instancí

V šabloně nasazení pro prostředek aplikace má každá služba vlastnost *replicaCount*, kterou můžete použít k nastavení toho, kolikrát chcete tuto službu nasadit. Aplikace se může skládat z několika služeb, z nichž každá má jedinečný počet *replicaCount* a které jsou společně nasazovány a spravovány. Pokud chcete škálovat počet replik služby, upravte hodnotu *replicaCount* pro každou službu, kterou chcete škálovat, v šabloně nasazení nebo souboru parametrů. Potom aplikaci upgradujte.

Příklady ručního škálování instancí služeb naleznete v [tématu Ruční škálování služeb v aplikaci nebo vně](service-fabric-mesh-tutorial-template-scale-services.md).

## <a name="autoscaling-service-instances"></a>Automatické škálování instancí služby
Automatické škálování je další možnost Service Fabric dynamicky škálovat počet instancí služby (horizontální škálování). Automatické škálování poskytuje velkou pružnost a umožňuje zřizování nebo odebrání instancí služby na základě využití procesoru nebo paměti.  Automatické škálování umožňuje spustit správný počet instancí služby pro vaše úlohy a optimalizovat náklady.

Zásady automatického škálování jsou definovány pro službu v souboru prostředků služby. Každá zásada škálování se skládá ze dvou částí:

- Aktivační událost škálování, která popisuje, kdy bude provedeno škálování služby. Existují tři faktory, které určují, kdy bude služba škálovat. *Nižší prahová hodnota zatížení* je hodnota, která určuje, kdy bude služba škálovat. Pokud je průměrné zatížení všech instancí oddílů nižší než tato hodnota, bude služba škálována. *Horní prahová hodnota zatížení* je hodnota, která určuje, kdy bude služba škálovat. Pokud je průměrné zatížení všech instancí oddílu vyšší než tato hodnota, bude služba škálována. *Interval změny velikosti* určuje, jak často (v sekundách) bude aktivační událost kontrolována. Jakmile je zaškrtnuto aktivační události, pokud je potřeba škálování mechanismus bude použit. Pokud není potřeba škálování, nebude provedena žádná akce. V obou případech aktivační událost nebude znovu zkontrolována před vypršením intervalu škálování.

- Mechanismus škálování, který popisuje, jak se bude provádět škálování při jeho aktivaci. *Zvýšení měřítka určuje,* kolik instancí bude přidáno nebo odebráno při aktivaci mechanismu. *Maximální počet instancí* definuje horní limit pro změnu měřítka. Pokud počet instancí dosáhne tohoto limitu, nebude služba škálovat bez ohledu na zatížení. *Minimální počet instancí* definuje dolní limit pro škálování. Pokud počet instancí oddílu dosáhne tohoto limitu, pak služba nebude škálovat bez ohledu na zatížení.

Chcete-li se dozvědět, jak nastavit zásady automatického škálování pro vaši službu, přečtěte si [služby automatického škálování](service-fabric-mesh-howto-auto-scale-services.md).

## <a name="next-steps"></a>Další kroky

Informace o modelu aplikace naleznete v tématu [Service Fabric prostředky](service-fabric-mesh-service-fabric-resources.md)
