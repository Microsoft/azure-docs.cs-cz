---
title: Obnovení souborů na Windows serveru pomocí agenta MARS
description: V tomto článku se dozvíte, jak obnovit data uložená v Azure do počítače s Windows serverem nebo Windows pomocí agenta Microsoft Azure Recovery Services (MARS).
ms.topic: conceptual
ms.date: 09/07/2018
ms.openlocfilehash: 7ca0787ec38e1bc22b62e756c7ee56c5c9e93493
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517330"
---
# <a name="restore-files-to-windows-server-using-the-mars-agent"></a>Obnovení souborů na Windows serveru pomocí agenta MARS

Tento článek vysvětluje, jak obnovit data z trezoru služby Backup. Chcete-li obnovit data, použijte Průvodce obnovením dat v agentovi Microsoft Azure Recovery Services (MARS). Další možnosti:

* Obnovte data na stejném počítači, ze kterého byly zálohy pořízeny.
* Obnovení dat na jiný počítač

Pomocí funkce okamžitého obnovení můžete připojit zapisovatelný zapisovatelný snímek bodu obnovení jako svazek pro obnovení. Pak můžete prozkoumat svazek pro obnovení a kopírovat soubory do místního počítače, v tomto případě selektivně obnovování souborů.

> [!NOTE]
> Pokud chcete k obnovení dat použít okamžité obnovení, je potřeba [aktualizace z ledna 2017 Azure Backup](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) . Zálohovaná data musí být také chráněna v trezorech v místních prostředích uvedených v článku podpory. Nejnovější seznam národních prostředí, která podporují okamžité obnovení, najdete v [2017. Azure Backup aktualizace](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) .
>

Pomocí okamžitého obnovení s Recovery Services trezory v Azure Portal. Pokud jste data ukládali v trezorech služby Backup, byly převedeny na Recovery Services trezory. Pokud chcete použít okamžité obnovení, Stáhněte aktualizaci MARS a postupujte podle pokynů pro okamžité obnovení.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Obnovení dat na stejném počítači pomocí okamžitého obnovení

Pokud jste omylem odstranili soubor a chcete ho obnovit do stejného počítače (ze kterého se zálohování provádí), při obnovování těchto dat vám pomůže následující postup.

1. Otevřete modul snap-in **Microsoft Azure Backup**. Pokud si nejste jisti, kde byl modul snap-in nainstalován, vyhledejte **Microsoft Azure Backup** v počítači nebo na serveru.

    Aplikace klasické pracovní plochy by se měla zobrazit ve výsledcích hledání.

2. Vyberte **obnovit data** a spusťte průvodce.

    ![Snímek obrazovky Azure Backup se zvýrazněnou možností obnovit data (obnovit do stejného počítače)](./media/backup-azure-restore-windows-server/recover.png)

