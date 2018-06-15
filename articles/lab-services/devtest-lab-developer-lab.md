---
title: Použití Azure DevTest Labs pro vývojáře | Microsoft Docs
description: Naučte se používat Azure DevTest Labs pro vývojáře scénáře.
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 96432abe619ea23c1a06735567d00660e5430550
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787560"
---
# <a name="use-azure-devtest-labs-for-developers"></a>Použití Azure DevTest Labs pro vývojáře
Azure DevTest Labs lze použít k implementaci mnoho klíčových scénářů, ale jeden primární scénářů, která využívá DevTest Labs hostovat vývoj počítače pro vývojáře. V tomto scénáři DevTest Labs poskytuje následující výhody:

- Vývojáři mohou rychle zřizovat jejich vývoj počítačů na vyžádání.
- Vývojáři mohou snadno přizpůsobit své počítače vývoj vždy, když je potřeba.
- Správci můžou řídit náklady, přičemž zajistí, aby:
  - Vývojáři nelze získat více virtuálních počítačů, než potřebují pro vývoj.
  - Virtuální počítače jsou vypnutí při není používán. 

![Používá DevTest Labs pro školení](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

V tomto článku se dozvíte o různých funkcí Azure DevTest Labs, které lze použít ke splnění požadavků na vývojáře a podrobné kroky, které vám pomůžou nastavit testovací prostředí.

## <a name="implementing-developer-environments-with-azure-devtest-labs"></a>Implementace vývojáře prostředí s Azure DevTest Labs
1. **Vytvořit testovací prostředí** 
   
    Labs jsou výchozím bodem v Azure DevTest Labs. Po vytvoření testovacího prostředí, můžete provádět úlohy, jako je například přidávání uživatelů (vývojáři) do testovacího prostředí, nastavení zásad pro řízení nákladů, definování Image virtuálních počítačů, které můžete rychle vytvořit a další.  
   
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Vytvoření testovacího prostředí v Azure DevTest Labs](devtest-lab-create-lab.md) |Postup vytvoření testovacího prostředí v Azure DevTest Labs na portálu Azure. |
2. **Vytvořit virtuální počítače během pár minut pomocí připravených marketplace Image a vlastních bitových kopií** 
   
    Můžete vybrat připravených bitové kopie z široké škály bitové kopie v Azure Marketplace a zpřístupnit v testovacím prostředí. Jestliže připravených bitové kopie nevyhovují vašim požadavkům, můžete vytvořit vlastní image vytvořením testovacího prostředí virtuálních počítačů pomocí připravených bitové kopie z Azure Marketplace, veškerý software, který budete potřebovat, instalace a uložení virtuálního počítače jako vlastní image v testovacím prostředí.

    Pokud budete používat vlastní Image, zvažte použití objekt pro vytváření bitové kopie k vytvoření a distribuce obrázků. Objekt pro vytváření bitové kopie je jako kód konfigurace řešení, které pravidelně sestavení a automaticky distribuuje nakonfigurované obrázků. To umožňuje ušetřit čas potřebný k ručně konfigurovat systém po vytvoření virtuálního počítače se základní operačního systému.
  
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Konfigurace Azure Marketplace obrázků](devtest-lab-configure-marketplace-images.md) |Přečtěte si, jak povolených Image Azure Marketplace, zpřístupnění pro výběr pouze obrázky, které chcete použít pro vývojáři.|
   | [Vytvořit vlastní image](devtest-lab-create-template.md) |Vytvořte vlastní image před instalací softwaru, které potřebujete, aby vývojáři mohou rychle vytvořit virtuální počítač pomocí vlastní image.|
   | [Další informace o vytváření bitové kopie](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |Přehrát video, které popisuje, jak nastavit a používat objekt pro vytváření bitové kopie.|

3. **Vytváření opakovaně použitelných šablon pro vývojáře počítače** 
   
    Vzorec v Azure DevTest Labs je seznam výchozí hodnoty vlastností použít k vytvoření virtuálního počítače. Vzorec v testovacím prostředí vytvoříte výběr obrázku, velikost virtuálního počítače (kombinaci procesor a paměť RAM) a virtuální sítě. Každý vývojář může zobrazit vzorec v testovacím prostředí a ji použít k vytvoření virtuálního počítače. 
   
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Spravovat DevTest Labs vzorce pro vytvoření virtuálních počítačů](devtest-lab-manage-formulas.md) |Zjistěte, jak můžete vytvořit vzorec podle vyzvednutí obrázku, velikost virtuálního počítače (kombinaci procesor a paměť RAM) a virtuální sítě.|

