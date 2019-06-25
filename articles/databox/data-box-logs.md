---
title: Sledování a protokolů Azure Data Box, Azure Data Box náročné události | Dokumentace Microsoftu
description: Popisuje, jak sledovat a protokolovat události v různých fázích vaše objednávka zařízení Azure Data Box a Azure Data Box náročné.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 108d17d3e0ca5f32648f9d4f6cf4b5f9a2984d0c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66495808"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>Sledování a protokolování událostí pro Azure Data Box a Azure Data Box Heavy

Objednávky zařízení Data Box nebo Data Box náročné prochází následující kroky: order, nastavit, data kopírovat, vraťte, nahrajte do Azure a ověřit a výmaz dat. Odpovídající každý krok v pořadí, můžete proveďte více akcí pro řízení přístupu k pořadí, událostech auditování, sledování pořadí a interpretovat různé protokoly, které jsou generovány.

Následující tabulka uvádí přehled zařízení Data Box nebo Data Box náročné pořadí kroků a dostupnost nástrojů pro sledování a auditování pořadí během jednotlivých kroků.

| Data Box pořadí fází       | Nástroj ke sledování a auditování                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Vytvořit objednávku               | [Nastavení řízení přístupu na příkazu via RBAC](#set-up-access-control-on-the-order)                                                    |
| Pořadí zpracování            | [Sledování pořadí](#track-the-order) prostřednictvím <ul><li> portál Azure </li><li> Přesouvání dopravce webu </li><li>E-mailová oznámení</ul> |
| Nastavení zařízení              | Přístup k přihlášení pověření zařízení [protokolů aktivit](#query-activity-logs-during-setup)                                              |
| Kopírování dat do zařízení        | [Zobrazení *error.xml* soubory](#view-error-log-during-data-copy) pro kopírování dat                                                             |
| Příprava k odeslání            | [Kontrola souborů BOM](#inspect-bom-during-prepare-to-ship) nebo soubory manifestu v zařízení                                      |
| Nahrání dat do Azure       | [Kontrola *copylogs* ](#review-copy-log-during-upload-to-azure) chyby během data nahrát na datové centrum Azure                         |
| Vymazání dat ze zařízení   | [Zobrazit řetězce sledování postupného předávání protokolů](#get-chain-of-custody-logs-after-data-erasure) včetně protokoly auditu a uspořádat historii                                                   |

Tento článek podrobně popisuje, různé mechanismy nebo nástroje ke sledování a auditování objednávky zařízení Data Box nebo Data Box náročné. Informace v tomto článku platí pro zařízení Data Box i Data Box náročné. V dalších částech všechny odkazy na zařízení Data Box platí také pro Data Box náročné.

## <a name="set-up-access-control-on-the-order"></a>Nastavení řízení přístupu na pořadí

Můžete řídit, kdo má přístup k vaší objednávky při prvním vytvoření pořadí. Nastavení řízení přístupu na základě rolí (RBAC) rolí na různé obory pro řízení přístupu k objednávka zařízení Data Box. Roli RBAC Určuje typ přístupu – pro čtení i zápis, jen pro čtení, čtení a zápis na podmnožinu operace.

Tyto dvě role, které lze definovat pro službu Azure Data Box jsou:

- **Čtecí modul dat pole** -přístup jen pro čtení pro objednávky podle oboru. Může zobrazit jenom podrobnosti objednávky. Nemohou přistupovat k další podrobnosti související s účty úložiště nebo upravit podrobnosti objednávky, jako je například adresa a tak dále.
- **Přispěvatel dat pole** – může pouze vytvořit objednávku posílat data do účtu úložiště pro danou *Pokud už mají přístup pro zápis do účtu úložiště*. Pokud nemají přístup k účtu úložiště, nelze dokonce vytvořit objednávka zařízení Data Box ke zkopírování dat do účtu. Tato role nedefinuje žádné účet úložiště související oprávnění ani uděluje přístup k účtům úložiště.  

Chcete-li omezit přístup k objednávky, můžete:

- Přiřazení role na úrovni pořadí. Tato oprávnění má uživatel jenom podle definice role pro interakci s této konkrétní objednávka zařízení Data Box pouze a nic jiného.
- Přiřazení role na úrovni skupiny prostředků, má uživatel přístup pro všechny objednávky zařízení Data Box v rámci skupiny prostředků.

Další informace o použití navrhované RBAC najdete v tématu [osvědčené postupy pro RBAC](../role-based-access-control/overview.md#best-practice-for-using-rbac).

## <a name="track-the-order"></a>Sledování objednávky

Můžete sledovat vaši objednávku na webu Azure portal a na webových stránkách přenosů operátora. Těchto mechanismů jsou na místě můžete kdykoli sledovat objednávka zařízení Data Box:

- Ke sledování pořadí, když je zařízení v datovém centru Azure nebo místním, přejděte k vaší **objednávka zařízení Data Box > Přehled** na webu Azure portal.

    ![Zobrazit stav objednávky a ne pro sledování](media/data-box-logs/overview-view-status-1.png)

- Ke sledování pořadí, když je zařízení během přenosu, přejdete na web místní operátora, například UPS webu v oblasti USA. Zadejte číslo pro sledování přidružené k vaší objednávky.
- Zařízení data Box rovněž odesílá e-mailová oznámení, kdykoli se stav změní pořadí podle e-mailů zadaná při vytvoření pořadí. Seznam všech stavů objednávky zařízení Data Box najdete v tématu [zobrazit stav objednávky](data-box-portal-admin.md#view-order-status). Chcete-li změnit nastavení oznámení související s pořadím [upravit podrobnosti o oznámení](data-box-portal-admin.md#edit-notification-details).

## <a name="query-activity-logs-during-setup"></a>Protokoly aktivit dotazu během instalace

- Data Box doručení ve vlastních prostorách v uzamčeném stavu. Přihlašovací údaje zařízení, která je k dispozici na webu Azure Portal můžete použít pro vaši objednávku.  

    Pokud zařízení Data Box je nastavena, budete muset vědět, kdo všechny přihlašovací údaje zařízení získat přístup. Chcete-li zjistit, kdo měl přístup k **přihlašovací údaje zařízení** okně dotazujete protokoly aktivit.  Všechny akce, která zahrnuje přístup k **podrobnosti o zařízení > přihlašovací údaje** přihlášený okno protokoly aktivit jako `ListCredentials` akce.

    ![Dotazy na protokoly aktivit](media/data-box-logs/query-activity-log-1.png)

- Každý znak do zařízení Data Box je zaznamenané reálném čase. Ale tyto informace je k dispozici pouze [protokoly auditu](#audit-logs) po úspěšném dokončení pořadí.

## <a name="view-error-log-during-data-copy"></a>Zobrazit v protokolu chyb při kopírování dat.

Při kopírování dat do zařízení Data Box nebo náročné pole dat je generována chybový soubor pokud nastanou problémy se data kopírují.

### <a name="errorxml-file"></a>Soubor Error.XML

Ujistěte se, že kopie dokončení bez chyb. Pokud nejsou chyby během kopírování, stáhněte si protokoly z **připojit a Kopírovat** stránky.

- Pokud jste zkopírovali soubor, který ne 512 bajtů zarovnána do složky spravovaného disku na vaše zařízení Data Box, není soubor nahrát jako objekt blob stránky do přípravného účtu úložiště. Zobrazí se chybu v protokolech. Odeberte tento soubor a zkopírujte soubor, který je 512 bajtů zarovnána.
- Pokud jste si zkopírovali disk VHDX, nebo dynamický virtuální pevný disk nebo rozdílový virtuální pevný disk (tyto soubory nejsou podporovány), zobrazí se chybu v protokolech.

Tady je ukázka *error.xml* různých chyb při kopírování do spravované disky.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Tady je ukázka *error.xml* různých chyb při kopírování pro objekty BLOB stránky.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Tady je ukázka *error.xml* různých chyb při kopírování pro objekty BLOB bloku.

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

Tady je ukázka *error.xml* různých chyb při kopírování do služby soubory Azure.

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

V každé z výše uvedených případech vyřešte chyby, než budete pokračovat k dalšímu kroku. Další informace o chyb oznámených při kopírování dat do zařízení Data Box přes protokoly SMB a systému souborů NFS, přejděte na [řešení potíží s poli Data a Data Box náročné problémy](data-box-troubleshoot.md). Informace o chyb oznámených při kopírování dat do zařízení Data Box přes REST, přejděte na [problémů s úložištěm objektů Blob řešení potíží s poli Data](data-box-troubleshoot-rest.md).

## <a name="inspect-bom-during-prepare-to-ship"></a>Kontrola BOM během přípravu k odeslání

Během přípravu k odeslání, seznam souborů, které jsou známé jako vyúčtování kusovník (BOM) nebo soubor manifestu vytvořen.

- Tento soubor můžete použijte k ověření proti skutečné názvy a počet souborů, které byly zkopírovány do zařízení Data Box.
- Tento soubor můžete použijte k ověření proti skutečné velikosti souborů.
- Ověřte, že *crc64* odpovídá řetězec nenulové. <!--A null value for crc64 indicates that there was a reparse point error)-->

Další informace o chyb oznámených při přípravě k odeslání, přejděte na [řešení potíží s poli Data a Data Box náročné problémy](data-box-troubleshoot.md).

### <a name="bom-or-manifest-file"></a>Soubor manifestu nebo BOM

BOM nebo soubor manifestu obsahuje seznam všech souborů, které se zkopírují do zařízení Data Box. Soubor Kusovník obsahuje názvy souborů a odpovídající velikostí, jakož i kontrolní součet. Pro objekty BLOB bloku, objekty BLOB stránky Azure Files a pro kopírování prostřednictvím rozhraní REST API a pro kopírování na spravované disky na zařízení Data Box se vytvoří samostatný soubor BOM. BOM soubory si můžete stáhnout z místního webového uživatelského rozhraní zařízení během Příprava k odeslání.

Tyto soubory také nacházet v zařízení Data Box a jsou odeslány do přidruženého účtu úložiště v datovém centru Azure.

### <a name="bom-file-format"></a>Formát souboru BOM

Soubor Kusovník nebo manifestu má následující obecné formát:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Tady je ukázka manifestu vygeneruje, když data byl zkopírován do sdílené složky objektů blob bloku v zařízení Data Box.

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

BOM nebo soubory manifestu jsou zkopírovány také do účtu úložiště Azure. Můžete použít BOM nebo manifest souborů a ověřit, že soubory nahrané do Azure odpovídají data, která byla zkopírována do zařízení Data Box.

## <a name="review-copy-log-during-upload-to-azure"></a>Prohlédněte si protokol kopírování během odesílání do Azure

Při nahrávání dat do Azure *copylog* se vytvoří.

### <a name="copylog"></a>Copylog

Pro každé objednávky, které se zpracovává, služba Data Box vytvoří *copylog* do přidruženého účtu úložiště. *Copylog* má celkový počet souborů, které byly odeslány a počet souborů, který došlo k chybě během data kopírovat z zařízení Data Box ke svému účtu Azure storage.

Cyklické zkontrolujte redundance (CRC) výpočet se provádí při nahrávání do Azure. CRC kopírování dat a po nahrání dat porovnávány. Neshoda CRC označuje, že odpovídající soubory se nepodařilo nahrát.

Ve výchozím nastavení se protokoly zapisují na kontejner s názvem copylog. Protokoly se ukládají s následujícími zásadami vytváření názvů:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Cesta copylog se zobrazuje taky na **přehled** okně portálu.

![Cesta k copylog v okně Přehled po dokončení](media/data-box-logs/copy-log-path-1.png)

Následující příklad popisuje obecný formát souboru copylog pro nahrávání zařízení Data Box, která byla úspěšně dokončena:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

Nahrát do Azure mohou také provádět s chybami.

![Cesta k copylog v okně přehledu, když byla dokončena s chybami](media/data-box-logs/copy-log-path-2.png)

Tady je příklad copylog kde nahrávání dokončeno s chybami:

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


## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Získá řetězec sledování postupného předávání protokolů za výmaz dat

Po data se vymažou z disků Data Box podle pokynů SP NIST 800-88 revizi 1, jsou k dispozici řetězu sledování postupného předávání protokolů. Tyto protokoly obsahovat protokoly auditu a historie objednávek. BOM nebo soubory manifestu jsou zkopírovány také pomocí protokolů auditu.

### <a name="audit-logs"></a>Protokoly auditu

Protokoly auditu obsahují informace o zapnutí a sdílet přístup k zařízení Data Box nebo velkým pole Data, když je mimo datové centrum Azure. Tyto protokoly jsou umístěné na: `storage-account/azuredatabox-chainofcustodylogs`

Tady je ukázka protokolu auditu ze zařízení Data Box:

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

Historie objednávek je k dispozici na webu Azure portal. Pokud pořadí je kompletní a dokončení vyčištění zařízení (výmaz dat z disků), přejděte na vaše objednávka zařízení a přejděte do **podrobnosti objednávky**. ** Stažení historie objednávek** možnost je k dispozici. Další informace najdete v tématu [stažení historie objednávek](data-box-portal-admin.md#download-order-history).

Pokud při procházení historie objednávek se zobrazí:

- Poskytovatel služeb sledování informací pro vaše zařízení.
- Události se *SecureErase* aktivity. Tyto události odpovídají mazání dat na disku.
- Propojení dat v poli protokolu. Cesty pro *protokoly auditu*, *copylogs*, a *BOM* soubory jsou uvedeny.

Tady je příklad historie protokolu pořadí z webu Azure portal:

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

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [problémů na zařízení Data Box a Data Box náročné](data-box-troubleshoot.md).
