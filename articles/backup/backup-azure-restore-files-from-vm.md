---
title: 'Zálohování Azure: Obnovovat soubory a složky ze zálohy virtuálního počítače Azure'
description: Obnovit soubory z bodu obnovení virtuálního počítače Azure
services: backup
author: pvrk
manager: shivamg
keywords: obnovení na úrovni položek; obnovení souborů ze zálohy virtuálního počítače Azure; Obnovit soubory z virtuálního počítače Azure
ms.service: backup
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: pullabhk
ms.openlocfilehash: 4be1ffcabed6667ab76ec790326a687d75c8b125
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2018
ms.locfileid: "36958616"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Obnovit soubory ze zálohy virtuálního počítače Azure

Azure Backup poskytuje možnosti obnovení [Azure virtuální počítače (VM) a disky](./backup-azure-arm-restore-vms.md) ze záloh virtuálních počítačů Azure, také známé jako body obnovení. Tento článek vysvětluje, jak obnovit soubory a složky ze zálohy virtuálního počítače Azure. Obnovení souborů a složek je k dispozici pouze pro virtuální počítače Azure nasazení pomocí modelu Resource Manager a chránit do trezoru služeb zotavení.

> [!Note]
> Tato funkce je k dispozici pro virtuální počítače Azure nasazení pomocí modelu Resource Manager a chránit do trezoru služeb zotavení.
> Obnovení souborů ze šifrovaných zálohy virtuálního počítače není podporováno.
>

## <a name="mount-the-volume-and-copy-files"></a>Připojte svazek a zkopírujte soubory

Pokud chcete obnovit soubory nebo složky z bodu obnovení, přejděte k virtuálnímu počítači a zvolte bod obnovení požadované. 

