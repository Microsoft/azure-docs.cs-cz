---
title: 'Azure Backup: Obnovení souborů a složek ze zálohy virtuálního počítače Azure'
description: Obnovení souborů z bodu obnovení virtuálního počítače Azure
services: backup
author: pvrk
manager: shivamg
keywords: obnovení na úrovni položek; obnovení souborů ze záloh virtuálních počítačů Azure; obnovení souborů z virtuálního počítače Azure
ms.service: backup
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: pullabhk
ms.openlocfilehash: fecdb54af58faaf601ab74f89039a47e0d32e650
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39493377"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Obnovení souborů ze záloh virtuálních počítačů Azure

Azure Backup poskytuje možnosti obnovení [Azure virtual machines (VM) a disky](./backup-azure-arm-restore-vms.md) ze záloh virtuálních počítačů Azure, označované také jako body obnovení. Tento článek vysvětluje, jak obnovit soubory a složky ze zálohy virtuálního počítače Azure. Obnovení souborů a složek je k dispozici pouze pro virtuální počítače Azure nasazené pomocí modelu Resource Manageru a chránit v trezoru služby Recovery services.

> [!Note]
> Tato funkce je dostupná pro virtuální počítače Azure nasazené pomocí modelu Resource Manageru a chránit v trezoru služby Recovery Services.
> Obnovení souborů z šifrované zálohování virtuálního počítače není podporováno.
>

## <a name="mount-the-volume-and-copy-files"></a>Připojit svazek a kopírovat soubory

Obnovit soubory a složky z bodu obnovení, přejděte na virtuální počítač a vyberte bod obnovení požadované.

