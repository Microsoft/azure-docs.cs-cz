---
title: Zálohování řešení potíží s virtuálním počítači Azure
description: Řešení zálohování a obnovení virtuálních počítačů Azure
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 01/21/2018
ms.author: trinadhk
ms.openlocfilehash: a5828b4e4f42c349246845bd003e874fb0352bae
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008072"
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Odstraňování potíží se zálohováním virtuálních počítačů Azure
Řešení potíží s chybami při používání Azure Backup s informacemi o uvedené v následující tabulce došlo k chybě.

| Detaily chyby | Alternativní řešení |
| --- | --- |
| Operace se nedá provést, protože virtuální počítač už neexistuje. -Zastaví ochranu virtuálního počítače bez odstranění dat záloh. Další podrobnosti najdete v http://go.microsoft.com/fwlink/?LinkId=808124 |To se stane, když primární virtuální počítač se odstraní, ale zásady zálohování pokračuje v hledání pro virtuální počítač k zálohování. Chcete-li vyřešit tuto chybu: <ol><li> Znovu vytvořte virtuální počítač se stejným názvem a stejný název skupiny prostředků [název cloudové služby],<br>(NEBO)</li><li> Zastavte ochranu virtuálního počítače s nebo bez něj odstraňují se záložní data. [Další podrobnosti](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| Operace snímku nebyla úspěšná kvůli bez připojení k síti na virtuální počítač – Ujistěte se, že virtuální počítač má přístup k síti. Pro snímek úspěšný buď seznamu povolených IP adres Azure datacenter IP rozsahů adres nebo nastavení proxy serveru pro přístup k síti. Další podrobnosti najdete v http://go.microsoft.com/fwlink/?LinkId=800034. Pokud už používáte proxy server, ujistěte se, že jsou správně nakonfigurované nastavení proxy serveru | Tato chyba se vyvolá při zamítnutí odchozí připojení k Internetu na virtuálním počítači. Připojení k Internetu je vyžadován pro rozšíření snímku virtuálního počítače k vytvoření snímku discích virtuálního počítače. [Další informace](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine) k vyřešení selhání snímku kvůli zablokování síťový přístup. |
| Agent virtuálního počítače není schopen komunikovat se službou Azure Backup. – Ujistěte se virtuální počítač má síťové připojení a agent virtuálního počítače je nejnovější a spuštěna. Další informace najdete na  http://go.microsoft.com/fwlink/?LinkId=800034 |K této chybě dojde, pokud dojde k nějakému problému s agentem VM, nebo blokovaný přístup k síti na infrastrukturu Azure nějakým způsobem. [Další informace](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#vm-agent-unable-to-communicate-with-azure-backup) o ladění můžete virtuální počítač snímek problémy.<br> Pokud je agent virtuálního počítače není způsobuje problémy, restartujte virtuální počítač. V některých případech nesprávné stav virtuálního počítače může způsobit problémy a restartování virtuálního počítače obnoví tento "chybný stav". |
| Virtuální počítač je ve stavu nezdařeného zřizování – Restartujte prosím virtuální počítač a ujistěte se, že je virtuální počítač ve spuštěném nebo vypnutém stavu pro zálohování | K tomu dojde, když jeden chybami rozšíření vede stav virtuálního počítače ve stavu selhání zřizování. Přejít na seznam přípon a zjistit, jestli se nezdařilo rozšíření, odeberte ji a zkuste restartovat virtuální počítač. Pokud všechna rozšíření, je v běžícím stavu, kontrola, zda je spuštěná Služba agenta virtuálního počítače. Pokud ne, restartujte službu agenta virtuálního počítače. | 
| Operace rozšíření VMSnapshot selhala pro managed disks – zkuste to prosím znovu operace zálohování. Pokud se problém opakuje, postupujte podle pokynů na adrese 'http://go.microsoft.com/fwlink/?LinkId=800034". Pokud selže, kontaktujte prosím podporu Microsoftu | Tato chyba, když dojde k aktivaci snímku selhání služby Azure Backup. [Další informace](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#vmsnapshot-extension-operation-failed) o ladění VM snapshot problémy. |
| Může nelze zkopírovat snímek virtuálního počítače, protože je dostatek volného místa v účtu úložiště – Ujistěte se, že účet úložiště obsahuje volné místo odpovídající množství dat na disky premium storage připojených k virtuálnímu počítači | V případě virtuálních počítačů úrovně premium na zásobník záloh virtuálních počítačů V1 jsme zkopírování snímku do účtu úložiště. Toto je zajistit, že provoz správy zálohování, který pracuje na snímek, neomezuje počet vstupně-výstupních operací aplikace díky diskům premium k dispozici. Společnost Microsoft doporučuje že přidělit jenom 50 % (17,5 TB) prostoru účet celkový úložiště tak, že služba Azure Backup můžete zkopírování snímku do úložiště účtu a přenos dat z tohoto umístění zkopírovaného v účtu úložiště do trezoru. | 
| Nelze operaci provést, protože agent virtuálního počítače nereaguje |K této chybě dojde, pokud dojde k nějakému problému s agentem VM, nebo blokovaný přístup k síti na infrastrukturu Azure nějakým způsobem. Pro virtuální počítače s Windows zkontrolujte stav služby agenta virtuálního počítače do služby a určuje, zda agent zobrazí v programy v Ovládacích panelech. Zkuste odebrat program z ovládacího panelu a opětovné instalaci agenta, jak je uvedeno [níže](#vm-agent). Po opětovné instalaci agenta, aktivujte zálohování ad hoc k ověření. |
| Operace rozšíření služby Recovery services se nezdařilo. – Ujistěte se prosím, že je k dispozici na virtuálním počítači nejnovější agent virtuálního počítače a je spuštěna Služba agenta. Zkuste operaci zálohování znovu a pokud selže, kontaktujte podporu Microsoftu. |Tato chyba se vyvolá, když se agent virtuálního počítače je zastaralá. Přečtěte si níže aktualizace agenta virtuálního počítače v části "Aktualizace the Agent virtuálního počítače". |
| Virtuální počítač neexistuje. -Prosím ujistěte se, že tento virtuální počítač existuje, nebo vyberte jiný virtuální počítač. |To se stane, když primární virtuální počítač se odstraní, ale stále zásady zálohování k vyhledání virtuálních počítačů provést zálohování. Chcete-li vyřešit tuto chybu: <ol><li> Znovu vytvořte virtuální počítač se stejným názvem a stejný název skupiny prostředků [název cloudové služby],<br>(NEBO)<br></li><li>Zastavte ochranu virtuálního počítače bez odstranění zálohovaná data. [Další podrobnosti](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| Příkaz se nepodařilo spustit. -U této položky právě probíhá jiná operace. Počkejte na dokončení předchozí operace a pak zkuste |Existující zálohy na virtuálním počítači se systémem a novou úlohu nelze spustit, když existující úloha běží. |
| Kopírování virtuálních pevných disků ze záloh vypršení časového limitu – Zkuste prosím tuto operaci za několik minut. Pokud to problém nevyřeší, obraťte se na oddělení podpory Microsoftu. | To se stane, pokud dochází k přechodné chybě na straně úložiště nebo pokud se služba backup nedostává dostatečná vstupně-výstupních operací z účtu úložiště hostujícího virtuální počítač, aby bylo možné přenést data v rámci časového limitu do trezoru. Ujistěte se, že jste udělali [osvědčené postupy](backup-azure-vms-introduction.md#best-practices) při nastavování zálohování. Zkuste přesunout virtuální počítač do jiného úložiště účtu které není načteno a opakujte zálohování.|
| Zálohování selhalo s interní chybou – Zkuste prosím tuto operaci za několik minut. Pokud se problém nevyřeší, obraťte se na Microsoft Support |Této chybě může dojít z důvodu 2: <ol><li> K používání úložiště virtuálního počítače je přechodný problém. Zkontrolujte prosím [stav Azure](https://azure.microsoft.com/status/) jestli jsou všechny probíhající problém související s compute, úložiště nebo sítě v oblasti. Po vyřešení problému opakujte úlohu zálohování. <li>Původní virtuální počítač odstranil, a proto nelze provést bod obnovení. Chcete zachovat zálohovaná data pro odstraněného virtuálního počítače, ale odebrat chyby zálohování: zrušit ochranu virtuálního počítače a vyberte možnost zachovat data. Tato akce zastaví naplánované úlohy zálohování a opakované chybové zprávy. |
| Nepovedlo se nainstalovat rozšíření služby Azure Recovery Services na vybranou položku - agent virtuálního počítače je předpokladem pro rozšíření Azure Recovery Services. Nainstalujte agenta virtuálního počítače Azure a restartujte operaci zápisu |<ol> <li>Zkontrolujte, zda správně nainstalován agent virtuálního počítače. <li>Zkontrolujte, jestli že je správně nastaven příznak na konfiguraci virtuálního počítače.</ol> [Přečtěte si další](#validating-vm-agent-installation) o instalaci agenta virtuálního počítače a ověření instalace agenta virtuálního počítače. |
| Instalace rozšíření selhala s chybou "modelu COM + se nepovedlo komunikovat s Microsoft Distributed Transaction Coordinator |To obvykle znamená, zda není spuštěna služba COM +. Kontaktujte podporu Microsoftu o pomoc na tento problém vyřešíme. |
| Operace snímku nebyla úspěšná kvůli chybě operace VSS "Tato jednotka je uzamčena nástrojem BitLocker Drive Encryption. Je nutné ji odemknout pomocí ovládacích panelů. |Vypnout nástroj BitLocker pro všechny disky na virtuálním počítači a podívejte se, pokud je vyřešen problém stínové kopie svazku |
| Virtuální počítač není ve stavu, který umožňuje zálohování. |<ul><li>Zkontrolujte, jestli virtuální počítač je v přechodovém stavu mezi spuštění a vypnutí dolů. Pokud se jedná, počkejte stav virtuálního počítače být jedna z nich a aktivovat zálohování provést znovu. <li> Pokud virtuální počítač je virtuální počítač s Linuxem a používá [zabezpečení rozšířené Linux] modulu jádra, je třeba vyloučit cestu agenta pro Linux (_/var/lib/waagent_) z zabezpečení zásad Ujistěte se, že záložní linka se nainstaluje.  |
| Virtuální počítač Azure nebyl nalezen. |To se stane, když primární virtuální počítač se odstraní, ale stále zásady zálohování k vyhledání virtuálních počítačů k provedení zálohování. Chcete-li vyřešit tuto chybu: <ol><li>Znovu vytvořte virtuální počítač se stejným názvem a stejný název skupiny prostředků [název cloudové služby], <br>(NEBO) <li> Vypněte ochranu pro tento virtuální počítač tak, aby úlohy zálohování se nevytvoří. </ol> |
| Agent virtuálního počítače není k dispozici na virtuálním počítači – nainstalujte agenta virtuálního počítače a všechny požadavky a restartujte operaci. |[Přečtěte si další](#vm-agent) o instalaci agenta virtuálního počítače a ověření instalace agenta virtuálního počítače. |
| Operace snímku nebyla úspěšná, protože zapisovače VSS jsou ve špatném stavu |Budete muset restartovat zapisovače VSS (službu Stínová kopie svazku), které jsou ve špatném stavu. Chcete-li toho dosáhnout, z příkazového řádku se zvýšenými oprávněními, spusťte _vssadmin list writers_. Výstup obsahuje všechny zapisovače VSS a jejich stav. Pro všechny zapisovače služby VSS, jejichž stav není "[1] stabilní verze" restartujte zapisovač VSS spuštěním následujících příkazů z příkazového řádku se zvýšenými oprávněními:<br> _serviceName net stop_ <br> _příkaz net start serviceName_|
| Operace snímku nebyla úspěšná kvůli chybě parsování konfigurace |To se stane, že z důvodu změny oprávnění k adresáři MachineKeys: _%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_ <br>Spusťte následující příkaz a ověřte, zda jsou oprávnění pro adresář MachineKeys výchozí značky:<br>_icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_ <br><br> Výchozí oprávnění jsou:<br>Everyone:(R,W) <br>BUILTIN\Administrators:(F)<br><br>Pokud se zobrazí oprávnění v adresáři MachineKeys jiné než výchozí, postupujte podle následujících kroků správnými oprávněními, odstraňte certifikát a aktivovat zálohování.<ol><li>Oprava oprávnění MachineKeys adresáře.<br>Pomocí vlastnosti zabezpečení Explorer a Upřesnit nastavení zabezpečení v adresáři a obnovit výchozí hodnoty oprávnění, odebrat libovolný objekt navíc (než výchozí) uživatele z adresáře a ujistěte se, že "Everyone" měl oprávnění pro zvláštní přístup:<br>-Zobrazovat obsah složky / Číst data <br>– Čtení atributy <br>-Číst rozšířené atributy <br>-Vytvářet soubory / Zapisovat data <br>-Vytvářet složky / Připojovat data<br>-Zapisovat atributy<br>-Zapisovat rozšířené atributy<br>-Oprávnění ke čtení<br><br><li>Odstranit všechny certifikáty s polem "Vystaveno pro" = "Windows Azure Service Management pro rozšíření" nebo "Windows Azure CRP certifikát generátor".<ul><li>[Používat konzolu Certifikáty (místní počítač)](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx)<li>Odstranit všechny certifikáty (v části osobní -> certifikáty) s polem "Vystaveno pro" = "Windows Azure Service Management pro rozšíření" nebo "Windows Azure CRP certifikát generátor".</ul><li>Spouští se zálohování virtuálního počítače. </ol>|
| Služba Azure Backup nemá dostatečná oprávnění pro Key Vault pro zálohování šifrovaných virtuálních počítačů. |Služby zálohování by měl být k dispozici tato oprávnění v prostředí PowerShell pomocí kroků popsaných v **povolit zálohování** část [dokumentaci k Powershellu](backup-azure-vms-automation.md). |
|Snímek instalaci rozšíření se nepovedla s chybou - modelu COM + se nepovedlo komunikovat s Microsoft Distributed Transaction Coordinator | Spusťte službu systému windows "aplikace modelu COM + System" (z příkazového řádku se zvýšenými oprávněními - _net start COMSysApp_). <br>Pokud selže při spouštění, postupujte podle následujících kroků:<ol><li> Ověřte, že účet přihlášení služby "Distributed Transaction Coordinator" je "Síťové služby". Pokud není, změňte ji na "Síťovou službu", restartujte tuto službu a zkuste spustit službu "aplikace modelu COM + systému". "<li>Pokud stále nepodaří spustit, odinstalace/instalace služby "Distributed Transaction Coordinator" podle následujících kroků:<br> -Zastavit službu MSDTC<br> – Otevřete příkazový řádek (cmd) <br> – Spusťte příkaz "msdtc-odinstalace" <br> – Spusťte příkaz "msdtc – instalace" <br> -Spuštění služby MSDTC<li>Spusťte službu systému windows "aplikace modelu COM + systému" a po spuštění, spustit zálohování z portálu.</ol> |
|  Operace snímku nebyla úspěšná kvůli chybě modelu COM +. | Doporučená akce je restart služby systému windows "aplikace modelu COM + systému" (z příkazového řádku se zvýšenými oprávněními - _net start COMSysApp_). Pokud se problém nevyřeší, restartujte virtuální počítač. Pokud vám nepomohly restartování virtuálního počítače, zkuste [odebrání rozšíření VMSnapshot](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#cause-3-the-backup-extension-fails-to-update-or-load) a aktivovat zálohování ručně. |
| Nepovedlo se zablokovat nejméně jeden přípojný bod virtuálního počítače na pořízení konzistentního snímku systému souborů | Použijte k tomu následující postup: <ol><li>Zkontrolujte stav systému souborů všech připojených zařízení používat _"tune2fs"_ příkazu.<br> Například: tune2fs -l/dev/sdb1 \| grep "Stavu systému souborů" <li>Odpojte zařízení, pro které systému souborů stavu není pomocí čisté _"umount"_ příkaz <li> Spusťte kontrolu FileSystemConsistency na těchto zařízeních pomocí _"fsck"_ příkaz <li> Znovu připojte zařízení a opakujte zálohování.</ol> |
| Operace snímku nebyla úspěšná kvůli chybě při vytváření zabezpečený komunikační kanál | <ol><Li> Otevřete Editor registru spuštěním regedit.exe v režimu se zvýšenými oprávněními. <li> Identifikujte všechny verze. NetFramework k dispozici v systému. Jsou k dispozici v nabídce hierarchie klíč registru "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft" <li> Pro každý. K dispozici v klíči registru NetFramework přidat následující klíč: <br> "SchUseStrongCrypto"=dword:00000001 </ol>|
| Operace snímku nebyla úspěšná kvůli chybě v instalaci distribuovatelné součásti Visual C++ pro Visual Studio 2012 | Přejděte na C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion a nainstalujte vcredist2012_x64. Ujistěte se, že hodnota klíče registru pro umožnění instalace této služby je nastavena na správnou hodnotu tedy hodnota klíče registru _HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver_ nastavená na 3 a 4 není. Pokud stále máte problémy s instalací, restartujte službu instalace spuštěním _MSIEXEC/UNREGISTER_ následovaný _MSIEXEC /REGISTER_ z příkazového řádku se zvýšenými oprávněními.  |


## <a name="jobs"></a>Úlohy
| Detaily chyby | Alternativní řešení |
| --- | --- |
| Zrušení není podporován pro tento typ úlohy – Počkejte, dokud se úloha nedokončí. |Žádný |
| Úlohu není možné zrušit stavu – Počkejte, dokud se úloha nedokončí. <br>NEBO<br> Vybraná úloha není ve stavu zrušitelný – Počkejte prosím, než se úloha dokončí. |Ve všech pravděpodobnost téměř dokončení úlohy. Počkejte prosím, než je úloha dokončena.|
| Tuto úlohu nelze zrušit, protože se nenachází v průběhu – zrušení se podporuje jenom pro úlohy, které jsou v průběhu. Prosím pokus o zrušení u probíhající úlohy. |K tomu dochází z důvodu přechodné stavu. Počkejte zhruba minutu a zkuste operaci zrušit. |
| Nepodařilo se zrušit úlohu – Počkejte prosím, až se úloha dokončí. |Žádný |

## <a name="restore"></a>Obnovení
| Detaily chyby | Alternativní řešení |
| --- | --- |
| Obnovení se nezdařilo s Cloud vnitřní chyba |<ol><li>Cloudové služby, ke kterému se pokoušíte obnovit je nakonfigurován s nastavením DNS. Můžete zkontrolovat <br>$deployment = get-AzureDeployment - ServiceName "ServiceName"-slotu "Produkční" Get AzureDns - části Networkinterfaceconfigurations $deployment. Části Networkinterfaceconfigurations<br>Pokud není nakonfigurovaná adresa, znamená to, jestli nakonfigurované nastavení DNS.<br> <li>Cloudová služba, do kterého se pokoušíte obnovit je nakonfigurována s vyhrazené IP adresy a stávající virtuální počítače v cloudové službě se v zastaveném stavu.<br>Můžete zkontrolovat, že Cloudová služba má vyhrazené IP pomocí následujících rutin prostředí powershell:<br>$deployment = get-AzureDeployment - ServiceName "servicename"-slotu "Produkční" $ programu dep. ReservedIPName <br><li>Pokoušíte se při obnovení virtuálního počítače s následující zvláštní konfigurace sítě ve stejné cloudové službě. <br>– Virtuální počítače v rámci konfigurace služby Vyrovnávání zatížení (interní a externí)<br>– Virtuální počítače s víc vyhrazených IP adres<br>– Virtuální počítače s několika síťovými kartami<br>Vyberte novou cloudovou službu v uživatelském rozhraní nebo najdete [obnovení aspekty](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) pro virtuální počítače se speciální konfigurací sítě.</ol> |
| Vybraný název DNS je již obsazen – Zadejte prosím jiný název DNS a zkuste to znovu. |Název DNS, odkazuje na název cloudové služby (obvykle končící. cloudapp.net). Toto musí být jedinečný. Pokud dojde k této chybě, musíte zvolit jiný název virtuálního počítače během obnovení. <br><br> Tato chyba se zobrazí pouze pro uživatele na webu Azure portal. Operaci obnovení prostřednictvím prostředí PowerShell bude úspěšné, protože pouze obnoví disky a není vytvoření virtuálního počítače. Chyba bude čelí, když virtuální počítač je explicitně vytvořené po operaci obnovení na disku. |
| Konfigurace zadané virtuální sítě není správná – Zadejte prosím jinou konfiguraci virtuální sítě a zkuste to znovu. |Žádný |
| Zadaná Cloudová služba používá rezervovanou IP adresu, což neodpovídá s konfigurací obnoveného virtuálního počítače – prosím jinou cloudovou službu, která nepoužívá rezervovanou IP adresu, zadejte nebo vyberte jiný bod obnovení pro obnovení z. |Žádný |
| Cloudová služba dosáhla limitu počtu vstupních koncových bodů – zkuste operaci zopakovat tak, že zadáte jinou cloudovou službu nebo s použitím existujícího koncového bodu. |Žádný |
| Zálohování trezoru a cílový účet úložiště jsou ve dvou různých oblastech – Ujistěte se, že účet úložiště zadaný u operace obnovení ve stejné oblasti Azure jako trezor záloh. |Žádný |
| Účet úložiště zadaný pro operaci obnovení není podporováno – pouze základní/standardní účty úložiště s místně redundantním nebo nastavení geograficky redundantní replikace se podporují. Vyberte prosím účet úložiště podporuje |Žádný |
| Typ účtu úložiště zadaný pro operaci obnovení není online – Ujistěte se, že účet úložiště zadaný u operace obnovení je online |K tomu může dojít z důvodu o přechodnou chybu ve službě Azure Storage nebo kvůli výpadku. Zvolte prosím jiný účet úložiště. |
| Byla dosažena kvóta skupin prostředků – odstraňte prosím některé skupiny prostředků z webu Azure portal nebo se obraťte na podporu Azure o navýšení limitů. |Žádný |
| Vybraná podsíť neexistuje - Vyberte prosím podsíť, která existuje |Žádný |
| Služba zálohování nemá oprávnění pro přístup k prostředkům ve vašem předplatném. |Tento problém vyřešíte tak, první obnovení disků pomocí kroků popsaných v části **obnovení zálohovaných disky** v [výběr VM obnovit konfiguraci](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Potom použijte PowerShell kroky uvedené v [vytvořit virtuální počítač z obnovených disků](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) vytvořit úplnou virtuální počítač z obnovených disků. |

## <a name="backup-or-restore-taking-time"></a>Zálohování nebo obnovení trvá čas
Pokud se zobrazí vaše backup(>12 hours) nebo obnovení trvá time(>6 hours):
* Vysvětlení [faktory přispívající k prodloužení doby zálohování](backup-azure-vms-introduction.md#total-vm-backup-time) a [faktory, které ovlivňují dobu obnovení](backup-azure-vms-introduction.md#total-restore-time).
* Ujistěte se, že se řídíte [doporučené zálohovat](backup-azure-vms-introduction.md#best-practices). 

## <a name="vm-agent"></a>Agent virtuálního počítače
### <a name="setting-up-the-vm-agent"></a>Nastavení agenta virtuálního počítače
Obvykle je Agent virtuálního počítače už ve virtuálních počítačích vytvořených z Galerie Azure. Virtuální počítače, které se migrují z místních datových centrech by však mít nainstalovaného agenta virtuálního počítače. Pro tyto virtuální počítače musí být explicitně nainstalovaný Agent virtuálního počítače.

Pro virtuální počítače s Windows:

* Stáhněte si a nainstalujte [MSI agenta](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). K dokončení instalace je potřeba oprávnění správce.
* Pro klasické virtuální počítače [aktualizovat vlastnosti virtuálního počítače](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) označující, zda je agent nainstalovaný. Tento krok není povinný pro virtuální počítače Resource Manageru.

Pro virtuální počítače s Linuxem:

* Nainstalujte nejnovější verzi z úložiště distribuce. Jsme **důrazně doporučujeme** instalace agentů pouze prostřednictvím distribuce úložiště. Podrobnosti o název balíčku najdete [úložiště agent systému Linux](https://github.com/Azure/WALinuxAgent) 
* Pro klasické virtuální počítače [aktualizovat vlastnosti virtuálního počítače](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) označující, zda je agent nainstalovaný. Tento krok není povinný pro virtuální počítače Resource Manageru.

### <a name="updating-the-vm-agent"></a>Aktualizace agenta virtuálního počítače
Pro virtuální počítače s Windows:

* Aktualizace agenta virtuálního počítače je stejně jednoduchá, jako přeinstalace [binárních souborů agenta virtuálního počítače](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Ale je potřeba zajistit, že se během aktualizace agenta virtuálního počítače neběží žádná operace zálohování.

Pro virtuální počítače s Linuxem:

* Postupujte podle pokynů [aktualizace agenta virtuálního počítače Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
Jsme **důrazně doporučujeme** aktualizací agenta pouze prostřednictvím distribuce úložiště. Nedoporučujeme stažením agenta kódu z githubu přímo a aktualizacích. Pokud nejnovější verzi agenta není k dispozici pro vaši distribuci, kontaktujte prosím podporu distribuční pokyny o tom, jak nainstalovat nejnovější verzi agenta. Můžete zkontrolovat nejnovější [Windows Azure Linux agent](https://github.com/Azure/WALinuxAgent/releases) informace v úložišti github.

### <a name="validating-vm-agent-installation"></a>Ověřuje se instalace agenta virtuálního počítače
Jak zkontrolovat verzi agenta virtuálního počítače na virtuálních počítačích s Windows:

1. Přihlaste se na virtuálním počítači Azure a přejděte do složky *C:\WindowsAzure\Packages*. Měl by být přítomný soubor WaAppAgent.exe.
2. Pravým tlačítkem myši klikněte na soubor, přejděte na **Vlastnosti** a poté vyberte kartu **Podrobnosti**. Pole verze produktu by mělo být 2.6.1198.718 nebo vyšší

## <a name="troubleshoot-vm-snapshot-issues"></a>Řešení potíží s snímku virtuálního počítače
Zálohování virtuálních počítačů se spoléhá na vydávání příkazů snímek základního úložiště. Nemáte přístup k úložišti, nebo zpoždění při provádění úloh snímků může způsobit selhání úlohy zálohování. Následující může způsobit selhání úlohy snímku.

1. Síťový přístup k úložišti je blokováno pomocí skupiny zabezpečení sítě<br>
    Další informace o tom, jak [povolit přístup k síti](backup-azure-arm-vms-prepare.md#establish-network-connectivity) úložiště pomocí buď na seznam povolených IP nebo prostřednictvím proxy serveru.
2. Virtuální počítače pomocí zálohování serveru Sql Server nakonfigurovaný, může způsobit zpoždění úlohy vytvoření snímku <br>
   Ve výchozím nastavení virtuálního počítače problémy se zálohováním VSS úplné zálohování na virtuálních počítačích s Windows. Na virtuálních počítačích, na kterých běží Sql servery a pokud je nakonfigurovaná zálohování serveru Sql Server to může způsobit zpoždění při provádění snímku. Nastavte následující klíč registru, pokud dochází k selhání zálohování kvůli potíže se snímky.

   ```
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```
3. Stav virtuálního počítače hlášených nesprávně, protože virtuální počítač je vypnutý v protokolu RDP.  <br>
   Pokud vypnete virtuální počítač v protokolu RDP, vraťte se prosím na portál, že se správně odráží stav virtuálního počítače. Pokud tomu tak není, vypněte virtuální počítač na portálu pomocí možnosti "Vypnout" na řídicím panelu virtuálních počítačů.
4. Pokud více než čtyři virtuální počítače sdílet stejnou cloudovou službou, nakonfigurujte více zásad zálohování, které chcete připravit záložní časy proto není, více než čtyři záloh virtuálních počítačů se spustí ve stejnou dobu. Zkuste rozšířit doby hodinu od sebe mezi zásadami zálohování spuštění.
5. Virtuální počítač běží na vysoké využití procesoru nebo paměti.<br>
   Pokud virtuální počítač běží na vysoké využití procesoru usage(>90%) nebo paměť, úloha snímku je zařazeno do fronty, zpoždění a bude nakonec získá vypršel časový limit. Vyzkoušejte zálohování na vyžádání v takových situacích.

<br>

## <a name="networking"></a>Sítě
Stejně jako všechna rozšíření záložní linka se potřebují přístup k veřejnému Internetu fungovat. Nemáte přístup k veřejnému Internetu může projevit různými způsoby:

* Může selhat instalace rozšíření
* Operace zálohování (např. snímku disku) může selhat.
* Zobrazení stavu operace zálohování může selhat.

Byla kloubové potřebu řešení veřejné internetové adresy [tady](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Budete muset Zkontrolujte konfiguraci DNS pro virtuální síť a ujistěte se, že identifikátory URI Azure lze vyřešit.

Po dokončení správně překlad názvů, přístup k IP adresy Azure také musí být k dispozici. Pokud chcete odblokovat přístup k infrastruktuře Azure, použijte jeden z těchto kroků:

1. Seznam povolených adres Azure datacenter rozsahy IP adres.
   * Získat seznam [Azure datacenter IP adresy](https://www.microsoft.com/download/details.aspx?id=41653) uvedení na seznamu povolených.
   * Odblokování pomocí IP adresy [New-NetRoute](https://docs.microsoft.com/powershell/module/nettcpip/new-netroute) rutiny. V okně PowerShell se zvýšenými oprávněními (Spustit jako správce) spusťte tuto rutinu v rámci virtuálního počítače Azure.
   * Přidání pravidel k NSG (Pokud nemáte na místě) povolit přístup k IP adres.
2. Vytvoření cesty pro příjem provozu HTTP
   * Pokud máte některá omezení sítě na místě (skupiny zabezpečení sítě, například) nasadit proxy server HTTP pro směrování provozu. Postup nasazení serveru Proxy protokolu HTTP lze nalézt [tady](backup-azure-arm-vms-prepare.md#establish-network-connectivity).
   * Přidání pravidel k NSG (Pokud nemáte na místě) umožňující přístup k Internetu proxy server HTTP.

> [!NOTE]
> DHCP musí být povolené uvnitř hosta pro zálohování virtuálních počítačů IaaS pro práci.  Pokud potřebujete statickou privátní IP adresu, měli byste ji nakonfigurovat prostřednictvím platformy. Možnosti DHCP ve virtuálním počítači musí být povolené vlevo.
> Zobrazit další informace o [nastavení statické interní privátní IP adresy](../virtual-network/virtual-networks-reserved-private-ip.md).
>
>
