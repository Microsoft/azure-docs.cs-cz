---
title: Příručka pro správu Azure Data Boxu pomocí portálu | Microsoft Docs
description: Popisuje, jak spravovat Azure Data Box pomocí webu Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 10/19/2018
ms.author: alkohli
ms.openlocfilehash: 1b228a66f2d59b3ff252df266783f7bd5d27139e
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49645435"
---
# <a name="use-the-azure-portal-to-administer-your-data-box"></a>Správa Data Boxu pomocí portálu Azure Portal

Tento článek popisuje některé komplexní pracovní postupy a úlohy správy, které se dají provádět u Data Boxu. Data Box můžete spravovat přes Azure Portal nebo v místním webovém uživatelském rozhraní. 

Tento článek se zaměřuje na úlohy, které můžete provést pomocí webu Azure Portal. Azure Portal můžete použít ke správě objednávek a Data Boxu a ke sledování stavu objednávek až do jejich dokončení.


## <a name="cancel-an-order"></a>Zrušení objednávky

Může se stát, že po zadání objednávky budete muset tuto objednávku z různých důvodů zrušit. Objednávku je možné zrušit jen do doby, než je zpracována. Jakmile je objednávka zpracovaná a je připravený Data Box, není už možné objednávku zrušit. 

Když chcete zrušit objednávku, postupujte následovně.

1.  Přejděte na **Přehled > Zrušit**. 

    ![Zrušení objednávky 1](media/data-box-portal-admin/cancel-order1.png)

2.  Zadejte důvod zrušení objednávky.  

    ![Zrušení objednávky 2](media/data-box-portal-admin/cancel-order2.png)

3.  Po zrušení objednávky se na portálu aktualizuje její stav na **Zrušeno**. 

## <a name="clone-an-order"></a>Klonování objednávky

V některých situacích může být užitečné klonování. Uživatel například používá Data Box k přenosu dat. Jak se postupně generují další data, může být potřeba další Data Box k přenosu těchto dat do Azure. V takovém případě se dá stejná objednávka jednoduše naklonovat.

Pokud chcete naklonovat objednávku, proveďte následující kroky.

1.  Přejděte na **Přehled > Klonovat**. 

    ![Klonování objednávky 1](media/data-box-portal-admin/clone-order1.png)

2.  Všechny podrobnosti o objednávce zůstávají stejné. Název objednávky má podobu názvu původní objednávky s příponou *-Clone*. Zaškrtnutím políčka potvrďte, že jste si přečetli informace o ochraně osobních údajů. Klikněte na možnost **Vytvořit**.

Během pár minut se vytvoří klon, portál se aktualizuje a zobrazí se na něm nová objednávka.


## <a name="delete-order"></a>Odstranění objednávky

Po dokončení objednávky můžete chtít objednávku odstranit. Objednávka obsahuje vaše osobní údaje jako jméno, adresu a kontaktní údaje. Při odstranění objednávky se tyto osobní údaje odstraní.

Odstranit se dají jenom dokončené nebo zrušené objednávky. Pokud chcete odstranit objednávku, proveďte následující kroky.

1. Přejděte na **Všechny prostředky**. Vyhledejte svoji objednávku.

2. Klikněte na objednávku, kterou chcete odstranit, a přejděte na **Přehled**. Na panelu příkazů klikněte na **Odstranit**.

    ![Odstranění první objednávky Data Boxu](media/data-box-portal-admin/delete-order1.png)

3. Po zobrazení výzvy k potvrzení odstranění objednávky zadejte název objednávky. Klikněte na **Odstranit**.

## <a name="download-shipping-label"></a>Stažení expedičního štítku

Pokud displej E-ink vašeho Data Boxu nefunguje a nezobrazuje zpětný expediční štítek, budete možná muset expediční štítek stáhnout. 

Pokud chcete stáhnout expediční štítek, proveďte následující kroky.

1.  Přejděte na **Přehled > Stáhnout expediční štítek**. Tato možnost je dostupná teprve po odeslání zařízení. 

    ![Stažení expedičního štítku](media/data-box-portal-admin/download-shipping-label.png)

2.  Tato akce stáhne následující zpětný expediční štítek. Štítek uložte a vytiskněte ho. Štítek přeložte a vložte do průhledného obalu na zařízení. Ujistěte se, že je štítek vidět. Odstraňte všechny nálepky, které jsou na zařízení z předchozí expedice.

    ![Ukázkový expediční štítek](media/data-box-portal-admin/example-shipping-label.png)

## <a name="edit-shipping-address"></a>Úprava dodací adresy

Po zadání objednávky může být potřeba upravit dodací adresu. Tato možnost je dostupná jenom do odeslání zařízení. Po odeslání zařízení už tato možnost dostupná není.

Pokud chcete upravit objednávku, proveďte následující kroky.

1. Přejděte na **Podrobnosti objednávky > Upravit dodací adresu**.

    ![Úprava dodací adresy 1](media/data-box-portal-admin/edit-shipping-address1.png)

2. Upravte a zkontrolujte dodací adresu a pak uložte změny.

    ![Úprava dodací adresy 2](media/data-box-portal-admin/edit-shipping-address2.png)

