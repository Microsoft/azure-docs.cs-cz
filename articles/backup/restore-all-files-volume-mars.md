---
title: Obnovení všech souborů ve svazku pomocí MARS
description: Zjistěte, jak obnovit všechny soubory ve svazku pomocí agenta MARS.
ms.topic: conceptual
ms.date: 01/17/2021
ms.openlocfilehash: 44c12809fc94f78721ab1788cb352076dfebabe4
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613468"
---
# <a name="restore-all-the-files-in-a-volume-using-the-mars-agent"></a>Obnovení všech souborů ve svazku pomocí agenta MARS

Tento článek vysvětluje, jak obnovit všechny zálohované soubory v celém svazku pomocí Průvodce obnovením dat v agentovi Microsoft Azure Recovery Services (MARS). Další možnosti:

- Obnovte všechny zálohované soubory ve svazku do stejného počítače, ze kterého byly zálohy pořízeny.
- Obnovte všechny zálohované soubory ve svazku na alternativním počítači.

>[!TIP]
>Možnost **svazek** obnoví všechna zálohovaná data v zadaném svazku. Tato možnost poskytuje rychlejší přenosovou rychlost (až 40 MB/s) a doporučuje se pro obnovu velkých objemů dat nebo celých svazků.
>
>**Možnost jednotlivé soubory a složky** umožňuje rychlý přístup k datům bodů obnovení. Je vhodný pro obnovování jednotlivých souborů a doporučuje se pro celkovou velikost menší než 80 GB. Během obnovení nabízí rychlost přenosu nebo kopírování až do 6 MB/s.

## <a name="volume-level-restore-to-the-same-machine"></a>Obnovení na úrovni svazku na stejný počítač

Následující kroky vám pomůžou s obnovením všech zálohovaných souborů ve svazku:

1. Otevřete modul snap-in **Microsoft Azure Backup**. Pokud si nejste jisti, kde byl modul snap-in nainstalován, vyhledejte **Microsoft Azure Backup** v počítači nebo na serveru. Aplikace klasické pracovní plochy by se měla zobrazit ve výsledcích hledání.

1. Vyberte **obnovit data** a spusťte průvodce.

    ![Nabídka obnovit data](./media/restore-all-files-volume-mars/recover.png)

1. Na stránce **Začínáme** pro obnovení dat na stejný server nebo počítač vyberte možnost **Server (název serveru)**  >  **Další**.

    ![Stránka Začínáme](./media/restore-all-files-volume-mars/same-machine-instant-restore.png)

