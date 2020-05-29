---
title: Kurz objednání Azure Data Boxu Heavy | Microsoft Docs
description: Seznamte se s požadavky na nasazení a postupem objednání Azure Data Boxu Heavy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 91d78975106299c3b3a6df6d8dee05f337dd339a
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83742977"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>Kurz: Objednání Azure Data Boxu Heavy


Azure Data Box Heavy je hybridní řešení, které umožňuje rychle, snadno a spolehlivě importovat místní data do Azure. Svá data přenesete do 770TB (přibližná využitelná kapacita) úložného zařízení od Microsoftu a pak toto zařízení odešlete zpět. Data se potom nahrají do Azure.

Tento kurz popisuje, jak Azure Data Box Heavy objednat. V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Požadavky na Data Box Heavy
> * Objednání Data Boxu Heavy
> * Sledování objednávky
> * Zrušení objednávky

## <a name="prerequisites"></a>Požadavky

Před nasazením zařízení dokončete následující požadavky na konfiguraci služby a zařízení Data Box.

### <a name="for-installation-site"></a>Místo instalace

Než začnete, ujistěte se, že:

- Zařízení projde standardními dveřmi a vstupními otvory. Přesto se ujistěte, že zařízení projde všemi vašimi vstupními otvory. Rozměry zařízení: šířka: 66 cm, délka: 122 cm, výška: 71 cm.
- Při instalaci v jiném patře než přízemí musíte mít možnost přenést zařízení výtahem nebo přes rampu. Zařízení váží přibližně 227 kg.
- Ujistěte se, že v datacentru máte rovnou plochu v blízkosti dostupného síťového připojení, kam je možné umístit takto velké zařízení.

### <a name="for-service"></a>Služba

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>Zařízení

Než začnete, ujistěte se, že:
- Vaše zařízení je rozbalené.
- Máte k síti datacentra připojený hostitelský počítač. Data Box Heavy zkopíruje data z tohoto počítače. Na vašem hostitelském počítači musí běžet podporovaný operační systém, jak je popsáno v [systémových požadavcích Azure Data Boxu Heavy](data-box-system-requirements.md).
- Musíte mít přenosný počítač s kabelem RJ-45, abyste se mohli připojit k místnímu uživatelskému rozhraní a nakonfigurovat zařízení. Pomocí přenosného počítače postupně nakonfigurujete jednotlivé uzly zařízení.
- Vaše datacentrum má přístup k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení alespoň 10 GbE.
- Pro každý uzel zařízení potřebujete jeden kabel s rychlostí 40 GB/s nebo 10 GB/s. Zvolte kabely, které jsou kompatibilní se síťovým rozhraním [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html):

    - V případě kabelu s rychlostí 40 GB/s musí na konci kabelu na straně zařízení být QSFP+.
    - V případě kabelu s rychlostí 10 GB/s potřebujete kabel SFP+, který se na jednom konci připojí k přepínači s rychlostí 10 GB/s, s adaptérem QSFP+ na SFP+ (neboli adaptérem QSA) na druhém konci, který se připojí k zařízení.
    - Napájecí kabely se dodávají společně se zařízením.

## <a name="order-data-box-heavy"></a>Objednání Data Boxu Heavy

Provedením následujících kroků na webu Azure Portal si objednejte zařízení.

