---
title: Obnovení dat v Azure pro Windows Server nebo počítač se systémem Windows
description: Zjistěte, jak obnovit data uložená v Azure a Windows Server nebo počítač se systémem Windows.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 1/4/2018
ms.author: saurse
ms.openlocfilehash: 16f0460dea75b0dc52c3852d9947db0ad15f8fbe
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606320"
---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-resource-manager-deployment-model"></a>Obnovení souborů na serveru Windows nebo klientském počítači s využitím modelu nasazení Resource Manager

Tento článek vysvětluje, jak obnovit data z úložiště záloh. Chcete-li obnovit data, použijte Průvodce obnovení dat v agentovi nástroje Microsoft Azure Recovery Services (MARS). Při obnovování dat, je možné:

* Obnovení dat na stejný počítač, ve kterém byly provedeny zálohy.
* Obnovte data do alternativní počítače.

V lednu 2017 společnost Microsoft vydala Preview aktualizace agenta MARS. Společně s oprav chyb tato aktualizace umožňuje rychlé obnovení, která umožňuje připojit obnovení zapisovatelného snímku bodu jako svazek obnovení. Pak můžete zkoumat obnovení svazku a zkopírujte soubory do místního počítače a následné obnovení souborů.

> [!NOTE]
> [Ledna 2017 Azure Backup aktualizace](https://support.microsoft.com/en-us/help/3216528?preview) je povinný, pokud chcete obnovit data pomocí funkce Rychlé obnovení. Zálohovaná data musí být chráněna v trezorů v národní prostředí, které jsou uvedené v článku podpory. Obrátit [ledna 2017 Azure Backup aktualizace](https://support.microsoft.com/en-us/help/3216528?preview) nejnovější seznam národních prostředí, které podporují rychlé obnovení. Rychlé obnovení je **není** aktuálně k dispozici ve všech národních prostředí.
>

Pomocí rychlé obnovení s trezory služeb zotavení na portálu Azure. Pokud data uložena v trezory Backup, byla převedena do trezory služeb zotavení. Pokud chcete použít rychlé obnovení, stažení aktualizace MARS a postupujte podle pokynů, které zmínili, rychlé obnovení.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Obnovit data do stejného počítače pomocí funkce Rychlé obnovení

Pokud jste omylem odstranit soubor a chcete obnovit do stejného počítače (ze kterého dochází k zálohování), následující kroky vám pomohou obnovit data.

1. Otevřete **Microsoft Azure Backup** přichycení v. Pokud si nejste jisti, kde byl nainstalován modul snap-in, hledání se počítač nebo server pro **Microsoft Azure Backup**.

    Desktopová aplikace by se ve výsledcích hledání.

2. Klikněte na tlačítko **obnovit Data** spusťte průvodce.

    ![Obnovení dat](./media/backup-azure-restore-windows-server/recover.png)

3. Na **Začínáme** , chcete-li obnovit data na stejném serveru nebo počítače, vyberte **tento server (`<server name>`)** a klikněte na tlačítko **Další**.

    ![Vyberte tuto možnost serveru k obnovení dat na stejný počítač](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. Na **vyberte režimu obnovení** podokně vyberte **jednotlivých souborů a složek** a pak klikněte na **Další**.

    ![Procházet soubory](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)

5. Na **vyberte svazek a datum** podokně, vyberte svazek, který obsahuje soubory nebo složky, kterou chcete obnovit.

    V kalendáři vyberte bod obnovení. Můžete obnovit z libovolného obnovení bodu v čase. Data v **tučné** označuje dostupnost alespoň jeden bod obnovení. Jakmile vyberete datum, pokud jsou k dispozici více bodů obnovení, vyberte konkrétní bod obnovení z **čas** rozevírací nabídce.

    ![Svazek a datum](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Po zadání bodu obnovení pro obnovení, klikněte na tlačítko **připojit**.

    Zálohování Azure připojí bod místní obnovení a používá je jako svazek obnovení.

7. Na **procházení a obnovit soubory** podokně klikněte na tlačítko **Procházet** otevřete Průzkumníka Windows a soubory a složky, které chcete najít.

    ![Možnosti obnovení](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)


8. V Průzkumníku Windows zkopírujte soubory nebo složky, kterou chcete obnovit a vložit do libovolného umístění místní k serveru nebo počítači. Můžete otevřít nebo stream soubory přímo ze svazku obnovení a ověřte, že se obnoví správné verze.

    ![Zkopírujte a vložte souborů a složek z připojeného svazku do místního umístění](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Po dokončení obnovení souborů a složek, na **procházení a obnovení souborů** podokně klikněte na tlačítko **odpojení**. Pak klikněte na tlačítko **Ano** potvrďte, že chcete odpojit svazek.

    ![Odpojte Image svazku a potvrďte](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Pokud neklikejte na odpojení, zůstane svazek obnovení připojené 6 hodin od okamžiku, kdy byla připojena. Čas připojení je však rozšířené maximálně 24 hodin, v případě probíhající kopírování souborů. Žádná operace zálohování se spustí připojený svazek. Naplánované spuštění v době, kdy je svazek připojený, zálohování se spustí po obnovení svazku je odpojené.
    >


## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>K obnovení dat alternativní počítači použít rychlé obnovení
Pokud dojde ke ztrátě celý server, můžete stále obnovit data z Azure Backup na jiný počítač. Následující kroky popisují pracovního postupu.


Zahrnuje technologiím použitým v těchto kroků:

* *Zdrojový počítač* – původní počítač, ze kterého bylo provedeno zálohování a který není aktuálně k dispozici.
* *Cílový počítač* – počítače, do níž se obnovuje data.
* *Ukázka trezoru* – trezor služeb zotavení, ke kterému *zdrojový počítač* a *cílový počítač* jsou registrované. <br/>

> [!NOTE]
> Zálohování nelze obnovit do cílového počítače, který používá starší verzi operačního systému. Například převzat ze systému Windows 7, počítač může být obnovena záloha v systému Windows 8 nebo novější, počítače. Zálohy z počítače se systémem Windows 8 není možné obnovit do počítače Windows 7.
>
>

1. Otevřete **Microsoft Azure Backup** přichycení v na *cílový počítač*.

2. Ujistěte se, *cílový počítač* a *zdrojový počítač* jsou registrované ke stejnému trezoru služeb zotavení.

3. Klikněte na tlačítko **obnovit Data** otevřete **Průvodce obnovení dat**.

    ![Obnovení dat](./media/backup-azure-restore-windows-server/recover.png)

4. Na **Začínáme** podokně, vyberte **jiný server**

    ![Jiný Server](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Zadejte soubor s přihlašovacími údaji trezoru, která odpovídá *ukázka trezoru*a klikněte na tlačítko **Další**.

    Pokud soubor s přihlašovacími údaji trezoru je neplatný (nebo vypršela platnost), stáhněte si nový soubor přihlašovacích údajů trezoru z *ukázka trezoru* na portálu Azure. Po zadání přihlašovacích údajů platné úložiště, zobrazí se název odpovídajícího úložiště záloh.


6. Na **vyberte zálohování serveru** podokně, vyberte *zdrojový počítač* ze seznamu zobrazených počítačů a zadejte heslo. Pak klikněte na tlačítko **Další**.

    ![Seznam počítačů](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Na **vyberte režimu obnovení** podokně, vyberte **jednotlivých souborů a složek** a klikněte na tlačítko **Další**.

    ![Search](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. Na **vyberte svazek a datum** podokně, vyberte svazek, který obsahuje soubory nebo složky, kterou chcete obnovit.

    V kalendáři vyberte bod obnovení. Můžete obnovit z libovolného obnovení bodu v čase. Data v **tučné** označuje dostupnost alespoň jeden bod obnovení. Jakmile vyberete datum, pokud jsou k dispozici více bodů obnovení, vyberte konkrétní bod obnovení z **čas** rozevírací nabídce.

    ![Hledání položek](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Klikněte na tlačítko **připojit** místně připojit bod obnovení jako svazek obnovení na vaše *cílový počítač*.

10. Na **procházení a obnovit soubory** podokně klikněte na tlačítko **Procházet** otevřete Průzkumníka Windows a soubory a složky, které chcete najít.

    ![Šifrování](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. V Průzkumníku Windows, zkopírujte soubory nebo složky ze svazku obnovení a vložte je do vašeho *cílový počítač* umístění. Můžete otevřít nebo stream soubory přímo ze svazku obnovení a ověřte, že se obnoví správné verze.

    ![Šifrování](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Po dokončení obnovení souborů a složek, na **procházení a obnovení souborů** podokně klikněte na tlačítko **odpojení**. Pak klikněte na tlačítko **Ano** potvrďte, že chcete odpojit svazek.

    ![Šifrování](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Pokud neklikejte na odpojení, zůstane svazek obnovení připojené 6 hodin od okamžiku, kdy byla připojena. Čas připojení je však rozšířené maximálně 24 hodin, v případě probíhající kopírování souborů. Žádná operace zálohování se spustí připojený svazek. Naplánované spuštění v době, kdy je svazek připojený, zálohování se spustí po obnovení svazku je odpojené.
    >

## <a name="troubleshooting"></a>Řešení potíží
Pokud nelze zálohy Azure úspěšně připojit svazek obnovení ani po několika minutách kliknutí na možnost **připojit** nebo nepodaří připojit svazek obnovení jedné nebo více chybám, použijte následující postup zahájíte obnovení normálně.

1.  Zrušte proces probíhající připojení v případě, že po spuštění pro několik minut.

2.  Ujistěte se, že jste na nejnovější verzi agenta Azure Backup. Chcete-li zjistit informace o verzi agenta Azure Backup, klikněte na **o Microsoft agenta služeb zotavení Azure** na **akce** podokně služby Microsoft Azure Backup konzoly a ujistěte se, že **Verze** číslo je rovna nebo vyšší než verze uvedené v [v tomto článku](https://go.microsoft.com/fwlink/?linkid=229525). Můžete si stáhnout nejnovější verzi z [sem](https://go.microsoft.com/fwLink/?LinkID=288905)

3.  Přejděte na **Správce zařízení** -> **řadiče úložiště** a ujistěte se, že je možné najít **iniciátor iSCSI společnosti Microsoft**. Pokud ji můžete najít, přímo přejděte ke kroku 7 níže. 

4.  Pokud služba iniciátoru iSCSI společnosti Microsoft nelze najít, jak je uvedeno v kroku 3, zkontrolujte, budete moci najít položku v části **Správce zařízení** -> **řadiče úložiště** názvem  **Neznámé zařízení** s ID hardwaru **ROOT\ISCSIPRT**.

5.  Klikněte pravým tlačítkem na **neznámé zařízení** a vyberte **aktualizace ovladače**.

6.  Aktualizovat ovladač tak, že vyberete možnost **vyhledání automaticky aktualizovaný ovladač softwaru**. Dokončení aktualizace by se měl změnit **neznámé zařízení** k **iniciátor iSCSI společnosti Microsoft** jak je uvedeno níže. 

    ![Šifrování](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Přejděte na **Správce úloh** -> **služby (místní počítač)** -> **služba iniciátoru iSCSI společnosti Microsoft**. 

    ![Šifrování](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)
    
8.  Restartujte službu iniciátoru iSCSI společnosti Microsoft kliknutím pravým tlačítkem na službu, kliknutím na **Zastavit** a další kliknutím pravým tlačítkem na znovu a kliknete na **spustit**.

9.  Opakujte obnovení pomocí rychlé obnovení. 

Pokud se obnovení stále nedaří, restartujte server nebo klienta. Pokud restartování není žádoucí nebo obnovení stále nedaří i po restartování serveru, pokuste obnovení provést z alternativní počítače a obraťte se na podporu Azure přechodem na [portálu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a odeslání žádosti o podporu.

## <a name="next-steps"></a>Další postup
* Teď, když jste obnovit soubory a složky, můžete [spravovat zálohy](backup-azure-manage-windows-server.md).
