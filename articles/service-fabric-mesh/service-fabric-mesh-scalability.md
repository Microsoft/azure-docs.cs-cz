---
title: Škálovatelnost aplikací Service Fabric sítě Azure
description: Jednou z výhod nasazení aplikací do Service Fabric sítě je schopnost snadno škálovat vaše služby, a to buď ručně, nebo pomocí zásad automatického škálování.
author: georgewallace
ms.author: gwallace
ms.date: 10/26/2018
ms.topic: conceptual
ms.openlocfilehash: 67b4b27a5d1faab3e6c156704d67a30b220ac54c
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91840179"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Škálování aplikací Service Fabric sítě

Jednou z hlavních výhod nasazení aplikací do Service Fabric sítě je možnost snadného škálování služeb v systému nebo. Tato služba by se měla používat ke zpracování proměnlivých objemů zatížení vašich služeb nebo ke zlepšení dostupnosti. Můžete ručně škálovat služby nebo nastavení zásad automatického škálování nebo nastavení.

## <a name="manual-scaling-instances"></a>Instance ručního škálování

V šabloně nasazení pro prostředek aplikace má každá služba vlastnost *replicaCount*, kterou můžete použít k nastavení toho, kolikrát chcete tuto službu nasadit. Aplikace se může skládat z několika služeb, z nichž každá má jedinečný počet *replicaCount* a které jsou společně nasazovány a spravovány. Pokud chcete škálovat počet replik služby, upravte hodnotu *replicaCount* pro každou službu, kterou chcete škálovat, v šabloně nasazení nebo souboru parametrů. Potom aplikaci upgradujte.

Příklady instancí služby, které můžete ručně škálovat, najdete [v tématu Ruční škálování služeb v systému nebo](service-fabric-mesh-tutorial-template-scale-services.md).

## <a name="autoscaling-service-instances"></a>Automatické škálování instancí služby
Automatické škálování je další možností Service Fabric dynamicky škálovat počet instancí služby (horizontální škálování). Automatické škálování dává velkou pružnost a umožňuje zřizování nebo odebírání instancí služby na základě využití procesoru nebo paměti.  Automatické škálování umožňuje spustit správný počet instancí služby pro vaše zatížení a optimalizovat pro náklady.

Zásada automatického škálování je definovaná na službu v souboru prostředků služby. Každá zásada škálování se skládá ze dvou částí:

- Aktivační událost škálování, která popisuje, kdy bude provedeno škálování služby. Existují tři faktory, které určují, kdy bude služba škálovat. *Nižší prahová hodnota zatížení* je hodnota, která určuje, kdy se má služba škálovat. Pokud je průměrné zatížení všech instancí oddílů nižší než tato hodnota, služba se škáluje. *Horní prahová hodnota zatížení* je hodnota, která určuje, kdy se má služba škálovat. Pokud je průměrné zatížení všech instancí oddílu vyšší než tato hodnota, služba se škáluje. *Interval škálování* určuje, jak často (v sekundách) se má zkontrolovat aktivační událost. V případě, že je aktivační událost zaškrtnuta, bude použit mechanismus škálování. Pokud se škálování nepotřebuje, neprovede se žádná akce. V obou případech se Trigger nevrátí znovu ještě před vypršením platnosti intervalu škálování.

- Mechanismus škálování, který popisuje, jak bude při aktivaci provedeno škálování. *Přírůstek stupnice* určuje, kolik instancí bude přidáno nebo odebráno při aktivaci mechanismu. Hodnota *maximální počet instancí* definuje horní limit pro škálování. Pokud počet instancí dosáhne tohoto limitu, služba nebude bez ohledu na zatížení škálovaná. *Minimální počet instancí* definuje dolní limit pro škálování. Pokud počet instancí oddílu dosáhne tohoto limitu, služba nebude škálovat bez ohledu na zatížení.

Pokud se chcete dozvědět, jak nastavit zásady automatického škálování pro vaši službu, přečtěte si téma [služby automatického škálování](service-fabric-mesh-howto-auto-scale-services.md).

## <a name="next-steps"></a>Další kroky

Informace o aplikačním modelu najdete v tématu [Service Fabric prostředky](service-fabric-mesh-service-fabric-resources.md) .
