---
title: Vytvořte objekt pro vytváření bitové kopie ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit objekt pro vytváření vlastní image ve službě Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 48412b3006a462fcc9c77219f42fb41d08f2df61
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2019
ms.locfileid: "59469366"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Vytvořte objekt pro vytváření vlastní image ve službě Azure DevTest Labs
Tento článek se věnuje nastavení zásad uchovávání informací, čištění objekt pro vytváření a vyřazení staré Image ze všech jiných službě DevTest Labs v organizaci. 

## <a name="prerequisites"></a>Požadavky
Ujistěte se, že jste postupovali podle těchto článků než budete pokračovat:

- [Vytvoření objektu pro vytváření imagí](image-factory-create.md)
- [Spuštění objektu pro vytváření imagí z Azure DevOps](image-factory-set-up-devops-lab.md)
- [Uložení vlastních imagí a jejich distribuce do několika testovacích prostředí](image-factory-save-distribute-custom-images.md)

Následující položky by už měla:

- Testovací prostředí pro vytváření bitové kopie ve službě Azure DevTest Labs
- Azure DevTest Labs, kde se příslušný objekt pro vytváření distribuovat zlaté Image cílit na jeden nebo více
- Projekt Azure DevOps se použít k automatizaci vytváření bitové kopie.
- Umístění zdrojového kódu, který obsahuje skripty a konfiguraci (v našem příkladu ve stejném projektu DevOps využité nad)
- Definice sestavení k ladění úloh prostředí Azure Powershell
 
## <a name="setting-the-retention-policy"></a>Nastavení zásad uchovávání informací
Než začnete konfigurovat vyčištění kroky, definujte kolik historických imagí, které chcete zachovat v DevTest Labs. Pokud jste postupovali podle [spustit objekt pro vytváření image z Azure DevOps](image-factory-set-up-devops-lab.md) článku, můžete nakonfigurovat různé vytvářet proměnné. Jeden z nich byl **ImageRetention**. Tuto proměnnou nastavit na `1`, což znamená, že nebude DevTest Labs udržovat historii vlastních imagí. Jenom nejnovější distribuované obrázků bude k dispozici. Pokud změníte tuto proměnnou na `2`, nejnovější distribuovat image navíc bude udržovat ta předchozí. Tuto hodnotu určující počet historických Image, kterou chcete spravovat ve službě DevTest Labs můžete nastavit.

## <a name="cleaning-up-the-factory"></a>Čištění objekt pro vytváření
Prvním krokem při čištění objekt pro vytváření je odebrat zlaté Image virtuálních počítačů z vytváření bitové kopie. Je skript, který stačí tuto úlohu stejně jako naše předchozí skripty. Prvním krokem je přidání dalšího **prostředí Azure Powershell** úloh k definici sestavení, jak je znázorněno na následujícím obrázku:

![Krok prostředí PowerShell](./media/set-retention-policy-cleanup/powershell-step.png)

Jakmile budete mít nový úkol v seznamu, vyberte položku a vyplňte všechny podrobnosti, jak je znázorněno na následujícím obrázku:

![Původní bitové kopie prostředí PowerShell úlohy čištění](./media/set-retention-policy-cleanup/configure-powershell-task.png)

Parametry skriptu jsou: `-DevTestLabName $(devTestLabName)`.

## <a name="retire-old-images"></a>Vyřazení staré Image 
Tento úkol odstraní všechny původní bitové kopie, zachovat pouze historii odpovídající **ImageRetention** vytvářet proměnné. Přidat další **prostředí Azure Powershell** sestavení úloh naše definici sestavení. Po přidání, vyberte úlohu a vyplňte podrobnosti, jak je znázorněno na následujícím obrázku: 

![Vyřazení staré úkolem PowerShell imagí](./media/set-retention-policy-cleanup/retire-old-image-task.png)

Parametry skriptu jsou: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>Zařaďte sestavení do fronty
Teď, když jste dokončili definici sestavení, fronty nové sestavení, abyste měli jistotu, že všechno funguje. Po úspěšném dokončení sestavení nové vlastní Image se zobrazí v cílovém testovacím prostředí a pokud zaškrtnete lab objekt pro vytváření bitové kopie, zobrazí se žádné virtuální počítače zřízené. Kromě toho pokud fronty další sestavení, uvidíte úlohy čištění po vyřazení navýšení kapacity staré vlastní Image z DevTest Labs v souladu pro uchování hodnoty nastavené v proměnných sestavení.

> [!NOTE]
> Pokud kanál sestavení na konci posledního článku jste spustili v této sérii, ručně odstraňte virtuální počítače, které byly vytvořené v labu objekt pro vytváření image před zařazování nového sestavení.  Ruční čištění kroku je potřeba pouze při všechno nastavíme a ověřte, zda že vše funguje.



## <a name="summary"></a>Souhrn
Nyní máte spuštěné tovární image, která mohou generovat a distribuovat vlastní Image pro vaše testovací prostředí na vyžádání. Na tento bod, je správně nastavený jenom pár získání vašich imagí a určení cílového labs. Jak je uvedeno v předchozím článku **Labs.json** soubor umístěný ve vaší **konfigurace** složky určuje imagí, které by měla být k dispozici ve všech testovacích prostředí cílového. Při přidávání dalších DevTest Labs pro vaši organizaci, stačí jednoduše přidat záznam v Labs.json pro nové prostředí.

Přidání nové image objektem pro vytváření je také jednoduchá. Pokud chcete zahrnout svým objektem pro vytváření otevřete novou bitovou kopii [webu Azure portal](https://portal.azure.com), přejděte na svůj objekt pro vytváření DevTest Labs, vyberte tlačítko pro přidání virtuálního počítače a zvolte požadovaný marketplace image a artefakty. Místo výběru možnosti **vytvořit** tlačítko nastavit nový virtuální počítač, vyberte **zobrazení Azure Resource Manageru šablony**"a šablonu uložte jako soubor .json někde v rámci **GoldenImages** složky v úložišti. Při příštím spuštění factory vaše image se vytvoří vlastní image.


## <a name="next-steps"></a>Další postup
1. [Sestavení/vydané verze naplánovat](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer) pravidelné spouštění objekt pro vytváření bitové kopie. Aktualizuje bitové kopie generované factory v pravidelných intervalech.
2. Ujistěte se, další zlaté Image pro výrobce. Můžete také zvážit [vytváření artefaktů](devtest-lab-artifact-author.md) skriptu další části vašich úloh nastavení virtuálního počítače a zahrnout artefakty, které objekt pro vytváření imagí.
4. Vytvoření [oddělení sestavení/vydané verze](/azure/devops/pipelines/overview?view=azure-devops-2019) ke spuštění **DistributeImages** skriptu samostatně. Tento skript můžete spustit, když provedete změny Labs.json a získat Image zkopírován do cílové cvičení bez nutnosti provádět všechny bitové kopie znovu.

