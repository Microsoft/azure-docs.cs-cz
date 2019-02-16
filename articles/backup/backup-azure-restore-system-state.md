---
title: 'Azure Backup: Obnovení stavu systému na Windows Server'
description: Krok vysvětlení krok pro obnovení stavu systému Windows Server ze zálohy v Azure.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 8/18/2017
ms.author: saurse
ms.openlocfilehash: ab307548853a545c4aa8ee5a573ca5b1ca67c91d
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310248"
---
# <a name="restore-system-state-to-windows-server"></a>Obnovení stavu systému na Windows Server

Tento článek vysvětluje postup obnovení zálohy stavu systému Windows Server z trezoru služby Azure Recovery Services. Chcete-li obnovit stav systému, musíte mít zálohu stavu systému (vytvořili pomocí pokynů v [zálohování stavu systému](backup-azure-system-state.md#back-up-windows-server-system-state)a ujistěte se, že jste si nainstalovali [nejnovější verzi sady Microsoft Azure Recovery Services (MARS) Agent](https://aka.ms/azurebackup_agent). Obnovení dat stavu systému Windows Server z trezoru služby Azure Recovery Services je dvoustupňový proces:

1. Obnovení stavu systému souborů z Azure Backup. Při obnovení stavu systému jako soubory ze zálohy Azure, můžete se buď:
  * Obnovení stavu systému na stejný server ve kterém byly provedeny zálohy, nebo
  * Obnovení stavu systému souborů na alternativní server.

2. Použijte obnovený stav systému souborů na Windows Server.


## <a name="recover-system-state-files-to-the-same-server"></a>Obnovení stavu systému souborů na stejný server
Následující kroky popisují, jak vrátit zpět konfiguraci Windows serveru do předchozího stavu. Konfigurace serveru vrácení zpět do stavu známé a stabilní, může být velmi důležité. Následující postup obnovení stavu systému serveru z trezoru služby Recovery Services. 

1. Otevřete modul snap-in **Microsoft Azure Backup**. Pokud si nejste jisti, kde nainstalovaný modul snap-in, vyhledávat počítač nebo server pro **Microsoft Azure Backup**.

    Desktopové aplikace by se zobrazit ve výsledcích hledání.

2. Klikněte na tlačítko **obnovit Data** spusťte průvodce.

    ![Obnovení dat](./media/backup-azure-restore-windows-server/recover.png)

3. Na **Začínáme** podokno, chcete-li obnovit data na stejný server nebo počítač, vyberte **tento server (`<server name>`)** a klikněte na tlačítko **Další**.

    ![Tato možnost server pro obnovení dat na stejný počítač](./media/backup-azure-restore-system-state/samemachine.png)

4. Na **vybrat režim obnovení** podokně zvolte **stavu systému** a potom klikněte na tlačítko **Další**.

    ![Procházet soubory](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. V kalendáři v **vybrat svazek a datum** podokně, vyberte obnovení bodu. 

    Z libovolného bodu obnovení můžete obnovit v čase. Data zobrazená **tučné** označení dostupnosti alespoň jeden bod obnovení. Jakmile vyberete datum, pokud jsou k dispozici více bodů obnovení, vyberte konkrétní bod obnovení z **čas** rozevírací nabídky.

    ![Svazek a datum](./media/backup-azure-restore-system-state/select-date.png)

6. Když vyberete bod obnovení pro obnovení, klikněte na tlačítko **Další**.

    Azure Backup připojí bod obnovení pro místní a použije je jako svazek pro obnovení.

7. V dalším podokně, zadejte cílovou složku pro obnovený stav systému souborů a klikněte na tlačítko **Procházet** otevřete Průzkumníka Windows a vyhledejte soubory a složky, které mají. Možnost **vytvořit kopie tak, aby obě verze**, vytváří kopie jednotlivých souborů v archivu existující soubor stavu systému místo vytvoření kopie celý archiv stavu systému.

    ![Možnosti obnovení](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Ověřte podrobnosti obnovení **potvrzení** podokně a klepněte na **obnovit**.

   ![Klikněte na obnovit a potvrďte akci obnovení](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Kopírovat *WindowsImageBackup* adresáře v cílovém umístění obnovení pro méně náročné svazku serveru. Svazek s operačním systémem Windows je obvykle nepostradatelného svazku.

10. Po úspěšném obnovení postupujte podle kroků v části [použít obnovit soubory se stavem systému na Windows Server](backup-azure-restore-system-state.md), dokončete proces obnovení stavu systému.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Obnovení stavu systému souborů na alternativní server

Pokud systému Windows Server je poškozená nebo nepřístupná, a chcete obnovit do stabilního stavu pomocí obnovení stavu systému Windows Server, můžete obnovit stav systému poškozený serveru z jiného serveru. Použijte následující postup obnovení stavu systému na samostatném serveru.  

Zahrnuje terminologii používané v těchto krocích:

- *Zdrojový počítač* – původní počítač, ze kterého bylo provedeno zálohování a který není aktuálně k dispozici.
- *Cílový počítač* – počítače, na který se obnovuje data.
- *Ukázkové úložiště* – trezor Recovery Services ke kterému *zdrojový počítač* a *cílový počítač* jsou registrované. <br/>

> [!NOTE]
> Zálohy pořízené z jednoho počítače nelze obnovit do počítače se starší verzí operačního systému. Například není možné obnovit zálohy pořízené z počítače s Windows serverem 2016 do systému Windows Server 2012 R2. Inverzní je však možné. Zálohování z Windows serveru 2012 R2 můžete použít k obnovení Windows serveru 2016.
>

1. Otevřít **Microsoft Azure Backup** modul snap-in *cílový počítač*.
2. Ujistěte se, že *cílový počítač* a *zdrojový počítač* zaregistrováni do stejného trezoru služby Recovery Services.
3. Klikněte na tlačítko **obnovit Data** k zahájení pracovního postupu.
4. Vyberte **jiný server**

    ![Another Server](./media/backup-azure-restore-system-state/anotherserver.png)

5. Zadejte soubor přihlašovacích údajů trezoru, který odpovídá *ukázka trezor*. Pokud soubor s přihlašovacími údaji trezoru je neplatný (nebo jejichž platnost vypršela), stáhněte si nový soubor přihlašovacích údajů trezoru z *ukázka trezor* na webu Azure Portal. Když soubor s přihlašovacími údaji trezoru je k dispozici, zobrazí se trezor služby Recovery Services přidružený soubor s přihlašovacími údaji trezoru.

6. V podokně vyberte Server Backup, vyberte *zdrojový počítač* ze seznamu zobrazených počítačů.
7. V podokně Vybrat režim obnovení, zvolte **stavu systému** a klikněte na tlačítko **Další**. 

    ![Search](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. V kalendáři v **vybrat svazek a datum** podokně, vyberte obnovení bodu. Z libovolného bodu obnovení můžete obnovit v čase. Data zobrazená **tučné** označení dostupnosti alespoň jeden bod obnovení. Jakmile vyberete datum, pokud jsou k dispozici více bodů obnovení, vyberte konkrétní bod obnovení z **čas** rozevírací nabídky. 

    ![Hledat položky](./media/backup-azure-restore-system-state/select-date.png)

9. Když vyberete bod obnovení pro obnovení, klikněte na tlačítko **Další**.

10. Na **vybrat režim obnovení stavu systému** podokně, zadejte cílovou složku, kam chcete soubory obnovit, a pak klikněte na tlačítko se stavem systému **Další**.

    ![Šifrování](./media/backup-azure-restore-system-state/recover-as-files.png)

    Možnost **vytvořit kopie tak, aby obě verze**, vytváří kopie jednotlivých souborů v archivu existující soubor stavu systému místo vytvoření kopie celý archiv stavu systému.

11. Ověřte podrobnosti o obnovení v podokně potvrzení a klikněte na tlačítko **obnovit**. 

    ![Klikněte na tlačítko Obnovit potvrďte proces obnovení](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Kopírovat *WindowsImageBackup* adresáře pro méně náročné svazku serveru (například D:\). Svazek s operačním systémem Windows je obvykle nepostradatelného svazku.

13. Chcete-li dokončit proces obnovy, použijte následující část, která [obnovené soubory se stavem systému na Windows serveru použít](backup-azure-restore-system-state.md#apply-restored-system-state-on-a-windows-server).




## <a name="apply-restored-system-state-on-a-windows-server"></a>Použít obnovený stav systému na Windows serveru

Jednou zotavily stavu systému jako soubory pomocí agenta Azure Recovery Services, pomocí nástroje Zálohování Windows serveru použít obnovený stav systému na Windows Server. Nástroj Zálohování systému Windows Server je již k dispozici na serveru. Následující postup vysvětluje, jak použít obnovený stav systému.

1. Použijte následující příkazy k restartování serveru v *režimu oprav adresářových služeb*. V příkazovém řádku se zvýšenými oprávněními:

    ```
    PS C:\> Bcdedit /set safeboot dsrepair
    PS C:\> Shutdown /r /t 0
    ```

2. Po restartování počítače otevřete modul snap-in Zálohování systému Windows Server. Pokud si nejste jisti, kde nainstalovaný modul snap-in, vyhledávat počítač nebo server pro **zálohování Windows serveru**.

    Desktopové aplikace se zobrazí ve výsledcích hledání.

3. V modulu snap-in, vyberte **místní záloha**.

    ![Vyberte místní zálohu pro obnovení z něj](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. V konzole místní záloha v **podokna akcí**, klikněte na tlačítko **obnovit** otevřete Průvodce obnovením.

5. Vyberte možnost, **zálohy uložené v jiném umístění**a klikněte na tlačítko **Další**.

   ![Vyberte k obnovení na jiný server](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. Při určování typu umístění, vyberte **Vzdálená sdílená složka** Pokud zálohování stavu systému byl obnoven na jiný server. Pokud stavu systému byl obnoven místně, můžete vybrat **místní jednotky**. 

    ![Vyberte, zda se má obnovení z místního serveru nebo jiné](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Zadejte cestu k *WindowsImageBackup* adresáře, nebo zvolte místní jednotce, která obsahuje tento adresář (třeba D:\WindowsImageBackup), obnovit jako součást obnovení stavu systému souborů pomocí služby Azure Recovery Services Agent a klikněte na tlačítko **Další**.

    ![cestu ke sdílenému souboru](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Vyberte verzi stavu systému, kterou chcete obnovit a klikněte na **Další**.

9. V podokně výběr typu obnovení, vyberte **stavu systému** a klikněte na tlačítko **Další**.

10. Umístění obnovení stavu systému, vyberte **původního umístění**a klikněte na tlačítko **Další**.

11. Projděte si podrobnosti o potvrzení, ověřte restartování nastavení a klikněte na tlačítko **obnovit** použít obnovený stav systému souborů.

    ![spuštění obnovení stavu systému souborů](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Zvláštní upozornění pro obnovení stavu systému na serveru služby Active Directory

Zálohování stavu systému obsahuje data služby Active Directory. Následujícím postupem obnovit Active Directory Domain Services (AD DS) v jejím aktuálním stavu do předchozího stavu.

1. Restartování řadiče domény v adresáři režimu obnovení služeb (DSRM).
2. Postupujte podle kroků [tady](https://technet.microsoft.com/library/cc794755(v=ws.10).aspx) použití rutiny zálohování Windows serveru k obnovení služby AD DS.


## <a name="troubleshoot-failed-system-state-restore"></a>Řešení potíží se nezdařilo obnovení stavu systému

Pokud předchozí proces použití stavu systému nepodaří úspěšně vyřídit, použijte prostředí Windows Recovery Environment (Windows RE) k obnovení systému Windows Server. Následující postup vysvětluje, jak provést obnovení pomocí prostředí Windows RE. Tuto možnost použijte jenom v případě systému Windows Server nespustil normálně po obnovení stavu systému. Následující proces vymaže nesystémové dat, buďte opatrní. 

1. Spouštění systému Windows Server do prostředí Windows Recovery Environment (Windows RE).

2. Poradce při potížích vyberte ze tří dostupných možností.

    ![Otevření nabídky](./media/backup-azure-restore-system-state/winre-1.png)

3. Z **rozšířené možnosti** obrazovky, vyberte **příkazového řádku** a zadejte uživatelské jméno správce serveru a heslo.

   ![Otevření nabídky](./media/backup-azure-restore-system-state/winre-2.png)

4. Zadejte uživatelské jméno správce serveru a heslo.

    ![Otevření nabídky](./media/backup-azure-restore-system-state/winre-3.png)

5. Když otevřete příkazový řádek v režimu správce spusťte následující příkaz k získání verzí zálohování stavu systému.

    ```
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```
    ![Získání verzí zálohování stavu systému](./media/backup-azure-restore-system-state/winre-4.png)

6. Spuštěním následujícího příkazu Získejte všechny svazky, které jsou k dispozici v záloze.

    ```
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![Získání verzí zálohování stavu systému](./media/backup-azure-restore-system-state/winre-5.png)

7. Následující příkaz obnoví všechny svazky, které jsou součástí zálohování stavu systému. Všimněte si, že tento krok obnoví jenom nepostradatelné svazky, které jsou součástí stavu systému. Všechna data nesystémové se vymažou.

    ```
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```
     ![Získání verzí zálohování stavu systému](./media/backup-azure-restore-system-state/winre-6.png)



## <a name="next-steps"></a>Další postup
* Teď, když jste obnovit soubory a složky, můžete [správa záloh](backup-azure-manage-windows-server.md).
