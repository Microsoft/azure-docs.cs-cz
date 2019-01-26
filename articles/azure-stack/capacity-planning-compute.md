---
title: Výpočetní plánování kapacity pro Azure Stack | Dokumentace Microsoftu
description: Další informace o plánování nasazení Azure Stack výpočetní kapacitu.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: jeffgilb
ms.reviewer: prchint
ms.custom: mvc
ms.openlocfilehash: 314d40ba365f6dc9a279744ac3af874057fd2321
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55076787"
---
# <a name="azure-stack-compute-capacity-planning"></a>Plánování kapacity výpočetních s Azure Stack
[Velikosti virtuálních počítačů, které jsou podporované ve službě Azure Stack](./user/azure-stack-vm-sizes.md) jsou podmnožinou, které jsou podporovány v Azure. Azure má omezení prostředků podél mnoho vektorů, aby overconsumption prostředků (server místní a úrovni služeb). Bez uložení některá omezení na prostředky spotřebované klienty, sníží tenanta prostředí při jiných tenantů overconsume prostředky. Pro sítě odchozího přenosu dat z virtuálního počítače existují omezení šířky pásma na místě ve službě Azure Stack, odpovídající omezení Azure. Pro prostředky úložiště limity vstupně-výstupních operací úložiště je implementovaná v Azure stacku, aby se zabránilo základní overconsumption prostředků tenantů pro přístup k úložišti.  

## <a name="vm-placement-and-virtual-to-physical-core-overprovisioning"></a>Umístění virtuálního počítače a předimenzování virtuálních a fyzických jader
Ve službě Azure Stack se nedají použít pro umístění virtuálního počítače pro klienta zadat konkrétní server. Jediný faktor při umísťování virtuálních počítačů je, zda je na hostiteli pro tento typ virtuálního počítače dostatek paměti. Azure Stack není přetížit paměti. overcommit počet jader je však povoleno. Protože algoritmy umístění se na pohled na stávajících virtuálních a fyzických jader předimenzování poměr jako faktor, každý hostitel může mít různý poměr. 

V Azure abyste dosáhli vysoké dostupnosti produkční systém více virtuálních počítačů, virtuální počítače jsou umístěné ve skupině dostupnosti bývalo rozprostřené mezi více domén selhání. Ve službě Azure Stack doménu selhání ve skupině dostupnosti je definován jako jeden uzel v jednotce škálování.

Infrastruktury služby Azure Stack je odolné vůči selhání, základní technologie (clustering převzetí služeb při selhání) stále s sebou nese náklady výpadkům pro virtuální počítače na ovlivněné fyzickém serveru v případě selhání hardwaru. V současné době podporuje Azure Stack s dostupnosti s délkou maximálně tři domény selhání pro zajištění konzistence s Azure. Virtuální počítače umístěné ve skupině dostupnosti budou fyzicky izolované od sebe navzájem tím, že rozprostírá co nejrovnoměrněji rozložené přes víc domén selhání (uzly Azure Stack). Pokud dojde k selhání hardwaru, virtuálních počítačů z neúspěšných doména bude být restartování v jiných uzlech, ale pokud je to možné udržovat v samostatných doménách selhání z jiných virtuálních počítačů ve stejné sadě dostupnosti. Když hardware vrátí do režimu online, virtuálních počítačů bude možné znovu vyrovnána udržet vysokou dostupnost.

Jiné pojem, který používá Azure k zajištění vysoké dostupnosti je ve formě aktualizačních domén ve skupinách dostupnosti. Aktualizační doména je logická skupina hardwarových komponent, u kterých je možné provést údržbu nebo restart současně. Ve službě Azure Stack jsou virtuální počítače za provozu migrovat na jiné online hostitelích v clusteru předtím, než se aktualizuje jejich základního hostitele. Protože neexistuje žádný výpadek tenanta při aktualizaci hostitele, funkci aktualizace domény ve službě Azure Stack existuje pouze pro šablony kompatibilitu s Azure.

## <a name="azure-stack-resiliency-resources"></a>Odolnost proti chybám prostředky Azure Stack
Umožňuje opravy a aktualizace služby Azure Stack integrovaný systém a pokud chcete být odolní vůči selhání fyzického hardwaru, část paměti úplné havárii serveru je rezervována a není k dispozici pro umístění virtuálních počítačů (VM) tenanta.

Pokud dojde k selhání serveru, restartuje virtuální počítače hostované na serveru, který selhal, serverech zbývající, která je dostupná pro dostupnost virtuálních počítačů. Podobně během procesu opravy a aktualizace všechny virtuální počítače spuštěné na serveru se pracovat migrované na jiný server k dispozici. Tato Správa virtuálních počítačů nebo přesun jde dosáhnout jenom Pokud je záložní kapacitu, aby bylo restartování nebo migraci dojde k.

Následující výpočet výsledkem celkovou a dostupnou paměť, která lze použít pro umístění virtuálního počítače tenanta. Tato kapacita paměti je podkladové jednotky škálování Azure Stack.

  Paměť k dispozici pro umístění virtuálního počítače = režijní náklady na celkové paměti serveru – odolnost proti chybám rezervy – Azure Stack infrastruktury <sup>1</sup>

  Odolnost proti chybám rezervy = H + R * (N-1) + V * (N-2)

> Kde:
> - H = velikost paměti na jeden server
> - N = velikost z jednotky škálování (počet serverů)
> - R = rezerva operačního systému pro režijní náklady na operační systém<sup>2</sup>
> - V = největší virtuální počítač v jednotce škálování

  <sup>1</sup> infrastruktura azure stacku režie = 208 GB

  <sup>2</sup> rezerva operačního systému pro režii = 15 % paměti uzlu. Hodnotu rezervy operačního systému je odhad a budou lišit v závislosti na kapacita fyzické paměti serveru a režijní náklady na obecné operačního systému.

Hodnota V největší virtuální počítač v jednotce škálování dynamicky podle největší tenanta velikost paměti virtuálního počítače. Největší hodnota virtuálního počítače může být například, 7 GB nebo 112 GB nebo jakékoli jiné podporovanou velikost virtuálního počítače paměti v řešení služby Azure Stack.

Výše uvedené kalkulace je odhad a mohou se změnit na základě aktuální verze služby Azure Stack. Schopnost nasadit virtuální počítače klientů a služeb je založená na konkrétních podrobnostech nasazeného řešení. Tento příklad výpočtu je právě tento průvodce a není absolutní odpověď schopnost nasadit virtuální počítače.



## <a name="next-steps"></a>Další postup
[Plánování kapacity úložiště](capacity-planning-storage.md)
