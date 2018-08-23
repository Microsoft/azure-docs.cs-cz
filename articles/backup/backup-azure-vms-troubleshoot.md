---
title: Zálohování řešení potíží s virtuálním počítači Azure
description: Řešení zálohování a obnovení virtuálních počítačů Azure
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 8/7/2018
ms.author: trinadhk
ms.openlocfilehash: 5dc722b54127731be774bb4a0a7ff9cb359baa76
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "42058323"
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Odstraňování potíží se zálohováním virtuálních počítačů Azure
Řešení potíží s chybami při používání Azure Backup s informacemi o uvedené v následující tabulce došlo k chybě.

| Podrobnosti o chybě | Alternativní řešení |
| --- | --- |
| Operace se nedá provést, protože virtuální počítač už neexistuje. -Zastaví ochranu virtuálního počítače bez odstranění dat záloh. Další podrobnosti najdete v http://go.microsoft.com/fwlink/?LinkId=808124 |To se stane, když primární virtuální počítač se odstraní, ale zásady zálohování pokračuje v hledání pro virtuální počítač k zálohování. Chcete-li vyřešit tuto chybu: <ol><li> Znovu vytvořte virtuální počítač se stejným názvem a stejný název skupiny prostředků [název cloudové služby],<br>(NEBO)</li><li> Zastavte ochranu virtuálního počítače s nebo bez něj odstraňují se záložní data. [Další podrobnosti](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| Operace snímku nebyla úspěšná kvůli bez připojení k síti na virtuální počítač – Ujistěte se, že virtuální počítač má přístup k síti. Pro snímek úspěšný buď seznamu povolených IP adres Azure datacenter IP rozsahů adres nebo nastavení proxy serveru pro přístup k síti. Další informace najdete v http://go.microsoft.com/fwlink/?LinkId=800034. Pokud už používáte proxy server, ujistěte se, že jsou správně nakonfigurované nastavení proxy serveru | Nastane, pokud odepřete odchozí připojení k Internetu na virtuálním počítači. Rozšíření snímku virtuálního počítače vyžaduje připojení k Internetu pro pořízení snímku discích. [Na opravě snímku chyby způsobené blokované síťového přístupu najdete v části](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine). |
| Agent virtuálního počítače není schopen komunikovat se službou Azure Backup. – Ujistěte se virtuální počítač má síťové připojení a agent virtuálního počítače je nejnovější a spuštěna. Další informace najdete v článku http://go.microsoft.com/fwlink/?LinkId=800034. |K této chybě dojde, pokud dojde k nějakému problému s agentem VM, nebo blokovaný přístup k síti na infrastrukturu Azure nějakým způsobem. [Další informace](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#vm-agent-unable-to-communicate-with-azure-backup) o ladění můžete virtuální počítač snímek problémy.<br> Agent virtuálního počítače není způsobuje problémy, restartujte virtuální počítač. Nesprávný stav virtuálního počítače může způsobit problémy a restartování virtuálního počítače obnoví stav. |
| Virtuální počítač je ve stavu nezdařeného zřizování – restartování virtuálního počítače a ujistěte se, že je virtuální počítač spuštěný nebo vypnout. | Tato chyba nastane, pokud jeden z rozšíření selhání vede stav virtuálního počítače ve stavu selhání zřizování. Přejít na seznam přípon a zjistit, jestli se nezdařilo rozšíření, odeberte ji a zkuste restartovat virtuální počítač. Pokud všechna rozšíření, je v běžícím stavu, kontrola, zda je spuštěná Služba agenta virtuálního počítače. Pokud ne, restartujte službu agenta virtuálního počítače. | 
| Operace rozšíření VMSnapshot selhala pro managed disks – zkuste operaci zálohování zopakovat. Pokud problém přetrvává, postupujte podle pokynů na adrese 'http://go.microsoft.com/fwlink/?LinkId=800034". Pokud problém stále přetrvává, obraťte se na podporu Microsoftu | Tato chyba nastane, pokud služba Backup se nepodařilo aktivovat snímku. [Další informace](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#vmsnapshot-extension-operation-failed) o ladění VM snapshot problémy. |
| Může nelze zkopírovat snímek virtuálního počítače, protože je dostatek volného místa v účtu úložiště – Ujistěte se, že účet úložiště obsahuje volné místo odpovídající množství dat na disky premium storage připojených k virtuálnímu počítači | V případě virtuálních počítačů úrovně premium na zásobník záloh virtuálních počítačů V1 jsme zkopírování snímku do účtu úložiště. Toto je zajistit, že provoz správy zálohování, který pracuje na snímek, neomezuje počet vstupně-výstupních operací aplikace díky diskům premium k dispozici. Společnost Microsoft doporučuje že přidělit jenom 50 % (17,5 TB) prostoru účet celkový úložiště tak, že služba Azure Backup můžete zkopírování snímku do úložiště účtu a přenos dat z tohoto umístění zkopírovaného v účtu úložiště do trezoru. | 
| Nelze operaci provést, protože agent virtuálního počítače nereaguje |K této chybě dojde, pokud dojde k nějakému problému s agentem VM, nebo blokovaný přístup k síti na infrastrukturu Azure nějakým způsobem. Pro virtuální počítače s Windows zkontrolujte stav služby agenta virtuálního počítače do služby a určuje, zda agent zobrazí v programy v Ovládacích panelech. Zkuste odebrat program z ovládacího panelu a opětovné instalaci agenta, jak je uvedeno [níže](#vm-agent). Po opětovné instalaci agenta, aktivujte zálohování ad hoc k ověření. |
| Operace rozšíření služby Recovery services se nezdařilo. – Ujistěte se, že nejnovější agent virtuálního počítače je k dispozici na virtuálním počítači a je spuštěna Služba agenta. Zkuste operaci zálohování zopakujte. Pokud selže celá operace zálohování, kontaktujte podporu Microsoftu. |Tato chyba se vyvolá, když se agent virtuálního počítače je zastaralá. Přečtěte si níže aktualizace agenta virtuálního počítače v části "Aktualizace the Agent virtuálního počítače". |
| Virtuální počítač neexistuje. – Ujistěte se, že virtuální počítač existuje, nebo vyberte jiný virtuální počítač. |Nastane, pokud primární virtuální počítač se odstraní, ale zásady zálohování i nadále vyhledejte virtuální počítač k zálohování. Chcete-li vyřešit tuto chybu: <ol><li> Znovu vytvořte virtuální počítač se stejným názvem a stejný název skupiny prostředků [název cloudové služby],<br>(NEBO)<br></li><li>Zastavte ochranu virtuálního počítače bez odstranění zálohovaná data. [Další podrobnosti](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| Příkaz se nepodařilo spustit. -U této položky právě probíhá jiná operace. Počkejte, dokud se nedokončí předchozí operace a pak zkuste operaci zopakovat. |Spuštěná existující úloha zálohování a novou úlohu nelze spustit, dokud se nedokončí aktuální úlohu. |
| Kopírování virtuálních pevných disků ze služby Recovery Services vault vypršení časového limitu – zkuste operaci zopakovat za několik minut. Pokud to problém nevyřeší, obraťte se na oddělení podpory Microsoftu. | Nastane, pokud dochází k přechodné chybě na straně úložiště nebo pokud službu Backup v účtu úložiště dostatek vstupně-výstupních operací pro přenos dat do trezoru, v časovém limitu. Ujistěte se, že chcete postupovat podle [osvědčené postupy při konfiguraci vašich virtuálních počítačů](backup-azure-vms-introduction.md#best-practices). Váš virtuální počítač přesunout do jiného účtu úložiště, který není načten a opakujte úlohu zálohování.|
| Zálohování selhalo s interní chybou – zkuste operaci zopakovat za několik minut. Pokud se problém nevyřeší, obraťte se na Microsoft Support |Tato chyba se zobrazí dvou důvodů: <ol><li> K používání úložiště virtuálního počítače je přechodný problém. Zkontrolujte [web Azure stavu](https://azure.microsoft.com/status/) zobrazíte, pokud dojde k problémům výpočetní prostředky, úložiště nebo sítě v oblasti. Po vyřešení problému opakujte úlohu zálohování. <li>Původní virtuální počítač odstranil a bod obnovení nelze provést. Chcete zachovat zálohovaná data pro odstraněného virtuálního počítače, ale odebrat chyby zálohování: zrušit ochranu virtuálního počítače a vyberte možnost zachovat data. Tato akce zastaví naplánované úlohy zálohování a opakované chybové zprávy. |
| Nepovedlo se nainstalovat rozšíření služby Azure Recovery Services na vybranou položku - agent virtuálního počítače je předpokladem pro rozšíření Azure Recovery Services. Nainstalujte agenta virtuálního počítače Azure a restartujte operaci zápisu |<ol> <li>Zkontrolujte, zda správně nainstalován agent virtuálního počítače. <li>Zkontrolujte, jestli že je správně nastaven příznak na konfiguraci virtuálního počítače.</ol> [Přečtěte si další](#validating-vm-agent-installation) o instalaci agenta virtuálního počítače a ověření instalace agenta virtuálního počítače. |
| Instalace rozšíření selhala s chybou "modelu COM + se nepovedlo komunikovat s Microsoft Distributed Transaction Coordinator |To obvykle znamená, zda není spuštěna služba COM +. Kontaktujte podporu Microsoftu o pomoc na tento problém vyřešíme. |
| Operace snímku nebyla úspěšná kvůli chybě operace VSS "Tato jednotka je uzamčena nástrojem BitLocker Drive Encryption. Je nutné ji odemknout pomocí ovládacích panelů. |Vypnout nástroj BitLocker pro všechny disky na virtuálním počítači a podívejte se, pokud je vyřešen problém stínové kopie svazku |
| Virtuální počítač není ve stavu, který umožňuje zálohování. |<ul><li>Pokud je virtuální počítač v přechodném stavu mezi **systémem** a **vypnout**počkejte chcete změnit stav a potom aktivujte úlohu zálohování. <li> Pokud virtuální počítač je virtuální počítač s Linuxem a používá modul jádro Linuxu rozšířeného zabezpečení, vyloučit cestu agenta pro Linux (_/var/lib/waagent_) ze zásad zabezpečení a ujistěte se, že je nainstalované rozšíření zálohování.  |
| Virtuální počítač Azure nebyl nalezen. |Této chyby nastane, pokud je primární virtuální počítač odstraněn, ale zásady zálohování pokračuje v hledání odstraněného virtuálního počítače. Chcete-li vyřešit tuto chybu: <ol><li>Znovu vytvořte virtuální počítač se stejným názvem a stejný název skupiny prostředků [název cloudové služby], <br>(NEBO) <li> Vypněte ochranu pro tento virtuální počítač tak, aby úlohy zálohování se nevytvoří. </ol> |
| Agent virtuálního počítače není k dispozici na virtuálním počítači – nainstalujte agenta virtuálního počítače a všechny požadavky a restartujte operaci. |[Přečtěte si další](#vm-agent) o instalaci agenta virtuálního počítače a ověření instalace agenta virtuálního počítače. |
| Operace snímku nebyla úspěšná, protože zapisovače VSS jsou ve špatném stavu |Budete muset restartovat zapisovače VSS (službu Stínová kopie svazku), které jsou ve špatném stavu. Chcete-li toho dosáhnout, z příkazového řádku se zvýšenými oprávněními, spusťte ```vssadmin list writers```. Výstup obsahuje všechny zapisovače VSS a jejich stav. Pro všechny zapisovače služby VSS, jejichž stav není "[1] stabilní verze" restartujte zapisovač VSS spuštěním následujících příkazů z příkazového řádku se zvýšenými oprávněními:<br> ```net stop serviceName``` <br> ```net start serviceName```|
| Operace snímku nebyla úspěšná kvůli chybě parsování konfigurace |To se stane, že z důvodu změny oprávnění k adresáři MachineKeys: _%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_ <br>Spusťte následující příkaz a ověřte, zda jsou oprávnění pro adresář MachineKeys výchozí značky:<br>_icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_ <br><br> Výchozí oprávnění jsou:<br>Everyone:(R,W) <br>BUILTIN\Administrators:(F)<br><br>Pokud se zobrazí oprávnění v adresáři MachineKeys jiné než výchozí, postupujte podle následujících kroků správnými oprávněními, odstraňte certifikát a aktivovat zálohování.<ol><li>Oprava oprávnění MachineKeys adresáře.<br>Oprávnění pomocí Průzkumníka vlastnosti zabezpečení a Upřesnit nastavení zabezpečení v adresáři, resetovat zpátky na výchozí hodnoty. Odebrat všechny objekty uživatelů (kromě výchozího) z adresáře a ujistěte **všichni** oprávnění má zvláštní přístup pro:<br>-Zobrazovat obsah složky / Číst data <br>– Čtení atributy <br>-Číst rozšířené atributy <br>-Vytvářet soubory / Zapisovat data <br>-Vytvářet složky / Připojovat data<br>-Zapisovat atributy<br>-Zapisovat rozšířené atributy<br>-Oprávnění ke čtení<br><br><li>Odstranit všechny certifikáty které **vystaveno pro** je model nasazení classic nebo **generátor certifikátů CRP Windows Azure**.<ul><li>[Používat konzolu Certifikáty (místní počítač)](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx)<li>V části **osobní** > **certifikáty**, odstraňte všechny certifikáty kde **vystaveno pro** je model nasazení classic nebo **Windows Azure CRP Generátor certifikátů**.</ul><li>Aktivace úlohy zálohování virtuálního počítače. </ol>|
| Služba Azure Backup nemá dostatečná oprávnění pro Key Vault pro zálohování šifrovaných virtuálních počítačů. |Služby zálohování by měl být k dispozici tato oprávnění v prostředí PowerShell pomocí kroků popsaných v **povolit zálohování** část [dokumentaci k Powershellu](backup-azure-vms-automation.md). |
|Snímek instalaci rozšíření se nepovedla s chybou - modelu COM + se nepovedlo komunikovat s Microsoft Distributed Transaction Coordinator | Z příkazového řádku se zvýšenými oprávněními spusťte službu Windows "aplikace modelu COM + systému", například _net start COMSysApp_. <br>Pokud se službu nepodaří spustit, pak:<ol><li> Ujistěte se, že v protokolu na základě je "Síťové služby" služba "Distributed Transaction Coordinator". Pokud není, změnit přihlašovací účet "Síťové služby", restartujte tuto službu a zkuste spustit "aplikace modelu COM + systému". "<li>Pokud "aplikace systému COM + nebude začít, použijte následující kroky k instalaci/odinstalaci služby"Distributed Transaction Coordinator":<br> -Zastavit službu MSDTC<br> – Otevřete příkazový řádek (cmd) <br> – Spusťte příkaz ```msdtc -uninstall``` <br> – Spusťte příkaz ```msdtc -install``` <br> -Spuštění služby MSDTC<li>Spusťte službu systému windows "aplikace modelu COM + systému". Jednou "systém COM + aplikace spustí, aktivační události úlohy zálohování z webu Azure portal.</ol> |
|  Operace snímku nebyla úspěšná kvůli chybě modelu COM +. | Doporučená akce je restart služby systému windows "aplikace modelu COM + systému" (z příkazového řádku se zvýšenými oprávněními - _net start COMSysApp_). Pokud se problém nevyřeší, restartujte virtuální počítač. Pokud vám nepomohly restartování virtuálního počítače, zkuste [odebrání rozšíření VMSnapshot](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#cause-3-the-backup-extension-fails-to-update-or-load) a aktivovat zálohování ručně. |
| Nepovedlo se zablokovat nejméně jeden přípojný bod virtuálního počítače na pořízení konzistentního snímku systému souborů | Použijte k tomu následující postup: <ol><li>Zkontrolujte stav systému souborů všech připojených zařízení používat _"tune2fs"_ příkazu.<br> Například: tune2fs -l/dev/sdb1 \| grep "Stavu systému souborů" <li>Odpojte zařízení, pro které systému souborů stavu není pomocí čisté _"umount"_ příkaz <li> Spusťte kontrolu FileSystemConsistency na těchto zařízeních pomocí _"fsck"_ příkaz <li> Znovu připojte zařízení a opakujte zálohování.</ol> |
| Operace snímku nebyla úspěšná kvůli chybě při vytváření zabezpečený komunikační kanál | <ol><Li> Otevřete Editor registru spuštěním regedit.exe v režimu se zvýšenými oprávněními. <li> Identifikujte všechny verze rozhraní .NET Framework, které jsou k dispozici ve vašem systému. Jsou k dispozici v nabídce hierarchie klíč registru "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft" <li> Pro každé rozhraní .net Framework, které jsou k dispozici v klíči registru a přidat následující klíč: <br> "SchUseStrongCrypto"=dword:00000001 </ol>|
| Operace snímku nebyla úspěšná kvůli chybě v instalaci distribuovatelné součásti Visual C++ pro Visual Studio 2012 | Přejděte na C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion a nainstalujte vcredist2012_x64. Ujistěte se, že hodnota klíče registru pro umožnění instalace této služby je nastavena na správnou hodnotu tedy hodnota klíče registru _HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver_ nastavená na 3 a 4 není. Pokud stále máte problémy s instalací, restartujte službu instalace spuštěním _MSIEXEC/UNREGISTER_ následovaný _MSIEXEC /REGISTER_ z příkazového řádku se zvýšenými oprávněními.  |


## <a name="jobs"></a>Úlohy
| Podrobnosti o chybě | Alternativní řešení |
| --- | --- |
| Zrušení není podporován pro tento typ úlohy – Počkejte, dokud se úloha nedokončí. |Žádný |
| Úlohu není možné zrušit stavu – Počkejte, dokud se úloha nedokončí. <br>NEBO<br> Vybraná úloha není ve stavu zrušitelný – Počkejte prosím, než se úloha dokončí. |Ve všech pravděpodobnost téměř dokončení úlohy. Počkejte prosím, než je úloha dokončena.|
| Tuto úlohu nelze zrušit, protože se nenachází v průběhu – zrušení se podporuje jenom pro úlohy, které jsou v průběhu. Prosím pokus o zrušení u probíhající úlohy. |K tomu dochází z důvodu přechodné stavu. Počkejte zhruba minutu a zkuste operaci zrušit. |
| Nepodařilo se zrušit úlohu – Počkejte prosím, až se úloha dokončí. |Žádný |

## <a name="restore"></a>Obnovení
| Podrobnosti o chybě | Alternativní řešení |
| --- | --- |
| Obnovení se nezdařilo s Cloud vnitřní chyba |<ol><li>Cloudové služby, ke kterému se pokoušíte obnovit je nakonfigurován s nastavením DNS. Můžete zkontrolovat <br>$deployment = get-AzureDeployment - ServiceName "ServiceName"-slotu "Produkční" Get AzureDns - části Networkinterfaceconfigurations $deployment. Části Networkinterfaceconfigurations<br>Pokud není nakonfigurovaná adresa, znamená to, jestli nakonfigurované nastavení DNS.<br> <li>Cloudová služba, do kterého se pokoušíte obnovit je nakonfigurována s vyhrazené IP adresy a stávající virtuální počítače v cloudové službě se v zastaveném stavu.<br>Můžete zkontrolovat, že Cloudová služba má vyhrazené IP pomocí následujících rutin prostředí powershell:<br>$deployment = get-AzureDeployment - ServiceName "servicename"-slotu "Produkční" $ programu dep. ReservedIPName <br><li>Pokoušíte se při obnovení virtuálního počítače s následující zvláštní konfigurace sítě ve stejné cloudové službě. <br>– Virtuální počítače v rámci konfigurace služby Vyrovnávání zatížení (interní a externí)<br>– Virtuální počítače s víc vyhrazených IP adres<br>– Virtuální počítače s několika síťovými kartami<br>Vyberte novou cloudovou službu v uživatelském rozhraní nebo najdete [obnovení aspekty](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) pro virtuální počítače se speciální konfigurací sítě.</ol> |
| Vybraný název DNS je již obsazen – Zadejte prosím jiný název DNS a zkuste to znovu. |Název DNS, odkazuje na název cloudové služby (obvykle končící. cloudapp.net). Toto musí být jedinečný. Pokud dojde k této chybě, musíte zvolit jiný název virtuálního počítače během obnovení. <br><br> Tato chyba se zobrazí pouze pro uživatele na webu Azure portal. Operaci obnovení prostřednictvím prostředí PowerShell bude úspěšné, protože pouze obnoví disky a není vytvoření virtuálního počítače. Chyba bude čelí, když virtuální počítač je explicitně vytvořené po operaci obnovení na disku. |
| Konfigurace zadané virtuální sítě není správná – Zadejte prosím jinou konfiguraci virtuální sítě a zkuste to znovu. |Žádný |
| Zadaná Cloudová služba používá rezervovanou IP adresu, což neodpovídá s konfigurací obnoveného virtuálního počítače – prosím jinou cloudovou službu, která nepoužívá rezervovanou IP adresu, zadejte nebo vyberte jiný bod obnovení pro obnovení z. |Žádný |
| Cloudová služba dosáhla limitu počtu vstupních koncových bodů – zkuste operaci zopakovat tak, že zadáte jinou cloudovou službu nebo s použitím existujícího koncového bodu. |Žádný |
| Služby Recovery Services vault a cílový účet úložiště jsou ve dvou různých oblastech – Ujistěte se, že zadaný u operace obnovení účet úložiště, je ve stejné oblasti Azure jako trezor služby Recovery Services. |Žádný |
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

* Nainstalujte nejnovější verzi agenta z úložiště distribuce. Podrobnosti o název balíčku, najdete v článku [úložiště agent systému Linux](https://github.com/Azure/WALinuxAgent).
* Pro klasické virtuální počítače [použijte příspěvku na blogu k aktualizaci vlastností virtuálního počítače](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)a ověřit, že agent je nainstalovaný. Tento krok není povinný pro virtuální počítače Resource Manageru.

### <a name="updating-the-vm-agent"></a>Aktualizace agenta virtuálního počítače
Pro virtuální počítače s Windows:

* Pokud chcete aktualizovat agenta virtuálního počítače, přeinstalujte [binárních souborů agenta virtuálního počítače](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Před aktualizací agenta, ujistěte se, že žádná operace zálohování, k nimž došlo při aktualizaci agenta virtuálního počítače.

Pro virtuální počítače s Linuxem:

* Chcete-li aktualizovat agenta virtuálního počítače s Linuxem, postupujte podle pokynů v článku, [aktualizace agenta virtuálního počítače s Linuxem](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
**Vždy používejte úložiště distribuce aktualizace agenta**. Nestahovat agenta kódu z Githubu. Pokud nejnovější verzi agenta není k dispozici pro vaši distribuci, kontaktujte podporu distribuční pokyny k získání nejnovějšího agenta. Můžete také zkontrolovat nejnovější [Windows Azure Linux agent](https://github.com/Azure/WALinuxAgent/releases) informace v úložišti GitHub.

### <a name="validating-vm-agent-installation"></a>Ověřuje se instalace agenta virtuálního počítače

K ověření verze agenta virtuálního počítače na virtuálních počítačích s Windows:

1. Přihlaste se k virtuálnímu počítači Azure a přejděte do složky *C:\WindowsAzure\Packages*. Měl by být přítomný soubor WaAppAgent.exe.
2. Pravým tlačítkem myši klikněte na soubor, přejděte na **Vlastnosti** a poté vyberte kartu **Podrobnosti**. Pole verze produktu by mělo být 2.6.1198.718 nebo vyšší

## <a name="troubleshoot-vm-snapshot-issues"></a>Řešení potíží s snímku virtuálního počítače
Zálohování virtuálních počítačů se spoléhá na vydávání příkazů snímek základního úložiště. Nemáte přístup k úložišti, nebo zpoždění při provádění úloh snímků může způsobit selhání úlohy zálohování. Následující může způsobit selhání úlohy snímku.

1. Síťový přístup k úložišti je blokováno pomocí skupiny zabezpečení sítě<br>
    Další informace o tom, jak [povolit přístup k síti](backup-azure-arm-vms-prepare.md#establish-network-connectivity) úložiště pomocí buď na seznam povolených IP nebo prostřednictvím proxy serveru.
2. Virtuální počítače pomocí zálohování serveru Sql Server nakonfigurovaný, může způsobit zpoždění úlohy vytvoření snímku <br>
   Ve výchozím nastavení zálohování virtuálního počítače vytvoří VSS úplného zálohování na virtuálních počítačích s Windows. Virtuální počítače s SQL serverem, s SQL serverem zálohování nakonfigurovaný, může docházet k snímku zpoždění. Pokud snímek zpoždění způsobit selhání zálohování, nastavte následující klíč registru.

   ```
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

3. Stav virtuálního počítače hlášených nesprávně, protože virtuální počítač je vypnutý v protokolu RDP.  <br>
   Pokud jste použili vzdálené plochy pro vypnutí virtuálního počítače, ověřte, že stav virtuálního počítače na portálu je správná. Pokud stav není správný, použijte **vypnutí** možnost v řídicím panelu portálu virtuální počítač vypnout virtuální počítač.
4. Pokud více než čtyři virtuální počítače sdílejí stejné cloudové službě, rozděleny mezi více zásad zálohování virtuálních počítačů. Rozložte zálohování časy proto není, že spustit více než čtyři záloh virtuálních počítačů ve stejnou dobu. Došlo k pokusu o oddělení časy zahájení v zásadách o aspoň hodinu.
5. Virtuální počítač běží na vysoké využití procesoru nebo paměti.<br>
   Pokud virtuální počítač běží na vysoký poměr paměti nebo procesoru usage(>90%), vaše snímku úloha je zařazena do fronty, opožděné a nakonec vyprší časový limit. Pokud v takovém případě zkuste zálohu na vyžádání.

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
