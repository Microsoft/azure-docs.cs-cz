---
title: Zálohování řešení potíží s virtuálními počítači Azure
description: Řešení zálohování a obnovení virtuálních počítačů Azure
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 8/7/2018
ms.author: trinadhk
ms.openlocfilehash: 1714a29e4b27f6363d748ceb180f56ba98c713bb
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54809526"
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Odstraňování potíží se zálohováním virtuálních počítačů Azure
Řešení potíží s chybami při pomocí služby Azure Backup pomocí informací uvedených v následující tabulce došlo k chybě:

| Podrobnosti o chybě | Alternativní řešení |
| ------ | --- |
| Zálohování nebylo možné provést operaci, protože už existuje virtuální počítač (VM): <br>Zastavte ochranu virtuálního počítače bez odstranění dat záloh. Další informace najdete v tématu [zastavení ochrany virtuálních počítačů](http://go.microsoft.com/fwlink/?LinkId=808124). |K této chybě dochází, když primární virtuální počítač se odstraní, ale zásad zálohování vypadá stále pro virtuální počítač k zálohování. Chcete-li vyřešit tuto chybu, proveďte následující kroky: <ol><li> Znovu vytvořte virtuální počítač se stejným názvem a stejný název skupiny prostředků, **název cloudové služby**,<br>**nebo**</li><li> Zastavte ochranu virtuálního počítače s nebo bez něj odstraňují se záložní data. Další informace najdete v tématu [zastavení ochrany virtuálních počítačů](https://go.microsoft.com/fwlink/?LinkId=808124).</li></ol> |
| Operace snímku nebyla úspěšná, protože byl na virtuálním počítači bez připojení k síti: <br>Ujistěte se, že virtuální počítač má přístup k síti. Pro snímek úspěšný buď seznamu povolených IP adres Azure datacenter IP rozsahů adres nebo nastavení proxy serveru pro přístup k síti. Další informace najdete v tématu [selhání Poradce při potížích s Azure Backup: Potíže s agentů nebo rozšíření](http://go.microsoft.com/fwlink/?LinkId=800034). <br><br>Pokud už používáte proxy server, ujistěte se, že jsou správně nakonfigurované nastavení proxy serveru. | K této chybě dochází při zamítnutí odchozí připojení k Internetu na virtuálním počítači. Rozšíření snímku virtuálního počítače vyžaduje připojení k Internetu pro pořízení snímku discích. Zobrazit [ExtensionSnapshotFailedNoNetwork](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine). |
| Agent virtuálního počítače Azure (VM Agent) nemůže komunikovat se službou Azure Backup: <br>Ujistěte se, že virtuální počítač má síťové připojení a agent virtuálního počítače je na nejnovější verzi a spouštění. Další informace najdete v tématu [selhání Poradce při potížích s Azure Backup: Potíže s agentů nebo rozšíření](http://go.microsoft.com/fwlink/?LinkId=800034). |K této chybě dochází, pokud dojde k nějakému problému s agentem virtuálního počítače, nebo blokovaný přístup k síti na infrastrukturu Azure nějakým způsobem. Další informace o [ladění VM snapshot problémy](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup). <br><br>Agent virtuálního počítače není způsobuje problémy, restartujte virtuální počítač. Nesprávný stav virtuálního počítače může způsobit problémy a restartování virtuálního počítače obnoví stav. |
| Virtuální počítač je ve stavu selhání zřizování: <br>Restartujte virtuální počítač a ujistěte se, že je virtuální počítač spuštěný nebo vypnout. | Tato chyba nastane, pokud mezi chybami rozšíření umístí virtuální počítač do stavu selhání zřizování. Přejděte do seznamu přípon, zkontrolujte, pokud je neúspěšné rozšíření, odeberte ji a zkuste restartovat virtuální počítač. Pokud všechna rozšíření, je v běžícím stavu, kontrola, zda je spuštěná Služba agenta virtuálního počítače. Pokud ne, restartujte službu agenta virtuálního počítače. |
| **VMSnapshot** rozšíření operace se nezdařila, za spravované disky: <br>Zkuste operaci zálohování zopakujte. Pokud problém přetrvává, postupujte podle pokynů v [Poradce při potížích s Azure Backup selhání](http://go.microsoft.com/fwlink/?LinkId=800034). Pokud to problém nevyřeší, obraťte se na oddělení podpory Microsoftu. | Tato chyba nastane, pokud služba Backup se nepodařilo aktivovat snímku. Další informace o [ladění VM snapshot problémy](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#ExtentionOperationFailed-vmsnapshot-extension-operation-failed). |
| Zálohování nelze zkopírovat snímek virtuálního počítače z důvodu nedostatku volného místa v účtu úložiště: <br>Ujistěte se, že účet úložiště obsahuje volné místo odpovídající dat na disky premium storage připojených k virtuálnímu počítači. | U virtuálních počítačů úrovně premium na zásobník záloh virtuálních počítačů verze 1 jsme zkopírování snímku do účtu úložiště. Tento krok zajistí, že provoz správy zálohování, který pracuje na snímek, neomezuje počet vstupně-výstupních operací aplikace díky diskům premium k dispozici. <br><br>Doporučujeme vám, že přidělíte pouze 50 procent, 17,5 TB místa celkový úložiště účtu. Potom můžete službu Azure Backup zkopírování snímku do úložiště účtu a přenos dat z tohoto umístění zkopírovaného v účtu úložiště do trezoru. |
| Zálohování nelze operaci provést, protože Agent virtuálního počítače není interaktivní. |K této chybě dochází, pokud dojde k nějakému problému s agentem VM, nebo blokovaný přístup k síti na infrastrukturu Azure nějakým způsobem. Pro virtuální počítače s Windows zkontrolujte stav služby agenta virtuálního počítače do služby a určuje, zda agent zobrazí v programy v Ovládacích panelech. <br><br>Zkuste odebrat program z ovládacích panelů a opětovná instalace agenta, jak je popsáno v [agenta virtuálního počítače](#vm-agent). Po přeinstalování agenta aktivujte ad hoc záloha ověřit. |
| Operace rozšíření recovery services se nezdařilo: <br>Ujistěte se, že je k dispozici na virtuálním počítači nejnovější Agent virtuálního počítače a je spuštěna Služba agenta virtuálního počítače. Zkuste operaci zálohování zopakujte. Pokud selže celá operace zálohování, obraťte se na Microsoft Support. |Tato chyba se stane, když se Agent virtuálního počítače je zastaralá. Odkazovat na [Poradce při potížích s Azure se zálohováním virtuálních počítačů](#updating-the-VM-agent) aktualizace agenta virtuálního počítače. |
| Virtuální počítač neexistuje: <br>Ujistěte se, že virtuální počítač existuje, nebo vyberte jiný virtuální počítač. |Tato chyba nastane, pokud primární virtuální počítač se odstraní, ale zásad zálohování vypadá stále pro virtuální počítač k zálohování. Chcete-li vyřešit tuto chybu, proveďte následující kroky: <ol><li> Znovu vytvořte virtuální počítač se stejným názvem a stejný název skupiny prostředků, **název cloudové služby**,<br>**nebo**<br></li><li>Zastavte ochranu virtuálního počítače bez odstranění zálohovaná data. Další informace najdete v tématu [zastavení ochrany virtuálních počítačů](https://go.microsoft.com/fwlink/?LinkId=808124).</li></ol> |
| Příkaz spuštění se nepovedlo: <br>U této položky právě probíhá jiná operace. Počkejte na dokončení předchozí operace. Opakujte operaci. |Spuštěná existující úloha zálohování a novou úlohu nelze spustit, dokud se nedokončí aktuální úlohu. |
| Kopírování virtuálních pevných disků z trezoru služby Recovery Services, vypršel časový limit: <br>Zkuste operaci zopakovat za několik minut. Pokud to problém nevyřeší, obraťte se na oddělení podpory Microsoftu. | K této chybě dochází, pokud dochází k přechodné chybě na straně úložiště nebo pokud službu Backup v účtu úložiště dostatek vstupně-výstupních operací pro přenos dat do trezoru, v časovém limitu. Ujistěte se, že chcete postupovat podle [osvědčené postupy při konfiguraci vašich virtuálních počítačů](backup-azure-vms-introduction.md#best-practices). Váš virtuální počítač přesunout do jiného účtu úložiště, který není načten a opakujte úlohu zálohování.|
| Zálohování selhalo s vnitřní chybou: <br>Zkuste operaci zopakovat za několik minut. Pokud to problém nevyřeší, obraťte se na oddělení podpory Microsoftu. |K této chybě získáte dvou důvodů: <ul><li> K používání úložiště virtuálního počítače je přechodný problém. Zkontrolujte [stav služby Azure site](https://azure.microsoft.com/status/) zobrazíte, pokud jsou výpočty, úložiště nebo síťové potíže v oblasti. Po vyřešení problému opakujte úlohu zálohování. <li> Původní virtuální počítač odstranil a bod obnovení nelze provést. Pokud chcete zachovat zálohovaná data pro odstraněného virtuálního počítače, ale odebrat chyby zálohování, zrušit ochranu virtuálního počítače a zvolte možnost zachovat data. Tato akce zastaví naplánované úlohy zálohování a opakované chybové zprávy. |
| Zálohování se nepovedlo nainstalovat rozšíření služeb zotavení Azure na vybrané položky: <br>Agent virtuálního počítače je předpokladem pro rozšíření služby Azure Recovery Services. Nainstalujte agenta virtuálního počítače Azure a restartujte operace Registrování. |<ol> <li>Zkontrolujte, zda je správně nainstalován Agent virtuálního počítače. <li>Ujistěte se, že je správně nastaven příznak na konfiguraci virtuálního počítače.</ol> Další informace o [instalace agenta virtuálního počítače](#validating-vm-agent-installation) a ověření instalace agenta virtuálního počítače. |
| Instalace rozšíření nebyla úspěšná kvůli chybě **modelu COM + se nepovedlo komunikovat s Microsoft Distributed Transaction Coordinator**. |Tato chyba obvykle znamená, že není spuštěná služba COM +. Požádejte o pomoc s opravou tohoto problému Microsoft Support. |
| Operace snímku nebyla úspěšná kvůli chybě služby Stínová kopie svazku (VSS) operace **tato jednotka je uzamčena nástrojem BitLocker Drive Encryption. Je nutné ji odemknout pomocí ovládacích panelů.** |Vypnout nástroj BitLocker pro všechny disky na virtuálním počítači a zkontrolujte, zda VSS problém vyřešen. |
| Virtuální počítač není ve stavu, který umožňuje zálohování. |<ul><li>Pokud je virtuální počítač v přechodném stavu mezi **systémem** a **vypnout**, počkejte chcete změnit stav. Potom aktivujte úlohu zálohování. <li> Pokud virtuální počítač je virtuální počítač s Linuxem a používá Linux Security-Enhanced modulu jádra, vyloučit cestu agenta Azure Linux **/var/lib/waagent** ze zásad zabezpečení a ujistěte se, že je nainstalované rozšíření zálohování.  |
| Virtuální počítač Azure nebyl nalezen. |Tato chyba nastane, pokud primární virtuální počítač se odstraní, ale zásady zálohování stále hledá odstraněného virtuálního počítače. Opravte tuto chybu následujícím způsobem: <ol><li>Znovu vytvořte virtuální počítač se stejným názvem a stejný název skupiny prostředků, **název cloudové služby**, <br>**nebo** <li> Vypněte ochranu pro tento virtuální počítač tak, aby úlohy zálohování se nevytvoří. </ol> |
| Agent virtuálního počítače není k dispozici na virtuálním počítači: <br>Nainstalujte agenta virtuálního počítače a všechny požadavky. Restartujte operaci. |Další informace o [instalace agenta virtuálního počítače a ověření instalace agenta virtuálního počítače](#vm-agent). |
| Operace snímku nebyla úspěšná, protože zapisovače VSS jsou v chybném stavu. |Restartujte zapisovače služby VSS, které jsou v chybném stavu. Z příkazového řádku se zvýšenými oprávněními spusťte ```vssadmin list writers```. Výstup obsahuje všechny zapisovače VSS a jejich stav. Pro všechny zapisovače služby VSS se stavem, který není **[1] stabilní**, abyste mohli restartovat zapisovače služby VSS, spusťte následující příkazy z příkazového řádku se zvýšenými oprávněními: <ol><li>```net stop serviceName``` <li> ```net start serviceName```</ol>|
| Operace snímku nebyla úspěšná kvůli chybě parsování konfigurace. |K této chybě dochází z důvodu změny oprávnění na **MachineKeys** adresáře: **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**. <br> Spusťte následující příkaz a zkontrolujte oprávnění na **MachineKeys** adresáře jsou výchozí hodnoty:<br>**icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**. <br><br>Výchozí oprávnění jsou následující: <ul><li>Všem uživatelům: (R,W) <li>BUILTIN\Administrators: (F)</ul> Pokud se zobrazí oprávnění ve službě **MachineKeys** adresář, který se liší od výchozí hodnoty, použijte následující postup správnými oprávněními, odstraňte certifikát a aktivovat zálohování: <ol><li>Oprava oprávnění na **MachineKeys** adresáře. Pomocí Průzkumníka vlastnosti zabezpečení a pokročilá nastavení zabezpečení v adresáři resetování oprávnění zpět na výchozí hodnoty. Odebrat všechny objekty uživatelů s výjimkou výchozích hodnot z adresáře a ujistěte se, že **Everyone** oprávnění má zvláštní přístup k následujícím způsobem: <ul><li>Seznam složky/Číst data <li>Číst atributy <li>Číst rozšířené atributy <li>Vytvářet soubory/Zapisovat data <li>Vytvářet složky/Připojovat data<li>Zapisovat atributy<li>Zapisovat rozšířené atributy<li>Oprávnění ke čtení </ul><li>Odstranit všechny certifikáty které **vystaveno pro** je model nasazení classic nebo **generátor certifikátů CRP Windows Azure**:<ol><li>[Otevřete certifikátů na místním počítači konzolu](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx).<li>V části **osobní** > **certifikáty**, odstraňte všechny certifikáty kde **vystaveno pro** je model nasazení classic nebo **Windows Azure CRP Generátor certifikátů**.</ol> <li>Aktivace úlohy zálohování virtuálního počítače. </ol>|
| Služba Azure Backup nemá dostatečná oprávnění pro Azure Key Vault pro zálohování šifrovaných virtuálních počítačů. |Služba Backup tato oprávnění poskytli v Powershellu pomocí kroků v [vytvořit virtuální počítač z obnovených disků](backup-azure-vms-automation.md). |
|Instalace rozšíření snímku selhalo s chybou **modelu COM + se nepovedlo komunikovat s Microsoft Distributed Transaction Coordinator**. | Z příkazového řádku se zvýšenými oprávněními spusťte službu Windows **systémová aplikace modelu COM +**. Příkladem je **net start COMSysApp**. Pokud se službu nepodaří spustit, proveďte následující kroky:<ol><li> Ujistěte se, že účet přihlášení služby **Distributed Transaction Coordinator** je **síťová služba**. Pokud není, změnit přihlašovací účet, který chcete **síťová služba** a restartujte službu. Potom se pokuste spustit **systémová aplikace modelu COM +**.<li>Pokud **systémová aplikace modelu COM +** nebude spustit, proveďte následující kroky odinstalovat a nainstalovat službu **Distributed Transaction Coordinator**: <ol><li>Zastavte službu MSDTC. <li>Otevřete příkazový řádek, **cmd**. <li>Spusťte příkaz ```msdtc -uninstall```. <li>Spusťte příkaz ```msdtc -install```. <li>Spuštění služby MSDTC. </ol> <li>Spustit službu Windows **systémová aplikace modelu COM +**. Po **systémová aplikace modelu COM +** zahájení aktivace úlohy zálohování z webu Azure portal.</ol> |
|  Operace snímku nebyla úspěšná kvůli chybě modelu COM +. | Doporučujeme, abyste službu Windows **systémová aplikace modelu COM +** z příkazového řádku se zvýšenými oprávněními **net start COMSysApp**. Pokud se problém nevyřeší, restartujte virtuální počítač. Pokud vám nepomohly restartování virtuálního počítače, zkuste [odebrání rozšíření VMSnapshot](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout) a aktivovat zálohování ručně. |
| Zálohování se nepovedlo zablokovat přípojné body virtuálního počítače na pořízení konzistentního snímku souboru systému a. | Proveďte následující krok: <ul><li>Zkontrolujte stav systému souborů všech připojených zařízení s použitím **"tune2fs"** příkazu. Příkladem je **tune2fs -l/dev/sdb1 \** .| GREP **stavu systému souborů**. <li>Odpojte zařízení, u kterých stav systému souborů není čistý pomocí **"umount"** příkazu. <li> Spusťte kontrolu konzistence systému souborů v těchto zařízeních pomocí **"fsck"** příkazu. <li> Znovu připojte zařízení a opakujte zálohování.</ol> |
| Operace snímku nebyla úspěšná kvůli chybě vytvořit zabezpečený komunikační kanál. | <ol><li> Otevřete Editor registru spuštěním **regedit.exe** v režimu se zvýšenými oprávněními. <li> Identifikujte všechny verze rozhraní .NET Framework, které jsou k dispozici ve vašem systému. Jsou k dispozici v nabídce hierarchie klíč registru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft**. <li> Pro každé rozhraní .NET Framework v klíči registru přidejte následující klíč: <br> **SchUseStrongCrypto"=dword:00000001**. </ol>|
| Operace snímku nebyla úspěšná kvůli chybě v instalaci distribuovatelné součásti Visual C++ pro Visual Studio 2012. | Přejděte na C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion a nainstalujte vcredist2012_x64. Ujistěte se, že hodnotu klíče registru, který umožňuje tato instalace služby je nastavena na správnou hodnotu. To znamená, hodnota klíče registru **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** je nastavena na **3** a ne **4**. <br><br>Pokud stále máte problémy s instalací, restartujte službu instalace spuštěním **MSIEXEC/UNREGISTER** následovaný **MSIEXEC /REGISTER** z příkazového řádku se zvýšenými oprávněními.  |


## <a name="jobs"></a>Úlohy
| Podrobnosti o chybě | Alternativní řešení |
| --- | --- |
| Zrušení se nepodporuje pro tento typ úlohy: <br>Počkejte, až úloha dokončí. |Žádný |
| Úloha není zrušitelný stavu: <br>Počkejte, až úloha dokončí. <br>**nebo**<br> Vybrané úlohy není možné zrušit stavu: <br>Počkejte na dokončení úlohy. |Je pravděpodobné, že úloha je téměř u konce. Počkejte, dokud je úloha dokončena.|
| Zálohování tuto úlohu nelze zrušit, protože se nenachází v průběhu: <br>Zrušení se podporuje jenom pro probíhající úlohy. Zkuste zrušit probíhající úlohy. |K této chybě dochází z důvodu přechodné stavu. Počkejte chvíli a zkuste operaci zrušit. |
| Nepovedlo se zrušit úlohu zálohování: <br>Počkejte, až úloha dokončí. |Žádný |

## <a name="restore"></a>Obnovení

| Podrobnosti o chybě | Alternativní řešení |
| --- | --- |
| Obnovení se nezdařilo s vnitřní chybou cloudu. |<ol><li>Cloudové služby, ke kterému se snažíte obnovit je nakonfigurován s nastavením DNS. Můžete zkontrolovat: <br>**$deployment = get-AzureDeployment - ServiceName "ServiceName"-slotu "Produkční" Get AzureDns - části Networkinterfaceconfigurations $deployment. Části Networkinterfaceconfigurations**.<br>Pokud **adresu** je nakonfigurován, pak jsou nakonfigurovaná nastavení DNS.<br> <li>Cloudová služba, do kterého se snažíte obnovit, má nakonfigurovanou **vyhrazená IP adresa**, a stávající virtuální počítače v rámci cloudové služby jsou ve stavu Zastaveno. Můžete zkontrolovat, že Cloudová služba má vyhrazené IP adresy pomocí následující rutiny prostředí PowerShell: **$deployment = Get-AzureDeployment - ServiceName "servicename"-slotu "Produkční" $ programu dep. ReservedIPName**. <br><li>Pokoušíte se při obnovení virtuálního počítače se následující speciální konfigurací sítě do stejné cloudové služby: <ul><li>Virtuální počítače v rámci konfigurace služby Vyrovnávání zatížení, interní a externí.<li>Virtuální počítače s víc vyhrazených IP adres. <li>Virtuální počítače s několika síťovými kartami. </ul><li>Vyberte novou cloudovou službu v uživatelském rozhraní nebo viz [obnovení aspekty](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) pro virtuální počítače se speciální konfigurací sítě.</ol> |
| Vybraný název DNS se už používá: <br>Zadejte jiný název DNS a zkuste to znovu. |Tento název DNS, odkazuje na název cloudové služby, obvykle končí **. cloudapp.net**. Tento název musí být jedinečný. Pokud se zobrazí tato chyba, musíte zvolit jiný název virtuálního počítače během obnovení. <br><br> Tato chyba se zobrazí pouze pro uživatele na webu Azure portal. Operaci obnovení prostřednictvím prostředí PowerShell bude úspěšné, protože jej obnoví pouze disky a není vytvoření virtuálního počítače. Chyba bude čelí, když virtuální počítač je explicitně vytvořené po operaci obnovení na disku. |
| Konfigurace zadané virtuální sítě není správná: <br>Zadejte konfiguraci jinou virtuální síť a zkuste to znovu. |Žádný |
| Zadaná Cloudová služba používá vyhrazenou IP adresu, která se pravděpodobně neshoduje s konfigurací obnoveného virtuálního počítače: <br>Určete jinou cloudovou službu, která nepoužívá rezervovanou IP adresu. Nebo vyberte jiný bod obnovení pro obnovení z. |Žádný |
| Cloudová služba dosáhla svého limitu počtu vstupních koncových bodů: <br>Zkuste operaci zopakovat tak, že zadáte jinou cloudovou službu nebo s použitím existujícího koncového bodu. |Žádný |
| Účet služby Recovery Services trezor a cílové úložiště jsou ve dvou různých oblastech: <br>Ujistěte se, že účet úložiště zadaný u operace obnovení je ve stejné oblasti Azure jako trezor služby Recovery Services. |Žádný |
| Účet úložiště zadaný pro operaci obnovení se nepodporuje: <br>Jsou podporovány pouze Basic nebo Standard účtů úložiště s nastavením místně redundantní nebo geograficky redundantní replikace. Vyberte účet úložiště podporuje. |Žádný |
| Typ účtu úložiště zadaný pro operaci obnovení není online: <br>Ujistěte se, že účet úložiště zadaný u operace obnovení je online. |K této chybě může dojít z důvodu o přechodnou chybu ve službě Azure Storage nebo kvůli výpadku. Zvolte jiný účet úložiště. |
| Byla dosažena kvóta skupin prostředků: <br>Odstraňte některé skupiny prostředků na portálu Azure nebo se obraťte na podporu Azure o navýšení limitů. |Žádný |
| Vybraná podsíť neexistuje: <br>Vyberte podsíť, která existuje. |Žádný |
| Služba zálohování nemá autorizaci pro přístup k prostředkům ve vašem předplatném. |Chcete-li vyřešit tuto chybu, nejdříve obnovit disky pomocí kroků v [obnovení disků zálohovaných](backup-azure-arm-restore-vms.md#create-new-restore-disks). Pak použijte rutinu prostředí PowerShell kroky v [vytvořit virtuální počítač z obnovených disků](backup-azure-vms-automation.md#restore-an-azure-vm). |

## <a name="backup-or-restore-takes-time"></a>Zálohování nebo obnovení trvá určitou dobu
Pokud zálohování trvá déle než 12 hodin nebo obnovení trvá déle než 6 hodin:
* Vysvětlení [faktorů, které přispívají k čas zálohování](backup-azure-vms-introduction.md#time-considerations) a [faktorů, které přispívají k čas obnovení](backup-azure-vms-introduction.md#restore-considerations).
* Ujistěte se, že se řídíte [doporučené zálohovat](backup-azure-vms-introduction.md#best-practices).

## <a name="vm-agent"></a>Agent virtuálního počítače
### <a name="set-up-the-vm-agent"></a>Nastavení agenta virtuálního počítače
Obvykle je Agent virtuálního počítače už ve virtuálních počítačích vytvořených z Galerie Azure. Ale virtuální počítače, které se migrují z místních datových center nebudou mít nainstalovaného agenta virtuálního počítače. U těchto počítačů musí být explicitně nainstalovaný Agent virtuálního počítače.

#### <a name="windows-vms"></a>Virtuální počítače s Windows

* Stáhněte si a nainstalujte [MSI agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Je potřeba oprávnění správce pro dokončení instalace.
* Pro virtuální počítače vytvořené pomocí modelu nasazení classic [aktualizovat vlastnosti virtuálního počítače](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) označující, zda je agent nainstalovaný. Tento krok není povinný pro virtuální počítače Azure Resource Manageru.

#### <a name="linux-vms"></a>Virtuální počítače s Linuxem

* Nainstalujte nejnovější verzi agenta z úložiště distribuce. Podrobnosti o název balíčku, najdete v článku [agenta pro Linux úložiště](https://github.com/Azure/WALinuxAgent).
* Pro virtuální počítače vytvořené pomocí modelu nasazení classic [použít tento blog](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) aktualizovat vlastnosti virtuálního počítače a ověřte, zda je agent nainstalovaný. Tento krok není povinný pro virtuální počítače Resource Manageru.

### <a name="update-the-vm-agent"></a>Aktualizace agenta virtuálního počítače
#### <a name="windows-vms"></a>Virtuální počítače s Windows

* Pokud chcete aktualizovat agenta virtuálního počítače, přeinstalujte [binárních souborů agenta virtuálního počítače](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Před aktualizací agenta, ujistěte se, že žádná operace zálohování, k nimž došlo při aktualizaci agenta virtuálního počítače.

#### <a name="linux-vms"></a>Virtuální počítače s Linuxem

* Chcete-li aktualizovat agenta virtuálního počítače s Linuxem, postupujte podle pokynů v článku [aktualizace agenta virtuálního počítače s Linuxem](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

    > [!NOTE]
    > Vždy používejte úložiště distribuce aktualizace agenta.

    Nestahovat agenta kódu z Githubu. Pokud není k dispozici pro vaši distribuci nejnovější verzi agenta, obraťte se na podporu distribuční pokyny k získání nejnovějšího agenta. Můžete také zkontrolovat nejnovější [Windows Azure Linux agent](https://github.com/Azure/WALinuxAgent/releases) informace v úložišti GitHub.

### <a name="validate-vm-agent-installation"></a>Ověření instalace agenta virtuálního počítače

Ověřte, že verze agenta virtuálního počítače na virtuálních počítačích s Windows:

1. Přihlaste se k virtuálnímu počítači Azure a přejděte do složky **C:\WindowsAzure\Packages**. Měli byste najít **WaAppAgent.exe** souboru.
2. Klikněte pravým tlačítkem na soubor a přejděte na **vlastnosti**. Vyberte **podrobnosti** kartu. **Verze produktu** pole by mělo být 2.6.1198.718 nebo vyšší.

## <a name="troubleshoot-vm-snapshot-issues"></a>Řešení potíží s snímku virtuálního počítače
Zálohování virtuálních počítačů se spoléhá na vydávání příkazů snímek základního úložiště. Nemáte přístup k úložišti nebo zpoždění v úloze snímků spuštění může způsobit selhání úlohy zálohování. Selhání úlohy snímku mohou způsobovat následující podmínky:

- **Pomocí skupiny zabezpečení sítě je blokován síťového přístupu k úložišti**. Další informace o tom, jak [navázat přístup k síti](backup-azure-arm-vms-prepare.md#establish-network-connectivity) do služby Storage s použitím buď na seznam povolených IP adres nebo prostřednictvím proxy serveru.
- **Virtuální počítače pomocí zálohování serveru SQL Server nakonfigurovaný, může způsobit zpoždění úlohy vytvoření snímku**. Ve výchozím nastavení zálohování virtuálního počítače vytvoří VSS úplného zálohování na virtuálních počítačích s Windows. Virtuální počítače, na kterých běží SQL Server pomocí zálohování serveru SQL Server nakonfigurovaný, může docházet k snímku zpoždění. Pokud snímek zpoždění způsobit selhání zálohování, nastavte následující klíč registru:

   ```
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

- **Stav virtuálního počítače, je uvedena nesprávně, protože virtuální počítač je vypnutý v protokolu RDP**. Pokud jste použili vzdálené plochy k vypnutí virtuálního počítače ověřte, zda je správný stav virtuálního počítače na portálu. Pokud stav není správný, použijte **vypnutí** možnost v řídicím panelu portálu virtuální počítač vypnout virtuální počítač.
- **Pokud více než čtyři virtuální počítače sdílet stejné cloudové službě, rozdělené mezi více zásad zálohování virtuálních počítačů**. Rozložte zálohování dobu, proto není, že spustit více než čtyři záloh virtuálních počítačů ve stejnou dobu. Došlo k pokusu o oddělení časy zahájení v zásadách o aspoň hodinu.
- **Virtuální počítač běží na vysoké využití procesoru nebo paměti**. Pokud virtuální počítač běží na vysoký poměr paměti nebo využití procesoru, víc než 90 procent úlohu snímku je zařazena do fronty a zpoždění. Nakonec vyprší časový limit. Pokud tento problém nastane, zkuste zálohu na vyžádání.

## <a name="networking"></a>Sítě
Stejně jako všechna rozšíření rozšíření zálohování potřebovat přístup k veřejnému Internetu fungovat. Nemáte přístup k veřejnému Internetu může projevit různými způsoby:

* Instalace rozšíření může selhat.
* Operace zálohování jako snímku disku může selhat.
* Zobrazení stavu operace zálohování může selhat.

Je potřeba vyřešit veřejné internetové adresy podrobněji [tento blog podpory Azure](https://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Zkontrolujte konfiguraci DNS pro virtuální síť a ujistěte se, že identifikátory URI Azure je vyřešit.

Po provádí překlad, přístup k IP adresy Azure také musí být k dispozici. Pokud chcete odblokovat přístup k infrastruktuře Azure, použijte jeden z těchto kroků:

- Seznam povolených adres Azure datacenter rozsahy IP adres:
   1. Získat seznam [Azure datacenter IP adresy](https://www.microsoft.com/download/details.aspx?id=41653) uvedení na seznamu povolených.
   1. Odblokování pomocí IP adresy [New-NetRoute](https://docs.microsoft.com/powershell/module/nettcpip/new-netroute) rutiny. V okně PowerShell se zvýšenými oprávněními spusťte tuto rutinu v rámci virtuálního počítače Azure. Spusťte jako správce.
   1. Přidání pravidel k NSG, pokud nemáte v místě, pokud chcete povolit přístup k IP adres.
- Vytvoření cesty pro příjem provozu HTTP:
   1. Pokud máte některá omezení sítě, nasaďte proxy server HTTP pro směrování provozu. Příkladem je skupina zabezpečení sítě. Postup nasazení proxy server HTTP v [navázat připojení k síti](backup-azure-arm-vms-prepare.md#establish-network-connectivity).
   1. Přidání pravidel k NSG, pokud nemáte v místě, chcete-li povolit přístup k Internetu z proxy serveru HTTP.

> [!NOTE]
> DHCP musí být povolené uvnitř hosta pro zálohování virtuálních počítačů IaaS pro práci. Pokud potřebujete statickou privátní IP adresu, můžete ji nakonfigurujte prostřednictvím webu Azure portal nebo Powershellu. Ujistěte se, že je povolená možnost DHCP ve virtuálním počítači.
> Získejte další informace o tom, jak nastavit statickou IP adresu prostřednictvím Powershellu:
> - [Jak přidat do existujícího virtuálního počítače statické interní IP adresa](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)
> - [Změníte metodu přidělování privátní IP adresy přiřazené k síťovému rozhraní](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)
>
>
