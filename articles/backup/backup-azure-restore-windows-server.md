---
title: Obnovení dat v Azure na server Windows
description: V tomto článku se dozvíte, jak obnovit data uložená v Azure na server s Windows nebo počítač s Windows pomocí agenta Microsoft Azure Recovery Services (MARS).
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 09/07/2018
ms.openlocfilehash: 25ca8eecaeb615f071340188a23fae7978ddb75c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409809"
---
# <a name="restore-files-to-windows-by-using-the-azure-resource-manager-deployment-model"></a>Obnovení souborů do Windows pomocí modelu nasazení Azure Resource Manageru

Tento článek vysvětluje, jak obnovit data ze záložního trezoru. Chcete-li obnovit data, použijte Průvodce obnovením dat v agentovi Microsoft Azure Recovery Services (MARS). Můžete:

* Obnovte data do stejného počítače, ze kterého byly zálohy převzaty.
* Obnovení dat na jiný počítač

Pomocí funkce Okamžité obnovení můžete připojit zapisovatelný snímek bodu obnovení jako svazek obnovení. Potom můžete prozkoumat svazek pro obnovení a zkopírovat soubory do místního počítače, a tím selektivně obnovit soubory.

> [!NOTE]
> [Aktualizace zálohování Azure z ledna 2017](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar) je povinná, pokud chcete k obnovení dat použít okamžité obnovení. Záložní data musí být také chráněna v trezorech v národních prostředích uvedených v článku podpory. Nejnovější seznam národních prostředí, která podporují okamžité obnovení, nahlédněte do [aktualizace zálohování Azure z ledna 2017.](https://support.microsoft.com/help/3216528/azure-backup-update-for-microsoft-azure-recovery-services-agent-januar)
>

Na webu Azure Portal použijte okamžité obnovení s trezory služby Recovery Services. Pokud jste data uložili do trezorů zálohování, byla převedena na trezory služby Recovery Services. Pokud chcete použít okamžité obnovení, stáhněte si aktualizaci MARS a postupujte podle postupů, které zmiňují okamžité obnovení.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Obnovení dat do stejného počítače pomocí okamžitého obnovení

Pokud jste omylem odstranili soubor a chcete jej obnovit do stejného počítače (ze kterého je záloha provedena), následující kroky vám pomohou obnovit data.

1. Otevřete modul snap-in **Microsoft Azure Backup**. Pokud nevíte, kde byl modul snap-in nainstalován, vyhledejte v počítači nebo na serveru **službu Microsoft Azure Backup**.

    Ve výsledcích hledání by se měla zobrazit aplikace klasické pracovní plochy.

2. Chcete-li průvodce spustit, vyberte **obnovit data.**

    ![Snímek obrazovky s azure backupem se zvýrazněnou možností Obnovit data](./media/backup-azure-restore-windows-server/recover.png)

3. Chcete-li obnovit data na stejném serveru nebo počítači, vyberte na stránce **Začínáme** možnost **Tento server`<server name>`( )** > **Další**.

    ![Snímek obrazovky se stránkou Začínáme s Průvodce obnovením dat](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. Na stránce **Vybrat režim obnovení** zvolte Jednotlivé soubory a **složky** > **Další**.

    ![Snímek obrazovky se stránkou Obnovit průvodce obnovením vyberte režim obnovení](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > Možnost obnovení jednotlivých souborů a složek vyžaduje rozhraní .NET Framework 4.5.2 nebo novější. Pokud možnost Jednotlivé **soubory a složky** nevidíte, je nutné inovovat rozhraní .NET Framework na verzi 4.5.2 nebo novější a akci opakujte.
 
   > [!TIP]
   > **Možnost Jednotlivé soubory a složky** umožňuje rychlý přístup k datům bodů obnovení. Je vhodný pro obnovu jednotlivých souborů s velikostí ne přesahující 80 GB a nabízí přenos nebo rychlost kopírování až 6 MB/s během obnovy. Možnost **Objem** obnoví všechna zálohovaná data v určeném svazku. Tato možnost poskytuje vyšší přenosové rychlosti (až 60 MB/s), což je ideální pro obnovení velkých dat nebo celých svazků.

5. Na stránce **Vybrat svazek a datum** vyberte svazek obsahující soubory a složky, které chcete obnovit.

    V kalendáři vyberte bod obnovení. Data **tučně** označují dostupnost alespoň jednoho bodu obnovení. Pokud je v rámci jednoho data k dispozici více bodů obnovení, zvolte konkrétní bod obnovení z rozevírací nabídky **Čas.**

    ![Snímek obrazovky se stránkou Vybrat svazek a datum Průvodce obnovením dat](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Po výběru bodu obnovení, který chcete obnovit, vyberte **připojit**.

    Azure Backup připojí místní bod obnovení a použije ho jako svazek pro obnovení.

7. Na stránce **Procházet a obnovit soubory** vyberte **Procházet,** chcete-li otevřít Průzkumníka Windows, a najděte požadované soubory a složky.

    ![Snímek obrazovky se stránkou Procházet a obnovit soubory průvodce obnovením dat](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)

8. V Průzkumníkovi Windows zkopírujte soubory a složky, které chcete obnovit, a vložte je do libovolného místního umístění na server nebo počítač. Soubory můžete otevřít nebo streamovat přímo ze svazku pro obnovení a ověřit, zda obnovujete správné verze.

    ![Snímek obrazovky Průzkumníka Windows se zvýrazněnou možností Kopírovat](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Po dokončení vyberte na stránce **Procházet a obnovit soubory** **možnost Odpojit**. Pak vyberte **Ano,** abyste potvrdili, že chcete svazek odpojit.

    ![Snímek obrazovky se stránkou Procházet a obnovit soubory průvodce obnovením dat](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Pokud nevyberete **možnost Odpojit**, zůstane svazek pro obnovení připojen po dobu 6 hodin od okamžiku, kdy byl připojen. Doba připojení je však prodloužena až na maximálně 24 hodin v případě probíhající kopie souboru. Při nastavování svazku nebudou spuštěny žádné operace zálohování. Po odpojení svazku pro obnovení budou spuštěny všechny operace zálohování, které jsou naplánovány na spuštění v době, kdy je svazek připojen.
    >

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Obnovení dat do alternativního počítače pomocí okamžitého obnovení

Pokud dojde ke ztrátě celého serveru, můžete obnovit data z Azure Backup do jiného počítače. Následující kroky ilustrují pracovní postup.

Mezi tyto kroky patří následující terminologie:

* *Zdrojový počítač* – původní počítač, ze kterého byla záloha převzata a který je aktuálně nedostupný.
* *Cílový počítač* – počítač, do kterého jsou data obnovována.
* *Ukázková úschovna* – trezor služby Recovery Services, do kterého jsou zaregistrovány zdrojový počítač a cílový počítač.

> [!NOTE]
> Zálohy nelze obnovit do cílového počítače, ve které je spuštěna starší verze operačního systému. Například záloha převzatá z počítače se systémem Windows 7 může být obnovena v počítači se systémem Windows 7 (nebo novějším). Zálohu převzatou z počítače se systémem Windows 8 nelze obnovit do počítače se systémem Windows 7.
>
>

1. Otevřete modul snap-in **Zálohování Microsoft Azure** v cílovém počítači.

2. Ujistěte se, že cílový počítač a zdrojový počítač jsou registrovány do stejného trezoru služby Recovery Services.

3. Výběrem **možnosti Obnovit data** otevřete **Průvodce obnovením dat**.

    ![Snímek obrazovky s azure backupem se zvýrazněnou možností Obnovit data](./media/backup-azure-restore-windows-server/recover.png)

4. Na stránce **Začínáme** vyberte **Jiný server**.

    ![Snímek obrazovky se stránkou Začínáme s Průvodce obnovením dat](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Zadejte soubor pověření úschovny, který odpovídá ukázkové úschovně, a vyberte **Další**.

    Pokud je soubor přihlašovacích údajů trezoru neplatný (nebo vypršela jeho platnost), stáhněte si nový soubor přihlašovacích údajů trezoru z ukázkového úložiště na webu Azure Portal. Po zadání platného pověření trezoru se zobrazí název odpovídajícího záložního trezoru.

6. Na stránce **Vybrat záložní server** vyberte zdrojový počítač ze seznamu zobrazených počítačů a zadejte přístupové heslo. Pak vyberte **Další**.

    ![Snímek obrazovky se stránkou Vybrat záložní server vyberte průvodce obnovením dat](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Na stránce **Vybrat režim obnovení** vyberte Jednotlivé soubory a **složky** > **Další**.

    ![Snímek obrazovky se stránkou Obnovit průvodce obnovením vyberte režim obnovení](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. Na stránce **Vybrat svazek a datum** vyberte svazek obsahující soubory a složky, které chcete obnovit.

    V kalendáři vyberte bod obnovení. Data **tučně** označují dostupnost alespoň jednoho bodu obnovení. Pokud je v rámci jednoho data k dispozici více bodů obnovení, zvolte konkrétní bod obnovení z rozevírací nabídky **Čas.**

    ![Snímek obrazovky se stránkou Vybrat svazek a datum Průvodce obnovením dat](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Vyberte **připojit** místně připojit bod obnovení jako svazek pro obnovení na cílovém počítači.

10. Na stránce **Procházet a obnovovat soubory** vyberte **Procházet,** chcete-li otevřít Průzkumníka Windows, a najděte požadované soubory a složky.

    ![Snímek obrazovky se stránkou Průvodce procházením a obnovením souborů](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. V Průzkumníkovi Windows zkopírujte soubory a složky ze svazku pro obnovení a vložte je do cílového umístění počítače. Soubory můžete otevřít nebo streamovat přímo ze svazku pro obnovení a ověřit, zda jsou obnoveny správné verze.

    ![Snímek obrazovky Průzkumníka Windows se zvýrazněnou možností Kopírovat](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Po dokončení vyberte na stránce **Procházet a obnovit soubory** **možnost Odpojit**. Pak vyberte **Ano,** abyste potvrdili, že chcete svazek odpojit.

    ![Snímek obrazovky se stránkou Průvodce procházením a obnovením souborů](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Pokud nevyberete **možnost Odpojit**, zůstane svazek pro obnovení připojen po dobu 6 hodin od okamžiku, kdy byl připojen. Doba připojení je však prodloužena až na maximálně 24 hodin v případě probíhající kopie souboru. Při nastavování svazku nebudou spuštěny žádné operace zálohování. Po odpojení svazku pro obnovení budou spuštěny všechny operace zálohování, které jsou naplánovány na spuštění v době, kdy je svazek připojen.
    >

## <a name="next-steps"></a>Další kroky

* Nyní, když jste obnovili soubory a složky, můžete [spravovat zálohy](backup-azure-manage-windows-server.md).

* Vyhledejte [časté otázky týkající se zálohování souborů a složek](backup-azure-file-folder-backup-faq.md).
