---
title: Kurz přípravy webu Azure Portal k nasazení služby Data Box Gateway | Microsoft Docs
description: První kurz k nasazení služby Azure Data Box Gateway se zabývá přípravou webu Azure Portal.
services: databox-edge-gateway
author: v-dalc
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: f1b00273314c845a36c7efdb9cf9f4de9d5cdd46
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96583383"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>Kurz: Příprava na nasazení Azure Data Box Gateway

Toto je první kurz série kurzů nasazení potřebných k dokončení nasazení služby Azure Data Box Gateway. Tento kurz popisuje přípravu webu Azure Portal k nasazení prostředku Data Box Gateway.

K dokončení této instalace a procesu konfigurace potřebujete oprávnění správce. Příprava portálu trvá méně než 10 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvoření nového prostředku
> * Stažení image virtuálního zařízení
> * Získání aktivačního klíče

## <a name="get-started"></a>Začínáme

Pokud chcete nasadit službu Data Box Gateway, postupujte podle následujících kurzů v uvedeném pořadí.

| **#** | **V tomto kroku** | **Použijte tyto dokumenty** |
| --- | --- | --- | 
| 1. |**[Příprava webu Azure Portal pro službu Data Box Gateway](data-box-gateway-deploy-prep.md)** |Před zřízením virtuálního zařízení Data Box Gateway vytvořte a nakonfigurujte prostředek Data Box Gateway. |
| 2. |**[Zřízení služby Data Box Gateway v Hyper-V](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[Zřízení služby Data Box Gateway ve VMware](data-box-gateway-deploy-provision-vmware.md)**|V případě Hyper-V zřiďte virtuální zařízení Data Box Gateway a připojte se k němu v hostitelském systému s Hyper-V ve Windows Serveru 2016 nebo Windows Serveru 2012 R2. <br><br><br> Pro VMware zajistěte a připojte se k Data Box Gateway virtuálnímu zařízení v hostitelském systému, na kterém běží VMware ESXi 6,0, 6,5 nebo 6,7.<br></br> |
| 3. |**[Připojení, nastavení a aktivace služby Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)** |Připojte se k místnímu webovému uživatelskému rozhraní, dokončete nastavení zařízení a aktivujte zařízení. Potom můžete zřídit sdílené složky SMB.  |
| 4. |**[Přenos dat pomocí služby Data Box Gateway](data-box-gateway-deploy-add-shares.md)** |Přidejte sdílené složky a připojte se k nim pomocí protokolu SMB nebo přes systém souborů NFS. |

Teď můžete začít nastavovat Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Tady najdete požadavky na konfiguraci prostředku Data Box Gateway, zařízení Data Box Gateway a sítě datacentra.

### <a name="for-the-data-box-gateway-resource"></a>Prostředek Data Box Gateway

Než začnete, ujistěte se, že:

* Ve vašem předplatném Microsoft Azure je povolený prostředek Azure Stack Edge. Ujistěte se, že jste použili podporované předplatné, jako je například [Microsoft smlouva Enterprise (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)nebo [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Máte přístup vlastníka nebo přispěvatele na úrovni skupiny prostředků pro Azure Stack prostředky hraniční/Data Box Gateway, IoT Hub a Azure Storage.
    - Pokud chcete vytvořit Azure Stack hraničního nebo Data Box Gatewayho prostředku, měli byste mít oprávnění jako Přispěvatel (nebo vyšší) vymezené na úrovni skupiny prostředků. Musíte se také ujistit, že `Microsoft.DataBoxEdge` je poskytovatel zaregistrován. Informace o registraci najdete v tématu [Registrace poskytovatele prostředků](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Pokud chcete vytvořit prostředek účtu úložiště, musíte pro něj znovu použít rozsah přístupu přispěvatele nebo vyšší úrovně. Azure Storage je ve výchozím nastavení registrovaný poskytovatel prostředků.
- K rozhraní Microsoft Graph API máte přístup pro správce nebo uživatele. Další informace najdete v tématu informace o [Microsoft Graph oprávnění](https://docs.microsoft.com/graph/permissions-reference).
- Máte účet služby Microsoft Azure Storage a přihlašovací údaje účtu.

### <a name="for-the-data-box-gateway-device"></a>Zařízení Data Box Gateway

Před nasazením virtuálního zařízení se ujistěte, že:

- Máte přístup k hostitelskému systému, na kterém běží Hyper-V na Windows Serveru 2012 R2 nebo novějším, nebo VMware (ESXi 6,0, 6,5 nebo 6,7), který se dá použít k zřízení zařízení.
- Hostitelský systém může pro zřízení virtuálního zařízení Data Box vyhradit následující prostředky:
  
  - Minimálně 4 virtuální procesory.
  - Minimálně 8 GB paměti RAM
  - Jedno síťové rozhraní
  - 250GB disk s operačním systémem
  - 2TB virtuální disk pro data systému

### <a name="for-the-datacenter-network"></a>Síť datacentra

Než začnete, ujistěte se, že:

- Síť ve vašem datacentru je nakonfigurovaná podle požadavků na síť pro vaše zařízení Data Box Gateway. Další informace najdete v tématu [požadavky na systém data box Gateway](data-box-gateway-system-requirements.md).

- Pro normální provozní podmínky Data Box Gateway byste měli mít:

    - Minimální šířka pásma ke stažení, která zajistí, že se zařízení bude aktualizovat, musí mít minimálně 10 MB/s.
    - Minimálně 20 MB/s vyhrazeným nahráváním a šířkou pásma pro přenos souborů.

## <a name="create-a-new-resource"></a>Vytvoření nového prostředku

Pokud už pro správu vašich virtuálních zařízení máte existující prostředek Data Box Gateway, přeskočte tento krok a přejděte k části [Získání aktivačního klíče](#get-the-activation-key).

Chcete-li vytvořit prostředek Data Box Gateway, proveďte následující kroky v Azure Portal.

1. Pomocí přihlašovacích údajů pro Microsoft Azure se přihlaste k některému z těchto portálů:

    - Azure Portal na této adrese URL: [https://portal.azure.com](https://portal.azure.com) .
    - Portál Azure Government na této adrese URL: [https://portal.azure.us](https://portal.azure.us) . Podrobnosti najdete [v části připojení k Azure Government pomocí portálu](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).
    
2. Vyberte **+ Vytvořit prostředek**.

    ![Tlačítko pro vytvoření prostředku Azure Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-create-a-resource.png)

3. Do vyhledávacího pole zadejte **data box Gateway** a stiskněte klávesu ENTER.

    ![Hledání služby Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-search-box.png)

    Pak vyberte **Azure Data box Gateway**.

    ![Vyberte Data Box Gateway SKU.](media/data-box-gateway-deploy-prep/data-box-gateway-sku.png)

4. Vyberte **Vytvořit**.

    ![Kliknutím na vytvořit vytvořte prostředek Data Box Gateway.](media/data-box-gateway-deploy-prep/data-box-gateway-create.png)

5. Vyberte kartu **Základy**:

    Zadejte nebo vyberte následující **Podrobnosti o projektu**.
    
    |Nastavení  |Hodnota  |
    |---------|---------|
    |Předplatné    |Vyberte předplatné, které chcete použít pro zařízení Data Box Gateway. Předplatné je propojeno s vaším fakturačním účtem.|
    |Skupina prostředků  |Vyberte existující skupinu nebo vytvořte novou.<br>Další informace o [skupinách prostředků Azure](../azure-resource-manager/management/overview.md).|

   Zadejte nebo vyberte následující **Podrobnosti o instanci**.

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Název   |Popisný název pro identifikaci prostředku.<br>Název obsahuje 2 až 50 znaků obsahujících písmena, číslice a spojovníky. <br> Název musí začínat a končit písmenem nebo číslicí. |
    |Oblast  |Vyberte oblast, do které chcete prostředek nasadit. Vyberte umístění blízko geografické oblasti, kde chcete zařízení nasadit. <br> Seznam všech oblastí, ve kterých jsou k dispozici datové Base brány/Azure Stack hraniční prostředky, najdete v tématu [Dostupné produkty Azure v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). <br> Pro Azure Government jsou k dispozici všechny oblasti státní správy uvedené v [oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions/) .|

   Pak vyberte **zkontrolovat + vytvořit** a zkontrolujte vaši objednávku.

   ![Položka podrobností projektu a instance pro Data Box Gateway pořadí](media/data-box-gateway-deploy-prep/data-box-gateway-basics.png)

7. Na kartě **Revize + vytvořit** si přečtěte **Podrobnosti o cenách**, **podmínky použití** a podrobnosti k vašemu prostředku. Vyberte **Vytvořit**.

    ![Podrobnosti o Data Box Gateway prostředku zobrazené pro kontrolu](media/data-box-gateway-deploy-prep/data-box-gateway-resource-review-create.png)

Vytvoření prostředku trvá několik minut. Po úspěšném vytvoření a nasazení prostředku budete upozorněni. Vyberte **Přejít k prostředku**.

![Dokončené pořadí Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-completed-order.png)

## <a name="download-the-virtual-device-image"></a>Stažení image virtuálního zařízení

Po vytvoření prostředku Data Box Gateway si stáhněte odpovídající image virtuálního zařízení, pomocí které můžete ve svém hostitelském systému zřídit virtuální zařízení. Image virtuálních zařízení jsou specifické pro operační systém.

> [!IMPORTANT]
> Software ve službě Data Box Gateway je možné používat pouze s prostředkem Data Box Gateway.

Pomocí těchto kroků v [Azure Portal](https://portal.azure.com/) Stáhněte image virtuálního zařízení.

1. V prostředku, který jste vytvořili, a pak vyberte **Přehled**. Pokud máte existující prostředek Azure Data Box Gateway, vyberte prostředek a pokračujte na **Přehled**. Vyberte **nastavení zařízení**.

    ![Nový prostředek Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. Na dlaždici **Stáhnout obrázek** vyberte image virtuálního zařízení, která odpovídá operačnímu systému na hostitelském serveru, který se používá ke zřízení virtuálního počítače. Soubory obrázků mají přibližně 5,6 GB.
   
   * [VHDX pro Hyper-V ve Windows Serveru 2012 R2 nebo novějším](https://aka.ms/dbe-vhdx-2012)
   * [VMDK pro VMware ESXi 6,0, 6,5 nebo 6,7](https://aka.ms/dbe-vmdk).

    ![Stáhnout Data Box Gateway image virtuálního zařízení](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. Stáhněte soubor na místní disk, rozbalte ho a poznamenejte si umístění rozbaleného souboru.


## <a name="get-the-activation-key"></a>Získání aktivačního klíče

Po zprovoznění prostředku Data Box Gateway a spuštění je potřeba získat aktivační klíč. Tento klíč slouží k aktivaci a propojení zařízení Data Box Gateway s prostředkem. Tento klíč můžete získat teď, když jste na webu Azure Portal.

1. Vyberte prostředek, který jste vytvořili, a pak vyberte **Přehled**. V **nastavení zařízení** přejít na dlaždici **Konfigurovat a aktivovat** .

    ![Dlaždice konfigurovat a aktivovat](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. Vyberte vytvořit **klíč** a vytvořte aktivační klíč. Vyberte ikonu kopírování a zkopírujte klíč a uložte ho pro pozdější použití.

    ![Získání aktivačního klíče](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Aktivační klíč vyprší tři dny po vygenerování.
> - Pokud vypršela platnost klíče, vygenerujte nový klíč. Starší klíč není platný.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených se službou Data Box Gateway, jako jsou:

> [!div class="checklist"]
> * Vytvoření nového prostředku
> * Stažení image virtuálního zařízení
> * Získání aktivačního klíče

V dalším kurzu se dozvíte, jak pro službu Data Box Gateway zřídit virtuální počítač. V závislosti na vašem hostitelském operačním systému najdete podrobné pokyny v jednom z následujících témat:

> [!div class="nextstepaction"]
> [Zřízení služby Data Box Gateway v Hyper-V](./data-box-gateway-deploy-provision-hyperv.md)

NEBO

> [!div class="nextstepaction"]
> [Zřízení služby Data Box Gateway ve VMware](./data-box-gateway-deploy-provision-vmware.md)


