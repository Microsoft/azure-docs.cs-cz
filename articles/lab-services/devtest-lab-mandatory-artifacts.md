---
title: Určení povinných artefaktů pro laboratoře Azure DevTest | Dokumenty společnosti Microsoft
description: Zjistěte, jak zadat povinné artefakty, které je potřeba nainstalovat před instalací všech artefaktů vybraných uživatelem na virtuálních počítačích (VM) v testovacím prostředí.
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: spelluru
ms.openlocfilehash: 090236ec3647c7c3e38eb862780a615f854e952b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60562142"
---
# <a name="specify-mandatory-artifacts-for-your-lab-in-azure-devtest-labs"></a>Určení povinných artefaktů pro testovací prostředí v laboratořích Azure DevTest Labs
Jako vlastník testovacího prostředí můžete zadat povinné artefakty, které se použijí na každý počítač vytvořený v testovacím prostředí. Představte si scénář, ve kterém chcete, aby byl každý počítač ve vaší laboratoři připojen k vaší podnikové síti. V takovém případě by každý uživatel testovacího prostředí musel přidat artefakt spojení domény během vytváření virtuálního počítače, aby se ujistil, že je jejich počítač připojen k podnikové doméně. Jinými slovy, uživatelé testovacího prostředí by v podstatě museli znovu vytvořit počítač v případě, že by zapomněli použít povinné artefakty na svém počítači. Jako vlastník testovacího prostředí provedete artefakt spojení domény jako povinný artefakt ve vašem testovacím prostředí. Tento krok zajistí, že každý počítač je připojen k podnikové síti a šetří čas a úsilí pro uživatele testovacího prostředí.
 
Další povinné artefakty mohou zahrnovat společný nástroj, který váš tým používá, nebo balíček zabezpečení související s platformou, který musí mít každý počítač ve výchozím nastavení atd. Stručně řečeno, každý běžný software, který musí mít každý počítač ve vaší laboratoři, se stane povinným artefaktem. Pokud vytvoříte vlastní bitovou kopii ze počítače, který má povinné artefakty použít na něj a potom vytvořit nový počítač z této bitové kopie, povinné artefakty jsou znovu použity na počítači během vytváření. Toto chování také znamená, že i když vlastní image je stará, pokaždé, když vytvoříte počítač z něj nejaktuálnější verze povinné artefakty jsou použity na něj během toku vytváření. 
 
Pouze artefakty, které nemají žádné parametry jsou podporovány jako povinné. Váš uživatel testovacího prostředí nemusí zadávat další parametry během vytváření testovacího prostředí a tím jednoduchý proces vytváření virtuálních počítačů. 

## <a name="specify-mandatory-artifacts"></a>Určení povinných artefaktů
Povinné artefakty pro počítače se systémem Windows a Linux můžete vybrat samostatně. Můžete také upravit pořadí těchto artefaktů v závislosti na pořadí, ve kterém chcete, aby byly použity. 

1. Na domovské stránce testovacího prostředí vyberte v části **NASTAVENÍ**možnost **Konfigurace a zásady** . 
3. V části **EXTERNÍ ZDROJE**vyberte **povinné artefakty** . 
4. V části **Windows** nebo **Linux** uvyberte **Upravit.** Tento příklad používá možnost **systému Windows.** 

    ![Stránka Povinné artefakty - tlačítko Upravit](media/devtest-lab-mandatory-artifacts/mandatory-artifacts-edit-button.png)
4. Vyberte artefakt. Tento příklad používá **možnost 7-Zip.** 
5. Na stránce **Přidat artefakt** vyberte **Přidat**. 

    ![Povinná stránka artefaktů - Přidat 7-zip](media/devtest-lab-mandatory-artifacts/add-seven-zip.png)
6. Chcete-li přidat další artefakt, vyberte článek a vyberte **Přidat**. Tento příklad přidá **Chrome** jako druhý povinný artefakt.

    ![Stránka Povinné artefakty - Přidat Chrome](media/devtest-lab-mandatory-artifacts/add-chrome.png)
