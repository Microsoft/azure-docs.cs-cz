---
title: Správa Azure Data Box, Azure Data Box Heavy přes Azure Portal | Microsoft Docs
description: Popisuje, jak použít Azure Portal ke správě Azure Data Box a Azure Data Box Heavy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: 46a18cb2b6e1682427d5674be28b240f35b120fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97678648"
---
# <a name="use-the-azure-portal-to-administer-your-azure-data-box-and-azure-data-box-heavy"></a>Pomocí Azure Portal můžete spravovat Azure Data Box a Azure Data Box Heavy

Tento článek se týká Azure Data Box i Azure Data Box Heavy. Tento článek popisuje některé komplexní pracovní postupy a úlohy správy, které je možné provádět na Azure Data Boxm zařízení. Zařízení Data Box můžete spravovat přes Azure Portal nebo prostřednictvím místního webového uživatelského rozhraní.

Tento článek se zaměřuje na úlohy, které můžete provést pomocí webu Azure Portal. Pomocí Azure Portal můžete spravovat objednávky, spravovat Data Box zařízení a sledovat stav objednávky v průběhu jejich dokončení.

## <a name="cancel-an-order"></a>Zrušení objednávky

Po umístění může být potřeba objednávku zrušit z různých důvodů.

Pro import i export objednávek můžete objednávku zrušit jenom před jejím zpracováním. Po zpracování objednávky a přípravné Data Box zařízení nemůžete tuto objednávku zrušit.

Když chcete zrušit objednávku, postupujte následovně.

1.  Přejděte na **Přehled > Zrušit**.

    ![Příkaz Cancel na kartě Přehled pro objednávku](media/data-box-portal-admin/portal-admin-cancel-command.png)

2.  Zadejte důvod zrušení objednávky.  

    ![Zrušit objednávku – dialogové okno](media/data-box-portal-admin/portal-admin-cancel-order-dbox.png)

3.  Po zrušení objednávky se na portálu aktualizuje její stav na **Zrušeno**.

## <a name="clone-an-order"></a>Klonování objednávky

V některých situacích může být užitečné klonování. Například jste použili Data Box k přenosu některých dat. Při generování dalších dat je potřeba k přenosu těchto dat do Azure použít jiné Data Box zařízení. V takovém případě můžete pouze klonovat stejné pořadí.

Provedením následujících kroků naklonujte pořadí importu.

1.  Přejděte na **Přehled > Klonovat**. 

    ![Příkaz Clone na kartě Přehled pro objednávku](media/data-box-portal-admin/portal-admin-clone-command.png)

2.  Všechny podrobnosti o objednávce zůstávají stejné. Název objednávky má podobu názvu původní objednávky s příponou *-Clone*. Zaškrtnutím políčka potvrďte, že jste si přečetli informace o ochraně osobních údajů. Klikněte na **Vytvořit**.

Během pár minut se vytvoří klon, portál se aktualizuje a zobrazí se na něm nová objednávka.


## <a name="delete-order"></a>Odstranění objednávky

Po dokončení objednávky můžete chtít objednávku odstranit. Objednávka obsahuje vaše osobní údaje jako jméno, adresu a kontaktní údaje. Při odstranění objednávky se tyto osobní údaje odstraní.

Odstranit se dají jenom dokončené nebo zrušené objednávky. Pokud chcete odstranit objednávku, proveďte následující kroky.

1. Přejděte na **Všechny prostředky**. Vyhledejte svoji objednávku.

2. Klikněte na objednávku, kterou chcete odstranit, a přejděte na **Přehled**. Na panelu příkazů klikněte na **Odstranit**.

    ![Příkaz Odstranit na kartě Přehled pro objednávku](media/data-box-portal-admin/portal-admin-delete-command.png)

3. Po zobrazení výzvy k potvrzení odstranění objednávky zadejte název objednávky. Klikněte na **Odstranit**.

## <a name="download-shipping-label"></a>Stažení expedičního štítku

Je možné, že budete muset stáhnout štítek pro expedici, pokud zobrazení Data Box E-rukou nefunguje a nezobrazí popisek pro návratovou expedici. Na Data Box Heavy není žádný displej s inkoustem, takže se tento pracovní postup nevztahuje na Data Box Heavy.

Pokud chcete stáhnout expediční štítek, proveďte následující kroky.

