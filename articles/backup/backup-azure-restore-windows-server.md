---
title: Obnovení dat v Azure do Windows serveru nebo počítače Windows
description: Zjistěte, jak obnovit data uložená v Azure do Windows serveru nebo počítače Windows.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 8/6/2018
ms.author: saurse
ms.openlocfilehash: ddde297de49edb5f6543d03dfdb972771533301b
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576181"
---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-resource-manager-deployment-model"></a>Obnovení souborů na serveru Windows nebo klientském počítači s využitím modelu nasazení Resource Manager

Tento článek vysvětluje postup obnovení dat z úložiště záloh. Chcete-li obnovit data, použijete Průvodce obnovit Data v agentovi nástroje Microsoft Azure Recovery Services (MARS). Při obnovování dat je možné:

* Obnovení dat na stejném počítači, ze kterého byly provedeny zálohy.
* Obnovení dat na jiném počítači.

Společnost Microsoft vydala v lednu 2017, verze Preview aktualizace pro agenta MARS. Spolu s opravami chyb tato aktualizace umožňuje rychlé obnovení, který umožňuje připojit obnovení zapisovatelného snímku bodu jako svazek pro obnovení. Pak si můžete projít obnovení svazku a kopírovat soubory do místního počítače a tím selektivní obnovení souborů.

