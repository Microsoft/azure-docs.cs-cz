---
title: Povolit skupiny zabezpečení sítě v Azure Security Center | Microsoft Docs
description: V tomto dokumentu se dozvíte, jak implementovat Azure Security Center doporučení **Povolit skupiny zabezpečení sítě**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f53ed853-ffaf-4530-a019-1906ba6f341b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 14b7cc8f8162574380ca21ac515af8b7d3d5ded9
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "60911414"
---
# <a name="enable-network-security-groups-in-azure-security-center"></a>Povolit skupiny zabezpečení sítě v Azure Security Center
Azure Security Center doporučuje povolit skupinu zabezpečení sítě (NSG), pokud už jedna není povolená. Skupin zabezpečení sítě obsahují Access Control seznam pravidel seznamu řízení přístupu (ACL), která povolují nebo zakazují síťový provoz do instancí virtuálních počítačů ve Virtual Network. Skupiny NSG můžou být přidružené buď k podsítím, nebo k jednotlivým instancím virtuálních počítačů v této podsíti. Pokud je skupina zabezpečení sítě přidružená k podsíti, pravidla seznamu ACL platí pro všechny instance virtuálních počítačů v této podsíti. Kromě toho je možné provoz do jednotlivého virtuálního počítače dále omezit přidružením NSG k tomuto virtuálnímu počítači. Další informace najdete v tématu [co je skupina zabezpečení sítě (NSG)?](../virtual-network/security-overview.md)

Pokud nemáte skupin zabezpečení sítě povolený, Security Center nabízí dvě doporučení: Povolte skupiny zabezpečení sítě v podsítích a povolte skupiny zabezpečení sítě na virtuálních počítačích. Zvolíte, jakou úroveň, podsíť nebo virtuální počítač chcete použít skupin zabezpečení sítě.

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Není to podrobný průvodce.
>
>

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V okně **doporučení** vyberte **Povolit skupiny zabezpečení sítě** v podsítích nebo na virtuálních počítačích.
   ![Povolení skupin zabezpečení sítě][1]
2. Tím se otevře okno **Konfigurovat chybějící skupiny zabezpečení sítě** pro podsítě nebo pro virtuální počítače v závislosti na zvoleném doporučení. Vyberte podsíť nebo virtuální počítač, na kterém chcete nakonfigurovat NSG.

   ![Konfigurace NSG pro podsíť][2]

   ![Konfigurace NSG pro virtuální počítač][3]
3. V okně **zvolit skupinu zabezpečení sítě** vyberte existující NSG nebo vyberte **vytvořit novou** a vytvořte NSG.

   ![Zvolit skupinu zabezpečení sítě][4]

Pokud vytvoříte NSG, postupujte podle pokynů v části [Správa skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md) a vytvořte NSG a nastavte pravidla zabezpečení.

## <a name="see-also"></a>Viz také:
Tento článek vám ukázal, jak implementovat Security Center doporučení "Povolit skupiny zabezpečení sítě" pro podsítě nebo virtuální počítače. Další informace o povolení skupin zabezpečení sítě najdete v následujících tématech:

* [Co je skupina zabezpečení sítě (NSG)?](../virtual-network/security-overview.md)
* [Správa skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – Přečtěte si, jak doporučení vám pomůžou chránit vaše prostředky Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – Získejte nejnovější novinky a informace o zabezpečení Azure.

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
