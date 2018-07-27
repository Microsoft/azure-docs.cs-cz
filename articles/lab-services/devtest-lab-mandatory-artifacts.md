---
title: Zadejte povinné artefakty k vaší službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak chcete zadat povinný artefakty, které je třeba nainstalovat před instalací všechny artefakty uživatel vybral na virtuálních počítačích (VM) v testovacím prostředí.
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
ms.openlocfilehash: a739b958ad60e39c38e81ce887edf68349340bb0
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285698"
---
# <a name="specify-mandatory-artifacts-for-your-lab-in-azure-devtest-labs"></a>Zadejte povinné artefakty testovacího prostředí ve službě Azure DevTest Labs
Jako vlastník testovacího prostředí můžete zadat povinné artefakty, které se použijí na každém počítači v labu vytvoří. Představte si situaci, kde má každý počítač ve vaší laboratoři k připojení k vaší podnikové síti. Jednotliví uživatelé testovacího prostředí by v takovém případě muset přidat artefakt připojení k doméně při vytváření virtuálního počítače, abyste měli jistotu, že jejich počítač připojený k podnikové doméně. Jinými slovy uživatelé testovacího prostředí v podstatě musel znovu vytvořit počítač v případě, že zapomenete platí povinné artefakty na svém počítači. Jako vlastník testovacího prostředí provedete artefaktů připojení k doméně jako povinné artefaktů ve vaší laboratoři. Tento krok zajistí, že každý počítač připojený k podnikové síti a ukládá čas a úsilí pro uživatelům testovacího prostředí.
 
Další povinná artefakty může zahrnovat běžné nástroje, který používá váš tým nebo balíček platformy týkající se zabezpečení, které každý počítač, musí mít ve výchozím nastavení atd. Stručně řečeno běžné software, který musí mít každý počítač ve vaší laboratoři stane povinné artefaktů. Je-li vytvořit vlastní image z počítače, který má povinné použít artefakty a pak vytvořte nový počítač z této image, povinné artefakty, které jsou znovu použita na počítači během vytváření. Toto chování se také znamená, i když je starý, pokaždé, když vytváříte počítač z něj nejaktuálnější verzi povinné artefakty vlastní image se použijí k němu při vytváření toku. 
 
Jako povinné ty jsou podporovány pouze artefakty, které mají žádné parametry. Vaše uživatele testovacího prostředí není nutné k zadání dalších parametrů během vytváření prostředí a vytvoří tak jednoduchý proces vytvoření virtuálního počítače. 

## <a name="specify-mandatory-artifacts"></a>Zadejte povinné artefaktů
Povinné artefakty pro počítače s Windows a Linuxem můžete vybrat samostatně. Můžete také změnit pořadí těchto artefaktů v závislosti na pořadí, ve kterém chcete je použít. 

1. Na domovské stránce vašeho testovacího prostředí, vyberte **konfigurace a zásad** pod **nastavení**. 
3. Vyberte **povinné artefakty** pod **EXTERNÍM PROSTŘEDKŮM**. 
4. Vyberte **upravit** v **Windows** části nebo **Linux** oddílu. V tomto příkladu **Windows** možnost. 

    ![Stránka povinné artefakty – tlačítko pro úpravy](media/devtest-lab-mandatory-artifacts/mandatory-artifacts-edit-button.png)
4. Vyberte artefakt. Tento příklad používá **7-Zip** možnost. 
5. Na **přidání artefaktu** stránce **přidat**. 

    ![Povinné artefakty stránce – Přidání 7-zip](media/devtest-lab-mandatory-artifacts/add-seven-zip.png)
6. Pokud chcete přidat jiný artefakt, vyberte článek a vyberte **přidat**. V tomto příkladu přidá **Chrome** jako druhý povinný artefakt.

    ![Stránka povinné artefakty – přidání Chrome](media/devtest-lab-mandatory-artifacts/add-chrome.png)
