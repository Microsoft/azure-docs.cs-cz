---
title: "Kontinuita podnikových procesů virtuální sítě | Microsoft Docs"
description: "Zjistěte, co dělat v případě přerušení služby Azure služby virtuálních sítí Azure, které mají vliv."
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan;aglick
ms.openlocfilehash: d993144006d1fb17d79ffee4f2da538264a309a4
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="virtual-network--business-continuity"></a>Virtuální síť – kontinuita podnikových procesů

## <a name="overview"></a>Přehled
Virtuální síť (VNet) je logická reprezentace sítě v cloudu. Umožňuje definovat vlastní prostor privátní IP adresy a segmentovat do podsítí v síti. Virtuální sítě slouží jako hranice vztahu důvěryhodnosti pro hostování svoje výpočetní prostředky, jako jsou virtuální počítače Azure a cloudových služeb (webové/role pracovního procesu). Virtuální síť, která umožňuje přímé privátní IP komunikaci mezi prostředky v ní umístěné. Můžete propojit virtuální sítě k místní síti prostřednictvím brány sítě VPN nebo ExpressRoute.

Virtuální síť, která je vytvořena v rámci oboru oblasti. Můžete vytvořit virtuální sítě pomocí stejné adresní prostor ve dvou různých oblastech (například USA – východ a oblasti USA – západ), ale nemůže připojit je společně. 

## <a name="business-continuity"></a>Kontinuita podnikových procesů

Může dojít k několika různými způsoby, že vaše aplikace může být přerušeny. Oblast může být zcela zkrácené z důvodu přírodní katastrofě nebo částečné po havárii, z důvodu selhání více zařízení nebo služby. Dopad na službu virtuální sítě se liší v každé z těchto situacích.

**Otázka: Pokud dojde k výpadku pro celou oblast, co mám udělat? Například pokud oblast úplně zkrácena z důvodu přírodní katastrofě? Co se stane k virtuálním sítím, které jsou hostované v oblasti?**

Odpověď: virtuální sítě a prostředky v oblasti ovlivněných nepřístupná během doby přerušení služby.

![Diagram jednoduché virtuální sítě](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**Otázka: co se dá znovu vytvořit v jiné oblasti stejnou virtuální síť?**

Odpověď: virtuální sítě jsou poměrně jednoduché prostředky. Můžete volat rozhraní API služby Azure k vytvoření virtuální sítě s stejné adresní prostor v jiné oblasti. K opětovnému vytvoření stejné prostředí, ve kterém se nachází v oblasti ovlivněných, provedete volání rozhraní API k opětovnému nasazení webu cloudové služby a rolí pracovního procesu a virtuální počítače, které jste měli. Pokud máte místní připojení, například v hybridním nasazení, budete muset nasadit novou bránu VPN a připojení k síti na pracovišti.

Chcete-li vytvořit virtuální síť, přečtěte si téma [vytvořit virtuální síť](manage-virtual-network.md#create-a-virtual-network).

**Otázka: je možné repliku virtuální sítě v dané oblasti se znovu vytvořit v jiné oblasti předem?**

Odpověď: Ano, můžete vytvořit dvě virtuální sítě pomocí stejné prostor privátní IP adresy a prostředky ve dvou různých oblastech předem. Pokud hostujete internetových služeb ve virtuální síti, můžete mít nastavit až Traffic Manageru můžete provoz směrovat geografické oblasti, která je aktivní. Ale nemůžete se připojit dvě virtuální sítě se stejnou adresní prostor k místní síti, jako by to způsobilo směrování problémy. V době havárie a ztrátě virtuální sítě v jedné oblasti se můžete připojit další virtuální síť v oblasti k dispozici s odpovídající adresní prostor k místní síti.

Chcete-li vytvořit virtuální síť, přečtěte si téma [vytvořit virtuální síť](manage-virtual-network.md#create-a-virtual-network).

