---
title: Zjistěte, jak začlenění řešení Update Management, Change Tracking a Inventory pro několik virtuálních počítačů ve službě Azure Automation
description: Zjistěte, jak připojit Azure virtuální počítač pomocí řešení Update Management, Change Tracking a Inventory, která jsou součástí služby Azure Automation
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 9a5e75b762c2ab0591212ce0bc3d7fac42132f8a
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56116911"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Povolení správy aktualizací, Change Tracking a inventář řešení na několika virtuálních počítačích

Azure Automation poskytuje řešení ke správě aktualizací zabezpečení operačního systému, sledování změn a inventáře, co je nainstalována na počítačích. Existuje několik způsobů, jak připojit počítače, můžete připojit řešení [z virtuálního počítače](automation-onboard-solutions-from-vm.md), z vaší [účtu Automation](automation-onboard-solutions-from-automation-account.md), při procházení virtuální počítače, nebo podle [runbook](automation-onboard-solutions.md). Tento článek popisuje připojení těchto řešení při procházení virtuálních počítačů v Azure.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure na adrese https://portal.azure.com.

## <a name="enable-solutions"></a>Povolení řešení

Na webu Azure Portal, přejděte na **virtuálních počítačů**.

Pomocí zaškrtávacích políček vyberte virtuální počítače, který chcete připojit pomocí řešení Change Tracking a Inventory nebo Update Management. Registrace je dostupná pro až tří různých skupin prostředků v čase.

