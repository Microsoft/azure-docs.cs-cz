---
title: Poradce při potížích s chybami zálohování pomocí virtuálních počítačích Azure
description: V tomto článku se dozvíte, jak řešit chyby při zálohování a obnovení virtuálních počítačů Azure.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 08/30/2019
ms.openlocfilehash: 15e4b4c8850798fd2386cd2874b6ab58a18d5406
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297386"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>Řešení potíží s chybami zálohování na virtuálních počítačích Azure

Pomocí následujících informací můžete řešit chyby, ke kterým došlo při používání služby Azure Backup:

## <a name="backup"></a>Zálohování

Tato část popisuje selhání operace zálohování virtuálního počítače Azure.

### <a name="basic-troubleshooting"></a>Základní řešení potíží

* Ujistěte se, že agent virtuálního zařízení (WA Agent) je [nejnovější verze](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent).
* Ujistěte se, že je podporovaná verze operačního systému Windows nebo Linux, podívejte se na [matici podpory zálohování virtuálních počítače IaaS](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas).
* Ověřte, zda není spuštěna jiná služba zálohování.
  * Chcete-li zajistit, že neexistují žádné problémy s rozšířením snímku, [odinstalujte rozšíření, která vynutí opětovné načtení, a opakujte zálohování](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout).
* Ověřte, zda má virtuální hotel připojení k internetu.
  * Ujistěte se, že není spuštěna jiná služba zálohování.
