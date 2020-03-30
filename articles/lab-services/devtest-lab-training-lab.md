---
title: Použití azure devtest labs pro školení | Dokumenty společnosti Microsoft
description: Tento článek obsahuje podrobné kroky, které můžete postupovat podle nastavení testovacího prostředí pro školení v Azure DevTest Labs.
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 8f0a930d6e3c04548ade71f6d4e3294114eb60a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759973"
---
# <a name="use-azure-devtest-labs-for-training"></a>Použití azure devtest labs pro školení
Azure DevTest Labs se dá použít k implementaci mnoha klíčových scénářů kromě vývoji a testování. Jedním z těchto scénářů je nastavení testovacího prostředí pro školení. Azure DevTest Labs umožňuje vytvořit testovací prostředí, kde můžete poskytnout vlastní šablony, které každý stážista můžete použít k vytvoření identické a izolované prostředí pro školení. Můžete použít zásady, které zajistí, že vzdělávací prostředí jsou k dispozici pro každého stážistu pouze v případě, že je potřebují a obsahují dostatek zdrojů - jako jsou virtuální počítače - potřebné pro školení. Nakonec můžete snadno sdílet laboratoř se stážisty, ke kterým mají přístup jedním kliknutím.

![Použití DevTest Labs pro školení](./media/devtest-lab-training-lab/devtest-lab-training.png)

Azure DevTest Labs splňuje následující požadavky, které jsou nutné k provádění školení v libovolném virtuálním prostředí: 

* Stážisté nevidí virtuální chod vytvořené jinými stážisty.
* Každý tréninkový stroj by měl být totožný
* Stážisté mohou rychle zařídit své vzdělávací prostředí
* Řízení nákladů tím, že zajistí, že stážisté nemůže získat více virtuálních počítače, než potřebují pro školení a také vypnutí virtuálních počítače, když je nepoužíváte
* Snadno sdílejte tréninkovou laboratoř s každým stážistou
* Znovu a znovu použít tréninkovou laboratoř

V tomto článku se dozvíte o různých funkcích Azure DevTest Labs, které lze použít ke splnění dříve popsaných požadavků na školení a podrobné kroky, které můžete provést při nastavení testovacího prostředí pro školení.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Implementace školení s Azure DevTest Labs
1. **Vytvoření testovacího prostředí** 
   
    Testovací prostředí jsou výchozím bodem v azure devtest labs. Po vytvoření testovacího prostředí můžete provádět úkoly, jako je přidání uživatelů (stážistů) do testovacího prostředí, nastavení zásad pro řízení nákladů, definování ibi obrazů virtuálních počítačů, které se dají rychle vytvářet, a další.   
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Vytvoření testovacího prostředí v Azure DevTest Labs](devtest-lab-create-lab.md) |Zjistěte, jak vytvořit testovací prostředí v Azure DevTest Labs na webu Azure Portal. |
2. **Vytváření školicích virtuálních realit během několika minut pomocí hotových obrázků na trhu a vlastních bitových kopií** 
   
    Na Azure Marketplace si můžete vybrat hotové image z široké škály ibi a zpřístupnit je stážistům v testovacím prostředí. Pokud hotové image nesplňují vaše požadavky, můžete vytvořit vlastní image vytvořením virtuálního počítače testovacího prostředí pomocí hotové image z Azure Marketplace, instalací veškerého softwaru, který potřebujete pro školení a uložením virtuálního počítače jako vlastní image v testovacím prostředí. 
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Konfigurace imagí na Azure Marketplace](devtest-lab-configure-marketplace-images.md) |Zjistěte, jak můžete naseznamovat image Azure Marketplace na seznamu povolených; zpřístupnění pro výběr pouze obrázky, které chcete pro školení. |
   | [Vytvoření vlastní image](devtest-lab-create-template.md) |Vytvořte vlastní image předinstalací softwaru, který potřebujete pro školení, aby stážisté mohli rychle vytvořit virtuální hod pomocí vlastní image. |
3. **Vytvoření opakovaně použitelných šablon pro školicí stroje** 
   
    Vzorec v Azure DevTest Labs je seznam výchozích hodnot vlastností, které se používají k vytvoření virtuálního počítače. Vzorec můžete vytvořit v testovacím prostředí výběrem bitové kopie, velikosti virtuálního počítače (kombinace procesoru a paměti RAM) a virtuální sítě. Každý stážista může zobrazit vzorec v testovacím prostředí a použít ho k vytvoření virtuálního virtuálního soudu. 
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Správa vzorců DevTest Labs za účelem vytvoření virtuálních měn](devtest-lab-manage-formulas.md) |Zjistěte, jak můžete vytvořit vzorec tak, že zvednete bitovou kopii, velikost virtuálního počítače (kombinaci procesoru a paměti RAM) a virtuální síť. |
4. **Náklady na kontrolu**
   
    Azure DevTest Labs umožňuje nastavit zásady v testovacím prostředí k určení maximální počet virtuálních počítačů, které mohou být vytvořeny stážista v testovacím prostředí. 
   
    Pokud provádíte vícedenní školení a chcete zastavit všechny virtuální počítače v určitou denní dobu a pak je automaticky restartovat následující den, můžete to snadno provést nastavením zásad automatického vypnutí a automatického spuštění v testovacím prostředí. 
   
    Nakonec po dokončení trénování můžete odstranit všechny virtuální chod najednou spuštěním jednoho skriptu prostředí PowerShell. 
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Definice zásad testovacího prostředí](devtest-lab-set-lab-policy.md) |Řízení nákladů nastavením zásad v testovacím prostředí. |
   | [Odstranění všech virtuálních proudů testovacího prostředí pomocí skriptu PowerShellu](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Po dokončení školení odstraňte všechna testovací prostředí v jedné operaci. |
5. **Sdílejte laboratoř s každým stážistou**
   
    K laboratořím lze přímo přistupovat pomocí odkazu, který sdílíte se svými stážisty. Vaši stážisté ani nemusí mít účet Azure, pokud mají [účet Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Stážisté nevidí virtuální chod vytvořené jinými stážisty.  
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Přidání stážisty do testovacího prostředí v azure devtest labs](devtest-lab-add-devtest-user.md) |Pomocí portálu Azure přidejte stážisty do tréninkové laboratoře. |
   | [Přidání stážistů do testovacího prostředí pomocí skriptu PowerShellu](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Pomocí Prostředí PowerShell můžete automatizovat přidávání stážistů do tréninkové laboratoře. |
   | [Získání odkazu na laboratoř](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Zjistěte, jak lze k testovacímu prostředí přímo přistupovat prostřednictvím hypertextového odkazu. |
6. **Znovu a znovu použít laboratoř** 
   
    Vytvoření testovacího prostředí, včetně vlastních nastavení, můžete automatizovat vytvořením šablony Správce prostředků a jejím použitím k vytváření identických testovacích prostředí znovu a znovu. 
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Vytvoření testovacího prostředí pomocí šablony Správce prostředků](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Vytvářejte testovací prostředí v laboratořích Azure DevTest Labs pomocí šablon Správce prostředků. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