4. **Vytvoří artefakty za účelem flexibilní přizpůsobení virtuálního počítače**

   Artefakty slouží k nasazení a konfiguraci aplikace po zřízení virtuálního počítače. Artefakty mohou být:

   - Nástroje, které chcete nainstalovat do virtuálního počítače – například agentů, Fiddler a Visual Studio.
   - Akce, které chcete spustit na virtuálním počítači – například klonování úložišti.
   - Aplikace, které chcete otestovat

   Mnoho artefakty je již k dispozici out-of-the-box. Pokud chcete další přizpůsobení svých konkrétních potřeb můžete vytvořit vlastní artefakty.

   Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Vytvoření vlastních artefaktů pro virtuální počítač DevTest Labs](devtest-lab-artifact-author.md) |Vytvoření vlastních artefaktů pro virtuální počítače ve vašem testovacím prostředí.|
   | [Přidejte úložiště Git pro uložení vlastních artefaktů a šablon Azure Resource Manageru pro použití v Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Zjistěte, jak k uložení vlastních artefaktů v vlastní privátní úložiště Git.|

5. **Náklady na ovládací prvek**
   
    Azure DevTest Labs umožňuje nastavit zásady v testovacím prostředí, chcete-li určit maximální počet virtuálních počítačů, které lze vytvořit pomocí vývojář v testovacím prostředí. 
   
    Pokud má váš tým vývojáře sady pracovní plán a chcete zastavit všechny virtuální počítače v určitém čase dne a poté automaticky restartovat je následující den, který lze snadno provádět nastavením zásad, automatické ukončení a automaticky spouštěná v testovacím prostředí. 
   
    Nakonec po dokončení vývoj aplikací můžete odstranit všechny virtuální počítače najednou spuštěním jednoho skriptu prostředí PowerShell. 
   
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Definice zásad testovacího prostředí](devtest-lab-set-lab-policy.md) |Řízení nákladů pomocí nastavení zásad v testovacím prostředí. |
   | [Odstranit všechny testovací prostředí virtuálních počítačů pomocí skriptu prostředí PowerShell](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Po dokončení vývoj, odstraňte všechny labs v rámci jedné operace.|

1. **Přidání virtuální sítě k virtuálnímu počítači** 
   
    DevTest Labs vytvoří novou virtuální síť (VNET) vždy, když je vytvořena testovacím prostředí. Pokud jste nakonfigurovali vlastní virtuální síť – například s použitím ExpressRoute nebo VPN typu site-to-site – přidáním této virtuální sítě na nastavení virtuální sítě testovacího prostředí tak, aby bylo dostupné při vytváření virtuálních počítačů.

    Kromě toho je artefakt připojení k doméně služby Azure Active Directory k dispozici při vytvoření virtuálního počítače, který se připojí virtuální počítač k doméně. 
   
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Konfigurace virtuální sítě v Azure DevTest Labs](devtest-lab-configure-vnet.md) |Informace o konfiguraci virtuální sítě v Azure DevTest Labs pomocí portálu Azure.|

6. **Sdílet testovací prostředí se každý vývojář**
   
    Labs lze přímo přistupovat pomocí odkazu, který sdílíte s vaší vývojáři. Se dokonce ani nemusí mít účet Azure, tak dlouho, dokud mají [účtu Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Vývojáři nelze zobrazit virtuální počítače vytvořené jinými vývojáři.  
   
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Přidání vývojář do testovacího prostředí v Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Pomocí portálu Azure přidejte vývojáři do vašeho testovacího prostředí.|
   | [Přidat vývojáři do testovacího prostředí pomocí skriptu prostředí PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Použití Powershellu k automatizaci přidání vývojářům testovacího prostředí. |
   | [Získejte odkaz do testovacího prostředí](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Zjistěte, jak vývojáři přímo přistupovat k testovacím prostředí pomocí hypertextový odkaz.|

7. **Automatizovat vytvoření testovacího prostředí pro více týmů** 
   
    Je možné automatizovat vytvoření testovacího prostředí, včetně vlastních nastavení, vytvořením šablony Resource Manageru a jej použijete k vytvoření identické labs znovu a znovu. 
   
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Vytvoření testovacího prostředí pomocí šablony Resource Manageru](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Vytvoření prostředí v Azure DevTest Labs pomocí šablony Resource Manageru. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

