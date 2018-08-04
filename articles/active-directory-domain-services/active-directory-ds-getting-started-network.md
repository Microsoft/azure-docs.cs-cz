---
title: 'Azure Active Directory Domain Services: Začínáme | Dokumentace Microsoftu'
description: Povolit Azure Active Directory Domain Services pomocí webu Azure portal
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: maheshu
ms.openlocfilehash: c167f40e09ac877d00d348285383106fc5ba41bc
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503906"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Povolit Azure Active Directory Domain Services pomocí webu Azure portal


## <a name="before-you-begin"></a>Než začnete
Přečtěte si článek [Důležité informace o sítích pro Azure Active Directory Domain Services](active-directory-ds-networking.md).


## <a name="task-2-configure-network-settings"></a>Úloha 2: Konfigurace nastavení sítě
Další úlohou konfigurace je vytvoření virtuální sítě Azure a vyhrazenou podsíť ve ho. V této podsíti v rámci své virtuální sítě povolíte službu Azure Active Directory Domain Services. Také můžete vybrat existující virtuální sítě a vytvořit vyhrazenou podsíť ve ho.

1. Klikněte na tlačítko **virtuální síť** vybrat virtuální síť.
    > [!NOTE]
    > **Klasické virtuální sítě nejsou podporovány pro nová nasazení.** Klasické virtuální sítě nejsou podporovány pro nová nasazení. Existující spravovaných domén, které jsou nasazené v klasických virtuálních sítích nadále podporovány. Microsoft vám umožní přenést existující spravovanou doménu z klasické virtuální sítě k virtuální síti správce prostředků v blízké budoucnosti.
    >

2. Na **zvolte virtuální síť** stránce se zobrazí všechny existující virtuální sítě. Zobrazit pouze virtuální sítě, které patří do skupiny prostředků a umístění Azure, které jste vybrali na **Základy** stránky průvodce.
3. Zvolte virtuální síť, ve kterém by měla být povolená Azure AD Domain Services. Můžete si vybrat existující virtuální síť, nebo vytvořte novou.

  > [!TIP]
  > **Vaši spravovanou doménu nelze přesunout do jiné virtuální sítě po povolení služby Azure AD Domain Services.** Můžete si vyberte správnou virtuální síť umožňuje vaší spravované domény. Po vytvoření spravované domény, nemůžete ho přesunout do jiné virtuální sítě, bez odstranění spravované domény. Doporučujeme vám, že zkontrolujete [sítě důležité informace týkající se Azure Active Directory Domain Services](active-directory-ds-networking.md) předtím, než budete pokračovat.  
  >

4. **Vytvořit virtuální síť:** klikněte na tlačítko **vytvořit nový** při vytváření nové virtuální sítě. Pro službu Azure AD Domain Services použijte vyhrazenou podsíť. Například vytvořte podsíť s názvem "DomainServices' to vám usnadní ostatním správcům pochopit, co je nasazen v rámci podsítě. Klikněte na tlačítko **OK** po dokončení.

    ![Vyberte virtuální síť](./media/getting-started/domain-services-blade-network-pick-vnet.png)

  > [!WARNING]
  > Je nutné vybrat adresní prostor, který je v rámci privátní adresní prostor IP adres. IP adresy, které nevlastníte, která jsou v veřejný adresní prostor způsobit chyby v rámci Azure AD Domain Services.

5. **Existující virtuální síť:** Pokud budete chtít vybrat stávající virtuální síť, [vytvořit vyhrazenou podsíť virtuální sítě s příponou](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)a pak vyberte příslušné podsíti. Klikněte na tlačítko **virtuální sítě** vyberte existující virtuální sítě. Klikněte na tlačítko **podsítě** k výběru vyhrazenou podsíť ve stávající virtuální sítě, ve kterém se mají povolit nové spravované domény. Klikněte na tlačítko **OK** po dokončení.

    ![Vyberte podsíť ve virtuální síti](./media/getting-started/domain-services-blade-network-pick-subnet.png)

  > [!NOTE]
  > **Pokyny pro výběr podsítě**
  > 1. Pro službu Azure AD Domain Services použijte vyhrazenou podsíť. Nenasazujte jiné virtuální počítače s touto podsítí. Tato konfigurace umožňuje konfigurovat skupiny zabezpečení sítě (Nsg) pro vaše úlohy nebo virtuální počítače bez narušení vaší spravované domény. Podrobnosti najdete v tématu [sítě důležité informace týkající se Azure Active Directory Domain Services](active-directory-ds-networking.md).
  2. Nevybírejte podsíť brány pro nasazení služby Azure AD Domain Services, protože není podporovaná konfigurace.
  3. Podsíť, ve které jste vybrali, musí mít aspoň 3 až 5 dostupných IP adres ve svém adresním prostoru.
  >

6. Jakmile budete hotovi, klikněte na tlačítko **OK** přejděte na **skupiny správců** stránky průvodce.


## <a name="next-step"></a>Další krok
[Úloha 3: Konfigurace skupiny pro správu a povolení služby Azure AD Domain Services](active-directory-ds-getting-started-admingroup.md)
