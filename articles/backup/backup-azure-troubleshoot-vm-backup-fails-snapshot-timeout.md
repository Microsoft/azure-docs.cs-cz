---
title: Řešení potíží s agenty a rozšířením
description: Příznaky, příčiny a řešení chyb Azure Backup souvisejících s agentem, rozšířením a disky.
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: c4ee8cbeeec21c4af0cc3a7fd83844bc8c676add
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172591"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Řešení potíží s Azure Backup Chyba: problémy s agentem nebo rozšířením

Tento článek popisuje kroky pro řešení potíží, které vám pomohou vyřešit chyby Azure Backup související s komunikací s agentem a rozšířením virtuálního počítače.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable – agent virtuálního počítače nemůže komunikovat s Azure Backup

**Kód chyby**: UserErrorGuestAgentStatusUnavailable <br>
**Chybová zpráva**: Agent virtuálního počítače nemůže komunikovat s Azure Backup<br>

Agent virtuálního počítače Azure se může zastavit, zastaralá, je v nekonzistentním stavu nebo není nainstalovaný a zabránit tomu, aby služba Azure Backup spustila snímky.  

- Pokud je agent virtuálního počítače zastavený nebo je v nekonzistentním stavu, **restartujte agenta** a zkuste operaci zálohování zopakovat (vyzkoušejte zálohování na vyžádání). Postup restartování najdete v tématu věnovaném [virtuálním počítačům s Windows](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) nebo [virtuálním počítačům s Linuxem](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent).
- Pokud není agent virtuálního počítače nainstalovaný nebo je zastaralý, nainstalujte nebo aktualizujte agenta virtuálního počítače a zkuste operaci zálohování zopakovat. Postup instalace nebo aktualizace agenta najdete v tématu [virtuální počítače s Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) nebo [virtuální počítače](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent)se systémem Linux.  

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError – nepovedlo se komunikovat s agentem virtuálního počítače pro stav snímku.

**Kód chyby**: GuestAgentSnapshotTaskStatusError<br>
**Chybová zpráva**: nepovedlo se komunikovat s agentem virtuálního počítače pro stav snímku. <br>

Po registraci a naplánování virtuálního počítače pro službu Azure Backup služba Backup inicializuje úlohu prostřednictvím komunikace s rozšířením zálohy virtuálního počítače, aby se pomohlo vytvořit snímek v daném časovém okamžiku. Některé z následujících podmínek mohou zabránit spuštění snímku. Pokud se snímek neaktivuje, může dojít k selhání zálohování. Proveďte následující kroky pro řešení potíží v uvedeném pořadí a opakujte operaci:  

