---
title: Obnovení stavu systému na Windows Server
description: Podrobný popis postupu obnovení stavu systému Windows Server ze zálohy v Azure.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 08/18/2017
ms.openlocfilehash: 6d46a091a4e620e26d05735f12a201009663e65d
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77602469"
---
# <a name="restore-system-state-to-windows-server"></a>Obnovení stavu systému na Windows Server

Tento článek vysvětluje, jak obnovit zálohy stavu systému Windows Server z trezoru služby Azure Recovery Services. Chcete-li obnovit stav systému, je nutné mít zálohu stavu systému (vytvořenou pomocí pokynů v části [stav systému zálohování](backup-azure-system-state.md#back-up-windows-server-system-state)) a ujistěte se, že máte nainstalovanou [nejnovější verzi agenta Microsoft Azure Recovery Services (MARS)](https://aka.ms/azurebackup_agent). Obnova dat stavu systému Windows Server z trezoru služby Azure Recovery Services je proces se dvěma kroky:

1. Obnoví stav systému jako soubory z Azure Backup. Když obnovujete stav systému jako soubory z Azure Backup, můžete:
   * Obnovte stav systému na stejný server, na kterém byly zálohy pořízeny, nebo
   * Obnovte soubor stavu systému na alternativním serveru.

2. Použijte obnovené soubory stavu systému na Windows Server.

## <a name="recover-system-state-files-to-the-same-server"></a>Obnovení souborů stavu systému na stejný server

Následující postup vysvětluje, jak vrátit konfiguraci Windows serveru do předchozího stavu. Vracení konfigurace serveru zpátky na známý, stabilní stav může být extrémně cenné. Následující kroky obnoví stav systému serveru z trezoru Recovery Services.

1. Otevřete modul snap-in **Microsoft Azure Backup**. Pokud si nejste jisti, kde byl modul snap-in nainstalován, vyhledejte **Microsoft Azure Backup**v počítači nebo na serveru.

    Aplikace klasické pracovní plochy by se měla zobrazit ve výsledcích hledání.

2. Kliknutím na **obnovit data** spusťte průvodce.

    ![Obnovení dat](./media/backup-azure-restore-windows-server/recover.png)

3. Chcete-li obnovit data na stejném serveru nebo počítači, vyberte v podokně **Začínáme** možnost **tento server (`<server name>`)** a klikněte na tlačítko **Další**.

    ![Zvolením možnosti Server obnovte data do stejného počítače.](./media/backup-azure-restore-system-state/samemachine.png)

4. V podokně **Vybrat režim obnovení** zvolte možnost **stav systému** a klikněte na tlačítko **Další**.

    ![Procházet soubory](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. V kalendáři v podokně **Vybrat svazek a datum** vyberte bod obnovení.

    Můžete obnovit z libovolného bodu obnovení v čase. Kalendářní data **tučně** označují dostupnost alespoň jednoho bodu obnovení. Jakmile vyberete datum, pokud je k dispozici více bodů obnovení, zvolte konkrétní bod obnovení z rozevírací nabídky **čas** .

    ![Svazek a datum](./media/backup-azure-restore-system-state/select-date.png)

6. Jakmile vyberete bod obnovení, který chcete obnovit, klikněte na tlačítko **Další**.

    Azure Backup připojí místní bod obnovení a použije ho jako svazek pro obnovení.

7. V dalším podokně určete cílové umístění obnovených souborů stavu systému a kliknutím na tlačítko **Procházet** otevřete Průzkumníka Windows a vyhledejte soubory a složky, které chcete použít. Možnost **vytvoří kopie, takže máte obě verze**, vytvoří kopie jednotlivých souborů v existujícím archivu souborů stavu systému místo vytvoření kopie celého archivu stavu systému.

    ![Možnosti obnovení](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Ověřte podrobnosti o obnovení v podokně **potvrzení** a klikněte na tlačítko **obnovit**.

   ![Kliknutím na tlačítko Obnovit potvrďte akci obnovení](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Zkopírujte adresář *WindowsImageBackup* do umístění pro obnovení do nedůležitého svazku serveru. Nepostradatelným svazkem je obvykle svazek operačního systému Windows.

10. Po úspěšném obnovení postupujte podle kroků v části, [použijte obnovené soubory stavu systému na Windows Server](backup-azure-restore-system-state.md), abyste mohli dokončit proces obnovení stavu systému.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Obnovení souborů stavu systému na alternativním serveru

Pokud je váš Windows Server poškozený nebo nedostupný a chcete ho obnovit do stabilního stavu tím, že obnovujete stav systému Windows Server, můžete obnovit stav systému poškozeného serveru z jiného serveru. Následující postup použijte pro obnovení stavu systému na samostatném serveru.  

Terminologie použitá v těchto krocích zahrnuje:

* *Zdrojový počítač* – původní počítač, ze kterého byla provedena záloha, a který aktuálně není k dispozici.
* *Cílový počítač* – počítač, na který se data obnovují.
* *Vzorový trezor* – Recovery Services trezor, ke kterému jsou zaregistrované *zdrojový počítač* a *cílový počítač* . <br/>

> [!NOTE]
> Zálohy provedené z jednoho počítače nelze obnovit do počítače, na kterém je spuštěna dřívější verze operačního systému. Například zálohy pořízené z počítače se systémem Windows Server 2016 nelze obnovit do systému Windows Server 2012 R2. Je však možné, že se jedná o funkci INVERT. K obnovení systému Windows Server 2016 můžete použít zálohy z Windows Serveru 2012 R2.
>

1. Otevřete modul snap-in **Microsoft Azure Backup** v *cílovém počítači*.
2. Ujistěte se, že *cílový počítač* a *zdrojový počítač* jsou zaregistrované ve stejném trezoru Recovery Services.
3. Kliknutím na **obnovit data** spusťte pracovní postup.
4. Vybrat **jiný server**

    ![Jiný server](./media/backup-azure-restore-system-state/anotherserver.png)

5. Zadejte soubor s přihlašovacími údaji úložiště, který odpovídá *trezoru ukázek*. Pokud je soubor přihlašovacích údajů trezoru neplatný (nebo vypršela jeho platnost), Stáhněte si nový soubor s přihlašovacími údaji trezoru z *trezoru ukázek* v Azure Portal. Po zadání souboru s přihlašovacími údaji trezoru se zobrazí Recovery Services trezor přidružený k souboru s přihlašovacími údaji trezoru.

6. V podokně vybrat záložní server vyberte *zdrojový počítač* ze seznamu zobrazených počítačů.
7. V podokně vybrat režim obnovení zvolte možnost **stav systému** a klikněte na tlačítko **Další**.

    ![Hledání](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. V kalendáři v podokně **Vybrat svazek a datum** vyberte bod obnovení. Můžete obnovit z libovolného bodu obnovení v čase. Kalendářní data **tučně** označují dostupnost alespoň jednoho bodu obnovení. Jakmile vyberete datum, pokud je k dispozici více bodů obnovení, zvolte konkrétní bod obnovení z rozevírací nabídky **čas** .

    ![Hledat položky](./media/backup-azure-restore-system-state/select-date.png)

9. Jakmile vyberete bod obnovení, který chcete obnovit, klikněte na tlačítko **Další**.

10. V podokně **Vybrat režim obnovení stavu systému** zadejte cíl, ve kterém chcete obnovit soubory stavu systému, a pak klikněte na **Další**.

    ![Šifrování](./media/backup-azure-restore-system-state/recover-as-files.png)

    Možnost **vytvoří kopie, takže máte obě verze**, vytvoří kopie jednotlivých souborů v existujícím archivu souborů stavu systému místo vytvoření kopie celého archivu stavu systému.

11. Ověřte podrobnosti o obnovení v podokně potvrzení a klikněte na tlačítko **obnovit**.

    ![Kliknutím na tlačítko Obnovit potvrďte proces obnovení.](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Zkopírujte adresář *WindowsImageBackup* do nedůležitého svazku serveru (například D:\). Nepostradatelným svazkem je obvykle svazek operačního systému Windows.

13. K dokončení procesu obnovení použijte následující část k [použití obnovených souborů stavu systému na Windows serveru](#apply-restored-system-state-on-a-windows-server).

## <a name="apply-restored-system-state-on-a-windows-server"></a>Použít obnovený stav systému na Windows serveru

Jakmile obnovíte Stav systému jako soubory pomocí služby Azure Recovery Services agent, použijte nástroj Zálohování Windows Serveru a nainstalujte obnovený stav systému na Windows Server. Nástroj Zálohování Windows Serveru je již na serveru k dispozici. Následující kroky vysvětlují, jak použít obnovený stav systému.

1. Následující příkazy použijte k restartování serveru v *režimu opravy adresářových služeb*. Na příkazovém řádku se zvýšenými oprávněními:

    ```cmd
    Bcdedit /set safeboot dsrepair
    Shutdown /r /t 0
    ```

2. Po restartování otevřete modul snap-in Zálohování Windows Serveru. Pokud si nejste jisti, kde byl modul snap-in nainstalován, vyhledejte **zálohování Windows serveru**v počítači nebo na serveru.

    Aplikace klasické pracovní plochy se zobrazí ve výsledcích hledání.

3. V modulu snap-in vyberte **místní zálohování**.

    ![Vyberte místní zálohování, ze kterého se má obnovit.](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. V konzole místní zálohování klikněte v **podokně akce**na **obnovit** a otevřete Průvodce obnovením.

5. Vyberte možnost **zálohování uložené v jiném umístění**a klikněte na tlačítko **Další**.

   ![Výběr obnovení na jiný server](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. Když zadáte typ umístění, vyberte **Vzdálená sdílená složka** , pokud se vaše záloha stavu systému obnovila na jiný server. Pokud se stav systému obnovil místně, vyberte **místní jednotky**.

    ![Vyberte, jestli se má obnovení z místního serveru nebo jiného.](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Zadejte cestu k adresáři *WindowsImageBackup* nebo vyberte místní jednotku obsahující tento adresář (například D:\WindowsImageBackup), která se obnovila jako součást obnovení souborů stavu systému pomocí agenta Azure Recovery Services a klikněte na **Další**.

    ![Cesta ke sdílenému souboru](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Vyberte verzi stavu systému, kterou chcete obnovit, a klikněte na tlačítko **Další**.

9. V podokně vybrat typ obnovení vyberte možnost **stav systému** a klikněte na tlačítko **Další**.

10. Pro umístění obnovení stavu systému vyberte možnost **původní umístění**a klikněte na tlačítko **Další**.

11. Zkontrolujte podrobnosti o potvrzení, ověřte nastavení restartování a kliknutím na **obnovit** použijte obnovené soubory stavu systému.

    ![Spustit obnovení souborů stavu systému](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Zvláštní požadavky na obnovení stavu systému na serveru služby Active Directory

Zálohování stavu systému zahrnuje data služby Active Directory. Pomocí následujících kroků obnovte Doména služby Active Directory služby (služba AD DS) z aktuálního stavu do předchozího stavu.

1. Restartujte řadič domény v režimu obnovení adresářových služeb (DSRM).
2. Použijte [následující postup k](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-nonauthoritative-restore) obnovení služba AD DS pomocí rutin zálohování Windows serveru.

## <a name="troubleshoot-failed-system-state-restore"></a>Řešení potíží s nezdařeným obnovením stavu systému

Pokud se předchozí proces použití stavu systému úspěšně nedokončí, použijte k obnovení Windows serveru prostředí Windows Recovery Environment (Win RE). Následující kroky vysvětlují, jak obnovit pomocí funkce Win znovu. Tuto možnost použijte jenom v případě, že se Windows Server nespouští normálně po obnovení stavu systému. Následující postup smaže data, která nejsou systémová, buďte opatrní.

1. Spusťte Windows Server do prostředí Windows Recovery Environment (Win RE).

2. Vyberte řešení potíží ze tří dostupných možností.

    ![otevření nabídky](./media/backup-azure-restore-system-state/winre-1.png)

3. Na obrazovce **Upřesnit možnosti** vyberte **příkazový řádek** a zadejte uživatelské jméno a heslo správce serveru.

   ![otevření nabídky](./media/backup-azure-restore-system-state/winre-2.png)

4. Zadejte uživatelské jméno a heslo správce serveru.

    ![otevření nabídky](./media/backup-azure-restore-system-state/winre-3.png)

5. Když otevřete příkazový řádek v režimu správce, spusťte následující příkaz, který získá verzi zálohy stavu systému.

    ```cmd
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```

    ![získat verze zálohy stavu systému](./media/backup-azure-restore-system-state/winre-4.png)

6. Spusťte následující příkaz, který načte všechny svazky, které jsou k dispozici v záloze.

    ```cmd
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![získat verze zálohy stavu systému](./media/backup-azure-restore-system-state/winre-5.png)

7. Následující příkaz obnoví všechny svazky, které jsou součástí zálohy stavu systému. Všimněte si, že tento krok obnovuje jenom důležité svazky, které jsou součástí stavu systému. Všechna data, která nejsou v systému, se vymažou.

    ```cmd
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```

     ![získat verze zálohy stavu systému](./media/backup-azure-restore-system-state/winre-6.png)

## <a name="next-steps"></a>Další kroky

* Teď, když jste obnovili své soubory a složky, můžete [Spravovat zálohy](backup-azure-manage-windows-server.md).
