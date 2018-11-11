---
title: Obnovení dat v Azure k počítači se systémem Windows server nebo Windows
description: Zjistěte, jak obnovit data uložená v Azure k počítači se systémem Windows server nebo Windows.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 9/7/2018
ms.author: saurse
ms.openlocfilehash: 1e8e9365567c19400b86dc60d966eb965b83591d
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281740"
---
# <a name="restore-files-to-windows-by-using-the-azure-resource-manager-deployment-model"></a>Obnovení souborů do Windows pomocí modelu nasazení Azure Resource Manageru

Tento článek vysvětluje postup obnovení dat z úložiště záloh. Chcete-li obnovit data, použijete Průvodce obnovit Data v agentovi nástroje Microsoft Azure Recovery Services (MARS). Můžete:

* Obnovení dat na stejném počítači, ze kterého byly provedeny zálohy.
* Obnovení dat na jiný počítač

Pomocí funkce rychlého obnovení připojení obnovení zapisovatelného snímku bodu jako svazek pro obnovení. Můžete pak prozkoumat obnovení svazku a kopírovat soubory do místního počítače, a tím selektivní obnovení souborů.

> [!NOTE]
> [. Ledna 2017 aktualizaci služby Azure Backup](https://support.microsoft.com/en-us/help/3216528?preview) je vyžadována, pokud chcete použít k obnovení dat rychlé obnovení. Navíc zálohovaná data musí být chráněné ve trezorů v národních prostředí, které jsou uvedené v článku podpory. Poraďte [. ledna 2017 aktualizaci služby Azure Backup](https://support.microsoft.com/en-us/help/3216528?preview) nejnovější seznam národních prostředí, které podporují rychlé obnovení.
>

Použijte rychlé obnovení s trezory služby Recovery Services na webu Azure Portal. Pokud jste si uložili data v trezory služby Backup, byl převeden na trezory služby Recovery Services. Pokud chcete použít rychlé obnovení, stáhněte si aktualizaci MARS a postupujte podle pokynů, které uvádějí, rychlé obnovení.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Pomocí nástroje Obnovení rychlé obnovení dat na stejném počítači

Pokud omylem odstranil se soubor a chcete provést obnovení do stejného počítače (ze kterého dochází k zálohování), vám pomůže následující postup, můžete obnovit data.

1. Otevřete modul snap-in **Microsoft Azure Backup**. Pokud si nejste jisti, kde nainstalovaný modul snap-in, vyhledávat počítač nebo server pro **Microsoft Azure Backup**.

    Desktopové aplikace by se zobrazit ve výsledcích hledání.

2. Vyberte **obnovit Data** spusťte průvodce.

    ![Snímek obrazovky s Azure Backup, se zvýrazněným obnovit Data](./media/backup-azure-restore-windows-server/recover.png)

3. Na **Začínáme** stránku k obnovení dat na stejný server nebo počítač, vyberte **tento server (`<server name>`)** > **Další**.

    ![Snímek obrazovky z obnovit Data Průvodce Začínáme stránky](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. Na **vybrat režim obnovení** zvolte **jednotlivých souborů a složek** > **Další**.

    ![Snímek obrazovky z obnovit Data Průvodce vybrat režim obnovení stránky](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
  > [!IMPORTANT]
  > Možnost obnovení jednotlivých souborů a složek vyžaduje rozhraní .NET Framework 4.5.2 nebo novější. Pokud se nezobrazí **jednotlivých souborů a složek** možnost, musíte upgradovat rozhraní .NET Framework verze 4.5.2 nebo novější a zkuste to znovu.

  > [!TIP]
  > **Jednotlivých souborů a složek** možnost umožňuje rychlý přístup k data bodu obnovení. Je vhodné pro obnovení jednotlivých souborů s více než 80 GB více velikostí a nabízí přenos nebo kopírování urychluje MB/s až 6 během obnovení. **Svazku** možnost obnoví všechna zálohovaná data v určeném svazku. Tato možnost poskytuje rychlejší přenos rychlosti (až 60 MB/s), která je ideální pro obnovení velkých a velkých dat nebo celé svazky.

5. Na **vybrat svazek a datum** vyberte svazek, který obsahuje soubory a složky, kterou chcete obnovit.

    V kalendáři vyberte bod obnovení. Data zobrazená **tučné** označení dostupnosti alespoň jeden bod obnovení. Pokud v rámci jednoho data jsou k dispozici více bodů obnovení, vyberte konkrétní bod obnovení z **čas** rozevírací nabídky.

    ![Snímek obrazovky z obnovení průvodce vyberte objemu dat a data stránky](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Po výběru bodu obnovení pro obnovení, vyberte **připojit**.

    Azure Backup připojí bod obnovení pro místní a použije je jako svazek pro obnovení.

7. Na **Procházet a obnovit soubory** stránce **Procházet** otevřete Průzkumníka Windows a vyhledejte soubory a složky, které mají.

    ![Snímek obrazovky z obnovit Data Průvodce Procházet a obnovit soubory stránky](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)


8. V Průzkumníku Windows zkopírujte soubory a složky, které chcete obnovit a vložte je do libovolného umístění místní k serveru nebo počítači. Můžete otevřít nebo Streamovat soubory přímo ze svazku pro obnovení a ověřte, že obnovujete správné verze.

    ![Snímek obrazovky Windows Explorer, se zvýrazněným kopií](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)


9. Až budete hotoví, na **Procházet a obnovit soubory** stránce **odpojení**. Potom vyberte **Ano** potvrďte, že chcete svazek odpojit.

    ![Snímek obrazovky z obnovit Data Průvodce Procházet a obnovit soubory stránky](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Pokud nevyberete **odpojení**, obnovení svazku zůstanou připojené po dobu 6 hodin od okamžiku, kdy byla připojena. Čas připojení je však rozšířit až 24 hodin, v případě probíhající kopírování souborů. Žádná operace zálohování se spustí, zatímco je svazek připojený. Během doby, kdy je svazek připojený spuštění je naplánované zálohování se spustí po obnovení svazku je odpojeny.
    >


## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Použít k obnovení dat na jiný počítač, který rychlé obnovení
Pokud dojde ke ztrátě celý server, můžete stále obnovení dat z Azure Backup na jiném počítači. Následující postup ukazuje pracovní postup.


Tyto kroky zahrnují následující terminologií:

* *Zdrojový počítač* – původní počítač, ze kterého se záloha vytvořila, a která je aktuálně není k dispozici.
* *Cílový počítač* – počítače, na který se obnovuje data.
* *Ukázka trezor* – trezor Recovery Services do kterého jsou registrované na zdrojovém počítači a cílový počítač. <br/>

> [!NOTE]
> Zálohování není možné obnovit do cílového počítače, na kterém běží starší verze operačního systému. Například je možné obnovit zálohu z počítače Windows 7 na počítači s Windows 8 (nebo novější). Zálohování provést z počítače se systémem Windows 8 není možné obnovit do počítače Windows 7.
>
>

1. Otevřít **Microsoft Azure Backup** modul snap-in na cílovém počítači.

2. Ujistěte se, že se cílový počítač i zdrojový počítač zaregistrováni do stejného trezoru služby Recovery Services.

3. Vyberte **obnovit Data** otevřít **Průvodci obnovením dat**.

    ![Snímek obrazovky s Azure Backup, se zvýrazněným obnovit Data](./media/backup-azure-restore-windows-server/recover.png)

4. Na **Začínáme** stránce **jiný server**.

    ![Snímek obrazovky z obnovit Data Průvodce Začínáme stránky](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Zadejte soubor přihlašovacích údajů trezoru, který odpovídá vzorku trezoru a vyberte **Další**.

    Pokud soubor s přihlašovacími údaji trezoru je neplatný (nebo jejichž platnost vypršela), stáhněte si nový soubor přihlašovacích údajů trezoru z trezoru ukázky na webu Azure Portal. Po zadání přihlašovacích údajů platný trezor se zobrazí název odpovídajícího úložiště záloh.


6. Na **vybrat Server Backup** stránce vyberte zdrojový počítač ze seznamu zobrazených počítačů a zadejte heslo. Pak vyberte **Další**.

    ![Snímek obrazovky z obnovit Data průvodce vyberte zálohování stránka serveru](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Na **vybrat režim obnovení** stránce **jednotlivých souborů a složek** > **Další**.

    ![Snímek obrazovky z obnovit Data Průvodce vybrat režim obnovení stránky](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. Na **vybrat svazek a datum** vyberte svazek, který obsahuje soubory a složky, kterou chcete obnovit.

    V kalendáři vyberte bod obnovení. Data zobrazená **tučné** označení dostupnosti alespoň jeden bod obnovení. Pokud v rámci jednoho data jsou k dispozici více bodů obnovení, vyberte konkrétní bod obnovení z **čas** rozevírací nabídky.

    ![Snímek obrazovky z obnovení průvodce vyberte objemu dat a data stránky](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Vyberte **připojit** do místně na cílovém počítači připojí bod obnovení jako svazek pro obnovení.

10. Na **Procházet a obnovit soubory** stránce **Procházet** otevřete Průzkumníka Windows a vyhledejte soubory a složky, které mají.

    ![Snímek obrazovky z obnovit Data Průvodce Procházet a obnovit soubory stránky](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. V Průzkumníku Windows zkopírujte soubory a složky ze svazku pro obnovení a vložte je do umístění cílového počítače. Můžete otevřít nebo Streamovat soubory přímo ze svazku pro obnovení a ověřte, že se dají obnovit správné verze.

    ![Snímek obrazovky Windows Explorer, se zvýrazněným kopií](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Až budete hotoví, na **Procházet a obnovit soubory** stránce **odpojení**. Potom vyberte **Ano** potvrďte, že chcete svazek odpojit.

    ![Snímek obrazovky z obnovit Data Průvodce Procházet a obnovit soubory stránky](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Pokud nevyberete **odpojení**, obnovení svazku zůstanou připojené po dobu 6 hodin od okamžiku, kdy byla připojena. Čas připojení je však rozšířit až 24 hodin, v případě probíhající kopírování souborů. Žádná operace zálohování se spustí, zatímco je svazek připojený. Během doby, kdy je svazek připojený spuštění je naplánované zálohování se spustí po obnovení svazku je odpojeny.
    >

## <a name="next-steps"></a>Další postup
Teď, když jste obnovit soubory a složky, můžete [správa záloh](backup-azure-manage-windows-server.md).
