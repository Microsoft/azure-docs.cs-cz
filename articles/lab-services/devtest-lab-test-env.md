---
title: Použití testovacích prostředí Azure DevTest Labs pro testovací prostředí virtuálních počítačů a PaaS | Dokumenty společnosti Microsoft
description: Zjistěte, jak používat Azure DevTest Labs pro scénáře testovacího prostředí virtuálních počítače a PaaS.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: d4e2c334-643a-40c9-9051-625b8f39fc86
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: c6b458091a8e5e22cca55d401e89e5e13bcf9de9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60623170"
---
# <a name="use-azure-devtest-labs-for-vm-and-paas-test-environments"></a>Použití testovacích prostředí Azure DevTest Labs pro testovací prostředí virtuálních počítačů a PaaS

Azure DevTest Labs můžete použít k implementaci mnoha klíčových scénářů. Jedním z nejzákladnějších je hostování počítačů pro testery. 

V tomto scénáři poskytuje služba DevTest Labs následující výhody:

- Rychlé zřizování prostředí Windows nebo Linuxu pomocí opakovaně použitelných šablon a artefaktů umožňuje testerům testovat nejnovější verze jejich aplikací.
- Testeři můžou škálovat zátěžové testy zřízením více testovacích agentů.
- Správci mohou řídit náklady tím, že zajistí, aby:
  - Testeři nemohou získat více virtuálních virtuálních discích, než potřebují.
  - Virtuální virtuální mích se vypínají, když se nepoužívají.

![Použití DevTest Labs pro školení](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

V tomto článku se dozvíte o různých funkcích Azure DevTest Labs, které slouží ke splnění požadavků testeru a podrobných kroků, které je třeba provést při nastavení testovacího prostředí.

## <a name="implementing-test-environments-with-azure-devtest-labs"></a>Implementace testovacích prostředí pomocí azure devtest labs
1. **Vytvoření testovacího prostředí** 
   
    Testovací prostředí jsou výchozím bodem v azure devtest labs. Po vytvoření testovacího prostředí můžete provádět úkoly, jako je přidávání uživatelů (testerů) do testovacího prostředí, nastavení zásad pro řízení nákladů, definování ibi obrazových kopií virtuálních počítačů, které lze rychle vytvořit, a další.  
   
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
   | [Konfigurace imagí na Azure Marketplace](devtest-lab-configure-marketplace-images.md) |Zjistěte, jak můžete whitelist Image Azure Marketplace, zpřístupnění pro výběr pouze image, které chcete pro testery.|
   | [Vytvoření vlastní image](devtest-lab-create-template.md) |Vytvořte vlastní bitovou kopii předinstalací softwaru, který potřebujete, aby testeři mohli rychle vytvořit virtuální hod pomocí vlastní image.|
   | [Další informace o továrně obrázků](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |Podívejte se na video, které popisuje, jak nastavit a používat továrnu na obrázky.|

3. **Vytvoření opakovaně použitelných šablon pro testovací stroje** 
   
    Vzorec v Azure DevTest Labs je seznam výchozích hodnot vlastností, které se používají k vytvoření virtuálního počítače. Vzorec můžete vytvořit v testovacím prostředí výběrem bitové kopie, velikosti virtuálního počítače (kombinace procesoru a paměti RAM) a virtuální sítě. Každý tester může zobrazit vzorec v testovacím prostředí a použít ho k vytvoření virtuálního virtuálního soudu. 
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Správa vzorců DevTest Labs za účelem vytvoření virtuálních měn](devtest-lab-manage-formulas.md) |Zjistěte, jak můžete vytvořit vzorec tak, že zvednete bitovou kopii, velikost virtuálního počítače (kombinaci procesoru a paměti RAM) a virtuální síť.|

3. **Vytvoření testovacích prostředí pro více virtuálních virtuálních mích** 
   
    Pomocí šablon Azure Resource Manager můžete definovat infrastrukturu a konfiguraci vašeho řešení Azure a opakovaně nasazovat více testovacích virtuálních počítačů v konzistentním stavu.

    Prostředky Azure PaaS lze zřídit v prostředí ze šablony Správce prostředků a zobrazit ve sledování nákladů. Automatické vypnutí virtuálního počítače se však nevztahuje na prostředky PaaS.

    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Vytvoření prostředí více virtuálních počítačů a prostředků PaaS pomocí šablony Azure Resource Manageru](devtest-lab-create-environment-from-arm.md) |Zjistěte, jak můžete nasadit více virtuálních počítačů v konzistentním stavu pro testovací prostředí.|

