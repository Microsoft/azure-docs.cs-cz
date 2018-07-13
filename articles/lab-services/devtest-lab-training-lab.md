---
title: Pomocí Azure DevTest Labs pro vzdělávání | Dokumentace Microsoftu
description: Zjistěte, jak pomocí Azure DevTest Labs pro trénovací scénáře.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38666790"
---
# <a name="use-azure-devtest-labs-for-training"></a>Pomocí Azure DevTest Labs pro školení
Azure DevTest Labs je možné implementovat mnoha klíčové scénáře kromě pro vývoj/testování. Jedním z těchto scénářů je nastavení testovacího prostředí pro školení. Azure DevTest Labs umožňuje vytvoření testovacího prostředí, ve kterém můžete zadat vlastní šablony, které každý účastník výcviku můžete použít k vytvoření shodné a jsou izolované prostředí pro školení. Můžete využít zásady pro zajištění, že školení prostředí jsou k dispozici pro každý účastník výcviku pouze v případě potřeby a obsahovat dostatek prostředků – například virtual machines – požadováno pro vzdělávání. Nakonec můžete jednoduše sdílet testovací prostředí s prováděli úkoly, které mají přístup k jedním kliknutím.

![Použití DevTest Labs pro školení](./media/devtest-lab-training-lab/devtest-lab-training.png)

Azure DevTest Labs splňuje následující požadavky, které jsou nutné k provádějte školení v jakémkoli virtuální prostředí: 

* Stážisté neuvidí virtuální počítače vytvořené pomocí jiných prováděli úkoly
* Každý počítač školení by měly být shodné
* Stážisté můžete rychle zřídit prostředími školení
* Řízení nákladů díky zajištění, že stážisté nelze získat další virtuální počítače, než potřebují pro trénování a také vypnutí virtuálních počítačů, když nejsou na jejich používání
* Snadné sdílení pomocí každý účastník výcviku testovacího prostředí pro školení
* Znovu a znovu a znovu použít testovací prostředí pro školení

V tomto článku se dozvíte o různých funkcí Azure DevTest Labs, které lze použít ke splnění výš požadavkům a podrobné kroky, pomocí kterých můžete nastavit testovací prostředí pro školení.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Provádění trénování s využitím Azure DevTest Labs
1. **Vytvoření testovacího prostředí** 
   
    Praktická cvičení jsou výchozí bod ve službě Azure DevTest Labs. Po vytvoření testovacího prostředí můžete provádět úlohy, jak přidat uživatele (stážisté) do testovacího prostředí, nastavit zásady, které mít náklady pod kontrolou, definovat imagí virtuálních počítačů, které můžete rychle vytvořit a další.   
   
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Vytvoření testovacího prostředí ve službě Azure DevTest Labs](devtest-lab-create-lab.md) |Zjistěte, jak vytvořit testovací prostředí ve službě Azure DevTest Labs na webu Azure Portal. |
2. **Vytvoření virtuálních počítačů školení během pár minut pomocí Image z marketplace s předem připravená a vlastní Image** 
   
    Můžete vybrat předem vytvořené Image z širokou škálu imagí v Tržišti Azure Marketplace a zpřístupnit je pro prováděli úkoly v testovacím prostředí. Pokud předem vytvořené Image nesplňují vaše požadavky, můžete vytvořit vlastní image tak, že vytvoříte virtuální počítač pomocí předem vytvořené image z Azure Marketplace, instalace veškerý software, který budete potřebovat vlastní image v testovacím prostředí pro školení a ukládání virtuálního počítače testovacího prostředí. 
   
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Konfigurace imagí Azure Marketplace](devtest-lab-configure-marketplace-images.md) |Přečtěte si, jak na seznamu povolených IP adres Image Azure Marketplace; zpřístupnění pro výběr pouze obrázky mají pro vzdělávání. |
   | [Vytvoření vlastní image](devtest-lab-create-template.md) |Vlastní image můžete vytvořte před instalací softwaru, které potřebujete pro vzdělávání tak, aby prováděli úkoly můžete rychle vytvořit virtuální počítač pomocí vlastní image. |
3. **Vytvoření opakovaně použitelné šablony pro počítače školení** 
   
    Vzorec ve službě Azure DevTest Labs je seznam hodnot výchozí vlastnost použít k vytvoření virtuálního počítače. Výběrem obrázku, velikost virtuálního počítače (kombinací procesoru a paměti RAM) a virtuální síť můžete vytvořit vzorce v testovacím prostředí. Každý účastník výcviku můžete zobrazit vzorce v testovacím prostředí a použijte ji k vytvoření virtuálního počítače. 
   
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Správa vzorců DevTest Labs k vytvoření virtuálních počítačů](devtest-lab-manage-formulas.md) |Zjistíte, jak můžete vytvořit vzorce výběrem obrázku, velikost virtuálního počítače (kombinací procesoru a paměti RAM) a virtuální síť. |
4. **Náklady na ovládací prvek**
   
    Azure DevTest Labs umožňuje nastavit zásady v testovacím prostředí můžete určit maximální počet virtuálních počítačů, které mohou vytvořit praktikanta v testovacím prostředí. 
   
    Pokud jsou provádění Vícedenní školení a chcete zastavit všechny virtuální počítače v určitém čase dne a automaticky restartuje je následující den, můžete snadno provést tuto akci nastavením automatického vypínání a automatického spuštění zásad v testovacím prostředí. 
   
    Nakonec po dokončení školení odstraněním všech virtuálních počítačů najednou spuštěním jednoho skriptu prostředí PowerShell. 
   
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Definice zásad testovacího prostředí](devtest-lab-set-lab-policy.md) |Řídit náklady nastavením zásad v testovacím prostředí. |
   | [Odstranit všechna prostředí virtuálních počítačů pomocí skriptu prostředí PowerShell](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Odstraňte testovací prostředí v rámci jedné operace po dokončení se školení. |
5. **Každý účastník výcviku nasdílet testovacího prostředí**
   
    Testovací prostředí lze přímo přistupovat pomocí odkazu, který můžete sdílet s vaší prováděli úkoly. Vaše stážisté ani nemusí mít účet Azure, za předpokladu, že mají [účtu Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Stážisté neuvidí virtuální počítače vytvořené pomocí jiných prováděli úkoly.  
   
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Přidat praktikanta do testovacího prostředí ve službě Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Přidat do testovacího prostředí školení prováděli úkoly pomocí webu Azure portal. |
   | [Přidání prováděli úkoly do testovacího prostředí pomocí Powershellového skriptu](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Použití Powershellu k automatizaci přidání prováděli úkoly na vašem testovacím prostředí pro školení. |
   | [Získání odkazu na testovací prostředí](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Zjistěte, jak testovací prostředí je přímo přístupný prostřednictvím hypertextového odkazu. |
6. **Znovu a znovu a znovu použít testovací prostředí** 
   
    Vytvoření testovacího prostředí, vlastní nastavení, třeba tak, že vytvoření šablony Resource Manageru a jeho použití znovu a znovu vytvářet identické testovací prostředí můžete automatizovat. 
   
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Vytvoření testovacího prostředí pomocí šablony Resource Manageru](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Vytvoření testovacích prostředí v Azure DevTest Labs pomocí šablon Resource Manageru. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