* Od `Services.msc`, ujistěte se, že je **spuštěna**služba **Agent hosta Windows Azure** . Pokud služba **Agent hosta Windows Azure** chybí, nainstalujte ji z [zálohovat virtuální počítače Azure v trezoru služby Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent).
* **Protokol událostí** může zobrazit selhání zálohování, které jsou z jiných zálohovacích produktů, například záloha systému Windows Server a nejsou způsobeny zálohováním Azure. Pomocí následujících kroků zjistěte, jestli je problém s Azure Backup:
  * Pokud se ve zdroji nebo zprávě události zobrazí chyba se položkou **Zálohování,** zkontrolujte, jestli byly zálohy zálohování virtuálního počítače Azure IaaS úspěšné a jestli byl bod obnovení vytvořen s požadovaným typem snímku.
  * Pokud Azure Backup funguje, pak je problém pravděpodobně s jiným řešením zálohování.
  * Tady je příklad chyby prohlížeče událostí 517, kdy zálohování Azure fungovalo dobře, ale "Zálohování serveru" se lhalo:<br>
    ![Zálohování systému Windows Server se nedaří](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
  * Pokud azure backup selhává, vyhledejte odpovídající kód chyby v části Běžné chyby zálohování virtuálních počítačích v tomto článku.

## <a name="common-issues"></a>Běžné problémy

Následují běžné problémy s chybami zálohování na virtuálních počítačích Azure.

## <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>Kopírování vddfromBackUpVaultTakingLongTime – kopírování zálohovaných dat z úložiště bylo vyprodáno

Kód chyby: Kopírování VHDFromBackUpVaultTakingLongTime <br/>
Chybová zpráva: Kopírování zálohovaných dat z úložiště bylo vyprodáno

K tomu může dojít z důvodu přechodných chyb úložiště nebo nedostatku videa VOPS účtu úložiště pro službu zálohování k přenosu dat do úložiště v časovém odpočitatelu. Nakonfigurujte zálohování virtuálních počítače pomocí těchto [doporučených postupů](backup-azure-vms-introduction.md#best-practices) a opakujte operaci zálohování.

## <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInIfState - Virtuální počítače není ve stavu, který umožňuje zálohování

Kód chyby: UserErrorVmNotInIfState <br/>
Chybová zpráva: Virtuální počítače není ve stavu, který umožňuje zálohování.<br/>

Operace zálohování se nezdařila, protože virtuální počítače jsou ve stavu Selhání. Aby bylo možné úspěšně provést zálohování, virtuální počítač musí být ve stavu Spuštěno, Zastaveno nebo Zastaveno (přidělení zrušeno).

* Pokud je virtuální virtuální ms v přechodném stavu mezi **spuštěním** a **vypnutím**, počkejte na změnu stavu. Potom spusťte úlohu zálohování.
* Pokud je virtuální počítač virtuálním počítačem s Linuxem a používá modul jádra Linuxu s rozšířeným zabezpečením, vylučte cestu agenta Azure Linux **/var/lib/waagent** ze zásad zabezpečení a ujistěte se, že je nainstalované rozšíření Backup.

## <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed - Nepodařilo se zmrazit jeden nebo více přípojných bodů virtuálního počítače, aby se konzistentní snímek systému souborů

Kód chyby: UživatelErrorFsFreezeFailed <br/>
Chybová zpráva: Nepodařilo se zmrazit jeden nebo více přípojných bodů virtuálního počítače, aby se snímek konzistentní se souborovým systémem.

* Odpojte zařízení, pro která nebyl vyčištěn stav systému souborů, pomocí příkazu **umount.**
* Spusťte kontrolu konzistence systému souborů na těchto zařízeních pomocí příkazu **fsck.**
* Znovu připojte zařízení a opakujte operaci zálohování.</ol>

## <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM / ExtensionInstallationFailedCOM / ExtensionInstallationFailedMDTC - Instalace/operace rozšíření se nezdařila z důvodu chyby modelu COM+

Kód chyby: ExtensionSnapshotFailedCOM <br/>
Chybová zpráva: Operace snímku se nezdařila z důvodu chyby modelu COM+.

Kód chyby: ExtensionInstallationFailedCOM  <br/>
Chybová zpráva: Instalace nebo operace rozšíření se nezdařila z důvodu chyby modelu COM+.

Kód chyby: ExtensionInstallationFailedMDTC <br/>
Chybová zpráva: Instalace rozšíření se nezdařila s chybou "Com+ nemohl mluvit s koordinátorem distribuovaných transakcí společnosti Microsoft <br/>

Operace zálohování se nezdařila z důvodu problému se systémovou aplikací systému Windows **SERVICE.**  Pokud chcete tento problém vyřešit, postupujte následovně:

* Zkuste spustit/restartovat **systémovou aplikaci** modelu Windows COM+ (z příkazového řádku se zvýšenými oprávněními **– net start COMSysApp**).
* Ujistěte se, že služba **koordinátora distribuovaných transakcí** je spuštěna jako účet **síťové služby.** Pokud ne, změňte jej tak, aby byl spuštěn jako účet **síťové služby,** a restartujte **systémovou aplikaci modelu COM+**.
* Pokud nelze restartovat službu, přeinstalujte **službu Koordinátor distribuovaných transakcí** pomocí následujících kroků:
  * Zastavte službu DTC (Distributed Transaction Coordinator).
  * Otevřete příkazový řádek (cmd).
  * Spustit příkaz msdtc -uninstall
  * Spustit příkaz "msdtc -install"
  * Spusťte službu DTC (Distributed Transaction Coordinator).
* Spusťte **systémovou aplikaci modelu**Windows COM+ . Po spuštění **systémové aplikace modelu COM+** aktivujte úlohu zálohování z portálu Azure.</ol>

## <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState - Snímek operace se nezdařila, protože VSS autoři byli ve špatném stavu

Kód chyby: ExtensionFailedVssWriterInBadState <br/>
Chybová zpráva: Operace snímku se nezdařila, protože autoři služby VSS byli ve špatném stavu.

Restartujte zapisovače VSS, kteří jsou ve špatném stavu. Z příkazového řádku se ```vssadmin list writers```zvýšenými oprávněními spusťte . Výstup obsahuje všechny zapisovače VSS a jejich stav. Pro každého zapisovače VSS se stavem, který není **[1] Stabilní**, chcete-li restartovat zapisovač služby VSS, spusťte následující příkazy z příkazového řádku se zvýšenými oprávněními:

* ```net stop serviceName```
* ```net start serviceName```

## <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure - Selhání při analýzě konfigurace pro rozšíření zálohy

Kód chyby: ExtensionConfigParsingFailure<br/>
Chybová zpráva: Chyba při analýzě konfigurace rozšíření zálohy.

K této chybě dochází z důvodu změněných oprávnění v adresáři **MachineKeys:** **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.
Spusťte následující příkaz a ověřte, zda oprávnění v adresáři **MachineKeys** jsou výchozí:**icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.

Výchozí oprávnění jsou následující:

* Všichni: (R,W)
* BUILTIN\Správci: (F)

Pokud se v adresáři **MachineKeys** zobrazují oprávnění, která se liší od výchozích hodnot, opravte oprávnění, odstraňte certifikát a aktivujte zálohování takto:

1. Opravte oprávnění v adresáři **MachineKeys.** Pomocí vlastností zabezpečení průzkumníka a upřesňujících nastavení zabezpečení v adresáři obnovte oprávnění zpět na výchozí hodnoty. Odeberte z adresáře všechny objekty uživatele kromě výchozích hodnot a ujistěte **se,** že oprávnění Everyone má zvláštní přístup následujícím způsobem:

   * Seznam složek/přečtená data
   * Číst atributy
   * Čtení rozšířených atributů
   * Vytváření souborů/zápis dat
   * Vytváření složek/připojit data
   * Atributy zápisu
   * Zapsat rozšířené atributy
   * Oprávnění ke čtení
2. Odstranit všechny certifikáty, ve kterých je **vystaveno v programu** To klasický model nasazení nebo **generátor certifikátů CRP v systému Windows Azure**:

   * [Otevřete certifikáty v konzole místního počítače](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in).
   * V **části Osobní** > **certifikáty**odstraňte všechny certifikáty, ve kterých je **vystaveno** v programu je klasický model nasazení nebo generátor **certifikátů CRP v systému Windows Azure**.
3. Aktivujte úlohu zálohování virtuálního počítače.

## <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState - Stav rozšíření nepodporuje operaci zálohování

Kód chyby: ExtensionStuckInDeletionState <br/>
Chybová zpráva: Stav rozšíření nepodporuje operaci zálohování

Operace zálohování se nezdařila z důvodu nekonzistentního stavu rozšíření zálohování. Pokud chcete tento problém vyřešit, postupujte následovně:

* Ujistěte se, že je agent hosta nainstalovaný a funkční.
* Na webu Azure Portal přejděte na Rozšíření**o všechna** > **nastavení** **virtuálních strojů** > 
* Vyberte rozšíření zálohování VmSnapshot nebo VmSnapshotLinux a klikněte na **Odinstalovat**.
* Po odstranění rozšíření zálohy opakujte operaci zálohování
* Další operace zálohování nainstaluje nové rozšíření v požadovaném stavu.

## <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError - Operace snímku se nezdařila, protože pro některé připojené disky byl překročen limit snímku.

Kód chyby: Chyba rozšířeníFailedSnapshotLimitReachedError <br/>
Chybová zpráva: Operace snímku se nezdařila, protože u některých připojených disků byl překročen limit snímku.

Operace snímku se nezdařila, protože pro některé připojené disky byl překročen limit snímku. Proveďte níže uvedené kroky řešení potíží a opakujte operaci.

* Odstraňte snímky objektů blob disku, které nejsou vyžadovány. Buďte opatrní, abyste neodstranili disk ový objekt blob, měly by být odstraněny pouze objekty BLOB snímku.
* Pokud je na účtech úložiště disku virtuálního počítače povoleno obnovitelné odstranění, nakonfigurujte uchovávání měkkých odstranění tak, aby existující snímky byly v libovolném okamžiku menší než maximální povolené snímky.
* Pokud je azure site recovery v zálohovaném virtuálním počítači povolená, proveďte následující kroky:

  * Ujistěte se, že hodnota **isanysnapshotfailed** je nastavena jako false v /etc/azure/vmbackup.conf
  * Naplánujte azure site recovery v jiném čase, tak, aby nedošlo ke konfliktu operace zálohování.

## <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive - operace snímku se nezdařila z důvodu nedostatečných prostředků virtuálního počítače

Kód chyby: ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
Chybová zpráva: Operace snímku se nezdařila z důvodu nedostatečných prostředků virtuálního počítače.

Operace zálohování na virtuálním počítači se nezdařila z důvodu zpoždění v síťových volání při provádění operace snímek. Pokud chcete tento problém vyřešit, proveďte krok 1. Pokud problém přetrvává, zkuste kroky 2 a 3.

**Krok 1:** Vytvoření snímku prostřednictvím hostitele

Na příkazovém řádku se zvýšenými oprávněními (správce) spusťte následující příkaz:

```text
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Tím se zajistí, že se všechny snímky pořídí přes hostitele, a ne hosta. Zopakujte operaci zálohování.

**Krok 2**: Zkuste změnit plán zálohování na dobu, kdy je virtuální počítače pod menším zatížením (méně CPU/IOps atd.)

**Krok 3**: Zkuste [zvětšit velikost virtuálního počítače](https://azure.microsoft.com/blog/resize-virtual-machines/) a opakujte operaci

## <a name="common-vm-backup-errors"></a>Běžné chyby zálohování virtuálních počítačů

| Podrobnosti o chybě | Alternativní řešení |
| ------ | --- |
| **Kód chyby**: 320001, ResourceNotFound <br/> **Chybová zpráva**: Operaci nelze provést, protože virtuální virtuální měsíč již neexistuje. <br/> <br/> **Kód chyby**: 400094, BCMV2VMNotFound <br/> **Chybová zpráva**: Virtuální počítač neexistuje <br/> <br/>  Virtuální počítač Azure nebyl nalezen.  |K této chybě dojde, když se odstraní primární virtuální počítače, ale zásady zálohování stále hledá virtuálního počítače pro zálohování. Chcete-li tuto chybu opravit, postupujte takto: <ol><li> Znovu vytvořte virtuální počítač se stejným názvem a stejným názvem skupiny prostředků, **názvem cloudové služby**,<br>**Nebo**</li><li> Přestaňte chránit virtuální počítač s nebo bez odstranění záložních dat. Další informace najdete v tématu [Stop protecting virtual machines](backup-azure-manage-vms.md#stop-protecting-a-vm).</li></ol>|
|**Kód chyby**: Chyba UživateleBCMPremiumStorageQuotaError<br/> **Chybová zpráva**: Nelze zkopírovat snímek virtuálního počítače z důvodu nedostatku volného místa v účtu úložiště | Pro prémiové virtuální počítače na zálohovací zásobníkvirtuálních počítačích V1 zkopírujeme snímek do účtu úložiště. Tento krok zajišťuje, že provoz správy zálohování, který funguje na snímku, neomezuje počet IOPS, které jsou k dispozici pro aplikaci pomocí disků premium. <br><br>Doporučujeme přidělit pouze 50 procent, 17,5 TB, celkového místa účtu úložiště. Pak služba Azure Backup můžete zkopírovat snímek do účtu úložiště a přenášet data z tohoto zkopírovanéumístění v účtu úložiště do trezoru. |
| **Kód chyby:** 380008, AzureVmOffline <br/> **Chybová zpráva**: Instalace rozšíření služby Microsoft Recovery Services se nezdařila, protože virtuální počítač není spuštěn. | Agent virtuálního počítače je předpokladem pro rozšíření služby Azure Recovery Services. Nainstalujte agenta virtuálního počítače Azure a restartujte operaci registrace. <br> <ol> <li>Zkontrolujte, jestli je agent virtuálního zařízení správně nainstalovaný. <li>Ujistěte se, že příznak na konfiguraci virtuálního počítači je správně nastaven.</ol> Přečtěte si další informace o instalaci agenta virtuálního zařízení a o ověření instalace agenta virtuálního zařízení. |
| **Kód chyby:** Chyba rozšířeníPosuzeníBitLockerError <br/> **Chybová zpráva**: Operace snímku se nezdařila s chybou operace služby Stínová kopie svazku (VSS) **Tato jednotka je uzamčena šifrováním jednotky Nástrojem bitlocker. Tuto jednotku musíte odemknout z ovládacího panelu.** |Vypněte nástroj BitLocker pro všechny jednotky na virtuálním počítači a zkontrolujte, zda je problém VSS vyřešen. |
| **Kód chyby**: VmNotInDesirableState <br/> **Chybová zpráva**: Virtuální počítače není ve stavu, který umožňuje zálohování. |<ul><li>Pokud je virtuální virtuální ms v přechodném stavu mezi **spuštěním** a **vypnutím**, počkejte na změnu stavu. Potom spusťte úlohu zálohování. <li> Pokud je virtuální počítač virtuálním počítačem s Linuxem a používá modul jádra Linuxu s rozšířeným zabezpečením, vylučte cestu agenta Azure Linux **/var/lib/waagent** ze zásad zabezpečení a ujistěte se, že je nainstalované rozšíření Backup.  |
| Agent virtuálního počítače není k dispozici na virtuálním počítači: <br>Nainstalujte všechny předpoklady a agenta virtuálního soudu. Potom operaci restartujte. |Přečtěte si další informace o [instalaci agenta virtuálního zařízení a o ověření instalace agenta virtuálního zařízení](#vm-agent). |
| **Kód chyby:** ExtensionSnapshotFailedNoSecureNetwork <br/> **Chybová zpráva**: Operace snímku se nezdařila z důvodu selhání vytvoření zabezpečeného síťového komunikačního kanálu. | <ol><li> Otevřete Editor registru spuštěním **příkazu regedit.exe** ve zvýšeném režimu. <li> Identifikujte všechny verze rozhraní .NET Framework, které se nacházejí ve vašem systému. Jsou k dispozici v hierarchii klíče registru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft**. <li> Pro každou architekturu .NET, která je k dispozici v klíči registru, přidejte následující klíč: <br> **SchUseStrongCrypto"=dword:00000001**. </ol>|
| **Kód chyby**: ExtensionVCRedistInstallationFailure <br/> **Chybová zpráva**: Operace snímku se nezdařila z důvodu selhání instalace visual c++ redistribuovatelné pro Visual Studio 2012. | Přejděte na C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion a nainstalujte vcredist2013_x64.<br/>Ujistěte se, že hodnota klíče registru, která umožňuje instalaci služby, je nastavena na správnou hodnotu. To znamená nastavit **počáteční** hodnotu v **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** na **3** a ne **4**. <br><br>Pokud máte stále problémy s instalací, restartujte instalační službu spuštěním **MSIEXEC /UNREGISTER** následovaným **MSIEXEC /REGISTER** z příkazového řádku se zvýšenými oprávněními.  |
| **Kód chyby**: UserErrorRequestDisallowedByPolicy <BR> **Chybová zpráva**: Na virtuálním počítači je nakonfigurována neplatná zásada, která brání operaci snímek. | Pokud máte zásady Azure, které [řídí značky ve vašem prostředí](https://docs.microsoft.com/azure/governance/policy/tutorials/govern-tags), zvažte změnu zásady z [efektu Odepřít](https://docs.microsoft.com/azure/governance/policy/concepts/effects#deny) na efekt [Změnit](https://docs.microsoft.com/azure/governance/policy/concepts/effects#modify)nebo vytvořte skupinu prostředků ručně podle [schématu pojmenování vyžadovaného službou Azure Backup](https://docs.microsoft.com/azure/backup/backup-during-vm-creation#azure-backup-resource-group-for-virtual-machines).
## <a name="jobs"></a>Úlohy

| Podrobnosti o chybě | Alternativní řešení |
| --- | --- |
| Zrušení není pro tento typ úlohy podporováno: <br>Počkejte, až bude úloha dokončena. |Žádný |
| Úloha není v zrušeném stavu: <br>Počkejte, až bude úloha dokončena. <br>**Nebo**<br> Vybraná úloha není ve stavu, kdy lze zrušit: <br>Počkejte na dokončení úlohy. |Je pravděpodobné, že práce je téměř hotová. Počkejte, až bude úloha dokončena.|
| Zálohování nemůže úlohu zrušit, protože neprobíhá: <br>Zrušení je podporováno pouze pro probíhající úlohy. Pokuste se zrušit probíhající úlohu. |K této chybě dochází z důvodu přechodného stavu. Počkejte chvíli a opakujte operaci zrušení. |
| Zálohování se nepodařilo úlohu zrušit: <br>Počkejte, až bude úloha dokončena. |Žádný |

## <a name="restore"></a>Obnovení

| Podrobnosti o chybě | Alternativní řešení |
| --- | --- |
| Obnovení se nezdařilo s vnitřní chybou cloudu. |<ol><li>Cloudová služba, ke které se pokoušíte obnovit, je nakonfigurována s nastavením DNS. Můžete zkontrolovat: <br>**$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production" Get-AzureDns -DnsSettings $deployment. DnsSettings**.<br>Pokud je **nakonfigurována adresa,** pak jsou nakonfigurována nastavení DNS.<br> <li>Cloudová služba, do které se pokoušíte obnovit, je nakonfigurována pomocí **ReservedIP**a existující virtuální počítače v cloudové službě jsou v zastaveném stavu. Můžete zkontrolovat, zda cloudová služba rezervovala IP adresu pomocí následujících rutin prostředí PowerShell: **$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep. ReservedIPName**. <br><li>Pokoušíte se obnovit virtuální počítač s následujícími speciálními konfiguracemi sítě do stejné cloudové služby: <ul><li>Virtuální počítače pod konfigurací nástroje pro vyrovnávání zatížení, interní i externí.<li>Virtuální počítače s více vyhrazenými IP adresy. <li>Virtuální počítače s více síťové karty. </ul><li>Vyberte novou cloudovou službu v unovém ui nebo se podívejte na [důležité informace o obnovení virtuálních](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) počítačů se speciálními konfiguracemi sítě.</ol> |
| Vybraný název DNS je již přijat: <br>Zadejte jiný název DNS a akci opakujte. |Tento název DNS odkazuje na název cloudové služby, obvykle končící na **.cloudapp.net**. Tento název musí být jedinečný. Pokud se zobrazí tato chyba, musíte během obnovení zvolit jiný název virtuálního virtuálního serveru. <br><br> Tato chyba se zobrazí jenom uživatelům portálu Azure. Operace obnovení prostřednictvím prostředí PowerShell je úspěšná, protože obnovuje jenom disky a nevytváří virtuální hod. Chyba bude čelit při virtuálním počítače je explicitně vytvořen po operaci obnovení disku. |
| Zadaná konfigurace virtuální sítě není správná: <br>Zadejte jinou konfiguraci virtuální sítě a akci opakujte. |Žádný |
| Zadaná cloudová služba používá vyhrazenou ADRESU IP, která neodpovídá konfiguraci obnoveného virtuálního počítače: <br>Zadejte jinou cloudovou službu, která nepoužívá vyhrazenou ADRESU IP. Nebo zvolte jiný bod obnovení, ze kterýchcete obnovit. |Žádný |
| Cloudová služba dosáhla svého limitu na počet vstupních koncových bodů: <br>Opakujte operaci zadáním jiné cloudové služby nebo pomocí existujícího koncového bodu. |Žádný |
| Trezor služby Recovery Services a cílový účet úložiště se nacházejí ve dvou různých oblastech: <br>Ujistěte se, že účet úložiště zadaný v operaci obnovení je ve stejné oblasti Azure jako trezor služby Recovery Services. |Žádný |
| Účet úložiště zadaný pro operaci obnovení není podporován: <br>Podporovány jsou pouze účty úložiště Basic nebo Standard s místně redundantním nebo geograficky redundantním nastavením replikace. Vyberte podporovaný účet úložiště. |Žádný |
| Typ účtu úložiště zadaný pro operaci obnovení není online: <br>Ujistěte se, že účet úložiště zadaný v operaci obnovení je online. |K této chybě může dojít z důvodu přechodné chyby ve službě Azure Storage nebo z důvodu výpadku. Zvolte jiný účet úložiště. |
| Bylo dosaženo kvóty skupiny prostředků: <br>Odstraňte některé skupiny prostředků z webu Azure Portal nebo se obraťte na podporu Azure, abyste zvýšili limity. |Žádný |
| Vybraná podsíť neexistuje: <br>Vyberte podsíť, která existuje. |Žádný |
| Služba Zálohování nemá oprávnění k přístupu k prostředkům ve vašem předplatném. |Chcete-li tuto chybu vyřešit, obnovte disky nejprve pomocí kroků v části [Obnovení zálohovaných disků](backup-azure-arm-restore-vms.md#restore-disks). Pak použijte kroky Prostředí PowerShell v [části Vytvoření virtuálního počítače z obnovených disků](backup-azure-vms-automation.md#restore-an-azure-vm). |

## <a name="backup-or-restore-takes-time"></a>Zálohování nebo obnovení nějakou dobu trvá

Pokud zálohování trvá déle než 12 hodin nebo obnovení trvá déle než 6 hodin, přečtěte si [doporučené postupy](backup-azure-vms-introduction.md#best-practices)a důležité informace [o výkonu.](backup-azure-vms-introduction.md#backup-performance)

## <a name="vm-agent"></a>Agent virtuálního ms

### <a name="set-up-the-vm-agent"></a>Nastavení agenta virtuálního soudu

Agent virtuálních počítačů se obvykle už v ykretu je k dispozici ve virtuálních počítačích, které jsou vytvořené z galerie Azure. Ale virtuální počítače, které se migrují z místních datových center, nebudou mít nainstalovaného agenta virtuálního počítače. Pro tyto virtuální aplikace musí být agent virtuálního zařízení explicitně nainstalován.

#### <a name="windows-vms"></a>Virtuální počítače s Windows

* Stáhněte si a nainstalujte [MSI agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). K dokončení instalace potřebujete oprávnění správce.
* U virtuálních počítačů vytvořených pomocí klasického modelu nasazení [aktualizujte vlastnost virtuálního počítače](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) a označte, že je agent nainstalovaný. Tento krok není nutný pro virtuální počítače Azure Resource Manager.

#### <a name="linux-vms"></a>Virtuální počítače s Linuxem

* Nainstalujte nejnovější verzi agenta z distribučního úložiště. Podrobnosti o názvu balíčku naleznete v [úložišti linuxových agentů](https://github.com/Azure/WALinuxAgent).
* Pro virtuální počítače vytvořené pomocí klasického modelu nasazení [použijte tento blog](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) k aktualizaci vlastnosti virtuálního počítače a ověřte, zda je agent nainstalovaný. Tento krok není vyžadován pro virtuální počítače Správce prostředků.

### <a name="update-the-vm-agent"></a>Aktualizace agenta virtuálního softwaru

#### <a name="windows-vms"></a>Virtuální počítače s Windows

* Chcete-li aktualizovat agenta virtuálního zařízení, přeinstalujte [binární soubory agenta virtuálního zařízení](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Před aktualizací agenta se ujistěte, že během aktualizace agenta virtuálního počítače nedochází k žádným operacím zálohování.

#### <a name="linux-vms"></a>Virtuální počítače s Linuxem

* Chcete-li aktualizovat agenta virtuálního počítače s Linuxem, postupujte podle pokynů v článku [Aktualizace agenta virtuálního počítače pro Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

    > [!NOTE]
    > K aktualizaci agenta vždy použijte distribuční úložiště.

    Nestahujte kód agenta z GitHubu. Pokud nejnovější agent není k dispozici pro vaši distribuci, obraťte se na podporu distribuce pokyny k získání nejnovějšího agenta. Můžete také zkontrolovat nejnovější informace o [agentovi Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) v úložišti GitHub.

### <a name="validate-vm-agent-installation"></a>Ověření instalace agenta virtuálního zařízení

Ověřte verzi agenta virtuálního počítače na virtuálních počítačích s Windows:

1. Přihlaste se k virtuálnímu počítači Azure a přejděte do složky **C:\WindowsAzure\Packages**. Měli byste najít soubor **WaAppAgent.exe.**
2. Klepněte pravým tlačítkem myši na soubor a přejděte na **položku Vlastnosti**. Pak vyberte kartu **Podrobnosti.** Pole **Verze produktu** by mělo být 2.6.1198.718 nebo vyšší.

## <a name="troubleshoot-vm-snapshot-issues"></a>Poradce při potížích se snímkem virtuálního počítače

Zálohování virtuálních počítačových společností závisí na vydávání příkazů snímků do základního úložiště. Nemají přístup k úložišti nebo zpoždění v spuštění úlohy snímek může způsobit selhání úlohy zálohování. Následující podmínky mohou způsobit selhání úlohy snímku:

* **Virtuální počítače s nakonfigurovanou zálohou serveru SQL Server mohou způsobit zpoždění úlohy snímku**. Ve výchozím nastavení vytvoří záloha virtuálních počítačových virtuálních počítačích úplnou zálohu na virtuálních počítačích se systémem Windows. Virtuální počítače, ke kterým běží SQL Server s nakonfigurovanou zálohou serveru SQL Server, můžou zaznamenat zpoždění snímků. Pokud zpoždění snímku způsobit selhání zálohování, nastavte následující klíč registru:

   ```text
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

* **Stav virtuálního soudu je hlášena nesprávně, protože virtuální ho virtuálního zařízení je vypnutv RDP**. Pokud jste k vypnutí virtuálního počítače použili vzdálenou plochu, ověřte, zda je stav virtuálního počítače na portálu správný. Pokud stav není správný, použijte možnost **Vypnutí** na řídicím panelu virtuálního počítače portálu k vypnutí virtuálního počítače.
* **Pokud více než čtyři virtuální počítače sdílejí stejnou cloudovou službu, rozprostřete virtuální počítače mezi více zásad zálohování**. Zavěste časy zálohování, takže ne více než čtyři zálohy virtuálních počítače spustit ve stejnou dobu. Pokuste se oddělit počáteční časy v zásadách alespoň o hodinu.
* **Virtuální počítače běží na vysoké procesoru nebo paměti**. Pokud virtuální počítač běží s vysokou pamětí nebo využití procesoru, více než 90 procent, váš snímek úloha je ve frontě a zpožděné. Nakonec to dojdou. Pokud k tomuto problému dojde, zkuste zálohu na vyžádání.

## <a name="networking"></a>Síťové služby

Aby záloha virtuálního počítače IaaS fungovala, musí být uvnitř hosta povolena služba DHCP. Pokud potřebujete statickou privátní IP adresu, nakonfigurujte ji přes portál Azure nebo PowerShell. Ujistěte se, že je povolena možnost DHCP uvnitř virtuálního soudu.
Získejte další informace o nastavení statické IP adresy pomocí PowerShellu:

* [Jak přidat statickou interní IP adresu do existujícího virtuálního počítačů](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkinterfaceipconfig?view=azps-3.5.0#description)
* [Změna metody přidělení privátní IP adresy přiřazené k síťovému rozhraní](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)

