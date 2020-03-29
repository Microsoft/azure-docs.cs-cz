---
title: Kontinuita provozu virtuální sítě | Dokumenty společnosti Microsoft
description: Přečtěte si, co dělat v případě přerušení služby Azure, které má dopad na virtuální sítě Azure.
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
ms.author: narayan
ms.reviewer: aglick
ms.openlocfilehash: 3f91d24bff0bec540ff0e7964f21c2f47c03638c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67876172"
---
# <a name="virtual-network--business-continuity"></a>Virtuální síť – kontinuita provozu

## <a name="overview"></a>Přehled
Virtuální síť (VNet) je logická reprezentace vaší sítě v cloudu. Umožňuje definovat vlastní privátní adresní prostor IP a segmentovat síť do podsítí. Virtuální sítě slouží jako hranice důvěryhodnosti pro hostování výpočetních prostředků, jako jsou virtuální počítače Azure a cloudové služby (role web/pracovní proces). Virtuální síť umožňuje přímou privátní IP komunikaci mezi prostředky hostované v něm. Virtuální síť můžete propojit s místní sítí prostřednictvím brány VPN nebo ExpressRoute.

Virtuální síť se vytvoří v rámci oblasti. Virtuální sítě se stejným adresním prostorem můžete *vytvořit* ve dvou různých oblastech (například US East a US West), ale protože mají stejný adresní prostor, nemůžete je propojit. 

## <a name="business-continuity"></a>Kontinuita podnikových procesů

Může existovat několik různých způsobů, které by mohly být narušeny. Oblast by mohla být zcela odříznuta v důsledku přírodní katastrofy nebo částečné katastrofy v důsledku selhání více zařízení nebo služeb. Dopad na službu virtuální sítě se liší v každé z těchto situací.

**Otázka: Pokud dojde k výpadku pro celou oblast, co mám dělat? Například, pokud je region zcela odříznut kvůli přírodní katastrofě? Co se stane s virtuálními sítěmi hostovanými v oblasti?**

A: Virtuální síť a prostředky v postižené oblasti zůstane nepřístupné v době přerušení služby.

![Jednoduchý diagram virtuální sítě](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**Otázka: Co mám udělat znovu vytvořit stejnou virtuální síť v jiné oblasti?**

A: Virtuální sítě jsou poměrně lehké prostředky. Můžete vyvolat Azure API k vytvoření virtuální sítě se stejným adresním prostorem v jiné oblasti. Chcete-li znovu vytvořit stejné prostředí, které bylo k dispozici v ohrožené oblasti, provedete volání rozhraní API k opětovnému nasazení webových a pracovních rolí cloudových služeb a virtuálních počítačů, které jste měli. Pokud máte místní připojení, například v hybridním nasazení, musíte nasadit novou bránu VPN a připojit se k místní síti.

Pokud chcete vytvořit virtuální síť, [přečtěte si tématu Vytvoření virtuální sítě](manage-virtual-network.md#create-a-virtual-network).

**Otázka: Může být replika virtuální sítě v dané oblasti znovu vytvořena v jiné oblasti předem?**

A: Ano, můžete vytvořit dvě virtuální sítě pomocí stejného privátního adresního prostoru IP a prostředky ve dvou různých oblastech předem. Pokud hostujete internetové služby ve virtuální síti, můžete nastavit Traffic Manager pro geografickou trasu provozu do oblasti, která je aktivní. Však nelze připojit dvě virtuální sítě se stejným adresním prostorem k místní síti, protože by to způsobit problémy se směrováním. V době havárie a ztráty virtuální sítě v jedné oblasti můžete připojit druhou virtuální síť v dostupné oblasti s odpovídajícím adresním prostorem k místní síti.

Pokud chcete vytvořit virtuální síť, [přečtěte si tématu Vytvoření virtuální sítě](manage-virtual-network.md#create-a-virtual-network).

