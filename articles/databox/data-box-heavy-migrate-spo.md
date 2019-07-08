---
title: Použití Azure Data Box náročné migraci obsah sdílené složky souboru na Sharepointu Online | Dokumentace Microsoftu
description: Pomocí tohoto kurzu se dozvíte, jak migrovat obsah sdílené složky souboru do sdílené složky bodu Online pomocí váš Azure Data Box náročné
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: d74539ec1de8f503b0d0e423adf6273d1422fed5
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592334"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>Použijte silná Azure Data Box k migraci obsahu sdílené složky souboru na Sharepointu Online

Použijte váš Azure Data Box zátěži a nástroj pro migraci SharePoint (SPMT) snadno přeneste obsah sdílené složky souboru na Sharepointu Online a OneDrive. S použitím dat pole náročné, můžete odebrat závislost na odkaz na vaši síť (WAN) WAN k přenosu dat.

Microsoft Azure Data Box je služba, která umožňuje uspořádat zařízení z portálu Microsoft Azure. Pak můžete zkopírovat terabajty dat z vašich serverů do zařízení. Po jeho dodání zpět společnosti Microsoft, vaše data zkopírována do Azure. V závislosti na velikosti dat, kterou chcete převést můžete vybrat z:

- [Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) s 35 TB využitelné kapacity na objednávky pro malé až střední datové sady.
- [Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) s 80 TB využitelné kapacity podle zařízení pro střední a velkých datových sad.
- [Data Box náročné](https://docs.microsoft.com/azure/databox/data-box-heavy-overview) s 770 TB využitelné kapacity podle zařízení pro velké datové sady.

Tento článek konkrétně mluví o tom, jak používat silná pole dat k migraci obsahu sdílené složky souboru na Sharepointu Online.

## <a name="requirements-and-costs"></a>Požadavky a náklady

### <a name="for-data-box-heavy"></a>Pro Data Box náročná na výkon

- Data Box náročné je k dispozici pouze pro Enterprise Agreement (EA), poskytovatele Cloud solution provider (CSP), nebo nabídek Azure sponsorship. Pokud vaše předplatné nespadá do některé z výše uvedených typů, obraťte se na Microsoft Support upgradovat vaše předplatné nebo naleznete v tématu [ceny za předplatné Azure](https://azure.microsoft.com/pricing/).
- Je poplatek za použití dat pole náročné. Přečtěte si [Data Box náročné ceny](https://azure.microsoft.com/pricing/details/databox/heavy/).


### <a name="for-sharepoint-online"></a>Pro SharePoint Online

- Zkontrolujte [minimální požadavky pro migraci nástroje SharePoint (SPMT)](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

## <a name="workflow-overview"></a>Přehled pracovního postupu

Tento pracovní postup vyžaduje, abyste proveďte kroky v Azure Data Box náročné i na Sharepointového online.
Následující kroky se vztahují na váš Azure Data Box náročné.

1. Pořadí Azure Data Box velmi náročné.
2. Příjem a nastavit vaše zařízení.
3. Kopírování dat ze souboru místní sdílené složky do složky pro soubory Azure na vašem zařízení.
4. Jakmile se kopírování dokončí, odešlete zařízení zpět podle pokynů.
5. Počkejte, data se kompletně nahrát do Azure.

Následující kroky se týkají Sharepointového online.

6. Vytvoření virtuálního počítače na webu Azure Portal a připojit sdílenou složku Azure.
7. Nainstalujte nástroj SPMT na virtuálním počítači Azure.
8. Spusťte nástroj SPMT pomocí sdílené složky Azure jako *zdroj*.
9. Dokončení závěrečné kroky tohoto nástroje.
10. Zkontrolujte a potvrďte své údaje.

## <a name="use-data-box-heavy-to-copy-data"></a>Použití dat pole náročné ke kopírování dat

Proveďte následující kroky ke zkopírování dat do vašich dat pole náročné.

1. [Uspořádat pole těžkých vaše Data](data-box-heavy-deploy-ordered.md).
2. Jakmile se zobrazí vaše Data Box vysoké, [Data Box náročné nastavit](data-box-heavy-deploy-set-up.md). Budete zapojení a konfigurace obou uzlů ve vašem zařízení.
3. [Kopírování dat do služby Azure Data Box náročné](data-box-heavy-deploy-copy-data.md). Při kopírování, ujistěte se, že na:

    - Použít pouze *AzureFile* složky na Data pole náročné kopírovat data. Je to proto, že chcete data ukládaly do sdílené složky Azure, ne v objekty BLOB bloku nebo objekty BLOB stránky.
    - Zkopírujte soubory do složky v rámci *AzureFile* složky. Podsložku v rámci *AzureFile* vytvoří sdílenou složku. Soubory zkopírován přímo do *AzureFile* složky selhání a jsou odeslány jako objekty BLOB bloku. Toto je sdílené složce, která se připojit na virtuálním počítači v dalším kroku.
    - Kopírování dat do obou uzlech vaše Data Box náročné.
3. Spustit [přípravu k odeslání](data-box-heavy-deploy-picked-up.md#prepare-to-ship) na vašem zařízení. Úspěšného přípravu k odeslání zajišťuje úspěšném nahrání souborů do Azure.
4. [Vrácení zařízení](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back).
5. [Ověřte nahrání dat do Azure](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure).

## <a name="use-spmt-to-migrate-data"></a>Použít SPMT pro migraci dat

Jakmile dostanete potvrzení od týmu Azure dat, který vaše kopie dat byla dokončena, můžete teď můžete přejít k migraci dat do Sharepointu Online.

Pro nejlepší výkon a připojení doporučujeme vytvořit virtuální počítač Azure (VM).

1. Přihlaste se k webu Azure portal a potom [vytvoření virtuálního počítače](../virtual-machines/windows/quick-create-portal.md).
2. [Připojení sdílené složky Azure do virtuálního počítače](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer).
3. [Stáhněte si nástroj pro migraci SharePoint](https://spmtreleasescus.blob.core.windows.net/install/default.htm) a nainstalujte na svém virtuálním počítači Azure.
4. Spusťte nástroj pro migraci služby SharePoint. Klikněte na tlačítko **přihlášení** a zadejte Office 365 uživatelské jméno a heslo.
5. Po zobrazení výzvy **kde jsou vaše data?** vyberte **sdílené**. Zadejte cestu ke sdílené složky Azure, kde se nachází vaše data.
6. Postupujte podle zbývajících výzev jako za normálních okolností, včetně cílového umístění. Další informace najdete v části [jak používat nástroj pro migraci SharePoint](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

> [!IMPORTANT]
> - Rychlost, jakou se data ingestují do Sharepointu Online je ovlivňován několika faktory, bez ohledu na to pokud již máte data v Azure. Pochopení těchto faktorů vám pomůže při plánování a maximalizovat efektivitu migrace.  Další informace najdete v části [migrace Sharepointu Online a Onedrivu rychlost](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed).
> - Existuje riziko ztráty stávajících oprávnění u souborů při migraci dat do Sharepointu Online. Také může ztratit určitá metadata, jako například *vytvořil* a *datum změny podle*.

## <a name="next-steps"></a>Další postup

[Uspořádat pole těžkých vaše Data](./data-box-heavy-deploy-ordered.md)