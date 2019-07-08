---
title: Kurz pro Azure Data Box náročné pořadí | Dokumentace Microsoftu
description: Přečtěte si požadavky nasazení a způsob řazení Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: c7fbd37ff8d40f27e0ca18a6f9816d3d96422ab9
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592401"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>Kurz: Pořadí Azure Data Box náročná na výkon


Azure Data Box velké je hybridní řešení, která umožňuje import místních dat do Azure v rámci rychlého, jednoduchý a spolehlivý způsob. Přenos dat se zařízením úložiště poskytované Microsoft 770 TB (přibližná využitelné kapacity) a potom odešlete zařízení zpět. Data se potom nahrají do Azure.

Tento kurz popisuje, jak si můžete objednat Azure Data Box náročné. V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Požadavky na Data Box náročná na výkon
> * Pořadí dat velkého pole
> * Sledování objednávky
> * Zrušení objednávky

## <a name="prerequisites"></a>Požadavky

Před nasazením zařízení dokončete následující požadavky na konfiguraci služby a zařízení Data Box.

### <a name="for-installation-site"></a>Pro instalaci lokality

Než začnete, ujistěte se, že:

- Zařízení vyhovuje prostřednictvím standardních dveří a entryways. Ale ujistěte se, že zařízení můžete přizpůsobit prostřednictvím všech entryways. Dimenze zařízení jsou: šířka: 26" Délka: 48" height: 28”.
- Nainstalovaná na dolní mez než dolní mez základu, potřebujete přístup pro zařízení prostřednictvím hodnocení nebo doběhu. Zařízení váží přibližně ~ 500 lbs.
- Ujistěte se, že máte lokalitu bez stromové struktury v datovém centru s blízkosti dostupné síťové připojení, který zvládne zařízení s nároky na toto místo.


### <a name="for-service"></a>Služba

