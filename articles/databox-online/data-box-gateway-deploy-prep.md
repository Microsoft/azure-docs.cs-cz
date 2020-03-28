---
title: Kurz přípravy webu Azure Portal k nasazení služby Data Box Gateway | Microsoft Docs
description: První kurz k nasazení služby Azure Data Box Gateway se zabývá přípravou webu Azure Portal.
services: databox-edge-gateway
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 74fec059bdffb91f5a7774d430e2f1897f0e863c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79474454"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>Kurz: Příprava na nasazení brány datové schránky Azure

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
| 2. |**[Zřízení služby Data Box Gateway v Hyper-V](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[Zřízení služby Data Box Gateway ve VMware](data-box-gateway-deploy-provision-vmware.md)**|V případě Hyper-V zřiďte virtuální zařízení Data Box Gateway a připojte se k němu v hostitelském systému s Hyper-V ve Windows Serveru 2016 nebo Windows Serveru 2012 R2. <br><br><br> V případě společnosti VMware zřizujte a připojujte se k virtuálnímu zařízení Brány datových schronů v hostitelském systému se systémem VMware ESXi 6.0, 6.5 nebo 6.7.<br></br> |
| 3. |**[Připojení, nastavení a aktivace služby Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)** |Připojte se k místnímu webovému uživatelskému rozhraní, dokončete nastavení zařízení a aktivujte zařízení. Potom můžete zřídit sdílené složky SMB.  |
| 4. |**[Přenos dat pomocí služby Data Box Gateway](data-box-gateway-deploy-add-shares.md)** |Přidejte sdílené složky a připojte se k nim pomocí protokolu SMB nebo přes systém souborů NFS. |

Teď můžete začít nastavovat Azure Portal.

## <a name="prerequisites"></a>Požadavky

Tady najdete požadavky na konfiguraci prostředku Data Box Gateway, zařízení Data Box Gateway a sítě datacentra.

### <a name="for-the-data-box-gateway-resource"></a>Prostředek Data Box Gateway

Než začnete, ujistěte se, že:

* Vaše předplatné Microsoft Azure je povolené pro prostředek Azure Stack Edge. Ujistěte se, že jste použili podporované předplatné, jako je [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/), [Poskytovatel cloudových řešení (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)nebo [Sponzorství Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Máte přístup vlastníka nebo přispěvatele na úrovni skupiny prostředků pro brány Data Box Edge/Data Box Gateway, IoT Hub a prostředky Úložiště Azure.
    - Chcete-li vytvořit jakýkoli prostředek Data Box Edge/ Data Box Gateway, měli byste mít oprávnění jako přispěvatel (nebo vyšší) s vymezeným oborem na úrovni skupiny prostředků. Musíte se také ujistit, že `Microsoft.DataBoxEdge` je registrován zprostředkovatel. Informace o registraci naleznete v seznamu [Registrovat zprostředkovatele prostředků](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Chcete-li vytvořit prostředek účtu úložiště, budete opět potřebovat přispěvatele nebo vyšší přístup s rozsahem na úrovni skupiny prostředků. Azure Storage je ve výchozím nastavení registrovaný poskytovatel prostředků.
- Máte přístup správce nebo uživatele k rozhraní Microsoft Graph API. Další informace naleznete v [tématu Odkaz na oprávnění aplikace Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference).
- Máte účet služby Microsoft Azure Storage a přihlašovací údaje účtu.

### <a name="for-the-data-box-gateway-device"></a>Zařízení Data Box Gateway

Před nasazením virtuálního zařízení se ujistěte, že:

- Máte přístup k hostitelskému systému se systémem Hyper-V v systému Windows Server 2012 R2 nebo novějším nebo společnosti VMware (ESXi 6.0, 6.5 nebo 6.7), který lze použít k zřízení zařízení.
- Hostitelský systém může pro zřízení virtuálního zařízení Data Box vyhradit následující prostředky:
  
  - Minimálně 4 virtuální procesory.
  - Minimálně 8 GB paměti RAM
  - Jedno síťové rozhraní
  - 250GB disk s operačním systémem
  - 2TB virtuální disk pro data systému

### <a name="for-the-datacenter-network"></a>Síť datacentra

Než začnete, ujistěte se, že:

- Síť ve vašem datacentru je nakonfigurovaná podle požadavků na síť pro vaše zařízení Data Box Gateway. Další informace naleznete v [požadavcích na systém Brány datových schronů](data-box-gateway-system-requirements.md).

- Pro běžné provozní podmínky brány datové schránky byste měli mít:

    - Minimálně 10 Mb/s šířka pásma pro stažení, aby zařízení zůstalo aktualizované.
    - Minimálně 20 Mb/s vyhrazená šířka pásma pro nahrávání a stahování pro přenos souborů.

## <a name="create-a-new-resource"></a>Vytvoření nového prostředku

Pokud už pro správu vašich virtuálních zařízení máte existující prostředek Data Box Gateway, přeskočte tento krok a přejděte k části [Získání aktivačního klíče](#get-the-activation-key).

Pokud chcete vytvořit prostředek brány datové schránky, postupujte na webu Azure Portal následujícím způsobem.

1. Pomocí přihlašovacích údajů k Microsoft Azure se můžete přihlásit k:

    - Portál Azure na této [https://portal.azure.com](https://portal.azure.com)adrese URL: .
    - Nebo portál Azure Government na [https://portal.azure.us](https://portal.azure.us)této adrese URL: . Další podrobnosti najdete v části [Připojení k Azure Government pomocí portálu](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. V levém podokně vyberte **+ Vytvořit prostředek**. Hledat **data box edge / data box gateway**. Vyberte Brána okraje datové schránky / brány datové schránky. Vyberte **Vytvořit**.
3. Vyberte předplatné, které chcete použít pro zařízení Brány datové schránky. Vyberte oblast, do které chcete nasadit prostředek brány datové schránky. Seznam všech oblastí, kde je prostředek Azure Stack Edge k dispozici, najdete v [tématu produkty Azure dostupné podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Zvolte umístění, které je nejblíže zeměpisné oblasti, ve které chcete zařízení nasadit. V možnosti **Brána datového pole** vyberte **Vytvořit**.

    ![Vyhledání služby Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

4. Na kartě **Základy** zadejte nebo vyberte následující **podrobnosti o projektu**.
    
    |Nastavení  |Hodnota  |
    |---------|---------|
    |Předplatné    |To je automaticky naplněna na základě předchozího výběru. Předplatné je propojené s vaším fakturačním účtem. |
    |Skupina prostředků  |Vyberte existující skupinu nebo vytvořte novou.<br>Další informace o [skupinách prostředků Azure](../azure-resource-manager/management/overview.md).     |

5. Zadejte nebo vyberte následující **podrobnosti instance**.

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Name (Název)   | Popisný název pro identifikaci prostředku.<br>Název musí obsahovat 2 až 50 znaků a může obsahovat písmena, číslice a spojovníky.<br> Název musí začínat a končit na písmeno nebo číslici.        |   
    |Region (Oblast)     |Seznam všech oblastí, kde je prostředek Azure Stack Edge k dispozici, najdete v [tématu produkty Azure dostupné podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Pro Azure Government jsou k dispozici všechny oblasti správy uvedené v [oblastech Azure.](https://azure.microsoft.com/global-infrastructure/regions/) <br> Zvolte umístění, které je nejblíže zeměpisné oblasti, ve které chcete zařízení nasadit.|
    
    ![Vytvoření prostředku Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
6. Vyberte **Zkontrolovat a vytvořit**.
 
7. Na kartě **Revize + vytvořit** zkontrolujte **podrobnosti o cenách**, **Podmínky použití**a podrobnosti o zdroji. Vyberte **Vytvořit**.

    ![Zkontrolovat podrobnosti o prostředku brány datové schránky](media/data-box-gateway-deploy-prep/data-box-gateway-resource1.png)

Vytvoření prostředku trvá několik minut. Po úspěšném vytvoření a nasazení prostředku budete upozorněni. Vyberte **Přejít na zdroj**.

![Zkontrolovat podrobnosti o prostředku brány datové schránky](media/data-box-gateway-deploy-prep/data-box-gateway-resource2.png)

## <a name="download-the-virtual-device-image"></a>Stažení image virtuálního zařízení

Po vytvoření prostředku Data Box Gateway si stáhněte odpovídající image virtuálního zařízení, pomocí které můžete ve svém hostitelském systému zřídit virtuální zařízení. Image virtuálních zařízení jsou specifické pro operační systém.

> [!IMPORTANT]
> Software ve službě Data Box Gateway je možné používat pouze s prostředkem Data Box Gateway.

Podle těchto kroků na [webu Azure Portal](https://portal.azure.com/) stáhněte image virtuálního zařízení.

1. Ve zdroji, který jste vytvořili, vyberte **možnost Přehled**. Pokud máte existující prostředek brány datové schránky Azure, vyberte prostředek a přejděte na **Přehled**. Vyberte **nastavení zařízení**.

    ![Nový prostředek Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. Na dlaždici **Stáhnout obrázek** vyberte bitovou kopii virtuálního zařízení odpovídající operačnímu systému na hostitelském serveru, který slouží ke zřízení virtuálního počítače. Obrazové soubory jsou přibližně 5,6 GB.
   
   * [VHDX pro Hyper-V ve Windows Serveru 2012 R2 nebo novějším](https://aka.ms/dbe-vhdx-2012)
   * [VMDK pro VMWare ESXi 6.0, 6.5 nebo 6.7](https://aka.ms/dbe-vmdk).

    ![Stáhnout obrázek virtuálního zařízení Brány datové schránky](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. Stáhněte soubor na místní disk, rozbalte ho a poznamenejte si umístění rozbaleného souboru.


## <a name="get-the-activation-key"></a>Získání aktivačního klíče

Po spuštění prostředku brány datové schránky budete muset získat aktivační klíč. Tento klíč slouží k aktivaci a propojení zařízení Data Box Gateway s prostředkem. Tento klíč můžete získat teď, když jste na webu Azure Portal.

1. Vyberte zdroj, který jste vytvořili, a pak vyberte **Přehled**. V **nastavení zařízení**přejděte na dlaždici **Konfigurovat a aktivovat.**

    ![Konfigurace a aktivace dlaždic](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. Chcete-li vytvořit aktivační klíč, vyberte **generovat klíč.** Vyberte ikonu kopírování, chcete-li klíč zkopírovat, a uložte jej pro pozdější použití.

    ![Získání aktivačního klíče](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Platnost aktivačního klíče vyprší tři dny po jeho vygenerování.
> - Pokud platnost klíče vypršela, vygenerujte nový klíč. Starší klíč není platný.

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


