---
title: 'Řešení potíží s Azure Backupm: Stav agenta hosta není k dispozici'
description: Příznaky, příčiny a řešení chyb Azure Backup souvisejících s agentem, rozšířením a disky.
author: saurabhsensharma
manager: saurabhsensharma
keywords: Zálohování Azure; Agent virtuálního počítače; Připojení k síti;
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: saurse
ms.openlocfilehash: e4337c9c89ca239bb664cbb7fb953ab9eedd3ac5
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465318"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Řešení potíží s Azure Backupm: Problémy s agentem nebo rozšířením

Tento článek popisuje kroky pro řešení potíží, které vám pomohou vyřešit chyby Azure Backup související s komunikací s agentem a rozšířením virtuálního počítače.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]



## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable – agent virtuálního počítače nemůže komunikovat s Azure Backup

**Kód chyby**: UserErrorGuestAgentStatusUnavailable <br>
**Chybová zpráva**: Agent virtuálního počítače nemůže komunikovat s Azure Backup.<br>

Po registraci a naplánování virtuálního počítače pro službu zálohování inicializuje zálohování úlohu pomocí komunikace s agentem virtuálního počítače, aby pomohlo vytvořit snímek v čase. Některé z následujících podmínek mohou zabránit spuštění snímku. Pokud snímek není aktivovaný, zálohování může selhat. Proveďte následující kroky pro řešení potíží v uvedeném pořadí a opakujte operaci:<br>
**1. příčina: [Agent je nainstalovaný na virtuálním počítači, ale nereaguje (pro virtuální počítače s Windows).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**2. příčina: [Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače se systémem Linux).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Příčina 3: [Stav snímku nelze načíst nebo nelze vytvořit snímek.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**     
**Příčina 4: [Rozšíření zálohování se nepodařilo aktualizovat nebo načíst.](#the-backup-extension-fails-to-update-or-load)**  
**Příčina 5: [Virtuální počítač nemá přístup k Internetu.](#the-vm-has-no-internet-access)**

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError – nepovedlo se komunikovat s agentem virtuálního počítače pro stav snímku.

**Kód chyby**: GuestAgentSnapshotTaskStatusError<br>
**Chybová zpráva**: Nejde komunikovat s agentem virtuálního počítače kvůli stavu snímku <br>

Po registraci a naplánování virtuálního počítače pro službu Azure Backup služba Backup inicializuje úlohu prostřednictvím komunikace s rozšířením zálohy virtuálního počítače, aby se pomohlo vytvořit snímek v daném časovém okamžiku. Některé z následujících podmínek mohou zabránit spuštění snímku. Pokud se snímek neaktivuje, může dojít k selhání zálohování. Proveďte následující kroky pro řešení potíží v uvedeném pořadí a opakujte operaci:  
**1. příčina: [Agent je nainstalovaný na virtuálním počítači, ale nereaguje (pro virtuální počítače s Windows).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**2. příčina: [Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače se systémem Linux).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Příčina 3: [Virtuální počítač nemá přístup k Internetu.](#the-vm-has-no-internet-access)**

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached – dosáhlo se maximálního limitu kolekce bodů obnovení.

**Kód chyby**: UserErrorRpCollectionLimitReached <br>
**Chybová zpráva**: Dosáhlo se maximálního limitu kolekce bodů obnovení. <br>
* K tomuto problému může dojít, pokud je zámek skupiny prostředků bodu obnovení znemožněn automatickým čištěním bodu obnovení.
* K tomuto problému může dojít také v případě, že je aktivováno více záloh za den. V současné době doporučujeme jenom jednu zálohu za den, protože body okamžitého obnovení se uchovávají po dobu 1-5 dnů, jak nakonfigurované uchovávání snímků, a k virtuálnímu počítači se dá v kterémkoli okamžiku přidružit jenom 18 okamžité RPs. <br>

Doporučená akce:<br>
Pokud chcete tento problém vyřešit, odeberte zámek pro skupinu prostředků virtuálního počítače a potom operaci spusťte znovu, aby se aktivovala operace vyčištění.
> [!NOTE]
> Služba Backup vytvoří samostatnou skupinu prostředků, než je skupina prostředků virtuálního počítače pro uložení kolekce bodů obnovení. Zákazníkům se doporučuje, aby nezamkli skupinu prostředků vytvořenou pro použití v rámci služby zálohování. Formát názvů pro skupinu prostředků vytvořenou službou zálohování: AzureBackupRG_`<Geo>`_`<number>` Eg: AzureBackupRG_northeurope_1

**Krok 1: [Odebrat zámek ze skupiny prostředků bodu obnovení](#remove_lock_from_the_recovery_point_resource_group)** <br>
**Krok 2: [Vyčistit kolekci bodů obnovení](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured-Backup nemá dostatečná oprávnění k trezoru klíčů pro zálohování šifrovaných virtuálních počítačů.

**Kód chyby**: UserErrorKeyvaultPermissionsNotConfigured <br>
**Chybová zpráva**: Zálohování nemá dostatečná oprávnění k trezoru klíčů pro zálohování šifrovaných virtuálních počítačů. <br>

Aby operace zálohování proběhla úspěšně na šifrovaných virtuálních počítačích, musí mít oprávnění pro přístup k trezoru klíčů. To se dá udělat pomocí [Azure Portal](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) nebo přes [PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection).

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>Operace ExtensionSnapshotFailedNoNetwork-Snapshot selhala, protože ve virtuálním počítači není síťové připojení.

**Kód chyby**: ExtensionSnapshotFailedNoNetwork<br>
**Chybová zpráva**: Operace vytvoření snímku se nepovedla, protože virtuální počítač není připojený k síti<br>

Po registraci a naplánování virtuálního počítače pro službu Azure Backup služba Backup inicializuje úlohu prostřednictvím komunikace s rozšířením zálohy virtuálního počítače, aby se pomohlo vytvořit snímek v daném časovém okamžiku. Některé z následujících podmínek mohou zabránit spuštění snímku. Pokud se snímek neaktivuje, může dojít k selhání zálohování. Proveďte následující kroky pro řešení potíží v uvedeném pořadí a opakujte operaci:    
**1. příčina: [Stav snímku nelze načíst nebo nelze vytvořit snímek.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**2. příčina: [Rozšíření zálohování se nepodařilo aktualizovat nebo načíst.](#the-backup-extension-fails-to-update-or-load)**  
**Příčina 3: [Virtuální počítač nemá přístup k Internetu.](#the-vm-has-no-internet-access)**

## <a name="ExtentionOperationFailed-vmsnapshot-extension-operation-failed"></a>Operace rozšíření ExtentionOperationFailedForManagedDisks-VMSnapshot se nezdařila.

**Kód chyby**: ExtentionOperationFailedForManagedDisks <br>
**Chybová zpráva**: Operace rozšíření VMSnapshot selhala<br>

Po registraci a naplánování virtuálního počítače pro službu Azure Backup služba Backup inicializuje úlohu prostřednictvím komunikace s rozšířením zálohy virtuálního počítače, aby se pomohlo vytvořit snímek v daném časovém okamžiku. Některé z následujících podmínek mohou zabránit spuštění snímku. Pokud se snímek neaktivuje, může dojít k selhání zálohování. Proveďte následující kroky pro řešení potíží v uvedeném pořadí a opakujte operaci:  
**1. příčina: [Stav snímku nelze načíst nebo nelze vytvořit snímek.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**2. příčina: [Rozšíření zálohování se nepodařilo aktualizovat nebo načíst.](#the-backup-extension-fails-to-update-or-load)**  
**Příčina 3: [Agent je nainstalovaný na virtuálním počítači, ale nereaguje (pro virtuální počítače s Windows).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Příčina 4: [Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače se systémem Linux).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed/BackUpOperationFailedV2 – zálohování se nepovedlo, došlo k vnitřní chybě.

**Kód chyby**: BackUpOperationFailed / BackUpOperationFailedV2 <br>
**Chybová zpráva**: Zálohování se nepovedlo, došlo k vnitřní chybě – zkuste prosím operaci zopakovat za pár minut. <br>

Po registraci a naplánování virtuálního počítače pro službu Azure Backup služba Backup inicializuje úlohu prostřednictvím komunikace s rozšířením zálohy virtuálního počítače, aby se pomohlo vytvořit snímek v daném časovém okamžiku. Některé z následujících podmínek mohou zabránit spuštění snímku. Pokud se snímek neaktivuje, může dojít k selhání zálohování. Proveďte následující kroky pro řešení potíží v uvedeném pořadí a opakujte operaci:  
**1. příčina: [Agent nainstalovaný na virtuálním počítači, ale nereaguje (pro virtuální počítače s Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**2. příčina: [Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače se systémem Linux).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Příčina 3: [Stav snímku nelze načíst nebo nelze vytvořit snímek.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Příčina 4: [Rozšíření zálohování se nepodařilo aktualizovat nebo načíst.](#the-backup-extension-fails-to-update-or-load)**  
**Příčina 5: Služba zálohování nemá oprávnění odstranit staré body obnovení z důvodu zámku skupiny prostředků.** <br>
**Příčina 6: [Virtuální počítač nemá přístup k Internetu.](#the-vm-has-no-internet-access)**

## <a name="usererrorunsupporteddisksize---currently-azure-backup-does-not-support-disk-sizes-greater-than-4095gb"></a>UserErrorUnsupportedDiskSize – aktuálně Azure Backup nepodporuje velikosti disků větší než 4095 GB.

**Kód chyby**: UserErrorUnsupportedDiskSize <br>
**Chybová zpráva**: V současné době Azure Backup nepodporuje velikosti disků větší než 4095 GB. <br>

Operace zálohování může selhat při zálohování virtuálního počítače s velikostí disku větší než 4095 GB. Už brzy bude dostupná podpora pro velké disky.  

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress – nepovedlo se inicializovat zálohování, protože v tuto chvíli probíhá jiná operace zálohování.

**Kód chyby**: UserErrorBackupOperationInProgress <br>
**Chybová zpráva**: Nejde iniciovat zálohování, protože v tuto chvíli probíhá jiná operace zálohování.<br>

Poslední úloha zálohování se nezdařila, protože probíhá existující úloha zálohování. Novou úlohu zálohování nemůžete spustit, dokud se nedokončí aktuální úloha. Před aktivací nebo plánováním dalších operací zálohování zajistěte, aby byla právě probíhající operace zálohování dokončena. Chcete-li zjistit stav úloh zálohování, proveďte následující kroky:

1. Přihlaste se k webu Azure Portal a klikněte na **Všechny služby**. Zadejte Recovery Services a klikněte na **Trezory služby Recovery Services**. Objeví se seznam trezorů Recovery Services.
2. V seznamu trezorů služby Recovery Services vyberte trezor, ve kterém je zálohování nakonfigurované.
3. V nabídce řídicího panelu trezoru klikněte na **úlohy zálohování** . zobrazí se všechny úlohy zálohování.

    * Pokud úloha zálohování probíhá, počkejte, než se dokončí, nebo zrušte úlohu zálohování.
        * Úlohu zálohování zrušíte tak, že kliknete pravým tlačítkem na úlohu zálohování a kliknete na **Zrušit** nebo použijete [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0).
    * Pokud jste znovu nakonfigurovali zálohu v jiném trezoru, ujistěte se, že ve starém trezoru nejsou spuštěné žádné úlohy zálohování. Pokud existuje, zrušte úlohu zálohování.
        * Úlohu zálohování zrušíte tak, že kliknete pravým tlačítkem na úlohu zálohování a kliknete na **Zrušit** nebo použijete [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0) .
4. Opakujte operaci zálohování.

Pokud naplánovaná operace zálohování trvá v konfliktu s další konfigurací zálohování, Projděte si [osvědčené postupy](backup-azure-vms-introduction.md#best-practices), [výkon zálohování](backup-azure-vms-introduction.md#backup-performance) a [aspekty obnovení](backup-azure-vms-introduction.md#backup-and-restore-considerations).


## <a name="causes-and-solutions"></a>Příčiny a řešení

### <a name="the-vm-has-no-internet-access"></a>Virtuální počítač nemá přístup k Internetu.
Za požadavek nasazení nemá virtuální počítač přístup k Internetu. Nebo může mít omezení, která brání přístupu k infrastruktuře Azure.

Pro správné fungování rozšíření Backup vyžaduje připojení k veřejným IP adresám Azure. Rozšíření odesílá příkazy koncovému bodu služby Azure Storage (adresa URL protokolu HTTPs) pro správu snímků virtuálního počítače. Pokud rozšíření nemá přístup k veřejnému Internetu, zálohování nakonec neproběhne úspěšně.

####  <a name="solution"></a>Řešení
Informace o vyřešení problému v síti najdete v tématu [vytvoření připojení k síti](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agent je nainstalovaný na virtuálním počítači, ale nereaguje (pro virtuální počítače s Windows).

#### <a name="solution"></a>Řešení
Je možné, že agent virtuálního počítače je poškozený nebo se služba zastavila. Opětovná instalace agenta virtuálního počítače pomáhá získat nejnovější verzi. Také pomáhá s restartováním komunikace se službou.

1. Zjistěte, jestli je služba agenta hosta systému Windows Azure spuštěná ve službě VM (Services. msc). Pokuste se restartovat službu agenta hosta systému Windows Azure a zahájit zálohování.    
2. Pokud není služba Windows Azure Host agent v části služby viditelná, v Ovládacích panelech klikněte na **programy a funkce** a určete, jestli je nainstalovaná služba Windows Azure Host agent.
4. Pokud se Agent hosta systému Windows Azure zobrazí v části **programy a funkce**, odinstalujte agenta hosta systému Windows Azure.
5. Stáhněte a nainstalujte si [nejnovější verzi MSI agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). K dokončení instalace musíte mít oprávnění správce.
6. Ověřte, že se v části služby zobrazují služby agenta hosta systému Windows Azure.
7. Spusťte zálohování na vyžádání:
    * Na portálu vyberte **Zálohovat nyní**.

Ověřte také, že na virtuálním počítači [je nainstalováno rozhraní Microsoft .net 4,5](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) . Aby agent virtuálního počítače mohl komunikovat se službou, je vyžadován .NET 4,5.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače se systémem Linux).

#### <a name="solution"></a>Řešení
Většina selhání souvisejících s agentem nebo rozšířeními pro virtuální počítače se systémem Linux je způsobena problémy, které mají vliv na zastaralého agenta virtuálního počítače. Pokud chcete tento problém vyřešit, postupujte podle následujících obecných pokynů:

1. Postupujte podle pokynů pro [aktualizaci agenta virtuálního počítače se systémem Linux](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > *Důrazně doporučujeme* , abyste agenta aktualizovali pouze prostřednictvím distribučního úložiště. Nedoporučujeme stahovat kód agenta přímo z GitHubu a aktualizovat ho. Pokud není k dispozici nejnovější agent pro distribuci, obraťte se na podporu distribuce, kde najdete pokyny k jeho instalaci. Chcete-li zjistit nejnovějšího agenta, v úložišti GitHubu, navštivte stránku [agenta Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) .

2. Spuštěním následujícího příkazu zajistěte, aby byl na virtuálním počítači spuštěný agent Azure:`ps -e`

   Pokud proces není spuštěný, restartujte ho pomocí následujících příkazů:

   * Pro Ubuntu:`service walinuxagent start`
   * Pro ostatní distribuce:`service waagent start`

3. [Nakonfigurujte agenta automatického restartování](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Spusťte novou zálohu testu. Pokud chyba přetrvává, shromážděte z virtuálního počítače následující protokoly:

   * /var/lib/waagent/*. XML
   * /var/log/waagent.log
   * /var/log/azure/*

Pokud budeme vyžadovat podrobné protokolování pro waagent, postupujte takto:

1. V souboru/etc/waagent.conf vyhledejte následující řádek: **Povolit podrobné protokolování (y | n)**
2. Změňte **log. verbose** hodnota z *n* na *y*.
3. Uložte změnu a pak restartujte waagent, a to provedením kroků popsaných výše v této části.

###  <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Stav snímku nelze načíst nebo nelze vytvořit snímek.
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
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Přejděte na **možnost všechny prostředky**, vyberte skupinu prostředků kolekce bodů obnovení v následujícím formátu AzureBackupRG_`<Geo>`_.`<number>`
3. V části **Nastavení** vyberte zámky  a zobrazte zámky.
4. Pokud chcete zámek odebrat, vyberte tři tečky a klikněte na **Odstranit**.

    ![Odstranit zámek](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a>Vyčistit kolekci bodů obnovení
Po odebrání zámku je potřeba vyčistit body obnovení. Chcete-li vyčistit body obnovení, postupujte podle kterékoli z těchto metod:<br>
* [Vyčištění kolekce bodů obnovení spuštěním služby ad hoc Backup](#clean-up-restore-point-collection-by-running-ad-hoc-backup)<br>
* [Vyčistit kolekci bodů obnovení z Azure Portal](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-ad-hoc-backup"></a>Vyčištění kolekce bodů obnovení spuštěním služby ad hoc Backup
Po odebrání zámku spusťte ad hoc/ruční zálohování. Tím se zajistí, že body obnovení se automaticky vyčistí. Očekává se, že tato ad hoc/ruční operace poprvé selže; ale zajistí automatické vyčištění místo ručního odstranění bodů obnovení. Po vyčištění by mělo být úspěšné další plánované zálohování.

> [!NOTE]
> Automatické čištění proběhne po několika hodinách aktivace ad hoc/ruční zálohy. Pokud se naplánované zálohování stále nedaří, zkuste kolekci bodů obnovení ručně odstranit pomocí kroků uvedených [tady](#clean-up-restore-point-collection-from-azure-portal).

#### <a name="clean-up-restore-point-collection-from-azure-portal"></a>Vyčistit kolekci bodů obnovení z Azure Portal <br>

Chcete-li ručně vymazat kolekci bodů obnovení, která nebyla smazána z důvodu zámku skupiny prostředků, zkuste provést následující kroky:
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. V nabídce **centra** klikněte na **všechny prostředky**a vyberte skupinu prostředků s následujícím formátem AzureBackupRG_`<Geo>`_`<number>` , kde se nachází váš virtuální počítač.

    ![Odstranit zámek](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Klikněte na skupina prostředků a zobrazí se okno **Přehled** .
4. Výběrem možnosti **Zobrazit skryté typy** zobrazíte všechny skryté prostředky. Vyberte kolekce bodů obnovení s následujícím formátem AzureBackupRG_`<VMName>`_.`<number>`

    ![Odstranit zámek](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Kliknutím na tlačítko **Odstranit**vyčistíte kolekci bodů obnovení.
6. Opakujte operaci zálohování znovu.

> [!NOTE]
 >Pokud prostředek (kolekce RP) obsahuje velký počet bodů obnovení, může se stát, že odstraněním stejného typu z portálu dojde k vypršení časového limitu a selhání. Jedná se o známý problém CRP, kdy se v určitém čase neodstraní všechny body obnovení a vyprší časový limit operace. operace odstranění ale obvykle proběhne po 2 nebo 3 opakováních.
