---
title: 'Zálohování Azure: Obnovení stavu systému na Windows Server | Microsoft Docs'
description: Krok podle kroku vysvětlení pro obnovení stavu systému Windows Server ze zálohy v Azure.
services: backup
documentationcenter: ''
author: saurabhsensharma
manager: shivamg
editor: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/18/2017
ms.author: saurse;trinadhk;markgal;
ms.openlocfilehash: c673cca6a35cfdc0edaecdc69a797f48772d847c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="restore-system-state-to-windows-server"></a>Obnovení stavu systému na Windows Server

Tento článek vysvětluje způsob obnovení zálohy stavu systému Windows Server z trezoru služby Azure Recovery Services. Chcete-li obnovit stav systému, musíte mít zálohu stavu systému (vytvořili pomocí pokynů v [zálohování stavu systému](backup-azure-system-state.md#back-up-windows-server-system-state)a ujistěte se, že máte nainstalovanou [nejnovější verzi systému Microsoft Azure Recovery Services (MARS) Agent](http://aka.ms/azurebackup_agent). Obnovení dat stavu systému Windows Server z trezoru služby Azure Recovery Services je dvoustupňový proces:

1. Obnovení stavu systému souborů z Azure Backup. Při obnovení stavu systému jako soubory ze zálohy Azure, můžete se buď:
  * Obnovení stavu systému na stejný server kterém byly provedeny zálohy, nebo
  * Obnovit stav systému souborů na alternativní server.

2. Použít obnovené soubory stavu systému pro Windows Server.


## <a name="recover-system-state-files-to-the-same-server"></a>Obnovení stavu systému souborů na stejný server
Následující kroky popisují, jak vrátit konfiguraci systému Windows Server do předchozího stavu. Vracení konfigurace serveru známé, stabilního stavu, může být velmi důležité. Následující postup obnovení stavu systému serveru z trezoru služeb zotavení. 

1. Otevřete modul snap-in **Microsoft Azure Backup**. Pokud si nejste jisti, kde nainstalovaný modul snap-in, hledání se počítač nebo server pro **Microsoft Azure Backup**.

    Desktopová aplikace by se ve výsledcích hledání.

2. Klikněte na tlačítko **obnovit Data** spusťte průvodce.

    ![Obnovení dat](./media/backup-azure-restore-windows-server/recover.png)

3. Na **Začínáme** , chcete-li obnovit data na stejném serveru nebo počítače, vyberte **tento server (`<server name>`)** a klikněte na tlačítko **Další**.

    ![Vyberte tuto možnost serveru k obnovení dat na stejný počítač](./media/backup-azure-restore-system-state/samemachine.png)

4. Na **vyberte režimu obnovení** podokně vyberte **stav systému** a pak klikněte na **Další**.

    ![Procházet soubory](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. V kalendáři v **vyberte svazek a datum** podokně, vyberte obnovení bodu. 

    Můžete obnovit z libovolného obnovení bodu v čase. Data v **tučné** označuje dostupnost alespoň jeden bod obnovení. Jakmile vyberete datum, pokud jsou k dispozici více bodů obnovení, vyberte konkrétní bod obnovení z **čas** rozevírací nabídce.

    ![Svazek a datum](./media/backup-azure-restore-system-state/select-date.png)

6. Po zadání bodu obnovení pro obnovení, klikněte na tlačítko **Další**.

    Zálohování Azure připojí bod místní obnovení a používá je jako svazek obnovení.

7. V podokně Další zadejte cíl pro obnovené soubory stavu systému a klikněte na tlačítko **Procházet** otevřete Průzkumníka Windows a soubory a složky, které chcete najít. Možnost **vytvořit kopie tak, aby obě verze**, vytváří kopie jednotlivé soubory do stávajícího archivu soubor stavu systému místo vytvoření kopie celý archivu stavu systému.

    ![Možnosti obnovení](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Zkontrolujte podrobnosti o obnovení na **potvrzení** panelu a klikněte na tlačítko **obnovit**.

   ![Klikněte na tlačítko Obnovit na vědomí akce obnovení](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Kopírování *WindowsImageBackup* adresář v cílovém umístění obnovení na svazek nekritické serveru. Svazek operačního systému Windows je obvykle nepostradatelný svazek.

10. Po obnovení je úspěšné, postupujte podle kroků v části [použít obnovit stav systému souborů pro Windows Server](backup-azure-restore-system-state.md#apply-restored-system-state-files-to-the-windows-server), k dokončení procesu obnovení stavu systému.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Obnovení stavu systému souborů na alternativní server

Pokud váš Server systému Windows je poškozená nebo je nepřístupný a chcete ho obnovit stabilního stavu pomocí obnovení stavu systému Windows Server, můžete obnovit stav systému poškozená serveru z jiného serveru. Použijte následující postup obnovení stavu systému na samostatný server.  

Zahrnuje technologiím použitým v těchto kroků:

- *Zdrojový počítač* – původní počítač, ze kterého bylo provedeno zálohování a který není aktuálně k dispozici.
- *Cílový počítač* – počítače, do níž se obnovuje data.
- *Ukázka trezoru* – trezor služeb zotavení, ke kterému *zdrojový počítač* a *cílový počítač* jsou registrované. <br/>

> [!NOTE]
> Zálohy vytvořené z jednoho počítače nelze obnovit na počítač se starší verzí operačního systému. Například zálohy pořízené z počítače systému Windows Server 2016 nelze obnovit na Windows Server 2012 R2. Inverzní je ale možné. Zálohování z Windows serveru 2012 R2 můžete použít k obnovení systému Windows Server 2016.
>

1. Otevřete **Microsoft Azure Backup** modul snap-in na *cílový počítač*.
2. Ujistěte se, že *cílový počítač* a *zdrojový počítač* jsou registrované ke stejnému trezoru služeb zotavení.
3. Klikněte na tlačítko **obnovit Data** inicializace pracovního postupu.
4. Vyberte **jiný server**

    ![Jiný Server](./media/backup-azure-restore-system-state/anotherserver.png)

5. Zadejte soubor s přihlašovacími údaji trezoru, která odpovídá *ukázka trezoru*. Pokud soubor s přihlašovacími údaji trezoru je neplatný (nebo vypršela platnost), stáhněte si nový soubor přihlašovacích údajů trezoru z *ukázka trezoru* na portálu Azure. Jakmile je zadaný soubor s přihlašovacími údaji trezoru, se zobrazí trezor služeb zotavení přidružený soubor s přihlašovacími údaji trezoru.

6. V podokně vyberte Server Backup, vyberte *zdrojový počítač* ze seznamu zobrazených počítačů.
7. V podokně vyberte režimu obnovení, vyberte **stav systému** a klikněte na tlačítko **Další**. 

    ![Search](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. V kalendáři v **vyberte svazek a datum** podokně, vyberte obnovení bodu. Můžete obnovit z libovolného obnovení bodu v čase. Data v **tučné** označuje dostupnost alespoň jeden bod obnovení. Jakmile vyberete datum, pokud jsou k dispozici více bodů obnovení, vyberte konkrétní bod obnovení z **čas** rozevírací nabídce. 

    ![Hledání položek](./media/backup-azure-restore-system-state/select-date.png)

9. Po zadání bodu obnovení pro obnovení, klikněte na tlačítko **Další**.

10. Na **vyberte režim obnovení stavu systému** podokně zadejte cíl, kde chcete soubory stavu systému obnovit a pak klikněte na **Další**.

    ![Šifrování](./media/backup-azure-restore-system-state/recover-as-files.png)

    Možnost **vytvořit kopie tak, aby obě verze**, vytváří kopie jednotlivé soubory do stávajícího archivu soubor stavu systému místo vytvoření kopie celý archivu stavu systému.

11. Zkontrolujte podrobnosti o obnovení v podokně potvrzení a klikněte na tlačítko **obnovit**. 

    ![Klikněte na tlačítko Obnovit potvrďte proces obnovení](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Kopírování *WindowsImageBackup* adresář nekritické svazku serveru (například D:\). Svazek operačního systému Windows je obvykle nepostradatelný svazek.

13. Chcete-li dokončit proces obnovy, použijte následující části [použít obnovené soubory stavu systému v systému Windows Server](backup-azure-restore-system-state.md#apply-restored-system-state-on-a-windows-server).




## <a name="apply-restored-system-state-on-a-windows-server"></a>Použít obnovený stav systému v systému Windows Server

Jednou obnovily stavu systému jako soubory pomocí agenta služeb zotavení Azure, použijte nástroj zálohování systému Windows Server použít obnovené stavu systému na Windows Server. Nástroj Zálohování systému Windows Server je již k dispozici na serveru. Následující kroky popisují, jak použít obnovené stav systému.

1. Použijte následující příkazy k restartování serveru v *režimu obnovení adresářových služeb*. V řádku se zvýšenými oprávněními příkaz:

    ```
    PS C:\> Bcdedit /set safeboot dsrepair
    PS C:\> Shutdown /r /t 0
    ```

2. Po restartování otevřete modul snap-in Zálohování serveru. Pokud si nejste jisti, kde nainstalovaný modul snap-in, hledání se počítač nebo server pro **zálohování serveru**.

    Desktopová aplikace se zobrazí ve výsledcích hledání.

3. V modulu snap-in, vyberte **místní záloha**.

    ![Vyberte místní zálohu k obnovení z tohoto umístění](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. V konzole místní záloha v **podokna akce**, klikněte na tlačítko **obnovit** otevřete Průvodce obnovením.

5. Vyberte možnost, **zálohy uložené v jiném umístění**a klikněte na tlačítko **Další**.

   ![Vyberte k obnovení na jiný server](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. Při zadávání typ umístění, vyberte **vzdálené sdílené složce** Pokud záloha stavu systému se obnovení provádělo na jiný server. Pokud stav systému byla obnovena místně, pak vyberte **místní jednotky**. 

    ![Vyberte, zda se má obnovení z místního serveru nebo jiné](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Zadejte cestu k *WindowsImageBackup* adresář, nebo zvolte místní jednotku, která obsahuje tento adresář (například D:\WindowsImageBackup), obnovit jako součást obnovení stavu systému souborů pomocí služeb zotavení Azure Agent a klikněte na tlačítko **Další**.

    ![Cesta pro sdílený soubor](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Vyberte verzi stav systému, kterou chcete obnovit a klikněte na tlačítko **Další**.

9. V podokně Vybrat typ obnovení, vyberte **stav systému** a klikněte na tlačítko **Další**.

10. Pro umístění obnovení stavu systému, vyberte **původního umístění**a klikněte na tlačítko **Další**.

11. Zkontrolujte podrobnosti o potvrzení, ověřte nastavení restartování a klikněte na tlačítko **obnovit** k applly obnovený stav systému souborů.

    ![spuštění obnovení stavu systému souborů](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Zvláštní upozornění pro obnovení stavu systému na serveru služby Active Directory

Zálohování stavu systému zahrnuje data služby Active Directory. Použijte následující kroky k obnovení služby Active Directory Domain Services (AD DS) z jeho aktuální stav do předchozího stavu.

1. Restartujte řadič domény v adresářových služeb obnovení režimu (DSRM).
2. Postupujte podle kroků [sem](https://technet.microsoft.com/library/cc794755(v=ws.10).aspx) použití rutiny zálohování serveru k obnovení služby AD DS.


## <a name="troubleshoot-failed-system-state-restore"></a>Řešení potíží se nezdařilo obnovení stavu systému

Pokud předchozí proces použití stavu systému úspěšně nedokončí, použijte prostředí Windows Recovery Environment (Win RE) k obnovení systému Windows Server. Následující kroky popisují, jak obnovit pomocí Win znovu. Tuto možnost použijte pouze v případě systému Windows Server není normálnímu spuštění po obnovení stavu systému. Následující proces vymaže data nesystémové, buďte opatrní. 

1. Spouštění systému Windows Server do prostředí Windows Recovery Environment (Windows RE).

2. Vyberte Poradce při potížích ze tří dostupných možností.

    ![Otevření nabídky](./media/backup-azure-restore-system-state/winre-1.png)

3. Z **pokročilé možnosti** obrazovku, vyberte **příkazového řádku** a zadejte uživatelské jméno správce serveru a heslo.

   ![Otevření nabídky](./media/backup-azure-restore-system-state/winre-2.png)

4. Zadejte uživatelské jméno správce serveru a heslo.

    ![Otevření nabídky](./media/backup-azure-restore-system-state/winre-3.png)

5. Když otevřete příkazový řádek v režimu správce, spusťte následující příkaz k získání verzí zálohování stavu systému.

    ```
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```
    ![získání verze zálohování stavu systému](./media/backup-azure-restore-system-state/winre-4.png)

6. Spusťte následující příkaz získat všechny svazky, které jsou k dispozici v zálohování.

    ```
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![získání verze zálohování stavu systému](./media/backup-azure-restore-system-state/winre-5.png)

7. Následující příkaz obnoví všechny svazky, které jsou součástí zálohování stavu systému. Všimněte si, že tento krok obnoví jenom nepostradatelné svazky, které jsou součástí stavu systému. Všechna nesystémové data budou vymazána.

    ```
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```
     ![získání verze zálohování stavu systému](./media/backup-azure-restore-system-state/winre-6.png)



## <a name="next-steps"></a>Další postup
* Teď, když jste obnovit soubory a složky, můžete [spravovat zálohy](backup-azure-manage-windows-server.md).
