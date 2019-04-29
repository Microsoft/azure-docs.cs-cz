---
title: Kontinuita podnikových procesů virtuální sítě | Dokumentace Microsoftu
description: Zjistěte, co můžete dělat v případě výpadku služby Azure, vliv na Azure Virtual Network.
services: virtual-network
documentationcenter: ''
author: NarayanAnnamalai
manager: jefco
editor: ''
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan;aglick
ms.openlocfilehash: f8068524a33c6d29056af456a26b8bd87fc3e3d6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60742698"
---
# <a name="virtual-network--business-continuity"></a>Virtuální sítě – kontinuita podnikových procesů

## <a name="overview"></a>Přehled
Virtuální síť (VNet) je logické znázornění vaší vlastní sítě v cloudu. Umožňuje definovat vlastní privátní adresní prostor IP a sítě segmentovat do podsítí. Virtuální sítě slouží jako hranice vztahů důvěryhodnosti pro hostování vašeho výpočetní prostředky, jako jsou Azure Virtual Machines nebo Cloud Services (webové nebo pracovní role). Virtuální síť umožňuje přímou komunikaci privátních IP mezi prostředky v ní. Propojení virtuální sítě k místní síti prostřednictvím brány sítě VPN nebo ExpressRoute.

Virtuální sítě se vytvoří v rámci oboru oblast. Můžete vytvořit virtuální sítě pomocí stejného adresního prostoru ve dvou různých oblastech (například USA – východ a USA – západ), ale nelze je vzájemně propojit. 

## <a name="business-continuity"></a>Kontinuita podnikových procesů

Může dojít k několika různými způsoby, že vaše aplikace může přerušit. Oblast může zcela oříznou kvůli přírodní katastrofa nebo částečné po havárii, protože došlo k chybě více zařízení a služeb. Dopad na virtuální síť služby se liší ve všech těchto situacích.

**Otázka: Pokud dojde k výpadku celé oblasti, co mám udělat? Například pokud oblast úplně zkrácena kvůli přírodní katastrofě? Co se stane s virtuálními sítěmi, které jsou hostované v oblasti?**

Odpověď: Virtuální síť a prostředky v oblasti ovlivněné nepřístupná během doby přerušení služeb.

![Diagram jednoduchého virtuální sítě](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**Otázka: Co se dá znovu vytvořit v jiné oblasti stejné virtuální síti?**

Odpověď: Virtuální sítě jsou poměrně jednoduché prostředky. Můžete vyvolat rozhraní API Azure k vytvoření virtuální sítě pomocí stejného adresního prostoru v jiné oblasti. Pro opětovné vytvoření stejné prostředí, která byla k dispozici v ovlivněné oblasti, můžete volat rozhraní API k opětovnému nasazení webové služby Cloud Services a role pracovních procesů a virtuálních počítačů, které jste měli. Pokud máte místní připojení, například v hybridním nasazení, budete muset nasadit novou bránu VPN a připojení k místní síti.

Vytvoření virtuální sítě najdete v tématu [vytvoření virtuální sítě](manage-virtual-network.md#create-a-virtual-network).

**Otázka: Replika virtuálních sítí v dané oblasti lze znovu vytvořit v jiné oblasti předem?**

Odpověď: Ano, můžete vytvořit dvě virtuální sítě s použitím stejné privátní adresní prostor IP adres a prostředky ve dvou různých oblastech předem. Pokud hostujete internetové služby ve virtuální síti, může mít nastavíte si Traffic Manager pro geografické směrování provozu do oblasti, která je aktivní. Můžete ji ale nelze propojit dvě virtuální sítě pomocí stejného adresního prostoru k místní síti, protože by to způsobilo problémů se směrováním. V době selhání a ztráta virtuální sítě v jedné oblasti připojíte k jiné virtuální sítě v oblasti k dispozici s odpovídající adresního prostoru přesměrují do místní sítě.

Vytvoření virtuální sítě najdete v tématu [vytvoření virtuální sítě](manage-virtual-network.md#create-a-virtual-network).

