---
title: Obnovit stav systému na systému Windows Server
description: Podrobné vysvětlení pro obnovení stavu systému Windows Server System ze zálohy v Azure.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 08/18/2017
ms.openlocfilehash: 6d46a091a4e620e26d05735f12a201009663e65d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77602469"
---
# <a name="restore-system-state-to-windows-server"></a>Obnovit stav systému windows server

Tento článek vysvětluje, jak obnovit zálohy stavu systému Windows Server system ze trezoru služby Azure Recovery Services. Chcete-li obnovit stav systému, musíte mít zálohu stavu systému (vytvořenou podle pokynů v části [Zálohovat stav systému](backup-azure-system-state.md#back-up-windows-server-system-state)a ujistěte se, že jste nainstalovali [nejnovější verzi agenta služby Microsoft Azure Recovery Services (MARS).](https://aka.ms/azurebackup_agent) Obnovení dat stavu systému Windows Server system z trezoru služby Azure Recovery Services je dvoustupňový proces:

1. Obnovení stavu systému jako soubory z Azure Backup. Při obnovení stavu systému jako souborů z Azure Backup můžete buď:
   * Obnovit stav systému na stejném serveru, kde byly zálohy pořízeny, nebo
   * Obnovte soubor Stavu systému na alternativní server.

2. Použijte obnovené soubory stavu systému na systému Windows Server.

## <a name="recover-system-state-files-to-the-same-server"></a>Obnovení souborů se stavem systému na stejný server

Následující kroky vysvětlují, jak vrátit konfiguraci systému Windows Server do předchozího stavu. Vrácení konfigurace serveru zpět do známého a stabilního stavu může být velmi cenné. Následující kroky obnoví stav systému serveru z trezoru služby Recovery Services.

1. Otevřete modul snap-in **Microsoft Azure Backup**. Pokud nevíte, kde byl modul snap-in nainstalován, vyhledejte v počítači nebo na serveru **službu Microsoft Azure Backup**.

    Ve výsledcích hledání by se měla zobrazit aplikace klasické pracovní plochy.

2. Průvodce spusťte klepnutím na **tlačítko Obnovit data.**

    ![Obnovit data](./media/backup-azure-restore-windows-server/recover.png)

3. Chcete-li obnovit data na stejném serveru nebo počítači, vyberte v podokně **Začínáme** v yberte **Tento server`<server name>`( )** a klepněte na tlačítko **Další**.

    ![Tuto možnost serveru zvolte, chcete-li obnovit data do stejného počítače.](./media/backup-azure-restore-system-state/samemachine.png)

4. V podokně **Vybrat režim obnovení** zvolte Stav **systému** a klepněte na tlačítko **Další**.

    ![Procházení souborů](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. V kalendáři v podokně **Vybrat svazek a Datum** vyberte bod obnovení.

    Můžete obnovit z libovolného bodu obnovení v čase. Data **tučně** označují dostupnost alespoň jednoho bodu obnovení. Jakmile vyberete datum, pokud je k dispozici více bodů obnovení, zvolte konkrétní bod obnovení z rozevírací nabídky **Čas.**

    ![Objem a datum](./media/backup-azure-restore-system-state/select-date.png)

6. Po výběru bodu obnovení, který chcete obnovit, klepněte na tlačítko **Další**.

    Azure Backup připojí místní bod obnovení a použije ho jako svazek pro obnovení.

7. V dalším podokně zadejte cíl obnovených souborů Stav systému a klepnutím na **tlačítko Procházet** otevřete Průzkumníka Windows a vyhledejte požadované soubory a složky. Možnost **Vytvořit kopie tak, abyste měli obě verze**, vytvoří kopie jednotlivých souborů v existujícím archivu souborů Stav systému namísto vytvoření kopie celého archivu Stav systému.

    ![Možnosti obnovení](./media/backup-azure-restore-system-state/recover-as-files.png)

8. V **potvrzovacím** podokně ověřte podrobnosti o obnovení a klepněte na tlačítko **Obnovit**.

   ![klikněte na Obnovit, abyste potvrdili akci obnovení](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Zkopírujte adresář *WindowsImageBackup* v cíli obnovení na nekritický svazek serveru. Svazek operačního systému Windows je obvykle kritický svazek.

10. Po úspěšném obnovení dokončete proces obnovení stavu systému podle kroků v části [Použít obnovené soubory stavu systému na systému Windows Server](backup-azure-restore-system-state.md).

## <a name="recover-system-state-files-to-an-alternate-server"></a>Obnovení souborů stavu systému na alternativní server

Pokud je systém Windows Server poškozen nebo nepřístupný a chcete jej obnovit do stabilního stavu obnovením stavu systému Windows Server System, můžete obnovit stav systému poškozeného serveru z jiného serveru. Pomocí následujících kroků obnovte stav systému na samostatném serveru.  

Terminologie použitá v těchto krocích zahrnuje:

* *Zdrojový počítač* – původní počítač, ze kterého byla záloha odebrána a který je momentálně nedostupný.
* *Cílový počítač* – počítač, do kterého jsou data obnovována.
* *Ukázková úschovna* – trezor služby Recovery Services, do kterého jsou *zaregistrovány zdrojový a* *cílový počítač.* <br/>

> [!NOTE]
> Zálohy převzaté z jednoho počítače nelze obnovit do počítače se starší verzí operačního systému. Například zálohy převzaté z počítače se systémem Windows Server 2016 nelze obnovit do systému Windows Server 2012 R2. Inverzní je však možné. K obnovení systému Windows Server 2016 můžete použít zálohy ze systému Windows Server 2012 R2.
>

1. Otevřete modul snap-in **Microsoft Azure Backup** v *cílovém počítači*.
2. Ujistěte se, že *cílový počítač* a *zdrojový počítač* jsou registrovány do stejného trezoru služby Recovery Services.
3. Chcete-li zahájit pracovní postup, klepněte na tlačítko **Obnovit data.**
4. Vybrat **jiný server**

    ![Jiný server](./media/backup-azure-restore-system-state/anotherserver.png)

5. Zadejte soubor pověření úschovny, který odpovídá *ukázkové úschovně*. Pokud je soubor přihlašovacích údajů trezoru neplatný (nebo vypršela jeho platnost), stáhněte si nový soubor přihlašovacích údajů *trezoru* úložiště na webu Azure Portal. Po poskytnutí souboru pověření úschovny se zobrazí úschovna služeb obnovení přidružená k souboru pověření úschovny.

6. V podokně Vybrat záložní server vyberte *zdrojový počítač* ze seznamu zobrazených počítačů.
7. V podokně Vybrat režim obnovení zvolte **Stav systému** a klepněte na tlačítko **Další**.

    ![Search](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. V podokně **Vybrat svazek a Datum** vyberte v podokně Vybrat svazek a Datum bod obnovení. Můžete obnovit z libovolného bodu obnovení v čase. Data **tučně** označují dostupnost alespoň jednoho bodu obnovení. Jakmile vyberete datum, pokud je k dispozici více bodů obnovení, zvolte konkrétní bod obnovení z rozevírací nabídky **Čas.**

    ![Hledat položky](./media/backup-azure-restore-system-state/select-date.png)

9. Po výběru bodu obnovení, který chcete obnovit, klepněte na tlačítko **Další**.

10. V podokně **Vybrat režim obnovení stavu systému** zadejte cíl, ve kterém chcete obnovit soubory stavu systému, a klepněte na tlačítko **Další**.

    ![Šifrování](./media/backup-azure-restore-system-state/recover-as-files.png)

    Možnost **Vytvořit kopie tak, abyste měli obě verze**, vytvoří kopie jednotlivých souborů v existujícím archivu souborů Stav systému namísto vytvoření kopie celého archivu Stav systému.

11. V potvrzovacím podokně ověřte podrobnosti o obnovení a klepněte na tlačítko **Obnovit**.

    ![Klepnutím na tlačítko Obnovit potvrďte proces obnovení.](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Zkopírujte adresář *WindowsImageBackup* na nekritický svazek serveru (například D:\). Obvykle je svazek operačního systému Windows kritickým svazkem.

13. Chcete-li dokončit proces obnovení, použijte následující část k [použití obnovených souborů Stav systému v systému Windows Server](#apply-restored-system-state-on-a-windows-server).

## <a name="apply-restored-system-state-on-a-windows-server"></a>Použití obnoveného stavu systému na Windows Serveru

Po obnovení stavu systému jako souborů pomocí azure recovery services agenta použijte nástroj Zálohování serveru systému Windows pro použití obnoveného stavu systému na Windows Server. Nástroj Zálohování serveru je již na serveru k dispozici. Následující kroky vysvětlují, jak použít obnovený stav systému.

1. Pomocí následujících příkazů restartujte server v *režimu opravy adresářových služeb*. V příkazovém řádku se zvýšenými oprávněními:

    ```cmd
    Bcdedit /set safeboot dsrepair
    Shutdown /r /t 0
    ```

2. Po restartování otevřete modul snap-in Zálohování serveru. Pokud nevíte, kde byl modul snap-in nainstalován, vyhledejte v počítači nebo na serveru **program Zálohování serveru**.

    Ve výsledcích hledání se zobrazí aplikace klasické pracovní plochy.

3. V modulu snap-in vyberte **položku Místní záloha**.

    ![Vyberte místní zálohování, které chcete obnovit.](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. V konzole Místní záloha v **podokně akcí**klepnutím na **tlačítko Obnovit** otevřete Průvodce obnovením.

5. Vyberte možnost **Záloha uložená v jiném umístění**a klepněte na tlačítko **Další**.

   ![zvolte obnovení na jiný server](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. Při zadávání typu umístění vyberte **možnost Vzdálená sdílená složka,** pokud byla záloha Stav systému obnovena na jiném serveru. Pokud byl stav systému obnoven místně, vyberte **možnost Místní jednotky**.

    ![vyberte, zda chcete obnovit z místního serveru nebo jiného](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Zadejte cestu k adresáři *WindowsImageBackup* nebo zvolte místní jednotku obsahující tento adresář (například D:\WindowsImageBackup), která byla obnovena jako součást obnovení souborů stavu systému pomocí agenta služby Azure Recovery Services a klepněte na tlačítko **Další**.

    ![cesta ke sdílenému souboru](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Vyberte verzi Stav systému, kterou chcete obnovit, a klepněte na tlačítko **Další**.

9. V podokně Vybrat typ obnovení vyberte **Stav systému** a klepněte na tlačítko **Další**.

10. Umístění obnovení stavu systému zobrazíte vyberte **Původní umístění**a klepněte na tlačítko **Další**.

11. Zkontrolujte podrobnosti potvrzení, ověřte nastavení restartování a klepnutím na **tlačítko Obnovit** použijte obnovené soubory stavu systému.

    ![spuštění obnovení souborů Stavu systému](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Zvláštní informace o obnovení stavu systému na serveru Služby Active Directory

Zálohování stavu systému zahrnuje data služby Active Directory. Pomocí následujících kroků obnovte službu Active Directory Domain Service (AD DS) z aktuálního stavu do předchozího stavu.

1. Restartujte řadič domény v režimu obnovení adresářových služeb (DSRM).
2. Postupujte [podle zde uvedených](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-nonauthoritative-restore) kroků a pomocí rutin zálohování serveru obnovení služby AD DS.

## <a name="troubleshoot-failed-system-state-restore"></a>Poradce při potížích se nezdařilo obnovení stavu systému

Pokud předchozí proces použití stavu systému není úspěšně dokončen, použijte prostředí Windows Recovery Environment (Win RE) k obnovení systému Windows Server. Následující kroky vysvětlují, jak obnovit pomocí Win RE. Tuto možnost použijte pouze v případě, že se systém Windows Server po obnovení stavu systému nespustí normálně. Následující proces vymaže nesystémová data, buďte opatrní.

1. Spusťte systém Windows Server do prostředí Windows Recovery Environment (Win RE).

2. Ze tří dostupných možností vyberte Poradce při potížích.

    ![otevření nabídky](./media/backup-azure-restore-system-state/winre-1.png)

3. Na obrazovce **Upřesnit možnosti** vyberte **příkazový řádek** a zadejte uživatelské jméno a heslo správce serveru.

   ![otevření nabídky](./media/backup-azure-restore-system-state/winre-2.png)

4. Zadejte uživatelské jméno a heslo správce serveru.

    ![otevření nabídky](./media/backup-azure-restore-system-state/winre-3.png)

5. Při otevření příkazového řádku v režimu správce spusťte následující příkaz a získejte záložní verze Stav systému.

    ```cmd
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```

    ![získat záložní verze stavsystému](./media/backup-azure-restore-system-state/winre-4.png)

6. Spusťte následující příkaz, abyste získali všechny svazky dostupné v záloze.

    ```cmd
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![získat záložní verze stavsystému](./media/backup-azure-restore-system-state/winre-5.png)

7. Následující příkaz obnoví všechny svazky, které jsou součástí zálohování stavu systému. Všimněte si, že tento krok obnoví pouze kritické svazky, které jsou součástí stavu systému. Všechna nesystémová data jsou vymazána.

    ```cmd
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```

     ![získat záložní verze stavsystému](./media/backup-azure-restore-system-state/winre-6.png)

## <a name="next-steps"></a>Další kroky

* Nyní, když jste obnovili soubory a složky, můžete [spravovat zálohy](backup-azure-manage-windows-server.md).
