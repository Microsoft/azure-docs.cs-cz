---
title: Sledovat a protokolovat Azure Data Box Azure Data Box Heavy události pro pořadí importu | Microsoft Docs
description: Popisuje, jak sledovat a protokolovat události v různých fázích Azure Data Box a Azure Data Box Heavy pořadí importu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: a9304936f746b82b59550d62e8b60a9e0035d188
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92147927"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy-import-order"></a>Sledování a protokolování událostí pro Azure Data Box a Azure Data Box Heavy pořadí importu

Pořadí importu Data Box nebo Data Box Heavy prochází následujícími kroky: pořadí, nastavení, kopírování dat, návrat, nahrání do Azure a ověření a mazání dat. V souladu s každým krokem v pořadí můžete provést několik akcí pro řízení přístupu k objednávce, Auditovat události, sledovat pořadí a interpretovat různé protokoly, které jsou vygenerovány.

V následující tabulce je uveden souhrn Data Box nebo Data Box Heavy kroků importu a nástrojů, které jsou k dispozici pro sledování a auditování pořadí v průběhu každého kroku.

| Data Box fáze objednávky importu       | Nástroj pro sledování a audit                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Vytvoření objednávky               | [Nastavení řízení přístupu v objednávce prostřednictvím Azure RBAC](#set-up-access-control-on-the-order)                                                    |
| Zpracování objednávky            | [Sledovat pořadí](#track-the-order) přes <ul><li> portál Azure </li><li> Web lodního dopravce </li><li>E-mailová oznámení</ul> |
| Nastavení zařízení              | Přístup k přihlašovacím údajům zařízení přihlášení k [protokolům aktivit](#query-activity-logs-during-setup)                                              |
| Kopírování dat do zařízení        | [Zobrazit *error.xml* soubory](#view-error-log-during-data-copy) pro kopírování dat                                                             |
| Příprava k odeslání            | [Zkontrolujte soubory kusovníku](#inspect-bom-during-prepare-to-ship) nebo soubory manifestu na zařízení.                                      |
| Nahrávání dat do Azure       | [Kontrola chyb v protokolech kopírování](#review-copy-log-during-upload-to-azure) při nahrávání dat v datovém centru Azure                         |
| Data mazání ze zařízení   | [Zobrazit řetěz protokolů o úschově](#get-chain-of-custody-logs-after-data-erasure) včetně protokolů auditu a historie objednávek                |

Tento článek podrobně popisuje různé mechanismy a nástroje, které jsou k dispozici pro sledování a audit Data Box nebo Data Box Heavyho pořadí importu. Informace v tomto článku se vztahují na Data Box a Data Box Heavy import objednávek. V následujících částech se všechny odkazy na Data Box vztahují také na Data Box Heavy.

## <a name="set-up-access-control-on-the-order"></a>Nastavení řízení přístupu na objednávce

Můžete určit, kdo má mít přístup k vaší objednávce při prvním vytvoření objednávky. Nastavte role Azure v různých oborech, abyste mohli řídit přístup k pořadí Data Box. Role Azure určuje typ přístupu – čtení i zápis, čtení i zápis v podmnožině operací.

Pro službu Azure Data Box lze definovat dvě role:

- **Data box Reader** – mají přístup jen pro čtení k objednávkám definovaným oborem. Mohou pouze zobrazit podrobnosti o objednávce. Nemůžou přistupovat k žádným jiným podrobnostem souvisejícím s účty úložiště ani upravovat podrobnosti objednávky, jako je například adresa a tak dále.
- **Přispěvatel data box** – může vytvořit objednávku pro přenos dat do daného účtu úložiště jenom v *případě, že už mají oprávnění k zápisu do účtu úložiště*. Pokud nemají přístup k účtu úložiště, nemůžou ani vytvořit Data Box objednávku ke zkopírování dat do účtu. Tato role nedefinuje žádné související oprávnění účtu úložiště ani neuděluje přístup k účtům úložiště.  

Chcete-li omezit přístup k objednávce, můžete:

- Přiřaďte roli na úrovni objednávky. Uživatel má pouze ta oprávnění definovaná rolemi k interakci s tímto konkrétním Data Box objednávka a nic jiného.
- Přiřaďte roli na úrovni skupiny prostředků, uživatel má přístup ke všem Data Box objednávkám v rámci skupiny prostředků.

Další informace o navrhovaných použitích služby Azure RBAC najdete v tématu [osvědčené postupy pro službu Azure RBAC](../role-based-access-control/best-practices.md).

## <a name="track-the-order"></a>Sledování objednávky

Můžete sledovat svou objednávku prostřednictvím Azure Portal a prostřednictvím webu přepravce. K dispozici jsou následující mechanismy pro sledování pořadí Data Box.

- Pokud chcete sledovat pořadí, v jakém je zařízení v datovém centru Azure nebo v místním prostředí, v Azure Portal můžete **> přehled na objednávku data box** .

    ![Zobrazit stav objednávky a sledovat ne](media/data-box-logs/overview-view-status-1.png)

- Pokud chcete sledovat pořadí, v jakém je zařízení v provozu, přečtěte si web pro místní dopravce, například web UPS v USA. Zadejte sledovací číslo přidružené k vaší objednávce.
- Data Box taky pošle e-mailová oznámení, kdykoli se změní stav objednávky na základě e-mailů, které jste zadali při vytvoření objednávky. Seznam všech stavů Data Box objednávek najdete v tématu [zobrazení stavu objednávky](data-box-portal-admin.md#view-order-status). Postup změny nastavení oznámení přidružených k objednávce najdete v tématu [úprava podrobností oznámení](data-box-portal-admin.md#edit-notification-details).

## <a name="query-activity-logs-during-setup"></a>Dotazování protokolů aktivit během instalace

- Vaše Data Box dorazí na vaše místní prostředí v uzamčeném stavu. K dispozici jsou přihlašovací údaje pro zařízení, které jsou k dispozici v Azure Portal pro vaši objednávku.  

    Když se nastaví Data Box, možná budete muset zjistit, kdo získal všechna oprávnění k těmto přihlašovacím údajům pro zařízení. Chcete-li zjistit, kdo získal do okna **pověření zařízení** , můžete zadat dotaz na protokoly aktivit.  Všechny akce, které zahrnují přístup k **podrobnostem o zařízení > přihlašovací údaje** , se zaprotokolují do protokolů aktivit jako `ListCredentials` akce.

    ![Dotazy na protokoly aktivit](media/data-box-logs/query-activity-log-1.png)

- Každé přihlášení k Data Box je zaznamenáno v reálném čase. Tyto informace jsou však k dispozici pouze v [protokolech auditu](#audit-logs) po úspěšném dokončení objednávky.

## <a name="view-error-log-during-data-copy"></a>Zobrazit protokol chyb během kopírování dat

Při kopírování dat do Data Box nebo Data Box Heavy se vygeneruje chybový soubor, pokud dojde k problémům s kopírovanými daty.

### <a name="errorxml-file"></a>Soubor Error.xml

Ujistěte se, že úlohy kopírování byly dokončeny bez chyb. Pokud během kopírování dojde k chybám, Stáhněte si protokoly ze stránky **připojit a kopírovat** .

- Pokud jste zkopírovali soubor, který není 512 bajtů zarovnaný do složky spravovaného disku na vašem Data Box, soubor se do pracovního účtu úložiště nenahrál jako objekt blob stránky. V protokolech se zobrazí chyba. Odeberte soubor a zkopírujte soubor, který je 512 bajtů zarovnaných.
- Pokud jste zkopírovali VHDX nebo dynamický virtuální pevný disk nebo Rozdílový virtuální pevný disk (tyto soubory nejsou podporované), zobrazí se v protokolech chyba.

Tady je ukázka *error.xml* různých chyb při kopírování na spravované disky.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Tady je ukázka *error.xml* pro různé chyby při kopírování do objektů blob stránky.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Tady je ukázka *error.xml* pro různé chyby při kopírování do objektů blob bloku.

```xml
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ni3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMy3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL">\InvalidUnicodeFiles\Ã.txt</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMi3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ny3vv70=</file>
```

Tady je ukázka *error.xml* různých chyb při kopírování do souborů Azure.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_LIMIT">\AzFileMorethan1TB\AzFile1.2TB</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
```

V každém z výše uvedených případů vyřešte chyby a potom přejděte k dalšímu kroku. Další informace o chybách přijatých během kopírování dat do Data Box přes protokoly SMB nebo NFS najdete v tématu [řešení potíží s data box a data box Heavy problémy](data-box-troubleshoot.md). Informace o chybách přijatých během kopírování dat do Data Box prostřednictvím REST najdete v tématu [řešení potíží s data Boxm úložištěm objektů BLOB](data-box-troubleshoot-rest.md).

## <a name="inspect-bom-during-prepare-to-ship"></a>Kontrola kusovníku během přípravy na dodání

Během přípravy na odeslání se vytvoří seznam souborů, které jsou známé jako kusovník (BOM) nebo soubor manifestu.

- Tento soubor použijte k ověření proti skutečným názvům a počtu souborů, které byly zkopírovány do Data Box.
- Pomocí tohoto souboru můžete ověřit skutečné velikosti souborů.
- Ověřte, že *crc64* odpovídá nenulovému typu řetězce. <!--A null value for crc64 indicates that there was a reparse point error)-->

Další informace o chybách přijatých během přípravy na odeslání najdete v tématu [řešení potíží s data box a data box Heavy problémů](data-box-troubleshoot.md).

### <a name="bom-or-manifest-file"></a>Soubor kusovníku nebo manifestu

Soubor BOM nebo manifest obsahuje seznam všech souborů, které jsou zkopírovány do Data Boxho zařízení. Soubor BOM má názvy souborů a odpovídající velikosti a také kontrolní součet. Vytvoří se samostatný soubor kusovníku pro objekty blob bloku, objekty blob stránky, soubory Azure, pro kopírování přes rozhraní REST API a pro kopírování na spravované disky na Data Box. Soubory kusovníku si můžete stáhnout z místního webového uživatelského rozhraní zařízení během Příprava na odeslání.

Tyto soubory se také nacházejí v zařízení Data Box a odesílají se do přidruženého účtu úložiště v datacentru Azure.

### <a name="bom-file-format"></a>Formát souboru BOM

Soubor BOM nebo manifest má následující obecný formát:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Tady je ukázka manifestu vygenerovaného při zkopírování dat do sdílené složky objektů blob bloku v Data Box.

```
<file size="10923" crc64="0x51c78833c90e4e3f">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer1.png</file>
<file size="15308" crc64="0x091a8b2c7a3bcf0a">\databox\media\data-box-deploy-copy-data\get-share-credentials2.png</file>
<file size="53486" crc64="0x053da912fb45675f">\databox\media\data-box-deploy-copy-data\nfs-client-access.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="7749" crc64="0xd2e346a4588e307a">\databox\data-box-deploy-ordered.md</file>
<file size="14275" crc64="0x260296e5d1b1608a">\databox\data-box-deploy-copy-data.md</file>
<file size="4077" crc64="0x2bb0a170225bceec">\databox\data-box-deploy-picked-up.md</file>
<file size="15447" crc64="0xcec0ca8527720b3c">\databox\data-box-portal-admin.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
```

Soubory kusovníku nebo manifestu se také zkopírují do účtu služby Azure Storage. Soubory BOM nebo manifest můžete použít k ověření, že se soubory odeslané do Azure shodují s daty, která byla zkopírována do Data Box.

## <a name="review-copy-log-during-upload-to-azure"></a>Přečtěte si protokol kopírování během nahrávání do Azure.

Během nahrávání dat do Azure se vytvoří protokol kopírování.

### <a name="copy-log"></a>Kopírovat protokol

Pro každé zpracovávané pořadí vytvoří služba Data Box v přidruženém účtu úložiště protokol kopírování. Protokol kopírování má celkový počet souborů, které byly odeslány, a počet souborů, které byly při kopírování dat z Data Box do vašeho účtu úložiště Azure vydány chybou.

Při nahrávání do Azure se provádí výpočet s cyklicky redundantní kontrola (CRC). CRCs z kopie dat a po nahrání dat se porovnávají. Neshoda CRC znamená, že se nepovedlo nahrát odpovídající soubory.

Ve výchozím nastavení se protokoly zapisují do kontejneru s názvem `copylog` . Protokoly se ukládají s následujícími zásadami vytváření názvů:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Cesta k protokolu kopírování se taky zobrazuje v okně **Přehled** pro portál.

![Cesta ke kopírování protokolu v okně Přehled po dokončení](media/data-box-logs/copy-log-path-1.png)

### <a name="upload-completed-successfully"></a>Nahrávání se úspěšně dokončilo. 

Následující příklad popisuje obecný formát protokolu kopírování pro Data Box nahrání, které se úspěšně dokončilo:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

### <a name="upload-completed-with-errors"></a>Nahrávání se dokončilo s chybami. 

Nahrávání do Azure se může také dokončit s chybami.

![Cesta ke kopírování protokolu v okně Přehled po dokončení s chybami](media/data-box-logs/copy-log-path-2.png)

Tady je příklad protokolu kopírování, ve kterém se nahrávání dokončilo s chybami:

```xml
<ErroredEntity Path="iso\samsungssd.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="iso\iSCSI_Software_Target_33.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>72</TotalFiles_Blobs>
  <FilesErrored>2</FilesErrored>
</CopyLog>
```
### <a name="upload-completed-with-warnings"></a>Nahrávání se dokončilo s upozorněními.

Nahrávání do Azure se dokončí s upozorněními, pokud data obsahovala názvy kontejnerů, objektů BLOB nebo souborů, které nebyly v souladu se zásadami vytváření názvů Azure, a názvy se změnily, aby se nahrály do Azure

![Cesta ke kopírování protokolu v okně s přehledem po dokončení s upozorněními](media/data-box-logs/copy-log-path-3.png)

Tady je příklad protokolu kopírování, ve kterém se při nahrávání dat do Azure přejmenovaly kontejnery, které nesplňovaly zásady vytváření názvů Azure.

Nové jedinečné názvy kontejnerů jsou ve formátu `DataBox-GUID` a data pro kontejner jsou vložena do nového přejmenovaného kontejneru. Protokol kopírování určuje Starý a nový název kontejneru pro kontejner.

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

Tady je příklad protokolu kopírování, ve kterém se při nahrávání dat do Azure přejmenovaly objekty blob nebo soubory, které nesplňovaly zásady vytváření názvů Azure. Nové názvy objektů BLOB nebo souborů jsou převedeny na SHA256 výtah relativní cesty ke kontejneru a jsou nahrány do cesty na základě cílového typu. Cílem mohou být objekty blob bloku, objekty blob stránky nebo soubory Azure.

`copylog`Určuje Starý a nový objekt BLOB nebo název souboru a cestu v Azure.

```xml
<ErroredEntity Path="TesDir028b4ba9-2426-4e50-9ed1-8e89bf30d285\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: PageBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDir9856b9ab-6acb-4bc3-8717-9a898bdb1f8c\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: AzureFile/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDirf92f6ca4-3828-4338-840b-398b967d810b\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: BlockBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity>
```

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Získání řetězu protokolů pro úschov po vymazání dat

Po vymazání dat z Data Box disků podle pokynů pro NIST SP 800-88 verze 1 je k dispozici řetěz protokolů o úschově. Tyto protokoly zahrnují protokoly auditu a historii objednávek. Soubory kusovníku nebo manifestu jsou také zkopírovány pomocí protokolů auditu.

### <a name="audit-logs"></a>Protokoly auditu

Protokoly auditu obsahují informace o tom, jak zapnout a přistupovat ke sdíleným složkám na Data Box nebo Data Box Heavy, pokud se nachází mimo datové centrum Azure. Tyto protokoly jsou umístěny na adrese: `storage-account/azuredatabox-chainofcustodylogs`

Tady je ukázka protokolu auditu z Data Box:

```
9/10/2018 8:23:01 PM : The operating system started at system time ‎2018‎-‎09‎-‎10T20:23:01.497758400Z.
9/10/2018 8:23:42 PM : An account was successfully logged on.
Subject:
    Security ID:        S-1-5-18
    Account Name:       WIN-DATABOXADMIN
    Account Domain: Workgroup
    Logon ID:       0x3E7
Logon Information:
    Logon Type:     3
    Restricted Admin Mode:  -
    Virtual Account:        No
    Elevated Token:     No
Impersonation Level:        Impersonation
New Logon:
    Security ID:        S-1-5-7
    Account Name:       ANONYMOUS LOGON
    Account Domain: NT AUTHORITY
    Logon ID:       0x775D5
    Linked Logon ID:    0x0
    Network Account Name:   -
    Network Account Domain: -
    Logon GUID:     {00000000-0000-0000-0000-000000000000}
Process Information:
    Process ID:     0x4
    Process Name:       
Network Information:
    Workstation Name:   -
    Source Network Address: -
    Source Port:        -
Detailed Authentication Information:
    Logon Process:      NfsSvr
    Authentication Package:MICROSOFT_AUTHENTICATION_PACKAGE_V1_0
    Transited Services: -
    Package Name (NTLM only):   -
    Key Length:     0
This event is generated when a logon session is created. It is generated on the computer that was accessed. 
The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe. 
The logon type field indicates the kind of logon that occurred. The most common types are 2 (interactive) and 3 (network).
The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
The network fields indicate where a remote logon request originated. Workstation name is not always available and may be left blank in some cases.
The impersonation level field indicates the extent to which a process in the logon session can impersonate.
The authentication information fields provide detailed information about this specific logon request.
    - Logon GUID is a unique identifier that can be used to correlate this event with a KDC event.
    - Transited services indicate which intermediate services have participated in this logon request.
    - Package name indicates which sub-protocol was used among the NTLM protocols.
    - Key length indicates the length of the generated session key. This will be 0 if no session key was requested.
9/10/2018 8:25:58 PM : An account was successfully logged on.
```


## <a name="download-order-history"></a>Stažení historie objednávky

Historie objednávek je k dispozici v Azure Portal. Pokud je objednávka dokončená a vyčištění zařízení (data mazání z disků) je dokončené, přejděte do pořadí zařízení a přejděte na **Podrobnosti o objednávce**. Máte k dispozici možnost **Stáhnout historii objednávky**. Další informace najdete v tématu [historie pořadí stahování](data-box-portal-admin.md#download-order-history).

Pokud se posunete přes historii objednávek, uvidíte:

- Informace o sledování dopravce pro vaše zařízení.
- Události s aktivitou *SecureErase* Tyto události odpovídají mazání dat na disku.
- Data Box odkazy protokolu. Zobrazí se cesty k *protokolům auditu*, *kopírování protokolů* a souborům *kusovníků* .

Tady je ukázka protokolu historie objednávky z Azure Portal:

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-poland                              
StartTime(UTC)                              : 9/19/2018 8:49:23 AM +00:00                       
DeviceType                                     : DataBox                                           
-------------------
Data Box Activities
-------------------
Time(UTC)                 | Activity                       | Status          | Description

9/19/2018 8:49:26 AM      | OrderCreated                   | Completed       |
10/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |
10/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 10/3/2018 1:36:43 PM at AMSTERDAM-NLD                                                                                
10/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 10/4/2018 8:23:30 PM at AMSTERDAM-NLD                                                                        
10/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time : 10/4/2018 11:43:34 PM at AMSTERDAM-NLD
10/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in BRIGHTON-GBR. Local Time : 10/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
10/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 10/5/2018 9:13:24 AM at BRIGHTON-GBR                                                                               
10/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 10/5/2018 12:03:04 PM at BRIGHTON-GBR                                                                          
1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25 PM at BRIGHTON-GBR                                                                                       
1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at BRIGHTON-GBR. Local Time : 1/25/2019 8:03:55 PM at LAMBETH-GBR                                                                            
1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in BRIGHTON-GBR. Local Time : 1/25/2019 8:04:58 PM at BRIGHTON-GBR                                                                    
1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR
1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD. Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019 9:07:57 AM at AMSTERDAM-NLD                                                                             
1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019 1:35:56 PM at AMSTERDAM-NLD                                                                            
1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019 2:57:48 PM at AMSTERDAM-NLD
1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |
1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully.
1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.
2/12/2019 10:37:03 PM     | DataCopy                       | Resumed         |
2/13/2019 12:05:15 AM     | DataCopy                       | Resumed         |
2/15/2019 7:07:34 PM      | DataCopy                       | Completed       | Copy Completed.
2/15/2019 7:47:32 PM      | SecureErase                    | Started         |
2/15/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:<Device-serial-no> has been sanitized according to NIST 800-88 Rev 1.
------------------
Data Box Log Links
------------------
Account Name         : gusacct
Copy Logs Path       : databoxcopylog/gus-poland_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>Další kroky

- Naučte se [řešit problémy s data box a data box Heavy](data-box-troubleshoot.md).
