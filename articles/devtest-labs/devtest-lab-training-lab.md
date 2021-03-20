---
title: Použití Azure DevTest Labs pro školení | Microsoft Docs
description: Tento článek poskytuje podrobné kroky, pomocí kterých můžete nastavit testovací prostředí pro školení v Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b2de9550c62f04286a4f9ad42238bfefb9846477
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89294845"
---
# <a name="use-azure-devtest-labs-for-training"></a>Použití Azure DevTest Labs pro školení
Azure DevTest Labs lze použít k implementaci mnoha klíčových scénářů kromě vývoje a testování. Jedním z těchto scénářů je nastavení testovacího prostředí pro školení. Azure DevTest Labs vám umožní vytvořit testovací prostředí, ve kterém můžete zadat vlastní šablony, které může každý praktikant použít k vytvoření identického a izolovaného prostředí pro školení. Můžete použít zásady, abyste zajistili, že jsou školicí prostředí k dispozici pro každého účastníka pouze v případě, že je potřebují a obsahují dostatek prostředků – například virtuální počítače – vyžadované pro školení. Nakonec můžete testovací prostředí snadno sdílet s praktikanty, ke kterým mají přístup jediným kliknutím.

![Použití DevTest Labs pro školení](./media/devtest-lab-training-lab/devtest-lab-training.png)

Azure DevTest Labs splňuje následující požadavky, které jsou nutné k provedení školení v jakémkoli virtuálním prostředí: 

* Stážisté nemůžou zobrazit virtuální počítače vytvořené jinými praktikanty.
* Každý školicí počítač by měl být stejný.
* Stážisté můžou rychle zřídit svoje školicí prostředí.
* Řízení nákladů zajištěním, že stážisté nemůžou získat více virtuálních počítačů, než potřebují pro školení a také vypnout virtuální počítače, když je nepoužívají
* Snadno sdílejte školicí prostředí s každým z praktikantů
* Znovu znovu spustit školicí prostředí a znovu

V tomto článku se dozvíte o různých funkcích Azure DevTest Labs, které se dají použít ke splnění předchozích popsaných požadavků na školení a podrobných kroků, které můžete provést při nastavení testovacího prostředí pro školení.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Implementace školení pomocí Azure DevTest Labs
1. **Vytvoření testovacího prostředí** 
   
    Laboratoře jsou výchozím bodem v Azure DevTest Labs. Po vytvoření testovacího prostředí můžete provádět úlohy, jako je přidání uživatelů (praktikantů) do testovacího prostředí, nastavení zásad pro řízení nákladů, definování imagí virtuálních počítačů, které se dají rychle vytvořit, a další.   
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Vytvoření testovacího prostředí v Azure DevTest Labs](devtest-lab-create-lab.md) |Naučte se, jak vytvořit testovací prostředí v Azure DevTest Labs Azure Portal. |
2. **Vytváření školicích virtuálních počítačů během několika minut pomocí připravených imagí a vlastních imagí z Marketplace** 
   
    Můžete vybrat připravené image z široké škály imagí v Azure Marketplace a zpřístupnit je pro praktikanty v testovacím prostředí. Pokud připravené image nesplňují vaše požadavky, můžete vytvořit vlastní image vytvořením testovacího virtuálního počítače pomocí předem připraveného obrázku z Azure Marketplace, instalací veškerého softwaru, který potřebujete pro školení, a uložením virtuálního počítače jako vlastního obrazu v testovacím prostředí. 
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Konfigurace imagí na Azure Marketplace](devtest-lab-configure-marketplace-images.md) |Přečtěte si, jak můžete Azure Marketplace imagí použít. zpřístupnění pro výběr pouze těch imagí, které chcete pro školení použít. |
   | [Vytvoření vlastní image](devtest-lab-create-template.md) |Vytvořte vlastní image předinstalací softwaru, který potřebujete pro školení, aby mohli praktikanti rychle vytvořit virtuální počítač pomocí vlastní image. |
3. **Vytváření opakovaně použitelných šablon pro školení počítačů** 
   
    Vzorec v Azure DevTest Labs je seznam výchozích hodnot vlastností, které se používají k vytvoření virtuálního počítače. Vzorec můžete v testovacím prostředí vytvořit tak, že vybíráte image, velikost virtuálního počítače (kombinaci procesoru a paměti RAM) a virtuální síť. Každý účastník může zobrazit vzorec v testovacím prostředí a použít ho k vytvoření virtuálního počítače. 
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Správa vzorců DevTest Labs pro vytváření virtuálních počítačů](devtest-lab-manage-formulas.md) |Zjistěte, jak můžete vytvořit vzorec vyzvednutím image, velikosti virtuálního počítače (kombinace CPU a paměti RAM) a virtuální sítě. |
4. **Náklady pod kontrolou**
   
    Azure DevTest Labs umožňuje nastavit v testovacím prostředí zásadu, která určuje maximální počet virtuálních počítačů, které může praktikant v testovacím prostředí vytvořit. 
   
    Pokud provádíte jednorázové školení a chcete zastavit všechny virtuální počítače v konkrétní denní době a pak je automaticky restartovat následující den, můžete to provést tak, že nastavíte automatické vypínání a automaticky spouštěné zásady v testovacím prostředí. 
   
    Až se školení dokončí, můžete odstranit všechny virtuální počítače najednou spuštěním jednoho skriptu PowerShellu. 
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Definice zásad testovacího prostředí](devtest-lab-set-lab-policy.md) |Řízení nákladů pomocí nastavení zásad v testovacím prostředí. |
   | [Odstranění všech virtuálních počítačů testovacího prostředí pomocí skriptu PowerShellu](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Až se školení dokončí, odstraňte všechny cvičení v jedné operaci. |
5. **Sdílejte testovací prostředí s každou praktikantem**
   
    K testovacím prostředím se dá přímo získat přímý odkaz pomocí odkazu, který sdílíte s vašimi praktikanty. Vaše stážisté nemusí mít ani účet Azure, pokud mají [účet Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Stážisté nemohou zobrazit virtuální počítače vytvořené jinými praktikanty.  
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Přidání praktikantu do testovacího prostředí v Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Pomocí Azure Portal můžete do školicího prostředí přidat praktikanty. |
   | [Přidání praktikantů do testovacího prostředí pomocí skriptu PowerShellu](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Pomocí PowerShellu můžete automatizovat přidávání praktikantů do školicího prostředí. |
   | [Získat odkaz na testovací prostředí](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Přečtěte si, jak lze k testovacímu prostředí získat přímý odkaz prostřednictvím hypertextového odkazu. |
6. **Znovu znovu spustit testovací prostředí a znovu** 
   
    Vytváření testovacích prostředí, včetně vlastních nastavení, můžete automatizovat vytvořením šablony Správce prostředků a jejím použitím pro opětovné vytvoření identické laboratoře. 
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Vytvoření testovacího prostředí pomocí šablony Správce prostředků](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Pomocí šablon Správce prostředků vytvořte cvičení v Azure DevTest Labs. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