3. Na stránce **Začínáme** pro obnovení dat na stejném serveru nebo počítači vyberte **Tento server ( `<server name>` )**  >  **Další**.

    ![Snímek obrazovky s průvodcem obnovením dat Začínáme stránce (obnovení do stejného počítače)](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. Na stránce **Vybrat režim obnovení** vyberte **jednotlivé soubory a složky** > **Další**.

    ![Snímek obrazovky Průvodce obnovením dat výběr stránky režimu obnovení (obnovení do stejného počítače)](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > Možnost obnovení jednotlivých souborů a složek vyžaduje .NET Framework 4.5.2 nebo novější. Pokud nevidíte možnost **jednotlivé soubory a složky** , musíte upgradovat .NET Framework na verzi 4.5.2 nebo novější. potom to zkuste znovu.

   > [!TIP]
   > Možnost **jednotlivé soubory a složky** umožňuje rychlý přístup k datům bodů obnovení. Je vhodný pro obnovování jednotlivých souborů a doporučuje se pro celkovou velikost menší než 80 GB. Během obnovení nabízí rychlost přenosu nebo kopírování až do 6 MB/s. Možnost **svazek** obnoví všechna zálohovaná data v zadaném svazku. Tato možnost poskytuje rychlejší přenosovou rychlost (až 40 MB/s) a doporučuje se pro obnovu velkých objemů dat nebo celých svazků.

5. Na stránce **Vybrat svazek a datum** vyberte svazek, který obsahuje soubory a složky, které chcete obnovit.

    V kalendáři vyberte bod obnovení. Kalendářní data **tučně** označují dostupnost alespoň jednoho bodu obnovení. Pokud je v jednom dni k dispozici více bodů obnovení, vyberte konkrétní bod obnovení z rozevírací nabídky **čas** .

    ![Snímek obrazovky Průvodce obnovením dat výběr svazku a data stránky (obnovení do stejného počítače)](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Po výběru bodu obnovení, který se má obnovit, vyberte **připojit**.

    Azure Backup připojí místní bod obnovení a použije ho jako svazek pro obnovení.

7. Na stránce **Procházet a obnovit soubory** vyberte **Procházet** a otevřete Průzkumníka Windows a najděte požadované soubory a složky.

    ![Snímek obrazovky se stránkou Průvodce obnovením dat procházení a obnovení souborů (obnovení do stejného počítače)](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)

8. V Průzkumníku Windows zkopírujte soubory a složky, které chcete obnovit, a vložte je do libovolného umístění na serveru nebo v místním počítači. Soubory můžete otevřít nebo zasílat přímo ze svazku pro obnovení a ověřit si, že obnovujete správné verze.

    ![Snímek obrazovky Průzkumníka Windows se zvýrazněnou možností kopírovat (obnovit do stejného počítače)](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Až budete hotovi, na stránce **Procházet a obnovit soubory** vyberte **Odpojit**. Pak vyberte **Ano** a potvrďte tak, že chcete odpojit svazek.

    ![Snímek obrazovky se stránkou Průvodce obnovením dat procházení a obnovení souborů (obnovení do stejného počítače) – potvrzení odpojení svazku pro obnovení](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Pokud nevyberete **Odpojit**, svazek pro obnovení zůstane připojený po dobu 6 hodin od okamžiku, kdy byl připojen. Doba připojení se ale v případě průběžného kopírování souborů prodlouží na maximálně 24 hodin. Během připojení svazku se nespustí žádné operace zálohování. Jakákoli zálohovací operace naplánovaná ke spuštění v době, kdy je svazek připojen, se spustí po odpojení svazku pro obnovení.
    >

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Obnovení dat na alternativním počítači pomocí okamžitého obnovení

Pokud dojde ke ztrátě celého serveru, můžete i nadále obnovit data z Azure Backup na jiný počítač. Následující kroky ilustrují pracovní postup.

Tyto kroky zahrnují následující terminologii:

* *Zdrojový počítač* – původní počítač, ze kterého byla provedena záloha, a který není momentálně k dispozici.
* *Cílový počítač* – počítač, na který se data obnovují.
* *Vzorový trezor* – Recovery Services trezor, ke kterému jsou zaregistrované zdrojový počítač a cílový počítač.

> [!NOTE]
> Zálohy nelze obnovit do cílového počítače, na kterém je spuštěna dřívější verze operačního systému. Například zálohování z počítače se systémem Windows 7 lze obnovit v počítači se systémem Windows 7 (nebo novějším). Záloha pořízená z počítače s Windows 10 se nedá obnovit v počítači se systémem Windows 7.
>
>

1. Otevřete modul snap-in **Microsoft Azure Backup** v cílovém počítači.

2. Ujistěte se, že cílový počítač a zdrojový počítač jsou zaregistrované ve stejném trezoru Recovery Services.

3. Vyberte **obnovit data** a otevřete tak **Průvodce obnovením dat**.

    ![Snímek obrazovky Azure Backup s zvýrazněnou možností obnovit data (obnovit do alternativního počítače)](./media/backup-azure-restore-windows-server/recover.png)

4. Na stránce **Začínáme** vyberte **jiný server**.

    ![Snímek obrazovky s průvodcem obnovením dat Začínáme stránce (obnovení do alternativního počítače)](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Zadejte soubor s přihlašovacími údaji úložiště, který odpovídá trezoru ukázek, a vyberte **Další**.

    Pokud je soubor přihlašovacích údajů trezoru neplatný (nebo vypršela jeho platnost), [Stáhněte si nový soubor s přihlašovacími údaji trezoru z trezoru ukázek](backup-azure-file-folder-backup-faq.yml#where-can-i-download-the-vault-credentials-file-) v Azure Portal. Po zadání platného pověření trezoru se zobrazí název odpovídajícího úložiště záloh.

6. Na stránce **Vybrat záložní server** vyberte zdrojový počítač ze seznamu zobrazených počítačů a zadejte heslo. Pak vyberte **Další**.

    ![Snímek obrazovky Průvodce obnovením dat vybrat záložní server stránku (obnovit do alternativního počítače)](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Na stránce **Vybrat režim obnovení** vyberte **jednotlivé soubory a složky**  >  **Další**.

    ![Snímek obrazovky Průvodce obnovením dat výběr stránky režim obnovení (obnovení do alternativního počítače)](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. Na stránce **Vybrat svazek a datum** vyberte svazek, který obsahuje soubory a složky, které chcete obnovit.

    V kalendáři vyberte bod obnovení. Kalendářní data **tučně** označují dostupnost alespoň jednoho bodu obnovení. Pokud je v jednom dni k dispozici více bodů obnovení, vyberte konkrétní bod obnovení z rozevírací nabídky **čas** .

    ![Snímek obrazovky Průvodce obnovením dat výběr svazku a data stránky (obnovení do alternativního počítače)](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Vyberte **připojit** k místnímu připojení bodu obnovení jako svazku pro obnovení na cílovém počítači.

10. Na stránce **Procházet a obnovit soubory** vyberte **Procházet** a otevřete Průzkumníka Windows a najděte požadované soubory a složky.

    ![Snímek obrazovky se stránkou Průvodce obnovením dat procházení a obnovení souborů (obnovení do alternativního počítače)](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. V Průzkumníku Windows zkopírujte soubory a složky ze svazku pro obnovení a vložte je do umístění cílového počítače. Soubory můžete otevřít nebo zasílat přímo ze svazku pro obnovení a ověřit, jestli se obnovily správné verze.

    ![Snímek obrazovky Průzkumníka Windows se zvýrazněnou možností kopírovat (obnovit do alternativního počítače)](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Až budete hotovi, na stránce **Procházet a obnovit soubory** vyberte **Odpojit**. Pak vyberte **Ano** a potvrďte tak, že chcete odpojit svazek.

    ![Odpojte svazek (obnovit do alternativního počítače).](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Pokud nevyberete **Odpojit**, svazek pro obnovení zůstane připojený po dobu 6 hodin od okamžiku, kdy byl připojen. Doba připojení se ale v případě průběžného kopírování souborů prodlouží na maximálně 24 hodin. Během připojení svazku se nespustí žádné operace zálohování. Jakákoli zálohovací operace naplánovaná ke spuštění v době, kdy je svazek připojen, se spustí po odpojení svazku pro obnovení.
    >

## <a name="next-steps"></a>Další kroky

* Teď, když jste obnovili své soubory a složky, můžete [Spravovat zálohy](backup-azure-manage-windows-server.md).

* Najděte [běžné otázky týkající se zálohování souborů a složek](backup-azure-file-folder-backup-faq.yml).
