---
title: Kurz pro přípravu Azure Portal prostředí Datacenter k nasazení Azure Data Box Edge | Microsoft Docs
description: První kurz o nasazení Azure Data Box Edge zahrnuje přípravu Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: d9778544fd9e20dc3244a37c644117d1cd505a96
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438673"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>Kurz: Příprava na nasazení Azure Data Box Edge  

Toto je první kurz v řadě kurzů nasazení, které jsou potřeba k úplnému nasazení Azure Data Box Edge. V tomto kurzu se dozvíte, jak připravit Azure Portal k nasazení Data Box Edge prostředků.

K dokončení této instalace a procesu konfigurace potřebujete oprávnění správce. Příprava portálu trvá méně než 10 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření nového prostředku
> * Získání aktivačního klíče

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

### <a name="get-started"></a>Začít

Pokud chcete nasadit Data Box Edge, přečtěte si následující kurzy v předepsané posloupnosti.

| **#** | **V tomto kroku** | **Použijte tyto dokumenty** |
| --- | --- | --- | 
| 1. |**[Příprava webu Azure Portal pro Data Box Edge](data-box-edge-deploy-prep.md)** |Před instalací fyzického zařízení Data Box Edge vytvořte a nakonfigurujte prostředek Data Box Edge. |
| 2. |**[Instalace Data Boxu Edge](data-box-edge-deploy-install.md)**|Rozbalte fyzické zařízení Data Box Edge, usaďte ho do racku a zapojte kabeláž.  |
| 3. |**[Připojení, nastavení a aktivace Data Box Edge](data-box-edge-deploy-connect-setup-activate.md)** |Připojte se k místnímu webovému uživatelskému rozhraní, dokončete nastavení zařízení a aktivujte zařízení. Zařízení je připravené na nastavení sdílených složek SMB nebo sdílených složek systému souborů NFS.  |
| 4. |**[Přenos dat pomocí Data Boxu Edge](data-box-edge-deploy-add-shares.md)** |Přidejte sdílené složky a připojte se k nim pomocí protokolu SMB nebo přes systém souborů NFS. |
| 5. |**[Transformace dat pomocí Data Boxu Edge](data-box-edge-deploy-configure-compute.md)** |Nakonfigurujte výpočetní moduly na zařízení, aby se data transformoval při přesunu do Azure. |

Teď můžete začít nastavovat Azure Portal.

## <a name="prerequisites"></a>Požadavky

Níže jsou uvedené požadavky na konfiguraci pro váš prostředek Data Box Edge, Data Box Edge zařízení a síť datacenter.

### <a name="for-the-data-box-edge-resource"></a>Prostředek Data Box Edge

Než začnete, ujistěte se, že:

