---
title: Zálohování sdílených složek Azure v Azure Portal
description: Naučte se používat Azure Portal k zálohování sdílených složek Azure v trezoru Recovery Services.
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: b2e2053857147513a95b3ae72b82d55450ebcffa
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294510"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Zálohování sdílených složek Azure v trezoru Recovery Services

Tento článek vysvětluje, jak používat Azure Portal k zálohování [sdílených složek Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

V této příručce se naučíte tyto postupy:

* Vytvoření trezoru Služeb zotavení
* Zjistit sdílené složky a nakonfigurovat zálohy
* Spuštění úlohy zálohování na vyžádání pro vytvoření bodu obnovení

## <a name="prerequisites"></a>Požadavky

* Identifikujte nebo vytvořte [Recovery Services trezor](#create-a-recovery-services-vault) ve stejné oblasti jako účet úložiště, který hostuje sdílenou složku.

* Ujistěte se, že se sdílená složka nachází v jednom z [podporovaných typů účtů úložiště](#limitations-for-azure-file-share-backup-during-preview).

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Omezení zálohování sdílených složek Azure během období Preview

Zálohování sdílených složek Azure je ve verzi Preview. Podporují se sdílené složky Azure v účtech úložiště pro obecné účely v1 i pro obecné účely v2. Toto jsou omezení pro zálohování sdílených složek Azure:

* Podpora pro zálohování sdílených složek Azure v účtech úložiště s replikací [zóny redundantního úložiště](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) (ZRS) je v současné době omezená na [tyto oblasti](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares).
* Azure Backup aktuálně podporuje konfiguraci naplánovaných jednorázových záloh sdílených složek Azure.
* Maximální počet plánovaných záloh je jedna za den.
* Maximální počet záloh na vyžádání jsou čtyři za den.
* Používejte v účtu úložiště [zámky prostředků](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest), abyste zabránili nechtěnému odstranění záloh v trezoru služby Recovery Services.
* Neodstraňujte snímky vytvořené službou Azure Backup. Odstranění snímků může způsobit ztrátu bodů obnovení nebo selhání obnovení.
* Neodstraňujte sdílené složky, které jsou chráněné pomocí Azure Backup. Aktuální řešení odstraní všechny snímky vybrané Azure Backup po odstranění sdílené složky, takže všechny body obnovení budou ztraceny.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Úprava replikace úložiště

Ve výchozím nastavení trezory používají [geograficky redundantní úložiště (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Pokud je trezor vaším primárním zálohovacím mechanismem, doporučujeme použít GRS.

* Jako možnost s nízkými náklady můžete použít [místně redundantní úložiště (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) .

Typ replikace úložiště upravte takto:

1. V novém trezoru klikněte na **vlastnosti** v části **Nastavení** .

2. V části **vlastnosti**v části **Konfigurace zálohování**klikněte na **aktualizovat**.

3. Vyberte typ replikace úložiště a klikněte na **Uložit**.

    ![Aktualizovat konfiguraci zálohování](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Po nastavení trezoru a obsahujícího zálohované položky nemůžete upravit typ replikace úložiště. Pokud to chcete provést, je třeba znovu vytvořit trezor.
>

## <a name="discover-file-shares-and-configure-backup"></a>Zjistit sdílené složky a nakonfigurovat zálohování

1. V [Azure Portal](https://portal.azure.com/)otevřete trezor Recovery Services, který chcete použít k zálohování sdílené složky.

2. Na řídicím panelu **trezoru Recovery Services** vyberte **+ záloha**.

   ![Trezor služby Recovery Services](./media/backup-afs/recovery-services-vault.png)

   a. V nastavení **cíl zálohování**nastavte, **kde je vaše úloha spuštěná?** do **Azure**.

    ![Vybrat sdílenou složku Azure jako cíl zálohování](./media/backup-afs/backup-goal.png)

    b.    V části **co chcete zálohovat**, vyberte v rozevírací nabídce možnost **sdílená složka Azure** .

    c.    Kliknutím na **zálohovat** zaregistrujete rozšíření sdílené složky Azure v trezoru.

      ![Kliknutím na zálohovat přidružíte sdílenou složku Azure k trezoru.](./media/backup-afs/register-extension.png)

3. Po kliknutí na **zálohovat**se otevře okno zálohování s výzvou k výběru účtu úložiště ze seznamu zjištěných podporovaných účtů úložiště. Jsou buď přidruženy k tomuto trezoru, nebo přítomny ve stejné oblasti jako trezor, ale ještě nejsou přidružené k žádnému Recovery Servicesmu trezoru.

   ![Výběr účtu úložiště](./media/backup-afs/select-storage-account.png)

4. V seznamu zjištěných účtů úložiště vyberte účet a klikněte na **OK**. Azure v účtu úložiště vyhledá sdílené složky, které je možné zálohovat. Pokud jste nedávno přidali sdílené složky a nevidíte je v seznamu, počkejte nějakou dobu, než se sdílené složky zobrazí.

    ![Zjišťují se sdílené složky.](./media/backup-afs/discovering-file-shares.png)

5. V seznamu **sdílené složky** vyberte jednu nebo více sdílených souborů, které chcete zálohovat, a klikněte na tlačítko **OK**.

6. Po výběru sdílených složek se nabídka **zálohování** přepne na **zásady zálohování**. V této nabídce vyberte buď existující zásadu zálohování, nebo vytvořte novou, a pak klikněte na **Povolit zálohování**.

    ![Vybrat zásady zálohování](./media/backup-afs/select-backup-policy.png)

Po vytvoření zásady zálohování se v naplánovaném čase pořídí snímek sdílených složek a po zvolenou dobu se bude uchovávat bod obnovení.

## <a name="create-an-on-demand-backup"></a>Vytvoření zálohy na vyžádání

V některých případech můžete chtít vytvořit snímek zálohy nebo bod obnovení mimo čas naplánovaný v zásadách zálohování. Běžným důvodem pro vygenerování zálohy na vyžádání je napravo od nakonfigurované zásady zálohování. V závislosti na plánu v zásadě zálohování může pořízení snímku trvat hodiny nebo dny. Pokud chcete svá data chránit před zapojením zásady zálohování, vyvolejte zálohování na vyžádání. Vytvoření zálohy na vyžádání se často vyžaduje předtím, než provedete plánované změny sdílených složek.

### <a name="to-create-an-on-demand-backup"></a>Vytvoření zálohy na vyžádání

1. Otevřete trezor Recovery Services, který jste použili k zálohování sdílené složky, a v části **chráněné položky** v okně **Přehled** klikněte na **zálohovat položky** .

   ![Klikněte na zálohované položky.](./media/backup-afs/backup-items.png)

2. Po kliknutí na **zálohované položky**se vedle okna s **přehledem** zobrazí nové okno se seznamem všech **typů správy zálohování** :

   ![Seznam typů správy zálohování](./media/backup-afs/backup-management-types.png)

3. V seznamu **typů správy zálohování**vyberte možnost **Azure Storage (soubory Azure)** . Zobrazí se seznam všech sdílených složek a odpovídajících účtů úložiště, které se zálohují pomocí tohoto trezoru.

   ![Zálohované položky Azure Storage (soubory Azure)](./media/backup-afs/azure-files-backup-items.png)

4. V seznamu sdílených složek Azure vyberte požadovanou sdílenou složku. Zobrazí se podrobnosti o **zálohovaných položkách** . V nabídce **zálohovaná položka** klikněte na **Zálohovat nyní**. Protože se jedná o úlohu zálohování na vyžádání, není k bodu obnovení přidružená žádná zásada uchovávání.

   ![Klikněte na zálohovat hned](./media/backup-afs/backup-now.png)

5. Otevře se okno **Zálohovat nyní** . Zadejte poslední den, ke kterému chcete uchovávat bod obnovení. Pro zálohování na vyžádání můžete mít maximálně 10 let.

   ![Zvolit datum uchovávání](./media/backup-afs/retention-date.png)

6. Kliknutím na **OK** potvrďte spuštění úlohy zálohování na vyžádání.

7. Monitorujte oznámení na portálu, abyste měli přehled o dokončení spuštění úlohy zálohování. Průběh úlohy můžete monitorovat na řídicím panelu trezoru > **úlohy zálohování** ** > probíhá**.

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [obnovit sdílené složky Azure](restore-afs.md) .

* Naučte se [Spravovat zálohy sdílených složek Azure](manage-afs-backup.md)
