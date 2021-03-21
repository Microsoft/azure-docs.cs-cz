---
title: Nastavte zásady uchovávání informací v Azure DevTest Labs | Microsoft Docs
description: Naučte se konfigurovat zásady uchovávání informací, vyčistit továrnu a vyřadit staré image z DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 85384e88f8d456c7bf67302a57618d7a9703a5ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550021"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>Nastavení zásad uchovávání informací v Azure DevTest Labs
Tento článek popisuje, jak nastavit zásady uchovávání informací, vyčistit továrnu a vyřazení starých imagí ze všech ostatních DevTest Labs v organizaci. 

## <a name="prerequisites"></a>Předpoklady
Než budete pokračovat, ujistěte se, že jste postupovali podle těchto článků:

- [Vytvoření objektu pro vytváření imagí](image-factory-create.md)
- [Spuštění objektu pro vytváření imagí z Azure DevOps](image-factory-set-up-devops-lab.md)
- [Uložení vlastních imagí a jejich distribuce do několika testovacích prostředí](image-factory-save-distribute-custom-images.md)

Již by měly být zavedeny následující položky:

- Testovací prostředí pro objekt pro vytváření imagí v Azure DevTest Labs
- Jeden nebo více cílových Azure DevTest Labs, kde bude továrna distribuovat zlaté obrázky
- Projekt Azure DevOps, který slouží k automatizaci objektu pro vytváření imagí.
- Umístění zdrojového kódu obsahující skripty a konfiguraci (v našem příkladu v jednom použitém projektu DevOps)
- Definice sestavení pro orchestraci úloh Azure PowerShellu
 
## <a name="setting-the-retention-policy"></a>Nastavují se zásady uchovávání informací.
Než nakonfigurujete kroky čištění, definujte, kolik historických imagí si v DevTest Labs chcete zachovat. Když jste postupovali podle článku [spuštění objektu pro vytváření imagí z Azure DevOps](image-factory-set-up-devops-lab.md) , nakonfigurovali jste různé proměnné sestavení. Jedna z nich byla **ImageRetention**. Tato proměnná se nastavuje na `1` , což znamená, že DevTest Labs nebude udržovat historii vlastních imagí. K dispozici budou pouze nejnovější distribuované obrázky. Pokud změníte tuto proměnnou na `2` , zachová se nejnovější distribuovaná Image plus předchozí. Tuto hodnotu můžete nastavit tak, aby definovala počet historických imagí, které chcete zachovat v DevTest Labs.

## <a name="cleaning-up-the-factory"></a>Vyčištění továrny
Prvním krokem při čištění továrny je odebrání zlatých imagí virtuálních počítačů z továrny imagí. K provedení této úlohy stačí skript, stejně jako naši předchozí skripty. Prvním krokem je přidání další úlohy **Azure PowerShellu** do definice sestavení, jak je znázorněno na následujícím obrázku:

![Krok PowerShellu](./media/set-retention-policy-cleanup/powershell-step.png)

Jakmile budete mít nový úkol v seznamu, vyberte položku a vyplňte všechny podrobnosti, jak je znázorněno na následujícím obrázku:

![Vyčistit staré image PowerShell Task](./media/set-retention-policy-cleanup/configure-powershell-task.png)

Parametry skriptu jsou: `-DevTestLabName $(devTestLabName)` .

## <a name="retire-old-images"></a>Vyřadit staré image 
Tato úloha odebere všechny staré image a udržuje jenom historii, která odpovídá proměnné sestavení **ImageRetention** . Přidejte další úlohu sestavení **Azure PowerShellu** do definice sestavení. Po přidání vyberte úlohu a vyplňte podrobnosti, jak je znázorněno na následujícím obrázku: 

![Úloha PowerShellu pro vyřazení starých imagí](./media/set-retention-policy-cleanup/retire-old-image-task.png)

Parametry skriptu jsou: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>Zařazení sestavení do fronty
Teď, když jste dokončili definici sestavení, zaznamenejte nové sestavení, abyste se ujistili, že všechno funguje. Po úspěšném dokončení sestavení se nové vlastní image zobrazí v cílovém testovacím prostředí. Pokud zkontrolujete testovací prostředí pro Image Factory, nezobrazí se žádné zřízené virtuální počítače. Kromě toho, pokud zařadíte další buildy do fronty, uvidíte, že úlohy čištění vybírají staré vlastní image z DevTest Labs v souladu s hodnotou uchování nastavenou v proměnných sestavení.

> [!NOTE]
> Pokud jste provedli kanál sestavení na konci posledního článku v řadě, ručně odstraňte virtuální počítače, které byly vytvořeny v laboratoři pro vytváření imagí, před zařazením nového sestavení do fronty.  Krok ručního vyčištění je potřeba jenom v případě, že nastavili vše a ověří, jestli funguje.



## <a name="summary"></a>Souhrn
Teď máte spuštěnou továrnu imagí, která může na vyžádání generovat a distribuovat vlastní image do vašich cvičení. V tuto chvíli je to jen pro to, aby se vaše image správně nastavily a identifikovaly cílové cvičení. Jak je uvedeno v předchozím článku, **Labs.jsv** souboru umístěném v **konfigurační** složce určuje, které Image by se měly zpřístupnit v každé cílové laboratoři. Když do své organizace přidáte další DevTest Labs, stačí přidat položku v Labs.jspro nové testovací prostředí.

Přidání nového obrázku do továrny je také jednoduché. Pokud chcete do továrny zahrnout nový obrázek, otevřete [Azure Portal](https://portal.azure.com), přejděte do továrního DevTest Labs, vyberte toto tlačítko a přidejte virtuální počítač a zvolte požadovanou image na Marketplace a artefakty. Místo výběru tlačítka **vytvořit** pro vytvoření nového virtuálního počítače vyberte **Zobrazit Azure Resource Manager šablonu** a uložte šablonu jako soubor. JSON někam do složky **GoldenImages** v úložišti. Při příštím spuštění objektu pro vytváření imagí se vytvoří vlastní image.


## <a name="next-steps"></a>Další kroky
1. [Naplánujte sestavení nebo vydání](/azure/devops/pipelines/build/triggers?tabs=designer) , aby se továrna imagí pravidelně spouštěla. Pravidelně obnovuje image generované v továrně.
2. Vytvořte pro svou továrnu více zlatých imagí. Můžete také zvážit [Vytvoření artefaktů](devtest-lab-artifact-author.md) pro skriptování dalších částí úloh nastavení virtuálních počítačů a zahrnutí artefaktů do imagí pro vytváření.
4. Vytvořte [samostatné sestavení/vydání](/azure/devops/pipelines/overview?view=azure-devops-2019) , ke kterému se má skript **DistributeImages** spustit samostatně. Tento skript můžete spustit, když provedete změny Labs.jsa dostanete image zkopírované do cílové laboratoře, aniž byste museli znovu vytvářet všechny image znovu.