- Vaše předplatné Microsoft Azure je povolené pro prostředek Data Box Edge. Předplatná s průběžnými platbami se nepodporují.
- Máte přístup vlastníka nebo přispěvatele na úrovni skupiny prostředků pro prostředky Data Box Edge/Data Box Gateway, IoT Hub a Azure Storage.

    - Chcete-li vytvořit libovolný prostředek Data Box Edge/Data Box Gateway, měli byste mít oprávnění jako Přispěvatel (nebo vyšší) v oboru na úrovni skupiny prostředků. Také je nutné zajistit, aby byl zprostředkovatel `Microsoft.DataBoxEdge` zaregistrován. Informace o tom, jak se zaregistrovat, najdete v tématu [registrace poskytovatele prostředků](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Pokud chcete vytvořit libovolný prostředek IoT Hub, ujistěte se, že je poskytovatel Microsoft. Devices zaregistrovaný. Informace o tom, jak se zaregistrovat, najdete v tématu [registrace poskytovatele prostředků](data-box-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Pokud chcete vytvořit prostředek účtu úložiště, musíte pro něj znovu použít rozsah přístupu přispěvatele nebo vyšší úrovně. Azure Storage je ve výchozím nastavení registrovaný poskytovatel prostředků.
- K Graph API Azure Active Directory máte oprávnění správce nebo uživatel. Další informace najdete v tématu [Azure Active Directory Graph API](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
- Máte účet služby Microsoft Azure Storage a přihlašovací údaje účtu.

### <a name="for-the-data-box-edge-device"></a>Zařízení Data Box Edge

Před nasazením fyzického zařízení se ujistěte, že:

- Zkontrolovali jste bezpečnostní údaje, které byly součástí balíčku dodávek.
- Máte slot 1U, který je ve vašem datacentru k dispozici ve standardu 19 "Rack pro připojení zařízení.
- Máte přístup k plochému, stabilnímu a pracovnímu pracovnímu prostoru, kde může zařízení bezpečně klidovat.
- Lokalita, ve které máte v úmyslu zařízení nastavit, má standardní sílu napájení z nezávislého zdroje nebo jednotky PDU (AC) (napájení) s nepřerušitelným zdrojem napájení (UPS).
- Máte přístup k fyzickému zařízení.


### <a name="for-the-datacenter-network"></a>Síť datacentra

Než začnete, ujistěte se, že:

- Síť v datacentru je nakonfigurovaná podle požadavků na síť pro vaše zařízení Data Box Edge. Další informace najdete v tématu [data box Edge systémových požadavků](data-box-edge-system-requirements.md).

- Pro normální provozní podmínky vašeho Data Box Edge máte následující:

    - Minimální šířka pásma ke stažení, která zajistí, že se zařízení bude aktualizovat, musí mít minimálně 10 MB/s.
    - Minimálně 20 MB/s vyhrazeným nahráváním a šířkou pásma pro přenos souborů.

## <a name="create-a-new-resource"></a>Vytvoření nového prostředku

Pokud už pro správu fyzického zařízení máte existující prostředek Data Box Edge, přeskočte tento krok a přejděte k části [Získání aktivačního klíče](#get-the-activation-key).

Chcete-li vytvořit prostředek Data Box Edge, proveďte následující kroky v Azure Portal.

1. Použití přihlašovacích údajů pro Microsoft Azure pro přihlášení k 
    
    - Azure Portal na této adrese URL: [https://portal.azure.com](https://portal.azure.com).
    - Nebo Azure Government portál na této adrese URL: [https://portal.azure.us](https://portal.azure.us). Další podrobnosti najdete na webu [připojení k Azure Government pomocí portálu](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. V levém podokně vyberte **+ vytvořit prostředek**. Vyhledejte **data box Edge/data box Gateway**. Vyberte **data box Edge/data box Gateway**. Vyberte **Vytvořit**.
3. Vyberte předplatné, které chcete použít pro Data Box Edge zařízení. Vyberte oblast, ve které chcete prostředek Data Box Edge nasadit. V této verzi jsou k dispozici Východní USA, Jižní Východní Asie a Západní Evropa. 

    Zvolte umístění, které je nejblíže zeměpisné oblasti, ve které chcete zařízení nasadit. V oblasti jsou uložena pouze metadata pro správu zařízení. Skutečná data můžou být uložená v jakémkoli účtu úložiště. 
    
    V možnosti **data box Edge** vyberte **vytvořit**.

    ![Vyhledání služby Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. Na kartě **základy** zadejte nebo vyberte následující **Podrobnosti o projektu**.
    
    |Nastavení  |Hodnota  |
    |---------|---------|
    |Předplatné    |Tato hodnota se vyplní automaticky na základě dřívějšího výběru. Předplatné je propojené s vaším fakturačním účtem. |
    |Skupina prostředků  |Vyberte existující skupinu nebo vytvořte novou.<br>Další informace o [skupinách prostředků Azure](../azure-resource-manager/management/overview.md).     |

4. Zadejte nebo vyberte následující **Podrobnosti o instanci**.

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Name (Název)   | Popisný název pro identifikaci prostředku.<br>Název musí obsahovat 2 až 50 znaků a může obsahovat písmena, číslice a spojovníky.<br> Název musí začínat a končit na písmeno nebo číslici.        |
    |Region (Oblast)     |V této verzi jsou k dispozici Východní USA, Jižní Východní Asie a Západní Evropa k nasazení prostředku. Pokud používáte Azure Government, jsou dostupné všechny oblasti státní správy, jak je znázorněno v [oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Zvolte umístění, které je nejblíže zeměpisné oblasti, ve které chcete zařízení nasadit.|

    ![Podrobnosti o projektu a instanci](media/data-box-edge-deploy-prep/data-box-edge-resource.png)

5. Vyberte **Další: adresa příjemce**.

    - Pokud už máte nějaké zařízení, vyberte pole se seznamem, **které má data box Edge zařízení**.
    - Pokud se jedná o nové zařízení, které objednáváte, zadejte jméno kontaktu, společnost, adresu pro odeslání zařízení a kontaktní údaje.

    ![Dodací adresa pro nové zařízení](media/data-box-edge-deploy-prep/data-box-edge-resource1.png)

6. Vyberte **Další: zkontrolovat + vytvořit**.

7. Na kartě **Revize + vytvořit** si přečtěte **Podrobnosti o cenách**, **podmínky použití**a podrobnosti k vašemu prostředku. Zaškrtněte pole se seznamem pro **uživatele, kteří si prošli podmínka ochrany osobních údajů**.

    ![Kontrola Data Box Edge podrobností prostředků a podmínek ochrany osobních údajů](media/data-box-edge-deploy-prep/data-box-edge-resource2.png)

8. Vyberte **Vytvořit**.

Vytvoření prostředku trvá několik minut. Po úspěšném vytvoření a nasazení prostředku budete upozorněni. Vyberte **Přejít k prostředku**.

![Přejít na prostředek Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-resource3.png)

Po uvedení objednávky Microsoft zkontroluje pořadí a zavede vám (prostřednictvím e-mailu) informace o expedici.

![Oznámení pro revizi Data Box Edgeho pořadí](media/data-box-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>Získání aktivačního klíče

Po zprovoznění prostředku Data Box Edge a spuštění je potřeba získat aktivační klíč. Tento klíč slouží k aktivaci a propojení zařízení Data Box Edge s prostředkem. Tento klíč můžete získat teď, když jste na webu Azure Portal.

1. Vyberte prostředek, který jste vytvořili. Vyberte **Přehled** a pak vyberte **nastavení zařízení**.

    ![Vybrat nastavení zařízení](media/data-box-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. Na dlaždici **aktivovat** vyberte **Generovat klíč** a vytvořte aktivační klíč. Vyberte ikonu kopírování a zkopírujte klíč a uložte ho pro pozdější použití.

    ![Získání aktivačního klíče](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Aktivační klíč vyprší tři dny po vygenerování.
> - Pokud vypršela platnost klíče, vygenerujte nový klíč. Starší klíč není platný.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených s Data Boxem Edge, jako jsou:

> [!div class="checklist"]
> * Vytvoření nového prostředku
> * Získání aktivačního klíče

Přejděte k dalšímu kurzu, kde se dozvíte, jak nainstalovat Data Box Edge.

> [!div class="nextstepaction"]
> [Nainstalovat Data Box Edge](./data-box-edge-deploy-install.md)