1.  Přejděte na **Přehled > Stáhnout expediční štítek**. Tato možnost je dostupná teprve po odeslání zařízení. 

    ![Stažení expedičního štítku](media/data-box-portal-admin/portal-admin-download-shipping-label.png)

2.  Tato akce stáhne následující zpětný expediční štítek. Uložte popisek a vytiskněte ho. Skládání a vložení popisku do prostého rukávu na zařízení. Ujistěte se, že je štítek vidět. Odstraňte všechny nálepky, které jsou na zařízení z předchozí expedice.

    ![Ukázkový expediční štítek](media/data-box-portal-admin/portal-admin-example-shipping-label.png)

## <a name="edit-shipping-address"></a>Úprava dodací adresy

Po zadání objednávky může být potřeba upravit dodací adresu. Tato možnost je dostupná jenom do odeslání zařízení. Po odeslání zařízení už tato možnost dostupná není.

Pokud chcete upravit objednávku, proveďte následující kroky.

1. Přejděte na **Podrobnosti objednávky > Upravit dodací adresu**.

    ![Upravit dodací adresu – příkaz v pořadí podrobností](media/data-box-portal-admin/portal-admin-edit-shipping-address-command.png)

2. Upravte a zkontrolujte dodací adresu a pak uložte změny.

    ![Dialogové okno Upravit dodací adresu](media/data-box-portal-admin/portal-admin-edit-shipping-address-dbox.png)

## <a name="edit-notification-details"></a>Úprava podrobností o oznámení

Možná budete muset změnit uživatele, kteří obdrží e-maily o stavu objednávky. Některý uživatel například může potřebovat informaci o doručení nebo vyzvednutí zařízení. Po dokončení kopírování dat může být potřeba informovat jiného uživatele, aby před jeho odstraněním ze zdroje mohl ověřit data v účtu služby Azure Storage. V takových případech můžete upravit podrobnosti o oznámení.

Pokud chcete upravit podrobnosti o oznámení, proveďte následující kroky.

1. Přejděte na **Podrobnosti objednávky > Upravit podrobnosti o oznámení**.

    ![Upravit podrobnosti oznámení příkazu v podrobnostech objednávky](media/data-box-portal-admin/portal-admin-edit-notification-details-command.png)

2. Teď můžete upravit podrobnosti o oznámení a uložit změny.
 
    ![Dialogové okno Upravit podrobnosti oznámení](media/data-box-portal-admin/portal-admin-edit-notification-details-dbox.png)


## <a name="download-order-history"></a>Stažení historie objednávky

Po dokončení objednávky Data Boxu se vymažou data na discích zařízení. Po vyčištění zařízení si na webu Azure Portal můžete stáhnout historii objednávky.

Pokud si chcete stáhnout historii objednávky, proveďte následující kroky.

1. Ve vaší objednávce Data Boxu přejděte do části **Přehled**. Ujistěte se, že je objednávka dokončená. Pokud je objednávka dokončená a zařízení vyčištěné, přejděte do části **Podrobnosti objednávky**. Máte k dispozici možnost **Stáhnout historii objednávky**.

    ![Stažení historie objednávky](media/data-box-portal-admin/portal-admin-download-order-history.png)

