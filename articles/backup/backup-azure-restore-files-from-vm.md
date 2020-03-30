---
title: Obnovení souborů a složek ze zálohy virtuálních počítačů Azure
description: V tomto článku se dozvíte, jak obnovit soubory a složky z bodu obnovení virtuálního počítače Azure.
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 0e3061ea8fc26adcf39fe415cd9a662de739543a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273303"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Obnovení souborů ze zálohy virtuálního počítače Azure

Azure Backup poskytuje možnost obnovení [virtuálních počítačů Azure (VM) a disků](./backup-azure-arm-restore-vms.md) ze záloh virtuálních počítačů Azure, označované také jako body obnovení. Tento článek vysvětluje, jak obnovit soubory a složky ze zálohy virtuálního počítače Azure. Obnovení souborů a složek je k dispozici jenom pro virtuální počítače Azure nasazené pomocí modelu Resource Manager a chráněné do trezoru služeb obnovení.

> [!NOTE]
> Tato funkce je dostupná pro virtuální počítače Azure nasazené pomocí modelu Resource Manager a chráněné do trezoru služby Recovery Services.
> Obnovení souboru ze šifrované zálohy virtuálního počítače není podporováno.
>

## <a name="mount-the-volume-and-copy-files"></a>Připojení souborů svazku a kopírování

Chcete-li obnovit soubory nebo složky z bodu obnovení, přejděte na virtuální počítač a zvolte požadovaný bod obnovení.

