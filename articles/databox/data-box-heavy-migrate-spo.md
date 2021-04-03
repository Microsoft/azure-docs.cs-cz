---
title: Použití Azure Data Box Heavy k přesunutí obsahu sdílené složky do SharePointu Online
description: V tomto kurzu se dozvíte, jak migrovat obsah sdílené složky do sdílení bodu online pomocí Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: how-to
ms.date: 07/18/2019
ms.author: alkohli
ms.openlocfilehash: b00a944b69b8492d4aa00258154bf00f1c66ad33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92127110"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>Použití Azure Data Box Heavy k migraci obsahu sdílené složky do SharePointu Online

Pomocí Azure Data Box Heavy a nástroje pro migraci služby SharePoint (SPMT) můžete snadno migrovat obsah sdílené složky do SharePointu Online a OneDrive. Pomocí Data Box Heavy můžete odebrat závislost na připojení WAN (Wide Area Network) a přenést data.

Data Box Microsoft Azure je služba, která umožňuje objednat zařízení od portál Microsoft Azure. Potom můžete zkopírovat terabajty dat ze serverů do zařízení. Po odeslání zpátky do Microsoftu se vaše data zkopírují do Azure. V závislosti na velikosti dat, která chcete přenést, si můžete vybrat z těchto míst:

- [Data box disk](./data-box-disk-overview.md) s využitím kapacity 35 TB pro malé až střední datové sady na objednávku.
- Pro středně velké datové sady je [data box](./data-box-overview.md) s využitím kapacity 80 TB na zařízení.
- [Data box Heavy](./data-box-heavy-overview.md) s využitím kapacity 770 TB na zařízení pro velké datové sady.

Tento článek se týká zejména používání Data Box Heavy k migraci obsahu sdílené složky do SharePointu Online.

## <a name="requirements-and-costs"></a>Požadavky a náklady

### <a name="for-data-box-heavy"></a>Pro Data Box Heavy

- Data Box Heavy je k dispozici pouze pro smlouva Enterprise (EA), poskytovatele Cloud Solution Provider (CSP) nebo nabídky sponzorství Azure. Pokud vaše předplatné nepatří do žádného z výše uvedených typů, kontaktujte podpora Microsoftu k upgradu vašeho předplatného nebo si prohlédněte [ceny předplatného Azure](https://azure.microsoft.com/pricing/).
- Použití Data Box Heavy je zpoplatněné. Nezapomeňte si prohlédnout [ceny data box Heavy](https://azure.microsoft.com/pricing/details/databox/heavy/).


### <a name="for-sharepoint-online"></a>Pro SharePoint Online

- Projděte si [minimální požadavky na nástroj pro migraci SharePoint (SPMT)](/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

## <a name="workflow-overview"></a>Přehled pracovního postupu

Tento pracovní postup vyžaduje, abyste provedli kroky Azure Data Box Heavy a také na SharePointu Online.
Následující kroky se vztahují k vašemu Azure Data Box Heavy.

1. Pořadí Azure Data Box Heavy.
2. Přijmout a nastavit zařízení.
3. Zkopírujte data z místní sdílené složky do složky pro soubory Azure na vašem zařízení.
4. Až se kopie dokončí, pošlete zařízení zpátky podle pokynů.
5. Počkejte na dokončení nahrávání dat do Azure.

Následující postup se týká SharePointu Online.

6. Vytvořte virtuální počítač v Azure Portal a připojte sdílenou složku Azure.
7. Nainstalujte nástroj SPMT na virtuální počítač Azure.
8. Spusťte nástroj SPMT pomocí sdílené složky Azure jako *zdroje*.
9. Proveďte poslední kroky tohoto nástroje.
10. Ověřte a potvrďte svá data.

## <a name="use-data-box-heavy-to-copy-data"></a>Použití Data Box Heavy ke kopírování dat

Při kopírování dat do Data Box Heavy proveďte následující kroky.

1. [Seřazení data box Heavy](data-box-heavy-deploy-ordered.md).
2. Po přijetí Data Box Heavy [nastavte data box Heavy](data-box-heavy-deploy-set-up.md). Můžete na svém zařízení kabelovat a konfigurovat oba uzly.
3. [Zkopírujte data do Azure Data box Heavy](data-box-heavy-deploy-copy-data.md). Při kopírování nezapomeňte:

    - Ke zkopírování dat použijte pouze složku *StorageAccountName_AzFile* v data box Heavy. Důvodem je to, že chcete, aby data byla ukončena ve sdílené složce Azure, nikoli v objektech blob bloku nebo objektech blob stránky.
    - Zkopírujte soubory do složky v *StorageAccountName_AzFile* složce. Sdílená složka v rámci složky *StorageAccountName_AzFile* vytvoří sdílenou složku. Soubory zkopírované přímo do složky *StorageAccountName_AzFile* selžou a nahrají se jako objekty blob bloku. Toto je sdílená složka, kterou budete na VIRTUÁLNÍm počítači připojeni v dalším kroku.
    - Zkopírujte data do obou uzlů Data Box Heavy.
3. Na svém zařízení spusťte [Příprava k odeslání](data-box-heavy-deploy-picked-up.md#prepare-to-ship) . Úspěšná Příprava na dodávání zajišťuje úspěšné nahrání souborů do Azure.
4. [Vrátí zařízení](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back).
5. [Ověřte, že se data nahrávají do Azure](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure).

## <a name="use-spmt-to-migrate-data"></a>Použití SPMT k migraci dat

Po obdržení potvrzení od týmu Azure data po dokončení vaší kopie dat přejděte k migraci dat do SharePointu Online.

Pro zajištění nejlepšího výkonu a konektivity doporučujeme vytvořit virtuální počítač Azure (VM).

1. Přihlaste se k Azure Portal a pak [vytvořte virtuální počítač](../virtual-machines/windows/quick-create-portal.md).
2. [Připojte sdílenou složku Azure do virtuálního počítače](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer).
3. [Stáhněte si nástroj pro migraci SharePointu](https://spmtreleasescus.blob.core.windows.net/install/default.htm) a nainstalujte ho na virtuální počítač Azure.
4. Spusťte nástroj pro migraci služby SharePoint. Klikněte na **Přihlásit** se a zadejte uživatelské jméno a heslo pro svůj pracovní nebo školní účet.
5. Když se zobrazí výzva, **kde jsou vaše data?** vyberte **sdílet soubor**. Zadejte cestu ke sdílené složce Azure, kde se nacházejí vaše data.
6. Sledujte zbývající výzvy jako normální, včetně vašeho cílového umístění. Další informace najdete [v tématu Jak používat nástroj pro migraci služby SharePoint](/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

> [!IMPORTANT]
> - Rychlost, s jakou se data ingestují do SharePointu Online, ovlivňují několik faktorů bez ohledu na to, jestli data v Azure už máte. Porozumění těmto faktorům vám pomůže naplánovat a maximalizovat efektivitu migrace.  Další informace najdete na [webu SharePoint Online a na rychlost migrace na OneDrive](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed).
> - Při migraci dat do SharePointu Online existuje riziko ztráty stávajících oprávnění k souborům. Můžete také ztratit určitá metadata, jako je například *Vytvořeno pomocí* a *Datum změny*.

## <a name="next-steps"></a>Další kroky

[Seřazení Data Box Heavy](./data-box-heavy-deploy-ordered.md)