**Příčina 1: [Agent je nainstalovaný na virtuálním počítači, ale nereaguje (pro virtuální počítače s Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) .**  

**Příčina 2: [Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače se systémem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) .**

**Příčina 3: [nejde načíst stav snímku nebo nejde udělat snímek](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken) .**

**Příčina 4: [rozšíření zálohování se nepodařilo aktualizovat nebo načíst](#the-backup-extension-fails-to-update-or-load) .**

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached – dosáhlo se maximálního limitu kolekce bodů obnovení.

**Kód chyby**: UserErrorRpCollectionLimitReached <br>
**Chybová zpráva**: dosáhlo se maximálního limitu kolekce bodů obnovení. <br>

- K tomuto problému může dojít, pokud je zámek skupiny prostředků bodu obnovení znemožněn automatickým čištěním bodů obnovení.
- K tomuto problému může dojít také v případě, že je aktivováno více záloh za den. V současné době doporučujeme jenom jednu zálohu za den, protože body okamžitého obnovení se uchovávají po dobu 1-5 dnů na základě nakonfigurovaného uchování snímku a k virtuálnímu počítači se dá v jednom okamžiku přidružit jenom 18 RPs. <br>

Doporučená akce:<br>
Pokud chcete tento problém vyřešit, odeberte zámek pro skupinu prostředků virtuálního počítače a potom operaci spusťte znovu, aby se aktivovala operace vyčištění.
> [!NOTE]
> Služba Backup vytvoří samostatnou skupinu prostředků, než je skupina prostředků virtuálního počítače pro uložení kolekce bodů obnovení. Zákazníkům se doporučuje, aby nezamkli skupinu prostředků vytvořenou pro použití v rámci služby zálohování. Formát názvů pro skupinu prostředků vytvořenou službou zálohování: AzureBackupRG_`<Geo>`_`<number>` například: AzureBackupRG_northeurope_1

**Krok 1: [Odebrání zámku ze skupiny prostředků bodu obnovení](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Krok 2: [Vyčištění kolekce bodů obnovení](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured-Backup nemá dostatečná oprávnění k trezoru klíčů pro zálohování šifrovaných virtuálních počítačů.

**Kód chyby**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Chybová zpráva**: zálohování nemá dostatečná oprávnění k trezoru klíčů pro zálohování šifrovaných virtuálních počítačů. <br>

Aby operace zálohování proběhla úspěšně na šifrovaných virtuálních počítačích, musí mít oprávnění pro přístup k trezoru klíčů. To se dá udělat pomocí [Azure Portal](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) nebo přes [PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection).

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>Operace ExtensionSnapshotFailedNoNetwork-Snapshot selhala, protože ve virtuálním počítači není síťové připojení.

**Kód chyby**: ExtensionSnapshotFailedNoNetwork<br>
**Chybová zpráva**: operace snímku selhala, protože ve virtuálním počítači není síťové připojení.<br>

Po registraci a naplánování virtuálního počítače pro službu Azure Backup služba Backup inicializuje úlohu prostřednictvím komunikace s rozšířením zálohy virtuálního počítače, aby se pomohlo vytvořit snímek v daném časovém okamžiku. Některé z následujících podmínek mohou zabránit spuštění snímku. Pokud se snímek neaktivuje, může dojít k selhání zálohování. Proveďte následující kroky pro řešení potíží v uvedeném pořadí a opakujte operaci:

**Příčina 1: nelze [načíst stav snímku nebo nelze](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken) pořídit snímek.**  
**Příčina 2: [rozšíření zálohování se nepodařilo aktualizovat nebo načíst](#the-backup-extension-fails-to-update-or-load)**  
**Příčina 3: [virtuální počítač nemá přístup k Internetu](#the-vm-has-no-internet-access)**

## <a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>Operace rozšíření ExtensionOperationFailedForManagedDisks-VMSnapshot se nezdařila.

**Kód chyby**: ExtensionOperationFailedForManagedDisks <br>
**Chybová zpráva**: operace rozšíření VMSnapshot se nezdařila.<br>

Po registraci a naplánování virtuálního počítače pro službu Azure Backup služba Backup inicializuje úlohu prostřednictvím komunikace s rozšířením zálohy virtuálního počítače, aby se pomohlo vytvořit snímek v daném časovém okamžiku. Některé z následujících podmínek mohou zabránit spuštění snímku. Pokud se snímek neaktivuje, může dojít k selhání zálohování. Proveďte následující kroky pro řešení potíží v uvedeném pořadí a opakujte operaci:  
**Příčina 1: nelze [načíst stav snímku nebo nelze](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken) pořídit snímek.**  
**Příčina 2: [rozšíření zálohování se nepodařilo aktualizovat nebo načíst](#the-backup-extension-fails-to-update-or-load)**  
**Příčina 3: [Agent je nainstalovaný na virtuálním počítači, ale nereaguje (pro virtuální počítače s Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) .**  
**Příčina 4: [Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače se systémem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) .**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed/BackUpOperationFailedV2 – zálohování se nepovedlo, došlo k vnitřní chybě.

**Kód chyby**: BackUpOperationFailed/BackUpOperationFailedV2 <br>
**Chybová zpráva**: Zálohování selhalo s interní chybou – zkuste prosím operaci zopakovat za pár minut. <br>

Po registraci a naplánování virtuálního počítače pro službu Azure Backup služba Backup inicializuje úlohu prostřednictvím komunikace s rozšířením zálohy virtuálního počítače, aby se pomohlo vytvořit snímek v daném časovém okamžiku. Některé z následujících podmínek mohou zabránit spuštění snímku. Pokud se snímek neaktivuje, může dojít k selhání zálohování. Proveďte následující kroky pro řešení potíží v uvedeném pořadí a opakujte operaci:  
**Příčina 1: [Agent nainstalovaný na virtuálním počítači, ale nereaguje (pro virtuální počítače s Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Příčina 2: [Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače se systémem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) .**  
**Příčina 3: [nejde načíst stav snímku nebo nejde udělat snímek](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken) .**  
**Příčina 4: [rozšíření zálohování se nepodařilo aktualizovat nebo načíst](#the-backup-extension-fails-to-update-or-load) .**  
**Příčina 5: Služba zálohování nemá oprávnění odstranit staré body obnovení z důvodu zámku skupiny prostředků.** <br>
**Příčina 6: [virtuální počítač nemá přístup k Internetu](#the-vm-has-no-internet-access)**

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize – Azure Backup aktuálně není podporována nakonfigurovaná velikost disku.

**Kód chyby**: UserErrorUnsupportedDiskSize <br>
**Chybová zpráva**: nakonfigurovaná velikost disku není v Azure Backup aktuálně podporovaná. <br>

Operace zálohování může selhat při zálohování virtuálního počítače s velikostí disku větší než 32 TB. Zálohování šifrovaných disků větších než 4 TB se taky v současnosti nepodporuje. Zajistěte, aby velikost disku byla menší nebo rovna podporovanému limitu rozdělením disků.

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress – nepovedlo se inicializovat zálohování, protože v tuto chvíli probíhá jiná operace zálohování.

**Kód chyby**: UserErrorBackupOperationInProgress <br>
**Chybová zpráva**: nejde inicializovat zálohování, protože v tuto chvíli probíhá jiná operace zálohování.<br>

Poslední úloha zálohování se nezdařila, protože probíhá existující úloha zálohování. Novou úlohu zálohování nemůžete spustit, dokud se nedokončí aktuální úloha. Před aktivací nebo plánováním dalších operací zálohování zajistěte, aby byla právě probíhající operace zálohování dokončena. Chcete-li zjistit stav úloh zálohování, proveďte následující kroky:

1. Přihlaste se k Azure Portal klikněte na **všechny služby**. Zadejte Recovery Services a klikněte na **Trezory služby Recovery Services**. Objeví se seznam trezorů Recovery Services.
2. V seznamu trezorů služby Recovery Services vyberte trezor, ve kterém je zálohování nakonfigurované.
3. V nabídce řídicího panelu trezoru klikněte na **úlohy zálohování** . zobrazí se všechny úlohy zálohování.
   - Pokud úloha zálohování probíhá, počkejte, než se dokončí, nebo zrušte úlohu zálohování.
     - Úlohu zálohování zrušíte tak, že kliknete pravým tlačítkem na úlohu zálohování a kliknete na **Zrušit** nebo použijete [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0).
   - Pokud jste znovu nakonfigurovali zálohu v jiném trezoru, ujistěte se, že ve starém trezoru nejsou spuštěné žádné úlohy zálohování. Pokud existuje, zrušte úlohu zálohování.
     - Pokud chcete zrušit úlohu zálohování, klikněte na ni pravým tlačítkem a pak klikněte na **Zrušit** nebo použijte [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0).
4. Opakujte operaci zálohování.

Pokud naplánovaná operace zálohování trvá déle, v konfliktu s další konfigurací zálohování, Projděte si [osvědčené postupy](backup-azure-vms-introduction.md#best-practices), [výkon zálohování](backup-azure-vms-introduction.md#backup-performance)a [aspekty obnovení](backup-azure-vms-introduction.md#backup-and-restore-considerations).

## <a name="causes-and-solutions"></a>Příčiny a řešení

### <a name="the-vm-has-no-internet-access"></a>Virtuální počítač nemá přístup k Internetu.

Za požadavek nasazení nemá virtuální počítač přístup k Internetu. Nebo může mít omezení, která brání přístupu k infrastruktuře Azure.

Pro správné fungování rozšíření Backup vyžaduje připojení k veřejným IP adresám Azure. Rozšíření odesílá příkazy koncovému bodu služby Azure Storage (adresa URL protokolu HTTPs) pro správu snímků virtuálního počítače. Pokud rozšíření nemá přístup k veřejnému Internetu, zálohování nakonec neproběhne úspěšně.

#### <a name="solution"></a>Řešení

Informace o vyřešení problému v síti najdete v tématu [vytvoření připojení k síti](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agent je nainstalovaný na virtuálním počítači, ale nereaguje (pro virtuální počítače s Windows).

#### <a name="solution"></a>Řešení

Je možné, že agent virtuálního počítače je poškozený nebo se služba zastavila. Opětovná instalace agenta virtuálního počítače pomáhá získat nejnovější verzi. Také pomáhá s restartováním komunikace se službou.

1. Zjistěte, jestli je služba agenta hosta systému Windows Azure spuštěná ve službě VM (Services. msc). Pokuste se restartovat službu agenta hosta systému Windows Azure a zahájit zálohování.
2. Pokud není služba Windows Azure Host agent v části služby viditelná, v Ovládacích panelech klikněte na **programy a funkce** a určete, jestli je nainstalovaná služba Windows Azure Host agent.
3. Pokud se Agent hosta systému Windows Azure zobrazí v části **programy a funkce**, odinstalujte agenta hosta systému Windows Azure.
4. Stáhněte a nainstalujte si [nejnovější verzi MSI agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). K dokončení instalace musíte mít oprávnění správce.
5. Ověřte, že se v části služby zobrazují služby agenta hosta systému Windows Azure.
6. Spusťte zálohování na vyžádání:
   - Na portálu vyberte **Zálohovat nyní**.

Ověřte také, že na virtuálním počítači [je nainstalováno rozhraní Microsoft .net 4,5](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) . Aby agent virtuálního počítače mohl komunikovat se službou, je vyžadován .NET 4,5.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače se systémem Linux).

#### <a name="solution"></a>Řešení

Většina selhání souvisejících s agentem nebo rozšířeními pro virtuální počítače se systémem Linux je způsobena problémy, které mají vliv na zastaralého agenta virtuálního počítače. Pokud chcete tento problém vyřešit, postupujte podle následujících obecných pokynů:

1. Postupujte podle pokynů pro [aktualizaci agenta virtuálního počítače se systémem Linux](../virtual-machines/linux/update-agent.md).

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

### <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Stav snímku nelze načíst nebo nelze vytvořit snímek.

Zálohování virtuálního počítače se spoléhá na vydání příkazu Snapshot do základního účtu úložiště. Zálohování může selhat buď proto, že nemá přístup k účtu úložiště, nebo protože provádění úlohy snímku je zpožděné.

#### <a name="solution"></a>Řešení

Následující podmínky mohou způsobit selhání úlohy snímku:

| Příčina | Řešení |
| --- | --- |
| Stav virtuálního počítače je nesprávně hlášený, protože virtuální počítač je vypnutý v protokol RDP (Remote Desktop Protocol) (RDP). | Pokud vypnete virtuální počítač v RDP, zkontrolujte portál a určete, jestli je stav virtuálního počítače správný. Pokud není správná, vypněte virtuální počítač na portálu pomocí možnosti **vypnutí** na řídicím panelu virtuálních počítačů. |
| Virtuální počítač nemůže získat adresu hostitele nebo prostředku infrastruktury z protokolu DHCP. | Služba DHCP musí být povolená v rámci hosta, aby mohla záloha virtuálního počítače IaaS fungovat. Pokud virtuální počítač nemůže získat adresu hostitele nebo prostředku infrastruktury z odpovědi DHCP 245, nemůže stáhnout ani spustit žádná rozšíření. Pokud potřebujete statickou privátní IP adresu, měli byste ji nakonfigurovat přes **Azure Portal** nebo **PowerShell** a zajistěte, aby byla ve virtuálním počítači povolená možnost DHCP. [Přečtěte si další informace](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) o nastavení statické IP adresy pomocí PowerShellu.

### <a name="the-backup-extension-fails-to-update-or-load"></a>Rozšíření zálohování se nepodařilo aktualizovat nebo načíst.

Pokud se rozšíření nedají načíst, zálohování se nepovede, protože nejde udělat snímek.

#### <a name="solution"></a>Řešení

Odinstalujte rozšíření pro vynucení opětovného načtení rozšíření VMSnapshot. Další pokus o zálohování znovu načte rozšíření.

Postup při odinstalaci rozšíření:

1. V [Azure Portal](https://portal.azure.com/)přejdete na virtuální počítač, u kterého dochází k chybě zálohování.
2. Vyberte **nastavení**.
3. Vyberte **Extensions** (Rozšíření).
4. Vyberte **rozšíření VMSnapshot**.
5. Vyberte **odinstalovat**.

Pokud se u virtuálního počítače se systémem Linux v Azure Portal nezobrazuje rozšíření VMSnapshot, [aktualizujte agenta Azure Linux](../virtual-machines/linux/update-agent.md)a spusťte zálohování.

Provedením těchto kroků dojde k přeinstalování rozšíření během příštího zálohování.

### <a name="remove_lock_from_the_recovery_point_resource_group"></a>Odebrat zámek ze skupiny prostředků bodu obnovení

1. Přihlásit se na [Azure Portal](https://portal.azure.com/).
2. Přejděte na **možnost všechny prostředky**, vyberte skupinu prostředků kolekce bodů obnovení v následujícím formátu AzureBackupRG_`<Geo>`_`<number>`.
3. V části **Nastavení** vyberte **zámky** a zobrazte zámky.
4. Pokud chcete zámek odebrat, vyberte tři tečky a klikněte na **Odstranit**.

    ![Odstranit zámek](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a>Vyčistit kolekci bodů obnovení

Po odebrání zámku je potřeba vyčistit body obnovení.

Pokud odstraníte skupinu prostředků virtuálního počítače nebo samotný virtuální počítač, zůstanou snímky pro okamžité obnovení na spravovaných discích aktivní a jejich platnost vyprší v závislosti na sadě pro uchovávání informací. Chcete-li odstranit snímky okamžitého obnovení (Pokud už je nepotřebujete) uložené v kolekci bodů obnovení, vyčistěte kolekci bodů obnovení podle kroků uvedených níže.

Chcete-li vyčistit body obnovení, postupujte podle kterékoli z těchto metod:<br>

- [Vyčištění kolekce bodů obnovení spuštěním zálohování na vyžádání](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [Vyčistit kolekci bodů obnovení z Azure Portal](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>Vyčištění kolekce bodů obnovení spuštěním zálohování na vyžádání

Po odebrání zámku aktivujte zálohování na vyžádání. Tím se zajistí, že body obnovení se automaticky vyčistí. Tuto operaci na vyžádání byste měli očekávat při prvním selhání. ale zajistí automatické vyčištění místo ručního odstranění bodů obnovení. Po vyčištění by mělo být úspěšné další plánované zálohování.

> [!NOTE]
> Automatické čištění proběhne po několika hodinách spuštění zálohování na vyžádání. Pokud se naplánované zálohování stále nedaří, zkuste kolekci bodů obnovení ručně odstranit pomocí kroků uvedených [tady](#clean-up-restore-point-collection-from-azure-portal).

#### <a name="clean-up-restore-point-collection-from-azure-portal"></a>Vyčistit kolekci bodů obnovení z Azure Portal <br>

Chcete-li ručně vymazat kolekci bodů obnovení, která není smazána z důvodu zámku skupiny prostředků, zkuste provést následující kroky:

1. Přihlásit se na [Azure Portal](https://portal.azure.com/).
2. V nabídce **centra** klikněte na **všechny prostředky**a vyberte skupinu prostředků s následujícím formátem AzureBackupRG_`<Geo>`_`<number>`, kde se virtuální počítač nachází.

    ![Odstranit zámek](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Klikněte na skupina prostředků a zobrazí se okno **Přehled** .
4. Výběrem možnosti **Zobrazit skryté typy** zobrazíte všechny skryté prostředky. Vyberte kolekce bodů obnovení s následujícím formátem AzureBackupRG_`<VMName>`_`<number>`.

    ![Odstranit zámek](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Kliknutím na tlačítko **Odstranit** vyčistíte kolekci bodů obnovení.
6. Opakujte operaci zálohování znovu.

> [!NOTE]
 >Pokud má prostředek (kolekce RP) velký počet bodů obnovení, může se stát, že odstraněním z portálu dojde k vypršení časového limitu a selhání. Jedná se o známý problém CRP, kdy se v určitém čase neodstraní všechny body obnovení a vyprší časový limit operace. operace odstranění ale obvykle proběhne po 2 nebo 3 opakováních.
