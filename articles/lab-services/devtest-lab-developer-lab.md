---
title: Použití azure devtest labs pro vývojáře | Dokumenty společnosti Microsoft
description: Seznamte se s funkcemi Azure DevTest Labs, které se můžou použít ke splnění požadavků vývojářů, a podrobných krocích, které můžete použít k nastavení testovacího prostředí.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 22e070e5-3d1a-49fe-9d4c-5e07cb0b7fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: ae44696f62a085bf1e798f0915e2bd1e27c3a78f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760143"
---
# <a name="use-azure-devtest-labs-for-developers"></a>Použití azure devtest labs pro vývojáře
Azure DevTest Labs lze použít k implementaci mnoha klíčových scénářů. Jedním z nejzákladnějších je hostování počítačů pro vývojáře. V tomto scénáři poskytuje služba DevTest Labs následující výhody:

- Vývojáři mohou rychle zřídit své vývojové počítače na vyžádání.
- Vývojáři si můžou počítače pro vývoj kdykoli snadno přizpůsobit.
- Správci mohou řídit náklady tím, že zajistí, aby:
  - Vývojáři nemohou získat více virtuálních aplikací, než potřebují pro vývoj.
  - Virtuální virtuální mích se vypínají, když se nepoužívají. 

![Použití DevTest Labs pro školení](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

V tomto článku se dozvíte o různých funkcích Azure DevTest Labs, které lze použít ke splnění požadavků pro vývojáře a podrobné kroky, které můžete postupovat při nastavení testovacího prostředí.

## <a name="implementing-developer-environments-with-azure-devtest-labs"></a>Implementace vývojářských prostředí pomocí Azure DevTest Labs
1. **Vytvoření testovacího prostředí** 
   
    Testovací prostředí jsou výchozím bodem v azure devtest labs. Po vytvoření testovacího prostředí můžete provádět úkoly, jako je přidávání uživatelů (vývojářů) do testovacího prostředí, nastavení zásad pro řízení nákladů, definování ibi obrazových kopií virtuálních počítačů, které lze rychle vytvořit, a další.  
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Vytvoření testovacího prostředí v Azure DevTest Labs](devtest-lab-create-lab.md) |Zjistěte, jak vytvořit testovací prostředí v Azure DevTest Labs na webu Azure Portal. |
2. **Vytváření virtuálních měn během několika minut pomocí hotových obrázků na trhu a vlastních bitových kopií** 
   
    Můžete si vybrat hotové image z široké škály ibi na Azure Marketplace a zpřístupnit je v testovacím prostředí. Pokud hotové image nesplňují vaše požadavky, můžete vytvořit vlastní image vytvořením virtuálního počítače testovacího prostředí pomocí hotové image z Azure Marketplace, instalací veškerého softwaru, který potřebujete, a uložením virtuálního počítače jako vlastní image v testovacím prostředí.

    Pokud budete používat vlastní obrázky, zvažte použití továrny obrázků k vytvoření a distribuci obrázků. Továrna bitových kopií je řešení konfigurace jako kódu, které pravidelně sestavuje a distribuuje nakonfigurované bitové kopie automaticky. To šetří čas potřebný k ruční konfiguraci systému po vytvoření virtuálního počítače se základním osem.
  
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Konfigurace imagí na Azure Marketplace](devtest-lab-configure-marketplace-images.md) |Zjistěte, jak můžete whitelist Image Azure Marketplace, takže k dispozici pro výběr pouze image, které chcete pro vývojáře.|
   | [Vytvoření vlastní image](devtest-lab-create-template.md) |Vytvořte vlastní bitovou kopii předinstalací softwaru, který potřebujete, aby vývojáři mohli rychle vytvořit virtuální hod pomocí vlastní image.|
   | [Další informace o továrně obrázků](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |Podívejte se na video, které popisuje, jak nastavit a používat továrnu na obrázky.|

3. **Vytváření opakovaně použitelných šablon pro vývojářské počítače** 
   
    Vzorec v Azure DevTest Labs je seznam výchozích hodnot vlastností, které se používají k vytvoření virtuálního počítače. Vzorec můžete vytvořit v testovacím prostředí výběrem bitové kopie, velikosti virtuálního počítače (kombinace procesoru a paměti RAM) a virtuální sítě. Každý vývojář může zobrazit vzorec v testovacím prostředí a použít ho k vytvoření virtuálního virtuálního soudu. 
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Správa vzorců DevTest Labs za účelem vytvoření virtuálních měn](devtest-lab-manage-formulas.md) |Zjistěte, jak můžete vytvořit vzorec tak, že zvednete bitovou kopii, velikost virtuálního počítače (kombinaci procesoru a paměti RAM) a virtuální síť.|