2. Klikněte na **Stáhnout historii objednávky**. Stažená historie obsahuje záznam protokolů sledování nosné frekvence. Existují dvě sady protokolů odpovídající dvěma uzlům na zařízení Data Box Heavy. Pokud se posunete do dolní části tohoto protokolu, zobrazí se následující odkazy:
    
   - **Kopírovat protokoly** – seznam souborů, které byly při kopírování dat z data box do vašeho účtu úložiště Azure (pořadí importu), nebo z vašeho účtu úložiště do data box (pořadí exportu).
   - **Protokoly auditu** – obsahují informace o tom, jak zapnout data box a přistupovat ke sdíleným složkám, když je data box mimo datové centrum Azure.
   - **Soubory kusovníku v pořadí importu** – mají seznam souborů (označovaný také jako manifest souboru), které si můžete stáhnout během **Příprava k odeslání** a mají názvy souborů, velikosti souborů a kontrolní součty souborů.
   - **Podrobné protokoly v pořadí exportu** – mají seznam souborů s názvy souborů, velikostí souborů a výpočetem kontrolního součtu, když se data zkopírují z Azure Storage účtů do data box.

   Zde je ukázka historie objednávek z pořadí importu.

    ```output
    -------------------------------
    Microsoft Data Box Order Report
    -------------------------------
    Name                                               : DataBoxTestOrder                              
    StartTime(UTC)                                     : 10/31/2018 8:49:23 AM +00:00                       
    DeviceType                                         : DataBox                                           
    -------------------
    Data Box Activities
    -------------------
    Time(UTC)                 | Activity                       | Status          | Description  
    
    10/31/2018 8:49:26 AM     | OrderCreated                   | Completed       |                                                   
    11/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |                                                   
    11/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 11/3/2018 1:36:43        PM at AMSTERDAM-NLD                                                                                
    11/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 11/4/2018        8:23:30 PM at AMSTERDAM-NLD                                                                        
    11/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time :          11/4/2018 11:43:34 PM at AMSTERDAM-NLD                                                               
    11/5/2018 1:38:20 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LEIPZIG-DEU. Local Time :        11/5/2018 1:38:20 AM at LEIPZIG-DEU                                                                
    11/5/2018 2:31:07 AM      | ShippingToCustomer             | InProgress      | Processed at LEIPZIG-DEU. Local Time : 11/5/2018          2:31:07 AM at LEIPZIG-DEU                                                                            
    11/5/2018 4:05:58 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LEIPZIG-DEU. Local Time :            11/5/2018 4:05:58 AM at LEIPZIG-DEU                                                                    
    11/5/2018 4:35:43 AM      | ShippingToCustomer             | InProgress      | Transferred through LUTON-GBR. Local Time :              11/5/2018 4:35:43 AM at LUTON-GBR                                                                         
    11/5/2018 4:52:15 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LUTON-GBR. Local Time :              11/5/2018 4:52:15 AM at LUTON-GBR                                                                        
    11/5/2018 5:47:58 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 10/5/2018 5:47:58 AM at LONDON-HEATHROW-GBR                                                
    11/5/2018 6:27:37 AM      | ShippingToCustomer             | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            11/5/2018 6:27:37 AM at LONDON-HEATHROW-GBR                                                            
    11/5/2018 6:39:40 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 11/5/2018 6:39:40 AM at LONDON-HEATHROW-GBR                                                    
    11/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in LAMBETH-GBR. Local        Time : 11/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
    11/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 11/5/2018            9:13:24 AM at LAMBETH-GBR                                                                               
    11/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 11/5/2018        12:03:04 PM at LAMBETH-GBR                                                                          
    1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25        PM at LAMBETH-GBR                                                                                       
    1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at LAMBETH-GBR. Local Time : 1/25/2019          8:03:55 PM at LAMBETH-GBR                                                                            
    1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in LAMBETH-GBR. Local Time :            1/25/2019 8:04:58 PM at LAMBETH-GBR                                                                    
    1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
    1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
    1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR                                                   
    1/26/2019 2:17:10 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility BRUSSELS-BEL. Local Time        : 1/26/2019 2:17:10 PM at BRUSSELS-BEL                                                              
    1/26/2019 2:31:57 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        2:31:57 PM at BRUSSELS-BEL                                                                          
    1/26/2019 3:37:53 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        3:37:53 PM at BRUSSELS-BEL                                                                          
    1/27/2019 11:01:45 AM     | ShippingToDataCenter           | InProgress      | Departed Facility in BRUSSELS-BEL. Local Time :          1/27/2019 11:01:45 AM at BRUSSELS-BEL                                                                 
    1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD.            Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
    1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019            9:07:57 AM at AMSTERDAM-NLD                                                                             
    1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019            1:35:56 PM at AMSTERDAM-NLD                                                                            
    1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019        2:57:48 PM at AMSTERDAM-NLD                                                                         
    1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |                                              
    1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully                  
    1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.                               
    1/29/2019 4:55:00 PM      | DataCopy                       | Started         |                                                 
    2/2/2019 7:07:34 PM       | DataCopy                       | Completed       | Copy Completed.                                   
    2/4/2019 7:47:32 PM       | SecureErase                    | Started         |                                                  
    2/4/2019 8:01:10 PM       | SecureErase                    | Completed       | Azure Data Box:DEVICESERIALNO has been sanitized          according to NIST 800-88 Rev 1.                                                                       

    ------------------
    Data Box Log Links
    ------------------

    Account Name         : Gus                                                       
    Copy Logs Path       : databoxcopylog/DataBoxTestOrder_CHC533180024_CopyLog_73a81b2d613547a28ecb7b1612fe93ca.xml
    Audit Logs Path      : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024
    BOM Files Path       : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024      
    ```

    Potom můžete přejít ke svému účtu úložiště a zobrazit protokoly kopírování.

   ![Protokoly kopírování pro účet úložiště](media/data-box-portal-admin/portal-admin-storage-account-copy-logs.png)

   Můžete si také prohlédnout řetěz protokolů pro úschovu, které zahrnují protokoly auditu a soubory KUSOVNÍKŮ.

   ![Řetěz protokolů úschovy pro účet úložiště](media/data-box-portal-admin/portal-admin-storage-account-chain-of-custody-logs.png)