1. Na stránce **Vybrat režim obnovení** klikněte na tlačítko **Hlasitost**  >  **Další**.

    ![Vybrat režim obnovení](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. Na stránce **Vybrat svazek a datum** vyberte svazek, který chcete obnovit.

    V kalendáři vyberte bod obnovení. Kalendářní data **tučně** označují dostupnost alespoň jednoho bodu obnovení. Pokud je v jednom dni k dispozici více bodů obnovení, vyberte konkrétní bod obnovení z rozevírací nabídky **čas** .

     ![Vybrat svazek a datum](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. Na stránce **zadat možnosti obnovení** nakonfigurujte chování při obnovení.
    1. Vyberte cíl obnovení:
        - **Původní umístění**: obnovit data do původní cesty.
        - **Jiné umístění**: zadejte alternativní umístění pro obnovení dat.
    1. Vyberte chování pro **případ, kdy se položky v záloze již nacházejí v cíli obnovení**:
        - **Vytvořit kopie, abyste měli obě verze**: Pokud už soubor se stejným názvem existuje, data v bodu obnovení se obnoví jako kopie. Kopie bude mít lokalizovanou předponu názvu souboru s použitím místního času úlohy obnovení v jednom z následujících formátů:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Přepsat existující verze obnovenými verzemi**: Pokud již soubor se stejným názvem existuje, bude obsah nahrazen daty v bodu obnovení.
        - **Neobnovujte položky, které už existují v cíli obnovení**: Pokud už soubor se stejným názvem existuje, přeskočí se.
    1. **Povolte možnost obnovit seznam řízení přístupu (ACL) pro soubor nebo složku, která se** má obnovit, pokud by měl být soubor obnoven s původními oprávněními v bodu obnovení.
        ![Zadat možnosti obnovení](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. Ověřte podrobnosti o obnovení v podokně **potvrzení** a vyberte možnost **obnovit**.

    ![Podrobnosti o potvrzení](./media/restore-all-files-volume-mars/confirmation-details.png)

1. Na stránce **Průběh obnovení** Sledujte průběh úlohy obnovení. Průvodce může být také bezpečně uzavřen a operace obnovení bude pokračovat na pozadí. Průběh můžete znovu zobrazit dvojitým kliknutím na úlohu obnovení na řídicím panelu.

## <a name="volume-level-restore-to-an-alternate-machine"></a>Obnovení na úrovni svazku na alternativním počítači

Následující kroky vám pomůžou s obnovením všech zálohovaných souborů ve svazku na jiný počítač. Pomocí těchto kroků můžete obnovit data z Azure Backup, pokud dojde ke ztrátě celého serveru.

Tyto kroky zahrnují následující terminologii:

- *Zdrojový počítač* – původní počítač, ze kterého byla provedena záloha, a který není momentálně k dispozici.
- *Cílový počítač* – počítač, na který se data obnovují.
- *Vzorový trezor* – Recovery Services trezor, ke kterému jsou zaregistrované zdrojový počítač a cílový počítač.

> [!NOTE]
> Zálohy nelze obnovit do cílového počítače, na kterém je spuštěna dřívější verze operačního systému. Například zálohování z počítače se systémem Windows 7 lze obnovit v počítači se systémem Windows 7 (nebo novějším). Záloha pořízená z počítače s Windows 10 se nedá obnovit v počítači se systémem Windows 7.

1. Otevřete modul snap-in **Microsoft Azure Backup** v cílovém počítači.

1. Ujistěte se, že cílový počítač a zdrojový počítač jsou zaregistrované ve stejném trezoru Recovery Services.

1. Vyberte **obnovit data** a otevřete tak **Průvodce obnovením dat**.

    ![Snímek obrazovky Azure Backup s zvýrazněnou možností obnovit data (obnovit do alternativního počítače)](./media/backup-azure-restore-windows-server/recover.png)

1. Na stránce **Začínáme** vyberte **jiný server**.

    ![Snímek obrazovky s průvodcem obnovením dat Začínáme stránce (obnovení do alternativního počítače)](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

1. Zadejte soubor s přihlašovacími údaji úložiště, který odpovídá trezoru ukázek, a vyberte **Další**.

    Pokud je soubor přihlašovacích údajů trezoru neplatný (nebo vypršela jeho platnost), [Stáhněte si nový soubor s přihlašovacími údaji trezoru z trezoru ukázek](backup-azure-file-folder-backup-faq.md#where-can-i-download-the-vault-credentials-file) v Azure Portal. Po zadání platného pověření trezoru se zobrazí název odpovídajícího úložiště záloh.

1. Na stránce **Vybrat záložní server** vyberte zdrojový počítač ze seznamu zobrazených počítačů a zadejte heslo. Pak vyberte **Další**.

    ![Snímek obrazovky Průvodce obnovením dat vybrat záložní server stránku (obnovit do alternativního počítače)](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

1. Na stránce **Vybrat režim obnovení** klikněte na tlačítko **Hlasitost**  >  **Další**.

    ![Vybrat režim obnovení](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. Na stránce **Vybrat svazek a datum** vyberte svazek, který chcete obnovit.

    V kalendáři vyberte bod obnovení. Kalendářní data **tučně** označují dostupnost alespoň jednoho bodu obnovení. Pokud je v jednom dni k dispozici více bodů obnovení, vyberte konkrétní bod obnovení z rozevírací nabídky **čas** .

     ![Vybrat svazek a datum](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. Na stránce **zadat možnosti obnovení** nakonfigurujte chování při obnovení.
    1. Vyberte cíl obnovení:
        - **Původní umístění**: obnovit data do původní cesty.
        - **Jiné umístění**: zadejte alternativní umístění pro obnovení dat.
    1. Vyberte chování pro **případ, kdy se položky v záloze již nacházejí v cíli obnovení**:
        - **Vytvořit kopie, abyste měli obě verze**: Pokud už soubor se stejným názvem existuje, data v bodu obnovení se obnoví jako kopie. Kopie bude mít lokalizovanou předponu názvu souboru s použitím místního času úlohy obnovení v jednom z následujících formátů:
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Přepsat existující verze obnovenými verzemi**: Pokud již soubor se stejným názvem existuje, bude obsah nahrazen daty v bodu obnovení.
        - **Neobnovujte položky, které už existují v cíli obnovení**: Pokud už soubor se stejným názvem existuje, přeskočí se.
    1. **Povolte možnost obnovit seznam řízení přístupu (ACL) pro soubor nebo složku, která se** má obnovit, pokud by měl být soubor obnoven s původními oprávněními v bodu obnovení.
        ![Zadat možnosti obnovení](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. Ověřte podrobnosti o obnovení v podokně **potvrzení** a vyberte možnost **obnovit**.

    ![Podrobnosti o potvrzení](./media/restore-all-files-volume-mars/confirmation-details.png)

1. Na stránce **Průběh obnovení** Sledujte průběh úlohy obnovení. Průvodce může být také bezpečně uzavřen a operace obnovení bude pokračovat na pozadí. Průběh můžete znovu zobrazit dvojitým kliknutím na úlohu obnovení na řídicím panelu.

## <a name="next-steps"></a>Další kroky

- Teď, když jste obnovili své soubory a složky, můžete [Spravovat zálohy](backup-azure-manage-windows-server.md).
- Najděte [běžné otázky týkající se zálohování souborů a složek](backup-azure-file-folder-backup-faq.md).
