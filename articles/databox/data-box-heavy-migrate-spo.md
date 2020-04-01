---
title: Přesunutí obsahu sdílené složky do SharePointu Online pomocí Azure Data Box Heavy
description: V tomto kurzu se dozvíte, jak migrovat obsah sdílené složky do Share Point Online pomocí azure datové schránky Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/18/2019
ms.author: alkohli
ms.openlocfilehash: f97ea17551d4415f7ed6371853172cfde30fe4b6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77560044"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>Migrace obsahu sdílené složky do SharePointu Online pomocí Azure Data Box Heavy

Pomocí Azure Data Box Heavy a Nástroje pro migraci SharePointu (SPMT) můžete snadno migrovat obsah sdílené složky do SharePointu Online a OneDrivu. Pomocí funkce Data Box Heavy můžete odebrat závislost na propojení WAN (Wide-area Network) pro přenos dat.

Microsoft Azure Data Box je služba, která vám umožní objednat zařízení z portálu Microsoft Azure. Poté můžete kopírovat terabajty dat ze serverů do zařízení. Po odeslání zpět do Microsoftu se vaše data zkopírují do Azure. V závislosti na velikosti dat, která chcete přenést, si můžete vybrat z následujících:

- [Disk datové schránky](https://docs.microsoft.com/azure/databox/data-box-disk-overview) s využitelnou kapacitou 35 TB na objednávku pro malé a střední datové sady.
- [Datová schránka](https://docs.microsoft.com/azure/databox/data-box-overview) s využitelnou kapacitou 80 TB na zařízení pro středně velké až velké datové sady.
- [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-overview) s využitelnou kapacitou 770 TB na zařízení pro velké datové sady.

Tento článek konkrétně popisuje, jak pomocí datové schránky Heavy migrovat obsah sdílené složky do SharePointu Online.

## <a name="requirements-and-costs"></a>Požadavky a náklady

### <a name="for-data-box-heavy"></a>Pro Data Box Heavy

- Data Box Heavy je k dispozici jenom pro smlouvy Enterprise (EA), poskytovatele cloudových řešení (CSP) nebo sponzorské nabídky Azure. Pokud vaše předplatné nespadá do žádného z výše uvedených typů, obraťte se na podporu Microsoftu a požádejte o upgrade předplatného nebo si přečtěte [ceny předplatného Azure](https://azure.microsoft.com/pricing/).
- Za použití datové schránky Heavy se účtuje poplatek. Zkontrolujte ceny [Data Box Heavy](https://azure.microsoft.com/pricing/details/databox/heavy/).


### <a name="for-sharepoint-online"></a>Pro SharePoint Online

- Projděte si [minimální požadavky na Nástroj pro migraci služby SharePoint (SPMT).](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool)

## <a name="workflow-overview"></a>Přehled pracovního postupu

Tento pracovní postup vyžaduje, abyste provedli kroky na Azure Data Box Heavy a také na SharePointu Online.
Následující kroky se týkají vaší Azure Data Box Heavy.

1. Objednejte si Azure Data Box Heavy.
2. Přijímejte a nastavujte zařízení.
3. Zkopírujte data z místní sdílené složky do složky pro soubory Azure na vašem zařízení.
4. Po dokončení kopírování zasuzujte zařízení zpět podle pokynů.
5. Počkejte, až se data úplně nahrají do Azure.

Následující kroky se týkají SharePointu Online.

6. Vytvořte virtuální počítač na webu Azure Portal a připojte na něj sdílenou složku Azure.
7. Nainstalujte nástroj SPMT na virtuální počítač Azure.
8. Spusťte nástroj SPMT pomocí sdílené složky Azure jako *zdroj*.
9. Proveďte poslední kroky nástroje.
10. Ověřte a potvrďte svá data.

## <a name="use-data-box-heavy-to-copy-data"></a>Kopírování dat pomocí datové schránky Heavy

Následujícím postupem zkopírujte data do datové schránky Heavy.

1. [Objednejte si datovou schránku Heavy](data-box-heavy-deploy-ordered.md).
2. Po obdržení datové schránky Heavy [nastavte datovou schránku Heavy](data-box-heavy-deploy-set-up.md). Nakonfigurujete kabel a nakonfigurujete oba uzly v zařízení.
3. [Kopírování dat do Azure Data Box Heavy](data-box-heavy-deploy-copy-data.md). Při kopírování se ujistěte, že:

    - Ke kopírování dat použijte pouze *složku StorageAccountName_AzFile* v datové schránce Heavy. Důvodem je, že chcete, aby data skončila ve sdílené složce Azure, ne v blobů bloku nebo objektů blob stránky.
    - Zkopírujte soubory do složky ve *složce StorageAccountName_AzFile.* Podsložka v *StorageAccountName_AzFile* složky vytvoří sdílenou složku. Soubory zkopírované přímo do *StorageAccountName_AzFile* složky se nezdaří a jsou odeslány jako objekty BLOB bloku. Toto je sdílená složka, kterou připojíte na virtuální počítač v dalším kroku.
    - Zkopírujte data do obou uzlů datové schránky Heavy.
3. [Spusťte Připravte se](data-box-heavy-deploy-picked-up.md#prepare-to-ship) na odeslání na vašem zařízení. Úspěšná příprava na odeslání zajišťuje úspěšné nahrání souborů do Azure.
4. [Vraťte zařízení](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back).
5. [Ověřte nahrávání dat do Azure](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure).

## <a name="use-spmt-to-migrate-data"></a>Migrace dat pomocí SPMT

Až od datového týmu Azure obdržíte potvrzení, že vaše datová kopie byla dokončena, přejděte k migraci dat do SharePointu Online.

Pro dosažení nejlepšího výkonu a připojení doporučujeme vytvořit virtuální počítač Azure (VM).

1. Přihlaste se k portálu Azure a pak [vytvořte virtuální počítač](../virtual-machines/windows/quick-create-portal.md).
2. [Připojení sdílené složky Azure na virtuální počítač](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer).
3. [Stáhněte si nástroj migrace sharepointu](https://spmtreleasescus.blob.core.windows.net/install/default.htm) a nainstalujte ho do virtuálního počítače Azure.
4. Spusťte Nástroj pro migraci služby SharePoint. Klikněte na **Přihlásit se** a zadejte své uživatelské jméno a heslo Office 365.
5. Po zobrazení výzvy Kde jsou **File share** **data?** Zadejte cestu ke sdílené složce Azure, kde se nacházejí vaše data.
6. Postupujte podle zbývajících výzev jako obvykle, včetně cílové polohy. Další informace naleznete v části [Jak používat Nástroj pro migraci služby SharePoint](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

> [!IMPORTANT]
> - Rychlost, s jakou se data ingestují do SharePointu Online, je ovlivněna několika faktory, bez ohledu na to, jestli už máte data v Azure. Pochopení těchto faktorů vám pomůže naplánovat a maximalizovat efektivitu migrace.  Další informace najdete v [SharePointu Online a rychlost migrace na OneDrivu](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed).
> - Při migraci dat do SharePointu Online hrozí ztráta stávajících oprávnění k souborům. Můžete také ztratit určitá metadata, například *Vytvořeno podle* a *Datum změny .*

## <a name="next-steps"></a>Další kroky

[Objednejte si datovou schránku Heavy](./data-box-heavy-deploy-ordered.md)