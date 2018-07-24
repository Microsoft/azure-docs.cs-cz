---
title: Kurz objednání služby Microsoft Azure Data Box Disk | Microsoft Docs
description: V tomto kurzu se naučíte zaregistrovat a objednat službu Azure Data Box Disk, abyste mohli importovat data do Azure.
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: f46a3ecb3c4ef6ace31b7010ba9068ab04a43315
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126537"
---
# <a name="tutorial-order-an-azure-data-box-disk-preview"></a>Kurz: Objednání služby Azure Data Box Disk (Preview)

Azure Data Box Disk je cloudové řešení, které umožňuje rychle, bezpečně a spolehlivě importovat místní data do Azure. Svoje data přesunete na disky SSD (Solid-State Disk), které dostanete od Microsoftu, a tyto disky pak pošlete zpět. Data se potom nahrají do Azure. 

Tento kurz popisuje, jak Azure Data Box Disk objednat. V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Registrace služby Data Box Disk
> * Objednání služby Data Box Disk
> * Sledování objednávky
> * Zrušení objednávky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

> [!IMPORTANT]
> - Data Box Disk je ve verzi Preview. Před objednáním a nasazením tohoto řešení si přečtěte [podmínky užívání pro předběžné verze systému Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
> - Po dobu platnosti verze Preview je možné disky poslat zákazníkům služby Data Box Disk v USA a Evropské unii. Další informace najdete v části [Regionální dostupnost](data-box-disk-overview.md#region-availability).

## <a name="sign-up"></a>Registrace 

Data Box Disk je ve verzi Preview a musíte se v ní zaregistrovat. Následujícím postupem si zaregistrujte službu Data Box:

1. Přihlaste se k portálu Azure Portal na adrese: [https://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs).
2. Vyberte předplatné, které chcete povolit pro verzi Preview. Odpovězte na otázky týkající velikosti dat, země trvalého umístění dat, časového rámce a frekvence přenosu dat. Klikněte na **Chci se zaregistrovat**.
3. Po registraci a povolení verze Preview si můžete objednat službu Data Box Disk.

## <a name="order-data-box-disk"></a>Objednání služby Data Box Disk

Službu Data Box Disk si objednejte na portálu [Azure Portal](https://aka.ms/azuredataboxfromdiskdocs) následujícím postupem.

1. V levém horním rohu portálu klikněte na **+ Vytvořit prostředek** a vyhledejte *Azure Data Box*. Klikněte na **Azure Data Box**.
    
   ![Vyhledání Azure Data Box 1](media/data-box-disk-deploy-ordered/search-data-box11.png)

2. Klikněte na možnost **Vytvořit**.

3. Podívejte se, zda je služba Data Box dostupná ve vaší oblasti. Zadejte nebo vyberte následující informace a pak klikněte na **Použít**.

    ![Možnost výběru služby Data Box Disk](media/data-box-disk-deploy-ordered/select-data-box-sku-1.png)

    |Nastavení|Hodnota|
    |---|---|
    |Předplatné|Vyberte předplatné, pro které je služba Data Box povolena.<br> Předplatné je propojeno s vaším fakturačním účtem. |
    |Typ přenosu| Import do Azure|
    |Zdrojová země | Vyberte zemi, ve které jsou data aktuálně umístěna.|
    |Cílová oblast Azure|Vyberte oblast Azure, do které chcete data přenést.|

  
5.  Vyberte **Data Box Disk**. Maximální kapacita řešení u jedné objednávky 5 disků je 35 TB. V případě větších objemů dat můžete vytvořit více objednávek. 

     ![Možnost výběru služby Data Box Disk](media/data-box-disk-deploy-ordered/select-data-box-sku-zoom.png)

6.  V části **Objednávka** zadejte **Podrobnosti objednávky**. Zadejte nebo vyberte následující informace.

    |Nastavení|Hodnota|
    |---|---|
    |Název|Zadejte popisný název pro sledování objednávky.<br> Název může být tvořen 3 až 24 písmeny, číslicemi a spojovníky. <br> Název musí začínat a končit písmenem nebo číslicí. |
    |Skupina prostředků| Použijte existující skupinu prostředků, nebo vytvořte novou. <br> Skupina prostředků je logický kontejner prostředků, které lze spravovat nebo nasadit společně. |
    |Cílová oblast Azure| K účtu úložiště přiřaďte oblast.<br> V současné době se podporují účty úložiště ve všech oblastech USA a západní a severní Evropy. |
    |Účty úložiště|Na základě zadané oblasti Azure vyberte účet z filtrovaného seznamu existujících účtů úložiště. <br>Můžete si také vytvořit nový účet pro obecné účely v1 nebo obecné účely v2. |
    |Odhadovaná velikost dat v TB| Zadejte odhad v TB. <br>Podle objemu dat vám Microsoft pošle odpovídající počet 8TB disků SSD (s využitelnou kapacitou 7 TB). <br>Maximální využitelná kapacita 5 disků je 35 TB. |

13. Klikněte na **Další**. 

    ![Zadání podrobností objednávky](media/data-box-disk-deploy-ordered/data-box-order-details.png)

14. Na kartě **Dodací adresa** zadejte jméno a příjmení, název a poštovní adresu společnosti a platné telefonní číslo. Klikněte na **Ověřit adresu**. Služba ověří, zda je dodací adresa pro službu dostupná. Pokud je pro zadanou dodací adresu služba k dispozici, obdržíte o tom oznámení. 

    ![Zadání dodací adresy](media/data-box-disk-deploy-ordered/data-box-shipping-address.png)
15. Na kartě **Podrobnosti o oznámení** zadejte e-mailové adresy. Na tyto e-mailové adresy vám služba bude posílat e-mailová oznámení o všech aktualizacích stavu objednávky. 

    Doporučujeme použít skupinový e-mail, abyste oznámení mohli dostávat i tehdy, když odejde správce skupiny.

16. Na kartě **Souhrn** zkontrolujte informace týkající se objednávky, kontaktu, oznámení a podmínkách ochrany osobních údajů. Zaškrtněte políčko, že souhlasíte s podmínkami ochrany osobních údajů.

17. Klikněte na **Objednat**. Vytvoření objednávky trvá několik minut.

 
## <a name="track-the-order"></a>Sledování objednávky

Po odeslání objednávky můžete její stav sledovat na portálu Azure Preview Portal. Přejděte na objednávku a potom do části **Přehled**, kde se můžete podívat na stav. Na portálu se úloha zobrazuje ve stavu **Objednáno**. 

![Služba Data Box Disk ve stavu Objednáno](media/data-box-disk-deploy-ordered/data-box-portal-ordered.png) 

Pokud disky nejsou k dispozici, obdržíte oznámení. Pokud jsou disky k dispozici, Microsoft určí disky k odeslání a připraví balíček s disky. Při přípravě disků dojde k těmto akcím:

- Disky se zašifrují pomocí šifrování AES-128 nástrojem BitLocker.  
- Disky jsou uzamknuty, aby se zabránilo neoprávněnému přístupu k nim.
- Během tohoto procesu se vygeneruje klíč k odemknutí disků.

Jakmile budou disky připraveny, stav objednávky na portálu se změní na **Zpracováno**.

Microsoft potom připraví a odešle disky přes místní přepravní službu. Po odeslání disků obdržíte číslo pro sledování zásilky. Stav objednávky na portálu se změní na **Odesláno**.



## <a name="cancel-the-order"></a>Zrušení objednávky

Pokud chcete tuto objednávku zrušit, přejděte na portálu Azure Preview Portal do části **Přehled** a na panelu příkazů klikněte na **Zrušit**. 

Objednávku můžete zrušit pouze při objednávání disků a při zpracování objednávky k odeslání. Jakmile je objednávka zpracována, už ji nelze zrušit. 

![Zrušení objednávky](media/data-box-disk-deploy-ordered/cancel-order1.png)

Pokud chcete odstranit zrušenou objednávku, přejděte do části **Přehled** a na panelu příkazů klikněte na **Odstranit**. 


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
> * Registrace služby Data Box Disk
> * Objednání služby Data Box Disk
> * Sledování objednávky
> * Zrušení objednávky

V dalším kurzu se dozvíte, jak službu Data Box Disk nastavit.

> [!div class="nextstepaction"]
> [Nastavení služby Azure Data Box Disk](./data-box-disk-deploy-set-up.md)


