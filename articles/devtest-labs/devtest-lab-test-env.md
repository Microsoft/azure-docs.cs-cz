---
title: Použití Azure DevTest Labs pro testovací prostředí virtuálních počítačů a PaaS | Microsoft Docs
description: Naučte se používat Azure DevTest Labs scénářů testovacího prostředí pro virtuální počítače a PaaS.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 978071aa160098612a5effebfb30d5d58b777df7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89299826"
---
# <a name="use-azure-devtest-labs-for-vm-and-paas-test-environments"></a>Použití Azure DevTest Labs pro testovací prostředí virtuálních počítačů a PaaS

Azure DevTest Labs můžete použít k implementaci mnoha klíčových scénářů. Jedním z nejzákladnějších je hostování počítačů pro testery. 

V tomto scénáři poskytuje služba DevTest Labs následující výhody:

- Rychlé zřizování prostředí Windows nebo Linuxu pomocí opakovaně použitelných šablon a artefaktů umožňuje testerům testovat nejnovější verze jejich aplikací.
- Testeři můžou škálovat zátěžové testy zřízením více testovacích agentů.
- Správci mohou řídit náklady tím, že zajistí, že:
  - Testeri nemohou získat více virtuálních počítačů, než potřebují.
  - Virtuální počítače jsou vypnuté, když se nepoužívají.