1. Přihlaste se k [webu Azure portal](http://portal.Azure.com) a v levém podokně klikněte na tlačítko **virtuálních počítačů**. Ze seznamu virtuálních počítačů vyberte virtuální počítač a otevřete řídicí panel virtuální počítač.

2. V nabídce virtuálního počítače, klikněte na tlačítko **zálohování** otevřete řídicí panel zálohování.

    ![Otevřít položku Zálohování trezoru služby Recovery Services](./media/backup-azure-restore-files-from-vm/open-vault-from-vm.png)

3. V nabídce řídicího panelu zálohování, klikněte na tlačítko **obnovení souborů** otevřete nabídku.

    ![Obnovení nabídky Soubor](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. Z **vyberte bod obnovení** rozevírací nabídky vyberte bod obnovení, který obsahuje soubory, které chcete. Ve výchozím nastavení je již vybrán poslední bod obnovení.

5. Chcete-li stáhnout software použije ke zkopírování souborů z bodu obnovení, klikněte na tlačítko **stáhnout spustitelný soubor** (pro virtuální počítač Windows Azure) nebo **stáhnout skript** (pro virtuální počítač Azure s Linuxem).

    ![Vygenerované heslo](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure stáhne spustitelný soubor nebo skript na místním počítači.

    ![Stáhněte si zprávu pro spustitelný soubor nebo skript](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Ke spuštění spustitelného souboru nebo skriptu jako správce, doporučuje se, že Uložte stažený soubor do počítače.

6. Spustitelný soubor nebo skript chráněná heslem a vyžaduje heslo. V **obnovení souborů** nabídky, klikněte na tlačítko Kopírovat se heslo načíst do paměti.

    ![Vygenerované heslo](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. Z umístění stahování (obvykle složky Stažené soubory) klikněte pravým tlačítkem na spustitelný soubor nebo skript a spusťte ho pomocí přihlašovacích údajů správce. Po zobrazení výzvy zadejte heslo nebo vložte heslo z paměti a stiskněte klávesu Enter. Po zadání platné heslo, skript se připojí k bodu obnovení.

    ![Obnovení nabídky Soubor](./media/backup-azure-restore-files-from-vm/executable-output.png)

    Pokud skript spustíte na počítači s omezeným přístupem, ujistěte se, je přístup k:

    - download.microsoft.com
    - Adresy URL služby zotavení (geo-name se odkazuje na oblast, ve které se nachází trezor služby recovery Services)
        - <https://pod01-rec2.geo-name.backup.windowsazure.com> (U veřejných zeměpisných oblastech Azure)
        - <https://pod01-rec2.geo-name.backup.windowsazure.cn> (Pro Azure Čína)
        - <https://pod01-rec2.geo-name.backup.windowsazure.us> (Pro Azure Government v USA)
        - <https://pod01-rec2.geo-name.backup.windowsazure.de> (Pro Azure Germany)
    - odchozí port 3260

    Pro Linux vyžaduje skript 'open-iscsi' a "lshw" součásti pro připojení k bodu obnovení. Pokud komponenty na počítači, ve kterém se skript spouští neexistují, skript vyzve k zadání oprávnění k instalaci součásti. Poskytnout souhlas nainstalujte nezbytné součásti.

    Přístup k download.microsoft.com je potřeba stáhnout součásti sloužící k sestavení zabezpečený kanál mezi počítačem, ve kterém se skript spouští a dat v bodu obnovení.

    Spusťte skript na jakýkoli počítač, který má stejné (nebo kompatibilní) operačního systému jako zálohování virtuálního počítače. Zobrazit [tabulky kompatibilní operační systém](backup-azure-restore-files-from-vm.md#system-requirements) pro kompatibilní operační systémy. Pokud chráněný virtuální počítač Azure používá prostory úložiště ve Windows (pro virtuální počítače Azure s Windows) nebo pole LVM/RAID (pro virtuální počítače s Linuxem), nelze spustit spustitelný soubor nebo skript ve stejném virtuálním počítači. Místo toho spusťte spustitelný soubor nebo skript na jiný počítač s kompatibilní operační systém.

### <a name="identifying-volumes"></a>Určení svazků

#### <a name="for-windows"></a>Pro Windows

Při spuštění spustitelného souboru připojí nové svazky operačního systému a přiřadí písmena jednotek. Průzkumníka Windows nebo Průzkumníka souborů můžete použít k procházení těchto jednotek. Přiřazené k daným svazkům písmena jednotek nemusí být stejná písmena jako původní virtuální počítač, ale je zachováno název svazku. Například, pokud byl svazek na původní virtuální počítač "datový Disk (E:`\`)", že svazek je možné připojit v místním počítači jako "datový Disk (žádné písmeno:`\`). Projděte si všechny svazky, které jsou uvedené ve výstupu skriptu, dokud nenajdete souborů a složek.  

   ![Obnovení nabídky Soubor](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>Pro Linux

V systému Linux jsou do složky, ve kterém se skript spouští připojené svazky bodů obnovení. Připojené disky, svazky a příslušnými cestami připojení jsou uvedeny odpovídajícím způsobem. Připojte tyto cesty jsou viditelné pro uživatele s kořenové úrovně přístupu. Projděte si svazky uvedené ve výstupu skriptu.

  ![Nabídky obnovení souborů v Linuxu](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)
  
## <a name="closing-the-connection"></a>Probíhá ukončování připojení

Po identifikování souborů a zkopírujete do umístění místního úložiště, odeberte (nebo odpojte) další jednotky. Odpojit disky, na **obnovení souborů** nabídky na webu Azure Portal, klikněte na tlačítko **odpojit disky**.

![Odpojit disky](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Jakmile disky byly odpojeny, zobrazí se zpráva oznamující, že bylo úspěšné. Může trvat několik minut, než připojení aktualizovat tak, že odeberete disky.

V systému Linux po připojení k bodu obnovení je porušeno, operační systém neodebere příslušnými cestami připojení automaticky. Připojení cesty existovat jako "osamocené" svazky a jsou viditelné, ale vyvolá chybu, když je přístup a zápis souborů. Lze je ručně odstranit. Při spuštění skriptu identifikuje všechny tyto svazky existující z jakékoli předchozí body obnovení a jejich vyčištění po vyjádření souhlasu.

## <a name="special-configurations"></a>Zvláštní konfigurace

### <a name="dynamic-disks"></a>Dynamické disky

Pokud chráněný virtuální počítač Azure má svazky s jeden nebo oba z těchto vlastností, nelze spustit spustitelný soubor skriptu v jednom virtuálním počítači.

    - Svazky, které zahrnují více disků (doby trvání a prokládané svazky)
    - Odolné proti chybám svazky (RAID-5 a zrcadlených svazků) na dynamických discích

Místo toho spuštění spustitelného souboru skriptu na libovolném počítači s kompatibilní operační systém.

### <a name="windows-storage-spaces"></a>Prostory úložiště ve Windows

Prostory úložiště Windows je technologie Windows, která umožňuje Virtualizovat úložiště. S prostory úložiště ve Windows můžete seskupit standardní disky do fondů úložiště. Pak použijete dostupné místo v těchto fondů úložišť vytvořit virtuální disky, říká se jim prostory úložiště.

Pokud chráněném virtuálním počítači Azure používá prostory úložiště Windows, nelze spustit spustitelný soubor skriptu v jednom virtuálním počítači. Místo toho spuštění spustitelného souboru skriptu na jiný počítač s kompatibilní operační systém.

### <a name="lvmraid-arrays"></a>Pole LVM/diskového pole RAID

V systému Linux Správce logických svazků (LVM) a/nebo softwarové pole RAID slouží ke správě logické svazky přes několik disků. Pokud chráněného virtuálního počítače s Linuxem používá LVM a/nebo pole RAID, nelze spustit skript ve stejném virtuálním počítači. Místo toho spuštění skriptu na jiný počítač s kompatibilní operační systém a která podporuje systému souborů chráněném virtuálním počítači.

Následující výstup skriptu zobrazí disky LVM a/nebo pole RAID a svazky s typem oddílu.

   ![Výstup LVM Linux nabídky](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Chcete-li přenést tyto oddíly online, spusťte příkazy v následujících částech.

#### <a name="for-lvm-partitions"></a>Pro oddílech LVM

Seznam názvů skupin svazku v části fyzický svazek.

```bash
#!/bin/bash
$ pvs <volume name as shown above in the script output>
```

K výpisu všech logických svazků, názvy a jejich cesty ve skupině svazku.

```bash
#!/bin/bash
$ lvdisplay <volume-group-name from the pvs command’s results>
```

Připojte logickou svazky na cestu podle vašeho výběru.

```bash
#!/bin/bash
$ mount <LV path> </mountpath>
```

#### <a name="for-raid-arrays"></a>Pro pole RAID

Následující příkaz zobrazí podrobnosti o všech disků raid.

```bash
#!/bin/bash
$ mdadm –detail –scan
```

 Zobrazí se jako odpovídající disk RAID `/dev/mdm/<RAID array name in the protected VM>`

Pokud je RAID disk fyzických svazků, použijte příkaz připojení.

```bash
#!/bin/bash
$ mount [RAID Disk Path] [/mountpath]
```

Pokud RAID disk má jiné LVM jste v nich konfigurovali, použijte předchozí postup pro oddílech LVM ale použít název svazku místo názvu disku diskového pole RAID

## <a name="system-requirements"></a>Systémové požadavky

### <a name="for-windows-os"></a>Pro operační systém Windows

V následující tabulce jsou uvedeny kompatibilitu mezi serverem a počítačem operačních systémů. Při obnovování souborů, nelze obnovit soubory na operační systém předchozí nebo budoucí verzi. Například nelze obnovit soubor z virtuálního počítače s Windows serverem 2016 na Windows Server 2012 nebo počítači se systémem Windows 8. Stejný operační systém serveru, nebo na kompatibilní klientský operační systém, můžete obnovit soubory z virtuálního počítače.

|Serverový operační systém | Kompatibilní klientský operační systém  |
| --------------- | ---- |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>Pro operační systém Linux

V systému Linux musí podporovat operační systém počítače používá k obnovení souborů systému souborů chráněného virtuálního počítače. Při výběru počítače ke spuštění skriptu, zajistěte počítač kompatibilní operační systém a používá jednu z verzí, které jsou uvedené v následující tabulce:

|Operační systém Linux | Verze  |
| --------------- | ---- |
| Ubuntu | 12.04 a vyšší |
| CentOS | 6.5 a vyšší  |
| RHEL | 6.7 a vyšší |
| Debian | 7 a vyšší |
| Oracle Linux | 6.4 a vyšší |
| SLES | 12 a vyšší |
| openSUSE | 42.2 a vyšší |

Skript také vyžaduje součásti Python a bash ke spouštění a bezpečně připojit k bodu obnovení.

|Komponenta | Verze  |
| --------------- | ---- |
| Bash | 4 a vyšší |
| python | 2.6.6 a vyšší  |
| TLS | 1.2 by měla podporovat.  |

## <a name="troubleshooting"></a>Řešení potíží

Pokud máte potíže při obnovení souborů z virtuálních počítačů, zkontrolujte následující tabulce najdete další informace.

| Chybová zpráva / scénář | Pravděpodobná příčina | Doporučená akce |
| ------------------------ | -------------- | ------------------ |
| Výstupní soubor EXE: *výjimky připojení k cíli* |Skript není možné získat přístup k bodu obnovení    | Zkontrolujte, zda počítač splňuje předchozí požadavky na přístup. |  
| Výstupní soubor EXE: *cíl má již byl přihlášen prostřednictvím relace iSCSI.* | Skript byl již spuštěn ve stejném počítači a byly připojeny jednotky | Již byly připojeny svazků bodu obnovení. Může není připojený s stejná písmena jednotek původního virtuálního počítače. Projděte si všechny svazky, které jsou k dispozici v Průzkumníku souborů k nahrání souboru |
| Výstupní soubor EXE: *tento skript je neplatný, protože disky mají byly odpojeny prostřednictvím 12 hodin portál/překročil limit. Stáhněte nový skript z portálu.* |    Disky mají byla odpojena z portálu nebo Překročili jste limit 12 hodin | Tento konkrétní soubor exe je neplatný a nelze jej spustit. Pokud chcete získat přístup k souborům této obnovení v daném okamžiku, navštivte portál pro nový exe|
| Na počítači, ve kterém je spuštěn soubor exe: nové svazky nejsou odpojeny, po kliknutí na tlačítko odpojení | Iniciátor iSCSI na počítači není reagovat nebo aktualizuje jeho připojení k cíli a zachování mezipaměti |    Počkejte několik minut, po stisknutí tlačítka odpojení. Pokud ještě nejsou odpojeny nové svazky, prosím projděte si všechny svazky. To přinutí ze zahajujícího zařízení na aktualizovat připojení a svazek je odpojený s chybovou zprávou, že disk není k dispozici|
| Výstupní soubor EXE: skript je úspěšně spuštěn, ale "Nové svazky připojené" není zobrazena na výstup skriptu |    Jedná se o přechodnou chybu    | Svazky by byly již připojeny. Otevřete Průzkumníka a přejděte. Pokud ke spouštění skriptů pokaždé, když používáte stejný počítač, vezměte v úvahu počítač se restartuje a v seznamu má být zobrazena v následných exe spustí. |
| Specifické pro Linux: není možné zobrazit požadovaný svazky | Operační systém počítače, ve kterém se skript spouští nemusí rozpoznat základní systému souborů chráněných virtuálních počítačů | Zkontrolujte, zda bod obnovení je selhání konzistentní vzhledem k aplikacím nebo konzistentní. Pokud soubor konzistentní, spusťte skript na jiném počítači jejichž operační systém rozpozná systému souborů chráněném virtuálním počítači |
| Specifické pro Windows: není možné zobrazit požadovaný svazky | Disky připojené, ale nebyly nakonfigurovány svazky | Na obrazovce správy disků zjistěte další disky týkající se bodu obnovení. Jestliže má některý z těchto disků v offline stavu akci, díky kterým online kliknutím pravým tlačítkem na disk a klikněte na tlačítko "Online.|
