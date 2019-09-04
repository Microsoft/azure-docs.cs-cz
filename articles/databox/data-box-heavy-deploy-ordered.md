---
title: Kurz pro objednání Azure Data Box Heavy | Microsoft Docs
description: Seznamte se s požadavky na nasazení a seřazením Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: dce3549bde4c62245e1d2b1f8ac0c88c0b70260c
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241390"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>Kurz: Azure Data Box Heavy objednávky


Azure Data Box Heavy je hybridní řešení, které umožňuje rychle, snadno a spolehlivě importovat vaše místní data do Azure. Data přenášíte do 770 TB (přibližny dostupné kapacity) a pak zařízení dodáte zpátky. Data se potom nahrají do Azure.

V tomto kurzu se dozvíte, jak můžete Azure Data Box Heavy objednat. V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Předpoklady pro Data Box Heavy
> * Objednat Data Box Heavy
> * Sledování objednávky
> * Zrušení objednávky

## <a name="prerequisites"></a>Požadavky

Před nasazením zařízení dokončete následující požadavky na konfiguraci služby a zařízení Data Box.

### <a name="for-installation-site"></a>Pro instalační Web

Než začnete, ujistěte se, že:

- Zařízení se vejde na standardní dveře a entryways. Ujistěte se však, že se zařízení vejde do všech entryways. Rozměry zařízení jsou: width: 26 "Délka: 48 "height: 28 ".
- Pokud je nainstalovaná v jiné než základní desce, potřebujete přístup k zařízení prostřednictvím výtahu nebo rampy. Zařízení se zaváží přibližně po 500 kg.
- Ujistěte se, že máte v datacentru plochý web s blízkým připojením k dostupnému síťovému připojení, které může zařízení s těmito nároky přizpůsobit.


### <a name="for-service"></a>Služba