4. **Vytvoření artefaktů umožňujících flexibilní přizpůsobení virtuálních počítačů**

   Artefakty se používají k nasazení a konfiguraci aplikace po zřízení virtuálního počítače. Artefakty mohou být:

   - Nástroje, které chcete nainstalovat na virtuální počítač – například agenti, šumava a Visual Studio.
   - Akce, které chcete spustit na virtuálním počítači – například klonování repo.
   - Aplikace, které chcete testovat.

   Mnoho artefaktů je již k dispozici iout-of-the-box. Pokud chcete více přizpůsobení pro vaše specifické potřeby, můžete vytvořit vlastní artefakty.

   Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Vytvoření vlastních artefaktů pro váš virtuální počítač DevTest Labs](devtest-lab-artifact-author.md) |Vytvořte si vlastní artefakty pro virtuální počítače ve vašem testovacím prostředí.|
   | [Přidání úložiště Git pro ukládání vlastních artefaktů a šablon Azure Resource Managerpro použití v laboratořích Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Přečtěte si, jak ukládat vlastní artefakty do vlastního úložiště Git.|

5. **Náklady na kontrolu**
   
    Azure DevTest Labs umožňuje nastavit zásady v testovacím prostředí k určení maximální počet virtuálních počítačů, které mohou být vytvořeny vývojářv testovacím prostředí. 
   
    Pokud váš vývojářský tým má nastavený pracovní plán a chcete zastavit všechny virtuální počítače v určitou denní dobu a pak je automaticky restartovat následující den, můžete to snadno provést nastavením zásad automatického vypnutí a automatického spuštění v testovacím prostředí. 
   
    Nakonec po dokončení vývoje aplikací můžete odstranit všechny virtuální aplikace najednou spuštěním jednoho skriptu prostředí PowerShell. 
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Definice zásad testovacího prostředí](devtest-lab-set-lab-policy.md) |Řízení nákladů nastavením zásad v testovacím prostředí. |
   | [Odstranění všech virtuálních proudů testovacího prostředí pomocí skriptu PowerShellu](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Po dokončení vývoje odstraňte všechna testovací prostředí v jedné operaci.|

1. **Přidání virtuální sítě k virtuálnímu počítači** 
   
    DevTest Labs vytvoří novou virtuální síť (VNET) při každém vytvoření testovacího prostředí. Pokud jste nakonfigurovali vlastní virtuální síť – například pomocí ExpressRoute nebo VPN site-to-site – můžete tuto virtuální síť přidat do nastavení virtuální sítě testovacího prostředí tak, aby byla dostupná při vytváření virtuálních počítačů.

    Kromě toho je k dispozici artefakt spojení domény Azure Active Directory, který připojí virtuální počítač k doméně při vytváření virtuálního počítače. 
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Konfigurace virtuální sítě v Azure DevTest Labs](devtest-lab-configure-vnet.md) |Zjistěte, jak nakonfigurovat virtuální síť v Azure DevTest Labs pomocí portálu Azure.|

6. **Sdílení testovacího prostředí s každým vývojářem**
   
    K testovacím prostředím lze přistupovat přímo pomocí odkazu, který sdílíte s vývojáři. Ani nemusí mít účet Azure, pokud mají [účet Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Vývojáři nemohou zobrazit virtuální chod vytvořené jinými vývojáři.  
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Přidání vývojáře do testovacího prostředí v Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Pomocí portálu Azure přidejte vývojáře do testovacího prostředí.|
   | [Přidání vývojářů do testovacího prostředí pomocí skriptu PowerShellu](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Pomocí Prostředí PowerShell můžete automatizovat přidávání vývojářů do testovacího prostředí. |
   | [Získání odkazu na laboratoř](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Zjistěte, jak mohou vývojáři přímo přistupovat k testovacímu prostředí prostřednictvím hypertextového odkazu.|

7. **Automatizace vytváření testovacích prostředí pro další týmy** 
   
    Vytvoření testovacího prostředí, včetně vlastních nastavení, můžete automatizovat vytvořením šablony Správce prostředků a jejím použitím k vytváření identických testovacích prostředí znovu a znovu. 
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Vytvoření testovacího prostředí pomocí šablony Správce prostředků](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Vytvářejte testovací prostředí v laboratořích Azure DevTest Labs pomocí šablon Správce prostředků. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

