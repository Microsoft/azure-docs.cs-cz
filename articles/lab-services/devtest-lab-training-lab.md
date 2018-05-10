---
title: Použít k trénování Azure DevTest Labs | Microsoft Docs
description: Naučte se používat Azure DevTest Labs pro scénáře školení.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 85eddaaf101c3e85eca7514b04660163d23c1c80
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="use-azure-devtest-labs-for-training"></a>Použití Azure DevTest Labs pro školení
Azure DevTest Labs lze použít k implementaci mnoho klíčových scénářů, kromě vývoje/testování. Jedním z těchto scénářů je nastavit testovací prostředí pro školení. Azure DevTest Labs umožňuje vytvoření testovacího prostředí, kde můžete zadat vlastní šablony, které každý praktikanta můžete použít k vytvoření identické a izolované prostředí pro školení. Můžete použít zásady, abyste zajistili, že školení prostředí jsou k dispozici pro každý praktikanta jenom v případě, že je potřebují a obsahovat dostatek prostředků – jako jsou virtuální počítače - požadované pro školení. Nakonec můžete snadno sdílet testovací prostředí s povolání, které mají přístup k jedním kliknutím.

![Používá DevTest Labs pro školení](./media/devtest-lab-training-lab/devtest-lab-training.png)

Azure DevTest Labs splňuje následující požadavky, které jsou nutné k provádějte školení v jakémkoli virtuální prostředí: 

* Povolání neuvidí virtuální počítače vytvořené jiných povolání
* Každý počítač školení by měly být shodné
* Povolání můžete rychle zřídit jejich prostředí školení
* Řízení nákladů tím zajistí, že povolání nelze získat více virtuálních počítačů, než potřebují pro školení a také vypnutí virtuálních počítačů, když nejsou na jejich používání
* Snadno sdílet s každou praktikanta školení testovacího prostředí
* Znovu a znovu použít testovací prostředí školení

V tomto článku informace o různých funkcí Azure DevTest Labs, které můžete použít ke splnění požadavků bylo popsáno dříve školení a podrobné kroky, které vám pomůžou nastavit testovací prostředí pro školení.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Implementace školení s Azure DevTest Labs
1. **Vytvořit testovací prostředí** 
   
    Labs jsou výchozím bodem v Azure DevTest Labs. Po vytvoření testovacího prostředí, můžete provádět úlohy tak, jak přidat uživatele (povolání) do testovacího prostředí, nastavit zásady, které řídit náklady, definování Image virtuálních počítačů, které lze rychle vytvořit a další.   
   
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Vytvoření testovacího prostředí v Azure DevTest Labs](devtest-lab-create-lab.md) |Postup vytvoření testovacího prostředí v Azure DevTest Labs na portálu Azure. |
2. **Vytvořit virtuální počítače školení během pár minut pomocí připravených marketplace Image a vlastních bitových kopií** 
   
    Můžete vybrat připravených bitové kopie z široké škály bitové kopie v Azure Marketplace a zpřístupnit na povolání v testovacím prostředí. Jestliže připravených bitové kopie nevyhovují vašim požadavkům, můžete vytvořit vlastní image vytvořením testovacího prostředí virtuálních počítačů pomocí připravených bitové kopie z Azure Marketplace, instalace veškerý software, který budete potřebovat pro školení a uložení virtuálního počítače jako vlastní image v testovacím prostředí. 
   
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Konfigurace Azure Marketplace obrázků](devtest-lab-configure-marketplace-images.md) |Přečtěte si, jak Image Azure Marketplace povolených; zpřístupnění pro výběr pouze Image chcete použít pro školení. |
   | [Vytvořit vlastní image](devtest-lab-create-template.md) |Vytvořte vlastní image před instalací softwaru, které potřebujete k školení tak, aby povolání dokáže rychle vytvořit virtuální počítač pomocí vlastní image. |
3. **Vytvořit opakovaně použitelný šablony pro školení počítače** 
   
    Vzorec v Azure DevTest Labs je seznam výchozí hodnoty vlastností použít k vytvoření virtuálního počítače. Vzorec v testovacím prostředí vytvoříte výběr obrázku, velikost virtuálního počítače (kombinaci procesor a paměť RAM) a virtuální sítě. Každý praktikanta můžete zobrazit vzorec v testovacím prostředí a ji použít k vytvoření virtuálního počítače. 
   
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Spravovat DevTest Labs vzorce pro vytvoření virtuálních počítačů](devtest-lab-manage-formulas.md) |Zjistěte, jak můžete vytvořit vzorec podle vyzvednutí obrázku, velikost virtuálního počítače (kombinaci procesor a paměť RAM) a virtuální sítě. |
4. **Náklady na ovládací prvek**
   
    Azure DevTest Labs umožňuje nastavit zásady v testovacím prostředí, chcete-li určit maximální počet virtuálních počítačů, které lze vytvořit pomocí praktikanta v testovacím prostředí. 
   
    Pokud jsou provádění Vícedenní školení a chcete zastavit všechny virtuální počítače v určitém čase dne a pak automaticky je znovu spustit následující den, můžete snadno provést nastavením automatické ukončení a automatického spouštění zásad v testovacím prostředí. 
   
    Nakonec po dokončení školení můžete odstranit všechny virtuální počítače najednou spuštěním jednoho skriptu prostředí PowerShell. 
   
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Definice zásad testovacího prostředí](devtest-lab-set-lab-policy.md) |Řízení nákladů pomocí nastavení zásad v testovacím prostředí. |
   | [Odstranit všechny testovací prostředí virtuálních počítačů pomocí skriptu prostředí PowerShell](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Po dokončení školení, odstraňte všechny labs v rámci jedné operace. |
5. **Sdílet testovací prostředí s každou praktikanta**
   
    Labs lze přímo přistupovat pomocí odkazu, který sdílíte s vaší povolání. Vaše povolání dokonce ani nemusí mít účet Azure, tak dlouho, dokud mají [účtu Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Povolání neuvidí virtuální počítače vytvořené jiných povolání.  
   
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Přidání praktikanta do testovacího prostředí v Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Použití portálu Azure přidat povolání do testovacího prostředí školení. |
   | [Přidat povolání do testovacího prostředí pomocí skriptu prostředí PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Použití Powershellu k automatizaci přidání povolání do testovacího prostředí školení. |
   | [Získejte odkaz do testovacího prostředí](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Zjistěte, jak testovacího prostředí je přímo přístupný prostřednictvím hypertextový odkaz. |
6. **Znovu a znovu použít testovací prostředí** 
   
    Je možné automatizovat vytvoření testovacího prostředí, včetně vlastních nastavení, vytvořením šablony Resource Manageru a jej použijete k vytvoření identické labs znovu a znovu. 
   
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Vytvoření testovacího prostředí pomocí šablony Resource Manageru](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Vytvoření prostředí v Azure DevTest Labs pomocí šablony Resource Manageru. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

