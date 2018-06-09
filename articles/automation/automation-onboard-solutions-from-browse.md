---
title: Zjistěte, jak zařadit řešení pro správu aktualizací, sledování změn a inventáře pro víc virtuálních počítačů ve službě Azure Automation
description: Zjistěte, jak zařadit do služby Azure virtuální počítač s řešeními správy aktualizací, sledování změn a inventáře, které jsou součástí Azure Automation.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 0a624d850b8c3260acb24cb17566090e8ad0043e
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233933"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Povolení správy aktualizací, sledování změn a inventáře řešení na víc virtuálních počítačů

Azure Automation nabízí řešení pro správu aktualizací zabezpečení operačního systému, sledování změn a inventáře nainstalovaných v počítačích. Existuje více způsobů zařadit počítače, můžete zaváděním řešení [z virtuálního počítače](automation-onboard-solutions-from-vm.md), z vaší [účet Automation](automation-onboard-solutions-from-automation-account.md), při procházení virtuální počítače, nebo pomocí [runbook](automation-onboard-solutions.md). Tento článek se zabývá registrace těchto řešení při procházení virtuálních počítačů v Azure.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure na adrese https://portal.azure.com.

## <a name="enable-solutions"></a>Povolení řešení

Na portálu Azure přejděte do **virtuální počítače**.

Pomocí zaškrtávacích políček vyberte virtuální počítače, které chcete zařadit s sledování změn a inventáře nebo Správa aktualizací. Registrace je dostupná pro až tři různé skupiny prostředků v čase.

![Seznam virtuálních počítačů](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> Pomocí ovládacích prvků filtru upravit seznam virtuálních počítačů a potom klikněte na horním zaškrtávací políčko Vybrat všechny virtuální počítače v seznamu.

Na panelu příkazů klikněte na **služby** a vyberte buď **sledování změn**, **inventáře**, nebo **správy aktualizací**.

> [!NOTE]
> **Sledování změn** a **inventáře** použít stejné řešení, když je povolena jedna dalších je povolen také.

Na následujícím obrázku je ke správě aktualizací. Sledování změn a inventáře mají stejné rozložení a chování.

Seznam virtuálních počítačů je filtrovaná pro zobrazit pouze virtuální počítače, které jsou ve stejném předplatném a umístění. Pokud vaše virtuální počítače jsou ve více než tří skupin prostředků, vyberou se první tři prostředků skupiny.

Pomocí ovládacích prvků filtru vybrat virtuální počítače z různých předplatných, umístění a skupiny prostředků.

![Zařadit řešení pro správu aktualizací](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Zkontrolujte volby pro pracovní prostor analýzy protokolů a účet Automation. Ve výchozím nastavení jsou vybrány nový pracovní prostor a účet Automation. Pokud máte existujícímu pracovnímu prostoru analýzy protokolů a účet Automation chcete použít, klikněte na tlačítko **změnit** a vybrat je z **konfigurace** stránky. Po dokončení klikněte na **Uložit**.

![Vyberte pracovní prostor a účet](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Zrušte výběr zaškrtávacího políčka vedle virtuálního počítače, které nechcete povolit. Virtuální počítače, které nelze povolit, jsou již není vybraná.

Klikněte na tlačítko **povolit** povolit řešení. Povolení řešení trvá přibližně 15 minut.

## <a name="troubleshooting"></a>Řešení potíží

Při připojování více počítačů, může být počítače, které se zobrazí jako **nelze povolit**. Existují různé důvody, proč nemusí být některé počítače povoleny. Následující části vysvětlují možné důvody **nelze povolit** stavu na virtuálním počítači při pokusu o zařadit do provozu.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>Virtuální počítač sestavy do různých pracovního prostoru: '\<workspaceName\>'.  Změna konfigurace použít k povolení

**Příčina**: Tato chyba ukazuje, že virtuální počítač, který se pokoušíte připojit sestavy do jiného pracovního prostoru.

**Řešení**: klikněte na tlačítko **používat jako konfigurace** Změna cílové pracovní účet Automation a analýzy protokolů.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>Sestavy virtuálních počítačů do pracovního prostoru, který není k dispozici v tomto předplatném

**Příčina**: pracovní prostor, který virtuální počítač sestavy:

* Je v jiném předplatném, nebo
* Již neexistuje, nebo
* Je ve skupině prostředků, které nemáte oprávnění k přístupu k

**Řešení**: Najít účet automation přidružena k pracovnímu prostoru, která generuje sestavy do virtuálního počítače a taky virtuální počítač tak, že změníte konfiguraci rozsahu.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>Verze operačního systému virtuálního počítače nebo distribuce není podporován.

**Příčina:** řešení není podporován pro všechny distribuce systému Linux nebo všechny verze systému Windows.

**Řešení:** odkazovat [seznam podporovaných klientů](automation-update-management.md#clients) řešení.

### <a name="classic-vms-cannot-be-enabled"></a>Klasické virtuální počítače nelze povolit.

**Příčina**: virtuální počítače, které používají model nasazení classic nejsou podporované.

**Řešení**: migrace virtuálního počítače do modelu nasazení resource manager. Zjistěte, jak to udělat, najdete v tématu [migrovat prostředky modelu nasazení classic](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>Virtuální počítač je zastavena. (nepřiřazeném)

**Příčina**: virtuální počítač není v **systémem** stavu.

**Řešení**: za účelem využití virtuálního počítače na řešení, virtuální počítač musí být spuštěn. Klikněte **spustit virtuální počítač** vložený odkaz ke spuštění virtuálního počítače bez nutnosti opustit stránku.

## <a name="next-steps"></a>Další postup

Teď, když toto řešení je povoleno pro virtuální počítače, najdete v článku Přehled správy aktualizace se dozvíte, jak chcete-li zobrazit vyhodnocení aktualizací pro počítače.

> [!div class="nextstepaction"]
> [Správa aktualizací - vyhodnocení aktualizací zobrazení](./automation-update-management.md#viewing-update-assessments)

Přidání kurzy o řešení a jakým způsobem je použít:

* [Kurz – Správa aktualizací pro virtuální počítač](automation-tutorial-update-management.md)

* [Kurz – identifikaci softwaru na virtuálním počítači](automation-tutorial-installed-software.md)

* [Kurz – řešení potíží s změny na virtuálním počítači](automation-tutorial-troubleshoot-changes.md)