## <a name="view-order-status"></a>Zobrazení stavu objednávky

Po změně stavu zařízení na portálu budete upozorněni prostřednictvím e-mailu.

### <a name="statuses-for-import-order"></a>Stavy pro pořadí importu

Zde jsou stavy pro pořadí importu.

|Stav objednávky |Description |
|---------|---------|
|Objednáno     | Došlo k úspěšnému zadání objednávky. <br>Pokud je zařízení dostupné, určí Microsoft zařízení k odeslání a toto zařízení připraví. <br> Pokud zařízení není k dispozici okamžitě, bude zpracováno, jakmile bude zařízení k dispozici. Zpracování objednávky může trvat od několika dnů až do dvou měsíců. Pokud pořadí nelze splnit do 90 dnů, objednávka se zruší a vy budete upozorněni.         |
|Zpracováno     | Proběhlo zpracování objednávky. Zařízení je připravené k odeslání v datovém centru podle objednávky.         |
|Odesláno     | Došlo k odeslání objednávky. Zásilku můžete sledovat pomocí ID pro sledování, které je uvedené ve vaší objednávce na portálu.        |
|Doručeno     | Zásilka byla doručena na adresu uvedenou v objednávce.        |
|Vyzvednuto     |Zpáteční zásilka byla vyzvednuta a naskenována dopravcem.         |
|Přijato     | Zařízení bylo přijato a naskenováno v datovém centru Azure. <br> Po kontrole zásilky začne nahrávání dat ze zařízení.      |
|Kopírování dat     | Probíhá kopírování dat. Sledujte průběh kopírování u vaší objednávky na portálu Azure Portal. <br> Počkejte, dokud se kopírování dat nedokončí. |
|Dokončeno       |Objednávka se úspěšně dokončila.<br> Než odstraníte data z místních serverů, zkontrolujte, jestli se data objevila v Azure.         |
|Dokončeno s chybami| Kopírování dat se dokončilo, ale během kopírování došlo k chybám. <br> Zkontrolujte protokoly kopírování pomocí cesty uvedené na portálu Azure Portal. Podívejte [se na příklady kopírování protokolů po dokončení nahrávání s chybami](./data-box-logs.md#upload-completed-with-errors).   |
|Dokončeno s upozorněními| Kopírování dat se dokončilo, ale data se změnila. Data obsahovala nekritické chyby objektů BLOB nebo souborů, které byly opraveny změnou názvu souboru nebo objektu BLOB. <br> Zkontrolujte protokoly kopírování pomocí cesty uvedené na portálu Azure Portal. Poznamenejte si úpravy vašich dat. Podívejte [se na příklady kopírování protokolů po dokončení nahrávání s upozorněními](./data-box-logs.md#upload-completed-with-warnings).   |
|Zrušeno            |Došlo ke zrušení objednávky. <br> Buď jste zrušili objednávku, nebo služba zrušila pořadí po výskytu chyby. Pokud pořadí nelze splnit během 90 dnů, je objednávka také zrušena a vy budete upozorněni.     |
|Vyčištění | Vymažou se data na discích zařízení. Vyčištění zařízení se považuje za dokončené, jakmile je na webu Azure Portal k dispozici ke stažení historie objednávky.|

### <a name="statuses-for-export-order"></a>Stavy pro export objednávky

Zde jsou stavy pro export objednávky.

|Stav objednávky |Description |
|---------|---------|
|Objednáno     | Objednávka exportu se úspěšně nastavila. <br>Pokud je zařízení dostupné, určí Microsoft zařízení k odeslání a toto zařízení připraví. <br> Pokud zařízení není dostupné okamžitě, bude objednávka zpracována, až bude zařízení dostupné. Zpracování objednávky může trvat od několika dnů až do dvou měsíců. Pokud objednávku nelze vyřídit do 90 dnů, je objednávka zrušena a budete upozorněni.         |
|Zrušeno            |Došlo ke zrušení objednávky. <br> Buď jste zrušili objednávku (můžete zrušit pouze před zpracováním objednávky), nebo došlo k chybě a služba zrušila pořadí. Pokud objednávku nelze vyřídit do 90 dnů, je objednávka také zrušena a budete upozorněni.     |
|Zpracováno     | Proběhlo zpracování objednávky. Podle vaší objednávky se zařízení připravuje na kopírování dat v datovém centru. Sdílené složky zařízení se vytvářejí.         |
|Probíhá kopírování dat     | Probíhá kopírování dat ze zadaných účtů Azure Storage do zařízení. Sledujte průběh kopírování u vaší objednávky na portálu Azure Portal. <br> Počkejte, dokud se kopírování dat nedokončí. |
|Kopírování dokončeno     | Kopírování dat ze zadaných účtů Azure Storage do zařízení bylo dokončeno. Podrobný soubor protokolu (Pokud byla v pořadí povolená možnost) a v účtu úložiště se vytvoří protokol kopírování. Podrobný protokol obsahuje informace o všech souborech (název, cesta, výpočetního kontrolního součtu), které se zkopírují do zařízení. Protokol kopírování obsahuje souhrn procesu kopírování, včetně seznamu souborů, které se nedaly zkopírovat kvůli chybám. <br> Data účtu úložiště zůstanou tak, jak jsou. |
|Kopírování dokončeno s chybami| Kopírování dat se dokončilo, ale během kopírování došlo k chybám. <br> Zkontrolujte protokoly kopírování v Azure Storage účtu pomocí cesty uvedené v Azure Portal. Podívejte [se na příklady kopírování protokolů po dokončení stahování s chybami](./data-box-logs.md#upload-completed-with-errors).   |
|Kopírování dokončeno s upozorněními| Kopírování dat z účtu Azure Storage bylo dokončeno, ale data obsahovala méně závažné chyby. <br> Zkontrolujte protokoly kopírování pomocí cesty uvedené na portálu Azure Portal. Poznamenejte si méně závažné chyby. Podívejte [se na příklady kopírování protokolů po dokončení stahování s upozorněními](./data-box-logs.md#upload-completed-with-warnings).   |
|Kopírování se nezdařilo s chybami| Kopírování dat z účtu Azure Storage se nezdařilo a pořadí je ukončeno. Zařízení nebude dodány. <br> Zkontrolujte protokoly kopírování v Azure Storage účtu pomocí cesty uvedené v Azure Portal. Podívejte se [na příklady kopírování protokolů po stažení se nezdařilo s chybami](./data-box-logs.md#upload-completed-with-errors).   |
|Odesláno     |Došlo k odeslání objednávky. Zásilku můžete sledovat pomocí ID pro sledování, které je uvedené ve vaší objednávce na portálu.        |
|Doručeno     |Zásilka byla doručena na adresu uvedenou v objednávce.        |
|Vyzvednuto     |Zpáteční zásilka byla vyzvednuta a naskenována dopravcem.         |
|Přijato     | Zařízení bylo přijato a naskenováno v datovém centru Azure. <br> Zásilka byla zkontrolována.      |
|Dokončeno           |Pořadí je dokončené.     |
|Vyčištění | Vymažou se data na discích zařízení. Vyčištění zařízení se považuje za dokončené, jakmile je na webu Azure Portal k dispozici ke stažení historie objednávky.|

> [!NOTE]
> Pokud je úloha kopírování k exportu dat z Azure Storage účtů do Data Box dokončena s chybami nebo upozorněními, zařízení je stále dodáváno. V případě selhání kopírování se pořadí ukončí a zařízení se nedodá.


Pokud používáte samoobslužné expedici, po dokončení kopírování a před přijetím zařízení se zobrazí následující stavy (místo těch, které jsou uvedené v předchozí tabulce):

|Stav objednávky |Description |
|---------|---------|
|Připraveno k vyzvednutí v datacentru Azure      |Zařízení je připravené k vyzvednutí v datacentru Azure.        |
|Vyzvednuto    |Zařízení jste si vybrali.         |
|Připraveno k přijetí v datacentru Azure     |Zařízení je připravené k přijetí v datacentru Azure.        |
|Přijato     |Zařízení se dostalo v datacentru Azure.      |





## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [řešit problémy s data box a data box Heavy](data-box-troubleshoot.md).