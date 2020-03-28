---
title: Kurz pro přípravu portálu Azure, prostředí datového centra k nasazení Azure Data Box Edge | Dokumenty společnosti Microsoft
description: První kurz o nasazení Azure Data Box Edge zahrnuje přípravu portálu Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 90ed4bf8f0389619f130e998ed76c720442092b2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79474471"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>Kurz: Příprava na nasazení Azure Data Box Edge  

Toto je první kurz v řadě kurzů nasazení, které jsou nutné k úplnému nasazení Azure Data Box Edge. Tento kurz popisuje, jak připravit portál Azure k nasazení prostředku Data Box Edge.

K dokončení této instalace a procesu konfigurace potřebujete oprávnění správce. Příprava portálu trvá méně než 10 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvoření nového prostředku
> * Získání aktivačního klíče

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

### <a name="get-started"></a>Začínáme

Chcete-li nasadit Data Box Edge, naleznete v následujících kurzech v předepsaném pořadí.

| **#** | **V tomto kroku** | **Použijte tyto dokumenty** |
| --- | --- | --- | 
| 1. |**[Příprava webu Azure Portal pro Data Box Edge](data-box-edge-deploy-prep.md)** |Před instalací fyzického zařízení Data Box Edge vytvořte a nakonfigurujte prostředek Data Box Edge. |
| 2. |**[Instalace Data Boxu Edge](data-box-edge-deploy-install.md)**|Rozbalte fyzické zařízení Data Box Edge, usaďte ho do racku a zapojte kabeláž.  |
| 3. |**[Připojení, nastavení a aktivace okraje datové schránky](data-box-edge-deploy-connect-setup-activate.md)** |Připojte se k místnímu webovému uživatelskému rozhraní, dokončete nastavení zařízení a aktivujte zařízení. Zařízení je připravené na nastavení sdílených složek SMB nebo sdílených složek systému souborů NFS.  |
| 4. |**[Přenos dat pomocí Data Boxu Edge](data-box-edge-deploy-add-shares.md)** |Přidejte sdílené složky a připojte se k nim pomocí protokolu SMB nebo přes systém souborů NFS. |
| 5. |**[Transformace dat pomocí okraje datové schránky](data-box-edge-deploy-configure-compute.md)** |Nakonfigurujte výpočetní moduly na zařízení tak, aby transformovaly data při přesunu do Azure. |

Teď můžete začít nastavovat Azure Portal.

## <a name="prerequisites"></a>Požadavky

Následují požadavky konfigurace pro prostředek Data Box Edge, zařízení Data Box Edge a síť datového centra.

### <a name="for-the-data-box-edge-resource"></a>Prostředek Data Box Edge

Než začnete, ujistěte se, že:

* Vaše předplatné Microsoft Azure je povolené pro prostředek Azure Stack Edge. Ujistěte se, že jste použili podporované předplatné, jako je [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/), [Poskytovatel cloudových řešení (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)nebo [Sponzorství Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Máte přístup vlastníka nebo přispěvatele na úrovni skupiny prostředků pro brány Data Box Edge/Data Box Gateway, IoT Hub a prostředky Úložiště Azure.
    - Chcete-li vytvořit jakýkoli prostředek Data Box Edge/ Data Box Gateway, měli byste mít oprávnění jako přispěvatel (nebo vyšší) s vymezeným oborem na úrovni skupiny prostředků. Musíte se také ujistit, že `Microsoft.DataBoxEdge` je registrován zprostředkovatel. Informace o registraci naleznete v seznamu [Registrovat zprostředkovatele prostředků](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Chcete-li vytvořit jakýkoli prostředek služby IoT Hub, ujistěte se, že je registrovaný poskytovatel Microsoft.Devices. Informace o registraci naleznete v seznamu [Registrovat zprostředkovatele prostředků](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Chcete-li vytvořit prostředek účtu úložiště, budete opět potřebovat přispěvatele nebo vyšší přístup s rozsahem na úrovni skupiny prostředků. Azure Storage je ve výchozím nastavení registrovaný poskytovatel prostředků.
- Máte přístup správce nebo uživatele k rozhraní Microsoft Graph API. Další informace naleznete v [tématu Odkaz na oprávnění aplikace Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference).
- Máte účet služby Microsoft Azure Storage a přihlašovací údaje účtu.

### <a name="for-the-data-box-edge-device"></a>Zařízení Data Box Edge

Před nasazením fyzického zařízení se ujistěte, že:

- Zkontrolovali jste bezpečnostní informace, které byly zahrnuty v balíku zásilky.
- Ve standardním 19" racku máte k dispozici 1U slot pro montáž zařízení do racku.
- Máte přístup k ploché, stabilní a rovné pracovní ploše, kde může zařízení bezpečně odpočívat.
- Místo, kde chcete zařízení nastavit, má standardní střídavý proud z nezávislého zdroje nebo rozdělovací jednotky (PDU) v racku s nepřerušitelným zdrojem napájení (UPS).
- Máte přístup k fyzickému zařízení.


### <a name="for-the-datacenter-network"></a>Síť datacentra

Než začnete, ujistěte se, že:

- Síť ve vašem datovém centru je nakonfigurovaná podle síťových požadavků pro vaše zařízení Data Box Edge. Další informace naleznete v tématu [Požadavky na systém Data Box Edge](data-box-edge-system-requirements.md).

- Pro běžné provozní podmínky vašeho data boxu Edge máte:

    - Minimálně 10 Mb/s šířka pásma pro stažení, aby zařízení zůstalo aktualizované.
    - Minimálně 20 Mb/s vyhrazená šířka pásma pro nahrávání a stahování pro přenos souborů.

## <a name="create-a-new-resource"></a>Vytvoření nového prostředku

Pokud už pro správu fyzického zařízení máte existující prostředek Data Box Edge, přeskočte tento krok a přejděte k části [Získání aktivačního klíče](#get-the-activation-key).

Chcete-li vytvořit prostředek Data Box Edge, postupujte na webu Azure Portal následujícím způsobem.

1. Přihlaste se pomocí přihlašovacích údajů k Microsoft Azure. 
    
    - Portál Azure na této [https://portal.azure.com](https://portal.azure.com)adrese URL: .
    - Nebo portál Azure Government na [https://portal.azure.us](https://portal.azure.us)této adrese URL: . Další podrobnosti najdete v části [Připojení k Azure Government pomocí portálu](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. V levém podokně vyberte **+ Vytvořit prostředek**. Hledat **data box edge / data box gateway**. Vyberte **Brána okraje datové schránky / brány datové schránky**. Vyberte **Vytvořit**.
3. Vyberte předplatné, které chcete použít pro zařízení Data Box Edge. Vyberte oblast, ve které chcete prostředek Data Box Edge nasadit. Seznam všech oblastí, kde je prostředek Azure Stack Edge k dispozici, najdete v [tématu produkty Azure dostupné podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

    Zvolte umístění, které je nejblíže zeměpisné oblasti, ve které chcete zařízení nasadit. Oblast ukládá pouze metadata pro správu zařízení. Skutečná data mohou být uložena v libovolném účtu úložiště.
    
    V možnosti **Okraj datového pole** vyberte **Vytvořit**.

    ![Vyhledání služby Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. Na kartě **Základy** zadejte nebo vyberte následující **podrobnosti o projektu**.
    
    |Nastavení  |Hodnota  |
    |---------|---------|
    |Předplatné    |To je automaticky naplněna na základě předchozího výběru. Předplatné je propojené s vaším fakturačním účtem. |
    |Skupina prostředků  |Vyberte existující skupinu nebo vytvořte novou.<br>Další informace o [skupinách prostředků Azure](../azure-resource-manager/management/overview.md).     |

4. Zadejte nebo vyberte následující **podrobnosti instance**.

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Name (Název)   | Popisný název pro identifikaci prostředku.<br>Název musí obsahovat 2 až 50 znaků a může obsahovat písmena, číslice a spojovníky.<br> Název musí začínat a končit na písmeno nebo číslici.        |
    |Region (Oblast)     |Seznam všech oblastí, kde je prostředek Azure Stack Edge k dispozici, najdete v [tématu produkty Azure dostupné podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Pokud používáte Azure Government, všechny oblasti státní správy jsou k dispozici, jak je znázorněno v [oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Zvolte umístění, které je nejblíže zeměpisné oblasti, ve které chcete zařízení nasadit.|

    ![Podrobnosti o projektu a instanci](media/data-box-edge-deploy-prep/data-box-edge-resource.png)

5. Vyberte **další: Dodací adresa**.

    - Pokud již zařízení máte, vyberte pole se seznamem, **protože mám zařízení Data Box Edge**.
    - Pokud se jedná o nové zařízení, které objednáváte, zadejte jméno kontaktu, společnost, adresu pro odeslání zařízení a kontaktní informace.

    ![Dodací adresa nového zařízení](media/data-box-edge-deploy-prep/data-box-edge-resource1.png)

6. Vyberte **další: Kontrola + vytvoření**.

7. Na kartě **Revize + vytvořit** zkontrolujte **podrobnosti o cenách**, **Podmínky použití**a podrobnosti o zdroji. Vyberte pole se seznamem, protože **jsem zkontroloval podmínky ochrany osobních údajů**.

    ![Kontrola podrobností o zdrojích datové schránky Edge a podmínek ochrany osobních údajů](media/data-box-edge-deploy-prep/data-box-edge-resource2.png)

8. Vyberte **Vytvořit**.

Vytvoření prostředku trvá několik minut. Po úspěšném vytvoření a nasazení prostředku budete upozorněni. Vyberte **Přejít na zdroj**.

![Přejít na prostředek Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-resource3.png)

Po odeslání objednávky společnost Microsoft objednávku zkontroluje a kontaktuje vás (e-mailem) s podrobnostmi o odeslání.

![Oznámení o kontrole objednávky Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>Získání aktivačního klíče

Po spuštění prostředku Data Box Edge budete muset získat aktivační klíč. Tento klíč slouží k aktivaci a propojení zařízení Data Box Edge s prostředkem. Tento klíč můžete získat teď, když jste na webu Azure Portal.

1. Vyberte prostředek, který jste vytvořili. Vyberte **Přehled** a potom vyberte **Nastavení zařízení**.

    ![Vybrat nastavení zařízení](media/data-box-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. Na dlaždici **Aktivovat** vyberte **Generovat klíč** a vytvořte aktivační klíč. Vyberte ikonu kopírování, chcete-li klíč zkopírovat, a uložte jej pro pozdější použití.

    ![Získání aktivačního klíče](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Platnost aktivačního klíče vyprší tři dny po jeho vygenerování.
> - Pokud platnost klíče vypršela, vygenerujte nový klíč. Starší klíč není platný.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených s Data Boxem Edge, jako jsou:

> [!div class="checklist"]
> * Vytvoření nového prostředku
> * Získání aktivačního klíče

Přejdete k dalšímu kurzu, kde se dozvíte, jak nainstalovat Data Box Edge.

> [!div class="nextstepaction"]
> [Instalace Data Boxu Edge](./data-box-edge-deploy-install.md)



