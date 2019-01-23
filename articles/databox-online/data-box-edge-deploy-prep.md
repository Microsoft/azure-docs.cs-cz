---
title: Kurz přípravy webu Azure Portal k nasazení Data Boxu Edge | Microsoft Docs
description: První kurz o Azure Data Box Edge nasazení spočívá v přípravě na webu Azure portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 7764b0ceee1b540e9650d232b7087811d7376f28
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452084"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>Kurz: Příprava na nasazení Azure Data Box Edge  


Toto je první kurz v této sérii kurzů nasazení, které jsou nutné k úplné nasazení Azure Data Box Edge. Tento kurz popisuje, jak připravit na webu Azure portal k nasazení hrany pole datového prostředku. 

K dokončení této instalace a procesu konfigurace potřebujete oprávnění správce. Příprava portálu trvá méně než 10 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření nového prostředku
> * Získání aktivačního klíče

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


> [!IMPORTANT]
> Data Box Edge je ve verzi Preview. Než pořadí a nasazení tohohle řešení, projděte si [podmínkami pro verzi preview služby Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).  

### <a name="get-started"></a>Začínáme

Nasazení hrany pole dat, naleznete v následujících kurzech v předepsaném pořadí.

| **#** | **V tomto kroku** | **Použijte tyto dokumenty** |
| --- | --- | --- | 
| 1. |**[Příprava webu Azure Portal pro Data Box Edge](data-box-edge-deploy-prep.md)** |Před instalací fyzického zařízení Data Box Edge vytvořte a nakonfigurujte prostředek Data Box Edge. |
| 2. |**[Instalace Data Boxu Edge](data-box-edge-deploy-install.md)**|Rozbalte fyzické zařízení Data Box Edge, usaďte ho do racku a zapojte kabeláž.  |
| 3. |**[Připojení, nastavení a aktivovat okraj pole dat](data-box-edge-deploy-connect-setup-activate.md)** |Připojte se k místnímu webovému uživatelskému rozhraní, dokončete nastavení zařízení a aktivujte zařízení. Zařízení je připravené na nastavení sdílených složek SMB nebo sdílených složek systému souborů NFS.  |
| 4. |**[Přenos dat pomocí Data Boxu Edge](data-box-edge-deploy-add-shares.md)** |Přidejte sdílené složky a připojte se k nim pomocí protokolu SMB nebo přes systém souborů NFS. |
| 5. |**[Transformace dat pomocí Data Boxu Edge](data-box-edge-deploy-configure-compute.md)** |Nakonfigurujte na zařízení moduly Edge umožňující transformaci dat během přenosu do Azure. |

Teď můžete začít nastavovat Azure Portal.

## <a name="prerequisites"></a>Požadavky

Toto jsou požadavky na konfiguraci pro váš prostředek okraj pole Data, zařízení Data Box Edge a síti datového centra.

### <a name="for-the-data-box-edge-resource"></a>Prostředek Data Box Edge

Než začnete, ujistěte se, že:

* Vaše předplatné Microsoft Azure je povoleno pro zdroj dat pole Edge.
* Máte účet služby Microsoft Azure Storage a přihlašovací údaje účtu.

### <a name="for-the-data-box-edge-device"></a>Zařízení Data Box Edge

Před nasazením fyzického zařízení se ujistěte, že:
- Máte k dispozici ve standardní 19" rack 1 U slot ve vašem datovém centru pro připojení zařízení do racku. 
- Máte přístup bez stromové struktury, stabilní a úrovni pracovní plochu, ve kterém můžete zařízení bezpečně rest.
- Lokality, ve kterém chcete nastavit zařízení má standardní napájení z nezávislých zdroje nebo jednotka distribuci napájení (PDU) rack s nepřerušitelný zdroj napájení (UPS).
- Máte přístup k fyzickému zařízení.


### <a name="for-the-datacenter-network"></a>Síť datacentra

Než začnete, ujistěte se, že:

