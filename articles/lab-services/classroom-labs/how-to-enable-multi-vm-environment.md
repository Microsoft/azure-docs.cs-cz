---
title: Povolit prostředí více virtuálních počítačů v Azure Lab Services | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit prostředí s více virtuálními počítači v rámci šablony virtuálního počítače v prostředí v učebně Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: spelluru
ms.openlocfilehash: 6faf32232c42f863bff52fdfb3c0714aee8e9b88
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60702393"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>Vytvoření prostředí s více virtuálními počítači v rámci šablony virtuálního počítače z testovacího prostředí v učebně
Aktuálně Azure Lab Services vám umožní nastavit jednu šablonu virtuálního počítače v testovacím prostředí a zpřístupnit jedna kopie všech uživatelů. Ale pokud profesor vyučují třídu IT o tom, jak nastavit brány firewall nebo servery, budete muset jednotlivé studenty poskytnout prostředí, ve kterém více virtuálních počítačů můžou komunikovat přes síť.

Vnořená virtualizace umožňuje vytvářet prostředí s více virtuálních počítačů v testovacím prostředí šablony virtuálního počítače. Publikování šablony se pro každého uživatele v testovacím prostředí pomocí virtuálního počítače s několika virtuálních počítačů v rámci něj.

## <a name="what-is-nested-virtualization"></a>Co je vnořená virtualizace?
Vnořená virtualizace umožňuje vytvářet virtuální počítače v rámci virtuálního počítače. Vnořená virtualizace se provádí pomocí technologie Hyper-V a je dostupná pouze na virtuální počítače s Windows.

Další informace o vnořená virtualizace najdete v následujících článcích:

- [Vnořená virtualizace v Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Povolení vnořené virtualizace ve Virtuálním počítači Azure](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>Použití vnořené virtualizace v Azure Lab Services
Jsou důležité kroky:

1. Vytvoření **velké** velikosti **Windows** počítače šablony pro testovací prostředí. 
2. Připojte se k němu a [povolit vnořenou virtualizaci s](../../virtual-machines/windows/nested-virtualization.md).


Následující postup obsahuje podrobné pokyny: 

1. Pokud již nemáte, vytvořte si účet testovacího prostředí. Pokyny najdete v tématu [kurzu: Nastavení účtu testovacího prostředí pomocí Azure Lab Services](tutorial-setup-lab-account.md).
2. Přejděte na [web Azure Lab Services](https://labs.azure.com). 
3. Vyberte **Sign in** (Přihlásit se) a zadejte své přihlašovací údaje. Azure Lab Services podporuje účty organizací a účty Microsoft. 
4. V okně **New Lab** (Nové testovací prostředí) proveďte následující akce: 
    1. Zadejte **název** testovacího prostředí. 
    2. Zadejte maximální **počet virtuálních počítačů** v testovacím prostředí. Můžete zvýšit nebo decreate počet virtuálních počítačů po vytvoření testovacího prostředí nebo v existující testovací prostředí. Další informace najdete v tématu [aktualizovat počet virtuálních počítačů v testovacím prostředí](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)
    6. Vyberte **Uložit**.

        ![Vytvoření testovacího prostředí v učebně](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Na stránce **Select virtual machine specifications** (Výběr specifikací virtuálních počítačů) proveďte následující kroky:
    1. Vyberte **velké** pro velikosti virtuálních počítačů (VM) má být vytvořen v testovacím prostředí. V současné době podporuje pouze velká velikost vnořená virtualizace.
    2. Zvolte image virtuálního počítače, který je **Windows image**. Vnořená virtualizace je dostupná pouze na počítače s Windows. 
    3. Vyberte **Další**.

        ![Zadání specifikací virtuálních počítačů](../media/how-to-enable-multi-vm-environment/large-windows-vm.png)    
5. Na stránce **Set credentials** (Nastavení přihlašovacích údajů) zadejte výchozí přihlašovací údaje ke všem virtuálním počítačům v testovacím prostředí. 
    1. Zadejte **jméno uživatele** pro všechny virtuální počítače v testovacím prostředí.
    2. Zadejte **heslo** tohoto uživatele. 

        > [!IMPORTANT]
        > Uživatelské jméno a heslo si poznamenejte. Znovu se už nezobrazí.
    3. Vyberte **Vytvořit**. 

        ![Nastavení přihlašovacích údajů](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Na stránce **Configure template** (Konfigurace šablony) se zobrazí stav vytváření testovacího prostředí. Vytvoření šablony v testovacím prostředí může trvat až 20 minut. 

    ![Konfigurace šablony](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Po dokončení konfigurace šablony se zobrazí následující stránka: 

    ![Stránka Configure template (Konfigurace šablony) po dokončení konfigurace](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Na **konfigurovat šablony** stránce **připojit** pro připojení k šabloně virtuálního počítače ke konfiguraci vnořená virtualizace. Můžete také nakonfigurovat později po dokončení kroků v tomto průvodci. 
9. Uvnitř šablony virtuálního počítače nastavení vnořená virtualizace a konfigurace virtuální sítě s několika virtuálními počítači. Podrobné pokyny krok za krokem, najdete v článku [povolení vnořené virtualizace ve Virtuálním počítači Azure](../../virtual-machines/windows/nested-virtualization.md). Tady je stručný přehled kroků: 
    1. Povolte funkce Hyper-V v šabloně virtuálního počítače.
    2. Nastavit interní virtuální sítě s připojením k Internetu pro vnořených virtuálních počítačů
    3. Vytvářet virtuální počítače pomocí Správce technologie Hyper-V
    4. Přiřazení IP adresy k virtuálním počítačům
10. Na stránce šablony vyberte **Next** (Další). 
11. Na stránce **Publish the template** (Publikování šablony) proveďte následující akce. 
    1. Chcete-li publikovat šablony okamžitě a vyberte **publikovat**.  

        > [!WARNING]
        > Publikování nejde vrátit zpět. 
    2. Pokud chcete publikování provést později, vyberte **Save for later** (Uložit na později). Virtuální počítač šablony můžete publikovat i po dokončení průvodce. Podrobnosti o postupu konfigurace a publikování po dokončení průvodce najdete v části [Publikování šablony](how-to-create-manage-template.md#publish-the-template-vm) v článku [Správa testovacích prostředí v učebnách](how-to-manage-classroom-labs.md).

        ![Publikování šablony](../media/how-to-enable-multi-vm-environment/publish-template-page.png)
11. Zobrazí se **průběh publikování** šablony. Tento proces může trvat až hodinu. 

    ![Publikování šablony – průběh](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Po úspěšném publikování šablony se zobrazí následující stránka. Vyberte **Done** (Hotovo).

    ![Publikování šablony – úspěch](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Zobrazí se **řídicí panel** testovacího prostředí. 
    
    ![Řídicí panel testovacího prostředí v učebně](../media/how-to-enable-multi-vm-environment/dashboard.png)


## <a name="next-steps"></a>Další postup

Nyní každý uživatel získá jeden virtuální počítač, který obsahuje prostředí více virtuálních počítačů v rámci něj. Zjistěte, jak přidat uživatele do testovacího prostředí a poslat odkaz registrace k nim, najdete v následujícím článku: [Přidání uživatelů do testovacího prostředí](tutorial-setup-classroom-lab.md#add-users-to-the-lab).