Než začnete, ujistěte se, že:
- Máte účet služby Microsoft Azure Storage a přihlašovací údaje účtu.
- Typ předplatného, které používáte pro službu Data Box, musí být jeden z následujících:
    - Smlouva Microsoft Enterprise (EA). Další informace o [předplatných se smlouvou Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Cloud Solution Provider (CSP). Další informace o [programu Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Microsoft Azure Sponsorship. Získejte další informace o [programu Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).

- Ujistěte se, že máte přístup přispěvatele nebo vlastníka předplatného vytvořit objednávku Data Box náročné.

### <a name="for-device"></a>Zařízení

Než začnete, ujistěte se, že:
- Vaše zařízení bylo dokončeno.
- Máte k síti datacentra připojený hostitelský počítač. Data Box náročné zkopíruje data z tohoto počítače. Hostitelský počítač musí běžet podporovaný operační systém, jak je popsáno v [Azure Data Box náročné požadavky na systém](data-box-system-requirements.md).
- Musíte mít přenosném počítači pomocí kabelu RJ-45 kategorie pro připojení k místní uživatelského rozhraní a konfigurace zařízení. Pomocí přenosný počítač nakonfigurovat každý uzel zařízení jen jednou.
- Vaše datacentrum má přístup k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení alespoň 10 GbE.
- Budete potřebovat 40 GB/s nebo zapojení 10 GB/s na jeden uzel zařízení. Zvolte kabely, které jsou kompatibilní s [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) síťové rozhraní:

    - Pro kabel 40 GB/s musí být QSFP + zařízení konec kabelu.
    - 10 GB/s kabel je nutné kabel SFP +, která zpřístupní 10 G přepínač na jednom konci a QSFP + SFP + adaptéru (nebo adaptér podmínky) end, který se připojuje k zařízení.
    - Napájecích kabelů jsou součástí zařízení.

## <a name="order-data-box-heavy"></a>Pořadí dat pole náročná na výkon

Provedením následujících kroků na webu Azure Portal si objednejte zařízení.

1. Pomocí svých přihlašovacích údajů Microsoft Azure se přihlaste na následující adrese URL: [https://portal.azure.com](https://portal.azure.com).
2. Vyberte **+ vytvořit prostředek** a vyhledejte *Azure Data Box*. Vyberte **Azure Data Box**.
    
   [![Vyhledání Azure Data Boxu 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Vyberte **Vytvořit**.

4. Zkontrolujte, jestli služba Data Box je k dispozici ve vaší oblasti. Zadejte nebo vyberte následující informace a zvolte **použít**.

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Předplatné     | Vyberte předplatné EA, CSP nebo Azure sponsorship pro službu Data Box. <br> Předplatné je propojeno s vaším fakturačním účtem.       |
    |Typ přenosu     | Vyberte **Importovat do Azure**.        |
    |Zdrojová země     | Vyberte zemi nebo oblast, kde se aktuálně nachází vaše data.         |
    |Cílová oblast Azure     | Vyberte oblast Azure, do které chcete data přenést.        |

    [![Vyberte dostupnost řady zařízení Data Box](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

5. Vyberte **Data Box náročná na výkon**. Maximální využitelné kapacity na jednu objednávku je 770 TB.

    [![Výběr Data pole náročná na výkon](media/data-box-heavy-deploy-ordered/select-data-box-heavy.png)

6. V části **Objednávka** zadejte **Podrobnosti objednávky**. Zadejte nebo vyberte následující informace a zvolte **Další**.
    
    |Nastavení  |Hodnota  |
    |---------|---------|
    |Název     | Zadejte popisný název pro sledování objednávky. <br> Název může být tvořen 3 až 24 písmeny, číslicemi a spojovníky. <br> Název musí začínat a končit písmenem nebo číslicí.      |
    |Skupina prostředků     | Použijte existující skupinu prostředků, nebo vytvořte novou. <br> Skupina prostředků je logický kontejner prostředků, které lze spravovat nebo nasadit společně.         |
    |Cílová oblast Azure     | K účtu úložiště přiřaďte oblast. <br> Další informace najdete v části věnované [dostupnosti v oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=databox).        |
    |Cílové úložiště     | Vyberte si z účtu úložiště, spravované disky nebo obojí. <br> Na základě zadané oblasti Azure vyberte z filtrovaného seznamu existujících účtů úložiště jeden nebo více účtů úložiště. <br>Data Box náročné lze propojit s až 10 účty úložiště. <br> Můžete také vytvořit novou **pro obecné účely v1**, **pro obecné účely v2**, nebo **účtu úložiště objektů Blob**. <br> Účty Azure Data Lake Storage generace 2 nejsou podporovány. Zobrazit [účty úložiště, které jsou podporované s vaším zařízením](data-box-heavy-system-requirements.md#supported-storage-accounts). <br>Účty úložiště s virtuálními sítěmi se podporují. Služba Data Box pro práci s účty úložiště zabezpečené, povolte důvěryhodným službám v nastavení brány firewall sítě účtu úložiště. Další informace najdete v tématu Jak [přidat zařízení Azure Data Box service jako důvěryhodné služby](../storage/common/storage-network-security.md#exceptions).|

    Pokud používáte účet úložiště jako cílové úložiště, vidíte na následujícím snímku obrazovky:

    ![Data Box náročné pořadí pro účet úložiště](media/data-box-heavy-deploy-ordered/order-storage-account.png)

    Pokud kromě účtu úložiště jako cíl úložiště také používáte náročné pole Data pro vytvoření spravovaných disků z místních virtuálních pevných disků, budete muset zadat následující informace:

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Skupiny prostředků     | Pokud máte v úmyslu vytvoření spravovaných disků z místních virtuálních pevných disků, vytvořte nové skupiny prostředků. Můžete použít existující skupinu prostředků, pouze v případě, že skupina prostředků se vytvořila dříve při vytváření náročné pole Data objednávky pro spravovaný disk pomocí služby Data Box. <br> Zadejte více skupin prostředků, které jsou odděleny středníky. Podporují se maximálně 10 skupin prostředků.|

    ![Data Box náročné pořadí spravovaného disku](media/data-box-heavy-deploy-ordered/order-managed-disks.png)

    Účet úložiště zadaný pro spravované disky se používá jako přípravného účtu úložiště. Nahrávání služby Data Box virtuálních pevných disků jako stránka objekty BLOB do přípravného účtu úložiště před převod na spravované disky a přesunete do skupiny prostředků. Další informace najdete v tématu [ověřit data nahrát do Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. V části **Dodací adresa** zadejte jméno a příjmení, název a poštovní adresu společnosti a platné telefonní číslo. Vyberte **Ověřte adresu**. 

    Služba ověří, zda je dodací adresa pro službu dostupná. Pokud je pro zadanou dodací adresu služba k dispozici, obdržíte o tom oznámení. Vyberte **Další**.

8. Na kartě **Podrobnosti o oznámení** zadejte e-mailové adresy. Na tyto e-mailové adresy vám služba bude posílat e-mailová oznámení o všech aktualizacích stavu objednávky.

    Doporučujeme použít skupinový e-mail, abyste oznámení mohli dostávat i tehdy, když odejde správce skupiny.

9. Na kartě **Souhrn** zkontrolujte informace týkající se objednávky, kontaktu, oznámení a podmínkách ochrany osobních údajů. Zaškrtněte políčko, že souhlasíte s podmínkami ochrany osobních údajů.

10. Vyberte **pořadí**. Vytvoření objednávky trvá několik minut.


## <a name="track-the-order"></a>Sledování objednávky

Po odeslání objednávky můžete její stav sledovat na webu Azure Portal. Přejděte na těžké pole Data objednávky a potom přejděte ke **přehled** k zobrazení stavu. Na portálu se objednávka zobrazuje ve stavu **Objednáno**.

Pokud zařízení není k dispozici, dostanete oznámení. Pokud je zařízení k dispozici, Microsoft určí zařízení k odeslání a připraví zásilku. Při přípravě zařízení dojde k těmto akcím:

- Pro každý účet úložiště přidružený k zařízení se vytvoří sdílené složky SMB.
- Pro každou sdílenou složku se vygenerují přihlašovací údaje pro přístup, například uživatelské jméno a heslo.
- Také se vygeneruje heslo zařízení, které pomáhá odemknout zařízení.
- Data Box náročné je pevně nastavené na zabránit neoprávněnému přístupu k zařízení v libovolném bodě.

Jakmile bude zařízení připravené, stav objednávky na portálu se změní na **Zpracováno**.

![Data Box náročné pořadí zpracování](media/data-box-overview/data-box-order-status-processed.png)

Microsoft potom připraví a odešle vaše zařízení přes místní přepravní službu. Po odeslání zařízení obdržíte číslo pro sledování zásilky. Stav objednávky na portálu se změní na **Odesláno**.

![Pole náročné pořadí dat odeslaných](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Zrušení objednávky

Pokud chcete tuto objednávku zrušit, přejděte na webu Azure Portal do části **Přehled** a na panelu příkazů klikněte na **Zrušit**.

Objednávku můžete po odeslání kdykoli zrušit, dokud se stav objednávky nezmění na Zpracováno.
 
Pokud chcete odstranit zrušenou objednávku, přejděte do části **Přehled** a na panelu příkazů klikněte na **Odstranit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o Azure Data Box náročné témata, jako:

> [!div class="checklist"]
> * Požadavky
> * Pořadí dat pole náročná na výkon
> * Sledování objednávky
> * Zrušení objednávky

Přejděte k dalšímu kurzu, přečtěte si, jak nastavit vaše Data Box náročné.

> [!div class="nextstepaction"]
> [Váš Azure Data Box náročné nastavit](./data-box-heavy-deploy-set-up.md)