4. **Vytvoření artefaktů umožňujících flexibilní přizpůsobení virtuálních počítačů**

   Artefakty se používají k nasazení a konfiguraci aplikace po zřízení virtuálního počítače. Artefakty mohou být:

   - Nástroje, které chcete nainstalovat na virtuální počítač – například agenti, šumava a Visual Studio.
   - Akce, které chcete spustit na virtuálním počítači – například klonování repo.
   - Aplikace, které chcete testovat.

   Mnoho artefaktů je již k dispozici iout-of-the-box. Ale pokud chcete více přizpůsobení pro vaše konkrétní potřeby, můžete vytvořit vlastní artefakty.

   Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Vytvoření vlastních artefaktů pro váš virtuální počítač DevTest Labs](devtest-lab-artifact-author.md) |Vytvořte si vlastní artefakty pro virtuální počítače ve vašem testovacím prostředí.|
   | [Přidání úložiště Git pro ukládání vlastních artefaktů a šablon Azure Resource Managerpro použití v laboratořích Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Přečtěte si, jak ukládat vlastní artefakty do vlastního úložiště Git.|

5. **Náklady na kontrolu**
   
    Azure DevTest Labs umožňuje nastavit zásady v testovacím prostředí k určení maximální počet virtuálních počítačů, které mohou být vytvořeny tester v testovacím prostředí. 
   
    Pokud váš testovací tým má nastavený pracovní plán a chcete zastavit všechny virtuální počítače v určitou denní dobu a pak je automaticky restartovat následující den, můžete to snadno provést nastavením zásad automatického vypnutí a automatického spuštění v testovacím prostředí. 
   
    Nakonec po dokončení vývoje aplikací můžete odstranit všechny virtuální aplikace najednou spuštěním jednoho skriptu prostředí PowerShell. 
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Definice zásad testovacího prostředí](devtest-lab-set-lab-policy.md) |Řízení nákladů nastavením zásad v testovacím prostředí. |
   | [Odstranění všech virtuálních proudů testovacího prostředí pomocí skriptu PowerShellu](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Po dokončení testování odstraňte všechna testovací prostředí v jedné operaci.|

1. **Přidání virtuální sítě do testovacího prostředí** 
   
    DevTest Labs vytvoří novou virtuální síť (VNET) při každém vytvoření testovacího prostředí. Pokud jste nakonfigurovali vlastní virtuální síť – například pomocí ExpressRoute nebo VPN site-to-site – můžete tuto virtuální síť přidat do nastavení virtuální sítě testovacího prostředí tak, aby byla dostupná při vytváření virtuálních počítačů.

    Kromě toho je k dispozici artefakt spojení domény Azure Active Directory, který připojí virtuální počítač k doméně při vytváření virtuálního počítače. 
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Konfigurace virtuální sítě v Azure DevTest Labs](devtest-lab-configure-vnet.md) |Zjistěte, jak nakonfigurovat virtuální síť v Azure DevTest Labs pomocí portálu Azure.|

6. **Sdílení testovacího prostředí s každým testerem**
   
    K testovacím laboratořím lze přímo přistupovat pomocí odkazu, který sdílíte se svými testery. Ani nemusí mít účet Azure, pokud mají [účet Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Testeři nevidí virtuální chod vytvořené jinými testery.  
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Přidání testeru do testovacího prostředí v azure devtest labs](devtest-lab-add-devtest-user.md) |Pomocí portálu Azure přidejte testery do testovacího prostředí.|
   | [Přidání testerů do testovacího prostředí pomocí skriptu PowerShellu](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Pomocí prostředí PowerShell můžete automatizovat přidávání testerů do testovacího prostředí. |
   | [Získání odkazu na laboratoř](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Zjistěte, jak mohou testeři přímo přistupovat k testovacímu prostředí prostřednictvím hypertextového odkazu.|

7. **Automatizace vytváření testovacích prostředí pro další týmy** 
   
    Vytvoření testovacího prostředí, včetně vlastních nastavení, můžete automatizovat vytvořením šablony Správce prostředků a jejím použitím k vytváření identických testovacích prostředí znovu a znovu. 
   
    Další informace získáte kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Vytvoření testovacího prostředí pomocí šablony Správce prostředků](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Vytvářejte testovací prostředí v laboratořích Azure DevTest Labs pomocí šablon Správce prostředků. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