> [!NOTE]
> [. Ledna 2017 aktualizaci služby Azure Backup](https://support.microsoft.com/en-us/help/3216528?preview) je vyžadována, pokud chcete použít k obnovení dat rychlé obnovení. Také zálohovaná data musí být chráněné ve trezorů v národních prostředí, které jsou uvedené v článku podpory. Poraďte [. ledna 2017 aktualizaci služby Azure Backup](https://support.microsoft.com/en-us/help/3216528?preview) nejnovější seznam národních prostředí, které podporují rychlé obnovení. Okamžitá obnova je **není** aktuálně k dispozici ve všech národních prostředích.
>

Použijte rychlé obnovení s trezory služby Recovery Services na webu Azure Portal. Pokud jste si uložili data v trezory služby Backup, byl převeden na trezory služby Recovery Services. Pokud chcete použít rychlé obnovení, stáhněte si aktualizaci MARS a postupujte podle pokynů, které uvádějí, rychlé obnovení.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Pomocí nástroje Obnovení rychlé obnovení dat na stejném počítači

Pokud omylem odstranil se soubor a chcete obnovit na stejný počítač (ze kterého dochází k zálohování), následující kroky vám pomohou obnovit data.

1. Otevřít **Microsoft Azure Backup** Přichytit v. Pokud si nejste jisti, kam se nainstaloval snap-in, vyhledávat počítač nebo server pro **Microsoft Azure Backup**.

    Desktopové aplikace by se zobrazit ve výsledcích hledání.

2. Klikněte na tlačítko **obnovit Data** spusťte průvodce.

    ![Obnovení dat](./media/backup-azure-restore-windows-server/recover.png)

3. Na **Začínáme** podokno, chcete-li obnovit data na stejný server nebo počítač, vyberte **tento server (`<server name>`)** a klikněte na tlačítko **Další**.

    ![Tato možnost server pro obnovení dat na stejný počítač](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. Na **vybrat režim obnovení** podokně zvolte **jednotlivých souborů a složek** a potom klikněte na tlačítko **Další**.

    ![Procházet soubory](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
> [!IMPORTANT]
> Možnost obnovení *jednotlivých souborů a složek* vyžaduje rozhraní .NET Framework 4.5.2 nebo novější. Pokud se nezobrazí *jednotlivých souborů a složek* možnost, musíte upgradovat rozhraní .NET Framework verze 4.5.2 nebo novější a zkuste to znovu.

5. Na **vybrat svazek a datum** podokně, vyberte svazek, který obsahuje soubory a složky, kterou chcete obnovit.

    V kalendáři vyberte bod obnovení. Z libovolného bodu obnovení můžete obnovit v čase. Data zobrazená **tučné** označení dostupnosti alespoň jeden bod obnovení. Jakmile vyberete datum, pokud jsou k dispozici více bodů obnovení, vyberte konkrétní bod obnovení z **čas** rozevírací nabídky.

    ![Svazek a datum](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Když vyberete bod obnovení pro obnovení, klikněte na tlačítko **připojit**.

    Azure Backup připojí bod obnovení pro místní a použije je jako svazek pro obnovení.

7. Na **Procházet a obnovit soubory** podokně klikněte na tlačítko **Procházet** otevřete Průzkumníka Windows a vyhledejte soubory a složky, které mají.

    ![Možnosti obnovení](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)


8. V Průzkumníku Windows zkopírujte soubory a složky, které chcete obnovit a vložte je do libovolného umístění místní k serveru nebo počítači. Můžete otevřít nebo Streamovat soubory přímo ze svazku pro obnovení a ověřte, že obnovujete správné verze.

    ![Zkopírujte a vložte soubory a složky z připojeného svazku na místní umístění](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Až budete hotovi s obnovováním souborů a složek, na **Procházet a obnovit soubory** podokně klikněte na tlačítko **odpojení**. Pak klikněte na tlačítko **Ano** potvrďte, že chcete svazek odpojit.

    ![Svazek odpojit a potvrzení](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Pokud neklikejte na odpojení, obnovení svazku zůstanou připojené po dobu 6 hodin od okamžiku, kdy byl připojený. Čas připojení je však rozšířené až do maximální hodnoty 24 hodin, v případě probíhající kopírování souborů. Žádná operace zálohování se spustí, zatímco je svazek připojený. Naplánované spuštění v době, kdy je připojený svazek, zálohování se spustí po obnovení svazku je odpojeny.
    >


## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Použít k obnovení dat na jiný počítač, který rychlé obnovení
Pokud dojde ke ztrátě celý server, můžete stále obnovení dat z Azure Backup na jiném počítači. Následující postup ukazuje pracovní postup.


Zahrnuje terminologii používané v těchto krocích:

* *Zdrojový počítač* – původní počítač, ze kterého bylo provedeno zálohování a který není aktuálně k dispozici.
* *Cílový počítač* – počítače, na který se obnovuje data.
* *Ukázkové úložiště* – trezor Recovery Services ke kterému *zdrojový počítač* a *cílový počítač* jsou registrované. <br/>

> [!NOTE]
> Zálohování není možné obnovit do cílového počítače starší verzí operačního systému. Například zálohu z Windows 7, může být počítač obnovit, v systému Windows 8 nebo novější, počítač. Zálohování provést z počítače se systémem Windows 8 není možné obnovit do počítače Windows 7.
>
>

1. Otevřít **Microsoft Azure Backup** Přichytit v na *cílový počítač*.

2. Zkontrolujte *cílový počítač* a *zdrojový počítač* zaregistrováni do stejného trezoru služby Recovery Services.

3. Klikněte na tlačítko **obnovit Data** otevřít **obnovit Data Průvodce**.

    ![Obnovení dat](./media/backup-azure-restore-windows-server/recover.png)

4. Na **Začínáme** vyberte **jiný server**

    ![Jiný Server](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Zadejte soubor přihlašovacích údajů trezoru, který odpovídá *ukázka trezor*a klikněte na tlačítko **Další**.

    Pokud soubor s přihlašovacími údaji trezoru je neplatný (nebo jejichž platnost vypršela), stáhněte si nový soubor přihlašovacích údajů trezoru z *ukázka trezor* na webu Azure Portal. Po zadání přihlašovacích údajů platný trezor se zobrazí název odpovídajícího úložiště záloh.


6. Na **vybrat Server Backup** podokně, vyberte *zdrojový počítač* ze seznamu zobrazených počítačů a zadejte heslo. Pak klikněte na tlačítko **Další**.

    ![Seznam počítačů](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Na **vybrat režim obnovení** podokně, vyberte **jednotlivých souborů a složek** a klikněte na tlačítko **Další**.

    ![Search](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. Na **vybrat svazek a datum** podokně, vyberte svazek, který obsahuje soubory a složky, kterou chcete obnovit.

    V kalendáři vyberte bod obnovení. Z libovolného bodu obnovení můžete obnovit v čase. Data zobrazená **tučné** označení dostupnosti alespoň jeden bod obnovení. Jakmile vyberete datum, pokud jsou k dispozici více bodů obnovení, vyberte konkrétní bod obnovení z **čas** rozevírací nabídky.

    ![Hledat položky](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Klikněte na tlačítko **připojit** na místním počítači připojí bod obnovení jako svazek pro obnovení na vaše *cílový počítač*.

10. Na **Procházet a obnovit soubory** podokně klikněte na tlačítko **Procházet** otevřete Průzkumníka Windows a vyhledejte soubory a složky, které mají.

    ![Šifrování](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. V Průzkumníku Windows zkopírujte soubory a složky ze svazku pro obnovení a vložte je do vašeho *cílový počítač* umístění. Můžete otevřít nebo Streamovat soubory přímo ze svazku pro obnovení a ověřte, zda že jsou obnoveny správné verze.

    ![Šifrování](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Až budete hotovi s obnovováním souborů a složek, na **Procházet a obnovit soubory** podokně klikněte na tlačítko **odpojení**. Pak klikněte na tlačítko **Ano** potvrďte, že chcete svazek odpojit.

    ![Šifrování](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Pokud neklikejte na odpojení, obnovení svazku zůstanou připojené po dobu 6 hodin od okamžiku, kdy byl připojený. Čas připojení je však rozšířené až do maximální hodnoty 24 hodin, v případě probíhající kopírování souborů. Žádná operace zálohování se spustí, zatímco je svazek připojený. Naplánované spuštění v době, kdy je připojený svazek, zálohování se spustí po obnovení svazku je odpojeny.
    >

## <a name="next-steps"></a>Další postup
* Teď, když jste obnovit soubory a složky, můžete [správa záloh](backup-azure-manage-windows-server.md).
