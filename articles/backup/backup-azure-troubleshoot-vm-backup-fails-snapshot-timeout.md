---
title: Poradce při potížích s agentem a rozšířením
description: Příznaky, příčiny a řešení selhání azure zálohování související s agentem, rozšíření a disky.
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.service: backup
ms.openlocfilehash: a3eedb5440711c7a45a13dcd53dd489c490588fc
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677413"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Poradce při potížích s azure backup selhání: problémy s agentem nebo rozšíření

Tento článek obsahuje kroky pro řešení potíží, které vám pomohou vyřešit chyby zálohování Azure související s komunikací s agentem virtuálního počítače a rozšířením.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="usererrorguestagentstatusunavailable---vm-agent-unable-to-communicate-with-azure-backup"></a><a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable – Agent virtuálního počítače nemůže komunikovat se službou Azure Backup

**Kód chyby:** UživatelErrorGuestAgentStatus není k dispozici. <br>
**Chybová zpráva:** Agent virtuálního počítače nemůže komunikovat se službou Azure Backup<br>

Agent virtuálního počítače Azure může být zastaven, zastaralý, v nekonzistentním stavu nebo není nainstalován. Tyto stavy brání službě Azure Backup v aktivaci snímků.

- **Otevřete portál Azure > podokno nastavení > virtuálních počítači > vlastnosti** > ujistěte **se,** že je **spuštěn** stav virtuálního počítače a **stav agenta** je **připraven.** Pokud je agent virtuálního počítače zastavený nebo je v nekonzistentním stavu, restartujte agenta<br>
  - U virtuálních počítačů se systémem Windows restartujte agenta hosta následujícím [postupem.](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)<br>
  - U virtuálních počítačů s Linuxem restartujte agenta hosta následujícím [postupem.](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- **Otevřete portál Azure > nastavení > nastavení > rozšíření** > ujistěte se, že všechna rozšíření jsou ve stavu **zřizování proběhlo úspěšně.** Pokud ne, postupujte podle následujících [kroků](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) k vyřešení problému.

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError – Nejde komunikovat s agentem virtuálního počítače kvůli stavu snímku

**Kód chyby:** Chyba GuestAgentSnapshotTaskStatusError<br>
**Chybová zpráva**: Nelze komunikovat s agentem virtuálního počítače pro stav snímku <br>

Po registraci a naplánování virtuálního počítače pro službu Zálohování Azure spustí zálohování úlohu komunikací s rozšířením zálohování virtuálních počítači, aby se snímek bodu v čase. Některá z následujících podmínek může zabránit spuštění snímku. Pokud snímek není spuštěn, může dojít k selhání zálohování. V uvedeném pořadí proveďte následující kroky řešení potíží a opakujte operaci:  

**Příčina 1: [Agent je nainstalovaný ve virtuálním virtuálním mísu, ale neodpovídá (pro virtuální aplikace Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**Příčina 2: [Agent nainstalovaný ve virtuálním počítači je zastaralý (pro virtuální počítače s Linuxem)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**Příčina 3: [Stav snímku nelze načíst nebo snímek nelze pořizovat](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**Příčina 4: [Možnosti konfigurace agenta virtuálního počítače nejsou nastaveny (pro virtuální počítače s Linuxem)](#vm-agent-configuration-options-are-not-set-for-linux-vms)**

## <a name="usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state"></a>UserErrorVmProvisioningStateFailed - Virtuální ms se nezdařilo stav zřizování

**Kód chyby**: UživatelErrorVmProvisioningStateFailed<br>
**Chybová zpráva**: Virtuální hod se nezdařil o stavu zřizování.<br>

K této chybě dochází, když jedna z selhání rozšíření vloží virtuální ho do stavu selhání zřizování.<br>**Otevřete portál Azure > nastavení > nastavení > rozšíření > rozšíření stav** a zkontrolujte, jestli všechna rozšíření jsou v **zřizování úspěšnéstavu.** Další informace najdete v [tématu Zřizování stavů](https://docs.microsoft.com/azure/virtual-machines/windows/states-lifecycle#provisioning-states).

- Pokud je rozšíření VMSnapshot ve stavu selhání, klepněte pravým tlačítkem myši na neúspěšné rozšíření a odeberte ji. Aktivujte zálohu na vyžádání. Tato akce přeinstaluje rozšíření a spustí úlohu zálohování.  <br>
- Pokud jakékoli jiné rozšíření je ve stavu selhání, pak může zasahovat do zálohy. Ujistěte se, že tyto problémy rozšíření jsou vyřešeny a opakujte operaci zálohování.
- Pokud je stav zřizování virtuálního počítače ve stavu aktualizace, může bránit zálohování. Ujistěte se, že je v pořádku a opakujte operaci zálohování.

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached – Bylo dosaženo maximálního limitu kolekcí bodu obnovení

**Kód chyby**: UserErrorRpCollectionLimitReached <br>
**Chybová zpráva**: Bylo dosaženo maximálního limitu kolekce bodů obnovení. <br>

- K tomuto problému může dojít, pokud existuje zámek na skupinu prostředků bodu obnovení, který brání automatickému vyčištění bodů obnovení.
- K tomuto problému může dojít také v případě, že se aktivuje více záloh za den. V současné době doporučujeme pouze jednu zálohu za den, protože body okamžitého obnovení jsou zachovány po dobu 1 až 5 dnů na nakonfigurované uchovávání snímků a pouze 18 okamžitých rps může být přidruženo k virtuálnímu počítače v daném okamžiku. <br>
- Počet bodů obnovení v kolekcích bodů obnovení a skupin y prostředků pro virtuální ho disponuje hodnotou 18. Chcete-li vytvořit nový bod obnovení, odstraňte existující body obnovení.

Doporučená akce:<br>
Chcete-li tento problém vyřešit, odeberte zámek ve skupině prostředků virtuálního počítače a opakujte operaci ke spuštění vyčištění.
> [!NOTE]
> Služba zálohování vytvoří samostatnou skupinu prostředků než skupinu prostředků virtuálního počítače pro uložení kolekce bodů obnovení. Zákazníkům se doporučuje, aby neuzamykali skupinu prostředků vytvořenou pro použití službou Backup. Formát pojmenování skupiny prostředků vytvořené službou`<Geo>``<number>` Backup je: AzureBackupRG_ _ Např.: AzureBackupRG_northeurope_1

**Krok 1: [Odebrání zámku ze skupiny prostředků bodu obnovení](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Krok 2: [Vyčistit shromažďování bodů obnovení](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured - Zálohování nemá dostatečná oprávnění k trezoru klíčů pro zálohování šifrovaných virtuálních počítače

**Kód chyby**: UživatelErrorKeyvaultPermissionsNotConfigured <br>
**Chybová zpráva**: Zálohování nemá dostatečná oprávnění k trezoru klíčů pro zálohování šifrovaných virtuálních počítače. <br>

Aby byla operace zálohování úspěšná na šifrovaných virtuálních počítačích, musí mít oprávnění k přístupu k trezoru klíčů. Oprávnění lze nastavit prostřednictvím [portálu Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) nebo prostřednictvím [Prostředí PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection).

## <a name="extensionsnapshotfailednonetwork---snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a><a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork – Operace vytvoření snímku selhala, protože virtuální počítač není připojený k síti

**Kód chyby:** ExtensionSnapshotFailedNoNetwork<br>
**Chybová zpráva:** Operace snímku se nezdařila z důvodu žádného připojení k síti ve virtuálním počítači<br>

Po registraci a naplánování virtuálního počítače pro službu Zálohování Azure spustí zálohování úlohu komunikací s rozšířením zálohování virtuálních počítači, aby se snímek bodu v čase. Některá z následujících podmínek může zabránit spuštění snímku. Pokud snímek není spuštěn, může dojít k selhání zálohování. Proveďte následující krok řešení potíží a opakujte operaci:

**[Stav snímku nelze načíst nebo snímek nelze pořizovat](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  

## <a name="extensionoperationfailedformanageddisks---vmsnapshot-extension-operation-failed"></a><a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks - Operace rozšíření VMSnapshot se nezdařila.

**Kód chyby**: ExtensionOperationFailedForManagedDisks <br>
**Chybová zpráva**: Operace rozšíření VMSnapshot se nezdařila.<br>

Po registraci a naplánování virtuálního počítače pro službu Zálohování Azure spustí zálohování úlohu komunikací s rozšířením zálohování virtuálních počítači, aby se snímek bodu v čase. Některá z následujících podmínek může zabránit spuštění snímku. Pokud snímek není spuštěn, může dojít k selhání zálohování. V uvedeném pořadí proveďte následující kroky řešení potíží a opakujte operaci:  
**Příčina 1: [Stav snímku nelze načíst nebo snímek nelze pořizovat](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Příčina 2: [Agent je nainstalovaný ve virtuálním virtuálním mísu, ale neodpovídá (pro virtuální aplikace Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Příčina 3: [Agent nainstalovaný ve virtuálním počítači je zastaralý (pro virtuální počítače s Linuxem)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 – Služba Backup selhala s vnitřní chybou

**Kód chyby**: BackUpOperationFailed / BackUpOperationFailedV2 <br>
**Chybová zpráva**: Zálohování se nezdařilo s vnitřní chybou - Opakujte operaci během několika minut. <br>

Po registraci a naplánování virtuálního počítače pro službu Zálohování Azure, zálohování zahájí úlohu komunikací s rozšířením zálohování virtuálního počítače, aby snímek bodu v čase. Některá z následujících podmínek může zabránit spuštění snímku. Pokud snímek není spuštěn, může dojít k selhání zálohování. V uvedeném pořadí proveďte následující kroky řešení potíží a opakujte operaci:  
**Příčina 1: [Agent nainstalovaný ve virtuálním mísu, ale neodpovídá (pro virtuální aplikace Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Příčina 2: [Agent nainstalovaný ve virtuálním počítači je zastaralý (pro virtuální počítače s Linuxem)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Příčina 3: [Stav snímku nelze načíst nebo snímek nelze pořizovat](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Příčina 4: [Služba zálohování nemá oprávnění k odstranění starých bodů obnovení z důvodu uzamčení skupiny prostředků](#remove_lock_from_the_recovery_point_resource_group)**<br>

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize – Nakonfigurovaná velikost disku není aktuálně podporována službou Azure Backup

**Kód chyby**: UserErrorUnsupportedDiskSize <br>
**Chybová zpráva**: Nakonfigurovaná velikost disku není aktuálně podporována službou Azure Backup. <br>

Operace zálohování může selhat při zálohování virtuálního počítače s velikostí disku větší než 32 TB. Zálohování šifrovaných disků větších než 4 TB není v současné době podporováno. Rozdělením disků se ujistěte, že velikost disku je menší nebo rovna podporovanému limitu.

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress - Nelze zahájit zálohování, protože právě probíhá jiná operace zálohování.

**Kód chyby**: UserErrorBackupOperationInProgress <br>
**Chybová zpráva**: Nelze zahájit zálohování, protože právě probíhá další operace zálohování.<br>

Vaše nedávná úloha zálohování se nezdařila, protože probíhá existující úloha zálohování. Novou úlohu zálohování nelze spustit, dokud nebude dokončena aktuální úloha. Před aktivací nebo plánováním dalších operací zálohování se ujistěte, že právě probíhající operace zálohování je dokončena. Chcete-li zkontrolovat stav úloh zálohování, postupujte takto:

1. Přihlaste se k portálu Azure, klikněte na **všechny služby**. Zadejte Recovery Services a klikněte na **Trezory služby Recovery Services**. Objeví se seznam trezorů Recovery Services.
2. Ze seznamu trezorů služeb obnovení vyberte trezor, ve kterém je záloha nakonfigurována.
3. V nabídce řídicího panelu úschovny klikněte na **Položku Zálohovat úlohy,** zobrazí se všechny úlohy zálohování.
   - Pokud probíhá úloha zálohování, počkejte na její dokončení nebo zrušení zálohovací úlohy.
     - Chcete-li úlohu zálohování zrušit, klepněte pravým tlačítkem myši na úlohu zálohování a klepněte na příkaz **Storno** nebo použijte [prostředí PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0).
   - Pokud jste překonfigurovali zálohu v jiném trezoru, ujistěte se, že ve starém trezoru nejsou spuštěny žádné úlohy zálohování. Pokud existuje, zrušte úlohu zálohování.
     - Pokud chcete zrušit úlohu zálohování, klikněte na ni pravým tlačítkem a pak klikněte na **Zrušit** nebo použijte [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0).
4. Opakujte operaci zálohování.

Pokud plánovaná operace zálohování trvá déle a je v konfliktu s další konfigurací zálohování, zkontrolujte [nejvhodnější informace ,](backup-azure-vms-introduction.md#best-practices)výkon [zálohování](backup-azure-vms-introduction.md#backup-performance)a [obnovení](backup-azure-vms-introduction.md#backup-and-restore-considerations).

## <a name="usererrorcrpreportedusererror---backup-failed-due-to-an-error-for-details-see-job-error-message-details"></a>UserErrorCrpReportedUserError - Zálohování se nezdařilo z důvodu chyby. Podrobnosti naleznete v tématu Podrobnosti o chybové zprávě projektu

**Kód chyby**: Chyba UživateleCrpReportedUserError <br>
**Chybová zpráva**: Zálohování se nezdařilo z důvodu chyby. Podrobnosti naleznete v tématu Podrobnosti o chybové zprávě úlohy.

Tato chyba je hlášena z virtuálního ms IaaS. Chcete-li zjistit hlavní příčinu problému, přejděte do nastavení trezoru služby Recovery Services. V části **Monitorování** vyberte **Možnost Zálohovací úlohy,** kterou chcete filtrovat a zobrazit stav. Kliknutím na **Možnost Selhání** zkontrolujte podrobnosti základní chybové zprávy. Provést další akce podle doporučení na stránce s podrobnostmi o chybě.

## <a name="usererrorbcmdatasourcenotpresent---backup-failed-this-virtual-machine-is-not-actively-protected-by-azure-backup"></a>UserErrorBcmDatasourceNotPresent – zálohování se nezdařilo: Tento virtuální počítač není (aktivně) chráněn službou Azure Backup

**Kód chyby**: UserErrorBcmDatasourceNotPresent <br>
**Chybová zpráva**: Zálohování se nezdařilo: Tento virtuální počítač není (aktivně) chráněn službou Azure Backup.

Zkontrolujte, jestli je daný virtuální počítač aktivně (není ve stavu pozastavení) chráněný službou Azure Backup. Chcete-li tento problém překonat, ujistěte se, že virtuální počítač je aktivní a opakujte operaci.

## <a name="causes-and-solutions"></a>Příčiny a řešení

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agent je nainstalovaný ve virtuálním virtuálním mísu, ale neodpovídá (pro virtuální aplikace Windows)

#### <a name="solution"></a>Řešení

Agent virtuálního soudu pravděpodobně byl poškozen nebo služba byla zastavena. Přeinstalace agenta virtuálního zařízení pomáhá získat nejnovější verzi. Pomáhá také restartovat komunikaci se službou.

1. Zjistěte, jestli služba Agent hosta Windows Azure běží ve službách virtuálních zařízení (services.msc). Pokuste se restartovat službu Windows Azure Guest Agent a zahájit zálohování.
2. Pokud služba Agent hosta Windows Azure není viditelná ve službách, přejděte v Ovládacích panelech do **části Programy a funkce a** zjistěte, jestli je nainstalovaná služba Agent hosta Windows Azure.
3. Pokud se agent hosta Windows Azure zobrazí v **části Programy a funkce**, odinstalujte agenta hosta Windows Azure.
4. Stáhněte a nainstalujte [nejnovější verzi agenta MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). K dokončení instalace musíte mít práva správce.
5. Ověřte, zda se služby Agent hosta Windows Azure zobrazují ve službách.
6. Spusťte zálohu na vyžádání:
   - Na portálu vyberte **Možnost Zálohovat nyní**.

Ověřte také, že [microsoft .NET 4.5 je nainstalován](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ve virtuálním ms. .NET 4.5 je vyžadováno pro agenta virtuálního soudu ke komunikaci se službou.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agent nainstalovaný ve virtuálním počítači je zastaralý (pro virtuální počítače s Linuxem)

#### <a name="solution"></a>Řešení

Většina selhání souvisejících s agentem nebo rozšíření mů e pro virtuální počítače s Linuxem jsou způsobeny problémy, které ovlivňují zastaralé hocha virtuálního počítače. Chcete-li tento problém vyřešit, postupujte podle těchto obecných pokynů:

1. Postupujte podle pokynů pro [aktualizaci agenta virtuálního počítače s Linuxem](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > *Důrazně doporučujeme* aktualizovat agenta pouze prostřednictvím distribučního úložiště. Nedoporučujeme stahovat kód agenta přímo z GitHubu a aktualizovat ho. Pokud nejnovější agent pro vaši distribuci není k dispozici, obraťte se na podporu distribuce pokyny, jak ji nainstalovat. Pokud chcete vyhledat nejnovějšího agenta, přejděte na stránku [agenta Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) v úložišti GitHub.

2. Ujistěte se, že agent Azure běží na virtuálním počítači spuštěním následujícího příkazu:`ps -e`

   Pokud proces není spuštěn, restartujte jej pomocí následujících příkazů:

   - Pro Ubuntu:`service walinuxagent start`
   - Pro ostatní distribuce:`service waagent start`

3. [Nakonfigurujte agenta automatického restartování](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Spusťte novou testovací zálohu. Pokud selhání přetrvává, shromažďovat následující protokoly z virtuálního soudu:

   - /var/lib/waagent/*.xml
   - /var/log/waagent.log
   - /var/log/azure/*

Pokud požadujete podrobné protokolování pro waagent, postupujte takto:

1. V souboru /etc/waagent.conf vyhledejte následující řádek: **Povolit podrobné protokolování (y|n)**
2. Změňte hodnotu **Logs.Verbose** z *n* na *y*.
3. Uložte změnu a potom restartujte waagent dokončením kroků popsaných výše v této části.

### <a name="vm-agent-configuration-options-are-not-set-for-linux-vms"></a>Možnosti konfigurace v agenta virtuálního počítače nejsou nastaveny (pro virtuální počítače s Linuxem)

Konfigurační soubor (/etc/waagent.conf) řídí akce waagent. Možnosti konfigurace **Extensions.Enable** a **Provisioning.Agent** by měla být **nastavena** na y pro zálohování pracovat.
Úplný seznam možností konfiguračního souboru agenta v systému v systému v systému v p.,<https://github.com/Azure/WALinuxAgent#configuration-file-options>

### <a name="the-snapshot-status-cant-be-retrieved-or-a-snapshot-cant-be-taken"></a><a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Stav snímku nelze načíst nebo snímek nelze pořizovat

Záloha virtuálního počítače závisí na vydání příkazu snímek na základní účet úložiště. Zálohování může selhat buď proto, že nemá přístup k účtu úložiště, nebo proto, že provádění úlohy snímku je zpožděno.

#### <a name="solution"></a>Řešení

Následující podmínky mohou způsobit selhání úlohy snímku:

| Příčina | Řešení |
| --- | --- |
| Stav virtuálního počítače je hlášena nesprávně, protože virtuální počítač je vypnutý v protokolu RDP (RdP). | Pokud vypnete virtuální ho v RDP, zkontrolujte portál a zjistěte, zda je stav virtuálního aplikace správný. Pokud to není správné, vypněte virtuální počítač na portálu pomocí **možnosti Vypnutí** na řídicím panelu virtuálního počítače. |
| Virtuální ho virtuálního materiálu nemůže získat adresu hostitele nebo prostředků infrastruktury ze služby DHCP. | Aby záloha virtuálního počítače IaaS fungovala, musí být uvnitř hosta povolena služba DHCP. Pokud virtuální hod nemůže získat adresu hostitele nebo prostředků infrastruktury z odpovědi DHCP 245, nemůže stáhnout ani spustit žádná rozšíření. Pokud potřebujete statickou privátní IP adresu, měli byste ji nakonfigurovat prostřednictvím **portálu Azure nebo** **prostředí PowerShell** a ujistěte se, že je povolená možnost DHCP uvnitř virtuálního počítače. [Přečtěte si další informace](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) o nastavení statické ADRESY IP pomocí Prostředí PowerShell.

### <a name="remove-lock-from-the-recovery-point-resource-group"></a><a name="remove_lock_from_the_recovery_point_resource_group"></a>Odebrání zámku ze skupiny prostředků bodu obnovení

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Přejděte na **možnost Všechny zdroje**, vyberte skupinu`<Geo>``<number>`prostředků kolekce bodů obnovení v následujícím formátu AzureBackupRG_ _ .
3. V části **Nastavení** vyberte **Zámky,** chcete-li zámky zobrazit.
4. Chcete-li zámek odebrat, vyberte tři tečky a klepněte na **tlačítko Odstranit**.

    ![Odstranit zámek](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean-up-restore-point-collection"></a><a name="clean_up_restore_point_collection"></a>Vyčistit shromažďování bodů obnovení

Po vyjmutí zámku je třeba vyčistit body obnovení.

Pokud odstraníte skupinu prostředků virtuálního počítače nebo samotného virtuálního počítače, okamžité obnovení snímků spravovaných disků zůstane aktivní a vyprší podle sady uchovávání informací. Chcete-li odstranit okamžité obnovení snímky (pokud je již nepotřebujete), které jsou uloženy v kolekci bodů obnovení, vyčistěte kolekci bodů obnovení podle níže uvedených kroků.

Chcete-li vyčistit body obnovení, postupujte podle některé z metod:<br>

- [Vyčistit shromažďování bodů obnovení spuštěním zálohování na vyžádání](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [Vyčištění kolekce bodů obnovení z webu Azure Portal](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a><a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>Vyčistit shromažďování bodů obnovení spuštěním zálohování na vyžádání

Po odebrání zámku spusťte zálohování na vyžádání. Tato akce zajistí automatické vyčištění bodů obnovení. Očekávejte, že tato operace na vyžádání selže poprvé; však zajistí automatické vyčištění namísto ručního odstranění bodů obnovení. Po vyčištění by měla být úspěšná další naplánovaná záloha.

> [!NOTE]
> Automatické vyčištění proběhne po několika hodinách spuštění zálohy na vyžádání. Pokud se naplánované zálohování stále nezdaří, zkuste ručně smažit kolekci bodů obnovení pomocí [kroků uvedených zde](#clean-up-restore-point-collection-from-azure-portal).

#### <a name="clean-up-restore-point-collection-from-azure-portal-br"></a><a name="clean-up-restore-point-collection-from-azure-portal"></a>Vyčištění kolekce bodů obnovení z webu Azure Portal <br>

Chcete-li ručně vymazat kolekci bodů obnovení, která není vymazána z důvodu uzamčení ve skupině prostředků, vyzkoušejte následující kroky:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. V nabídce **Hub** klikněte na **Všechny prostředky**,`<Geo>`vyberte skupinu prostředků s následujícím formátem AzureBackupRG_ _`<number>` kde se virtuální počítač nachází.

    ![Odstranit zámek](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Klikněte na skupina prostředků, zobrazí se podokno **Přehled.**
4. Vyberte **Možnost Zobrazit skryté typy,** chcete-li zobrazit všechny skryté zdroje. Vyberte kolekce bodů obnovení v`<VMName>`následujícím`<number>`formátu AzureBackupRG_ _ .

    ![Odstranit zámek](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Chcete-li vyčistit kolekci bodů obnovení, klepněte na **tlačítko Odstranit.**
6. Opakujte operaci zálohování.

> [!NOTE]
 >Pokud prostředek (KOLEKCE RP) má velký počet bodů obnovení, jejich odstranění z portálu může časový rozsah a nezdaří. Jedná se o známý problém CRP, kde všechny body obnovení nejsou odstraněny ve stanoveném čase a provozní časový čas. operace odstranění však obvykle úspěšné po 2 nebo 3 opakování.