![Použití DevTest Labs pro školení](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

V tomto článku se dozvíte o různých funkcích Azure DevTest Labs používaných ke splnění požadavků na testery a podrobných kroků, které je potřeba provést při nastavování testovacího prostředí.

## <a name="implementing-test-environments-with-azure-devtest-labs"></a>Implementace testovacích prostředí pomocí Azure DevTest Labs
1. **Vytvoření testovacího prostředí** 
   
    Laboratoře jsou výchozím bodem v Azure DevTest Labs. Po vytvoření testovacího prostředí můžete provádět úlohy, jako je přidání uživatelů (testerů) do testovacího prostředí, nastavení zásad pro řízení nákladů a definování imagí virtuálních počítačů, které můžou rychle vytvořit a další.  
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úloha | Co se naučíte |
   | --- | --- |
   | [Vytvoření testovacího prostředí v Azure DevTest Labs](devtest-lab-create-lab.md) |Naučte se, jak vytvořit testovací prostředí v Azure DevTest Labs Azure Portal. |
2. **Vytváření virtuálních počítačů během několika minut pomocí připravených imagí a vlastních imagí z Marketplace** 
   
    V Azure Marketplace si můžete vybrat připravené image z nejrůznějších imagí a zpřístupnit je v testovacím prostředí. Pokud připravené image nesplňují vaše požadavky, můžete vytvořit vlastní image vytvořením testovacího virtuálního počítače pomocí předem připraveného obrázku z Azure Marketplace, instalací veškerého softwaru, který potřebujete, a uložením virtuálního počítače jako vlastního obrazu v testovacím prostředí.

    Pokud budete používat vlastní image, zvažte vytvoření a distribuci imagí pomocí objektu pro vytváření imagí. Objekt pro vytváření imagí je řešení pro konfiguraci, které pravidelně vytváří a distribuuje nakonfigurované image automaticky. Tím ušetříte čas potřebný k ruční konfiguraci systému po vytvoření virtuálního počítače se základním operačním systémem.
  
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úloha | Co se naučíte |
   | --- | --- |
   | [Konfigurace imagí na Azure Marketplace](devtest-lab-configure-marketplace-images.md) |Přečtěte si, jak můžete Azure Marketplace imagí použít a zpřístupnit pro výběr pouze imagí, které chcete pro testery.|
   | [Vytvoření vlastní image](devtest-lab-create-template.md) |Vytvořte vlastní image tak, že předem nainstalujete potřebný software, aby testeri mohli rychle vytvořit virtuální počítač pomocí vlastní image.|
   | [Informace o objektu pro vytváření imagí](./devtest-lab-faq.md#blog-post) |Podívejte se na video, které popisuje, jak nastavit a použít objekt pro vytváření imagí.|

3. **Vytváření opakovaně použitelných šablon pro testovací počítače** 
   
    Vzorec v Azure DevTest Labs je seznam výchozích hodnot vlastností, které se používají k vytvoření virtuálního počítače. Vzorec můžete v testovacím prostředí vytvořit tak, že vybíráte image, velikost virtuálního počítače (kombinaci procesoru a paměti RAM) a virtuální síť. Každý tester může zobrazit vzorec v testovacím prostředí a použít ho k vytvoření virtuálního počítače. 
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úloha | Co se naučíte |
   | --- | --- |
   | [Správa vzorců DevTest Labs pro vytváření virtuálních počítačů](devtest-lab-manage-formulas.md) |Zjistěte, jak můžete vytvořit vzorec vyzvednutím image, velikosti virtuálního počítače (kombinace CPU a paměti RAM) a virtuální sítě.|

3. **Vytváření testovacích prostředí pro více virtuálních počítačů** 
   
    Šablony Azure Resource Manager můžete použít k definování infrastruktury a konfigurace řešení Azure a opakovanému nasazení několika testovacích virtuálních počítačů v konzistentním stavu.

    Prostředky Azure PaaS se dají zřídit v prostředí ze šablony Správce prostředků a zobrazí se při sledování nákladů. Automatické vypnutí virtuálního počítače ale neplatí pro prostředky PaaS.

    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úloha | Co se naučíte |
   | --- | --- |
   | [Vytvoření prostředí více virtuálních počítačů a prostředků PaaS pomocí šablony Azure Resource Manageru](devtest-lab-create-environment-from-arm.md) |Přečtěte si, jak můžete nasadit více virtuálních počítačů v konzistentním stavu pro vaše testovací prostředí.|

4. **Vytvoření artefaktů pro povolení flexibilního přizpůsobení virtuálního počítače**

   Artefakty slouží k nasazení a konfiguraci aplikace po zřízení virtuálního počítače. Artefakty mohou být:

   - Nástroje, které chcete na virtuálním počítači nainstalovat – například agenti, Fiddler a Visual Studio.
   - Akce, které chcete na virtuálním počítači spustit – například klonování úložiště.
   - Aplikace, které chcete testovat.

   Mnoho artefaktů je již k dispozici předem. Pokud ale chcete další přizpůsobení pro konkrétní potřeby, můžete vytvořit vlastní artefakty.

   Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úloha | Co se naučíte |
   | --- | --- |
   | [Vytvoření vlastních artefaktů pro virtuální počítač s DevTest Labs](devtest-lab-artifact-author.md) |Vytvořte vlastní artefakty pro virtuální počítače v testovacím prostředí.|
   | [Přidání úložiště Git pro ukládání vlastních artefaktů a šablon Azure Resource Manager pro použití v Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Naučte se ukládat vlastní artefakty ve vlastním privátním úložišti Git.|

5. **Náklady pod kontrolou**
   
    Azure DevTest Labs umožňuje nastavit v testovacím prostředí zásadu, která určuje maximální počet virtuálních počítačů, které mohou být vytvořeny testerem v testovacím prostředí. 
   
    Pokud má váš testovací tým nastavený pracovní plán a chcete zastavit všechny virtuální počítače v konkrétní denní době a pak je automaticky restartovat v následujícím dni, můžete to provést tak, že nastavíte automatické vypínání a automaticky spouštěné zásady v testovacím prostředí. 
   
    Nakonec, až se vývoj aplikací dokončí, můžete odstranit všechny virtuální počítače najednou spuštěním jednoho skriptu PowerShellu. 
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úloha | Co se naučíte |
   | --- | --- |
   | [Definice zásad testovacího prostředí](devtest-lab-set-lab-policy.md) |Řízení nákladů pomocí nastavení zásad v testovacím prostředí. |
   | [Odstranění všech virtuálních počítačů testovacího prostředí pomocí skriptu PowerShellu](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Až se testování dokončí, odstraňte všechny cvičení v jedné operaci.|

1. **Přidání virtuální sítě do testovacího prostředí** 
   
    DevTest Labs vytváří novou virtuální síť (VNET) při každém vytvoření testovacího prostředí. Pokud jste si nakonfigurovali vlastní virtuální síť – třeba pomocí ExpressRoute nebo VPN typu Site-to-site – můžete tuto virtuální síť přidat do nastavení virtuální sítě v testovacím prostředí, aby byla dostupná při vytváření virtuálních počítačů.

    Kromě toho je k dispozici artefakt připojení Azure Active Directory domény, který při vytváření virtuálního počítače připojí virtuální počítač k doméně. 
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úloha | Co se naučíte |
   | --- | --- |
   | [Konfigurace virtuální sítě v Azure DevTest Labs](devtest-lab-configure-vnet.md) |Naučte se konfigurovat virtuální síť v Azure DevTest Labs pomocí Azure Portal.|

6. **Sdílet testovací prostředí s každým testerem**
   
    K testovacím prostředím se dá přímo získat přímý odkaz pomocí odkazu, který sdílíte s testery. Nemusí mít ani účet Azure, pokud mají [účet Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Testeri nemohou zobrazit virtuální počítače vytvořené jinými testery.  
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úloha | Co se naučíte |
   | --- | --- |
   | [Přidat testera do testovacího prostředí v Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Pomocí Azure Portal přidejte testery do testovacího prostředí.|
   | [Přidání testerů do testovacího prostředí pomocí skriptu PowerShellu](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |K automatizaci přidávání testerů do testovacího prostředí použijte PowerShell. |
   | [Získat odkaz na testovací prostředí](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Přečtěte si, jak můžou testeri přímo přistupovat k testovacímu prostředí prostřednictvím hypertextového odkazu.|

7. **Automatizace vytváření testovacího prostředí pro více týmů** 
   
    Vytváření testovacích prostředí, včetně vlastních nastavení, můžete automatizovat vytvořením šablony Správce prostředků a jejím použitím pro opětovné vytvoření identické laboratoře. 
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úloha | Co se naučíte |
   | --- | --- |
   | [Vytvoření testovacího prostředí pomocí šablony Správce prostředků](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Pomocí šablon Správce prostředků vytvořte cvičení v Azure DevTest Labs. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
