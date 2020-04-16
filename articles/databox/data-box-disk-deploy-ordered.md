---
title: Kurz k objednání disku datové schránky Azure | Dokumenty společnosti Microsoft
description: V tomto kurzu se naučíte zaregistrovat a objednat službu Azure Data Box Disk, abyste mohli importovat data do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 7b27f10567816884b14a0a2c69e3bd8e85cb7a76
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81398705"
---
# <a name="tutorial-order-an-azure-data-box-disk"></a>Kurz: Objednejte disk datové schránky Azure

Azure Data Box Disk je řešení hybridního cloudu, které vám umožňuje rychle, snadno a spolehlivě importovat místní data do Azure. Svoje data přesunete na disky SSD (Solid-State Disk), které dostanete od Microsoftu, a tyto disky pak pošlete zpět. Data se potom nahrají do Azure.

Tento kurz popisuje, jak Azure Data Box Disk objednat. V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Objednání služby Data Box Disk
> * Sledování objednávky
> * Zrušení objednávky

## <a name="prerequisites"></a>Požadavky

Před nasazením vyplňte následující požadavky konfigurace pro službu Data Box a disk datové schránky.

### <a name="for-service"></a>Služba

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>Zařízení

Než začnete, ujistěte se, že:
- Máte k dispozici klientský počítač, ze kterého můžete kopírovat data. Klientský počítač musí splňovat tyto požadavky:
    - Spusťte [podporovaný operační systém](data-box-disk-system-requirements.md#supported-operating-systems-for-clients).
    - Musí na něm být nainstalovaný [požadovaný software](data-box-disk-system-requirements.md#other-required-software-for-windows-clients), pokud jde o klienta pro Windows.  

## <a name="order-data-box-disk"></a>Objednání služby Data Box Disk

Přihlaste se k:

- Portál Azure na této https://portal.azure.com adrese URL: chcete-li objednat disk datové schránky.
- Nebo portál Azure Government na https://portal.azure.ustéto adrese URL: . Další podrobnosti najdete v části [Připojení k Azure Government pomocí portálu](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

Postupujte podle následujících kroků k objednání disku datové schránky.

1. V levém horním rohu portálu klikněte na **+ Vytvořit prostředek** a vyhledejte *Azure Data Box*. Klikněte na **Azure Data Box**.
    
   ![Vyhledání Azure Data Box 1](media/data-box-disk-deploy-ordered/search-data-box11.png)

2. Klikněte na **Vytvořit**.

3. Podívejte se, zda je služba Data Box dostupná ve vaší oblasti. Zadejte nebo vyberte následující informace a pak klikněte na **Použít**.

    ![Možnost výběru služby Data Box Disk](media/data-box-disk-deploy-ordered/select-data-box-sku-1.png)

    |Nastavení|Hodnota|
    |---|---|
    |Předplatné|Vyberte předplatné, pro které je služba Data Box povolena.<br> Předplatné je propojeno s vaším fakturačním účtem. |
    |Typ přenosu| Import do Azure|
    |Zdrojová země | Vyberte zemi nebo oblast, ve které jsou data aktuálně umístěná.|
    |Cílová oblast Azure|Vyberte oblast Azure, do které chcete data přenést.|

  
5.  Vyberte **Data Box Disk**. Maximální kapacita řešení u jedné objednávky 5 disků je 35 TB. V případě větších objemů dat můžete vytvořit více objednávek.

     ![Možnost výběru služby Data Box Disk](media/data-box-disk-deploy-ordered/select-data-box-sku-zoom.png)

6.  V části **Objednávka** zadejte **Podrobnosti objednávky**. Zadejte nebo vyberte následující informace.

    |Nastavení|Hodnota|
    |---|---|
    |Název|Zadejte popisný název pro sledování objednávky.<br> Název může být tvořen 3 až 24 písmeny, číslicemi a spojovníky. <br> Název musí začínat a končit písmenem nebo číslicí. |
    |Skupina prostředků| Použijte existující skupinu prostředků, nebo vytvořte novou. <br> Skupina prostředků je logický kontejner prostředků, které lze spravovat nebo nasadit společně. |
    |Cílová oblast Azure| K účtu úložiště přiřaďte oblast.<br> V současné době se podporují účty úložiště ve všech oblastech USA, Západní Evropě, Severní Evropě, Kanadě a Austrálii. |
    |Odhadovaná velikost dat v TB| Zadejte odhad v TB. <br>Podle objemu dat vám Microsoft pošle odpovídající počet 8TB disků SSD (s využitelnou kapacitou 7 TB). <br>Maximální využitelná kapacita 5 disků je 35 TB. |
    |Klíč pro disky| Pokud zaškrtnete možnost **Použít namísto klíče vygenerovaného službou Azure vlastní klíč**, zadejte klíč pro disky. <br> Zadejte alfanumerický klíč o 12 až 32 znakůch, který má alespoň jeden číselný a jeden speciální znak. Povolené speciální znaky jsou `@?_+`. <br> Tuto možnost můžete volitelně přeskočit a k odemknutí disků použít klíč vygenerovaný službou Azure.|
    |Cíl úložiště     | Zvolte účet úložiště, spravované disky nebo obojí. <br> Na základě zadané oblasti Azure vyberte účet úložiště z filtrovaného seznamu existujícího účtu úložiště. Disk datové schránky lze propojit pouze s 1 účtem úložiště. <br> Můžete si také vytvořit nový účet **Pro obecné účely v1**, **Pro obecné účely v2** nebo **účet úložiště objektů blob**. <br>Účty úložiště s virtuálními sítěmi se podporují. Pokud chcete službě Data Box povolit práci se zabezpečenými účty úložiště, v nastavení síťové brány firewall účtu úložiště povolte důvěryhodné služby. Další informace najdete v tématu [přidání Datové schránky Azure jako důvěryhodné služby](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).|

    Pokud jako cíl úložiště používáte účet úložiště, podívejte se na následující snímek obrazovky:

    ![Pořadí disku datové schránky pro účet úložiště](media/data-box-disk-deploy-ordered/order-storage-account.png)

    Pokud pomocí disku data boxu vytvořte spravované disky z místních virtuálních pevných disků, musíte také zadat následující informace:

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Skupina prostředků     | Pokud chcete vytvořit spravované disky z místních virtuálních pevných disků, vytvořte novou skupinu prostředků. Existující skupinu prostředků použijte pouze v případě, že byla vytvořena pro pořadí disků datové schránky pro spravovaný disk službou Data Box. <br> Je podporována pouze jedna skupina prostředků.|

    ![Pořadí disku datové schránky pro spravovaný disk](media/data-box-disk-deploy-ordered/order-managed-disks.png)

    Účet úložiště zadaný pro spravované disky slouží jako přípravný účet úložiště. Služba Data Box nahraje virtuální disky do účtu pracovního úložiště a pak je převede na spravované disky a přesune se do skupin prostředků. Další informace najdete v tématu popisujícím [ověření nahrání dat do Azure](data-box-disk-deploy-upload-verify.md#verify-data-upload-to-azure).

13. Klikněte na **Další**.

    ![Zadání podrobností objednávky](media/data-box-disk-deploy-ordered/data-box-order-details.png)

14. Na kartě **Dodací adresa** zadejte jméno a příjmení, název a poštovní adresu společnosti a platné telefonní číslo. Klikněte na **Ověřit adresu**. Služba ověří, zda je dodací adresa pro službu dostupná. Pokud je pro zadanou dodací adresu služba k dispozici, obdržíte o tom oznámení. 

    ![Zadání dodací adresy](media/data-box-disk-deploy-ordered/data-box-shipping-address.png)
15. Na kartě **Podrobnosti o oznámení** zadejte e-mailové adresy. Na tyto e-mailové adresy vám služba bude posílat e-mailová oznámení o všech aktualizacích stavu objednávky. 

    Doporučujeme použít skupinový e-mail, abyste oznámení mohli dostávat i tehdy, když odejde správce skupiny.

16. Na kartě **Souhrn** zkontrolujte informace týkající se objednávky, kontaktu, oznámení a podmínkách ochrany osobních údajů. Zaškrtněte políčko, že souhlasíte s podmínkami ochrany osobních údajů.

17. Klikněte na **Objednat**. Vytvoření objednávky trvá několik minut.

 
## <a name="track-the-order"></a>Sledování objednávky

Po odeslání objednávky můžete její stav sledovat na webu Azure Portal. Přejděte na objednávku a potom do části **Přehled**, kde se můžete podívat na stav. Na portálu se úloha zobrazuje ve stavu **Objednáno**.

![Služba Data Box Disk ve stavu Objednáno](media/data-box-disk-deploy-ordered/data-box-portal-ordered.png) 

Pokud disky nejsou k dispozici, obdržíte oznámení. Pokud jsou disky k dispozici, Microsoft určí disky k odeslání a připraví balíček s disky. Při přípravě disků dojde k těmto akcím:

- Disky se zašifrují pomocí šifrování AES-128 nástrojem BitLocker.  
- Disky jsou uzamknuty, aby se zabránilo neoprávněnému přístupu k nim.
- Během tohoto procesu se vygeneruje klíč k odemknutí disků.

Jakmile budou disky připraveny, stav objednávky na portálu se změní na **Zpracováno**.

Microsoft potom připraví a odešle disky přes místní přepravní službu. Po odeslání disků obdržíte číslo pro sledování zásilky. Stav objednávky na portálu se změní na **Odesláno**.

## <a name="cancel-the-order"></a>Zrušení objednávky

Pokud chcete tuto objednávku zrušit, přejděte na webu Azure Portal do části **Přehled** a na panelu příkazů klikněte na **Zrušit**.

Objednávku můžete zrušit pouze při objednávání disků a při zpracování objednávky k odeslání. Jakmile je objednávka zpracována, už ji nelze zrušit.

![Zrušení objednávky](media/data-box-disk-deploy-ordered/cancel-order1.png)

Pokud chcete odstranit zrušenou objednávku, přejděte do části **Přehled** a na panelu příkazů klikněte na **Odstranit**.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
> * Objednání služby Data Box Disk
> * Sledování objednávky
> * Zrušení objednávky

V dalším kurzu se dozvíte, jak službu Data Box Disk nastavit.

> [!div class="nextstepaction"]
> [Nastavení služby Azure Data Box Disk](./data-box-disk-deploy-set-up.md)
