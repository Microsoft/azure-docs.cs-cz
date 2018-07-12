---
title: Pomocí Azure DevTest Labs pro vývojáře | Dokumentace Microsoftu
description: Zjistěte, jak pomocí Azure DevTest Labs pro vývojářské scénáře.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38633277"
---
# <a name="use-azure-devtest-labs-for-developers"></a>Pomocí Azure DevTest Labs pro vývojáře
Azure DevTest Labs lze použít k implementaci mnoha klíčových scénářů. Jedním z nejzákladnějších je hostování počítačů pro vývojáře. V tomto scénáři poskytuje služba DevTest Labs následující výhody:

- Vývojářům můžete rychle zřídit své vývojové počítače na vyžádání.
- Vývojáři si můžou počítače pro vývoj kdykoli snadno přizpůsobit.
- Správci můžou řídit náklady tím, že zajišťuje, že:
  - Vývojáři nelze získat další virtuální počítače, než které potřebují pro vývoj.
  - Virtuální počítače jsou při ukončení není používáno. 

![Použití DevTest Labs pro školení](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

V tomto článku najdete informace o různých funkcí Azure DevTest Labs, které lze použít pro splnění požadavků pro vývojáře a podrobné kroky, pomocí kterých můžete pro nastavení testovacího prostředí.

## <a name="implementing-developer-environments-with-azure-devtest-labs"></a>Implementace prostředí pro vývojáře s Azure DevTest Labs
1. **Vytvoření testovacího prostředí** 
   
    Praktická cvičení jsou výchozí bod ve službě Azure DevTest Labs. Po vytvoření testovacího prostředí můžete provádět úlohy, jako je například přidávání uživatelů (vývojáři) do testovacího prostředí, nastavení zásad pro řízení nákladů, definování imagí virtuálních počítačů, které můžete rychle vytvořit a další.  
   
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Vytvoření testovacího prostředí ve službě Azure DevTest Labs](devtest-lab-create-lab.md) |Zjistěte, jak vytvořit testovací prostředí ve službě Azure DevTest Labs na webu Azure Portal. |
2. **Vytvoření virtuálních počítačů během několika minut pomocí Image z marketplace s předem připravená a vlastní Image** 
   
    Můžete vybrat předem vytvořené Image z širokou škálu imagí v Tržišti Azure Marketplace a zpřístupnit je v testovacím prostředí. Pokud předem vytvořené Image nesplňují vaše požadavky, můžete vytvořit vlastní image vytvořením testovacího prostředí virtuálního počítače pomocí předem vytvořené image z Azure Marketplace, instalace veškerý software, který potřebujete, a uložení virtuálního počítače jako vlastní image v testovacím prostředí.

    Pokud budete používat vlastní Image, zvažte použití objektu pro vytváření image k vytvoření a distribuce imagí. Objekt pro vytváření bitové kopie je konfigurace jako kódu řešení, které pravidelně sestavuje a distribuuje automaticky nakonfigurované Image. Tím ušetříte čas potřebný k ruční konfigurace systému po vytvoření virtuálního počítače se základní operační systém.
  
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Konfigurace imagí Azure Marketplace](devtest-lab-configure-marketplace-images.md) |Přečtěte si, jak Image Azure Marketplace seznamu povolených IP adres, zpřístupnění pro výběr pouze obrázky, které chcete použít pro vývojáře.|
   | [Vytvoření vlastní image](devtest-lab-create-template.md) |Vlastní image můžete vytvořte před instalací softwaru, které potřebujete, aby se vývojáři můžou rychle vytvořit virtuální počítač pomocí vlastní image.|
   | [Další informace o objekt pro vytváření bitové kopie](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |Podívejte se na video, které popisuje, jak nastavit a používat objekt pro vytváření bitové kopie.|

3. **Vytvoření opakovaně použitelné šablony pro počítače pro vývojáře** 
   
    Vzorec ve službě Azure DevTest Labs je seznam hodnot výchozí vlastnost použít k vytvoření virtuálního počítače. Výběrem obrázku, velikost virtuálního počítače (kombinací procesoru a paměti RAM) a virtuální síť můžete vytvořit vzorce v testovacím prostředí. Každý Vývojář můžete zobrazit vzorce v testovacím prostředí a použijte ji k vytvoření virtuálního počítače. 
   
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Správa vzorců DevTest Labs k vytvoření virtuálních počítačů](devtest-lab-manage-formulas.md) |Zjistíte, jak můžete vytvořit vzorce výběrem obrázku, velikost virtuálního počítače (kombinací procesoru a paměti RAM) a virtuální síť.|