7. Na **povinné artefakty** stránce se zobrazí zpráva, která určuje počet artefaktů vybrali. Pokud kliknete na zprávu, zobrazí artefakty, které jste vybrali. Vyberte **Uložit** uložte. 

    ![Stránka povinné artefakty – ukládání artefaktů](media/devtest-lab-mandatory-artifacts/save-artifacts.png)
8. Opakujte kroky je nutné určit povinné artefaktů pro virtuální počítače s Linuxem. 
    
    ![Stránka povinné artefakty – artefakty Windows a Linux](media/devtest-lab-mandatory-artifacts/windows-linux-artifacts.png)
9. K **odstranit** artefakt v seznamu vyberte **... (tři tečky)**  na konci řádku a vyberte **odstranit**. 
10. K **změnit pořadí** artefakty v seznamu, najeďte myší artefaktu, vyberte **... (tři tečky)**  , který se zobrazí na začátek řádku a přetáhnete položku do nového umístění. 
11. Chcete-li uložit povinné artefaktů v testovacím prostředí, vyberte **Uložit**. 

    ![Stránka povinné artefakty – ukládání artefaktů v testovacím prostředí](media/devtest-lab-mandatory-artifacts/save-to-lab.png)
12. Zavřít **konfigurace a zásad** stránky (vyberte **X** v pravém horním rohu) Chcete-li získat zpět na domovskou stránku pro testovací prostředí.  

## <a name="delete-a-mandatory-artifact"></a>Odstranit povinné artefaktu
Pokud chcete odstranit povinné artefaktů z testovacího prostředí, proveďte následující akce: 

1. Vyberte **konfigurace a zásad** pod **nastavení**. 
2. Vyberte **povinné artefakty** pod **EXTERNÍM PROSTŘEDKŮM**. 
3. Vyberte **upravit** v **Windows** části nebo **Linux** oddílu. V tomto příkladu **Windows** možnost. 
4. Vyberte zprávu s číslem povinné artefakty v horní části. 

    ![Povinné artefakty stránce – zpráva](media/devtest-lab-mandatory-artifacts/select-message-artifacts.png)
5. Na **vybrané artefakty** stránce **... (tři tečky)**  pro artefakt, který chcete odstranit a vyberte **odebrat**. 
    
    ![Stránka povinné artefakty – odebrat artefaktu](media/devtest-lab-mandatory-artifacts/remove-artifact.png)
6. Vyberte **OK** zavřete **vybrané artefakty** stránky. 
7. Vyberte **Uložit** na **povinné artefakty** stránky.
8. Opakujte kroky pro **Linux** obrázky v případě potřeby. 
9. Vyberte **Uložit** uložte všechny změny do testovacího prostředí. 

## <a name="view-mandatory-artifacts-when-creating-a-vm"></a>Povinné artefakty zobrazení při vytváření virtuálního počítače
Nyní jako uživatele testovacího prostředí můžete zobrazit seznam povinných artefakty při vytváření virtuálního počítače v testovacím prostředí. Se nedají upravit ani odstranit povinné artefakty nastaven vlastník testovacího prostředí v testovacím prostředí.

1. Na domovské stránce testovacího prostředí, vyberte **přehled** z nabídky.
2. Chcete-li přidat virtuální počítač do testovacího prostředí, vyberte **+ přidat**. 
3. Vyberte **základní image**. Tento příklad používá **systému Windows Server verze 1709**.
4. Všimněte si, že se zobrazí zpráva pro **artefakty** s počtem povinné vybrané artefakty. 
5. Vyberte **artefakty**. 
6. Zkontrolujte, jestli se **povinné artefakty** zadaný v konfiguraci testovacího prostředí a zásady. 

    ![Vytvoření virtuálního počítače – povinné artefaktů](media/devtest-lab-mandatory-artifacts/create-vm-artifacts.png)

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [přidání úložiště artefaktů Git do testovacího prostředí](devtest-lab-add-artifact-repo.md).