7. Na stránce **Povinné artefakty** se zobrazí zpráva, která určuje počet vybraných artefaktů. Pokud klepnete na zprávu, zobrazí se artefakty, které jste vybrali. Vyberte **Uložit,** chcete-li uložit. 

    ![Stránka povinné artefakty - Uložit artefakty](media/devtest-lab-mandatory-artifacts/save-artifacts.png)
8. Opakujte kroky k určení povinné artefakty pro virtuální počítače s Linuxem. 
    
    ![Stránka povinné artefakty - Windows a Linux artefakty](media/devtest-lab-mandatory-artifacts/windows-linux-artifacts.png)
9. Chcete-li **odstranit** artefakt ze seznamu, vyberte **... (tři tečky)** na konci řádku a vyberte **Odstranit**. 
10. Chcete-li v seznamu **přeuspořádat** artefakty, najeďte myší na artefakt, vyberte **... (tři tečky)** který se zobrazí na začátku řádku, a přetáhněte položku na nové místo. 
11. Chcete-li uložit povinné artefakty do testovacího prostředí, vyberte **uložit**. 

    ![Stránka povinné artefakty – uložení artefaktů do testovacího prostředí](media/devtest-lab-mandatory-artifacts/save-to-lab.png)
12. Zavřete stránku **Konfigurace a zásady** (vyberte **X** v pravém horním rohu) a vraťte se na domovskou stránku testovacího prostředí.  

## <a name="delete-a-mandatory-artifact"></a>Odstranění povinného artefaktu
Chcete-li odstranit povinný artefakt z testovacího prostředí, proveďte následující akce: 

1. V části **NASTAVENÍ**vyberte **Možnost Konfigurace a zásady** . 
2. V části **EXTERNÍ ZDROJE**vyberte **povinné artefakty** . 
3. V části **Windows** nebo **Linux** uvyberte **Upravit.** Tento příklad používá možnost **systému Windows.** 
4. Vyberte zprávu s počtem povinných artefaktů v horní části. 

    ![Stránka Povinné artefakty – vyberte zprávu](media/devtest-lab-mandatory-artifacts/select-message-artifacts.png)
5. Na stránce **Vybrané artefakty** vyberte **... (tři tečky)** chcete-li artefakt odstranit, a vyberte **odebrat**. 
    
    ![Stránka povinné artefakty - Odebrat artefakt](media/devtest-lab-mandatory-artifacts/remove-artifact.png)
6. Výběrem **možnosti OK** zavřete stránku **Vybrané artefakty.** 
7. Vyberte **Uložit** na stránce **Povinné artefakty.**
8. V případě potřeby opakujte kroky pro obrazy **Linuxu.** 
9. Vyberte **Uložit,** chcete-li uložit všechny změny do testovacího prostředí. 

## <a name="view-mandatory-artifacts-when-creating-a-vm"></a>Zobrazení povinných artefaktů při vytváření virtuálního virtuálního mísy
Teď jako uživatel testovacího prostředí můžete zobrazit seznam povinných artefaktů při vytváření virtuálního virtuálního uživatele v testovacím prostředí. Nemůžete upravovat nebo odstraňovat povinné artefakty nastavené v testovacím prostředí vlastníkem testovacího prostředí.

1. Na domovské stránce testovacího prostředí vyberte v nabídce **přehled.**
2. Chcete-li přidat virtuální ho do testovacího prostředí, vyberte **+ Přidat**. 
3. Vyberte **základní obrázek**. Tento příklad používá **systém Windows Server verze 1709**.
4. Všimněte si, že se zobrazí zpráva pro **artefakty** s vybraným počtem povinných artefaktů. 
5. Vyberte **artefakty**. 
6. Potvrďte, že se zobrazují **povinné artefakty, které** jste zadali v konfiguraci a zásadách testovacího prostředí. 

    ![Vytvoření virtuálního virtuálního virtuálního montova - povinné artefakty](media/devtest-lab-mandatory-artifacts/create-vm-artifacts.png)

## <a name="next-steps"></a>Další kroky
* Přečtěte si, jak [přidat úložiště artefaktů Git do testovacího prostředí](devtest-lab-add-artifact-repo.md).

