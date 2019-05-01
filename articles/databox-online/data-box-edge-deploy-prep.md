---
title: Kurz Azure portal, prostředí datového centra pro nasazení Azure Data Box Edge Příprava | Dokumentace Microsoftu
description: První kurz o Azure Data Box Edge nasazení spočívá v přípravě na webu Azure portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: d7e66970db3397531c798bc37bf7c1f346e999bf
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924777"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-edge"></a>Kurz: Příprava na nasazení Azure Data Box Edge  


Toto je první kurz v této sérii kurzů nasazení, které jsou nutné k úplné nasazení Azure Data Box Edge. Tento kurz popisuje, jak připravit na webu Azure portal k nasazení hrany pole datového prostředku.

K dokončení této instalace a procesu konfigurace potřebujete oprávnění správce. Příprava portálu trvá méně než 10 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření nového prostředku
> * Získání aktivačního klíče

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


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

- Vaše předplatné Microsoft Azure je povoleno pro zdroj dat pole Edge. Předplatná s průběžnými platbami nejsou podporovány.
- Budete mít roli vlastníka nebo přispěvatele, přístup k vašemu předplatnému.
- Máte správce nebo uživatel přístup k Azure Active Directory Graph API. Další informace najdete v tématu [Azure Active Directory Graph API](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
- Máte účet služby Microsoft Azure Storage a přihlašovací údaje účtu.

### <a name="for-the-data-box-edge-device"></a>Zařízení Data Box Edge

Před nasazením fyzického zařízení se ujistěte, že:

- Můžete si bezpečnostní informace, která byla součástí dodávky balíčku.
- Máte k dispozici ve standardní 19" rack 1 U slot ve vašem datovém centru pro připojení zařízení do racku. 
- Máte přístup bez stromové struktury, stabilní a úrovni pracovní plochu, ve kterém můžete zařízení bezpečně rest.
- Lokality, ve kterém chcete nastavit zařízení má standardní napájení z nezávislých zdroje nebo jednotka distribuci napájení (PDU) rack s nepřerušitelný zdroj napájení (UPS).
- Máte přístup k fyzickému zařízení.


### <a name="for-the-datacenter-network"></a>Síť datacentra

Než začnete, ujistěte se, že:

- Sítě ve vašem datovém centru se konfiguruje na základě požadavků na síť pro vaše zařízení Data Box Edge. Další informace najdete v tématu [požadavky na systém pro Data Box Edge](data-box-edge-system-requirements.md).

- Normální provozní podmínky při okraje pole dat budete mít:

    - Stáhnout minimálně 10 MB/s šířky pásma zajišťující, že zařízení zůstane aktualizované.
    - Minimálně 20 MB/s vyhrazenou nahrávání a stahování šířky pásma pro přenos souborů.

## <a name="create-a-new-resource"></a>Vytvoření nového prostředku

Pokud už pro správu fyzického zařízení máte existující prostředek Data Box Edge, přeskočte tento krok a přejděte k části [Získání aktivačního klíče](#get-the-activation-key).

Vytvořit prostředek služby Data Box Edge, proveďte následující kroky na webu Azure Portal.

1. Přihlaste se k pomocí svých přihlašovacích údajů Microsoft Azure 
    
    - Na webu Azure portal na této adrese URL: [ https://portal.azure.com ](https://portal.azure.com).
    - Nebo na portálu Azure Government na této adrese URL: [ https://portal.azure.us ](https://portal.azure.us). Další podrobnosti najdete v části [připojit se k portálu Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. V levém podokně vyberte **+ vytvořit prostředek**. Vyhledejte **Data Box Edge / Data Box brány**. Vyberte **Data Box Edge / Data Box brány**. Vyberte **Vytvořit**.
3. Vyberte předplatné, které chcete použít pro zařízení Data Box Edge. Vyberte oblast, ve které chcete prostředek Data Box Edge nasadit. Tato verze východní USA, jihovýchodní Asie a západní Evropa jsou k dispozici. 

    Zvolte umístění, které je nejblíže zeměpisné oblasti, ve které chcete zařízení nasadit. Oblast ukládá pouze metadata pro správu zařízení. Skutečná data mohou být uloženy v jakékoli účtu úložiště. 
    
    V **hrany pole Data** možnosti, vyberte **vytvořit**.

    ![Vyhledání služby Data Box Edge](media/data-box-edge-deploy-prep/data-box-edge-sku.png)

3. Na **Základy** kartu, zadejte nebo vyberte následující **podrobnosti o projektu**.
    
    |Nastavení  |Hodnota  |
    |---------|---------|
    |Předplatné    |To se automaticky vyplní podle předchozí výběr. Předplatné je propojené s vaším fakturačním účtem. |
    |Skupina prostředků  |Vyberte existující skupinu nebo vytvořte novou.<br>Další informace o [skupinách prostředků Azure](../azure-resource-manager/resource-group-overview.md).     |

4. Zadejte nebo vyberte následující **Instance podrobnosti**.

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Název   | Popisný název pro identifikaci prostředku.<br>Název musí obsahovat 2 až 50 znaků a může obsahovat písmena, číslice a spojovníky.<br> Název musí začínat a končit na písmeno nebo číslici.        |
    |Oblast     |Pro tuto verzi východní USA, jihovýchodní Asie a západní Evropa jsou k dispozici pro nasazení vašich prostředků. Pokud používáte Azure Government, všechny oblasti státní správy jsou k dispozici, jak je znázorněno [oblastí Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Zvolte umístění, které je nejblíže zeměpisné oblasti, ve které chcete zařízení nasadit.|

    ![Podrobnosti o projektu a instance](media/data-box-edge-deploy-prep/data-box-edge-resource.png)

5. Vyberte **Další: Dodací adresa**.

    - Pokud už máte zařízení, vyberte pole se seznamem pro **mám zařízení Data Box Edge**.
    - Pokud je to nová zařízení, které jsou řazení, zadejte jméno kontaktní osoby, společnosti, adresa k odeslání zařízení a kontaktní údaje.

    ![Dodací adresu pro nové zařízení](media/data-box-edge-deploy-prep/data-box-edge-resource1.png)

6. Vyberte **Další: Zkontrolovat a vytvořit**.

7. Na **zkontrolujte + vytvořit** kartu, zkontrolujte **podrobnosti o cenách**, **podmínky použití**a podrobnosti pro váš prostředek. Vyberte pole se seznamem pro **zkontrolovali jste podmínky ochrany osobních údajů**.

    ![Projděte si podrobnosti o prostředku okraj pole Data a zásady ochrany osobních údajů](media/data-box-edge-deploy-prep/data-box-edge-resource2.png)

8. Vyberte **Vytvořit**.

Vytvoření prostředku trvá několik minut. Po prostředek se úspěšně vytvoří a nasadí, budete upozorněni na to. Vyberte **přejít k prostředku**.

![Přejít k prostředku datového pole Edge](media/data-box-edge-deploy-prep/data-box-edge-resource3.png)

Jakmile objednávka, Microsoft zkontroluje pořadí a kontaktuje (prostřednictvím e-mailu) se podrobnosti o dopravě.

![Oznámení ke kontrole okraj pole Data objednávky](media/data-box-edge-deploy-prep/data-box-edge-resource4.png)

## <a name="get-the-activation-key"></a>Získání aktivačního klíče

Po zprovoznění prostředků okraj pole dat se budete muset získat aktivační klíč. Tento klíč slouží k aktivaci a propojení zařízení Data Box Edge s prostředkem. Tento klíč můžete získat teď, když jste na webu Azure Portal.

1. Vyberte prostředek, který jste vytvořili. Vyberte **přehled** a pak vyberte **nastavení zařízení**.

    ![Vyberte nastavení zařízení](media/data-box-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. Na **aktivovat** dlaždice, vyberte **vygenerovat klíč** vytvořit aktivační kód. Vyberte ikonu kopírování zkopírujte klíč a uložit pro pozdější použití.

    ![Získání aktivačního klíče](media/data-box-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Vypršení platnosti tohoto klíče aktivace tři dny po jeho vygenerování.
> - Pokud vypršela platnost klíče, vygenerujte nový klíč. Starší klíč není platný.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o tématech spojených s Data Boxem Edge, jako jsou:

> [!div class="checklist"]
> * Vytvoření nového prostředku
> * Získání aktivačního klíče

Přejděte k dalšímu kurzu se naučíte, nainstalovat Data Box Edge.

> [!div class="nextstepaction"]
> [Nainstalovat Data Box Edge](./data-box-edge-deploy-install.md)



