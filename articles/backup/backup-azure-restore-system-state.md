---
title: Obnovení stavu systému na Windows Server
description: Podrobné vysvětlení obnovení stavu systému Windows Server ze zálohy v Azure.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 4ef23d6ff16c263e310304cc240c2090751640b1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97008465"
---
# <a name="restore-system-state-to-windows-server"></a>Obnovení stavu systému na Windows Server

Tento článek vysvětluje, jak obnovit zálohy stavu systému Windows Server z trezoru služby Azure Recovery Services. Chcete-li obnovit stav systému, je nutné mít zálohu stavu systému (vytvořenou pomocí pokynů v části [stav systému zálohování](backup-azure-system-state.md#back-up-windows-server-system-state)) a ujistěte se, že jste nainstalovali [nejnovější verzi agenta Microsoft Azure Recovery Services (MARS)](https://aka.ms/azurebackup_agent). Obnova dat stavu systému Windows Server z trezoru služby Azure Recovery Services je proces se dvěma kroky:

1. Obnoví stav systému jako soubory z Azure Backup. Když obnovujete stav systému jako soubory z Azure Backup, můžete:
   * Obnovte stav systému na stejný server, na kterém byly zálohy pořízeny, nebo
   * Obnovte soubor stavu systému na alternativním serveru.

2. Pomocí nástroje Zálohování Windows Serveru použijte obnovené soubory stavu systému na Windows Server.

## <a name="recover-system-state-files-to-the-same-server"></a>Obnovení souborů se stavem systému na stejný server

Následující postup vysvětluje, jak vrátit konfiguraci Windows serveru do předchozího stavu. Vracení konfigurace serveru zpátky na známý, stabilní stav může být extrémně cenné. Následující kroky obnoví stav systému serveru z trezoru Recovery Services.

1. Otevřete modul snap-in **Microsoft Azure Backup**. Pokud si nejste jisti, kde byl modul snap-in nainstalován, vyhledejte **Microsoft Azure Backup** v počítači nebo na serveru.

    Aplikace klasické pracovní plochy by se měla zobrazit ve výsledcích hledání.

2. Vyberte **obnovit data** a spusťte průvodce.

    ![Obnovení dat](./media/backup-azure-restore-windows-server/recover.png)

3. Chcete-li obnovit data na stejném serveru nebo počítači, vyberte v podokně **Začínáme** možnost **Tento server ( `<server name>` )** a vyberte možnost **Další**.

    ![Zvolením možnosti Server obnovte data do stejného počítače.](./media/backup-azure-restore-system-state/samemachine.png)

4. V podokně **Vybrat režim obnovení** zvolte možnost **stav systému** a pak vyberte možnost **Další**.

    ![Procházet soubory](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. V kalendáři v podokně **Vybrat svazek a datum** vyberte bod obnovení.

    Můžete obnovit z libovolného bodu obnovení v čase. Kalendářní data **tučně** označují dostupnost alespoň jednoho bodu obnovení. Jakmile vyberete datum, pokud je k dispozici více bodů obnovení, zvolte konkrétní bod obnovení z rozevírací nabídky **čas** .

    ![Svazek a datum](./media/backup-azure-restore-system-state/select-date.png)

6. Až zvolíte bod obnovení, který se má obnovit, vyberte **Další**.

    Azure Backup připojí místní bod obnovení a použije ho jako svazek pro obnovení.

7. V dalším podokně určete cílové umístění obnovených souborů stavu systému. Pak vyberte **Procházet** a otevřete Průzkumníka Windows a najděte požadované soubory a složky. Možnost **vytvoří kopie, takže máte obě verze**, vytvoří kopie jednotlivých souborů v existujícím archivu souborů stavu systému místo vytvoření kopie celého archivu stavu systému.

    ![Možnosti obnovení](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Ověřte podrobnosti o obnovení v podokně **potvrzení** a vyberte možnost **obnovit**.

   ![Výběrem možnosti obnovit potvrďte akci obnovení.](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Zkopírujte adresář *WindowsImageBackup* do umístění pro obnovení do nedůležitého svazku serveru. Nepostradatelným svazkem je obvykle svazek operačního systému Windows.

10. Po úspěšném obnovení postupujte podle kroků v části, [použijte obnovený stav systému na Windows serveru](#apply-restored-system-state-on-a-windows-server), abyste mohli dokončit proces obnovení stavu systému.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Obnovení souborů stavu systému na alternativním serveru

Pokud je váš Windows Server poškozený nebo nedostupný a chcete ho obnovit do stabilního stavu tím, že obnovujete stav systému Windows Server, můžete obnovit stav systému poškozeného serveru z jiného serveru. Následující postup použijte pro obnovení stavu systému na samostatném serveru.  

Terminologie použitá v těchto krocích zahrnuje:

* *Zdrojový počítač* – původní počítač, ze kterého byla provedena záloha, a který aktuálně není k dispozici.
* *Cílový počítač* – počítač, na který se data obnovují.
* *Vzorový trezor* – Recovery Services trezor, ke kterému jsou zaregistrované *zdrojový počítač* a *cílový počítač* .

> [!NOTE]
> Zálohy provedené z jednoho počítače se nedají obnovit na počítač, na kterém běží starší verze operačního systému. Například zálohy pořízené z počítače se systémem Windows Server 2016 nelze obnovit do systému Windows Server 2012 R2. Je však možné, že se jedná o funkci INVERT. K obnovení systému Windows Server 2016 můžete použít zálohy z Windows Serveru 2012 R2.
>

1. Otevřete modul snap-in **Microsoft Azure Backup** v *cílovém počítači*.
2. Ujistěte se, že *cílový počítač* a *zdrojový počítač* jsou zaregistrované ve stejném trezoru Recovery Services.
3. Vyberte **obnovit data** a spusťte pracovní postup.
4. Vybrat **jiný server**

    ![Jiný server](./media/backup-azure-restore-system-state/anotherserver.png)

5. Zadejte soubor s přihlašovacími údaji úložiště, který odpovídá *trezoru ukázek*. Pokud je soubor přihlašovacích údajů trezoru neplatný (nebo vypršela jeho platnost), Stáhněte si nový soubor s přihlašovacími údaji trezoru z *trezoru ukázek* v Azure Portal. Po zadání souboru s přihlašovacími údaji trezoru se zobrazí Recovery Services trezor přidružený k souboru s přihlašovacími údaji trezoru.

6. V podokně vybrat záložní server vyberte *zdrojový počítač* ze seznamu zobrazených počítačů.
7. V podokně vybrat režim obnovení zvolte možnost **stav systému** a vyberte možnost **Další**.

    ![Hledat](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. V kalendáři v podokně **Vybrat svazek a datum** vyberte bod obnovení. Můžete obnovit z libovolného bodu obnovení v čase. Kalendářní data **tučně** označují dostupnost alespoň jednoho bodu obnovení. Jakmile vyberete datum, pokud je k dispozici více bodů obnovení, zvolte konkrétní bod obnovení z rozevírací nabídky **čas** .

    ![Hledat položky](./media/backup-azure-restore-system-state/select-date.png)

9. Až zvolíte bod obnovení, který se má obnovit, vyberte **Další**.

10. V podokně **Vybrat režim obnovení stavu systému** zadejte cíl, ve kterém chcete obnovit soubory stavu systému, a pak vyberte **Další**.

    ![Šifrování](./media/backup-azure-restore-system-state/recover-as-files.png)

    Možnost **vytvoří kopie, takže máte obě verze**, vytvoří kopie jednotlivých souborů v existujícím archivu souborů stavu systému místo vytvoření kopie celého archivu stavu systému.

11. Ověřte podrobnosti o obnovení v podokně potvrzení a vyberte možnost **obnovit**.

    ![Kliknutím na tlačítko Obnovit potvrďte proces obnovení.](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Zkopírujte adresář *WindowsImageBackup* do nedůležitého svazku serveru (například D:) \) . Nepostradatelným svazkem je obvykle svazek operačního systému Windows.

13. K dokončení procesu obnovení použijte následující část k [použití obnovených souborů stavu systému na Windows serveru](#apply-restored-system-state-on-a-windows-server).

## <a name="apply-restored-system-state-on-a-windows-server"></a>Použití obnoveného stavu systému na Windows Serveru

Jakmile obnovíte Stav systému jako soubory pomocí služby Azure Recovery Services agent, použijte nástroj Zálohování Windows Serveru a nainstalujte obnovený stav systému na Windows Server. Nástroj Zálohování Windows Serveru je již na serveru k dispozici. Následující kroky vysvětlují, jak použít obnovený stav systému.

1. Otevřete modul snap-in Zálohování Windows Serveru. Pokud si nejste jisti, kde byl modul snap-in nainstalován, vyhledejte **zálohování Windows serveru** v počítači nebo na serveru.

    Aplikace klasické pracovní plochy se zobrazí ve výsledcích hledání. Pokud se nezobrazí nebo dojde k chybám při otevření aplikace, je třeba nainstalovat **funkce zálohování Windows serveru** a závislé komponenty pod ní, které jsou k dispozici v **Průvodci přidáním funkcí** v **Správce serveru**.

1. V modulu snap-in vyberte **místní zálohování**.

    ![Vyberte místní zálohování, ze kterého se má obnovit.](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

1. V konzole místní zálohování klikněte v **podokně akce** na možnost **obnovit** a otevřete Průvodce obnovením.

1. Vyberte možnost **zálohování uložené v jiném umístění** a vyberte **Další**.

   ![Výběr obnovení na jiný server](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

1. Když zadáte typ umístění, vyberte **Vzdálená sdílená složka** , pokud se vaše záloha stavu systému obnovila na jiný server. Pokud se stav systému obnovil místně, vyberte **místní jednotky**.

    ![Vyberte, jestli se má obnovení z místního serveru nebo jiného.](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

1. Zadejte cestu k adresáři *WindowsImageBackup* nebo zvolte místní jednotku obsahující tento adresář (například D:\WindowsImageBackup), která se obnovila jako součást obnovení souborů stavu systému pomocí agenta Azure Recovery Services a vyberte **Další**.

    ![Cesta ke sdílenému souboru](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

1. Vyberte verzi stavu systému, kterou chcete obnovit, a vyberte **Další**.

1. V podokně vybrat typ obnovení vyberte možnost **stav systému** a vyberte možnost **Další**.

1. Pro umístění obnovení stavu systému vyberte možnost **původní umístění** a vyberte možnost **Další**.

    Pokud obnovujete řadič domény, zobrazí se následující další možnost:

    ![Umístění pro obnovení stavu systému](./media/backup-azure-restore-system-state/location-for-system-state-recovery.png)

    >[!NOTE]
    >Pokud výslovně chcete provést autoritativní obnovení všech dat služby Active Directory, vyberte možnost provést autoritativní obnovení souborů služby Active Directory.

1. Přečtěte si podrobnosti o potvrzení, ověřte nastavení restartování a pak použijte obnovené soubory stavu systému a vyberte **obnovit** .

    ![Spustit obnovení souborů stavu systému](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

    >[!NOTE]
    >Pokud provádíte obnovení v režimu DSRM, nevybírejte možnost **automaticky restartovat server** .

1. Po úspěšném dokončení obnovení je nutné restartovat server v normálním režimu. Otevřete příkazový řádek a zadejte následující příkaz: `bcdedit /deletevalue safeboot`
1. Restartujte server.

## <a name="special-considerations-for-system-state-recovery-on-a-domain-controller"></a>Zvláštní požadavky na obnovení stavu systému v řadiči domény

Zálohování stavu systému zahrnuje data služby Active Directory. Pomocí následujících kroků obnovte Doména služby Active Directory služby (služba AD DS) z aktuálního stavu do předchozího stavu. Tento typ obnovení je možné provést ve dvou scénářích:

* Obnova všech dat služby Active Directory, když v doménové struktuře nejsou žádné funkční řadiče domény
* Obnovení části dat služby Active Directory, když jsou tyto objekty smazány nebo poškozeny

Tento článek bude projednávat jenom první scénář, který volá nonauthorative obnovení služba AD DS a autoritativní obnovení složky SYSVOL.  Pokud potřebujete provést druhý scénář (kde jsou stále funkční řadiče domény, ale potřebujete obnovit konkrétní objekty služby AD), podívejte se na [tyto pokyny](https://support.microsoft.com/help/840001/how-to-restore-deleted-user-accounts-and-their-group-memberships-in-ac).

1. Pomocí následujících kroků [obnovte soubory stavu systému na alternativním serveru](#recover-system-state-files-to-an-alternate-server).
1. Následující příkazy použijte k restartování serveru v *režimu opravy adresářových služeb*. Na příkazovém řádku se zvýšenými oprávněními:

    ```cmd
    Bcdedit /set safeboot dsrepair
    Shutdown /r /t 0
    ```

1. Chcete-li obnovit službu Active Directory jako součást obnovení stavu systému, můžete zvolit jednu ze dvou metod:

    * Podle výše uvedených pokynů [použijte obnovený stav systému na Windows serveru](#apply-restored-system-state-on-a-windows-server) s nástrojem zálohování Windows serveru.

        >[!NOTE]
        >Pokud obnovujete všechna data služby Active Directory (a v doménové struktuře nezbývá žádné funkční řadiče domény), v kroku 9 výše se ujistěte, že jste vybrali **provést autoritativní obnovení souborů služby Active Directory**.

    * K provedení obnovení z příkazového řádku použijte nástroj [Wbadmin](/windows-server/administration/windows-commands/wbadmin-start-systemstaterecovery) .

        Budete potřebovat identifikátor verze zálohy, kterou chcete použít. Seznam identifikátorů verzí můžete získat spuštěním tohoto příkazu:

        ```cmd
        wbadmin get versions -backuptarget <servername\sharename>
        ```

        Pak použijte tento identifikátor verze ke spuštění obnovení.

        Pokud například chcete provést [nonauthorative obnovení služba AD DS a autoritativní obnovení složky SYSVOL](/windows-server/identity/ad-ds/manage/ad-forest-recovery-nonauthoritative-restore) pomocí zálohy od 04/30/2020 v 9:00 am, která je uložená ve sdíleném prostředku `\\servername\share` pro `server01` , zadejte:

        ```cmd
        wbadmin start systemstaterecovery -version:04/30/2020-09:00 -backupTarget:\\servername\share -machine:server01 -authsysvol
        ```

1. Po úspěšném dokončení obnovení byste měli restartovat server v normálním režimu. Otevřete příkazový řádek a zadejte následující příkaz: `bcdedit /deletevalue safeboot`
1. Restartujte server.

Další informace najdete v tématu [zálohování a obnovení řadičů domény služby Active Directory](active-directory-backup-restore.md).

## <a name="troubleshoot-failed-system-state-restore"></a>Řešení potíží s nezdařeným obnovením stavu systému

Pokud se předchozí proces použití stavu systému úspěšně nedokončí, použijte k obnovení Windows serveru prostředí Windows Recovery Environment (Win RE). Následující kroky vysvětlují, jak obnovit pomocí funkce Win znovu. Tuto možnost použijte jenom v případě, že se Windows Server nespouští normálně po obnovení stavu systému. Následující postup smaže data, která nejsou systémová, buďte opatrní.

1. Spusťte Windows Server do prostředí Windows Recovery Environment (Win RE).

2. Vyberte řešení potíží ze tří dostupných možností.

    ![Vybrat řešení potíží](./media/backup-azure-restore-system-state/winre-1.png)

3. Na obrazovce **Upřesnit možnosti** vyberte **příkazový řádek** a zadejte uživatelské jméno a heslo správce serveru.

   ![Výběr příkazového řádku](./media/backup-azure-restore-system-state/winre-2.png)

4. Zadejte uživatelské jméno a heslo správce serveru.

    ![Zadejte heslo](./media/backup-azure-restore-system-state/winre-3.png)

5. Když otevřete příkazový řádek v režimu správce, spusťte následující příkaz, který získá verzi zálohy stavu systému.

    ```cmd
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```

    ![Získat verze zálohy stavu systému](./media/backup-azure-restore-system-state/winre-4.png)

6. Spusťte následující příkaz, který načte všechny svazky, které jsou k dispozici v záloze.

    ```cmd
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![Získat všechny dostupné svazky](./media/backup-azure-restore-system-state/winre-5.png)

7. Následující příkaz obnoví všechny svazky, které jsou součástí zálohy stavu systému. Všimněte si, že tento krok obnovuje jenom důležité svazky, které jsou součástí stavu systému. Všechna data, která nejsou v systému, se vymažou.

    ```cmd
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```

     ![Obnovit všechny svazky](./media/backup-azure-restore-system-state/winre-6.png)

## <a name="next-steps"></a>Další kroky

* Teď, když jste obnovili své soubory a složky, můžete [Spravovat zálohy](backup-azure-manage-windows-server.md).
