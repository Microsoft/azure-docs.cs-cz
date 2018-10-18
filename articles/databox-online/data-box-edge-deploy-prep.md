---
title: Kurz přípravy webu Azure Portal k nasazení Data Boxu Edge | Microsoft Docs
description: První kurz k nasazení Azure Data Boxu Edge se zabývá přípravou webu Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 35ac28d687c8bc6636a7d8e10f54ffb5b219a776
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167456"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge-preview"></a>Kurz: Příprava k nasazení Azure Data Boxu Edge (Preview)


Toto je první kurz série kurzů nasazení potřebných k dokončení nasazení Azure Data Boxu Edge. Tento kurz popisuje přípravu webu Azure Portal k nasazení prostředku Data Box Edge. 

K dokončení této instalace a procesu konfigurace potřebujete oprávnění správce. Příprava portálu trvá méně než 10 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření nového prostředku
> * Získání aktivačního klíče

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


> [!IMPORTANT]
> Data Box Edge je ve verzi Preview. Před objednáním a nasazením tohoto řešení si přečtěte [podmínky užívání pro předběžné verze systému Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

### <a name="get-started"></a>Začínáme

Pokud chcete nasadit Data Box Edge, postupujte podle následujících kurzů v uvedeném pořadí.

| **#** | **V tomto kroku** | **Použijte tyto dokumenty** |
| --- | --- | --- | 
| 1. |**[Příprava webu Azure Portal pro Data Box Edge](data-box-edge-deploy-prep.md)** |Před instalací fyzického zařízení Data Box Edge vytvořte a nakonfigurujte prostředek Data Box Edge. |
| 2. |**[Instalace Data Boxu Edge](data-box-edge-deploy-install.md)**|Rozbalte fyzické zařízení Data Box Edge, usaďte ho do racku a zapojte kabeláž.  |
| 3. |**[Připojení, nastavení a aktivace Data Boxu Edge](data-box-edge-deploy-connect-setup-activate.md)** |Připojte se k místnímu webovému uživatelskému rozhraní, dokončete nastavení zařízení a aktivujte zařízení. Zařízení je připravené na nastavení sdílených složek SMB nebo sdílených složek systému souborů NFS.  |
| 4. |**[Přenos dat pomocí Data Boxu Edge](data-box-edge-deploy-add-shares.md)** |Přidejte sdílené složky a připojte se k nim pomocí protokolu SMB nebo přes systém souborů NFS. |
| 5. |**[Transformace dat pomocí Data Boxu Edge](data-box-edge-deploy-configure-compute.md)** |Nakonfigurujte na zařízení moduly Edge umožňující transformaci dat během přenosu do Azure. |

Teď můžete začít nastavovat Azure Portal.

## <a name="prerequisites"></a>Požadavky

Tady najdete požadavky na konfiguraci prostředku Data Box Edge, zařízení Data Box Edge a sítě datacentra.

### <a name="for-the-data-box-edge-resource"></a>Prostředek Data Box Edge

Než začnete, ujistěte se, že:

* Ve vašem předplatném Microsoft Azure je povolený prostředek Data Box Edge.
* Máte účet služby Microsoft Azure Storage a přihlašovací údaje účtu.

### <a name="for-the-data-box-edge-device"></a>Zařízení Data Box Edge

Před nasazením fyzického zařízení se ujistěte, že:

- Ve standardním 19palcovém racku ve vašem datacentru máte k dispozici slot 1U pro usazení zařízení. 
- Máte přístup k rovnému a stabilnímu pracovnímu povrchu, kam můžete zařízení bezpečně umístit.
- Lokalita, ve které chcete zařízení nastavit, disponuje nezávislým zdrojem standardního střídavého proudu nebo rozvodnou energetickou jednotkou racku s nepřerušitelným zdrojem napájení (UPS).
- Máte přístup k fyzickému zařízení.


### <a name="for-the-datacenter-network"></a>Síť datacentra

Než začnete, ujistěte se, že:

* Síť ve vašem datacentru je nakonfigurovaná podle požadavků na síť pro vaše zařízení Data Box Edge. Další informace najdete v tématu [Systémové požadavky na Data Box Edge](data-box-gateway-system-requirements.md).

* Váš Data Box Edge má vyhrazenou šířku internetového pásma 20 Mb/s (nebo více), která je vždy dostupná. Tato šířka pásma by se neměla sdílet s žádnými jinými aplikacemi. Pokud používáte omezení využití sítě, doporučujeme použít šířku internetového pásma 32 Mb/s nebo více, aby omezování fungovalo.

## <a name="create-a-new-resource"></a>Vytvoření nového prostředku

Pomocí následujícího postupu vytvořte nový prostředek Data Box Edge. 

Pokud už pro správu fyzického zařízení máte existující prostředek Data Box Edge, přeskočte tento krok a přejděte k části [Získání aktivačního klíče](#get-the-activation-key).

Provedením následujících kroků na webu Azure Portal vytvořte prostředek Data Box.

1. Pomocí svých přihlašovacích údajů Microsoft Azure se přihlaste k portálu Azure Preview na této adrese URL: [https://aka.ms/databox-edge](https://aka.ms/databox-edge). 

2. Vyberte předplatné, které chcete použít pro Data Box Edge ve verzi Preview. Vyberte oblast, ve které chcete prostředek Data Box Edge nasadit. U možnosti **Data Box Edge** klikněte na **Vytvořit**.

    ![Vyhledání služby Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. Zadejte nebo vyberte pro nový prostředek následující informace.
    
    |Nastavení  |Hodnota  |
    |---------|---------|
    |Název prostředku   | Popisný název pro identifikaci prostředku.<br>Název prostředku musí obsahovat 2 až 50 znaků a může obsahovat písmena, číslice a spojovníky.<br> Název musí začínat a končit na písmeno nebo číslici.        |
    |Předplatné    |Předplatné je propojené s vaším fakturačním účtem. |
    |Skupina prostředků  |Vyberte existující skupinu nebo vytvořte novou.<br>Další informace o [skupinách prostředků Azure](../azure-resource-manager/resource-group-overview.md).     |
    |Umístění     |Pro tuto verzi jsou dostupné oblasti USA – východ, USA – západ 2, Jihovýchodní Asie a Západní Evropa. <br> Zvolte umístění, které je nejblíže zeměpisné oblasti, ve které chcete zařízení nasadit.|
    
    ![Vytvoření prostředku Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-resource.png)
    
4. Klikněte na **OK**.
 
Vytvoření prostředku trvá několik minut. Po úspěšném vytvoření prostředku obdržíte odpovídající oznámení.


## <a name="get-the-activation-key"></a>Získání aktivačního klíče

Po zprovoznění prostředku Data Box Edge bude potřeba získat aktivační klíč. Tento klíč slouží k aktivaci a propojení zařízení Data Box Edge s prostředkem. Tento klíč můžete získat teď, když jste na webu Azure Portal.

1. Klikněte na prostředek, který jste vytvořili, a pak klikněte na **Přehled**.

2. Kliknutím na **Vygenerovat klíč** vytvořte aktivační klíč. Kliknutím na ikonu kopírování klíč zkopírujte a uložte ho pro pozdější použití.

    ![Získání aktivačního klíče](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Platnost aktivačního klíče vyprší 3 dny po vygenerování. 
> - Pokud platnost klíče vyprší, vygenerujte nový klíč. Starší klíč není platný.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených s Data Boxem Edge, jako jsou:

> [!div class="checklist"]
> * Vytvoření nového prostředku
> * Získání aktivačního klíče

V dalším kurzu se dozvíte, jak Data Box Edge nainstalovat. 

> [!div class="nextstepaction"]
> [Instalace Data Boxu Edge](./data-box-edge-deploy-install.md)



