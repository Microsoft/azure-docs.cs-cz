---
title: Sledování a protokolování Azure Data Box, události Azure Data Box Heavy| Dokumenty společnosti Microsoft
description: Popisuje, jak sledovat a protokolovat události v různých fázích vaší Azure Data Box a Azure Data Box těžké pořadí.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/08/2019
ms.author: alkohli
ms.openlocfilehash: 72e1d3b0ad72b1e68b88eb0550cbe839ade9d929
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260017"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>Sledování a protokolování událostí pro azure datovou schránku a Azure Data Box Heavy

Objednávka Data Box nebo Data Box Heavy prochází následujícími kroky: pořadí, nastavení, kopírování dat, vrácení, nahrávání do Azure a ověření a vymazání dat. Odpovídající každému kroku v pořadí, můžete provést více akcí k řízení přístupu k objednávce, audit událostí, sledovat pořadí a interpretovat různé protokoly, které jsou generovány.

V následující tabulce je uveden souhrn kroků objednávky Datové schránky nebo Datové schránky A nástrojů, které jsou k dispozici pro sledování a auditování objednávky během každého kroku.

| Fáze objednávky datové schránky       | Nástroj pro sledování a audit                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Vytvoření objednávky               | [Nastavit řízení přístupu na objednávku přes RBAC](#set-up-access-control-on-the-order)                                                    |
| Objednávka zpracována            | [Sledování objednávky](#track-the-order) prostřednictvím <ul><li> portál Azure </li><li> Webové stránky dopravce </li><li>E-mailová oznámení</ul> |
| Nastavení zařízení              | Přístup k pověřením zařízení [přihlášených v protokolech aktivit](#query-activity-logs-during-setup)                                              |
| Kopírování dat do zařízení        | [Zobrazení souborů *error.xml* ](#view-error-log-during-data-copy) pro kopírování dat                                                             |
| Příprava k odeslání            | [Kontrola souborů kusovníků](#inspect-bom-during-prepare-to-ship) nebo souborů manifestu v zařízení                                      |
| Nahrávání dat do Azure       | [Kontrola protokolů kopírování](#review-copy-log-during-upload-to-azure) na chyby během nahrávání dat v datovém centru Azure                         |
| Vymazání dat ze zařízení   | [Zobrazit řetěz protokolů úschovy](#get-chain-of-custody-logs-after-data-erasure) včetně protokolů auditu a historie objednávek                |

Tento článek podrobně popisuje různé mechanismy nebo nástroje, které jsou k dispozici pro sledování a auditování data boxu nebo objednávky Data Box Heavy. Informace v tomto článku platí jak pro Datovou schránku, tak pro Datovou schránku Heavy. V následujících částech se odkazy na Datovou schránku vztahují i na Data Box Heavy.

## <a name="set-up-access-control-on-the-order"></a>Nastavení řízení přístupu na objednávce

Můžete určit, kdo má přístup k vaší objednávce při prvním vytvoření objednávky. Nastavte role řízení přístupu na základě rolí (RBAC) v různých oborech pro řízení přístupu k pořadí datových schrápte. Role RBAC určuje typ přístupu – jen pro čtení, jen pro čtení, pro čtení a zápis do podmnožiny operací.

Dvě role, které lze definovat pro službu Azure Data Box jsou:

- **Čtečka datových schronů** - mají přístup jen pro čtení k objednávkám, jak je definováno oborem. Mohou zobrazit pouze podrobnosti objednávky. Nemají přístup k žádným dalším podrobnostem týkajícím se účtů úložiště ani nemají upravovat podrobnosti objednávky, jako je adresa a tak dále.
- **Přispěvatel datové schránky** – můžete vytvořit objednávku pro přenos dat do daného účtu úložiště pouze *v případě, že již mají přístup pro zápis k účtu úložiště*. Pokud nemají přístup k účtu úložiště, nemohou ani vytvořit příkaz data boxu ke kopírování dat do účtu. Tato role nedefinuje žádná oprávnění související s účtem úložiště ani neuděluje přístup k účtům úložiště.  

Chcete-li omezit přístup k objednávce, můžete:

- Přiřazení role na úrovni objednávky Uživatel má pouze tato oprávnění definovaná rolemi pro interakci s tímto konkrétním pořadím datové schránky a nic jiného.
- Přiřaďte roli na úrovni skupiny prostředků, uživatel má přístup ke všem objednávkám datové schránky v rámci skupiny prostředků.

Další informace o navrhovaném použití RBAC naleznete v [tématu Doporučené postupy pro RBAC](../role-based-access-control/overview.md#best-practice-for-using-rbac).

## <a name="track-the-order"></a>Sledování objednávky

Svou objednávku můžete sledovat prostřednictvím portálu Azure a na webu dopravce. Pro sledování objednávky datové schránky jsou kdykoli zavedeny následující mechanismy:

- Pokud chcete sledovat pořadí, když je zařízení v datovém centru Azure nebo ve vašem areálu, přejděte na **> přehled o objednávce datové schránky** na webu Azure Portal.

    ![Zobrazit stav objednávky a sledování ne](media/data-box-logs/overview-view-status-1.png)

- Chcete-li sledovat objednávku během přenosu zařízení, přejděte na web regionálního operátora, například na web SPOLEČNOSTI UPS v USA. Zadejte sledovací číslo přidružené k objednávce.
- Data Box také odesílá e-mailová oznámení kdykoli v závislosti na změně stavu objednávky na základě e-mailů poskytnutých při vytvoření objednávky. Seznam všech stavů objednávek datových schrátek naleznete v tématu [Zobrazení stavu objednávky](data-box-portal-admin.md#view-order-status). Pokud chcete změnit nastavení oznámení přidružené k objednávce, přečtěte si informace [o úpravách podrobností o oznámení](data-box-portal-admin.md#edit-notification-details).

## <a name="query-activity-logs-during-setup"></a>Protokoly aktivit dotazu během instalace

- Vaše datová schránka dorazí do vašich prostor v uzamčeném stavu. Pro vaši objednávku můžete použít přihlašovací údaje zařízení, které jsou dostupné na webu Azure Portal.  

    Když je datová schránka nastavena, možná budete potřebovat vědět, kdo všichni přistupovali k přihlašovacím údajům zařízení. Chcete-li zjistit, kdo přistupoval k rozhraní **pověření zařízení,** můžete dotaz protokoly aktivit.  Každá akce, která zahrnuje přístup **k podrobnostem zařízení > okno Pověření** je přihlášen do protokolů aktivit jako `ListCredentials` akce.

    ![Dotazy na protokoly aktivit](media/data-box-logs/query-activity-log-1.png)

- Každé přihlášení do datové schránky je zaznamenáno v reálném čase. Tyto informace jsou však k dispozici pouze v [protokolech auditu](#audit-logs) po úspěšném dokončení objednávky.

## <a name="view-error-log-during-data-copy"></a>Zobrazit protokol chyb během kopírování dat

Během kopírování dat do datové schránky nebo datové schránky Heavy je generován soubor chyb, pokud se jedná o problémy s kopírovanými daty.

### <a name="errorxml-file"></a>Soubor Error.xml

Ujistěte se, že úlohy kopírování byly dokončeny bez chyb. Pokud během kopírování dojde k chybám, stáhněte protokoly ze stránky **Připojit a zkopírovat.**

- Pokud jste zkopírovali soubor, který není 512 bajtů zarovnaný do složky spravovaného disku v datové schránce, soubor se nenahraje jako objekt blob stránky do vašeho účtu pracovního úložiště. Zobrazí se chyba v protokolech. Odeberte soubor a zkopírujte soubor, který je zarovnaný o velikosti 512 bajtů.
- Pokud jste zkopírovali VHDX nebo dynamický virtuální pevný disk nebo differencing VHD (tyto soubory nejsou podporovány), zobrazí se chyba v protokolech.

Zde je ukázka *error.xml* pro různé chyby při kopírování na spravované disky.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Zde je ukázka *error.xml* pro různé chyby při kopírování na objekty BLOB stránky.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Zde je ukázka *error.xml* pro různé chyby při kopírování do bloku objektů BLOB.

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

Tady je ukázka *chyby.xml* pro různé chyby při kopírování do souborů Azure.

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

V každém z výše uvedených případů vyřešte chyby dříve, než přejdete k dalšímu kroku. Další informace o chybách přijatých při kopírování dat do datové schránky prostřednictvím protokolů SMB nebo NFS naleznete [v tématu Poradce při potížích s datovou schránkou a problémy s datovou schránkou .](data-box-troubleshoot.md) Informace o chybách přijatých během kopírování dat do datové schránky přes REST najdete [v tématu Poradce při potížích s úložištěm objektů blob datové schránky](data-box-troubleshoot-rest.md).

## <a name="inspect-bom-during-prepare-to-ship"></a>Zkontrolujte kusovník během přípravy na odeslání

Během přípravy na odeslání je vytvořen seznam souborů označovaných jako kusovník nebo soubor manifestu.

- Tento soubor slouží k ověření skutečných názvů a počtu souborů, které byly zkopírovány do datové schránky.
- Tento soubor slouží k ověření skutečné velikosti souborů.
- Ověřte, zda *crc64* odpovídá nenulovému řetězci. <!--A null value for crc64 indicates that there was a reparse point error)-->

Další informace o chybách, které se zobrazily během přípravy na odeslání, najdete [v tématu Poradce při potížích s datovou schránkou a problémy s datovou schránkou .](data-box-troubleshoot.md)

### <a name="bom-or-manifest-file"></a>Kusovník nebo soubor manifestu

Kusovník nebo soubor manifestu obsahuje seznam všech souborů, které jsou zkopírovány do zařízení Data Box. Soubor kusovníku má názvy souborů a odpovídající velikosti, stejně jako kontrolní součet. Samostatný soubor kusovníku se vytvoří pro objekty BLOB bloku, objekty BLOB stránky, soubory Azure, pro kopírování prostřednictvím souborů REST API a pro kopii na spravované disky v datovéschránce. Soubory kusovníku si můžete stáhnout z místního webového uživatelského rozhraní zařízení během přípravy na odeslání.

Tyto soubory jsou také umístěny na zařízení Data Box a jsou odeslány do přidruženého účtu úložiště v datovém centru Azure.

### <a name="bom-file-format"></a>Formát souboru kusovníku

Kusovník nebo soubor manifestu má následující obecný formát:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Tady je ukázka manifestu generovaného při kopírování dat do sdílené položky blob bloku v datové schránce.

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

Soubory kusovníku nebo manifestu se také zkopírují do účtu úložiště Azure. Pomocí kusovníku nebo souborů manifestu můžete ověřit, že soubory nahrané do Azure odpovídají datům, která byla zkopírována do datové schránky.

## <a name="review-copy-log-during-upload-to-azure"></a>Kontrola protokolu kopírování během nahrávání do Azure

Během odesílání dat do Azure se vytvoří protokol kopírování.

### <a name="copy-log"></a>Kopírovat protokol

Pro každou objednávku, která je zpracována, služba Data Box vytvoří protokol kopírování v přidruženém účtu úložiště. Protokol kopírování má celkový počet souborů, které byly odeslány a počet souborů, které došlo k chybě během kopírování dat z datové schránky do účtu úložiště Azure.

Cyklický redundantní kontrola (CRC) výpočtu se provádí během nahrávání do Azure. CrC z kopírování dat a po nahrání dat jsou porovnány. Neshoda CRC označuje, že odpovídající soubory se nepodařilo odeslat.

Ve výchozím nastavení jsou protokoly `copylog`zapsány do kontejneru s názvem . Protokoly jsou uloženy s následující zásadou pojmenování:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Cesta protokolu kopírování se také zobrazí v okně **Přehled** pro portál.

![Cesta ke kopírování protokolu v okně Přehled po dokončení](media/data-box-logs/copy-log-path-1.png)

### <a name="upload-completed-successfully"></a>Nahrávání bylo úspěšně dokončeno. 

Následující ukázka popisuje obecný formát protokolu kopírování pro nahrání datové schránky, který byl úspěšně dokončen:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

### <a name="upload-completed-with-errors"></a>Nahrávání dokončeno s chybami 

Nahrávání do Azure může také kompletní s chybami.

![Cesta ke kopírování protokolu v okně Přehled po dokončení s chybami](media/data-box-logs/copy-log-path-2.png)

Zde je příklad protokolu kopírování, kde nahrávání dokončeno s chybami:

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
### <a name="upload-completed-with-warnings"></a>Nahrání doplněno upozorněními

Nahrajte do Azure s upozorněními, pokud vaše data měla názvy kontejnerů/objektů blob/souborů, které neodpovídaly konvencím pojmenování Azure, a názvy byly upraveny tak, aby data nahrály do Azure.

![Cesta ke kopírování protokolu v okně Přehled po dokončení s upozorněními](media/data-box-logs/copy-log-path-3.png)

Tady je příklad protokolu kopírování, kde byly kontejnery, které neodpovídaly konvencím pojmenování Azure, přejmenovány během nahrávání dat do Azure.

Nové jedinečné názvy pro kontejnery jsou ve formátu `DataBox-GUID` a data pro kontejner jsou vloženy do nového přejmenovaného kontejneru. Protokol kopírování určuje starý a nový název kontejneru pro kontejner.

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

Tady je příklad protokolu kopírování, kde byly objekty BLOB nebo soubory, které neodpovídaly konvencím pojmenování Azure, přejmenovány během nahrávání dat do Azure. Nové názvy objektů blob nebo souborů jsou převedeny na SHA256 digest relativní cestu do kontejneru a jsou odeslány na cestu na základě cílového typu. Cílem může být objekty BLOB bloku, objekty BLOB stránky nebo soubory Azure.

Určuje `copylog` starý a nový název objektu blob nebo souboru a cestu v Azure.

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

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Získejte protokoly řetězce úschovy po vymazání dat

Po vymazání dat z disků datové schránky podle pokynů NIST SP 800-88 Revize 1 jsou k dispozici protokoly řetězce úschovy. Tyto protokoly zahrnují protokoly auditu a historii objednávek. Soubory kusovníku nebo manifestu jsou také zkopírovány protokoly auditu.

### <a name="audit-logs"></a>Protokoly auditu

Protokoly auditu obsahují informace o tom, jak zapnout sdílené složky a získat k nim přístup ke sdíleným položkám v datové schránce nebo datové schránce Těžké, když je mimo datové centrum Azure. Tyto protokoly jsou umístěny na adrese:`storage-account/azuredatabox-chainofcustodylogs`

Zde je ukázka protokolu auditu z datové schránky:

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

Historie objednávek je dostupná na webu Azure Portal. Pokud je objednávka dokončena a vyčištění zařízení (vymazání dat z disků) je dokončeno, přejděte do pořadí zařízení a přejděte na **podrobnosti objednávky**. Máte k dispozici možnost **Stáhnout historii objednávky**. Další informace naleznete v [tématu Download order history](data-box-portal-admin.md#download-order-history).

Pokud procházíte historii objednávek, zobrazí se:

- Informace o sledování operátora pro vaše zařízení.
- Události s aktivitou *SecureErase.* Tyto události odpovídají vymazání dat na disku.
- Odkazy protokolu datové schránky. Jsou uvedeny cesty pro *protokoly auditu*, *protokoly kopírování*a soubory *kusovníku.*

Tady je ukázka protokolu historie objednávek z webu Azure Portal:

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

- Přečtěte [si, jak řešit problémy s datovou schránkou a datovou schránkou Heavy](data-box-troubleshoot.md).
