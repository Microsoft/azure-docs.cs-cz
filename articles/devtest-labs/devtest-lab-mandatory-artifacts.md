---
title: Zadejte povinné artefakty pro váš Azure DevTest Labs | Microsoft Docs
description: Přečtěte si, jak zadat povinné artefakty, které je potřeba nainstalovat předtím, než nainstalujete jakékoli uživatelem vybrané artefakty na virtuální počítače (VM) v testovacím prostředí.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0677f8bea35cb34735fdcf34e717eea349fad8bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480299"
---
# <a name="specify-mandatory-artifacts-for-your-lab-in-azure-devtest-labs"></a>Zadejte pro testovací prostředí povinné artefakty v Azure DevTest Labs
Jako vlastník testovacího prostředí můžete zadat povinné artefakty, které se aplikují na každý počítač vytvořený v testovacím prostředí. Představte si situaci, kdy chcete, aby se každý počítač v testovacím prostředí připojoval k podnikové síti. V takovém případě by měl každý uživatel testovacího prostředí během vytváření virtuálního počítače přidat artefakt připojení k doméně, aby se zajistilo, že je počítač připojený k podnikové doméně. Jinými slovy, laboratorní uživatelé by museli v podstatě znovu vytvořit počítač pro případ, že by zapomněli použít na svém počítači povinné artefakty. Jako vlastník testovacího prostředí provedete artefakt připojení k doméně jako povinný artefakt v testovacím prostředí. Tento krok zajistí, že je každý počítač připojený k podnikové síti a šetří čas a úsilí pro vaše uživatele testovacího prostředí.
 
Mezi další povinné artefakty může patřit společný nástroj, který váš tým používá, nebo balíček zabezpečení pro konkrétní platformu, který každý počítač musí mít ve výchozím nastavení, atd. V krátkém případě je nutné, aby každý běžný software, který se každý počítač v testovacím prostředí, stal povinným artefaktem. Pokud vytvoříte vlastní image z počítače, na kterém jsou použité povinné artefakty, a pak z této image vytvoříte nový počítač, v počítači se při vytváření znovu nasadí povinné artefakty. Toto chování také znamená, že i když je vlastní image stará, pokaždé, když vytvoříte počítač z něj, v průběhu vytváření toku použijeme na něj nejvíce aktualizovanou verzi povinných artefaktů. 
 
Pouze artefakty bez parametrů nejsou podporovány jako povinné. Uživatel testovacího prostředí nemusí během vytváření testovacího prostředí zadávat další parametry, takže proces vytváření virtuálních počítačů je jednoduchý. 

## <a name="specify-mandatory-artifacts"></a>Určení povinných artefaktů
Můžete vybrat povinné artefakty pro počítače se systémem Windows a Linux samostatně. Tyto artefakty můžete změnit i v závislosti na pořadí, ve kterém je chcete použít. 

1. Na domovské stránce testovacího prostředí vyberte v části **Nastavení** možnost **Konfigurace a zásady** . 
3. V části **externí prostředky** vyberte **povinné artefakty** . 
4. V části **Windows** nebo **Linux** vyberte **Upravit** . V tomto příkladu se používá možnost **Windows** . 

    ![Stránka povinných artefaktů – tlačítko pro úpravy](media/devtest-lab-mandatory-artifacts/mandatory-artifacts-edit-button.png)
4. Vyberte artefakt. V tomto příkladu se používá parametr **7-zip** . 
5. Na stránce **Přidat artefakt** vyberte **Přidat**. 

    ![Stránka povinných artefaktů – přidat 7 – PSČ](media/devtest-lab-mandatory-artifacts/add-seven-zip.png)
6. Chcete-li přidat jiný artefakt, vyberte článek a vyberte možnost **Přidat**. Tento příklad přidá **chrom** jako druhý povinný artefakt.

    ![Stránka povinných artefaktů – přidání Chromu](media/devtest-lab-mandatory-artifacts/add-chrome.png)
