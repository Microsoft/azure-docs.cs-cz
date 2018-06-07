---
title: Řešení chyb zálohy s virtuální počítač Azure
description: Řešení zálohování a obnovení virtuálních počítačů Azure
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 01/21/2018
ms.author: trinadhk
ms.openlocfilehash: d6e78d46f0886b06cb1cf3577c16c8bc4f842bab
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34607255"
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Odstraňování potíží se zálohováním virtuálních počítačů Azure
Řešení potíží s chyb zjištěných při pomocí Azure Backup informace uvedené v následující tabulce.

| Detaily chyby | Alternativní řešení |
| --- | --- |
| Operace se nedá provést, protože virtuální počítač už neexistuje. -Zastavení ochrany virtuálního počítače bez odstraňují se záložní data. Další podrobnosti o http://go.microsoft.com/fwlink/?LinkId=808124 |To se stane, když primární virtuální počítač odstraněn, ale zásady zálohování pokračuje v hledání pro virtuální počítač k zálohování. Chcete-li vyřešit tuto chybu: <ol><li> Znovu vytvořte virtuální počítač se stejným názvem a stejný název skupiny prostředků [název cloudové služby],<br>(NEBO)</li><li> Zastavte ochranu virtuálního počítače s nebo bez odstranění zálohovaná data. [Další informace](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| Snímku operace se nezdařila z důvodu žádné připojení k síti na virtuálním počítači - zkontrolujte, zda má počítač přístup k síti. Pro vytvoření snímku proběhla úspěšně buď rozsahy IP seznamu povolených IP adres Azure datacenter nebo nastavení proxy serveru pro přístup k síti. Další podrobnosti najdete v části http://go.microsoft.com/fwlink/?LinkId=800034. Pokud už používáte proxy server, ujistěte se, že jsou správně nakonfigurované nastavení proxy serveru | Tato chyba se vyvolá, když odepřete odchozí připojení k Internetu na virtuálním počítači. Připojení k Internetu je vyžadována pro rozšíření snímek virtuálního počítače k pořízení snímku základní disky virtuálního počítače. [Další informace](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine) o tom, jak opravit snímku selhání kvůli přístupu k síti blokované. |
| Agent virtuálního počítače se nepodařilo komunikovat se službou Azure Backup. -Zkontrolujte virtuální počítač má síťové připojení a agent virtuálního počítače je nejnovější a spuštěná. Další informace naleznete v  http://go.microsoft.com/fwlink/?LinkId=800034 |Tato chyba se vyvolá, pokud došlo k potížím s agenta virtuálního počítače, nebo blokovaný přístup k síti na infrastrukturu Azure nějakým způsobem. [Další informace](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#vm-agent-unable-to-communicate-with-azure-backup) o ladění do virtuálních počítačů snímku problémy.<br> Pokud agenta virtuálního počítače není způsobuje problémy, restartujte virtuální počítač. Nesprávný stav virtuálního počítače v některých případech může způsobit problémy a restartování virtuálního počítače obnoví tento "chybný stav". |
| Virtuální počítač je v chybovém stavu zřizování – restartujte virtuální počítač a ujistěte se, že virtuální počítač je ve stavu spuštění nebo vypnutí pro zálohování | K tomu dochází, pokud jeden z chyby rozšíření vede stav virtuálního počítače ve stavu selhání zřizování. Přejít na seznam rozšíření a jestli je neúspěšné rozšíření, odeberte ji a zkuste restartovat virtuální počítač. Pokud všechna rozšíření je v běžícím stavu, zkontrolujte, zda je spuštěna Služba agenta virtuálního počítače. Pokud ne, restartujte službu agent virtuálního počítače. | 
| VMSnapshot rozšíření operace se nezdařila pro spravované disky - opakujte operaci zálohování. Pokud se problém opakuje, postupujte podle pokynů v 'http://go.microsoft.com/fwlink/?LinkId=800034'. Pokud selže dál, kontaktujte prosím podporu společnosti Microsoft | Tato chyba, když se nepodaří aktivovat snímek služby zálohování Azure. [Další informace](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#vmsnapshot-extension-operation-failed) o ladění virtuální počítač snímek problémy. |
| Může kopírování snímku virtuálního počítače, z důvodu nedostatku volného místa v účtu úložiště – zkontrolujte, zda má účet úložiště volné místo ekvivalentní data obsažená na prémiových discích úložiště připojených k virtuálnímu počítači | V případě virtuálních počítačů úrovně premium v zásobníku zálohování virtuálních počítačů V1 jsme zkopírujte snímku do účtu úložiště. Toto je zajistit, že není správu záloh přenos, který funguje na snímek, omezit počet IOPS, které jsou k dispozici pro aplikaci používáte prémiové disky. Společnost Microsoft doporučuje že přidělit jen 50 % (17,5 TB) z celkový úložný prostor účet tak, že služba Azure Backup můžete zkopírovat snímku do úložiště účet a přenos dat z tohoto umístění zkopírovaný v účtu úložiště do trezoru. | 
| Nelze provést operaci, protože agenta virtuálního počítače není reaguje |Tato chyba se vyvolá, pokud došlo k potížím s agenta virtuálního počítače, nebo blokovaný přístup k síti na infrastrukturu Azure nějakým způsobem. Pro virtuální počítače Windows zkontrolujte stav služby agent virtuálního počítače v služby a zda se zobrazí agenta v programy v Ovládacích panelech. Zkuste odebrat program z ovládacích panelů a opětovné instalaci agenta, jak je uvedeno [pod](#vm-agent). Po opětovné instalaci agenta, spusťte zálohování v režimu ad hoc k ověření. |
| Obnovení služby rozšíření operace se nezdařila. -Zkontrolujte, že je k dispozici na virtuálním počítači nejnovější agent virtuálního počítače a zda je spuštěna Služba agenta. Opakujte operaci zálohování a pokud se nezdaří, obraťte se na podporu společnosti Microsoft. |Tato chyba se vyvolá, když agenta virtuálního počítače je zastaralý. Najdete v části "Aktualizace virtuálního počítače Agent" aktualizovat agenta virtuálního počítače. |
| Virtuální počítač neexistuje. -Prosím zkontrolujte, zda že tento virtuální počítač existuje, nebo vyberte jiný virtuální počítač. |To se stane, když dojde k odstranění primární virtuální počítač ale zásady zálohování nadále vypadat pro virtuální počítač k provedení zálohování. Chcete-li vyřešit tuto chybu: <ol><li> Znovu vytvořte virtuální počítač se stejným názvem a stejný název skupiny prostředků [název cloudové služby],<br>(NEBO)<br></li><li>Zastavte ochranu virtuálního počítače bez odstranění zálohovaná data. [Další informace](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| Provedení příkazu se nezdařilo. -Na této položce aktuálně probíhá jiná operace. Počkejte prosím na dokončení předchozí operace a poté opakujte |Existující zálohy na virtuálním počítači se systémem a novou úlohu nelze spustit, pokud existující úloha běží. |
| Kopírování virtuálních pevných disků z trezoru záloh vypršel časový limit – opakujte operaci za několik minut. Pokud to problém nevyřeší, obraťte se na oddělení podpory Microsoftu. | K tomu dojde, pokud dojde k přechodné chybě na straně úložiště nebo pokud se služba backup není dostatečná IOPS z účtu úložiště hostování virtuálních počítačů k přenosu dat během časového limitu do trezoru. Ujistěte se, že jste udělali [osvědčené postupy](backup-azure-vms-introduction.md#best-practices) při nastavení zálohování. Zkuste přesunout virtuální počítač do jiného úložiště účet, který není načten a opakujte zálohování.|
| Zálohování došlo k vnitřní chybě – opakujte operaci za několik minut. Pokud potíže potrvají, obraťte se na Microsoft Support |Tuto chybu můžete získat z důvodů 2: <ol><li> V přístupu k úložiště virtuálního počítače je dočasný problém. Zkontrolujte, zda [stavu Azure](https://azure.microsoft.com/status/) jestli jsou všechny problém související s vypočítat průběžné, úložiště nebo sítě v oblasti. Jakmile je problém vyřešen, opakujte úlohu zálohování. <li>Původní virtuální počítač byl odstraněn, a proto nelze vytvářet bod obnovení. Chcete zachovat zálohovaná data pro virtuální počítač odstraněný, ale odebrat zálohování chyby: zrušení ochrany virtuálního počítače a vyberte možnost zachovat data. Tato akce ukončí naplánované úlohy zálohování a opakovaného chybové zprávy. |
| Nepodařilo se nainstalovat rozšíření služeb zotavení Azure na vybranou položku - agenta virtuálního počítače je předpokladem pro rozšíření Azure Recovery Services. Nainstalujte agenta virtuálního počítače Azure a restartujte operaci registrace |<ol> <li>Zkontrolujte, zda správně nainstalován agent virtuálního počítače. <li>Zkontrolujte, jestli je že správně nastavený příznak na konfiguraci virtuálního počítače.</ol> [Další informace](#validating-vm-agent-installation) o instalaci agenta virtuálního počítače a postup ověření instalace agenta virtuálního počítače. |
| Instalace rozšíření se nezdařilo s chybou "modelu COM + se nepodařilo komunikovat Microsoft Distributed Transaction Coordinator |To obvykle znamená, zda není spuštěna služba modelu COM +. Nápovědu k opravě tohoto problému, kontaktujte podporu společnosti Microsoft. |
| Snímek operace se nezdařila s chybou operaci služby Stínová kopie svazku "Tato jednotka je uzamčen nástrojem BitLocker Drive Encryption. Je třeba odemknout tuto jednotku z ovládacích panelů. |Vypnout nástroj BitLocker pro všechny disky na virtuálním počítači a zkontrolujte, zda je k odstranění problému služby Stínová kopie svazku |
| Virtuální počítač není ve stavu, který umožňuje zálohování. |<ul><li>Zkontrolujte, jestli virtuální počítač je ve stavu přechodný mezi spuštění a vypnutí dolů. Pokud se jedná, počkejte stav virtuálního počítače, který má být jeden z nich a aktivuje zálohovat znovu. <li> Pokud virtuální počítač je virtuální počítač s Linuxem a používá [Linux rozšířené zabezpečení] modulu jádra, budete muset vyloučit cestu agenta pro Linux (_/var/lib/waagent_) z zabezpečení zásady a ujistěte se, že zálohování rozšíření nainstalovány.  |
| Virtuální počítač Azure nebyla nalezena. |To se stane, když dojde k odstranění primární virtuální počítač ale zásady zálohování nadále vypadat pro virtuální počítač k provedení zálohování. Chcete-li vyřešit tuto chybu: <ol><li>Znovu vytvořte virtuální počítač se stejným názvem a stejný název skupiny prostředků [název cloudové služby], <br>(NEBO) <li> Vypněte ochranu pro tento virtuální počítač tak, aby úlohy zálohování se nevytvoří. </ol> |
| Agent virtuálního počítače není k dispozici na virtuálním počítači – nainstalujte součásti a agent virtuálního počítače a pak restartujte operaci. |[Další informace](#vm-agent) o instalace agenta virtuálního počítače a postup ověření instalace agenta virtuálního počítače. |
| Snímek operace se nezdařila z důvodu zapisovače VSS ve špatném stavu |Budete muset restartovat zapisovače VSS (službu Stínová kopie svazku), které jsou ve špatném stavu. Pokud chcete dosáhnout, z příkazového řádku se zvýšenými oprávněními spusťte _vssadmin seznam zapisovačů_. Výstup obsahuje všechny zapisovače VSS a jejich stavu. Pro každý zapisovač VSS, jejichž stav není "[1] stabilní" restartujte zapisovače VSS spuštěním následujících příkazů z příkazového řádku se zvýšenými oprávněními:<br> _serviceName net stop_ <br> _net start serviceName_|
| Snímek operace se nezdařila z důvodu selhání analýzy konfigurace |To se stane, že z důvodu změnu oprávnění v adresáři MachineKeys: _%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_ <br>Spusťte následující příkaz a ověřte, zda jsou oprávnění v adresáři MachineKeys výchozí-ty, které jsou:<br>_icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_ <br><br> Výchozí oprávnění jsou:<br>Everyone:(R,W) <br>BUILTIN\Administrators:(F)<br><br>Pokud se zobrazí oprávnění v adresáři MachineKeys jiný než výchozí, postupujte podle níže uvedených pokynů správnými oprávněními, odstraňte certifikát a spustit zálohování.<ol><li>Opravte oprávnění v adresáři MachineKeys.<br>Pomocí Průzkumníka vlastnosti zabezpečení a Upřesnit nastavení zabezpečení v adresáři, resetování oprávnění zpět na výchozí hodnoty, odeberte navíc (než výchozí nastavení) uživatelských objektů z adresáře a ujistěte se, že 'Everyone' měl oprávnění pro zvláštní přístup:<br>-Zobrazovat obsah složky / Číst data <br>– Přečtěte si atributy <br>– Přečtěte si rozšířené atributy <br>-Vytvářet soubory / Zapisovat data <br>-Vytvářet složky / Připojovat data<br>-Zápis atributů<br>-Zapisovat rozšířené atributy<br>-Oprávnění ke čtení<br><br><li>Odstranit všechny certifikáty vydané při s = "Windows Azure Service Management pro rozšíření" nebo "Windows Azure CRP certifikát generátor".<ul><li>[Otevřete konzolu Certifikáty (místní počítač)](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx)<li>Odstranit všechny certifikáty (v části osobní -> certifikáty) s pole "Vydáno pro" = "Windows Azure Service Management pro rozšíření" nebo "Windows Azure CRP certifikát generátor".</ul><li>Spouští se zálohování virtuálních počítačů. </ol>|
| Služba Azure Backup nemá dostatečná oprávnění pro Key Vault pro zálohování šifrované virtuálních počítačů. |Služba zálohování je nutné zadat položek tato oprávnění v prostředí PowerShell pomocí kroků uvedených v **povolit zálohování** části [prostředí PowerShell dokumentaci](backup-azure-vms-automation.md). |
|Snímek instalaci rozšíření se nezdařilo s chybou – modelu COM + se nepodařilo komunikovat Microsoft Distributed Transaction Coordinator | Pokuste se spustit službu systému windows "aplikace modelu COM + systému" (z příkazového řádku se zvýšenými - _net start COMSysApp_). <br>Pokud jsou selhání při spouštění, postupujte podle následujících kroků:<ol><li> Ověřte, že přihlašovací účet služby "Koordinátor distribuovaných transakcí" "Síťová služba". Pokud není, změňte ho na "Síťová služba", restartujte tuto službu a opakujte spuštění služby "aplikace modelu COM + systému". "<li>Pokud ho stále nepodaří spustit, odinstalovat a instalaci služby "Koordinátor distribuovaných transakcí" podle kroků v následujících kroků:<br> -Zastavit služba koordinátoru MS DTC<br> -Otevřete příkazový řádek (cmd) <br> – Spusťte příkaz "msdtc-odinstalovat" <br> – Spusťte příkaz "msdtc-instalaci" <br> -Start služba koordinátoru MS DTC<li>Spustit službu systému windows "aplikace modelu COM + systému" a po jeho spuštění aktivuje zálohování z portálu.</ol> |
|  Snímek operace se nezdařila z důvodu chyby modelu COM + | Doporučená akce, je restart služby systému windows "aplikace modelu COM + systému" (z příkazového řádku se zvýšenými - _net start COMSysApp_). Pokud problém přetrvává, restartujte virtuální počítač. Pokud vám nepomohly restartování virtuálního počítače, zkuste [odebírání rozšíření VMSnapshot](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#cause-3-the-backup-extension-fails-to-update-or-load) a ruční aktivaci zálohování. |
| Freeze – jeden nebo více přípojných bodů virtuálního počítače, který pořízení konzistentního snímku systému souborů se nezdařilo. | Použijte k tomu následující postup: <ol><li>Zkontrolujte stav systému souborů všech připojených zařízení pomocí _'tune2fs'_ příkaz.<br> Např: tune2fs -l/dev/sdb1 \| grep "Filesystem stavu" <li>Odpojte zařízení, pro které filesystem stav není čistá pomocí _'umount'_ příkaz <li> Spustit kontrolu FileSystemConsistency na tato zařízení pomocí _'fsck'_ příkaz <li> Znovu připojte zařízení a vyzkoušejte zálohování.</ol> |
| Snímek operace se nezdařila z důvodu chyby při vytváření zabezpečený komunikační kanál | <ol><Li> Spuštěním regedit.exe v režimu zvýšené otevřete Editor registru. <li> Identifikujte všechny verze. NetFramework v systému existuje. Jsou v hierarchii klíč registru "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft" <li> Pro každou. NetFramework nachází v klíči registru a přidejte následující klíče: <br> "SchUseStrongCrypto"=dword:00000001 </ol>|
| Snímek operace se nezdařila z důvodu selhání při instalaci sady Visual C++ Redistributable pro Visual Studio 2012 | Přejděte na C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion a nainstalujte vcredist2012_x64. Ujistěte se, zda hodnota klíče registru pro povolení této instalace služby nastaveno správnou hodnotu tj hodnota klíče registru _HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver_ nastavená na 3 a 4 není. Pokud se stále setkávají problémy s instalací, restartujte službu instalace spuštěním _MSIEXEC/UNREGISTER_ následuje _MSIEXEC /REGISTER_ z příkazového řádku se zvýšenými oprávněními.  |


## <a name="jobs"></a>Úlohy
| Detaily chyby | Alternativní řešení |
| --- | --- |
| Zrušení není podporován pro tento typ úlohy – počkejte na dokončení úlohy. |Žádný |
| Úloha není ve stavu, možné zrušit – počkejte na dokončení úlohy. <br>NEBO<br> Vybraná úloha není ve stavu, možné zrušit – počkejte na dokončení úlohy. |Ve všech pravděpodobnost téměř dokončení úlohy. Počkejte prosím na dokončení úlohy.|
| Nelze zrušit úlohu, protože není v průběhu – zrušení je podporována pouze pro úlohy, které jsou v průběhu. Prosím pokus o zrušení na v průběhu úlohy. |K tomu dochází z důvodu přechodné stavu. Počkejte několik minut a opakujte operaci zrušit. |
| Nepodařilo se zrušit úlohy – prosím počkejte na dokončení úlohy. |Žádný |

## <a name="restore"></a>Obnovení
| Detaily chyby | Alternativní řešení |
| --- | --- |
| Obnovení se nezdařilo s cloudu vnitřní chyba |<ol><li>Cloudové služby, ke kterému se pokoušíte obnovit je nakonfigurováno nastavení DNS. Můžete zkontrolovat <br>$deployment = get-AzureDeployment - ServiceName "ServiceName"-slotu "Výroba" Get-AzureDns - DnsSettings $deployment. DnsSettings<br>Pokud je nakonfigurovanou adresu, znamená to, zda jsou nakonfigurovány nastavení DNS.<br> <li>Cloudové služby, ke kterému chcete se pokoušíte obnovit je nakonfigurován s vyhrazené IP adresy a stávající virtuální počítače v rámci cloudové služby jsou v zastaveném stavu.<br>Můžete zkontrolovat, že cloudové služby má vyhrazená IP adresa pomocí následující rutiny prostředí powershell:<br>$deployment = get-AzureDeployment - ServiceName "servicename"-slotu $ "Výroba" dep. ReservedIPName <br><li>Pokoušíte se virtuální počítač s následující zvláštní síťové konfigurace v obnovit do stejné cloudové služby. <br>-Virtuální počítače v části Konfigurace služby Vyrovnávání zatížení (interní a externí)<br>-Virtuální počítače s víc vyhrazených IP adres<br>-Virtuální počítače s více síťovými kartami<br>Vyberte novou cloudovou službu v uživatelském rozhraní nebo prostudujte si prosím [obnovit aspekty](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) u virtuálních počítačů s zvláštní síťové konfigurace.</ol> |
| Vybraný název DNS už je zabraný – Zadejte prosím jiný název DNS a zkuste to znovu. |Název DNS odkazuje na název cloudové služby (obvykle konče. cloudapp.net). Toto musí být jedinečný. Pokud dojde k této chybě, budete muset zvolit jiný název virtuálního počítače během obnovení. <br><br> Tato chyba se zobrazí pouze uživatelům na portálu Azure. Operace obnovení pomocí prostředí PowerShell bude úspěšné, protože pouze obnoví disky a nepodporuje vytvoření virtuálního počítače. Chyba se potýkají, když virtuální počítač je explicitně vytvořený můžete po operaci obnovení na disku. |
| Zadaný virtuální síťový konfigurace není správná – zadejte konfiguraci s jinou virtuální síť a zkuste to znovu. |Žádný |
| Zadaná Cloudová služba používá vyhrazenou IP adresu, která nebude odpovídat konfiguraci virtuálního počítače, který se má obnovit – zadejte jinou cloudovou službu, která není pomocí vyhrazené IP adresy nebo zvolte jiný bod obnovení pro obnovení z. |Žádný |
| Cloudové služby bylo dosaženo limitu počtu vstupní koncové body – zkuste operaci zopakovat zadáním jiné cloudové služby nebo pomocí existující koncový bod. |Žádný |
| Zálohování úložiště a cílový účet úložiště jsou ve dvou různých oblastech – zajistěte, aby byl účet úložiště zadaný v operaci obnovení ve stejné oblasti Azure jako úložiště záloh. |Žádný |
| Účet úložiště, zadaný pro operaci obnovení není podporována – účty úložiště pouze Basic nebo Standard s místně redundantní nebo geograficky redundantní replikaci nastavení nejsou podporovány. Vyberte prosím účet podporované úložiště |Žádný |
| Typ zadaný pro operaci obnovení účtu úložiště není online – Ujistěte se, že je účet úložiště zadaný v operaci obnovení online |K tomu může dojít z důvodu přechodné chyby ve službě Azure Storage nebo kvůli výpadku. Zvolte prosím jiný účet úložiště. |
| Bylo dosaženo kvóty skupina prostředků – prosím odstraňte některé skupiny prostředků pomocí portálu Azure, nebo požádejte podporu Azure o navýšení limitů. |Žádný |
| Vybraná podsíť neexistuje – vyberte podsíť, která již existuje |Žádný |
| Služba zálohování nemá oprávnění pro přístup k prostředkům ve vašem předplatném. |Tento problém vyřešíte tak, první obnovení disků pomocí kroků uvedených v části **obnovení zálohovaných disky** v [konfigurace obnovení výběru virtuálního počítače](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Potom pomocí prostředí PowerShell kroků uvedených v [vytvoření virtuálního počítače z obnovené disků](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) pro vytvoření úplné virtuálního počítače z obnovené disků. |

## <a name="backup-or-restore-taking-time"></a>Zálohování nebo obnovení doba
Pokud se zobrazí vaše backup(>12 hours) nebo obnovení trvá time(>6 hours):
* Pochopení [faktory, které přispívají k čas zálohování](backup-azure-vms-introduction.md#total-vm-backup-time) a [faktory, které přispívají k obnovení čas](backup-azure-vms-introduction.md#total-restore-time).
* Ujistěte se, že [zálohování osvědčené postupy](backup-azure-vms-introduction.md#best-practices). 

## <a name="vm-agent"></a>Agent virtuálního počítače
### <a name="setting-up-the-vm-agent"></a>Nastavení agenta virtuálního počítače
Obvykle agenta virtuálního počítače již existuje ve virtuálních počítačích, které jsou vytvořené z Galerie Azure. Virtuální počítače, které jsou migrované z místních datových center však nebude mít Agent virtuálního počítače nainstalovaný. Pro tyto virtuální počítače musí být explicitně nainstalovaný Agent virtuálního počítače.

Pro virtuální počítače Windows:

* Stáhněte si a nainstalujte [MSI agenta](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). K dokončení instalace je potřeba oprávnění správce.
* Klasické virtuální počítače [aktualizujte vlastnost virtuálního počítače](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) k označení, že je nainstalován agent. Tento krok není požadovaná pro virtuální počítače správce prostředků.

Pro virtuální počítače Linux:

* Nainstalujte nejnovější z distribuční úložiště. Jsme **důrazně doporučujeme** instalaci agenta pouze prostřednictvím distribuční úložiště. Podrobnosti o název balíčku, naleznete v [Linux agent úložiště](https://github.com/Azure/WALinuxAgent) 
* Pro klasické virtuální počítače [aktualizujte vlastnost virtuálního počítače](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) k označení, že je nainstalován agent. Tento krok není požadovaná pro virtuální počítače správce prostředků.

### <a name="updating-the-vm-agent"></a>Aktualizace agenta virtuálního počítače
Pro virtuální počítače Windows:

* Aktualizace agenta virtuálního počítače je stejně jednoduchá, jako přeinstalace [binárních souborů agenta virtuálního počítače](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Ale je potřeba zajistit, že během aktualizace agenta virtuálního počítače neběží žádná operace zálohování.

Pro virtuální počítače Linux:

* Postupujte podle pokynů [aktualizace agenta virtuálního počítače Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
Jsme **důrazně doporučujeme** aktualizací agenta pouze prostřednictvím distribuční úložiště. Nedoporučujeme stáhne kód agenta z githubu přímo a jeho aktualizaci. Pokud není k dispozici pro distribuční nejnovější verze agenta, kontaktujte podporu distribuční pokyny o tom, jak nainstalovat nejnovější verzi agenta. Můžete zkontrolovat nejnovější [Windows Azure Linux agent](https://github.com/Azure/WALinuxAgent/releases) informace v úložišti github.

### <a name="validating-vm-agent-installation"></a>Ověření instalace agenta virtuálního počítače
Jak zjistit, jestli verze agenta virtuálního počítače na virtuálních počítačích Windows:

1. Přihlaste se k virtuálnímu počítači Azure a přejděte do složky *C:\WindowsAzure\Packages*. Měl by být přítomný soubor WaAppAgent.exe.
2. Pravým tlačítkem myši klikněte na soubor, přejděte na **Vlastnosti** a poté vyberte kartu **Podrobnosti**. Pole verze produktu by mělo být 2.6.1198.718 nebo vyšší

## <a name="troubleshoot-vm-snapshot-issues"></a>Řešení problémů snímek virtuálního počítače
Zálohování virtuálních počítačů spoléhá na vystavení snímku příkazy pro základní úložiště. Nemá přístup k úložišti nebo zpoždění při provádění úloh snímku může způsobit selhání úlohy zálohování. Následující může způsobit selhání úkolů snímku.

1. Přístup k síti do úložiště je blokován pomocí NSG<br>
    Další informace o tom, jak [povolit přístup k síti](backup-azure-arm-vms-prepare.md#establish-network-connectivity) do úložiště pomocí buď povolených IP nebo prostřednictvím proxy serveru.
2. Virtuální počítače pomocí zálohování serveru Sql Server nakonfigurován může způsobit zpoždění úloha snímku <br>
   Ve výchozím nastavení virtuálního počítače zálohování problémů služby Stínová kopie svazku úplné zálohování na virtuální počítače Windows. Na virtuálních počítačích se systémem Sql Server a jestli je nakonfigurovaná zálohování serveru Sql Server to může způsobit zpoždění při provádění snímku. Nastavte následující klíč registru, pokud dochází k chybám zálohování z důvodu problémů s snímku.

   ```
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```
3. Stav virtuálního počítače se nesprávně uvést, protože virtuální počítač je vypnutý v protokolu RDP.  <br>
   Pokud vypnete virtuální počítač v protokolu RDP, Zkontrolujte prosím zpět na portálu, stav virtuálního počítače je správně zobrazit. Pokud tomu tak není, vypněte virtuální počítač v portálu pomocí možnosti "Vypnout" na řídicím panelu virtuálních počítačů.
4. Pokud více než čtyři virtuální počítače sdílet stejné cloudové služby, nakonfigurujte více zásady zálohování k vytvoření dvoufázové zálohování časy proto žádné, více než čtyři zálohování virtuálních počítačů, které jsou spuštěny ve stejnou dobu. Pokuste se šíří doby hodinu od sebe mezi zásadami zálohování spuštění.
5. Virtuální počítač spuštěný na vysoké využití procesoru nebo paměti.<br>
   Pokud virtuální počítač běží na vysoké využití procesoru usage(>90%) nebo paměť, je snímek úloh zařazených do fronty, zpoždění a bude nakonec získá vypršení časového limitu. Opakujte zálohování na vyžádání v takových situacích.

<br>

## <a name="networking"></a>Sítě
Jako všechna rozšíření rozšíření zálohování potřebovat přístup k veřejnému Internetu pro práci. Nemá přístup k veřejnému Internetu může projevit různými způsoby:

* Instalace rozšíření může selhat.
* Operace zálohování (např. disku snímek) může selhat.
* Zobrazení stavu operace zálohování může selhat.

Potřebu řešení veřejné internetové adresy byla kloubové. [zde](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Budete muset zkontrolovat konfiguraci DNS pro virtuální sítě a ujistěte se, že identifikátory URI Azure lze vyřešit.

Jakmile se provádí překlad IP adresy, přístup k IP adres Azure také je třeba zadat. Chcete-li odblokovat přístup k infrastruktuře Azure, postupujte podle jeden z těchto kroků:

1. Seznam povolených adres Azure datacenter rozsahy IP adres.
   * Získání seznamu [IP adres Azure datacenter](https://www.microsoft.com/download/details.aspx?id=41653) jako seznam povolených adres.
   * Odblokování pomocí IP adresy [New-NetRoute](https://technet.microsoft.com/library/hh826148.aspx) rutiny. Tato rutina v rámci virtuálního počítače Azure, spusťte v okně prostředí PowerShell zvýšenými oprávněními (Spustit jako správce).
   * K této skupině přidáte pravidla, (Pokud nemáte na místě) pro povolení přístupu k IP adresy.
2. Vytvoření cesty pro tok provozu HTTP
   * Pokud máte nějaké omezení sítě na místě (skupina zabezpečení sítě, například) nasadit proxy server HTTP směrovat provoz. Kroky k nasazení serveru Proxy protokolu HTTP lze nalézt [zde](backup-azure-arm-vms-prepare.md#establish-network-connectivity).
   * K této skupině přidáte pravidla, (Pokud nemáte na místě) povolit přístup k Internetu z proxy serveru HTTP.

> [!NOTE]
> DHCP musí být povolen v hosta pro zálohování virtuálních počítačů IaaS pracovat.  Pokud potřebujete statickou privátní IP adresu, byste ho měli nakonfigurovat prostřednictvím platformy. Možnost DHCP ve virtuálním počítači by měly být vlevo povoleny.
> Zobrazit další informace o [nastavení statickou privátní IP interní](../virtual-network/virtual-networks-reserved-private-ip.md).
>
>
