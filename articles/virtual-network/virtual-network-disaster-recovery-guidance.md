---
title: Provozní kontinuita virtuální sítě | Microsoft Docs
description: Přečtěte si, co dělat v případě výpadku služby Azure s dopadem na virtuální sítě Azure.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67876172"
---
# <a name="virtual-network--business-continuity"></a>Virtual Network – provozní kontinuita

## <a name="overview"></a>Přehled
Virtual Network (VNet) je logická reprezentace vaší sítě v cloudu. Umožňuje vám definovat vlastní privátní adresní prostor IP adres a segmentovat síť do podsítí. Virtuální sítě slouží jako hranice důvěry pro hostování výpočetních prostředků, jako je Azure Virtual Machines a Cloud Services (Web/role pracovního procesu). Virtuální síť umožňuje přímou komunikaci privátních IP adres mezi prostředky, které jsou v ní hostované. Virtuální síť můžete propojit s místní sítí pomocí VPN Gateway nebo ExpressRoute.

Virtuální síť se vytvoří v rámci rozsahu oblasti. Můžete *vytvořit* virtuální sítě se stejným adresním prostorem ve dvou různých oblastech (například USA – východ a USA – západ), protože mají stejný adresní prostor, nemůžete je propojit dohromady. 

## <a name="business-continuity"></a>Kontinuita podnikových procesů

Může dojít k několika různým způsobům, které by mohlo dojít k přerušení vaší aplikace. Oblast může být zcela vyjmuta z důvodu přirozené havárie nebo částečné havárie z důvodu selhání více zařízení nebo služeb. Dopad na službu VNet se v každé z těchto situací liší.

**Otázka: Pokud dojde k výpadku pro celou oblast, co mám dělat? Například pokud je oblast zcela vyjmuta z důvodu přirozené havárie? Co se stane s virtuálními sítěmi hostovanými v dané oblasti?**

Odpověď: virtuální síť a prostředky v ovlivněné oblasti zůstávají v době přerušení služby nedostupné.

![Diagram jednoduchého Virtual Network](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**Otázka: co mohu znovu vytvořit stejnou virtuální síť v jiné oblasti?**

Odpověď: virtuální sítě jsou poměrně odlehčené prostředky. Můžete vyvolat rozhraní API Azure a vytvořit virtuální síť se stejným adresním prostorem v jiné oblasti. Pokud chcete znovu vytvořit stejné prostředí, které se nacházelo v zasažené oblasti, můžete volat rozhraní API k opětovnému nasazení Cloud Services webových rolí a rolí pracovních procesů a virtuálních počítačů, které jste měli. Pokud máte místní připojení, například při hybridním nasazení, musíte nasadit novou VPN Gateway a připojit se k místní síti.

Informace o vytvoření virtuální sítě najdete v tématu [vytvoření virtuální sítě](manage-virtual-network.md#create-a-virtual-network).

**Otázka: může být replika virtuální sítě v dané oblasti znovu vytvořena v jiné oblasti před časem?**

Odpověď: Ano, můžete vytvořit dvě virtuální sítě pomocí stejného privátního adresního prostoru IP adres a prostředků ve dvou různých oblastech předem. Pokud ve virtuální síti používáte internetové služby, můžete nastavit Traffic Manager provoz geografického směrování na oblast, která je aktivní. Nemůžete ale propojit dvě virtuální sítě se stejným adresním prostorem do místní sítě, protože by to mohlo způsobovat problémy s směrováním. V době havárie a ztráty virtuální sítě v jedné oblasti můžete připojit druhou virtuální síť v oblasti k dispozici s adresním prostorem pro místní síť.

Informace o vytvoření virtuální sítě najdete v tématu [vytvoření virtuální sítě](manage-virtual-network.md#create-a-virtual-network).

