---
title: Povolit prostředí s více virtuálními počítači v Azure Lab Services | Microsoft Docs
description: Naučte se vytvořit prostředí s několika virtuálními počítači v rámci šablony virtuálního počítače v testovacím prostředí pro učebnu Azure Lab Services.
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
ms.date: 10/13/2019
ms.author: spelluru
ms.openlocfilehash: 9a86ba803f899e78b2ba9640e6cc317966969e64
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332330"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>Vytvoření prostředí s několika virtuálními počítači v rámci šablony virtuálního počítače pro prostředí učebny
V současné době Azure Lab Services umožňuje nastavit jeden virtuální počítač šablony v testovacím prostředí a vytvořit pro každého uživatele jednu kopii. Pokud ale profesor výuku třídy IT o tom, jak nastavit brány firewall nebo servery, možná budete muset každému studentovi poskytnout prostředí, ve kterém může více virtuálních počítačů vzájemně komunikovat přes síť.

Vnořená virtualizace umožňuje vytvořit prostředí s několika virtuálními počítači v rámci virtuálních počítačů šablon testovacího prostředí. Publikování šablony poskytne každého uživatele v testovacím prostředí s virtuálním počítačem nastaveným s několika virtuálními počítači v rámci něj.

## <a name="what-is-nested-virtualization"></a>Co je vnořená virtualizace?
Vnořená virtualizace umožňuje vytvářet virtuální počítače v rámci virtuálního počítače. Vnořená virtualizace se provádí prostřednictvím technologie Hyper-V a je dostupná jenom na virtuálních počítačích s Windows.

Další informace o vnořené virtualizaci naleznete v následujících článcích:

- [Vnořená virtualizace v Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Jak povolit vnořenou virtualizaci na virtuálním počítači Azure](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>Použít vnořenou virtualizaci v Azure Lab Services
Důležité kroky:

1. Vytvořte pro testovací prostředí **rozsáhlou** velikost počítače s **Windows** šablonou. 
2. Připojte se k němu a [Povolte vnořenou virtualizaci](../../virtual-machines/windows/nested-virtualization.md).


Následující postup vám poskytne podrobné kroky: 

1. Vytvořte účet testovacího prostředí, pokud ho ještě nemáte. Pokyny najdete v tématu [kurz: nastavení účtu testovacího prostředí pomocí Azure Lab Services](tutorial-setup-lab-account.md).
1. Přejděte na [web Azure Lab Services](https://labs.azure.com). Všimněte si, že aplikace Internet Explorer 11 ještě není podporována. 
2. Vyberte **Sign in** (Přihlásit se) a zadejte své přihlašovací údaje. Azure Lab Services podporuje účty organizací a účty Microsoft. 
3. Vyberte **nové testovací prostředí**. 
    
    ![Vytvoření testovacího prostředí v učebně](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. V okně **New Lab** (Nové testovací prostředí) proveďte následující akce: 
    1. Zadejte **název** testovacího prostředí. 
    2. Pro **Velikost virtuálního počítače**vyberte **Velká (vnořená virtualizace)** nebo **střední (vnořená virtualizace)** .
    6. Vyberte **bitovou kopii** systému Windows, kterou chcete použít. Vnořená virtualizace je dostupná jenom na počítačích s Windows. 
    4. Pak vyberte **Next** (Další). 

        ![Vytvoření testovacího prostředí v učebně](../media/how-to-enable-multi-vm-environment/new-lab-window.png)
    1. Na stránce **přihlašovací údaje virtuálního počítače** zadejte výchozí přihlašovací údaje pro všechny virtuální počítače v testovacím prostředí. Zadejte **jméno** a **heslo** pro uživatele a pak vyberte **Další**.  

        ![Nové okno testovacího prostředí](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Uživatelské jméno a heslo si poznamenejte. Znovu se už nezobrazí.
    3. Na stránce **zásady testovacího prostředí** zadejte počet hodin přidělený každému uživateli (**kvótu pro každého uživatele**) mimo naplánovaný čas pro testovací prostředí a pak vyberte **Dokončit**. 

        ![Kvóta pro každého uživatele](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Měla by se zobrazit následující obrazovka, která zobrazuje stav vytvoření virtuálního počítače šablony. Vytvoření šablony v testovacím prostředí může trvat až 20 minut. 

    ![Stav vytvoření virtuálního počítače šablony](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
1. Na stránce **Šablona** vyberte **přizpůsobit šablonu** na panelu nástrojů. 

    ![Tlačítko přizpůsobit šablonu](../media/how-to-create-manage-template/customize-template-button.png)
2. V dialogovém okně **přizpůsobit šablonu** vyberte **pokračovat**. Po spuštění šablony a provedení změn už nebude mít stejné nastavení jako virtuální počítače, které jsou naposledy publikované pro vaše uživatele. Změny šablony se neprojeví na stávajících virtuálních počítačích vašich uživatelů, dokud je znovu nepublikujete.

    ![Dialogové okno přizpůsobit](../media/how-to-create-manage-template/customize-template-dialog.png)
1. Kliknutím na tlačítko **připojit k šabloně** na panelu nástrojů se připojte k virtuálnímu počítači šablony a nakonfigurujte vnořenou virtualizaci a postupujte podle pokynů. Pokud se jedná o počítač s Windows, zobrazí se možnost stáhnout soubor RDP. 

    ![Připojení k šabloně virtuálního počítače](../media/how-to-create-manage-template/connect-template-vm.png) 
9. V rámci virtuálního počítače šablony nastavte vnořenou virtualizaci a nakonfigurujte virtuální síť s více virtuálními počítači. Podrobné pokyny najdete v tématu [Postup povolení vnořené virtualizace ve virtuálním počítači Azure](../../virtual-machines/windows/nested-virtualization.md). Tady je stručný přehled kroků: 
    1. Povolte funkci Hyper-V na virtuálním počítači šablony.
    2. Nastavení interní virtuální sítě s připojením k Internetu pro vnořené virtuální počítače
    3. Vytváření virtuálních počítačů pomocí Správce technologie Hyper-V
    4. Přiřazení IP adresy k virtuálním počítačům
10. Na stránce **Šablona** vyberte **publikovat** na panelu nástrojů. 

    ![Tlačítko publikovat šablonu](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Publikování nejde vrátit zpět. 
8. Na stránce **publikovat šablonu** zadejte počet virtuálních počítačů, které chcete v testovacím prostředí vytvořit, a pak vyberte **publikovat**. 

    ![Šablona publikování – počet virtuálních počítačů](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Na stránce se zobrazí **stav publikování** šablony. Tento proces může trvat až hodinu. 

    ![Publikování šablony – průběh](../media/tutorial-setup-classroom-lab/publish-template-progress.png)


## <a name="next-steps"></a>Další kroky

Každý uživatel teď získá jeden virtuální počítač, který obsahuje prostředí s více virtuálními počítači. Informace o tom, jak přidat uživatele do testovacího prostředí a jak odeslat odkaz na registraci, najdete v následujícím článku: [Přidání uživatelů do testovacího prostředí](tutorial-setup-classroom-lab.md#add-users-to-the-lab).