![Seznam virtuálních počítačů](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> Ovládací prvky filtru můžete upravit seznam virtuálních počítačů a pak klikněte na nejvyšší zaškrtávací políčko, chcete-li vybrat všechny virtuální počítače v seznamu.

Na panelu příkazů klikněte na tlačítko **služby** a vyberte buď **řešení Change tracking**, **inventáře**, nebo **Update Management**.

> [!NOTE]
> **Sledování změn** a **inventáře** využívat stejná řešení, když je povolena jedna druhé je povolen také.

Na následujícím obrázku je pro správu aktualizací. Change tracking a Inventory mají stejné rozložení a chování.

Seznam virtuálních počítačů se vyfiltruje a zobrazí pouze virtuální počítače, které jsou ve stejném předplatném a umístění. Pokud jsou vaše virtuální počítače ve více než tři skupiny prostředků, jsou vybrány první tři zdroje skupiny.

### <a name="resource-group-limit"></a> Omezení registrace

Počet skupin prostředků, můžete použít pro připojení je omezena [omezení nasazení Resource Manageru](../azure-resource-manager/resource-manager-cross-resource-group-deployment.md). Nasazení Resource Manager by se zaměňovat s nasazeními aktualizací jsou omezené na 5 skupin zdrojů na nasazení. K zajištění integrity připojováním, jsou vyhrazené 2 z těchto skupin prostředků ke konfiguraci pracovního prostoru Log Analytics, účet Automation a související prostředky. Tak zůstanou 3 skupin prostředků a vyberte pro nasazení.

Použijte ovládací prvky filtru pro výběr virtuálních počítačů z různých předplatných, umístění a skupiny prostředků.

![Začlenění řešení Update management](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Projděte si možnosti pro pracovní prostor Log analytics a účet Automation. Ve výchozím nastavení jsou vybrány existující pracovní prostor a účet Automation. Pokud chcete použít jiný pracovní prostor Log Analytics a účet Automation, klikněte na tlačítko **vlastní** vybírat z **vlastní konfigurace** stránky. Při výběru pracovního prostoru Log Analytics se provede kontrola k určení, pokud je propojený s účtem Automation. Pokud je nalezen propojeného účtu Automation, zobrazí se na následující obrazovce. Až budete hotovi, klikněte na tlačítko **OK**.

![Vyberte pracovní prostor a účet](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Pokud vybraný pracovní prostor není propojený s účtem Automation, zobrazí se vám následující obrazovku. Vyberte účet Automation a klikněte na tlačítko **OK** po dokončení.

![Žádný pracovní prostor](media/automation-onboard-solutions-from-browse/no-workspace.png)

Při povolování řešení, pro propojení pracovního prostoru Log Analytics a účet Automation se podporují pouze určité oblasti.

V následující tabulce jsou uvedeny podporované mapování:

|**Oblasti pracovního prostoru log Analytics**|**Oblasti služby Azure Automation**|
|---|---|
|AustraliaSoutheast|AustraliaSoutheast|
|CanadaCentral|CanadaCentral|
|CentralIndia|CentralIndia|
|EastUS|EastUS2|
|JapanEast|JapanEast|
|SoutheastAsia|SoutheastAsia|
|WestCentralUS|WestCentralUS|
|WestEurope|WestEurope|
|UKSouth|UKSouth|
|USGovVirginia|USGovVirginia|
|EastUS2EUAP|CentralUSEUAP|

Zrušit zaškrtnutí políčka vedle libovolného virtuálního počítače, které nechcete povolit. Virtuálních počítačů, které není možné je už vybraná.

Klikněte na tlačítko **povolit** povolte řešení. Povolení řešení trvá přibližně 15 minut.

## <a name="unlink-workspace"></a>Zrušit propojení pracovního prostoru

Následující řešení jsou závislé na pracovní prostor Log Analytics:

* [Správa aktualizací](automation-update-management.md)
* [Sledování změn](automation-change-tracking.md)
* [Spuštění/zastavení virtuálních počítačů mimo špičku](automation-solution-vm-management.md)

Pokud se rozhodnete, že již nechcete integraci vašeho účtu Automation s Log Analytics, můžete svůj účet neodpojíte přímo z portálu Azure portal. Než budete pokračovat, budete nejdřív muset odebrat řešení již bylo zmíněno dříve, jinak nebudou moct tento proces budete pokračovat. Přečtěte si článek pro konkrétní řešení, které jste importovali pochopit kroky nutné k jeho odebrání.

Po odebrání těchto řešení provedením následujících kroků se zrušit propojení účtu Automation.

> [!NOTE]
> Některá řešení, včetně starších verzí řešení monitorování Azure SQL pravděpodobně vytvořena prostředky služby automation a může také muset před rušení propojení pracovního prostoru odebrat.

1. Z portálu Azure portal otevřete svůj účet Automation, a na automatizaci účtu vyberte stránku **pracovní prostor propojený** části **související prostředky** na levé straně.

2. Na stránce zrušit propojení pracovního prostoru klikněte na tlačítko **zrušit propojení pracovního prostoru**.

   ![Zrušit propojení pracovního prostoru stránky](media/automation-onboard-solutions-from-browse/automation-unlink-workspace-blade.png).

   Zobrazí se výzva s dotazem, jestli chcete pokračovat.

3. Zatímco bude Azure Automation se pokusí zrušit propojení účtu pracovního prostoru Log Analytics, můžete sledovat průběh **oznámení** z nabídky.

Pokud jste použili řešení Update Management, Volitelně můžete odebrat následující položky, které už nejsou potřeba po odebrání řešení.

* Plány aktualizace – každá bude mít názvy, které odpovídají vámi vytvořených nasazení aktualizací)

* Skupiny hybridních pracovních procesů vytvořené pro dané řešení – každá bude mít název podobně jako na machine1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8).

Pokud jste použili spouštění/zastavování virtuálních počítačů špičku, Volitelně můžete odebrat následující položky, které už nejsou potřeba po odebrání řešení.

* Spouštět a zastavovat sady runbook plány virtuálního počítače
* Spuštění a zastavení sad runbook virtuálního počítače
* Proměnné

## <a name="troubleshooting"></a>Řešení potíží

Při připojování více počítačů, může být počítače, které se zobrazují jako **nelze povolit**. Existují různé důvody, proč nemusí být některé počítače povolená. V následujících částech se dozvíte možným příčinám **nelze povolit** stavu na virtuálním počítači při pokusu o připojení.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>Virtuální počítač hlásí s jiným pracovním prostorem: "\<workspaceName\>".  Změna konfigurace použít k povolení

**Příčina:** Tato chyba ukazuje, že virtuální počítač, který se pokoušíte připojit sestavy s jiným pracovním prostorem.

**Řešení**: Klikněte na tlačítko **jako konfigurace** změnit cílový pracovní prostor účtu Automation a Log Analytics.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>Virtuální počítač sestavy do pracovního prostoru, který není v tomto předplatném k dispozici

**Příčina:** Pracovní prostor, který bude virtuální počítač hlásit do:

* Je v jiném předplatném, nebo
* Už existuje, nebo
* Je ve skupině prostředků nemáte oprávnění k přístupu k

**Řešení**: Najdete účet automation přidružený, který bude virtuální počítač hlásit do pracovního prostoru a připojit virtuální počítač tak, že změníte konfiguraci oboru.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>Verze operačního systému virtuálního počítače nebo distribuce se nepodporuje.

**Příčina:** Řešení není podporován pro všechny Linuxových distribucí nebo všechny verze Windows.

**Řešení:** Odkazovat [seznam podporovaných klientů](automation-update-management.md#clients) řešení.

### <a name="classic-vms-cannot-be-enabled"></a>Klasický virtuální počítač se nedá povolit.

**Příčina:** Virtuální počítače, které používají model nasazení classic se nepodporují.

**Řešení**: Virtuální počítač Migrate na modelu nasazení resource manager. Zjistěte, jak to provést, najdete v článku [migrace prostředků modelu nasazení classic](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>Virtuální počítač je zastavený. (přidělení zrušeno)

**Příčina:** Virtuální počítač není v **systémem** stavu.

**Řešení**: Za účelem připojení musí být virtuální počítač do řešení pro virtuální počítač spuštěn. Klikněte na tlačítko **spustit virtuální počítač** vložený odkaz ke spuštění virtuálního počítače bez navigaci pryč z stránky.

## <a name="next-steps"></a>Další postup

Teď, když bude řešení povoleno pro vaše virtuální počítače, naleznete v článku Přehled Update Management se naučíte zobrazit posouzení aktualizací pro počítače.

> [!div class="nextstepaction"]
> [Správa aktualizací – zobrazení posouzení aktualizací](./automation-update-management.md#viewing-update-assessments)

Další kurzy o řešení a jejich použití:

* [Kurz – Správa aktualizací pro virtuální počítač](automation-tutorial-update-management.md)

* [Kurz – identifikace softwaru na virtuálním počítači](automation-tutorial-installed-software.md)

* [Kurz – řešení potíží se změnami na virtuálním počítači](automation-tutorial-troubleshoot-changes.md)