## <a name="edit-notification-details"></a>Úprava podrobností o oznámení

Může být potřeba změnit uživatele, kteří mají dostávat e-maily se stavem objednávky. Některý uživatel například může potřebovat informaci o doručení nebo vyzvednutí zařízení. Jiný uživatel zase může potřebovat oznámení o dokončení kopírování dat, aby mohl zkontrolovat, jestli se data nacházejí na účtu služby Azure Storage, a potom je odstranit ze zdroje. V takových případech můžete upravit podrobnosti o oznámení.

Pokud chcete upravit podrobnosti o oznámení, proveďte následující kroky.

1. Přejděte na **Podrobnosti objednávky > Upravit podrobnosti o oznámení**.

    ![Úprava podrobností o oznámení 1](media/data-box-portal-admin/edit-notification-details1.png)

2. Teď můžete upravit podrobnosti o oznámení a uložit změny.
 
    ![Úprava podrobností o oznámení 2](media/data-box-portal-admin/edit-notification-details2.png)


## <a name="download-order-history"></a>Stažení historie objednávky

Po dokončení objednávky Data Boxu se vymažou data na discích zařízení. Po vyčištění zařízení si na webu Azure Portal můžete stáhnout historii objednávky.

Pokud si chcete stáhnout historii objednávky, proveďte následující kroky.

1. Ve vaší objednávce Data Boxu přejděte do části **Přehled**. Ujistěte se, že je objednávka dokončená. Pokud je objednávka dokončená a zařízení vyčištěné, přejděte do části **Podrobnosti objednávky**. Máte k dispozici možnost **Stáhnout historii objednávky**.

    ![Stažení historie objednávky](media/data-box-portal-admin/download-order-history-1.png)

2. Klikněte na **Stáhnout historii objednávky**. Ve stažené historii uvidíte záznam protokolů dopravců o sledování zásilky. Pokud se posunete do dolní části tohoto protokolu, zobrazí se následující odkazy:
    
    - **Copy logs** (Protokoly kopírování) – obsahují seznam souborů, u kterých během kopírování dat z Data Boxu do vašeho účtu úložiště Azure došlo k chybě.
    - **Audit logs** (Protokoly auditu) – obsahují informace o zapnutí a sdílení přístupu k Data Boxu, když je mimo datacentrum Azure.
    - **BOM files** (Soubory BOM) – obsahují seznam souborů (označovaný také jako soubor manifestu), které si můžete stáhnout během **přípravy k odeslání**, a zahrnují názvy, velikosti a kontrolní součty souborů.

        ```
        -------------------------------
        Microsoft Data Box Order Report
        -------------------------------
        
        Name                                               : eastusdryrun                                      
        StartTime(UTC)                                     : 9/6/2018 12:54:47 PM +00:00                       
        DeviceType                                         : ImolaPod                                          
        
        -------------------
        Data Box Activities
        -------------------
        
        Time(UTC)             | Activity                       | Status          | Description                                                                                                                                           
        
        9/6/2018 12:54:51 PM  | OrderCreated         | Completed  |                                                                                                                              
        9/11/2018 8:57:38 PM  | DevicePrepared       | Completed  |                                                                                                                                                       
        9/12/2018 7:28:15 PM  | ShippingToCustomer   | InProgress | Pickup Scan. Local Time : 9/12/2018 2:52:31 PM at Chantilly                                                                                           
        9/13/2018 2:33:04 AM  | ShippingToCustomer   | InProgress | Departure Scan. Local Time : 9/12/2018 9:00:00 PM at Chantilly                                                                                                                                                                                                                                                              
        9/13/2018 12:40:31 PM | ShippingToCustomer   | InProgress | Arrival Scan. Local Time : 9/13/2018 5:00:00 AM at Oakland                                                                                            
        9/13/2018 2:42:10 PM  | ShippingToCustomer   | InProgress | Departure Scan. Local Time : 9/13/2018 6:08:00 AM at Oakland                                                                                          
        9/13/2018 3:42:12 PM  | ShippingToCustomer   | InProgress | Destination Scan. Local Time : 9/13/2018 8:14:08 AM at Sunnyvale                                                                                      
        9/13/2018 4:43:05 PM  | ShippingToCustomer   | InProgress | Destination Scan. Local Time : 9/13/2018 8:56:54 AM at Sunnyvale                                                                                      
        9/13/2018 4:43:05 PM  | ShippingToCustomer   | InProgress | Out For Delivery Today. Local Time : 9/13/2018 9:11:21 AM at Sunnyvale                                                                                
        9/13/2018 5:43:07 PM  | ShippingToCustomer   | Completed  | Delivered. Local Time : 9/13/2018 9:44:17 AM at SUNNYVALE                                                                                             
        9/14/2018 11:48:35 PM | ShippingToDataCenter | InProgress | Pickup Scan. Local Time : 9/14/2018 3:55:37 PM at Sunnyvale                                                                                                                                                                                 
        9/15/2018 1:52:35 AM  | ShippingToDataCenter | InProgress | Arrival Scan. Local Time : 9/14/2018 6:31:00 PM at San Jose                                                                                           
        9/15/2018 2:52:39 AM  | ShippingToDataCenter | InProgress | Departure Scan. Local Time : 9/14/2018 7:17:00 PM at San Jose                                                                                                                                                                             
        9/17/2018 8:23:31 AM  | ShippingToDataCenter | InProgress | Destination Scan. Local Time : 9/17/2018 4:14:37 AM at Chantilly                                                                                      
        9/17/2018 12:24:42 PM | ShippingToDataCenter | InProgress | Loaded on Delivery Vehicle. Local Time : 9/17/2018 7:45:36 AM at Chantilly                                                                            
        9/17/2018 1:25:11 PM  | ShippingToDataCenter | InProgress | Out For Delivery Today. Local Time : 9/17/2018 8:27:11 AM at Chantilly                                                                                
        9/17/2018 2:25:51 PM  | ShippingToDataCenter | Completed | Delivered. Local Time : 9/17/2018 9:56:32 AM at STERLING                                                                                              
        9/18/2018 9:55:41 PM  | DeviceBoot           | Completed | Appliance booted up successfully                                                                                                                      
        9/18/2018 11:00:25 PM | DataCopy             | Started   |                                                                                                                                                       
        9/18/2018 11:01:33 PM | DataCopy             | Completed | Copy Completed.                                                                                                                                       
        9/18/2018 11:20:58 PM | SecureErase          | Started   |                                                                                                                                                       
        9/18/2018 11:28:46 PM | SecureErase          | Completed | Azure Data Box:BY506B4B616700 has been sanitized according to NIST 800 -88 Rev 1.                                                                     
        
        ----------------------
        Data Box Job Log Links
        ----------------------
        
        Account Name         : eastusdryrun                                         
        Copy Logs Path       : copylog/copylogd695869a2a294396b7b903296c208388.xml                                                                                                                                                     
        Audit Logs Path      : azuredatabox-chainofcustodylogs\3b4cf163-f1af-475c-a391-f8afea3fa327\by506b4b616700                                                                                                                     
        BOM Files Path       : azuredatabox-chainofcustodylogs\3b4cf163-f1af-475c-a391-f8afea3fa327\by506b4b616700
        ```