4. **Vytváření artefaktů umožňuje flexibilní vlastního nastavení virtuálních počítačů**

   Artefakty se používají k nasazení a konfiguraci aplikace po zřízení virtuálního počítače. Artefakty mohou být:

   - Nástroje, které chcete nainstalovat do virtuálního počítače – například agenti, Fiddler a Visual Studio.
   - Akce, které chcete spustit na virtuálním počítači – třeba klonování úložiště.
   - Aplikace, které chcete otestovat

   Mnoho artefakty jsou už k dispozici out-of-the-box. Pokud potřebujete větší míru přizpůsobení vašim konkrétním potřebám, můžete vytvořit vlastní artefaktů.

   Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Při vytváření vlastních artefaktů pro virtuální počítač DevTest Labs](devtest-lab-artifact-author.md) |Vytvoření vlastních artefaktů pro virtuální počítače ve vaší laboratoři.|
   | [Přidání úložiště Git pro ukládání vlastních artefaktů a šablony Azure Resource Manageru pro použití ve službě Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Zjistěte, jak pro ukládání vlastních artefaktů v privátní úložiště Git.|

5. **Náklady na ovládací prvek**
   
    Azure DevTest Labs umožňuje nastavit zásady v testovacím prostředí můžete určit maximální počet virtuálních počítačů, které je možné vytvořit vývojář v testovacím prostředí. 
   
    Pokud má váš tým pro vývojáře sady pracovní plán a chcete zastavit všechny virtuální počítače v určitém čase dne a poté automaticky restartovat jejich následujícího dne, to snadno udělat nastavením automatického vypínání a automatického spuštění zásad v testovacím prostředí. 
   
    Nakonec po dokončení vývoje aplikací, můžete odstranit všechny virtuální počítače najednou spuštěním jednoho skriptu prostředí PowerShell. 
   
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Definice zásad testovacího prostředí](devtest-lab-set-lab-policy.md) |Řídit náklady nastavením zásad v testovacím prostředí. |
   | [Odstranit všechna prostředí virtuálních počítačů pomocí skriptu prostředí PowerShell](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Odstraňte testovací prostředí v rámci jedné operace, když je dokončen vývoj.|

1. **Přidání virtuální sítě k virtuálnímu počítači** 
   
    DevTest Labs vytvoří novou virtuální síť (VNET) pokaždé, když se vytvoří testovací prostředí. Pokud jste nakonfigurovali vlastní virtuální sítě – například s využitím ExpressRoute nebo VPN typu site-to-site – můžete přidat tuto virtuální síť k nastavení testovacího prostředí virtuální sítě tak, aby bylo dostupné při vytváření virtuálních počítačů.

    Kromě toho je artefakt připojení k doméně Azure Active Directory k dispozici, který se připojí virtuální počítač k doméně při vytváření virtuálního počítače. 
   
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Konfigurace virtuální sítě ve službě Azure DevTest Labs](devtest-lab-configure-vnet.md) |Zjistěte, jak konfigurace virtuální sítě ve službě Azure DevTest Labs pomocí webu Azure portal.|

6. **Každý vývojář nasdílet testovacího prostředí**
   
    Testovací prostředí lze přímo přistupovat pomocí odkazu, který můžete sdílet s vývojáři. Dokonce nemusí mít účet Azure za předpokladu, že mají [účtu Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Vývojáři nelze zobrazit virtuální počítače vytvořené jinými vývojáři.  
   
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Přidat vývojáře do testovacího prostředí ve službě Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Umožňuje vývojářům přidat do testovacího prostředí na webu Azure portal.|
   | [Vývojářům přidat do testovacího prostředí pomocí Powershellového skriptu](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Použití Powershellu k automatizaci přidání vývojářům testovacího prostředí. |
   | [Získání odkazu na testovací prostředí](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Zjistěte, jak vývojáři mohou přímo přístup k testovacímu prostředí prostřednictvím hypertextového odkazu.|

7. **Automatizace vytvoření testovacího prostředí pro více týmů** 
   
    Vytvoření testovacího prostředí, vlastní nastavení, třeba tak, že vytvoření šablony Resource Manageru a jeho použití znovu a znovu vytvářet identické testovací prostředí můžete automatizovat. 
   
    Další informace kliknutím na odkazy v následující tabulce:
   
   | Úkol | Co se naučíte |
   | --- | --- |
   | [Vytvoření testovacího prostředí pomocí šablony Resource Manageru](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Vytvoření testovacích prostředí v Azure DevTest Labs pomocí šablon Resource Manageru. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

