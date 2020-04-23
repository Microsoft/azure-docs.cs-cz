---
title: Zálohování sdílených složek Azure v Azure Portal
description: Naučte se používat Azure Portal k zálohování sdílených složek Azure v trezoru Recovery Services.
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: da2c7fa4cc5c3b7b948604a6f6d3999671cb3697
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101256"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Zálohování sdílených složek Azure v trezoru Recovery Services

Tento článek vysvětluje, jak používat Azure Portal k zálohování [sdílených složek Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

V tomto článku se dozvíte, jak:

* Vytvořte trezor služby Recovery Services.
* Zjistit sdílené složky a nakonfigurovat zálohy.
* Spusťte úlohu zálohování na vyžádání a vytvořte bod obnovení.

## <a name="prerequisites"></a>Požadované součásti

* Identifikujte nebo vytvořte [trezor Recovery Services](#create-a-recovery-services-vault) ve stejné oblasti jako účet úložiště, který hostuje sdílenou složku.
* Ujistěte se, že se sdílená složka nachází v jednom z [podporovaných typů účtů úložiště](azure-file-share-support-matrix.md).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Úprava replikace úložiště

Ve výchozím nastavení trezory používají [geograficky redundantní úložiště (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Pokud je trezor vaším primárním zálohovacím mechanismem, doporučujeme použít GRS.
* Jako možnost s nízkými náklady můžete použít [místně redundantní úložiště (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) .

Postup úpravy typu replikace úložiště:

1. V novém trezoru vyberte v části **Nastavení** možnost **vlastnosti** .

1. Na stránce **vlastnosti** v části **Konfigurace zálohování**vyberte **aktualizovat**.

1. Vyberte typ replikace úložiště a vyberte **Uložit**.

    ![Aktualizovat konfiguraci zálohování](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Po nastavení trezoru a obsahujícího zálohované položky nemůžete upravit typ replikace úložiště. Pokud to chcete provést, je třeba znovu vytvořit trezor.
>

## <a name="discover-file-shares-and-configure-backup"></a>Zjistit sdílené složky a nakonfigurovat zálohování

1. V [Azure Portal](https://portal.azure.com/)otevřete trezor Recovery Services, který chcete použít k zálohování sdílené složky.

1. Na řídicím panelu **trezoru Recovery Services** vyberte **+ záloha**.

   ![Trezor služby Recovery Services](./media/backup-afs/recovery-services-vault.png)

    a. V nastavení **cíl zálohování**nastavte, **kde je vaše úloha spuštěná?** do **Azure**.

    ![Vybrat sdílenou složku Azure jako cíl zálohování](./media/backup-afs/backup-goal.png)

    b.  V části **co chcete zálohovat?** v rozevíracím seznamu vyberte **Azure File Share** .

    c.  Vyberte **zálohování** pro registraci rozšíření sdílené složky Azure v trezoru.

    ![Vyberte zálohování pro přidružení sdílené složky Azure k trezoru.](./media/backup-afs/register-extension.png)

1. Po výběru **zálohování**se otevře podokno **zálohování** s výzvou k výběru účtu úložiště ze seznamu zjištěných podporovaných účtů úložiště. Jsou buď přidruženy k tomuto trezoru, nebo přítomny ve stejné oblasti jako trezor, ale ještě nejsou přidružené k žádnému Recovery Servicesmu trezoru.

1. V seznamu zjištěných účtů úložiště vyberte účet a vyberte **OK**. Azure vyhledá v účtu úložiště sdílené složky, které se dají zálohovat. Pokud jste nedávno přidali sdílené složky a nevidíte je v seznamu, počkejte, než se sdílené složky zobrazí.

    ![Zjišťují se sdílené složky.](./media/backup-afs/discovering-file-shares.png)

1. V seznamu **sdílené složky** vyberte jednu nebo více sdílených souborů, které chcete zálohovat. Vyberte **OK**.

   ![Vybrat sdílené složky](./media/backup-afs/select-file-shares.png)

1. Po zvolení sdílených složek se nabídka **zálohování** přepne na **zásady zálohování**. V této nabídce vyberte buď existující zásadu zálohování, nebo vytvořte novou. Pak vyberte **Povolit zálohování**.

    ![Vybrat zásady zálohování](./media/backup-afs/select-backup-policy.png)

Po nastavení zásad zálohování se v naplánovaném čase povede snímek sdílených složek. Bod obnovení je uložen také pro zvolené období.

>[!NOTE]
>Azure Backup teď podporuje zásady s denním/týdenním/měsíčním/ročním uchováváním záloh sdílené složky Azure.

## <a name="create-an-on-demand-backup"></a>Vytvoření zálohy na vyžádání

V některých případech můžete chtít vytvořit snímek zálohy nebo bod obnovení mimo čas naplánovaný v zásadách zálohování. Běžným důvodem pro vygenerování zálohy na vyžádání je napravo od nakonfigurované zásady zálohování. Na základě plánu v zásadách zálohování může být hodiny nebo dny až do chvíle, kdy se snímek povede. Pokud chcete svá data chránit před zapojením zásady zálohování, vyvolejte zálohování na vyžádání. Vytvoření zálohy na vyžádání se často vyžaduje předtím, než provedete plánované změny sdílených složek.

### <a name="create-a-backup-job-on-demand"></a>Vytvoření úlohy zálohování na vyžádání

1. Otevřete Recovery Services trezor, který jste použili k zálohování sdílené složky. V podokně **Přehled** vyberte **položky zálohování** v části **chráněné položky** .

   ![Vybrat zálohované položky](./media/backup-afs/backup-items.png)

1. Po výběru **položek zálohování**se vedle podokna **přehledu** zobrazí nové podokno se seznamem všech **typů správy zálohování** .

   ![Seznam typů správy zálohování](./media/backup-afs/backup-management-types.png)

1. V seznamu **typ správy zálohování** vyberte možnost **Azure Storage (soubory Azure)**. Zobrazí se seznam všech sdílených složek a odpovídajících účtů úložiště, které se zálohují pomocí tohoto trezoru.

   ![Zálohované položky Azure Storage (soubory Azure)](./media/backup-afs/azure-files-backup-items.png)

1. V seznamu sdílených složek Azure vyberte sdílenou složku, kterou chcete. Zobrazí se podrobnosti o **zálohovaných položkách** . V nabídce **zálohovaná položka** vyberte **Zálohovat nyní**. Vzhledem k tomu, že je tato úloha zálohování na vyžádání, nejsou k bodu obnovení přidruženy žádné zásady uchovávání.

   ![Vyberte zálohovat hned](./media/backup-afs/backup-now.png)

1. Otevře se podokno **Zálohovat nyní** . Zadejte poslední den, ke kterému chcete uchovávat bod obnovení. Pro zálohování na vyžádání můžete mít maximálně 10 let.

   ![Zvolit datum uchovávání](./media/backup-afs/retention-date.png)

1. Výběrem **OK** potvrďte spuštěnou úlohu zálohování na vyžádání.

1. Monitorujte oznámení na portálu, abyste měli přehled o dokončení spuštění úlohy zálohování. Průběh úlohy můžete monitorovat na řídicím panelu trezoru. Vyberte > **probíhající** **úlohy zálohování**.

>[!NOTE]
>Azure Backup uzamkne účet úložiště při konfiguraci ochrany pro jakoukoli sdílenou složku v příslušném účtu. Tato operace zajišťuje ochranu před náhodným odstraněním účtu úložiště s zálohovanými sdílenými složkami.

## <a name="best-practices"></a>Osvědčené postupy

* Neodstraňujte snímky vytvořené pomocí Azure Backup. Odstranění snímků může způsobit ztrátu bodů obnovení nebo selhání obnovení.

* Neodstraňujte zámek pořízený na účtu úložiště pomocí Azure Backup. Pokud zámek odstraníte, bude váš účet úložiště náchylný k nechtěnému odstranění a pokud je odstraněný, ztratíte snímky nebo zálohy.

## <a name="next-steps"></a>Další kroky

Naučte se:

* [Obnovení sdílených složek Azure](restore-afs.md)
* [Správa záloh sdílených složek Azure](manage-afs-backup.md)