Potom můžete přejít ke svému účtu úložiště a zobrazit protokoly kopírování.

![Protokoly v účtech úložiště](media/data-box-portal-admin/logs-in-storage-acct-2.png)

Můžete také zobrazit řetězec protokolů opatrovnictví, mezi které patří protokoly auditu a soubory BOM.

![Protokoly v účtech úložiště](media/data-box-portal-admin/logs-in-storage-acct-1.png)

## <a name="view-order-status"></a>Zobrazení stavu objednávky

Když se stav zařízení na portálu změní, dostanete upozornění prostřednictvím e-mailu.

|Stav objednávky |Popis |
|---------|---------|
|Objednáno     | Došlo k úspěšnému zadání objednávky. <br>Pokud je zařízení dostupné, určí Microsoft zařízení k odeslání a toto zařízení připraví. <br> Pokud zařízení není dostupné okamžitě, bude objednávka zpracována, až bude zařízení dostupné. Zpracování objednávky může trvat od několika dnů až do dvou měsíců. Pokud objednávku nelze vyřídit do 90 dnů, je objednávka zrušena a budete upozorněni.         |
|Zpracováno     | Proběhlo zpracování objednávky. Zařízení je připravené k odeslání v datovém centru podle objednávky.         |
|Odesláno     | Došlo k odeslání objednávky. Zásilku můžete sledovat pomocí ID pro sledování, které je uvedené ve vaší objednávce na portálu.        |
|Doručeno     | Zásilka byla doručena na adresu uvedenou v objednávce.        |
|Vyzvednuto     |Zpáteční zásilka byla vyzvednuta a naskenována dopravcem.         |
|Přijato     | Zařízení bylo přijato a naskenováno v datovém centru Azure. <br> Po kontrole zásilky začne nahrávání dat ze zařízení.      |
|Kopírování dat     | Probíhá kopírování dat. Sledujte průběh kopírování u vaší objednávky na portálu Azure Portal. <br> Počkejte, dokud se kopírování dat nedokončí. |
|Dokončeno       |Objednávka se úspěšně dokončila.<br> Než odstraníte data z místních serverů, zkontrolujte, jestli se data objevila v Azure.         |
|Dokončeno s chybami| Kopírování dat se dokončilo, ale během kopírování došlo k chybám. <br> Zkontrolujte protokoly kopírování pomocí cesty uvedené na portálu Azure Portal.   |
|Zrušeno            |Došlo ke zrušení objednávky. <br> Buď jste objednávku zrušili, nebo došlo k chybě a objednávku zrušila služba. Pokud objednávku nelze vyřídit do 90 dnů, je objednávka také zrušena a budete upozorněni.     |
|Vyčištění | Vymažou se data na discích zařízení. Vyčištění zařízení se považuje za dokončené, jakmile je na webu Azure Portal k dispozici ke stažení historie objednávky.|



## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [řešit problémy s Data Boxem](data-box-faq.md).
