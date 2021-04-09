---
title: Sledovat a protokolovat Azure Data Box Azure Data Box Heavy události pro export objednávky | Microsoft Docs
description: Popisuje, jak sledovat a protokolovat události v různých fázích Azure Data Box a Azure Data Box Heavy pořadí exportu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 3a915ac8de83a5e183660ec4a3d05044eafff4a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94337504"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy-export-orders"></a>Sledování a protokolování událostí pro Azure Data Box a Azure Data Box Heavy exportu objednávek

Data Box nebo Data Box Heavy objednávka exportu prochází následujícími kroky: pořadí, nastavení, kopírování dat, vrácení a mazání dat. V souladu s každým krokem v pořadí můžete provést několik akcí pro řízení přístupu k objednávce, Auditovat události, sledovat pořadí a interpretovat různé protokoly, které jsou vygenerovány.

Tento článek podrobně popisuje různé mechanismy a nástroje, které jsou k dispozici pro sledování a audit exportu objednávek pro Data Box nebo Data Box Heavy. Informace v tomto článku se vztahují na Data Box i Data Box Heavy. V následujících částech se všechny odkazy na Data Box vztahují také na Data Box Heavy.

Následující tabulka obsahuje souhrn kroků Data Box exportu a nástroje, které jsou k dispozici pro sledování a auditování pořadí v průběhu každého kroku.

