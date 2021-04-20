---
title: Azure DDoS Protection standardní provozní kontinuitu | Microsoft Docs
description: Přečtěte si, co dělat v případě výpadku služby Azure, který ovlivňuje Azure DDoS Protection Standard.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2021
ms.author: yitoh
ms.topic: article
ms.openlocfilehash: 5085f1584a737e75adccf4ec23bf709bede28a4b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730425"
---
# <a name="azure-ddos-protection-standard--business-continuity"></a>Azure DDoS Protection Standard – Kontinuita podnikových aplikací

Provozní kontinuita a zotavení po havárii v Azure DDoS Protection Standard umožňuje vašemu podniku pokračovat v práci na tváři přerušení. Tento článek popisuje dostupnost (v rámci oblasti) a zotavení po havárii.

## <a name="overview"></a>Přehled
Azure DDoS Protection úrovně Standard chrání veřejné IP adresy ve virtuálních sítích. Ochranu je snadné povolit u jakékoli nové nebo existující virtuální sítě a nevyžaduje žádné změny aplikací nebo prostředků.

Virtual Network (VNet) je logická reprezentace vaší sítě v cloudu. Virtuální sítě slouží jako hranice důvěry k hostování vašich prostředků, jako je Azure Application Gateway, Azure Firewall a Azure Virtual Machines. Vytvoří se v rámci rozsahu oblasti. Můžete *vytvořit* virtuální sítě se stejným adresním prostorem ve dvou různých oblastech (například USA – východ a USA – západ), protože mají stejný adresní prostor, nemůžete je propojit dohromady. 

## <a name="business-continuity"></a>Kontinuita podnikových procesů

Může dojít k několika různým způsobům, které by mohlo dojít k přerušení vaší aplikace. Oblast může být zcela vyjmuta z důvodu přirozené havárie nebo částečné havárie z důvodu selhání více zařízení nebo služeb. Dopad na chráněný virtuální sítě se v každé z těchto situací liší.

**Otázka: Pokud dojde k výpadku pro celou oblast, co mám dělat? Například pokud je oblast zcela vyjmuta z důvodu přirozené havárie? Co se stane s virtuálními sítěmi hostovanými v dané oblasti?**

Odpověď: virtuální síť a prostředky v ovlivněné oblasti zůstávají v době přerušení služby nedostupné.

![Jednoduchý diagram Virtual Network.](../virtual-network/media/virtual-network-disaster-recovery-guidance/vnet.png)

**Otázka: co mohu znovu vytvořit stejnou virtuální síť v jiné oblasti?**

Odpověď: virtuální sítě jsou poměrně odlehčené prostředky. Můžete vyvolat rozhraní API Azure a vytvořit virtuální síť se stejným adresním prostorem v jiné oblasti. Pokud chcete znovu vytvořit stejné prostředí, které se nacházelo v zasažené oblasti, můžete volat rozhraní API k opětovnému nasazení prostředků v virtuální sítě, které byste měli mít. Pokud máte místní připojení, například při hybridním nasazení, musíte nasadit novou VPN Gateway a připojit se k místní síti.

Informace o vytvoření virtuální sítě najdete v tématu [vytvoření virtuální sítě](../virtual-network/manage-virtual-network.md#create-a-virtual-network).

**Otázka: může být replika virtuální sítě v dané oblasti znovu vytvořena v jiné oblasti před časem?**

Odpověď: Ano, můžete vytvořit dvě virtuální sítě pomocí stejného privátního adresního prostoru IP adres a prostředků ve dvou různých oblastech předem. Pokud ve virtuální síti používáte internetové služby, můžete nastavit Traffic Manager provoz geografického směrování na oblast, která je aktivní. Nemůžete ale propojit dvě virtuální sítě se stejným adresním prostorem do místní sítě, protože by to mohlo způsobovat problémy s směrováním. V době havárie a ztráty virtuální sítě v jedné oblasti můžete připojit druhou virtuální síť v oblasti k dispozici s adresním prostorem pro místní síť.

Informace o vytvoření virtuální sítě najdete v tématu [vytvoření virtuální sítě](../virtual-network/manage-virtual-network.md#create-a-virtual-network).

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit plán DDoS Protection](manage-ddos-protection.md).