* Sítě ve vašem datovém centru se konfiguruje na základě požadavků na síť pro vaše zařízení Data Box Edge. Další informace najdete v tématu [požadavky na systém pro Data Box Edge](data-box-gateway-system-requirements.md).

* Data Box Edge má vyhrazené šířky pásma Internetu 20 MB/s (nebo více) vždy k dispozici. Tuto šířku pásma by neměly sdílet s jinými aplikacemi. Pokud používáte omezení sítě, pak pro omezení pro práci, doporučujeme použít Internet 32 MB/s šířky pásma nebo více.

## <a name="create-a-new-resource"></a>Vytvoření nového prostředku

Pomocí následujícího postupu vytvořte nový prostředek Data Box Edge. 

Pokud už pro správu fyzického zařízení máte existující prostředek Data Box Edge, přeskočte tento krok a přejděte k části [Získání aktivačního klíče](#get-the-activation-key).

Vytvořit prostředek služby Data Box Edge, proveďte následující kroky na webu Azure Portal.

1. Použijte svoje přihlašovací údaje Microsoft Azure pro přihlášení k portálu Azure preview na této adrese URL: [ https://aka.ms/databox-edge ](https://aka.ms/databox-edge). 

2. Vyberte předplatné, které chcete použít pro Data Box Edge preview. Vyberte oblast, ve které chcete prostředek Data Box Edge nasadit. V **hrany pole Data** možnosti, vyberte **vytvořit**.

    ![Vyhledání služby Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. Zadejte nebo vyberte pro nový prostředek následující informace.
    
    |Nastavení  |Hodnota  |
    |---------|---------|
    |Název prostředku   | Popisný název, pomocí kterého se má identifikovat prostředek.<br>Název prostředku je dlouhý 2 až 50 znaků obsahující písmena, číslice a pomlčky.<br> Název musí začínat a končit na písmeno nebo číslici.        |
    |Předplatné    |Předplatné je propojené s vaším fakturačním účtem. |
    |Skupina prostředků  |Vyberte existující skupinu nebo vytvořte novou.<br>Další informace o [skupin prostředků Azure](../azure-resource-manager/resource-group-overview.md).     |
    |Umístění     |Pro tuto verzi jsou dostupné oblasti USA – východ, Západní USA 2, Jihovýchodní Asie a Západní Evropa. <br> Zvolte umístění, které je nejblíže zeměpisné oblasti, ve které chcete zařízení nasadit.|
    
    ![Vytvořit prostředek okraj pole dat](media/data-box-edge-deploy-prep/data-box-edge-resource.png)
    
4. Vyberte **OK**.
 
Vytvoření prostředku trvá několik minut. Po úspěšném vytvoření prostředku, budete upozorněni na odpovídajícím způsobem.


## <a name="get-the-activation-key"></a>Získání aktivačního klíče

Po zprovoznění prostředků okraj pole dat se budete muset získat aktivační klíč. Tento klíč slouží k aktivaci a propojení zařízení Data Box Edge s prostředkem. Tento klíč můžete získat teď, když jste na webu Azure Portal.

1. Vyberte prostředek, který jste vytvořili a pak vyberte **přehled**.

2. Vyberte **vygenerovat klíč** vytvořit aktivační kód. Vyberte ikonu kopírování zkopírujte klíč a uložit pro pozdější použití.

    ![Získání aktivačního klíče](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Vypršení platnosti tohoto klíče aktivace tři dny po jeho vygenerování. 
> - Pokud vypršela platnost klíče, vygenerujte nový klíč. Starší klíč není platný.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o tématech spojených s Data Boxem Edge, jako jsou:

> [!div class="checklist"]
> * Vytváří se nový prostředek
> * Načtení aktivační kód

Přejděte k dalšímu kurzu se naučíte, nainstalovat Data Box Edge. 

> [!div class="nextstepaction"]
> [Nainstalovat Data Box Edge](./data-box-edge-deploy-install.md)



