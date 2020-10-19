---
title: Řešení potíží s agenty a rozšířením
description: Příznaky, příčiny a řešení chyb Azure Backup souvisejících s agentem, rozšířením a disky.
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.service: backup
ms.openlocfilehash: 1711652434be3d9937c8199bc1999273ef58e4d0
ms.sourcegitcommit: 94ca9e89501e65f4dcccc3789249357c7d5e27e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92170288"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Řešení potíží s Azure Backup Chyba: problémy s agentem nebo rozšířením

Tento článek popisuje kroky pro řešení potíží, které vám pomohou vyřešit chyby Azure Backup související s komunikací s agentem a rozšířením virtuálního počítače.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="step-by-step-guide-to-troubleshoot-backup-failures"></a>Podrobný průvodce odstraňováním selhání zálohování

Většinu běžných selhání zálohování můžete vyřešit sami pomocí následujících kroků pro řešení potíží:

### <a name="step-1-check-azure-vm-health"></a>Krok 1: ověření stavu virtuálního počítače Azure

- Ujistěte se, že **stav zřizování virtuálních počítačů Azure je spuštěný**: Pokud [stav zřizování virtuálního počítače](../virtual-machines/states-lifecycle.md#provisioning-states) je ve stavu **Zastaveno/zrušeno přidělení/aktualizace** , bude v konfliktu s operací zálohování. Otevřete *Azure Portal > > přehled >* a zkontrolujte stav virtuálního počítače, abyste se ujistili, že je **spuštěný**  , a zkuste operaci zálohování zopakovat.
- **Kontrola nedokončených aktualizací operačního systému nebo restartování**: Ujistěte se, že na virtuálním počítači nejsou žádné nevyřízené aktualizace operačního systému ani čekají na restartování.

### <a name="step-2-check-azure-vm-guest-agent-service-health"></a>Krok 2: ověření stavu služby agenta hosta virtuálního počítače Azure

- **Ujistěte se, že je spuštěná služba agenta hosta virtuálního počítače Azure a aktuální**:
  - Na virtuálním počítači s Windows:
    - Přejděte na **Services. msc** a ujistěte se, že je **Služba agenta hosta virtuálního počítače Windows Azure** spuštěná. Také se ujistěte, že je nainstalovaná [nejnovější verze](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) . Další informace najdete v tématu [problémy agenta hosta virtuálních počítačů s Windows](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms).
    - Agent virtuálního počítače Azure se ve výchozím nastavení instaluje na jakýkoli virtuální počítač s Windows nasazený z Azure Marketplace image z portálu, PowerShellu, rozhraní příkazového řádku nebo šablony Azure Resource Manager. Pokud vytvoříte vlastní image virtuálního počítače, která je nasazená do Azure, může být potřeba [Ruční instalace agenta](../virtual-machines/extensions/agent-windows.md#manual-installation) .
    - Zkontrolujte matrici podpory a zkontrolujte, jestli je virtuální počítač spuštěný v [podporovaném operačním systému Windows](backup-support-matrix-iaas.md#operating-system-support-windows).
  - Na virtuálním počítači se systémem Linux
    - Zajistěte, aby služba agenta hosta virtuálního počítače Azure běžela spuštěním příkazu `ps-e` . Také se ujistěte, že je nainstalovaná [nejnovější verze](../virtual-machines/extensions/update-linux-agent.md) . Další informace najdete v tématu [problémy agenta hosta virtuálních počítačů se systémem Linux](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms).
    - Ujistěte se, že [závislosti agenta virtuálního počítače se systémem Linux u systémových balíčků](../virtual-machines/extensions/agent-linux.md#requirements) mají podporovanou konfiguraci. Příklad: podporovaná verze Pythonu je 2,6 a vyšší.
    - Zkontrolujte matrici podpory a zkontrolujte, jestli je virtuální počítač spuštěný v [podporovaném operačním systému Linux.](backup-support-matrix-iaas.md#operating-system-support-linux)

### <a name="step-3-check-azure-vm-extension-health"></a>Krok 3: ověření stavu rozšíření virtuálních počítačů Azure

- **Zajistěte, aby byla všechna rozšíření virtuálních počítačů Azure ve stavu úspěšného zřizování**: Pokud je jakékoli rozšíření ve stavu selhání, může to narušit zálohování.
- *Otevřete Azure Portal > > nastavení virtuálního počítače > rozšíření stav rozšíření >* a ověřte, jestli jsou všechna rozšíření ve stavu **úspěšné zřizování** .
- Zajistěte, aby všechny [problémy s rozšířením](../virtual-machines/extensions/overview.md#troubleshoot-extensions) byly vyřešeny, a opakujte operaci zálohování.
- Ujistěte se, že je **Systémová aplikace modelu COM+** spuštěná. **Služba DTC (Distributed Transaction Coordinator)** by měla také běžet jako **účet síťové služby**. [Při odstraňování problémů s com+ a MSDTC](backup-azure-vms-troubleshoot.md#extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error)postupujte podle kroků v tomto článku.

### <a name="step-4-check-azure-backup-vm-extension-health"></a>Krok 4: zkontrolování Azure Backup stavu rozšíření virtuálního počítače

Azure Backup používá rozšíření snímku virtuálního počítače k pořízení zálohy virtuálního počítače Azure konzistentní vzhledem k aplikacím. Azure Backup bude rozšíření instalovat jako součást prvního naplánovaného zálohování aktivovaného po povolení zálohování.

- **Ujistěte se, že rozšíření VMSnapshot není ve stavu selhání**: postupujte podle kroků uvedených v této [části](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) , abyste ověřili a zajistili, že je rozšíření Azure Backup v pořádku.

- **Ověřte, jestli antivirová ochrana blokuje rozšíření**: určitý antivirový software může zabránit v provádění rozšíření.
  
  V době selhání zálohování ověřte, zda jsou v protokolu ***aplikace Prohlížeč událostí protokoly aplikací*** s ***chybou název aplikace: IaaSBcdrExtension.exe***. Pokud se zobrazí položky, může to být antivirová ochrana nakonfigurovaná na virtuálním počítači, která omezuje spuštění rozšíření zálohování. Otestujte tak, že v konfiguraci antivirového programu vyloučíte následující adresáře a potom zkuste operaci zálohování zopakovat.
  - `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`
  - `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`

- **Zjistit, jestli je vyžadován síťový přístup**: balíčky rozšíření se stáhnou z úložiště rozšíření Azure Storage a nahrávání stavu rozšíření se publikují do Azure Storage. [Další informace](../virtual-machines/extensions/features-windows.md#network-access).
  - Pokud jste v nepodporované verzi agenta, musíte z virtuálního počítače v této oblasti povolený odchozí přístup ke službě Azure Storage.
  - Pokud jste zablokovali přístup k `168.63.129.16` použití brány firewall hosta nebo proxy serveru, rozšíření selžou bez ohledu na výše uvedené. Vyžadují se porty 80, 443 a 32526. [Další informace najdete tady](../virtual-machines/extensions/features-windows.md#network-access).

- **Zajistěte, aby byl na virtuálním počítači hosta povolený protokol DHCP**: vyžaduje se k získání adresy hostitele nebo prostředků infrastruktury z protokolu DHCP, aby mohla záloha virtuálního počítače IaaS fungovat. Pokud potřebujete statickou privátní IP adresu, měli byste ji nakonfigurovat přes Azure Portal nebo PowerShell a ujistit se, že je ve virtuálním počítači povolená možnost DHCP, [Další informace](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken).

- **Zajistěte, aby služba zapisovače VSS byla v**provozu: [při řešení potíží s modulem pro zápis VSS](backup-azure-vms-troubleshoot.md#extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state)postupujte podle těchto kroků.
- **Postupujte podle pokynů pro osvědčené postupy zálohování**: Projděte si [osvědčené postupy pro povolení zálohování virtuálních počítačů Azure](backup-azure-vms-introduction.md#best-practices).
- **Přečtěte si pokyny pro šifrované disky**: Pokud povolujete zálohování pro virtuální počítače s šifrovaným diskem, ujistěte se, že jste zadali všechna požadovaná oprávnění. Další informace najdete v tématu [zálohování a obnovení šifrovaného virtuálního počítače Azure](backup-azure-vms-encryption.md).

## <a name="usererrorguestagentstatusunavailable---vm-agent-unable-to-communicate-with-azure-backup"></a><a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable – Agent virtuálního počítače nemůže komunikovat se službou Azure Backup

**Kód chyby**: UserErrorGuestAgentStatusUnavailable <br>
**Chybová zpráva**: Agent virtuálního počítače nemůže komunikovat s Azure Backup<br>

Agent virtuálního počítače Azure se může zastavit, zastaralá, je v nekonzistentním stavu nebo není nainstalovaný. Tyto stavy brání službě Azure Backup v aktivaci snímků.

- **Otevřete Azure Portal > > nastavení virtuálního počítače > podokno vlastnosti** > zkontrolujte, že **stav** virtuálního počítače **běží** a že **Stav agenta** je **připravený**. Pokud je agent virtuálního počítače zastavený nebo je v nekonzistentním stavu, restartujte agenta.<br>
  - U virtuálních počítačů s Windows postupujte podle těchto [kroků](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) a restartujte agenta hosta.<br>
  - Pro virtuální počítače se systémem Linux pomocí následujících [kroků](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) restartujte agenta hosta.
- **Otevřete Azure Portal > virtuálního počítače > nastavení > rozšíření** > zajistěte, aby byla všechna rozšíření ve stavu **úspěšného zřizování** . Pokud ne, vyřešte problém podle těchto [kroků](#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) .

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError – Nejde komunikovat s agentem virtuálního počítače kvůli stavu snímku

**Kód chyby**: GuestAgentSnapshotTaskStatusError<br>
**Chybová zpráva**: nepovedlo se komunikovat s agentem virtuálního počítače pro stav snímku. <br>

Po registraci a naplánování virtuálního počítače pro službu Azure Backup služba Backup spustí úlohu prostřednictvím komunikace s rozšířením zálohování virtuálního počítače, aby bylo možné vytvořit snímek v daném časovém okamžiku. Některé z následujících podmínek mohou zabránit spuštění snímku. Pokud se snímek neaktivuje, může dojít k selhání zálohování. Proveďte následující kroky pro řešení potíží v uvedeném pořadí a opakujte operaci:  

**Příčina 1: [Agent je nainstalovaný na virtuálním počítači, ale nereaguje (pro virtuální počítače s Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) .**  

**Příčina 2: [Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače se systémem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) .**

**Příčina 3: [nejde načíst stav snímku nebo nejde udělat snímek](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken) .**

**Příčina 4: [virtuální počítače – možnosti konfigurace agenta nejsou nastavené (pro virtuální počítače se systémem Linux)](#vm-agent-configuration-options-are-not-set-for-linux-vms)**

**Příčina 5: [řešení řízení aplikací blokuje IaaSBcdrExtension.exe](#application-control-solution-is-blocking-iaasbcdrextensionexe)**

## <a name="usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state"></a>UserErrorVmProvisioningStateFailed – Virtuální počítač je ve stavu neúspěšného zřízení

**Kód chyby**: UserErrorVmProvisioningStateFailed<br>
**Chybová zpráva**: virtuální počítač je ve stavu selhání zřizování.<br>

K této chybě dojde, když se jedna z chyb rozšíření přesune virtuální počítač do stavu selhání zřizování.<br>**Otevřete Azure Portal > > nastavení virtuálního počítače > rozšíření stav rozšíření >** a ověřte, jestli jsou všechna rozšíření ve stavu **úspěšné zřizování** . Další informace najdete v tématu [stav zřizování](../virtual-machines/states-lifecycle.md#provisioning-states).

- Pokud je jakékoli jiné rozšíření ve stavu selhání, může to narušit zálohování. Zajistěte, aby byly problémy s rozšířením vyřešeny, a opakujte operaci zálohování.
- Pokud je stav zřizování virtuálního počítače v aktualizovaném stavu, může to narušit zálohování. Ujistěte se, že je v pořádku, a zkuste operaci zálohování zopakovat.

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached – Dosáhlo se maximálního limitu kolekcí bodů obnovení

**Kód chyby**: UserErrorRpCollectionLimitReached <br>
**Chybová zpráva**: dosáhlo se maximálního limitu kolekce bodů obnovení. <br>

- K tomuto problému může dojít, pokud je zámek skupiny prostředků bodu obnovení znemožněný automatickým čištěním bodů obnovení.
- K tomuto problému může dojít také v případě, že je aktivováno více záloh za den. V současné době doporučujeme jenom jednu zálohu za den, protože body okamžitého obnovení se uchovávají po dobu 1-5 dnů na základě nakonfigurovaného uchování snímku a k virtuálnímu počítači se dá v jednom okamžiku přidružit jenom 18 RPs. <br>
- Počet bodů obnovení mezi kolekcemi bodů obnovení a skupinami prostředků pro virtuální počítač nemůže překročit 18. Chcete-li vytvořit nový bod obnovení, odstraňte existující body obnovení.

Doporučená akce:<br>
Pokud chcete tento problém vyřešit, odeberte zámek pro skupinu prostředků virtuálního počítače a potom operaci spusťte znovu, aby se aktivovala operace vyčištění.
> [!NOTE]
> Služba Backup vytvoří samostatnou skupinu prostředků, než je skupina prostředků virtuálního počítače pro uložení kolekce bodů obnovení. Doporučujeme, abyste nezamkli skupinu prostředků vytvořenou pro použití v rámci služby zálohování. Formát názvů pro skupinu prostředků vytvořenou službou zálohování: AzureBackupRG_ `<Geo>` _ `<number>` . Příklad: *AzureBackupRG_northeurope_1*

**Krok 1: [Odebrání zámku ze skupiny prostředků bodu obnovení](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Krok 2: [Vyčištění kolekce bodů obnovení](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured-Backup nemá dostatečná oprávnění k trezoru klíčů pro zálohování šifrovaných virtuálních počítačů.

**Kód chyby**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Chybová zpráva**: zálohování nemá dostatečná oprávnění k trezoru klíčů pro zálohování šifrovaných virtuálních počítačů. <br>

Aby operace zálohování proběhla úspěšně na šifrovaných virtuálních počítačích, musí mít oprávnění pro přístup k trezoru klíčů. Oprávnění lze nastavit prostřednictvím [Azure Portal](./backup-azure-vms-encryption.md) nebo prostřednictvím [prostředí PowerShell](./backup-azure-vms-automation.md#enable-protection).

## <a name="extensionsnapshotfailednonetwork---snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a><a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork – Operace vytvoření snímku selhala, protože virtuální počítač není připojený k síti

**Kód chyby**: ExtensionSnapshotFailedNoNetwork<br>
**Chybová zpráva**: operace snímku selhala, protože ve virtuálním počítači není síťové připojení.<br>

Po registraci a naplánování virtuálního počítače pro službu Azure Backup služba Backup spustí úlohu prostřednictvím komunikace s rozšířením zálohování virtuálního počítače, aby bylo možné vytvořit snímek v daném časovém okamžiku. Některé z následujících podmínek mohou zabránit spuštění snímku. Pokud se snímek neaktivuje, může dojít k selhání zálohování. Proveďte následující krok řešení potíží a opakujte operaci:

**[Stav snímku nelze načíst nebo nelze vytvořit snímek.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  

## <a name="extensionoperationfailedformanageddisks---vmsnapshot-extension-operation-failed"></a><a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks – Operace rozšíření VMSnapshot selhala

**Kód chyby**: ExtensionOperationFailedForManagedDisks <br>
**Chybová zpráva**: operace rozšíření VMSnapshot se nezdařila.<br>

Po registraci a naplánování virtuálního počítače pro službu Azure Backup služba Backup spustí úlohu prostřednictvím komunikace s rozšířením zálohování virtuálního počítače, aby bylo možné vytvořit snímek v daném časovém okamžiku. Některé z následujících podmínek mohou zabránit spuštění snímku. Pokud se snímek neaktivuje, může dojít k selhání zálohování. Proveďte následující kroky pro řešení potíží v uvedeném pořadí a opakujte operaci:  
**Příčina 1: nelze [načíst stav snímku nebo nelze](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken) pořídit snímek.**  
**Příčina 2: [Agent je nainstalovaný na virtuálním počítači, ale nereaguje (pro virtuální počítače s Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) .**  
**Příčina 3: [Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače se systémem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) .**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 – Služba Backup selhala s vnitřní chybou

**Kód chyby**: BackUpOperationFailed/BackUpOperationFailedV2 <br>
**Chybová zpráva**: Zálohování selhalo s interní chybou – zkuste prosím operaci zopakovat za pár minut. <br>

Po registraci a naplánování virtuálního počítače pro službu Azure Backup služba Backup inicializuje úlohu prostřednictvím komunikace s rozšířením zálohy virtuálního počítače, aby se pomohlo vytvořit snímek v daném časovém okamžiku. Některé z následujících podmínek mohou zabránit spuštění snímku. Pokud se snímek neaktivuje, může dojít k selhání zálohování. Proveďte následující kroky pro řešení potíží v uvedeném pořadí a opakujte operaci:  
**Příčina 1: [Agent nainstalovaný na virtuálním počítači, ale nereaguje (pro virtuální počítače s Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Příčina 2: [Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače se systémem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) .**  
**Příčina 3: [nejde načíst stav snímku nebo nejde udělat snímek](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken) .**  
**Příčina 4: [Služba zálohování nemá oprávnění odstranit staré body obnovení z důvodu zámku skupiny prostředků](#remove_lock_from_the_recovery_point_resource_group) .**<br>

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize – konfigurace nakonfigurovaných disků aktuálně není v Azure Backup podporována.

**Kód chyby**: UserErrorUnsupportedDiskSize <br>
**Chybová zpráva**: nakonfigurovaná velikost disku není v Azure Backup aktuálně podporovaná. <br>

Operace zálohování může selhat při zálohování virtuálního počítače s velikostí disku větší než 32 TB. Zálohování šifrovaných disků větších než 4 TB se v současné době nepodporuje. Zajistěte, aby velikost disku byla menší nebo rovna podporovanému limitu rozdělením disků.

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress – nepovedlo se inicializovat zálohování, protože v tuto chvíli probíhá jiná operace zálohování.

**Kód chyby**: UserErrorBackupOperationInProgress <br>
**Chybová zpráva**: nejde inicializovat zálohování, protože v tuto chvíli probíhá jiná operace zálohování.<br>

Poslední úloha zálohování se nezdařila, protože probíhá existující úloha zálohování. Novou úlohu zálohování nemůžete spustit, dokud se nedokončí aktuální úloha. Před aktivací nebo plánováním dalších operací zálohování zajistěte, aby byla právě probíhající operace zálohování dokončena. Chcete-li zjistit stav úloh zálohování, proveďte následující kroky:

1. Přihlaste se k Azure Portal vyberte **všechny služby**. Zadejte Recovery Services a vyberte **trezory Recovery Services**. Objeví se seznam trezorů Služeb zotavení.
2. V seznamu trezorů Recovery Services vyberte trezor, ve kterém je zálohování nakonfigurované.
3. V nabídce řídicího panelu trezoru vyberte **úlohy zálohování** , ve kterých se zobrazí všechny úlohy zálohování.
   - Pokud úloha zálohování probíhá, počkejte, než se dokončí, nebo zrušte úlohu zálohování.
     - Úlohu zálohování zrušíte tak, že kliknete pravým tlačítkem na úlohu zálohování a vyberete **Zrušit** nebo použijete [PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob).
   - Pokud jste znovu nakonfigurovali zálohu v jiném trezoru, ujistěte se, že ve starém trezoru nejsou spuštěné žádné úlohy zálohování. Pokud existuje, zrušte úlohu zálohování.
     - Pokud chcete zrušit úlohu zálohování, klikněte pravým tlačítkem na úlohu zálohování a vyberte **Zrušit** nebo použít [PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob) .
4. Opakujte operaci zálohování.

Pokud naplánovaná operace zálohování trvá déle, v konfliktu s další konfigurací zálohování, Projděte si [osvědčené postupy](backup-azure-vms-introduction.md#best-practices), [výkon zálohování](backup-azure-vms-introduction.md#backup-performance)a [aspekty obnovení](backup-azure-vms-introduction.md#backup-and-restore-considerations).

## <a name="usererrorcrpreportedusererror---backup-failed-due-to-an-error-for-details-see-job-error-message-details"></a>UserErrorCrpReportedUserError – Zálohování selhalo kvůli chybě. Podrobnosti najdete v podrobnostech o chybové zprávě úlohy.

**Kód chyby**: UserErrorCrpReportedUserError <br>
**Chybová zpráva**: zálohování se nezdařilo z důvodu chyby. Podrobnosti najdete v podrobnostech o chybové zprávě úlohy.

Tato chyba je hlášena z virtuálního počítače IaaS. Hlavní příčinu problému zjistíte tak, že přejdete do nastavení trezoru Recovery Services. V části **monitorování** vyberte **úlohy zálohování** pro filtrování a zobrazení stavu. Vyberte **selhání** a zkontrolujte podrobnosti příslušné chybové zprávy. Proveďte další akce podle doporučení na stránce s podrobnostmi o chybě.

## <a name="usererrorbcmdatasourcenotpresent---backup-failed-this-virtual-machine-is-not-actively-protected-by-azure-backup"></a>UserErrorBcmDatasourceNotPresent-zálohování se nezdařilo: Tento virtuální počítač není (aktivně) chráněn nástrojem Azure Backup

**Kód chyby**: UserErrorBcmDatasourceNotPresent <br>
**Chybová zpráva**: zálohování se nepovedlo: Tento virtuální počítač není (aktivně) chráněný pomocí Azure Backup.

Ověřte, jestli je zadaný virtuální počítač aktivně (není ve stavu pozastavení) chráněný pomocí Azure Backup. Pokud chcete tento problém vyřešit, ujistěte se, že je virtuální počítač aktivní, a zkuste operaci zopakovat.

## <a name="causes-and-solutions"></a>Příčiny a řešení

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agent je nainstalovaný na virtuálním počítači, ale nereaguje (pro virtuální počítače s Windows).

#### <a name="solution-for-this-error"></a>Řešení pro tuto chybu

Je možné, že agent virtuálního počítače je poškozený nebo se služba zastavila. Opětovná instalace agenta virtuálního počítače pomáhá získat nejnovější verzi. Také pomáhá s restartováním komunikace se službou.

1. Zjistěte, jestli je služba agenta hosta systému Windows Azure spuštěná ve službě VM (Services. msc). Pokuste se restartovat službu agenta hosta systému Windows Azure a zahájit zálohování.
2. Pokud není služba Windows Azure Host agent v části služby viditelná, v Ovládacích panelech klikněte na **programy a funkce** a určete, jestli je nainstalovaná služba Windows Azure Host agent.
3. Pokud se Agent hosta systému Windows Azure zobrazí v části **programy a funkce**, odinstalujte agenta hosta systému Windows Azure.
4. Stáhněte a nainstalujte si [nejnovější verzi MSI agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). K dokončení instalace musíte mít oprávnění správce.
5. Ověřte, že se v části služby zobrazují služby agenta hosta systému Windows Azure.
6. Spusťte zálohování na vyžádání:
   - Na portálu vyberte **Zálohovat nyní**.

Ověřte také, že na virtuálním počítači [je nainstalováno rozhraní Microsoft .net 4,5](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) . Aby agent virtuálního počítače mohl komunikovat se službou, je vyžadován .NET 4,5.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače se systémem Linux).

#### <a name="solution"></a>Řešení

Většina selhání souvisejících s agentem nebo rozšířeními pro virtuální počítače se systémem Linux je způsobena problémy, které mají vliv na zastaralého agenta virtuálního počítače. Pokud chcete tento problém vyřešit, postupujte podle následujících obecných pokynů:

1. Postupujte podle pokynů pro [aktualizaci agenta virtuálního počítače se systémem Linux](../virtual-machines/extensions/update-linux-agent.md).

   > [!NOTE]
   > *Důrazně doporučujeme* , abyste agenta aktualizovali pouze prostřednictvím distribučního úložiště. Nedoporučujeme stahovat kód agenta přímo z GitHubu a aktualizovat ho. Pokud není k dispozici nejnovější agent pro distribuci, obraťte se na podporu distribuce, kde najdete pokyny k jeho instalaci. Chcete-li zjistit nejnovějšího agenta, v úložišti GitHubu, navštivte stránku [agenta Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) .

2. Spuštěním následujícího příkazu zajistěte, aby byl na virtuálním počítači spuštěný agent Azure: `ps -e`

   Pokud proces není spuštěný, restartujte ho pomocí následujících příkazů:

   - Pro Ubuntu: `service walinuxagent start`
   - Pro ostatní distribuce: `service waagent start`

3. [Nakonfigurujte agenta automatického restartování](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Spusťte novou zálohu testu. Pokud chyba přetrvává, shromážděte z virtuálního počítače následující protokoly:

   - /var/lib/waagent/*. XML
   - /var/log/waagent.log
   - /var/log/azure/*

Pokud požadujete podrobné protokolování pro waagent, postupujte takto:

1. V souboru/etc/waagent.conf vyhledejte následující řádek: **Povolit podrobné protokolování (y | n)**
2. Změňte **log. verbose** hodnota z *n* na *y*.
3. Uložte změnu a pak restartujte waagent, a to provedením kroků popsaných výše v této části.

### <a name="vm-agent-configuration-options-are-not-set-for-linux-vms"></a>Možnosti konfigurace VM-Agent nejsou nastavené (pro virtuální počítače se systémem Linux).

Konfigurační soubor (/etc/waagent.conf) řídí akce waagent. Rozšíření možností konfiguračního souboru **. možnost Enable** by měla být nastavena na **y** a **zřizování. Agent** by měl být nastaven na hodnotu **automaticky** , aby mohla zálohování fungovat.
Úplný seznam možností konfiguračního souboru VM-Agent najdete v tématu. <https://github.com/Azure/WALinuxAgent#configuration-file-options>

### <a name="application-control-solution-is-blocking-iaasbcdrextensionexe"></a>Řešení řízení aplikací blokuje IaaSBcdrExtension.exe

Pokud používáte [AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/what-is-applocker) (nebo jiné řešení řízení aplikací) a pravidla jsou založená na vydavateli nebo cestě, může **IaaSBcdrExtension.exe** spustitelný soubor zablokovat spuštění.

#### <a name="solution-to-this-issue"></a>Řešení tohoto problému

Vylučte `/var/lib` cestu nebo spustitelný soubor **IaaSBcdrExtension.exe** z AppLockeru (nebo jiného softwaru pro řízení aplikací.)

### <a name="the-snapshot-status-cant-be-retrieved-or-a-snapshot-cant-be-taken"></a><a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Stav snímku nelze načíst nebo nelze vytvořit snímek.

Zálohování virtuálního počítače se spoléhá na vydání příkazu Snapshot do základního účtu úložiště. Zálohování může selhat buď proto, že nemá přístup k účtu úložiště, nebo protože provádění úlohy snímku je zpožděné.

#### <a name="solution-for-this-issue"></a>Řešení pro tento problém

Následující podmínky mohou způsobit selhání úlohy snímku:

| Příčina | Řešení |
| --- | --- |
| Stav virtuálního počítače je nesprávně hlášený, protože virtuální počítač je vypnutý v protokol RDP (Remote Desktop Protocol) (RDP). | Pokud vypnete virtuální počítač v RDP, zkontrolujte portál a určete, jestli je stav virtuálního počítače správný. Pokud není správná, vypněte virtuální počítač na portálu pomocí možnosti **vypnutí** na řídicím panelu virtuálních počítačů. |
| Virtuální počítač nemůže získat adresu hostitele nebo prostředku infrastruktury z protokolu DHCP. | Služba DHCP musí být povolená v rámci hosta, aby mohla záloha virtuálního počítače IaaS fungovat. Pokud virtuální počítač nemůže získat adresu hostitele nebo prostředku infrastruktury z odpovědi DHCP 245, nemůže stáhnout ani spustit žádná rozšíření. Pokud potřebujete statickou privátní IP adresu, měli byste ji nakonfigurovat přes **Azure Portal** nebo **PowerShell** a zajistěte, aby byla ve virtuálním počítači povolená možnost DHCP. [Přečtěte si další informace](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) o nastavení statické IP adresy pomocí PowerShellu.

### <a name="remove-lock-from-the-recovery-point-resource-group"></a><a name="remove_lock_from_the_recovery_point_resource_group"></a>Odebrat zámek ze skupiny prostředků bodu obnovení

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
2. Přejděte na **možnost všechny prostředky**, vyberte skupinu prostředků kolekce bodů obnovení v následujícím formátu AzureBackupRG_ `<Geo>` _ `<number>` .
3. V části **Nastavení** vyberte **zámky** a zobrazte zámky.
4. Pokud chcete zámek odebrat, vyberte tři tečky a vyberte **Odstranit**.

    ![Odstranit zámek](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean-up-restore-point-collection"></a><a name="clean_up_restore_point_collection"></a> Vyčistit kolekci bodů obnovení

Po odebrání zámku je potřeba vyčistit body obnovení.

Pokud odstraníte skupinu prostředků virtuálního počítače nebo samotný virtuální počítač, zůstanou snímky pro okamžité obnovení na spravovaných discích aktivní a jejich platnost vyprší v závislosti na sadě pro uchovávání informací. Pokud chcete odstranit snímky okamžitého obnovení (Pokud už je nepotřebujete), které jsou uložené v kolekci bodů obnovení, vyčistěte kolekci bodů obnovení podle kroků uvedených níže.

Chcete-li vyčistit body obnovení, postupujte podle kterékoli z těchto metod:<br>

- [Vyčištění kolekce bodů obnovení spuštěním zálohování na vyžádání](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [Vyčistit kolekci bodů obnovení z Azure Portal](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a><a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>Vyčištění kolekce bodů obnovení spuštěním zálohování na vyžádání

Po odebrání zámku aktivujte zálohování na vyžádání. Tato akce zajistí, že body obnovení se automaticky vyčistí. Tato operace na vyžádání se pravděpodobně nezdařila při prvním spuštění. Ale zajistí automatické vyčištění místo ručního odstranění bodů obnovení. Po vyčištění by mělo doběhnout k dalšímu plánovanému zálohování.

> [!NOTE]
> Automatické čištění proběhne po několika hodinách spuštění zálohování na vyžádání. Pokud se naplánované zálohování stále nedaří, zkuste kolekci bodů obnovení ručně odstranit pomocí kroků uvedených [tady](#clean-up-restore-point-collection-from-azure-portal).

#### <a name="clean-up-restore-point-collection-from-azure-portal-br"></a><a name="clean-up-restore-point-collection-from-azure-portal"></a>Vyčistit kolekci bodů obnovení z Azure Portal <br>

Chcete-li ručně vymazat kolekci bodů obnovení, která není smazána z důvodu zámku skupiny prostředků, zkuste provést následující kroky:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
2. V nabídce **centra** vyberte **všechny prostředky**, vyberte skupinu prostředků s následujícím formátem AzureBackupRG_ `<Geo>` _ `<number>` , kde se nachází váš virtuální počítač.

    ![Vyberte skupinu prostředků.](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Vyberte skupina prostředků, zobrazí se podokno **Přehled** .
4. Výběrem možnosti **Zobrazit skryté typy** zobrazíte všechny skryté prostředky. Vyberte kolekce bodů obnovení s následujícím formátem AzureBackupRG_ `<VMName>` _ `<number>` .

    ![Vyberte kolekci bodů obnovení.](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Výběrem možnosti **Odstranit** vyčistíte kolekci bodů obnovení.
6. Opakujte operaci zálohování znovu.

> [!NOTE]
 >Pokud má prostředek (kolekce RP) velký počet bodů obnovení, může se stát, že odstraněním z portálu dojde k vypršení časového limitu a selhání. Jedná se o známý problém s CRP, kde se v určitém čase neodstraní všechny body obnovení a vyprší časový limit operace. Operace odstranění ale obvykle proběhne po dvou nebo třech opakovaných pokusech.
