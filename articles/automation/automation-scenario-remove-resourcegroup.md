---
title: Automatizovat odstranění skupiny prostředků s Azure Automation.
description: Verze scénáře Azure Automation s pracovními postupy prostředí PowerShell, včetně runbooků pro odebrání všech skupin prostředků v rámci vašeho předplatného.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f0ec42a1e62c4aa35bb6bdffce0acf00a971cb7d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194515"
---
# <a name="azure-automation-scenario---automate-removal-of-resource-groups"></a>Scénář Azure Automation – automatizace odebrání skupin prostředků
Mnoho zákazníků vytváří více než jednu skupinu prostředků. Některé mohou sloužit ke správě produkčních aplikací, jiné mohou sloužit jako vývojové, testovací nebo přípravné prostředí. Automatizace nasazení těchto prostředků je jedna věc, ale možnost vyřadit skupinu prostředků z provozu jediným kliknutím něco úplně jiného. Tuto běžnou úlohu správy můžete zjednodušit pomocí Azure Automation. To je užitečné v případě, že pracujete s předplatným Azure, které má nastavený limit útraty prostřednictvím členské nabídky, jako je například MSDN nebo program Microsoft Partner Network Cloud Essentials.

Tento scénář je založený na runbooku PowerShellu a je určený k odebrání jedné nebo několika skupin prostředků, které zadáte ze svého předplatného. Ve výchozím nastavení runbook nejdříve testuje a poté pokračuje. Tím je zajištěno, že nechtěně neodstraníte skupinu prostředků dříve, než budete připraveni tento postup dokončit.   

## <a name="getting-the-scenario"></a>Získání scénáře
Tento scénář se skládá z runbooku prostředí PowerShell, který si můžete stáhnou z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/Remove-ResourceGroup/1.0/DisplayScript). Můžete jej také importovat přímo z [Galerie runbooků](automation-runbook-gallery.md) na webu Azure Portal.<br><br>

| Runbook | Popis |
| --- | --- |
| Remove-ResourceGroup |Odebere z předplatného jednu nebo více skupin prostředků Azure a přidružené prostředky. |

<br>
Pro tento runbook jsou definované tyto vstupní parametry:

| Parametr | Popis |
| --- | --- |
| NameFilter (povinný) |Zadává filtr názvů pro omezení skupin prostředků, které máte v úmyslu odstranit. Můžete předat několik hodnot ve formě seznamu odděleného čárkami.<br>Filtr není velká a malá písmena a odpovídá libovolné skupině prostředků, který obsahuje řetězec. |
| PreviewMode (volitelný) |Spustí runbook, aby bylo vidět, které skupiny prostředků se odstraní, ale neprovede žádnou akci.<br>Výchozí hodnota je **true**, která pomáhá zabránit nechtěnému odstranění jedné nebo několika skupin prostředků předaných do runbooku. |

## <a name="install-and-configure-this-scenario"></a>Instalace a konfigurace tohoto scénáře
### <a name="prerequisites"></a>Požadavky
Tento runbook se ověřuje pomocí [účtu Spustit v Azure jako](automation-sec-configure-azure-runas-account.md).    

### <a name="install-and-publish-the-runbooks"></a>Instalace a zveřejnění runbooků
Po stažení můžete tento runbook naimportovat pomocí postupu uvedeného v tématu [Postupy importování runbooků](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation). Po úspěšném dokončení importu do účtu Automation můžete runbook publikovat.

## <a name="using-the-runbook"></a>Použití runbooku
Následující postup vás provede procesem spuštění této sady runbook a nápovědy, které jste se seznámili s jak to funguje. Testování sady runbook v tomto příkladu, ve skutečnosti není odstraníte skupinu prostředků.  

1. Na webu Azure Portal otevřete svůj účet Automation a klikněte na **Runbooky**.
2. Vyberte runbook **Remove-ResourceGroup** a klikněte na **Spustit**.
3. Při spuštění sady runbook **spuštění Runbooku** otevře se stránka a můžete nastavit parametry. Zadejte názvy skupin prostředků v rámci vašeho předplatného, můžete použít pro testování a způsobí, že není škodu Pokud náhodou smazala.

   > [!NOTE]
   > Zkontrolujte, že možnost **Previewmode** je nastavená na **true**, abyste zabránili odstranění vybraných skupin prostředků. Tato sada runbook neodebere skupinu prostředků, která obsahuje účet Automation, kterým je spuštěn tento runbook.  
   >
   >
1. Po dokončení konfigurace hodnot všech parametrů klikněte na **OK**. Runbook se zařadí do fronty pro spuštění.  

Chcete-li zobrazit podrobnosti o **Remove-ResourceGroup** úlohy sady runbook na portálu Azure v části **prostředků**, vyberte **úlohy** v sadě runbook. Vyberte úkol, který chcete zobrazit. V souhrnu úlohy se zobrazí vstupní parametry a výstupní datový proud a také obecné informace o příslušné úloze a případné výjimky, které nastaly.<br> ![Stav úlohy runbooku Remove-ResourceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-status.png)

**Souhrn úlohy** zahrnuje zprávy z datových proudů výstupu, upozornění a chyb. Pokud chcete zobrazit podrobné výsledky spuštění runbooku, vyberte **Výstup**.<br> ![Výsledky výstupu runbooku Remove-ResourceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-output.png)

## <a name="next-steps"></a>Další postup
* Pokud se chcete pustit do vytváření vlastního runbooku, přečtěte si téma [Vytvoření nebo import runbooku ve službě Azure Automation](automation-creating-importing-runbook.md).
* První kroky s runbooky pracovních postupů prostředí PowerShell najdete v článku [Můj první runbook pracovního postupu prostředí PowerShell](automation-first-runbook-textual.md).
