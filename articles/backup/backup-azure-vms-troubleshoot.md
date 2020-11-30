---
title: Řešení chyb zálohování pomocí virtuálních počítačů Azure
description: V tomto článku se dozvíte, jak řešit chyby zjištěné při zálohování a obnovení virtuálních počítačů Azure.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 08/30/2019
ms.openlocfilehash: cb25d9263648fbd92bc075751c1a8e627d03bd44
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325209"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>Řešení potíží se zálohováním virtuálních počítačů Azure

Můžete řešit chyby zjištěné při použití Azure Backup s informacemi uvedenými níže:

## <a name="backup"></a>Backup

Tato část popisuje selhání operace zálohování virtuálního počítače Azure.

### <a name="basic-troubleshooting"></a>Základní řešení potíží

* Ujistěte se, že agent virtuálního počítače (WA Agent) má [nejnovější verzi](./backup-azure-arm-vms-prepare.md#install-the-vm-agent).
* Ujistěte se, že je podporovaná verze operačního systému Windows nebo Linux, a podívejte se na [matrici podpory zálohování virtuálního počítače IaaS](./backup-support-matrix-iaas.md).
* Ověřte, že není spuštěná jiná služba zálohování.
  * Aby se zajistilo, že nedochází k žádným problémům s rozšířením snímků, [odinstalujte rozšíření pro vynucené načtení a pak zkuste zálohování zopakovat](./backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md).
* Ověřte, že virtuální počítač má připojení k Internetu.
  * Ujistěte se, že není spuštěná jiná služba zálohování.
* `Services.msc`Ověřte, že je **spuštěná** služba **agenta hosta systému Windows Azure** . Pokud chybí služba **Windows Azure Host agent** , nainstalujte ji z [zálohování virtuálních počítačů Azure do trezoru Recovery Services](./backup-azure-arm-vms-prepare.md#install-the-vm-agent).
* **Protokol událostí** může zobrazit selhání zálohování, které se nachází v jiných zálohovacích produktech, například zálohování serveru Windows Server, a není důvodem Azure Backup. K určení, zda se jedná o problém s Azure Backup, použijte následující postup:
  * Pokud dojde k chybě v **zálohování** položky ve zdroji nebo zprávě události, ověřte, jestli se zálohy zálohování virtuálních počítačů Azure IaaS úspěšně a jestli se vytvořil bod obnovení s požadovaným typem snímku.
  * Pokud Azure Backup funguje, bude problém nejspíš s jiným řešením zálohování.
  * Tady je příklad chyby Prohlížeč událostí 517, ve které Azure Backup pracovala správně, ale "Zálohování Windows Serveru" selhala: ![ selhání zálohování Windows serveru](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
  * Pokud se Azure Backup nedaří, vyhledejte odpovídající kód chyby v části běžné chyby zálohování virtuálních počítačů v tomto článku.

## <a name="common-issues"></a>Běžné problémy

Níže jsou uvedené běžné problémy se selháním zálohování virtuálních počítačů Azure.

### <a name="vmrestorepointinternalerror---antivirus-configured-in-the-vm-is-restricting-the-execution-of-backup-extension"></a>VMRestorePointInternalError – antivirová ochrana nakonfigurovaná na virtuálním počítači omezuje provádění záložního rozšíření.

Kód chyby: VMRestorePointInternalError

Pokud v době zálohování **Prohlížeč událostí protokoly aplikací** zobrazí **název aplikace s chybou: IaaSBcdrExtension.exe** pak se potvrdí, že antivirová ochrana nakonfigurovaná ve virtuálním počítači omezuje spuštění rozšíření zálohování.
Pokud chcete tento problém vyřešit, vylučte v konfiguraci antivirové ochrany adresáře a zkuste operaci zálohování zopakovat.

* `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`
* `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`

### <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime – při kopírování zálohovaných dat z trezoru vypršel časový limit.

Kód chyby: CopyingVHDsFromBackUpVaultTakingLongTime <br/>
Chybová zpráva: při kopírování zálohovaných dat z trezoru vypršel časový limit.

K tomu může dojít v důsledku přechodných chyb úložiště nebo nedostatečného účtu úložiště pro službu Backup pro přenos dat do trezoru v časovém limitu. Nakonfigurujte zálohování virtuálních počítačů pomocí těchto [osvědčených postupů](backup-azure-vms-introduction.md#best-practices) a zkuste operaci zálohování zopakovat.

### <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState – Virtuální počítač není ve stavu, který umožňuje zálohování

Kód chyby: UserErrorVmNotInDesirableState <br/>
Chybová zpráva: virtuální počítač není ve stavu, který umožňuje zálohování.<br/>

Operace zálohování se nezdařila, protože virtuální počítač je v neúspěšném stavu. Aby bylo možné úspěšně provést zálohování, virtuální počítač musí být ve stavu Spuštěno, Zastaveno nebo Zastaveno (přidělení zrušeno).

* Pokud je virtuální počítač v přechodném stavu mezi **spuštěním** a **vypnutím**, počkejte na změnu stavu. Potom aktivujte úlohu zálohování.
* Pokud se jedná o virtuální počítač se systémem Linux a používá modul jádra Security-Enhanced Linux, vylučte cestu agenta Azure Linux **/var/lib/waagent** ze zásad zabezpečení a ujistěte se, že je nainstalované rozšíření zálohování.

### <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed – nepovedlo se zablokovat jeden nebo více přípojných bodů virtuálního počítače, aby se mohl vytvořit snímek konzistentní se systémem souborů.

Kód chyby: UserErrorFsFreezeFailed <br/>
Chybová zpráva: Nepodařilo se zablokovat jeden nebo více přípojných bodů virtuálního počítače, aby bylo možné vytvořit snímek konzistentní se systémem souborů.

* Pomocí příkazu **umount** odpojte zařízení, pro které se stav systému souborů nečistí.
* Spusťte kontrolu konzistence systému souborů na těchto zařízeních pomocí příkazu **fsck** .
* Připojte zařízení znovu a zkuste operaci zálohování zopakovat.</ol>

### <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM/ExtensionInstallationFailedCOM/ExtensionInstallationFailedMDTC – instalace/operace rozšíření se nezdařila z důvodu chyby modelu COM+

Kód chyby: ExtensionSnapshotFailedCOM <br/>
Chybová zpráva: operace snímku se nepovedla kvůli chybě modelu COM+.

Kód chyby: ExtensionInstallationFailedCOM  <br/>
Chybová zpráva: Instalace rozšíření/operace selhala kvůli chybě modelu COM+.

Kód chyby: ExtensionInstallationFailedMDTC <br/>
Chybová zpráva: Instalace rozšíření se nezdařila s chybou "COM+ se nepovedlo kontaktovat Microsoft DTC (Distributed Transaction Coordinator) <br/>

Operace zálohování se nezdařila z důvodu problému se **systémovou aplikací com+** služby Windows.  Pokud chcete tento problém vyřešit, postupujte následovně:

* Zkuste spustit nebo restartovat **systémovou aplikaci modelu COM+** služby Windows (z příkazového řádku se zvýšenými oprávněními **– net start COMSysApp**).
* Ujistěte se, že je služba **DTC (Distributed Transaction Coordinator)** spuštěná jako účet **síťové služby** . Pokud ne, změňte ji tak, aby běžela jako účet **síťové služby** , a restartujte **systémovou aplikaci modelu COM+**.
* Pokud službu nemůžete restartovat, přeinstalujte **DTC (Distributed Transaction Coordinator)** službu pomocí následujících kroků:
  * Zastavte službu DTC (Distributed Transaction Coordinator).
  * Otevřete příkazový řádek (cmd).
  * Spusťte příkaz `msdtc -uninstall`.
  * Spusťte příkaz `msdtc -install`.
  * Spusťte službu DTC (Distributed Transaction Coordinator).
* Spusťte **systémovou aplikaci modelu COM+** služby systému Windows. Po spuštění **aplikace systému com+** spusťte úlohu zálohování z Azure Portal.</ol>

### <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState – Operace vytvoření snímku selhala kvůli zapisovačům VSS ve špatném stavu

Kód chyby: ExtensionFailedVssWriterInBadState <br/>
Chybová zpráva: operace snímku se nezdařila, protože zapisovače VSS byly ve špatném stavu.

K této chybě dochází, protože zapisovače VSS byly ve špatném stavu. Rozšíření Azure Backup komunikují se zapisovači VSS, aby bylo možné pořizovat snímky disků. Pokud chcete tento problém vyřešit, postupujte následovně:

Krok 1: Restartujte zapisovače služby VSS, které jsou ve špatném stavu.

* Z příkazového řádku se zvýšenými oprávněními spusťte příkaz ```vssadmin list writers``` .
* Výstup obsahuje všechny zapisovače VSS a jejich stav. U každého zapisovače VSS se stavem, který není **[1] stabilní**, restartujte příslušnou službu zapisovače VSS.
* Chcete-li službu restartovat, spusťte následující příkazy z příkazového řádku se zvýšenými oprávněními:

 ```net stop serviceName``` <br>
 ```net start serviceName```

> [!NOTE]
> Restartování některých služeb může mít dopad na produkční prostředí. Zajistěte, aby byl proces schválení následován a služba se restartovala v naplánovaném výpadku.

Krok 2: Pokud restartování zapisovačů VSS nevyřešilo problém, spusťte následující příkaz z příkazového řádku se zvýšenými oprávněními (jako správce), aby se zabránilo vytváření vláken pro snímky objektů BLOB.

```console
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotWithoutThreads /t REG_SZ /d True /f
```

Krok 3: Pokud kroky 1 a 2 nevyřešily problém, může být selhání způsobeno vypršením časového limitu zapisovače VSS z důvodu omezeného IOPS.<br>

Chcete-li ověřit, přejděte do části ***systémové a prohlížeč událostí protokoly aplikací** _ a zkontrolujte následující chybovou zprávu:<br>
Vypršel časový limit poskytovatele stínové kopie _The při ukládání zápisů do svazku, ve kterém se stín kopíruje. To je pravděpodobně způsobeno nadměrnými činnostmi na svazku aplikace nebo systémovou službou. Zkuste to znovu později, až bude snížena aktivita na svazku. *<br>

Řešení:

* Vyhledejte možnosti pro distribuci zatížení mezi disky virtuálních počítačů. Tím se sníží zatížení na jednom disku. [Omezení IOPS můžete kontrolovat tím, že povolíte diagnostické metriky na úrovni úložiště](../virtual-machines/troubleshooting/performance-diagnostics.md#install-and-run-performance-diagnostics-on-your-vm).
* Změňte zásadu zálohování tak, aby prováděla zálohování v době mimo špičku, kdy je zatížení virtuálního počítače na nejnižší úrovni.
* Upgradujte disky Azure tak, aby podporovaly vyšší IOPs. [Další informace](../virtual-machines/disks-types.md)

### <a name="extensionfailedvssserviceinbadstate---snapshot-operation-failed-due-to-vss-volume-shadow-copy-service-in-bad-state"></a>ExtensionFailedVssServiceInBadState – Operace vytvoření snímku selhala kvůli špatnému stavu služby Stínová kopie svazku (VSS)

Kód chyby: ExtensionFailedVssServiceInBadState <br/>
Chybová zpráva: operace snímku se nepovedla kvůli službě Stínová kopie svazku (VSS) ve špatném stavu.

K této chybě dochází, protože služba VSS je ve špatném stavu. Rozšíření Azure Backup komunikují se službou VSS, aby bylo možné pořizovat snímky disků. Pokud chcete tento problém vyřešit, postupujte následovně:

Restartujte službu VSS (Stínová kopie svazku).

* Přejděte do souboru Services. msc a restartujte službu Stínová kopie svazku.<br>
(nebo)<br>
* Z příkazového řádku se zvýšenými oprávněními spusťte následující příkazy:

 ```net stop VSS``` <br>
 ```net start VSS```

Pokud problém přetrvává, restartujte virtuální počítač v naplánovaném výpadku.

### <a name="usererrorskunotavailable---vm-creation-failed-as-vm-size-selected-is-not-available"></a>UserErrorSkuNotAvailable – vytvoření virtuálního počítače se nezdařilo, protože vybraná velikost virtuálního počítače není k dispozici.

Kód chyby: chybová zpráva UserErrorSkuNotAvailable: Vytvoření virtuálního počítače se nezdařilo, protože vybraná velikost virtuálního počítače není k dispozici.

K této chybě dochází, protože velikost virtuálního počítače vybraná během operace obnovení je Nepodporovaná velikost. <br>

Chcete-li tento problém vyřešit, použijte během operace obnovení možnost [obnovit disky](./backup-azure-arm-restore-vms.md#restore-disks) . Pomocí těchto disků vytvořte virtuální počítač ze seznamu [dostupných podporovaných velikostí virtuálních počítačů](./backup-support-matrix-iaas.md#vm-compute-support) pomocí [rutin PowerShellu](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

### <a name="usererrormarketplacevmnotsupported---vm-creation-failed-due-to-market-place-purchase-request-being-not-present"></a>UserErrorMarketPlaceVMNotSupported – vytvoření virtuálního počítače se nezdařilo, protože není k dispozici požadavek na nákup na trhu.

Kód chyby: chybová zpráva UserErrorMarketPlaceVMNotSupported: Vytvoření virtuálního počítače se nezdařilo z důvodu nepřítomnosti žádosti o nákup na trhu v místě.

Azure Backup podporuje zálohování a obnovení virtuálních počítačů, které jsou k dispozici v Azure Marketplace. K této chybě dochází, když se pokoušíte obnovit virtuální počítač (s konkrétním nastavením pro plán/Vydavatel), který už není dostupný v Azure Marketplace. další [informace najdete tady](/legal/marketplace/participation-policy#offering-suspension-and-removal).

* Pokud chcete tento problém vyřešit, použijte během operace obnovení možnost [obnovit disky](./backup-azure-arm-restore-vms.md#restore-disks) a pak použijte rutiny [PowerShellu](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks) nebo [Azure CLI](./tutorial-restore-disk.md) k vytvoření virtuálního počítače s nejnovějšími informacemi z webu Marketplace, které odpovídají danému virtuálnímu počítači.
* Pokud Vydavatel nemá žádné informace z Marketplace, můžete k načtení dat použít datové disky a můžete je připojit k existujícímu virtuálnímu počítači.

### <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure – při analýze konfigurace záložního rozšíření došlo k chybě.

Kód chyby: ExtensionConfigParsingFailure<br/>
Chybová zpráva: při analýze konfigurace záložního rozšíření došlo k chybě.

K této chybě dochází, protože se změnila oprávnění v adresáři **MachineKeys** : **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.
Spusťte následující příkaz a ověřte, zda jsou oprávnění v adresáři **MachineKeys** výchozí: `icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys` .

Výchozí oprávnění jsou následující:

* Everyone: (R, W)
* BUILTIN\Administrators: (F)

Pokud se v adresáři **MachineKeys** nacházejí oprávnění, která se liší od výchozích hodnot, použijte následující postup pro správné oprávnění, odstraňte certifikát a spusťte zálohování.

1. Opravte oprávnění v adresáři **MachineKeys** . Pomocí vlastnosti zabezpečení Průzkumníka a pokročilého nastavení zabezpečení v adresáři obnovte oprávnění zpět na výchozí hodnoty. Odeberte všechny objekty uživatele kromě výchozích hodnot z adresáře a ujistěte se, že oprávnění **Everyone** má zvláštní přístup, a to následujícím způsobem:

   * Výpis složky/čtení dat
   * Číst atributy
   * Číst rozšířené atributy
   * Vytváření souborů/zápis dat
   * Vytváření složek/připojovat data
   * Zapisovat atributy
   * Zápis rozšířených atributů
   * Oprávnění ke čtení
2. Odstraňte všechny certifikáty, které jsou **vydané pro** , model nasazení Classic nebo **generátor certifikátů Windows Azure CRP**:

   * [Otevřete certifikáty v konzole místního počítače](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in).
   * V části **osobní**  >  **certifikáty** odstraňte všechny certifikáty, které jsou **vydané pro** , model nasazení Classic nebo **generátor certifikátů Windows Azure CRP**.
3. Spustí úlohu zálohování virtuálního počítače.

### <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState – stav rozšíření nepodporuje operaci zálohování.

Kód chyby: ExtensionStuckInDeletionState <br/>
Chybová zpráva: stav rozšíření není pro operaci zálohování podporován.

Operace zálohování se nezdařila z důvodu nekonzistentního stavu záložního rozšíření. Pokud chcete tento problém vyřešit, postupujte následovně:

* Ujistěte se, že je agent hosta nainstalovaný a funkční.
* Z Azure Portal přejít na **Virtual Machine**  >  **všechna nastavení**  >  **rozšíření** pro virtuální počítače
* Vyberte záložní rozšíření VmSnapshot nebo VmSnapshotLinux a vyberte **odinstalovat**.
* Po odstranění rozšíření zálohování zkuste operaci zálohování znovu.
* Další operace zálohování nainstaluje nové rozšíření v požadovaném stavu.

### <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError-snímková operace se nezdařila, protože byl překročen limit počtu snímků pro některé připojené disky.

Kód chyby: ExtensionFailedSnapshotLimitReachedError  <br/>
Chybová zpráva: operace snímku se nezdařila, protože byl překročen limit počtu snímků pro některé připojené disky.

Operace snímku se nezdařila, protože byl překročen limit počtu snímků pro některé připojené disky. Proveďte následující kroky pro řešení potíží a potom operaci opakujte.

* Odstraňte objekty blob disku – snímky, které nejsou povinné. Buďte opatrní, abyste neodstranili objekty blob disku. Odstranit se dají jenom objekty blob snímku.
* Pokud je v účtech úložiště disků virtuálního počítače povolené obnovitelné odstranění, nakonfigurujte uchovávání obnovitelného odstranění, aby byly existující snímky menší, než je povolené maximum v jakémkoli okamžiku.
* Pokud je v zálohovaném virtuálním počítači povolená možnost Azure Site Recovery, proveďte následující kroky:

  * Ujistěte se, že hodnota **isanysnapshotfailed** je v/etc/Azure/vmbackup.conf nastavena na hodnotu false.
  * Naplánujte Azure Site Recovery v jinou dobu, takže nekoliduje s operací zálohování.

### <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive-snímková operace selhala kvůli nedostatečným prostředkům virtuálního počítače.

Kód chyby: ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
Chybová zpráva: operace snímku se nepovedla kvůli nedostatečným prostředkům virtuálního počítače.

Operace zálohování virtuálního počítače selhala kvůli zpoždění při volání sítě při provádění operace snímku. Pokud chcete tento problém vyřešit, proveďte krok 1. Pokud problém přetrvává, zkuste kroky 2 a 3.

**Krok 1**: vytvoření snímku pomocí hostitele

Z příkazového řádku se zvýšenými oprávněními (admin) spusťte následující příkaz:

```console
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Tím se zajistí, že se všechny snímky pořídí přes hostitele, a ne hosta. Zopakujte operaci zálohování.

**Krok 2**: zkuste změnit plán zálohování na čas menšího zatížení virtuálního počítače (například méně procesoru nebo IOPS).

**Krok 3**: zkuste [zvětšit velikost virtuálního počítače](../virtual-machines/windows/resize-vm.md) a zkuste operaci zopakovat.

### <a name="320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found"></a>320001, ResourceNotFound – nepovedlo se provést operaci, protože virtuální počítač už neexistuje/400094, BCMV2VMNotFound – virtuální počítač neexistuje nebo se nenašel virtuální počítač Azure.

Kód chyby: 320001, ResourceNotFound <br/> Chybová zpráva: operaci nejde provést, protože virtuální počítač už neexistuje. <br/> <br/> Kód chyby: 400094, BCMV2VMNotFound <br/> Chybová zpráva: virtuální počítač neexistuje. <br/>
Virtuální počítač Azure se nenašel.

K této chybě dojde, když se odstraní primární virtuální počítač, ale zásady zálohování pořád hledají virtuální počítač, který se má zálohovat. Chcete-li tuto chybu opravit, proveďte následující kroky:

* Znovu vytvořit virtuální počítač se stejným názvem a stejným názvem skupiny prostředků, **název cloudové služby**<br>nebo
* Zastavte ochranu virtuálního počítače s nebo bez odstranění zálohovaných dat. Další informace najdete v tématu [zastavení ochrany virtuálních počítačů](backup-azure-manage-vms.md#stop-protecting-a-vm).</li></ol>

### <a name="usererrorbcmpremiumstoragequotaerror---could-not-copy-the-snapshot-of-the-virtual-machine-due-to-insufficient-free-space-in-the-storage-account"></a>UserErrorBCMPremiumStorageQuotaError – snímek virtuálního počítače nejde zkopírovat kvůli nedostatku volného místa v účtu úložiště.

Kód chyby: UserErrorBCMPremiumStorageQuotaError<br/> Chybová zpráva: snímek virtuálního počítače nejde zkopírovat kvůli nedostatku volného místa v účtu úložiště.

 Pro virtuální počítače úrovně Premium v zásobníku zálohování virtuálních počítačů v1 zkopírujeme snímek do účtu úložiště. Tento krok zajistí, že provoz správy zálohování, který funguje na snímku, neomezuje počet IOPS dostupných pro aplikaci pomocí prémiových disků. <br><br>Pro celkové místo v účtu úložiště doporučujeme přidělit jenom 50%, 17,5 TB. Služba Azure Backup pak může zkopírovat snímek do účtu úložiště a přenést data z tohoto zkopírovaného umístění v účtu úložiště do trezoru.

### <a name="380008-azurevmoffline---failed-to-install-microsoft-recovery-services-extension-as-virtual-machine--is-not-running"></a>380008, AzureVmOffline – nepovedlo se nainstalovat rozšíření Microsoft Recovery Services, protože virtuální počítač není spuštěný.

Kód chyby: 380008, AzureVmOffline <br/> Chybová zpráva: Nepodařilo se nainstalovat rozšíření Microsoft Recovery Services, protože virtuální počítač není spuštěný.

Agent virtuálního počítače je předpokladem pro rozšíření Azure Recovery Services. Nainstalujte agenta virtuálního počítače Azure a restartujte operaci registrace. <br> <ol> <li>Ověřte, jestli je agent virtuálního počítače nainstalovaný správně. <li>Ujistěte se, že je správně nastavený příznak konfigurace virtuálního počítače.</ol> Přečtěte si další informace o instalaci agenta virtuálního počítače a o tom, jak ověřit instalaci agenta virtuálního počítače.

### <a name="extensionsnapshotbitlockererror---the-snapshot-operation-failed-with-the-volume-shadow-copy-service-vss-operation-error"></a>ExtensionSnapshotBitlockerError – operace snímku selhala s chybou operace služba Stínová kopie svazku (VSS).

Kód chyby: ExtensionSnapshotBitlockerError <br/> Chybová zpráva: operace snímku se nezdařila s chybou operace služba Stínová kopie svazku (VSS) **. Tato jednotka je uzamčena nástroj BitLocker Drive Encryption. Tuto jednotku musíte odemknout z ovládacích panelů.**

Vypněte BitLocker pro všechny jednotky na virtuálním počítači a zkontrolujte, jestli je problém VSS vyřešený.

### <a name="vmnotindesirablestate---the-vm-isnt-in-a-state-that-allows-backups"></a>VmNotInDesirableState – virtuální počítač není ve stavu, který umožňuje zálohování.

Kód chyby: VmNotInDesirableState <br/> Chybová zpráva: virtuální počítač není ve stavu, který umožňuje zálohování.

* Pokud je virtuální počítač v přechodném stavu mezi **spuštěním** a **vypnutím**, počkejte na změnu stavu. Potom aktivujte úlohu zálohování.
* Pokud se jedná o virtuální počítač se systémem Linux a používá modul jádra Security-Enhanced Linux, vylučte cestu agenta Azure Linux **/var/lib/waagent** ze zásad zabezpečení a ujistěte se, že je nainstalované rozšíření zálohování.

* Agent virtuálního počítače se na virtuálním počítači nenachází: <br>Nainstalujte libovolný požadavek a agenta virtuálního počítače. Pak restartujte operaci. | Přečtěte si další informace o [instalaci agenta virtuálního počítače a o tom, jak ověřit instalaci agenta virtuálního počítače](#vm-agent).

### <a name="extensionsnapshotfailednosecurenetwork---the-snapshot-operation-failed-because-of-failure-to-create-a-secure-network-communication-channel"></a>ExtensionSnapshotFailedNoSecureNetwork – operace snímku se nezdařila, protože došlo k chybě při vytváření zabezpečeného komunikačního kanálu sítě.

Kód chyby: ExtensionSnapshotFailedNoSecureNetwork <br/> Chybová zpráva: operace snímku se nezdařila, protože došlo k chybě při vytváření zabezpečeného kanálu komunikace v síti.

* Otevřete Editor registru spuštěním **regedit.exe** v režimu se zvýšenými oprávněními.
* Identifikujte všechny verze .NET Framework přítomné ve vašem systému. Nacházejí se v hierarchii **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft** klíčů registru.
* Pro každý .NET Framework přítomný v klíči registru přidejte následující klíč: <br> **Do schusestrongcrypto "= DWORD: 00000001**. </ol>

### <a name="extensionvcredistinstallationfailure---the-snapshot-operation-failed-because-of-failure-to-install-visual-c-redistributable-for-visual-studio-2012"></a>ExtensionVCRedistInstallationFailure – operace snímku se nezdařila, protože došlo k chybě při instalaci Distribuovatelné součásti Visual C++ pro Visual Studio 2012

Kód chyby: ExtensionVCRedistInstallationFailure <br/> Chybová zpráva: operace snímku se nezdařila, protože došlo k chybě při instalaci Distribuovatelné součásti Visual C++ pro Visual Studio 2012.

* Přejděte na `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion` vcredist2013_x64 a nainstalujte ji.<br/>Ujistěte se, že hodnota klíče registru, která umožňuje instalaci služby, je nastavená na správnou hodnotu. To znamená, že nastaví **počáteční** hodnotu v **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** na **3** a ne **4**. <br><br>Pokud stále máte problémy s instalací, restartujte instalační službu spuštěním příkazu **msiexec/unregister** následovaným příkazem **msiexec/Register** z příkazového řádku se zvýšenými oprávněními.
* Zkontrolujte protokol událostí a ověřte, jestli jste všímátei problémy související s přístupem. Příklad: *produkt: Microsoft Visual C++ 2013 x64 minimální modul runtime-12.0.21005--Error 1401. nelze vytvořit klíč: Software\Classes.  Chyba systému 5.  Ověřte, zda máte dostatečný přístup k tomuto klíči, nebo se obraťte na pracovníky podpory.* <br><br> Zajistěte, aby měl účet správce nebo uživatele dostatečná oprávnění k aktualizaci klíče registru **HKEY_LOCAL_MACHINE\SOFTWARE\Classes**. Poskytněte dostatečná oprávnění a restartujte agenta hosta systému Windows Azure.<br><br> <li> Pokud máte antivirové produkty, ujistěte se, že mají správná pravidla vyloučení, která umožňují instalaci.

### <a name="usererrorrequestdisallowedbypolicy---an-invalid-policy-is-configured-on-the-vm-which-is-preventing-snapshot-operation"></a>UserErrorRequestDisallowedByPolicy – Na virtuálním počítači jsou nakonfigurované neplatné zásady, které brání operaci vytvoření snímku

Kód chyby: UserErrorRequestDisallowedByPolicy <BR> Chybová zpráva: na virtuálním počítači je nakonfigurovaná neplatná zásada, která znemožňuje operaci snímku.

Pokud máte Azure Policy, který [řídí značky v rámci vašeho prostředí](../governance/policy/tutorials/govern-tags.md), zvažte možnost změnit zásadu z [efektu zamítnutí](../governance/policy/concepts/effects.md#deny) na [efekt úprav](../governance/policy/concepts/effects.md#modify)nebo vytvořit skupinu prostředků ručně podle [schématu pojmenování vyžadovaného Azure Backup](./backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines).

## <a name="jobs"></a>Úlohy

| Podrobnosti o chybě | Alternativní řešení |
| --- | --- |
| Zrušení není pro tento typ úlohy podporováno: <br>Počkejte, až se úloha dokončí. |Žádné |
| Úloha není ve stavu, který je možné zrušit: <br>Počkejte, až se úloha dokončí. <br>**nebo**<br> Vybraná úloha není ve stavu, který je možné zrušit: <br>Počkejte, až se úloha dokončí. |Je pravděpodobnější, že úloha je skoro dokončená. Počkejte, než se úloha dokončí.|
| Zálohování nemůže úlohu zrušit, protože neprobíhá: <br>Zrušení je podporováno pouze pro probíhající úlohy. Zkuste zrušit probíhající úlohu. |K této chybě dochází z důvodu přechodného stavu. Počkejte minutu a zkuste operaci zrušit. |
| Zálohování se nepodařilo zrušit úlohu: <br>Počkejte, až se úloha dokončí. |Žádné |

## <a name="restore"></a>Obnovení

### <a name="disks-appear-offline-after-file-restore"></a>Po obnovení souboru se disky zobrazí v režimu offline.

Po obnovení si všimněte, že jsou disky v režimu offline:

* Ověřte, zda počítač, ve kterém je spuštěn skript, splňuje požadavky na operační systém. [Další informace](./backup-azure-restore-files-from-vm.md#step-3-os-requirements-to-successfully-run-the-script).  
* Ujistěte se, že neprovádíte obnovení do stejného zdroje. další [informace](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)najdete v části.

### <a name="usererrorinstantrpnotfound---restore-failed-because-the-snapshot-of-the-vm-was-not-found"></a>UserErrorInstantRpNotFound – obnovení se nepovedlo, protože se nenašel snímek virtuálního počítače.

Kód chyby: UserErrorInstantRpNotFound <br>
Chybová zpráva: operace obnovení se nezdařila, protože nebyl nalezen snímek virtuálního počítače. Snímek se mohl odstranit, zkontrolujte prosím.<br>

K této chybě dochází při pokusu o obnovení z bodu obnovení, který nebyl přenesen do trezoru a byl odstraněn ve fázi snímku. 
<br>
Pokud chcete tento problém vyřešit, zkuste obnovit virtuální počítač z jiného bodu obnovení.<br>

#### <a name="common-errors"></a>Běžné chyby 
| Podrobnosti o chybě | Alternativní řešení |
| --- | --- |
| Obnovení selhalo s interní chybou cloudu. |<ol><li>Cloudová služba, na kterou se pokoušíte obnovit, je nakonfigurovaná pomocí nastavení DNS. Můžete kontrolovat: <br>**$Deployment = Get-AzureDeployment-ServiceName "ServiceName" – slot "provozní" Get-AzureDns-DnsSettings $Deployment. DnsSettings**.<br>Pokud je nakonfigurovaná **adresa** , nakonfigurují se nastavení DNS.<br> <li>Cloudová služba, na kterou se pokoušíte obnovit, má nakonfigurovanou **vyhrazenou IP adresu** a stávající virtuální počítače v cloudové službě jsou ve stavu Zastaveno. Můžete ověřit, že cloudová služba rezervovala IP adresu pomocí následujících rutin PowerShellu: **$Deployment = Get-AzureDeployment-ServiceName "ServiceName" – slot "produkční" $DEP. ReservedIPName**. <br><li>Pokoušíte se obnovit virtuální počítač pomocí následujících speciálních síťových konfigurací do stejné cloudové služby: <ul><li>Virtuální počítače s konfigurací nástroje pro vyrovnávání zatížení, interní a externí.<li>Virtuální počítače s několika rezervovanými IP adresami. <li>Virtuální počítače s několika síťovými kartami. </ul><li>V uživatelském rozhraní vyberte novou cloudovou službu nebo si přečtěte [důležité informace o obnovení](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) virtuálních počítačů se speciální konfigurací sítě.</ol> |
| Vybraný název DNS je již obsazen: <br>Zadejte jiný název DNS a zkuste to znovu. |Tento název DNS odkazuje na název cloudové služby, obvykle končící na **. cloudapp.NET**. Tento název musí být jedinečný. Pokud se zobrazí tato chyba, musíte během obnovování zvolit jiný název virtuálního počítače. <br><br> Tato chyba se zobrazí pouze uživatelům Azure Portal. Operace obnovení prostřednictvím prostředí PowerShell bude úspěšná, protože obnoví jenom disky a virtuální počítač nevytvoří. Tato chyba se projeví, když po operaci obnovení disku bude virtuální počítač explicitně vytvořen. |
| Zadaná konfigurace virtuální sítě není správná: <br>Zadejte jinou konfiguraci virtuální sítě a zkuste to znovu. |Žádné |
| Zadaná cloudová služba používá rezervovanou IP adresu, která se neshoduje s konfigurací obnoveného virtuálního počítače: <br>Zadejte jinou cloudovou službu, která nepoužívá rezervovanou IP adresu. Nebo vyberte jiný bod obnovení, ze kterého chcete obnovit. |Žádné |
| Cloudová služba dosáhla svého limitu počtu vstupních koncových bodů: <br>Zkuste operaci zopakovat zadáním jiné cloudové služby nebo pomocí existujícího koncového bodu. |Žádné |
| Recovery Services trezor a cílový účet úložiště jsou ve dvou různých oblastech: <br>Zajistěte, aby byl účet úložiště zadaný v rámci operace obnovení ve stejné oblasti Azure jako váš Recovery Services trezor. |Žádné |
| Účet úložiště zadaný pro operaci obnovení není podporovaný: <br>Podporují se jenom účty úložiště Basic a Standard s místně redundantním nebo geograficky redundantním nastavením replikace. Vyberte podporovaný účet úložiště. |Žádné |
| Typ účtu úložiště zadaný pro operaci obnovení není online: <br>Ujistěte se, že je účet úložiště zadaný v operaci obnovení online. |K této chybě mohlo dojít z důvodu přechodné chyby v Azure Storage nebo z důvodu výpadku. Vyberte jiný účet úložiště. |
| Byla dosažena kvóta skupiny prostředků: <br>Odstraňte některé skupiny prostředků z Azure Portal nebo požádejte podporu Azure o navýšení limitů. |Žádné |
| Vybraná podsíť neexistuje: <br>Vyberte podsíť, která existuje. |Žádné |
| Služba zálohování nemá autorizaci pro přístup k prostředkům ve vašem předplatném. |Chcete-li tuto chybu vyřešit, nejprve obnovte disky pomocí postupu v části [Obnovení zálohovaných disků](backup-azure-arm-restore-vms.md#restore-disks). Pak použijte kroky prostředí PowerShell v části [Vytvoření virtuálního počítače z obnovených disků](backup-azure-vms-automation.md#restore-an-azure-vm). |

## <a name="backup-or-restore-takes-time"></a>Zálohování nebo obnovení trvá čas

Pokud zálohování trvá déle než 12 hodin nebo obnovení trvá déle než 6 hodin, prostudujte si [osvědčené postupy](backup-azure-vms-introduction.md#best-practices)a [požadavky na výkon](backup-azure-vms-introduction.md#backup-performance) .

## <a name="vm-agent"></a>Agent virtuálního počítače

### <a name="set-up-the-vm-agent"></a>Nastavení agenta virtuálního počítače

Agent virtuálního počítače je obvykle přítomen na virtuálních počítačích vytvořených z Galerie Azure. Virtuální počítače, které jsou migrovány z místních datových center, ale nebudou mít nainstalovaného agenta virtuálního počítače. Pro tyto virtuální počítače musí být agent virtuálního počítače nainstalovaný explicitně.

#### <a name="windows-vms---set-up-the-agent"></a>Virtuální počítače s Windows – nastavení agenta

* Stáhněte si a nainstalujte [MSI agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). K dokončení instalace potřebujete oprávnění správce.
* Pro virtuální počítače vytvořené pomocí modelu nasazení Classic [aktualizujte vlastnost VM](../virtual-machines/troubleshooting/install-vm-agent-offline.md#use-the-provisionguestagent-property-for-classic-vms) tak, aby označovala, že agent je nainstalovaný. Tento krok není nutný pro Azure Resource Manager virtuálních počítačů.

#### <a name="linux-vms---set-up-the-agent"></a>Virtuální počítače se systémem Linux – nastavení agenta

* Nainstalujte nejnovější verzi agenta z distribučního úložiště. Podrobnosti o názvu balíčku najdete v části [úložiště agenta pro Linux](https://github.com/Azure/WALinuxAgent).
* Pro virtuální počítače vytvořené pomocí modelu nasazení Classic [aktualizujte vlastnost VM](../virtual-machines/troubleshooting/install-vm-agent-offline.md#use-the-provisionguestagent-property-for-classic-vms) a ověřte, jestli je agent nainstalovaný. Tento krok není nutný pro Správce prostředků virtuálních počítačů.

### <a name="update-the-vm-agent"></a>Aktualizace agenta virtuálního počítače

#### <a name="windows-vms---update-the-agent"></a>Virtuální počítače s Windows – aktualizace agenta

* Pokud chcete aktualizovat agenta virtuálního počítače, přeinstalujte [binární soubory agenta virtuálního počítače](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Než agenta aktualizujete, ujistěte se, že během aktualizace agenta virtuálního počítače nedochází k žádným operacím zálohování.

#### <a name="linux-vms---update-the-agent"></a>Virtuální počítače se systémem Linux – aktualizace agenta

* Chcete-li aktualizovat agenta virtuálního počítače se systémem Linux, postupujte podle pokynů v článku [aktualizace agenta virtuálního počítače se systémem Linux](../virtual-machines/extensions/update-linux-agent.md?toc=/azure/virtual-machines/linux/toc.json).

    > [!NOTE]
    > K aktualizaci agenta vždy použijte úložiště distribuce.

    Nestahujte kód agenta z GitHubu. Pokud pro vaši distribuci není k dispozici nejnovější agent, obraťte se na podporu distribuce, kde najdete pokyny k získání nejnovějšího agenta. V úložišti GitHub můžete také vyhledat nejnovější informace o [agentovi systému Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) .

### <a name="validate-vm-agent-installation"></a>Ověřit instalaci agenta virtuálního počítače

Ověření verze agenta virtuálního počítače ve virtuálních počítačích s Windows:

1. Přihlaste se k virtuálnímu počítači Azure a přejděte do složky **C:\WindowsAzure\Packages**. Měli byste najít soubor **WaAppAgent.exe** .
2. Klikněte na soubor pravým tlačítkem a přejděte na **vlastnosti**. Pak vyberte kartu **Podrobnosti** . Pole **verze produktu** by mělo být 2.6.1198.718 nebo vyšší.

## <a name="troubleshoot-vm-snapshot-issues"></a>Řešení potíží se snímky virtuálních počítačů

Zálohování virtuálního počítače se spoléhá na vystavování příkazů snímků pro základní úložiště. Při spuštění úlohy snímku není nutné mít přístup k úložišti ani prodlevám. může dojít k selhání úlohy zálohování. Následující podmínky mohou způsobit selhání úlohy snímku:

* **Virtuální počítače s nakonfigurovaným zálohováním SQL Server můžou způsobit zpoždění úlohy snímku**. Ve výchozím nastavení vytvoří služba Backup VM na virtuálních počítačích s Windows úplnou zálohu stínové kopie svazku (VSS). U virtuálních počítačů, které spouštějí SQL Server s nakonfigurovaným SQL Server zálohováním, může docházet ke zpoždění snímků. Pokud se zpoždění snímků způsobují selhání zálohování, nastavte následující klíč registru:

   ```console
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

* **Stav virtuálního počítače je nesprávně hlášený, protože virtuální počítač je vypnutý v RDP**. Pokud jste virtuální počítač vypnuli pomocí vzdálené plochy, ověřte, jestli je stav virtuálního počítače na portálu správný. Pokud stav není správný, vypněte virtuální počítač pomocí možnosti **vypnutí** na řídicím panelu virtuálních počítačů na portálu.
* **Pokud se stejnou cloudovou službou sdílí víc než čtyři virtuální počítače, rozšíří se virtuální počítače mezi několik zásad zálohování**. Rozložte časy zálohování, takže se ve stejnou dobu nespouštějí žádné více než čtyři zálohy virtuálních počítačů. Zkuste oddělit časy spuštění v zásadách alespoň po hodinu.
* **Virtuální počítač běží s vysokým procesorem nebo pamětí**. Pokud je virtuální počítač spuštěný při vysoké paměti nebo využití procesoru 90, bude se vaše úloha snímku zařadit do fronty a zpozdit. Nakonec vyprší časový limit. Pokud k tomuto problému dojde, vyzkoušejte zálohování na vyžádání.

## <a name="networking"></a>Sítě

Aby fungovala záloha virtuálního počítače IaaS, musí být v hostovi povolen protokol DHCP. Pokud potřebujete statickou privátní IP adresu, nakonfigurujte ji pomocí Azure Portal nebo PowerShellu. Ujistěte se, že je ve virtuálním počítači povolená možnost DHCP.
Získejte další informace o tom, jak nastavit statickou IP adresu prostřednictvím PowerShellu:

* [Postup přidání statické interní IP adresy do existujícího virtuálního počítače](/powershell/module/az.network/set-aznetworkinterfaceipconfig#description)
* [Změna metody alokace pro soukromou IP adresu přiřazenou síťovému rozhraní](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)