1. Přihlaste se k [portál Azure](http://portal.Azure.com) a nabídky na levé straně klikněte na **virtuální počítače**. Seznam virtuálních počítačů vyberte virtuální počítač a otevřete řídicí panel virtuálního počítače. 

2. V nabídce virtuálního počítače, klikněte na tlačítko **zálohování** Otevřete řídící panel zálohování.

    ![Otevřete položky zálohování trezoru služeb zotavení](./media/backup-azure-restore-files-from-vm/open-vault-from-vm.png)

3. V nabídce řídícího panelu zálohování, klikněte na **obnovení souboru** otevřete nabídku.

    ![Nabídka soubor obnovení](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. Z **vyberte bod obnovení** rozevírací nabídky vyberte bod obnovení, který obsahuje soubory, které chcete. Ve výchozím nastavení je již vybrán poslední bod obnovení.

5. Chcete-li stáhnout software používaný ke kopírování souborů z bodu obnovení, klikněte na tlačítko **spustitelný soubor stáhnout** (pro virtuální počítač Windows Azure) nebo **stáhnout skript** (pro virtuální počítač Azure s Linuxem). 

    ![Vygenerované heslo](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure stáhne spustitelný soubor nebo skript k místnímu počítači.

    ![Stáhnout zprávu pro spustitelný soubor nebo skript](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Pokud chcete spustit spustitelný soubor nebo skript jako správce, doporučuje se, že Uložte stažený soubor do počítače.

6. Spustitelný soubor nebo skript je chráněná heslem a vyžaduje heslo. V **obnovení souboru** nabídky, klikněte na tlačítko Kopírovat, pokud chcete načíst heslo do paměti.

    ![Vygenerované heslo](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. Z umístění stahování (obvykle složka souborů ke stažení) klikněte pravým tlačítkem na spustitelný soubor nebo skript a spusťte ji pomocí přihlašovacích údajů správce. Po zobrazení výzvy zadejte heslo nebo vložte heslo z paměti a stiskněte klávesu Enter. Jakmile je zadáno platné heslo, skript se spojí s bodem obnovení.

    ![Nabídka soubor obnovení](./media/backup-azure-restore-files-from-vm/executable-output.png)

    Pokud spustíte skript na počítači s omezeným přístupem, ujistěte se, je přístup k:

    - download.microsoft.com
    - [Koncové body Azure použít pro zálohování virtuálních počítačů Azure](backup-azure-arm-vms-prepare.md#establish-network-connectivity)
    - odchozí port 3260

    Pro systémy Linux vyžaduje skript 'open-iscsi' a 'lshw' součásti pro připojení k bodu obnovení. Pokud komponenty neexistují v počítači, kde je skript spuštěn, skript požádá o oprávnění k instalaci součásti. Zadejte souhlasu nainstalujte nezbytné součásti.
    
    Přístup k download.microsoft.com je nutný ke stažení součásti sloužící k vytvoření zabezpečeného kanálu mezi počítači, kde se spouští skript a data v bodu obnovení.         

    Tento skript můžete spustit na jakýkoli počítač, který má operační systém stejného (nebo kompatibilní) jako zálohované virtuální počítač. Najdete v článku [kompatibilní operační systém tabulky](backup-azure-restore-files-from-vm.md#system-requirements) pro kompatibilní operační systémy. Pokud chráněný virtuální počítač Azure používá prostory úložiště ve Windows (pro virtuální počítače Windows Azure) nebo pole LVM/RAID (pro virtuální počítače s Linuxem), nebudete moci spustit spustitelný soubor nebo skript na jednom virtuálním počítači. Místo toho spusťte spustitelný soubor nebo skript z jakéhokoli počítače s kompatibilní operační systém.
 

### <a name="identifying-volumes"></a>Identifikace svazky

#### <a name="for-windows"></a>Pro Windows

Při spuštění spustitelného souboru připojí nové svazky operačního systému a přiřadí písmena jednotek. Můžete procházet tyto jednotky Průzkumníka Windows nebo Průzkumníka souborů. Písmena jednotek přiřazená k daným svazkům nesmí být stejná písmena jako původní virtuální počítač, ale je zachováno název svazku. Například, pokud byl svazek v původní virtuální počítač "datový Disk (E:`\`)", že svazek může být připojen v místním počítači jako "datový Disk (jakékoli písmeno:`\`). Procházejte všechny svazky uvedeno ve výstupu skriptu vyhledejte soubory nebo složku.  
       
   ![Nabídka soubor obnovení](./media/backup-azure-restore-files-from-vm/volumes-attached.png)
           
#### <a name="for-linux"></a>Pro Linux

V systému Linux svazky bodu obnovení jsou připojené do složky, kde je skript spuštěn. Připojené disky, svazky a odpovídající cesty připojení se zobrazí odpovídajícím způsobem. Tyto cesty připojení jsou viditelné pro uživatele s kořenové úrovně přístupu. Procházejte svazky uvedeno ve výstupu skriptu.

  ![Nabídka Soubor Linux obnovení](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)
  

## <a name="closing-the-connection"></a>Probíhá ukončování připojení

Po identifikování souborů a jejich kopírování do umístění místního úložiště, odeberte (nebo odpojit) další jednotky. Odpojit jednotky, na **obnovení souboru** nabídky na portálu Azure klikněte na tlačítko **odpojit disky**.

![Odpojení disky](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Po disky se odpojily, zobrazí se, že byla úspěšná. To může trvat pár minut pro připojení k aktualizaci, které umožní odstranit disky.

V systému Linux po připojení k bodu obnovení je porušeno, operačního systému nemá odpovídající cesty připojení automaticky odebrat. Cesty připojení existovat jako "bez synchronní kopie" svazky a jsou viditelné, ale vyvolá chybu, když je přístup a zápis souborů. Je lze ručně odebrat. Skript, při spuštění, identifikuje všechny tyto svazky existující z jakékoli předchozí body obnovení a je vyčistí po souhlasu.

## <a name="special-configurations"></a>Zvláštní konfigurace

### <a name="dynamic-disks"></a>Dynamické disky

Pokud chráněný virtuální počítač Azure má svazků s jedna nebo obě následující vlastnosti, nebudete moci spustit spustitelný soubor skriptu na stejného virtuálního počítače. 

  - Svazky, které jsou rozmístěny v několika disků (předané a prokládané svazky)
  - Odolné proti chybám svazky (RAID-5 a zrcadlené) na dynamické disky 

Místo toho spusťte spustitelný soubor skriptu na libovolném počítači s kompatibilní operační systém.

### <a name="windows-storage-spaces"></a>Prostory úložiště ve Windows

Prostory úložiště ve Windows je technologie systému Windows, která umožňuje Virtualizovat úložiště. Prostory úložiště systému Windows můžete seskupit standardní disky do fondů úložišť. Pak použijete dostupné místo v těchto fondů úložišť vytvořit virtuální disky, nazývaných prostory úložiště.

Pokud chráněný virtuální počítač Azure používá prostory úložiště ve Windows, nebudete moci spustit spustitelný soubor skriptu na stejného virtuálního počítače. Místo toho spusťte spustitelný soubor skriptu z jakéhokoli počítače s kompatibilní operační systém.

### <a name="lvmraid-arrays"></a>Pole LVM/RAID

V systému Linux Správce logických svazků (LVM) nebo softwaru pole RAID se používají ke správě logické svazky přes několik disků. Pokud chráněný virtuální počítač s Linuxem používá LVM nebo pole RAID, nelze spustit skript v stejného virtuálního počítače. Místo toho spuštění skriptu na jiné počítače s operačním systémem kompatibilní a který podporuje systém souborů chráněných virtuálních počítačů.

Následující výstup skriptu zobrazí disky LVM nebo pole RAID a svazky s typem oddílu.

   ![Výstup LVM Linux nabídky](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)
   
Aby tyto oddíly online spusťte příkazy v následujících částech. 

**Pro LVM oddíly**

Seznam názvů skupin svazku v části fyzický svazku.
```
$ pvs <volume name as shown above in the script output> 
```
K zobrazení seznamu všech logických svazků, názvy a jejich cesty ve skupině svazku.

```
$ lvdisplay <volume-group-name from the pvs command’s results> 
```

Chcete-li připojit logické svazky na cestu podle svého výběru.

```
$ mount <LV path> </mountpath>
```



**Pro pole RAID**

Následující příkaz zobrazí podrobnosti o všech disků raid.

```
$ mdadm –detail –scan
```
 Relevantní RAID disku se zobrazí jako `/dev/mdm/<RAID array name in the protected VM>`

Pokud RAID disk fyzických svazků, použijte příkaz připojení.
```
$ mount [RAID Disk Path] [/mountpath]
```

Pokud RAID disk má jiné LVM v nich konfigurovali, použít předchozí postup pro oddíly LVM ale použít název svazku namísto názvu disku diskového pole RAID

## <a name="system-requirements"></a>Systémové požadavky

### <a name="for-windows"></a>Pro Windows

V následující tabulce jsou uvedeny kompatibilitu mezi serverem a počítačem operační systémy. Při obnovování souborů, nelze obnovit soubory na operační systém předchozí nebo budoucí verzi. Například nelze obnovit soubor z virtuálního počítače Windows serveru 2016 pro Windows Server 2012 nebo počítač Windows 8. Stejný operační systém serveru nebo kompatibilní klientský operační systém, můžete obnovit soubory z virtuálního počítače.   

|OS serveru | Kompatibilní klientského operačního systému  |
| --------------- | ---- |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>Pro Linux operačního systému

V systému Linux musí podporovat operačního systému počítače použitého k obnovení souborů systému souborů chráněného virtuálního počítače. Když vyberete počítač pro spuštění skriptu, zkontrolujte na počítači má kompatibilní operační systém a používá jednu z verzí identifikovat v následující tabulce:

|Operační systém Linux | Verze  |
| --------------- | ---- |
| Ubuntu | 12.04 a vyšší |
| CentOS | verze 6.5 a vyšší  |
| RHEL | 6.7 a vyšší |
| Debian | 7 a vyšší |
| Oracle Linux | 6.4 a vyšší |
| SLES | 12 a vyšší |
| openSUSE | 42.2 a vyšší |

Skript také vyžaduje Python a bash komponent ke spouštění a bezpečně připojit k bodu obnovení.

|Komponenta | Verze  |
| --------------- | ---- |
| bash | 4 a novější |
| python | 2.6.6 a vyšší  |
| TLS | 1.2 by podporovat.  |

## <a name="troubleshooting"></a>Řešení potíží

Pokud máte potíže při obnovování soubory z virtuálních počítačů, zkontrolujte následující tabulce najdete další informace.

| Chybová zpráva nebo scénáře | Pravděpodobná příčina | Doporučená akce |
| ------------------------ | -------------- | ------------------ |
| Výstupní soubor EXE: *výjimka připojení k cíli* |Skript není možné získat přístup k bodu obnovení | Zkontrolujte, zda počítač splňuje všechny požadavky na předchozí přístup. |  
|   Výstupní soubor EXE: *cíl již byla zaznamenána prostřednictvím relace ISCSI.* | Skript byl již spuštěn na stejném počítači a byly připojeny jednotky | Již byly připojeny svazků bodu obnovení. Může připojených není s stejná písmena jednotek původní virtuálního počítače. Procházet všechny svazky, které jsou k dispozici v Průzkumníku souborů souboru |
| Výstupní soubor EXE: *tento skript je neplatný, protože disky mají byly odpojeny prostřednictvím 12-hr portál nebo překročil limit. Stáhněte si nový skript z portálu.* | Disky mají byla odpojena z portálu nebo byl překročen limit 12 hr |    Tato konkrétní exe je neplatný a nelze spustit. Pokud chcete přístup k souborům tohoto obnovení bodu v čase, přejděte na portálu pro nové exe|
| Na počítači, kde se spouští exe: nové svazky nejsou odpojeny po kliknutí na tlačítko odpojení |    Iniciátor ISCSI na počítači není reagovat nebo obnovení připojení k cíli a údržbu mezipaměti |    Počkejte několik minut, po stisknutí tlačítka odpojení. Pokud ještě nejsou odpojeny nové svazky, přejděte prosím prostřednictvím všechny svazky. Vynutí se tak iniciátoru aktualizovat připojení a svazek je odpojený s chybovou zprávou, disk není k dispozici|
| Výstupní soubor EXE: skript je spuštěn úspěšně, ale "Nové svazky připojené" se nezobrazí na výstup skriptu | Toto je přechodné chybě.   | Svazky by byly již připojeny. Otevřete Průzkumníka a přejděte. Pokud ke spouštění skriptů pokaždé, když používáte stejný počítač, zvažte, restartování počítače a v seznamu má být zobrazena v běží následné exe. |
| Konkrétní Linux: Nepodařilo se zobrazit požadované svazky | Operačního systému počítače, kde se skript spouští nemusí rozpoznat základní systém souborů chráněného virtuálního počítače | Zkontrolujte, zda bod obnovení je havárií konzistentní nebo konzistentními soubory. Pokud soubor konzistentní, spusťte skript na jiném počítači jejichž operační systém rozpozná filesystem chráněného Virtuálního počítače |
| Specifické pro Windows: Nepodařilo se zobrazit požadované svazky | Jsou disky připojené, ale nebyly nakonfigurované svazky | Na obrazovce Správa disků zjistěte, které další disky týkající se bodu obnovení. Pokud jsou některé z těchto disků v režimu offline stavu zkuste přitom online kliknutím pravým tlačítkem na disk a klikněte na tlačítko 'Online.|
