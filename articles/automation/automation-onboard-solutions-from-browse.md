---
title: Naučte se integrovat řešení Update Management, Change Tracking a inventáře pro více virtuálních počítačů v Azure Automation
description: Naučte se, jak připojit virtuální počítač Azure pomocí Update Management, Change Tracking a řešení inventáře, která jsou součástí Azure Automation
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: 385806dca7dcac9fd0aac4c1bf9e1072e7fe5ecb
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979477"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Povolení řešení Update Management, Change Tracking a inventáře na několika virtuálních počítačích

Azure Automation poskytuje řešení pro správu aktualizací zabezpečení operačního systému, sledování změn a inventarizaci toho, co je na vašich počítačích nainstalované. Existují různé způsoby, jak připojit počítače, můžete řešení připojit k [virtuálnímu počítači](automation-onboard-solutions-from-vm.md), z [účtu Automation](automation-onboard-solutions-from-automation-account.md), při procházení virtuálních počítačů nebo pomocí [Runbooku](automation-onboard-solutions.md). Tento článek popisuje připojování těchto řešení při procházení virtuálních počítačů v Azure.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure na adrese https://portal.azure.com.

## <a name="enable-solutions"></a>Povolení řešení

V Azure Portal přejděte na **virtuální počítače**.

Pomocí zaškrtávacích políček vyberte virtuální počítače, které chcete připojit, pomocí Change Tracking a inventáře nebo Update Management. Připojování je dostupné až do tří různých skupin prostředků najednou. Virtuální počítače Azure můžou existovat v libovolné oblasti bez ohledu na umístění vašeho účtu Automation.