| Data Box fáze exportu objednávky       | Nástroj pro sledování a audit                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Vytvoření objednávky               | [Nastavení řízení přístupu v objednávce prostřednictvím Azure RBAC](#set-up-access-control-on-the-order) <br> [Povolit podrobný protokol v pořadí](#enable-verbose-log-in-the-order)                                                    |
| Zpracování objednávky            | [Sledovat pořadí](#track-the-order) přes <ul><li> portál Azure </li><li> Web lodního dopravce </li><li>E-mailová oznámení</ul> |
| Nastavení zařízení              | Přístup k přihlašovacím údajům zařízení přihlášení k [protokolům aktivit](#query-activity-logs-during-setup)              |
| Kopírování dat ze zařízení        | [Zkontrolovat protokoly kopírování](#copy-log) <br> [Kontrola podrobných protokolů](#verbose-log) před kopírováním dat            |
| Data mazání ze zařízení   | [Zobrazit řetěz protokolů o úschově](#get-chain-of-custody-logs-after-data-erasure) včetně protokolů auditu a historie objednávek                |


## <a name="set-up-access-control-on-the-order"></a>Nastavení řízení přístupu na objednávce

Můžete určit, kdo má mít přístup k vaší objednávce při prvním vytvoření objednávky. Nastavte role Azure v různých oborech, abyste mohli řídit přístup k pořadí Data Box. Role Azure určuje typ přístupu – čtení i zápis, čtení i zápis v podmnožině operací.

Pro službu Azure Data Box lze definovat dvě role:

- **Data box Reader** – mají přístup jen pro čtení k objednávkám definovaným oborem. Mohou pouze zobrazit podrobnosti o objednávce. Nemůžou přistupovat k žádným jiným podrobnostem souvisejícím s účty úložiště ani upravovat podrobnosti objednávky, jako je například adresa a tak dále.
- **Přispěvatel data box** – může vytvořit objednávku pro přenos dat do daného účtu úložiště jenom v *případě, že už mají oprávnění k zápisu do účtu úložiště*. Pokud nemají přístup k účtu úložiště, nemůžou ani vytvořit Data Box objednávku ke zkopírování dat do účtu. Tato role nedefinuje žádné související oprávnění účtu úložiště ani neuděluje přístup k účtům úložiště.  

Chcete-li omezit přístup k objednávce, můžete:

- Přiřaďte roli na úrovni objednávky. Uživatel má pouze ta oprávnění definovaná rolemi k interakci s tímto konkrétním Data Box objednávka a nic jiného.
- Přiřaďte roli na úrovni skupiny prostředků, uživatel má přístup ke všem Data Box objednávkám v rámci skupiny prostředků.

Další informace o navrhovaných použitích služby Azure RBAC najdete v tématu [osvědčené postupy pro službu Azure RBAC](../role-based-access-control/best-practices.md).

## <a name="enable-verbose-log-in-the-order"></a>Povolit podrobný protokol v pořadí

Při umísťování pořadí exportu pro Data Box máte možnost Povolit kolekci podrobného protokolu. Tady je obrazovka s objednávkou, kde můžete povolit podrobný protokol:

![Vybrat možnost exportu](media/data-box-deploy-export-ordered/azure-data-box-export-order-export-option.png)

Když vyberete možnost **zahrnout podrobný protokol** , při kopírování dat z účtu Azure Storage se vygeneruje podrobný soubor protokolu. Tento protokol obsahuje seznam všech souborů, které byly úspěšně exportovány.

Další informace o pořadí exportu najdete v tématu [vytvoření objednávky exportu pro data box](data-box-deploy-export-ordered.md)

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

- Každé přihlášení k Data Box je zaznamenáno v reálném čase. Tyto informace jsou však k dispozici pouze v [řetězu protokolů auditu](#chain-of-custody-audit-logs) po úspěšném dokončení objednávky.

## <a name="view-logs-during-data-copy"></a>Zobrazit protokoly během kopírování dat

Před kopírováním dat z Data Box můžete stáhnout a zkontrolovat *protokol kopírování* a *podrobný protokol* pro data, která byla zkopírována do data box. Tyto protokoly se generují při kopírování dat z vašeho účtu úložiště v Azure do svého Data Box. 

### <a name="copy-log"></a>Kopírovat protokol

Před kopírováním dat z Data Box Stáhněte ze stránky **připojit a kopírovat** protokol kopírování.

Tady je ukázkový výstup *protokolu kopírování* , pokud nedošlo k chybám a všechny soubory se zkopírovaly během kopírování dat z Azure do zařízení data box.

```output
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
  <TotalFiles_Blobs>5521</TotalFiles_Blobs>
  <FilesErrored>0</FilesErrored>
</CopyLog>
``` 
    
Tady je ukázkový výstup, když *protokol kopírování* obsahuje chyby a některé soubory se nepovedlo zkopírovat z Azure.

```output
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>    
```

Pro export těchto souborů máte následující možnosti: 

- Soubory, které se nedaly zkopírovat přes síť, můžete přenést. 
- Pokud velikost dat přesahuje použitelnou kapacitu zařízení, dojde k částečnému kopírování a všechny soubory, které se nezkopírují, jsou uvedené v tomto protokolu. Tento protokol můžete použít jako vstupní XML k vytvoření nové objednávky Data Boxu a potom tyto soubory zkopírovat.

### <a name="verbose-log"></a>Podrobný protokol

*Podrobný protokol* obsahuje seznam všech souborů úspěšně vyexportovaných z účtu Azure Storage. Tento protokol obsahuje také velikosti souborů a výpočty kontrolního součtu.

Podrobný protokol má informace v následujícím formátu:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Tady je ukázkový výstup podrobného protokolu. 

```powershell
  <File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820">
  </File>
  ``````

Podrobné protokoly se také zkopírují do účtu služby Azure Storage. Ve výchozím nastavení se protokoly zapisují do kontejneru s názvem `copylog` . Protokoly se ukládají s následujícími zásadami vytváření názvů:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Cesta k protokolu kopírování se taky zobrazuje v okně **Přehled** pro portál.

<!-- add a screenshot-->

Pomocí těchto protokolů můžete ověřit, jestli se soubory zkopírované z Azure shodují s daty zkopírovanými na váš místní server. 

Použijte soubor podrobného protokolu:

- Pro ověření proti skutečným názvům a počtu souborů, které byly zkopírovány z Data Box.
- Ověření proti skutečným velikostem souborů.
- Chcete-li ověřit, zda *crc64* odpovídá nenulovému typu řetězce. Při exportu z Azure se provádí výpočet s cyklicky redundantní kontrola (CRC). CRCs z exportu a po zkopírování dat z Data Box na místní server lze porovnat. Neshoda kontrolního součtu (CRC) znamená, že odpovídající soubory se nepodařilo správně zkopírovat.


## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Získání řetězu protokolů pro úschov po vymazání dat

Po vymazání dat z Data Box disků podle pokynů pro NIST SP 800-88 verze 1 je k dispozici řetěz protokolů o úschově. Tyto protokoly zahrnují řetězec protokolů auditu na úschovu a historii objednávek. Soubory kusovníku nebo manifestu jsou také zkopírovány pomocí protokolů auditu.

### <a name="chain-of-custody-audit-logs"></a>Řetěz protokolů auditu pro úschovu

Řetězec protokolů auditu v rámci úschovy obsahuje informace o zapnutí a přístupu ke sdíleným složkám na Data Box nebo Data Box Heavy, pokud se nachází mimo datové centrum Azure. Tyto protokoly jsou umístěny na adrese: `storage-account/azuredatabox-chainofcustodylogs`

Tady je ukázka protokolu auditu z Data Box:

```output
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
