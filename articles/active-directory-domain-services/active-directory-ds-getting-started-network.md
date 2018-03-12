---
title: "Azure Active Directory Domain Services: Začínáme | Microsoft Docs"
description: "Povolit Azure Active Directory Domain Services pomocí portálu Azure"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: maheshu
ms.openlocfilehash: 858adf2b0a0431078561a6f0edf80860927f8233
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Povolit Azure Active Directory Domain Services pomocí portálu Azure


## <a name="before-you-begin"></a>Než začnete
Přečtěte si článek [Důležité informace o sítích pro Azure Active Directory Domain Services](active-directory-ds-networking.md).


## <a name="task-2-configure-network-settings"></a>Úloha 2: Konfigurace nastavení sítě
Další úlohou konfigurace je vytvoření virtuální sítě Azure a vyhrazené podsítě v něm. V této podsíti v rámci své virtuální sítě povolíte službu Azure Active Directory Domain Services. Také můžete vybrat existující virtuální síť a vytvořit vyhrazený podsítě v něm.

1. Klikněte na tlačítko **virtuální síť** vyberte virtuální síť.
    > [!NOTE]
    > **Klasické virtuální sítě nejsou podporovány pro nová nasazení.** Klasické virtuální sítě nejsou podporovány pro nová nasazení. Existující spravované domény nasazené v klasické virtuální sítě i dál podporovaná. Poskytujeme umožňuje migrovat existující spravované domény z klasickou virtuální síť k virtuální síti Resource Manager v blízké budoucnosti.
    >

2. Na **zvolte virtuální síť** stránce se zobrazí všechny existující virtuální sítě. Zobrazí pouze virtuální sítě, které patří do skupiny prostředků a umístění Azure, které jste vybrali na **Základy** stránce průvodce.
3. Vyberte virtuální síť, ve kterém by měl být povolili Azure AD Domain Services. Můžete vybrat existující virtuální síť, nebo vytvořte novou.

  > [!TIP]
  > **Vaší spravované domény nelze přesunout do jiné virtuální sítě po povolení služby Azure AD Domain Services.** Vyberte správnou virtuální sítě pro povolení vaší spravované domény. Po vytvoření spravované domény, nemůžete ho přesunout do jiné virtuální sítě, bez odstranění spravované domény. Doporučujeme vám, že kontrola [sítě důležité informace týkající se Azure Active Directory Domain Services](active-directory-ds-networking.md) než budete pokračovat.  
  >

4. **Vytvořit virtuální síť:** klikněte na tlačítko **vytvořit nový** k vytvoření nové virtuální sítě. Důrazně doporučujeme používat vyhrazené podsíť pro Azure AD Domain Services. Můžete například vytvořte podsíť s názvem "DomainServices", a usnadňuje pro ostatní správci zjistit, co je nasazen v rámci podsítě. Klikněte na tlačítko **OK** po dokončení.

    ![Vyberte virtuální síť](./media/getting-started/domain-services-blade-network-pick-vnet.png)

  > [!WARNING]
  > Je nutné vybrat adresní prostor, který je v rámci prostor privátní IP adresy. IP adresy, které nevlastníte, které jsou v veřejný adresní prostor způsobovat chyby v rámci Azure AD Domain Services.

5. **Existující virtuální síť:** Pokud budete chtít vyberte stávající virtuální síť, [vytvořit vyhrazený podsíť pomocí virtuální sítě rozšíření](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)a pak vyberte této podsíti. Klikněte na tlačítko **virtuální sítě** vyberte stávající virtuální síť. Klikněte na tlačítko **podsíť** vybrat vyhrazené podsíť ve stávající virtuální síť, ve které chcete povolit vaší nové spravované domény. Klikněte na tlačítko **OK** po dokončení.

    ![Vyberte podsítí v rámci virtuální sítě](./media/getting-started/domain-services-blade-network-pick-subnet.png)

  > [!NOTE]
  > **Pokyny pro výběr podsíť**
  > 1. Použijte vyhrazenou podsíť pro Azure AD Domain Services. Nenasazujte případných dalších virtuálních počítačů na této podsíti. Tato konfigurace umožňuje nakonfigurovat skupiny zabezpečení sítě (Nsg) pro zatížení nebo virtuální počítače bez nutnosti přerušení vaší spravované domény. Podrobnosti najdete v tématu [sítě důležité informace týkající se Azure Active Directory Domain Services](active-directory-ds-networking.md).
  2. Nevybírejte podsíť brány pro nasazení služby Azure AD Domain Services, protože se nejedná o podporovanou konfiguraci.
  3. Zajistěte, aby podsíť, kterou jste vybrali dostatek místa na dostupnou adresu - aspoň 3 až 5 dostupných IP adres a existuje v prostor privátní IP adresy.
  >

6. Až budete hotovi, klikněte na tlačítko **OK** Chcete-li pokračovat **skupiny správců** stránce průvodce.


## <a name="next-step"></a>Další krok
[Úloha 3: Konfigurace skupiny pro správu a povolení služby Azure AD Domain Services](active-directory-ds-getting-started-admingroup.md)
