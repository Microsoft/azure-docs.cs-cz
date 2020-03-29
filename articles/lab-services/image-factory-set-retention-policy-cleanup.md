---
title: Nastavení zásad uchovávání informací v laboratořích Azure DevTest Labs | Dokumenty společnosti Microsoft
description: Zjistěte, jak nakonfigurovat zásady uchovávání informací, vyčistit továrnu a vyřadit staré obrázky z devTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: a472c500ee6b968b1459e65e49a352b81e5ea6ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759410"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>Nastavení zásad uchovávání informací v laboratořích Azure DevTest Labs
Tento článek popisuje nastavení zásad uchovávání informací, vyčištění továrny a vyřazení starých bitových kopií ze všech ostatních laboratoří DevTest v organizaci. 

## <a name="prerequisites"></a>Požadavky
Ujistěte se, že jste postupovali podle těchto článků, než budete pokračovat dále:

- [Vytvoření objektu pro vytváření imagí](image-factory-create.md)
- [Spuštění objektu pro vytváření imagí z Azure DevOps](image-factory-set-up-devops-lab.md)
- [Uložení vlastních imagí a jejich distribuce do několika testovacích prostředí](image-factory-save-distribute-custom-images.md)

Následující položky by již měly být na místě:

- Testovací prostředí pro továrnu na image v Azure DevTest Labs
- Jeden nebo více cílových laboratoří Azure DevTest, kde bude továrna distribuovat zlaté obrázky
- Projekt Azure DevOps slouží k automatizaci image factory.
- Umístění zdrojového kódu obsahující skripty a konfiguraci (v našem příkladu ve stejném projektu DevOps, který byl použit výše)
- Definice sestavení pro orchestratúlohy Azure Powershellu
 
## <a name="setting-the-retention-policy"></a>Nastavení zásad uchovávání informací
Před konfigurací kroků čištění definujte, kolik historických bitových kopií chcete zachovat v laboratořích DevTest. Když jste postupovali podle [spuštění výroby bitových bitových zjištění z článku Azure DevOps,](image-factory-set-up-devops-lab.md) nakonfigurovali jste různé proměnné sestavení. Jedním z nich byl **ImageRetention**. Tuto proměnnou `1`nastavíte na , což znamená, že DevTest Labs nebude udržovat historii vlastních bitových kopií. K dispozici budou pouze nejnovější distribuované obrázky. Pokud tuto proměnnou `2`změníte na , bude zachován nejnovější distribuovaný obrázek a předchozí. Tuto hodnotu můžete nastavit tak, aby definovala počet historických bitových kopií, které chcete zachovat ve svých laboratořích DevTest.

## <a name="cleaning-up-the-factory"></a>Vyčištění továrny
Prvním krokem při čištění továrny je odebrání virtuálních měn zlatého obrázku z továrny na obrázky. K dispozici je skript pro tento úkol, stejně jako naše předchozí skripty. Prvním krokem je přidání další **úlohy Azure Powershellu** do definice sestavení, jak je znázorněno na následujícím obrázku:

![Krok prostředí PowerShell](./media/set-retention-policy-cleanup/powershell-step.png)

Jakmile budete mít nový úkol v seznamu, vyberte položku a vyplňte všechny podrobnosti, jak je znázorněno na následujícím obrázku:

![Vyčistit staré obrázky úlohy PowerShellu](./media/set-retention-policy-cleanup/configure-powershell-task.png)

Parametry skriptu `-DevTestLabName $(devTestLabName)`jsou: .

## <a name="retire-old-images"></a>Vyřazení starých obrázků 
Tato úloha odebere všechny staré obrázky, přičemž pouze historie odpovídající **ImageRetention** sestavení proměnné. Přidejte další úlohu sestavení **Azure Powershellu** do naší definice sestavení. Po přidání vyberte úkol a vyplňte podrobnosti, jak je znázorněno na následujícím obrázku: 

![Vyřadit staré obrázky, úloha Prostředí PowerShell](./media/set-retention-policy-cleanup/retire-old-image-task.png)

Parametry skriptu jsou:`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>Zařazování sestavení do fronty
Nyní, když jste dokončili definici sestavení, zařazujte do fronty nové sestavení, abyste se ujistili, že vše funguje. Po dokončení sestavení úspěšně nové vlastní image se zobrazí v cílové laboratoři a pokud zkontrolujete testovací hodu vytvoření bitové kopie, uvidíte žádné zřízené virtuální chody. Navíc pokud fronty do fronty další sestavení, zobrazí se úlohy vyčištění vyřazení staré vlastní image z DevTest Labs v souladu s hodnotou uchování nastavit v proměnné sestavení.

> [!NOTE]
> Pokud jste provedli kanál sestavení na konci posledního článku v řadě, ručně odstraňte virtuální počítače, které byly vytvořeny v testovacím prostředí továrny bitových bitů před zařazením do fronty nové sestavení.  Ruční vyčištění krok je potřeba pouze tehdy, když jsme vše nastavit a ověřit, že to funguje.



## <a name="summary"></a>Souhrn
Nyní máte spuštěnou továrnu na bitové kopie, která může generovat a distribuovat vlastní bitové kopie do vašich testovacích prostředí na vyžádání. V tomto bodě, je to jen otázka, jak se vaše obrázky správně nastavit a identifikaci cílových laboratoří. Jak je uvedeno v předchozím článku, soubor **Labs.json** umístěný ve složce **Konfigurace** určuje, které bitové kopie by měly být k dispozici v každé z cílových testovacích prostředí. Při přidávání dalších laboratoří DevTest do vaší organizace stačí přidat položku v labs.json pro nové testovací prostředí.

Přidání nového obrázku do továrny je také jednoduché. Když chcete do továrny zahrnout novou bitovou kopii, otevřete [portál Azure](https://portal.azure.com), přejděte do továrního devtest labs, vyberte tlačítko pro přidání virtuálního počítače a zvolte požadovanou image a artefakty marketplace. Místo výběru tlačítka **Vytvořit** pro vytvoření nového virtuálního počítače vyberte **Zobrazit šablonu Správce prostředků Azure**a šablonu uložte jako soubor JSON někde ve složce **GoldenImages** ve vašem úložišti. Při příštím spuštění továrny na obrázky vytvoří tese vlastní obrázek.


## <a name="next-steps"></a>Další kroky
1. [Naplánujte sestavení nebo vydání](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer) pro pravidelné spouštění vytváření bitových obrázků. Pravidelně obnovuje obrázky generované továrnou.
2. Vytvořte více zlatých obrázků pro vaši továrnu. Můžete také zvážit [vytvoření artefaktů](devtest-lab-artifact-author.md) pro skript další části úlohnastavení virtuálního počítače a zahrnout artefakty v továrních iimages.
4. Vytvořte [samostatné sestavení nebo vydání](/azure/devops/pipelines/overview?view=azure-devops-2019) pro samostatné spuštění skriptu **DistributeImages.** Tento skript můžete spustit, když provedete změny souboru Labs.json a zkopírujete obrázky do cílových testovacích prostředí, aniž byste museli znovu vytvářet všechny obrázky.