1. Pomocí svých přihlašovacích údajů Microsoft Azure se přihlaste na následující adrese URL: [https://portal.azure.com](https://portal.azure.com).
2. Vyberte **+ Vytvořit prostředek** a vyhledejte *Azure Data Box*. Vyberte **Azure Data Box**.
    
   [![Vyhledání Azure Data Boxu 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Vyberte **Vytvořit**.

4. Zkontrolujte, jestli je služba Data Box dostupná ve vaší oblasti. Zadejte nebo vyberte následující informace a pak vyberte **Použít**.

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Předplatné     | Vyberte pro službu Data Box předplatné se smlouvou Enterprise, předplatné CSP nebo předplatné Azure Sponsorship. <br> Předplatné je propojeno s vaším fakturačním účtem.       |
    |Typ přenosu     | Vyberte **Importovat do Azure**.        |
    |Zdrojová země/oblast     | Vyberte zemi nebo oblast, ve které jsou data aktuálně umístěná.         |
    |Cílová oblast Azure     | Vyberte oblast Azure, do které chcete data přenést.        |

    [![Výběr dostupnosti řady služeb Data Box](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

5. Vyberte **Data Box Heavy**. Maximální využitelná kapacita pro jednu objednávku je 770 TB.

    [![Výběr Data Boxu Heavy](media/data-box-heavy-deploy-ordered/select-data-box-heavy.png)

6. V části **Objednávka** zadejte **Podrobnosti objednávky**. Zadejte nebo vyberte následující informace a pak vyberte **Další**.
    
    |Nastavení  |Hodnota  |
    |---------|---------|
    |Název     | Zadejte popisný název pro sledování objednávky. <br> Název může být tvořen 3 až 24 písmeny, číslicemi a spojovníky. <br> Název musí začínat a končit písmenem nebo číslicí.      |
    |Skupina prostředků     | Použijte existující skupinu prostředků, nebo vytvořte novou. <br> Skupina prostředků je logický kontejner prostředků, které lze spravovat nebo nasadit společně.         |
    |Cílová oblast Azure     | K účtu úložiště přiřaďte oblast. <br> Další informace najdete v části věnované [dostupnosti v oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=databox).        |
    |Cíl úložiště     | Zvolte účet úložiště, spravované disky nebo obojí. <br> Na základě zadané oblasti Azure vyberte z filtrovaného seznamu existujících účtů úložiště jeden nebo více účtů úložiště. <br>Data Box Heavy je možné propojit až s 10 účty úložiště. <br> Můžete si také vytvořit nový účet **Pro obecné účely v1**, **Pro obecné účely v2** nebo **účet úložiště objektů blob**. <br>Projděte si [podporované typy účtu úložiště pro vaše zařízení](data-box-heavy-system-requirements.md#supported-storage-accounts). <br>Účty úložiště s virtuálními sítěmi se podporují. Pokud chcete službě Data Box povolit práci se zabezpečenými účty úložiště, v nastavení síťové brány firewall účtu úložiště povolte důvěryhodné služby. Další informace najdete v tématu popisujícím [přidání služby Azure Data Box jako důvěryhodné služby](../storage/common/storage-network-security.md#exceptions).|

    Pokud jako cíl úložiště používáte účet úložiště, podívejte se na následující snímek obrazovky:

    ![Objednávka Data Boxu Heavy pro účet úložiště](media/data-box-heavy-deploy-ordered/order-storage-account.png)

    Pokud kromě cíle úložiště v podobě účtu úložiště používáte Data Box Heavy také k vytvoření spravovaných disků z místních virtuálních pevných disků, je potřeba zadat následující informace:

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Skupiny prostředků     | Pokud chcete vytvořit spravované disky z místních virtuálních pevných disků, vytvořte nové skupiny prostředků. Stávající skupinu prostředků můžete použít pouze v případě, že ji dříve vytvořila služba Data Box při vytváření objednávky Data Boxu Heavy pro spravovaný disk. <br> Můžete zadat i více skupin prostředků oddělených středníkem. Podporuje se maximálně 10 skupin prostředků.|

    ![Objednávka Data Boxu Heavy pro spravovaný disk](media/data-box-heavy-deploy-ordered/order-managed-disks.png)

    Účet úložiště zadaný pro spravované disky slouží jako přípravný účet úložiště. Služba Data Box nahraje virtuální pevné disky jako objekty blob stránky do přípravného účtu úložiště, a teprve pak je převede na spravované disky a přesune do skupin prostředků. Další informace najdete v tématu popisujícím [ověření nahrání dat do Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. V části **Dodací adresa** zadejte jméno a příjmení, název a poštovní adresu společnosti a platné telefonní číslo. Vyberte **Ověřit adresu**. 

    Služba ověří, zda je dodací adresa pro službu dostupná. Pokud je pro zadanou dodací adresu služba k dispozici, obdržíte o tom oznámení. Vyberte **Další**.

8. Na kartě **Podrobnosti o oznámení** zadejte e-mailové adresy. Na tyto e-mailové adresy vám služba bude posílat e-mailová oznámení o všech aktualizacích stavu objednávky.

    Doporučujeme použít skupinový e-mail, abyste oznámení mohli dostávat i tehdy, když odejde správce skupiny.

9. Na kartě **Souhrn** zkontrolujte informace týkající se objednávky, kontaktu, oznámení a podmínkách ochrany osobních údajů. Zaškrtněte políčko, že souhlasíte s podmínkami ochrany osobních údajů.

10. Vyberte **Objednat**. Vytvoření objednávky trvá několik minut.


## <a name="track-the-order"></a>Sledování objednávky

Po odeslání objednávky můžete její stav sledovat na webu Azure Portal. Přejděte k vaší objednávce Data Boxu Heavy a v části **Přehled** se zobrazí její stav. Na portálu se objednávka zobrazuje ve stavu **Objednáno**.

Pokud zařízení není k dispozici, dostanete oznámení. Pokud je zařízení k dispozici, Microsoft určí zařízení k odeslání a připraví zásilku. Při přípravě zařízení dojde k těmto akcím:

- Pro každý účet úložiště přidružený k zařízení se vytvoří sdílené složky SMB.
- Pro každou sdílenou složku se vygenerují přihlašovací údaje pro přístup, například uživatelské jméno a heslo.
- Také se vygeneruje heslo zařízení, které pomáhá odemknout zařízení.
- Zařízení Data Box Heavy je trvale uzamčené, aby se zabránilo neoprávněnému přístupu k zařízení.

Jakmile bude zařízení připravené, stav objednávky na portálu se změní na **Zpracováno**.

![Zpracovaná objednávka Data Boxu Heavy](media/data-box-overview/data-box-order-status-processed.png)

Microsoft potom připraví a odešle vaše zařízení přes místní přepravní službu. Po odeslání zařízení obdržíte číslo pro sledování zásilky. Stav objednávky na portálu se změní na **Odesláno**.

![Odeslaná objednávka Data Boxu Heavy](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Zrušení objednávky

Pokud chcete tuto objednávku zrušit, přejděte na webu Azure Portal do části **Přehled** a na panelu příkazů klikněte na **Zrušit**.

Objednávku můžete po odeslání kdykoli zrušit, dokud se stav objednávky nezmění na Zpracováno.
 
Pokud chcete odstranit zrušenou objednávku, přejděte do části **Přehled** a na panelu příkazů klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených s Azure Data Boxem Heavy, mezi která patří:

> [!div class="checklist"]
> * Požadavky
> * Objednání Data Boxu Heavy
> * Sledování objednávky
> * Zrušení objednávky

V dalším kurzu se dozvíte, jak Data Box Heavy nastavit.

> [!div class="nextstepaction"]
> [Nastavení Azure Data Boxu Heavy](./data-box-heavy-deploy-set-up.md)