1. Přihlaste se k [portálu Azure](https://portal.Azure.com) a v levém podokně klikněte na **Virtuální počítače**. Ze seznamu virtuálních počítačů vyberte virtuální počítač a otevřete řídicí panel tohoto virtuálního počítače.

2. V nabídce virtuálního počítače kliknutím na **Zálohovat** otevřete řídicí panel Zálohování.

    ![Otevřít položku zálohy trezoru služby Recovery Services](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. V nabídce Řídicí panel zálohy klepněte na **položku Obnovení souboru**.

    ![Tlačítko pro obnovení souboru](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    Otevře se nabídka **Obnovení souborů.**

    ![Nabídka obnovení souborů](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. V rozevírací nabídce **Vybrat bod obnovení** vyberte bod obnovení, který obsahuje požadované soubory. Ve výchozím nastavení je již vybrán nejnovější bod obnovení.

5. Pokud chcete stáhnout software použitý ke kopírování souborů z bodu obnovení, klikněte na **Stáhnout spustitelný soubor** (pro virtuální počítače Windows Azure) nebo **Stáhnout skript** (pro virtuální počítače Linux Azure se vygeneruje skript pythonu).

    ![Vygenerované heslo](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure stáhne spustitelný soubor nebo skript do místního počítače.

    ![stáhnout zprávu pro spustitelný soubor nebo skript](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Chcete-li spustit elný soubor nebo skript jako správce, doporučujeme uložit stažený soubor do počítače.

6. Spustitelný soubor nebo skript je chráněn heslem a vyžaduje heslo. V nabídce **Obnovení souboru** klepněte na tlačítko kopírovat a načtěte heslo do paměti.

    ![Vygenerované heslo](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. Z umístění pro stahování (obvykle ve složce Soubory ke stažení) klepněte pravým tlačítkem myši na spustitelný soubor nebo skript a spusťte jej s pověřeními správce. Po zobrazení výzvy zadejte heslo nebo vložte heslo z paměti a stiskněte **enter**. Po zadání platného hesla se skript připojí k bodu obnovení.

    ![Nabídka obnovení souborů](./media/backup-azure-restore-files-from-vm/executable-output.png)

8. Pro linuxové počítače je generován skript pythonu. Je třeba stáhnout skript a zkopírovat jej na příslušný / kompatibilní server Linux. Bude pravděpodobně nutné upravit oprávnění k ```chmod +x <python file name>```jeho spuštění pomocí . Potom spusťte ```./<python file name>```soubor pythonu s .

V části [Požadavky aplikace Access](#access-requirements) zkontrolujte, zda je skript úspěšně spuštěn.

### <a name="identifying-volumes"></a>Identifikace svazků

#### <a name="for-windows"></a>Pro Windows

Při spuštění spustitelného souboru operační systém připojí nové svazky a přiřadí písmena jednotek. Tyto jednotky můžete procházet pomocí Průzkumníka Windows nebo Průzkumníka souborů. Písmena jednotek přiřazená svazkům nemusí být stejná jako původní virtuální počítač. Název svazku je však zachován. Pokud byl například svazek na původním virtuálním počítači "Datový disk (E:`\`), lze tento svazek připojit`\`k místnímu počítači jako "Datový disk ("Libovolné písmeno": ). Procházejte všechny svazky uvedené ve výstupu skriptu, dokud nenajdete soubory nebo složku.  

   ![Nabídka obnovení souborů](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>Pro Linux

V Linuxu jsou svazky bodu obnovení připojeny do složky, ve které je skript spuštěn. Připojené disky, svazky a odpovídající cesty připojení jsou zobrazeny odpovídajícím způsobem. Tyto cesty připojení jsou viditelné pro uživatele, kteří mají přístup na kořenové úrovni. Projděte si svazky uvedené ve výstupu skriptu.

  ![Nabídka obnovení souboru Linuxu](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>Ukončení připojení

Po identifikaci souborů a jejich zkopírování do místního umístění úložiště odeberte (nebo odpojte) další jednotky. Pokud chcete jednotky odpojit, klikněte v nabídce **Obnovení souborů** na portálu Azure na **odpojit disky**.

![Odpojit disky](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Po odpojení disků se zobrazí zpráva. Může trvat několik minut, než se připojení obnoví, takže můžete disky odebrat.

V Linuxu po odpojení připojení k bodu obnovení operační systém automaticky neodebere odpovídající cesty připojení. Cesty připojení existují jako "osamocené" svazky a jsou viditelné, ale při přístupu nebo zápisu k souborům vyvolá chybu. Mohou být ručně odstraněny. Skript při spuštění identifikuje všechny takové svazky existující z předchozích bodů obnovení a vyčistí je na základě souhlasu.

## <a name="special-configurations"></a>Speciální konfigurace

### <a name="dynamic-disks"></a>Dynamické disky

Pokud chráněný virtuální počítač Azure má svazky s jednou nebo oběma následujícími charakteristikami, nelze spustit spustitelný skript na stejném virtuálním počítači.

- Svazky, které pokrývají více disků (rozložené a prokládané svazky)
- Svazky odolné proti chybám (zrcadlené a RAID-5 svazky) na dynamických discích

Místo toho spusťte spustitelný skript v jakémkoli jiném počítači s kompatibilním operačním systémem.

### <a name="windows-storage-spaces"></a>Prostory úložiště ve Windows

Prostory úložiště systému Windows je technologie systému Windows, která umožňuje virtualizovat úložiště. S prostory úložiště Windows můžete seskupit standardní disky do fondů úložiště. Pak můžete použít dostupné místo v těchto fondech úložiště k vytvoření virtuálních disků, nazývaných prostory úložiště.

Pokud chráněný virtuální počítač Azure používá prostory úložiště Windows, nelze spustit spustitelný skript na stejném virtuálním počítači. Místo toho spusťte spustitelný skript v jakémkoli jiném počítači s kompatibilním operačním systémem.

### <a name="lvmraid-arrays"></a>Pole LVM/RAID

V Linuxu se ke správě logických svazků (LVM) a softwarových polí RAID používá správa logických svazků na více discích. Pokud chráněný virtuální počítač s Linuxem používá LVM nebo RAID Arrays, nelze spustit skript na stejném virtuálním počítači. Místo toho spusťte skript na jakémkoli jiném počítači s kompatibilním os a který podporuje systém souborů chráněného virtuálního počítače.

Následující výstup skriptu zobrazuje disky LVM a/nebo RAID Arrays a svazky s typem oddílu.

   ![Linux LVM Výstupní nabídka](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Chcete-li tyto oddíly převést do režimu online, spusťte příkazy v následujících částech.

#### <a name="for-lvm-partitions"></a>Pro oddíly LVM

Chcete-li uvést názvy skupin svazků pod fyzickým svazkem:

```bash
#!/bin/bash
pvs <volume name as shown above in the script output>
```

Chcete-li vypsat všechny logické svazky, názvy a jejich cesty ve skupině svazků:

```bash
#!/bin/bash
lvdisplay <volume-group-name from the pvs command's results>
```

Chcete-li připojit logické svazky na cestu podle vašeho výběru:

```bash
#!/bin/bash
mount <LV path> </mountpath>
```

#### <a name="for-raid-arrays"></a>Pro pole RAID

Následující příkaz zobrazuje podrobnosti o všech raidových discích:

```bash
#!/bin/bash
mdadm –detail –scan
```

 Příslušný disk RAID je zobrazen jako`/dev/mdm/<RAID array name in the protected VM>`

Pokud má disk RAID fyzické svazky, použijte příkaz připojit:

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

Pokud je na disku RAID nakonfigurovánjiný lvm, použijte předchozí postup pro oddíly LVM, ale místo názvu disku RAID použijte název svazku.

## <a name="system-requirements"></a>Požadavky na systém

### <a name="for-windows-os"></a>Pro operační systém Windows

V následující tabulce je uvedena kompatibilita mezi operačními systémy serveru a počítače. Při obnovování souborů nelze obnovit soubory na předchozí nebo budoucí verzi operačního systému. Nemůžete například obnovit soubor z virtuálního počítače se systémem Windows Server 2016 do počítače se systémem Windows Server 2012 nebo Windows 8. Soubory z virtuálního počítači do stejného operačního systému serveru nebo do kompatibilního klientského operačního systému můžete obnovit.

|Operační systém serveru | Kompatibilní klientský operační systém  |
| --------------- | ---- |
| Windows Server 2019    | Windows 10 |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>Pro operační systém Linux

V Linuxu musí operační systém počítače používaného k obnovení souborů podporovat systém souborů chráněného virtuálního počítače. Při výběru počítače ke spuštění skriptu se ujistěte, že má počítač kompatibilní operační systém a používá jednu z verzí uvedených v následující tabulce:

|Operační systém Linux | Verze  |
| --------------- | ---- |
| Ubuntu | 12.04 a více |
| CentOS | 6.5 a více  |
| RHEL | 6.7 a více |
| Debian | 7 a vyšší |
| Oracle Linux | 6.4 a vyšší |
| SLES | 12 a více |
| openSUSE | 42.2 a více |

> [!NOTE]
> Našli jsme nějaké problémy při spuštění skriptu pro obnovu souborů na počítačích s operačním systémem SLES 12 SP4 a vyšetřujeme s týmem SLES.
> V současné době běží skript pro obnovení souborů pracuje na počítačích s Verzemi operačního systému SLES 12 SP2 a SP3.
>

Skript také vyžaduje, aby se komponenty Pythonu a bash spouštěly a bezpečně připojovaly k bodu obnovení.

|Komponenta | Version  |
| --------------- | ---- |
| bash | 4 a vyšší |
| python | 2.6.6 a vyšší  |
| TLS | 1.2 by měla být podporována  |

## <a name="access-requirements"></a>Požadavky na přístup

Pokud spustíte skript v počítači s omezeným přístupem, ujistěte se, že je k dispozici:

- `download.microsoft.com`
- Adresy URL služby obnovení (geografický název odkazuje na oblast, kde se nachází trezor služby obnovení)
  - `https://pod01-rec2.geo-name.backup.windowsazure.com`(Pro veřejné geos Azure)
  - `https://pod01-rec2.geo-name.backup.windowsazure.cn`(Pro Azure China 21Vianet)
  - `https://pod01-rec2.geo-name.backup.windowsazure.us`(Pro Azure us government)
  - `https://pod01-rec2.geo-name.backup.windowsazure.de`(Pro Azure Německo)
- Odchozí porty 53 (DNS), 443, 3260

> [!NOTE]
>
> - Název staženého souboru skriptu bude mít **geonázev,** který má být vyplněn v adrese URL. Pro \'exampple: Stažený název skriptu začíná\'\_\'vmname geoname\'_\'GUID\', jako *ContosoVM_wcus_12345678*
> - Adresa URL <https://pod01-rec2.wcus.backup.windowsazure.com>by byla "
>

Pro Linux skript vyžaduje komponenty "open-iscsi" a 'lshw' pro připojení k bodu obnovení. Pokud součásti v počítači, ve kterém je skript spuštěn, neexistují, skript požádá o oprávnění k instalaci součástí. Uveďte souhlas s instalací nezbytných součástí.

Přístup ke `download.microsoft.com` stažení součásti používané k vytvoření zabezpečeného kanálu mezi počítačem, kde je skript spuštěn, a daty v bodě obnovení.

Skript můžete spustit na libovolném počítači, který má stejný (nebo kompatibilní) operační systém jako zálohovaný virtuální počítač. Kompatibilní operační systémy najdete v [tabulce Kompatibilní operační](backup-azure-restore-files-from-vm.md#system-requirements) systém. Pokud chráněný virtuální počítač Azure používá prostory úložiště Windows (pro virtuální počítače Windows Azure) nebo lvm/RAID pole (pro virtuální počítače s Linuxem), nelze spustit spustitelný soubor nebo skript na stejném virtuálním počítači. Místo toho spusťte spustitelný soubor nebo skript v jiném počítači s kompatibilním operačním systémem.

## <a name="file-recovery-from-virtual-machine-backups-having-large-disks"></a>Obnovení souborů ze záloh virtuálních počítačů s velkými disky

Tato část vysvětluje, jak provést obnovení souborů ze záloh virtuálních počítačů Azure s více než 16 disků a každá velikost disku je větší než 32 TB.

Vzhledem k tomu, že proces obnovení souborů připojuje všechny disky ze zálohy, pokud se používá velký počet disků (>16) nebo velké disky (> 32 TB každý), doporučujeme následující body akce:

- Uchovávejte samostatný server pro obnovení (virtuální počítače Azure D2v3) pro obnovení souborů. Můžete jej použít pouze pro obnovení souborů a poté jej vypnout, pokud není vyžadován. Obnovení na původním počítači se nedoporučuje, protože to bude mít významný dopad na samotný virtuální počítač.
- Potom spusťte skript jednou a zkontrolujte, zda je operace obnovení souboru úspěšná.
- Pokud proces obnovení souboru přestane reagovat (disky nejsou nikdy připojeny nebo jsou připojeny, ale svazky se nezobrazí), proveďte následující kroky.
  - Pokud je server pro obnovení virtuálním virtuálním montovním virtuálním montovnam systému Windows:
    - Ujistěte se, že operační spoje je WS 2012 nebo vyšší.
    - Ujistěte se, že klíče registru jsou nastaveny tak, jak je navrženo níže na serveru pro obnovení a ujistěte se, že restartujte server. Číslo vedle identifikátoru GUID může být v rozsahu od 0001 do 0005. V následujícím příkladu je 0004. Procházejte cestou klíče registru až do oddílu parametry.

    ![iscsi-reg-key-changes.png](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

- Pokud je server pro obnovení virtuálním počítačem s Linuxem:
  - V souboru /etc/iscsi/iscsid.conf změňte nastavení z:
    - node.conn[0].timeo.noop_out_timeout = 5 to node.conn[0].timeo.noop_out_timeout = 30
- Po provedení výše uvedené změny spusťte skript znovu. S těmito změnami je vysoce pravděpodobné, že obnovení souboru bude úspěšné.
- Pokaždé, když uživatel stáhne skript, Azure Backup zahájí proces přípravy bodu obnovení ke stažení. U velkých disků bude tento proces trvat značnou dobu. Pokud dojde k následným výbuchům požadavků, příprava cíle se dostane do spirály stahování. Proto se doporučuje stáhnout skript z portálu / Powershell / CLI, počkejte 20-30 minut (heuristická) a pak ji spustit. Do této doby se očekává, že cíl bude připraven pro připojení ze skriptu.
- Po obnovení souboru se ujistěte, že se vrátíte na portál a klikněte na **Odpojit disky** pro body obnovení, kde jste nemohli připojit svazky. V podstatě tento krok vyčistí všechny existující procesy nebo relace a zvýší pravděpodobnost obnovení.

## <a name="troubleshooting"></a>Řešení potíží

Pokud máte problémy při obnově souborů z virtuálních počítačů, další informace naleznete v následující tabulce.

| Chybová zpráva / scénář | Pravděpodobná příčina | Doporučená akce |
| ------------------------ | -------------- | ------------------ |
| Výstup exe: *Výjimka zachycena při připojování k cíli* | Skript nemá přístup k bodu obnovení    | Zkontrolujte, zda stroj splňuje [předchozí požadavky na přístup](#access-requirements). |  
| Výstup exe: *Cíl již byl přihlášen prostřednictvím relace iSCSI.* | Skript byl již spuštěn ve stejném počítači a jednotky byly připojeny | Objemy bodu obnovení již byly připojeny. Nemusí být připojeny se stejnými písmeny jednotky původního virtuálního zařízení. Projděte si všechny dostupné svazky v průzkumníku souborů pro soubor. |
| Výstup exe: *Tento skript je neplatný, protože disky byly odpojeny prostřednictvím portálu/překročillimit 12 hodin. Stáhněte si nový skript z portálu.* |    Disky byly odpojeny z portálu nebo byl překročen 12hodinový limit. | Tento konkrétní exe je nyní neplatný a nelze jej spustit. Pokud chcete získat přístup k souborům tohoto bodu obnovení v čase, navštivte portál pro nový exe.|
| Na počítači, kde je spuštěn exe: Nové svazky nejsou odpojeny po kliknutí na tlačítko pro odpojení | Iniciátor iSCSI v počítači nereaguje nebo neaktualizuje jeho připojení k cíli a udržuje mezipaměť. |  Po klepnutí na **tlačítko Sesednout**počkejte několik minut. Pokud nové svazky nejsou odpojeny, projděte si všechny svazky. Procházení všech svazků vynutí iniciátoru aktualizovat připojení a svazek je odpojen s chybovou zprávou, že disk není k dispozici.|
| Výstup exe: Skript je úspěšně spuštěn, ale na výstupu skriptu se nezobrazí "Nové připojené svazky". |    Jedná se o přechodnou chybu.    | Svazky již byly připojeny. Otevřete Průzkumníka a procházejte. Pokud používáte stejný počítač pro spouštění skriptů pokaždé, zvažte restartování počítače a seznam by měl být zobrazen v následné mj. |
| Linux specifické: Není schopen zobrazit požadované svazky | Operační systém počítače, ve kterém je skript spuštěn, nemusí rozpoznat základní souborový systém chráněného virtuálního počítače. | Zkontrolujte, zda bod obnovení je konzistentní selhání nebo konzistentní soubor. Pokud konzistentní se soubory, spusťte skript na jiném počítači, jehož operační systém rozpozná souborový systém chráněného virtuálního počítače. |
| Windows specifické: Nelze zobrazit požadované svazky | Disky byly pravděpodobně připojeny, ale svazky nebyly nakonfigurovány. | Na obrazovce správy disku identifikujte další disky související s bodem obnovení. Pokud jsou některé z těchto disků v režimu offline, zkuste je převést do režimu online kliknutím pravým tlačítkem myši na disk a klepněte na příkaz **Online**.|

## <a name="security"></a>Zabezpečení

Tato část popisuje různá bezpečnostní opatření přijatá pro implementaci obnovení souborů ze záloh virtuálních počítačů Azure.

### <a name="feature-flow"></a>Tok funkcí

Tato funkce byla vytvořena pro přístup k datům virtuálního počítače bez nutnosti obnovovat celý virtuální počítače nebo virtuální počítače disky a s minimálním počtem kroků. Přístup k datům virtuálního zařízení poskytuje skript (který při spuštění, jak je znázorněno níže, připojí svazek obnovení a tvoří základní kámen všech implementací zabezpečení:

  ![Tok bezpečnostních funkcí](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Implementace zabezpečení

#### <a name="select-recovery-point-who-can-generate-script"></a>Vyberte bod obnovení (kdo může generovat skript)

Skript poskytuje přístup k datům virtuálního soudu, takže je důležité regulovat, kdo je může generovat na prvním místě. Musíte se přihlásit k portálu Azure a být [RBAC oprávnění](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) ke generování skriptu.

Obnovení souborů vyžaduje stejnou úroveň autorizace, jakou je požadováno pro obnovení virtuálního počítače a obnovení disků. Jinými slovy, skript můžou generovat jenom oprávnění uživatelé.

Vygenerovaný skript je podepsán oficiálním certifikátem Microsoftu pro službu Azure Backup. Jakékoli manipulace se skriptem znamená, že podpis je přerušený a jakýkoli pokus o spuštění skriptu je zvýrazněn jako potenciální riziko operačního es.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Svazek obnovení připojení (kdo může spustit skript)

Skript může spustit pouze správce a měl by být spuštěn ve zvýšeném režimu. Skript spustí pouze předem vygenerovanou sadu kroků a nepřijímá vstup z žádného externího zdroje.

Chcete-li spustit skript, je vyžadováno heslo, které se zobrazí pouze oprávněnému uživateli v době generování skriptu na webu Azure Portal nebo PowerShell/CLI. Tím je zajištěno, že oprávněný uživatel, který stáhne skript je také zodpovědný za spuštění skriptu.

#### <a name="browse-files-and-folders"></a>Procházení souborů a složek

Chcete-li procházet soubory a složky, skript používá iniciátor iSCSI v počítači a připojuje se k bodu obnovení, který je nakonfigurován jako cíl iSCSI. Zde si můžete představit scénáře, kde se člověk snaží napodobit / spoof buď / všechny komponenty.

Používáme mechanismus vzájemného ověřování CHAP tak, aby každá komponenta ověřuje druhou. To znamená, že je velmi obtížné pro falešný iniciátor připojit k cíli iSCSI a falešný cíl, který má být připojen k počítači, kde je skript spuštěn.

Tok dat mezi službou pro obnovení a počítačem je chráněn vytvořením zabezpečeného tunelu TLS přes TCP ([TLS 1.2 by měl být podporován](#system-requirements) v počítači, ve kterém je spuštěn skript).

Všechny seznamy řízení přístupu (ACL) souborů v nadřazeném/zálohovaném virtuálním virtuálním zařízení jsou zachovány také v připojeném systému souborů.

Skript poskytuje přístup jen pro čtení k bodu obnovení a je platný pouze 12 hodin. Pokud chcete odebrat přístup dříve, pak se přihlaste k Azure Portal/PowerShell/CLI a proveďte **odpojit disky** pro tento konkrétní bod obnovení. Skript bude okamžitě zrušen.

## <a name="next-steps"></a>Další kroky

- Informace o problémech při obnově souborů naleznete v části [Poradce při potížích](#troubleshooting)
- Přečtěte si, jak [obnovit soubory přes Powershell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#restore-files-from-an-azure-vm-backup)
- Zjistěte, jak [obnovit soubory pomocí azure CLI](https://docs.microsoft.com/azure/backup/tutorial-restore-files)
- Po obnovení virtuálního počítače se dozvíte, jak [spravovat zálohy](https://docs.microsoft.com/azure/backup/backup-azure-manage-vms)