Než začnete, ujistěte se, že:
- Máte účet služby Microsoft Azure Storage a přihlašovací údaje účtu.
- Typ předplatného, které používáte pro službu Data Box, musí být jeden z následujících:
    - Smlouva Microsoft Enterprise (EA). Další informace o [předplatných se smlouvou Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Cloud Solution Provider (CSP). Další informace o [programu Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Microsoft Azure Sponsorship. Získejte další informace o [programu Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).

- Ujistěte se, že máte přístup vlastníka nebo přispěvatele k předplatnému, abyste mohli vytvořit objednávku Data Box Heavy.

### <a name="for-device"></a>Zařízení

Než začnete, ujistěte se, že:
- Vaše zařízení se nebalí.
- Máte k síti datacentra připojený hostitelský počítač. Data Box Heavy se zkopírují data z tohoto počítače. Hostitelský počítač musí používat podporovaný operační systém, jak je popsáno v tématu [Azure Data box Heavy požadavky na systém](data-box-system-requirements.md).
- Pokud se chcete připojit k místnímu uživatelskému rozhraní a nakonfigurovat zařízení, musíte mít přenosný počítač s kabelem RJ-45. Pomocí přenosného počítače nakonfigurujete každý uzel zařízení jednou.
- Vaše datacentrum má přístup k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení alespoň 10 GbE.
- Pro jeden uzel zařízení potřebujete kabel 1 40 GB/s nebo 10 GB/s. Výběr kabelů, které jsou kompatibilní se síťovým rozhraním [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) :

    - Pro kabel 40 GB/s je potřeba, aby byl konec kabelu zařízení QSFP +.
    - U kabelu s rychlostí 10 GB/s budete potřebovat kabel SFP +, který na jednom konci připojuje k přepínači 10 G, a to s QSFP + až SFP + Adapter (nebo adaptér QUALIFIED Security ASSESSOR) pro konec, který se připojí k zařízení.
    - Napájecí kabely jsou součástí zařízení.

## <a name="order-data-box-heavy"></a>Data Box Heavy objednávky

Provedením následujících kroků na webu Azure Portal si objednejte zařízení.

1. Pomocí svých přihlašovacích údajů Microsoft Azure se přihlaste na následující adrese URL: [https://portal.azure.com](https://portal.azure.com).
2. Vyberte **+ vytvořit prostředek** a vyhledejte *Azure Data box*. Vyberte **Azure Data box**.
    
   [![Vyhledání Azure Data Boxu 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Vyberte **Vytvořit**.

4. Ověřte, jestli je služba Data Box ve vaší oblasti dostupná. Zadejte nebo vyberte následující informace a pak vyberte **použít**.

    |Nastavení  |Value  |
    |---------|---------|
    |Subscription     | Pro Data Box službu vyberte předplatné služby Azure sponzor, CSP nebo Azure. <br> Předplatné je propojeno s vaším fakturačním účtem.       |
    |Typ převodu     | Vyberte **Importovat do Azure**.        |
    |Zdrojová země     | Vyberte zemi nebo oblast, ve které se aktuálně nacházejí vaše data.         |
    |Cílová oblast Azure     | Vyberte oblast Azure, do které chcete data přenést.        |

    [![Vybrat dostupnost Data Box Family](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

5. Vyberte **data box Heavy**. Maximální použitelná kapacita pro jednu objednávku je 770 TB.

    [![Vyberte Data Box Heavy](media/data-box-heavy-deploy-ordered/select-data-box-heavy.png)

6. V části **Objednávka** zadejte **Podrobnosti objednávky**. Zadejte nebo vyberte následující informace a klikněte na tlačítko **Další**.
    
    |Nastavení  |Value  |
    |---------|---------|
    |Name     | Zadejte popisný název pro sledování objednávky. <br> Název může být tvořen 3 až 24 písmeny, číslicemi a spojovníky. <br> Název musí začínat a končit písmenem nebo číslicí.      |
    |Resource group     | Použijte existující skupinu prostředků, nebo vytvořte novou. <br> Skupina prostředků je logický kontejner prostředků, které lze spravovat nebo nasadit společně.         |
    |Cílová oblast Azure     | K účtu úložiště přiřaďte oblast. <br> Další informace najdete v části věnované [dostupnosti v oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=databox).        |
    |Cíl úložiště     | Vyberte si z účtu úložiště nebo spravovaných disků nebo obojí. <br> Na základě zadané oblasti Azure vyberte z filtrovaného seznamu existujících účtů úložiště jeden nebo více účtů úložiště. <br>Data Box Heavy můžete propojit s až 10 účty úložiště. <br> Můžete také vytvořit nový **účet úložiště**pro **obecné účely v1**, **Obecné pro účely v2**nebo BLOB. <br> Účty Azure Data Lake Storage Gen 2 nejsou podporovány. Podívejte se na účty úložiště, které jsou [pro vaše zařízení podporované](data-box-heavy-system-requirements.md#supported-storage-accounts). <br>Účty úložiště s virtuálními sítěmi jsou podporované. Pokud chcete povolit, aby služba Data Box fungovala s zabezpečenými účty úložiště, povolte důvěryhodné služby v nastavení brány firewall sítě účtu úložiště. Další informace najdete v tématu Postup [Přidání služby Azure Data box jako důvěryhodné služby](../storage/common/storage-network-security.md#exceptions).|

    Pokud jako cíl úložiště používáte účet úložiště, zobrazí se následující snímek obrazovky:

    ![Pořadí Data Box Heavy pro účet úložiště](media/data-box-heavy-deploy-ordered/order-storage-account.png)

    Pokud se kromě účtu úložiště jako cíle úložiště používá Data Box Heavy k vytváření spravovaných disků z místních virtuálních pevných disků, musíte zadat následující informace:

    |Nastavení  |Value  |
    |---------|---------|
    |Skupiny prostředků     | Pokud máte v úmyslu vytvořit spravované disky z místních virtuálních pevných disků, vytvořte nové skupiny prostředků. Stávající skupinu prostředků můžete použít jenom v případě, že se skupina prostředků vytvořila dříve při vytváření Data Box Heavyho pořadí pro spravovaný disk pomocí služby Data Box. <br> Zadejte více skupin prostředků oddělených středníkem. Podporuje se maximálně 10 skupin prostředků.|

    ![Pořadí Data Box Heavy pro spravovaný disk](media/data-box-heavy-deploy-ordered/order-managed-disks.png)

    Účet úložiště zadaný pro spravované disky se používá jako pracovní účet úložiště. Služba Data Box nahrává virtuální pevné disky jako objekty blob stránky do pracovního účtu úložiště před jejich převodem na spravované disky a jejich přesunutím do skupin prostředků. Další informace najdete v tématu [ověření nahrávání dat do Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. V části **Dodací adresa** zadejte jméno a příjmení, název a poštovní adresu společnosti a platné telefonní číslo. Vyberte **ověřit adresu**. 

    Služba ověří, zda je dodací adresa pro službu dostupná. Pokud je pro zadanou dodací adresu služba k dispozici, obdržíte o tom oznámení. Vyberte **Další**.

8. Na kartě **Podrobnosti o oznámení** zadejte e-mailové adresy. Na tyto e-mailové adresy vám služba bude posílat e-mailová oznámení o všech aktualizacích stavu objednávky.

    Doporučujeme použít skupinový e-mail, abyste oznámení mohli dostávat i tehdy, když odejde správce skupiny.

9. Na kartě **Souhrn** zkontrolujte informace týkající se objednávky, kontaktu, oznámení a podmínkách ochrany osobních údajů. Zaškrtněte políčko, že souhlasíte s podmínkami ochrany osobních údajů.

10. Vyberte **pořadí**. Vytvoření objednávky trvá několik minut.


## <a name="track-the-order"></a>Sledování objednávky

Po odeslání objednávky můžete její stav sledovat na webu Azure Portal. Přejít na Data Box Heavy objednávku a pak přejít na **Přehled** a zobrazit stav. Na portálu se objednávka zobrazuje ve stavu **Objednáno**.

Pokud zařízení není k dispozici, dostanete oznámení. Pokud je zařízení k dispozici, Microsoft určí zařízení k odeslání a připraví zásilku. Při přípravě zařízení dojde k těmto akcím:

- Pro každý účet úložiště přidružený k zařízení se vytvoří sdílené složky SMB.
- Pro každou sdílenou složku se vygenerují přihlašovací údaje pro přístup, například uživatelské jméno a heslo.
- Také se vygeneruje heslo zařízení, které pomáhá odemknout zařízení.
- Data Box Heavy je zamčený, aby se zabránilo neoprávněnému přístupu k zařízení v libovolném bodě.

Jakmile bude zařízení připravené, stav objednávky na portálu se změní na **Zpracováno**.

![Zpracování Data Box Heavyho pořadí](media/data-box-overview/data-box-order-status-processed.png)

Microsoft potom připraví a odešle vaše zařízení přes místní přepravní službu. Po odeslání zařízení obdržíte číslo pro sledování zásilky. Stav objednávky na portálu se změní na **Odesláno**.

![Data Box Heavy objednávka byla odeslána.](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Zrušení objednávky

Pokud chcete tuto objednávku zrušit, přejděte na webu Azure Portal do části **Přehled** a na panelu příkazů klikněte na **Zrušit**.

Objednávku můžete po odeslání kdykoli zrušit, dokud se stav objednávky nezmění na Zpracováno.
 
Pokud chcete odstranit zrušenou objednávku, přejděte do části **Přehled** a na panelu příkazů klikněte na **Odstranit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o Azure Data Box Heavy témata, jako například:

> [!div class="checklist"]
> * Požadavky
> * Data Box Heavy objednávky
> * Sledování objednávky
> * Zrušení objednávky

Přejděte k dalšímu kurzu, kde se dozvíte, jak nastavit Data Box Heavy.

> [!div class="nextstepaction"]
> [Nastavení Azure Data Box Heavy](./data-box-heavy-deploy-set-up.md)