![Seznam virtuálních počítačů](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> Pomocí ovládacích prvků filtru upravte seznam virtuálních počítačů a kliknutím na zaškrtávací políčko v horní části vyberte všechny virtuální počítače v seznamu.

Na panelu příkazů klikněte na položku **služby** a vyberte položku **sledování změn**, **inventář**nebo **Update Management**.

> [!NOTE]
> **Změna sledování** a **inventarizace** používá stejné řešení, pokud je povoleno, je povolen i druhý.

Následující obrázek je určen pro Update Management. Změna sledování a inventarizace má stejné rozložení a chování.

Seznam virtuálních počítačů je filtrovaný tak, aby zobrazoval pouze virtuální počítače, které jsou ve stejném předplatném a umístění. Pokud jsou vaše virtuální počítače ve více než třech skupinách prostředků, vybere se první tři skupiny prostředků.

### <a name="resource-group-limit"></a>Omezení připojování

Počet skupin prostředků, které můžete použít pro registraci, je omezen [omezeními nasazení Správce prostředků](../azure-resource-manager/templates/cross-resource-group-deployment.md). Správce prostředků nasazení, nepleťte si s nasazeními aktualizací, jsou omezeny na 5 skupin prostředků na nasazení. Aby se zajistila integrita připojování, 2 z těchto skupin prostředků je vyhrazená pro konfiguraci Log Analyticsho pracovního prostoru, účtu Automation a souvisejících prostředků. Tím se vám ponechá 3 skupiny prostředků, které se mají vybrat pro nasazení. Tento limit se vztahuje jenom na simultánní registraci, nikoli na počet skupin prostředků, které se dají spravovat pomocí řešení automatizace.

Můžete taky použít Runbook pro registraci, další informace najdete v tématu [řešení aktualizací a řešení Change Tracking pro Azure Automation](automation-onboard-solutions.md).

Pomocí ovládacích prvků filtru vyberte virtuální počítače z různých předplatných, umístění a skupin prostředků.

![Připojit řešení pro správu aktualizací](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Zkontrolujte volby pro Log Analytics pracovní prostor a účet Automation. Ve výchozím nastavení je vybraný existující pracovní prostor a účet Automation. Pokud chcete použít jiný Log Analytics pracovní prostor a účet Automation, klikněte na tlačítko **vlastní** a vyberte je na stránce **vlastní konfigurace** . Když zvolíte pracovní prostor Log Analytics, provedeme kontrolu, která určí, jestli je propojený s účtem Automation. Pokud se najde propojený účet Automation, zobrazí se následující obrazovka. Až budete hotovi, klikněte na **OK**.

![Vyberte pracovní prostor a účet.](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Pokud vybraný pracovní prostor není propojený s účtem Automation, zobrazí se následující obrazovka. Vyberte účet Automation a po dokončení klikněte na **OK** .

![Žádný pracovní prostor](media/automation-onboard-solutions-from-browse/no-workspace.png)

> [!NOTE]
> Při povolování řešení se podporuje propojení pracovního prostoru služby Log Analytics a účtu Automation pouze v určitých oblastech.
>
> Seznam podporovaných dvojic mapování najdete v tématu [mapování oblastí pro účet Automation a Log Analytics pracovní prostor](how-to/region-mappings.md).

Zrušte zaškrtnutí políčka vedle libovolného virtuálního počítače, který nechcete povolit. Virtuální počítače, které se nedají povolit, už nejsou vybrané.

Pokud chcete řešení povolit, klikněte na **Povolit** . Povolení řešení trvá přibližně 15 minut.

## <a name="unlink-workspace"></a>Zrušit propojení pracovního prostoru

Následující řešení jsou závislá na Log Analytics pracovním prostoru:

* [Správa aktualizací](automation-update-management.md)
* [Sledování změn](automation-change-tracking.md)
* [Start/Stop VMs during off-hours](automation-solution-vm-management.md)

Pokud se rozhodnete, že už nechcete integrovat svůj účet Automation s pracovním prostorem Log Analytics, můžete zrušit propojení svého účtu přímo s Azure Portal. Než budete pokračovat, musíte nejprve odebrat dříve uvedená řešení. v opačném případě bude znemožněno pokračovat v tomto procesu. Projděte si článek pro konkrétní řešení, které jste naimportovali, abyste pochopili kroky potřebné k jeho odebrání.

Po odebrání těchto řešení můžete provést následující kroky a zrušit propojení svého účtu Automation.

> [!NOTE]
> Některá řešení, včetně dřívějších verzí řešení Azure SQL monitoring, mohla vytvořit automatizační prostředky a možná je budete muset před odpojením pracovního prostoru odebrat.

1. V Azure Portal otevřete svůj účet Automation a na stránce účet Automation vyberte **propojený pracovní prostor** v části **související prostředky** na levé straně.

2. Na stránce zrušit propojení pracovního prostoru klikněte na **Zrušit propojení pracovního prostoru**.

   ![Zrušit propojení stránky pracovního prostoru](media/automation-onboard-solutions-from-browse/automation-unlink-workspace-blade.png).

   Zobrazí se výzva s dotazem, jestli chcete pokračovat.

3. I když se Azure Automation pokusí odpojit účet Log Analytics pracovním prostoru, můžete sledovat průběh v nabídce **oznámení** .

Pokud jste použili řešení Update Management, možná budete chtít po odebrání řešení odebrat následující položky, které už nepotřebujete.

* Aktualizace plánů – každá bude mít názvy, které odpovídají vytvořeným nasazením aktualizací.

* Skupiny hybridních pracovních procesů vytvořené pro řešení – každá bude pojmenována podobně jako machine1. contoso. com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Pokud jste použili řešení Start/Stop VMs during off-hours, možná budete chtít po odebrání řešení odebrat následující položky, které už nepotřebujete.

* Spuštění a zastavení plánů Runbook VM
* Spuštění a zastavení runbooků virtuálních počítačů
* Proměnné

Alternativně můžete také zrušit propojení pracovního prostoru s účtem Automation z pracovního prostoru Log Analytics. V pracovním prostoru vyberte **účet Automation** v části **související prostředky**. Na stránce účet Automation vyberte zrušit **propojení účtu**.

## <a name="troubleshooting"></a>Řešení potíží

Při připojování více počítačů můžou existovat počítače, které ukazují, že se **nedají povolit**. Existují různé důvody, proč nemusí být některé počítače povolené. V následujících částech se dozvíte o možných důvodech, proč se při pokusu o připojení k virtuálnímu počítači **nepodaří stav povolit** .

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>Virtuální počítače se sestavují do jiného pracovního prostoru:\<\>pracovního prostoru.  Změnit konfiguraci, aby se použila pro povolení

**Příčina**: Tato chyba ukazuje, že virtuální počítač, který se pokoušíte připojit, do jiného pracovního prostoru.

**Řešení**: kliknutím na **použít jako konfigurace** změníte cílový účet Automation a Log Analytics pracovní prostor.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>Sestavování virtuálních počítačů do pracovního prostoru, který není v tomto předplatném k dispozici

**Příčina**: pracovní prostor, do kterého se virtuální počítač hlásí:

* Je v jiném předplatném, nebo
* Již neexistuje, nebo
* Je ve skupině prostředků, ke které nemáte přístupová oprávnění.

**Řešení**: Najděte účet Automation přidružený k pracovnímu prostoru, který virtuální počítač hlásí a zaregistruje virtuální počítač, změnou konfigurace oboru.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>Verze nebo distribuce operačního systému virtuálního počítače se nepodporuje.

**Příčina:** Řešení není podporováno pro všechny distribuce systému Linux nebo všechny verze systému Windows.

**Řešení:** Přečtěte si [seznam podporovaných klientů](automation-update-management.md#clients) pro řešení.

### <a name="classic-vms-cannot-be-enabled"></a>Klasické virtuální počítače se nedají povolit.

**Příčina**: virtuální počítače, které používají model nasazení Classic, nejsou podporovány.

**Řešení**: migrujte virtuální počítač do modelu nasazení Správce prostředků. Další informace o tom, jak to udělat, najdete v tématu [migrace prostředků modelu nasazení Classic](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>Virtuální počítač je zastavený. přidělení zrušeno

**Příčina**: virtuální počítač není ve **spuštěném** stavu.

**Řešení**: aby bylo možné připojit virtuální počítač k řešení, musí být spuštěný virtuální počítač. Kliknutím na odkaz **Spustit virtuální počítač** spustíte virtuální počítač, aniž byste museli přejít pryč ze stránky.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odebrání virtuálního počítače z Update Management:

* Ve vašem pracovním prostoru Log Analytics odeberte virtuální počítač z uloženého hledání pro `MicrosoftDefaultScopeConfig-Updates`konfigurace oboru. Uložená hledání najdete v části **Obecné** v pracovním prostoru.
* Odeberte [agenta Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) nebo [agenta Log Analytics pro Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Další kroky

Když je teď řešení povolené pro vaše virtuální počítače, přečtěte si článek Přehled Update Management, kde se dozvíte, jak vytvořit **nasazení aktualizace** pro vaše počítače.

> [!div class="nextstepaction"]
> [Update Management – Správa aktualizací a oprav pro virtuální počítače Azure](./automation-tutorial-update-management.md)

Přidání kurzů k řešením a jejich použití:

* [Kurz – Správa aktualizací pro virtuální počítač](automation-tutorial-update-management.md)

* [Kurz – určení softwaru na virtuálním počítači](automation-tutorial-installed-software.md)

* [Kurz – řešení potíží se změnami na virtuálním počítači](automation-tutorial-troubleshoot-changes.md)
