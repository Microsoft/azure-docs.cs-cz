---
title: Povolit skupiny zabezpečení sítě v Azure Security Center | Dokumentace Microsoftu
description: Tento dokument popisuje, jak implementovat doporučení služby Azure Security Center **povolit skupiny zabezpečení sítě**.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: f53ed853-ffaf-4530-a019-1906ba6f341b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 2ac1baea3c4ef677f9c0927a038e15828683b851
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52308745"
---
# <a name="enable-network-security-groups-in-azure-security-center"></a>Povolit skupiny zabezpečení sítě v Azure Security Center
Azure Security Center doporučí, abyste povolili skupinu zabezpečení sítě (NSG), pokud ještě není povolené. Skupiny Nsg obsahují seznam pravidel seznamu řízení přístupu (ACL), která povolují nebo odpírají síťový provoz do vašich instancí virtuálních počítačů ve virtuální síti. Skupiny NSG můžou být přidružené buď k podsítím, nebo k jednotlivým instancím virtuálních počítačů v této podsíti. Pokud je skupina zabezpečení sítě přidružená k podsíti, pravidla seznamu ACL platí pro všechny instance virtuálních počítačů v této podsíti. Kromě toho provoz do konkrétního virtuálního počítače je možné omezit další tím, že přidružíte skupinu NSG přímo k tomuto virtuálnímu počítači. Další informace najdete tady [co je skupina zabezpečení sítě (NSG)?](../virtual-network/security-overview.md)

Pokud nemáte povolené skupiny zabezpečení sítě, Security Center nabízí dvě doporučení pro vás: Povolit skupiny zabezpečení sítě pro podsítě a povolení skupin zabezpečení sítě na virtuálních počítačích. Rozhodnete, jaké úroveň, podsíť nebo použití skupin zabezpečení sítě virtuálních počítačů.

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Není to podrobný průvodce.
>
>

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V **doporučení** okně vyberte **povolit skupiny zabezpečení sítě** podsítí nebo virtuálních počítačích.
   ![Povolení skupin zabezpečení sítě][1]
2. Otevře se okno **nakonfigurovat chybějící skupiny zabezpečení sítě** podsítí nebo virtuálních počítačů, v závislosti na doporučení, který jste vybrali. Vyberte podsíť nebo nakonfigurovat skupinu zabezpečení sítě na virtuální počítač.

   ![Nakonfigurujte skupiny zabezpečení sítě pro podsíť][2]

   ![Konfigurace skupiny zabezpečení sítě pro virtuální počítač][3]
3. Na **zvolit skupinu zabezpečení sítě** okno, vyberte existující skupině NSG nebo **vytvořit nový** vytvořit skupinu zabezpečení sítě.

   ![Vyberte skupinu zabezpečení sítě][4]

Pokud vytvoříte skupinu zabezpečení sítě, postupujte podle kroků v [spravovat skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md) vytvořte skupinu zabezpečení sítě a pravidel zabezpečení.

## <a name="see-also"></a>Další informace najdete v tématech
Tento článek vám ukázali, jak implementovat doporučení služby Security Center "Povolit skupiny zabezpečení sítě" pro podsítí nebo virtuálních počítačů. Další informace o povolení skupin zabezpečení sítě, naleznete v následujících tématech:

* [Co je skupina zabezpečení sítě (NSG)?](../virtual-network/security-overview.md)
* [Spravovat skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-azure-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – zjistěte, jak můžete monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – získejte nejnovější zprávy zabezpečení Azure a informace.

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