7. Na stránce **povinné artefakty** se zobrazí zpráva, která určuje počet vybraných artefaktů. Pokud kliknete na zprávu, zobrazí se artefakty, které jste vybrali. Vyberte **Uložit** a uložte. 

    ![Stránka povinných artefaktů – uložení artefaktů](media/devtest-lab-mandatory-artifacts/save-artifacts.png)
8. Opakujte postup pro zadání povinných artefaktů pro virtuální počítače se systémem Linux. 
    
    ![Povinná stránka artefaktů – artefakty Windows a Linux](media/devtest-lab-mandatory-artifacts/windows-linux-artifacts.png)
9. Chcete-li **Odstranit** artefakt ze seznamu, vyberte **... (tři tečky)** na konci řádku a vyberte **Odstranit**. 
10. Chcete-li **změnit pořadí** artefaktů v seznamu, najeďte myší na artefakt, vyberte **... (tři tečky)** který se zobrazí na začátku řádku a přetáhněte položku na novou pozici. 
11. Pokud chcete uložit povinné artefakty v testovacím prostředí, vyberte **Save (Uložit**). 

    ![Stránka povinných artefaktů – uložení artefaktů v testovacím prostředí](media/devtest-lab-mandatory-artifacts/save-to-lab.png)
12. Zavřete stránku **Konfigurace a zásady** (vyberte **X** v pravém horním rohu), abyste se mohli vrátit na domovskou stránku testovacího prostředí.  

## <a name="delete-a-mandatory-artifact"></a>Odstranění povinného artefaktu
Pokud chcete odstranit povinný artefakt z testovacího prostředí, proveďte následující akce: 

1. V části **Nastavení** vyberte **Konfigurace a zásady** . 
2. V části **externí prostředky** vyberte **povinné artefakty** . 
3. V části **Windows** nebo **Linux** vyberte **Upravit** . V tomto příkladu se používá možnost **Windows** . 
4. Vyberte zprávu s počtem povinných artefaktů v horní části. 

    ![Stránka povinných artefaktů – výběr zprávy](media/devtest-lab-mandatory-artifacts/select-message-artifacts.png)
5. Na stránce **vybrané artefakty** vyberte **... (tři tečky)** pro artefakt, který chcete odstranit, a vyberte **Odebrat**. 
    
    ![Stránka povinných artefaktů – odebrat artefakt](media/devtest-lab-mandatory-artifacts/remove-artifact.png)
6. Výběrem **OK** zavřete stránku **vybrané artefakty** . 
7. Na stránce **povinné artefakty** vyberte **Save (Uložit** ).
8. V případě potřeby zopakujte kroky pro image se **systémem Linux** . 
9. Vyberte **Uložit** a uložte všechny změny do testovacího prostředí. 

## <a name="view-mandatory-artifacts-when-creating-a-vm"></a>Zobrazit povinné artefakty při vytváření virtuálního počítače
Teď jako uživatel testovacího prostředí můžete při vytváření virtuálního počítače v testovacím prostředí zobrazit seznam povinných artefaktů. Vlastníkem testovacího prostředí nemůžete upravit ani odstranit povinné artefakty nastavené v testovacím prostředí.

1. Na domovské stránce testovacího prostředí vyberte v nabídce **Přehled** .
2. Pokud chcete přidat virtuální počítač do testovacího prostředí, vyberte **+ Přidat**. 
3. Vyberte **základní image**. V tomto příkladu **se používá Windows Server verze 1709**.
4. Všimněte si, že se zobrazí zpráva pro **artefakty** s počtem vybraných povinných artefaktů. 
5. Vyberte **artefakty**. 
6. Ověřte, že jste viděli **povinné artefakty** , které jste zadali v konfiguraci a zásadách testovacího prostředí. 

    ![Vytvoření virtuálního počítače – povinné artefakty](media/devtest-lab-mandatory-artifacts/create-vm-artifacts.png)

## <a name="next-steps"></a>Další kroky
* Naučte se [Přidat úložiště artefaktů Git do testovacího prostředí](devtest-lab-add-artifact-repo.md).

