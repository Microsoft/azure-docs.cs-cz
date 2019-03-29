---
title: Kurz přípravy webu Azure Portal k nasazení služby Data Box Gateway | Microsoft Docs
description: První kurz k nasazení služby Azure Data Box Gateway se zabývá přípravou webu Azure Portal.
services: databox-edge-gateway
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: alkohli
ms.openlocfilehash: 34bc4d7cbdbb89cd9ff3f334ca32087c474735b7
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620083"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>Kurz: Příprava na nasazení Azure Data Box brány


Toto je první kurz série kurzů nasazení potřebných k dokončení nasazení služby Azure Data Box Gateway. Tento kurz popisuje přípravu webu Azure Portal k nasazení prostředku Data Box Gateway. 

K dokončení této instalace a procesu konfigurace potřebujete oprávnění správce. Příprava portálu trvá méně než 10 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření nového prostředku
> * Stažení image virtuálního zařízení
> * Získání aktivačního klíče

## <a name="get-started"></a>Začínáme

Pokud chcete nasadit službu Data Box Gateway, postupujte podle následujících kurzů v uvedeném pořadí.

| **#** | **V tomto kroku** | **Použijte tyto dokumenty** |
| --- | --- | --- | 
| 1. |**[Příprava webu Azure Portal pro službu Data Box Gateway](data-box-gateway-deploy-prep.md)** |Před zřízením virtuálního zařízení Data Box Gateway vytvořte a nakonfigurujte prostředek Data Box Gateway. |
| 2. |**[Zřízení služby Data Box Gateway v Hyper-V](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[Zřízení služby Data Box Gateway ve VMware](data-box-gateway-deploy-provision-vmware.md)**|V případě Hyper-V zřiďte virtuální zařízení Data Box Gateway a připojte se k němu v hostitelském systému s Hyper-V ve Windows Serveru 2016 nebo Windows Serveru 2012 R2. <br><br><br> Pro replikaci z VMware zřídit a připojte se k virtuální zařízení Data Box brány v hostitelském systému, s VMware ESXi 6.0 a 6.5, 6.7.<br></br> |
| 3. |**[Připojení, nastavení a aktivace služby Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)** |Připojte se k místnímu webovému uživatelskému rozhraní, dokončete nastavení zařízení a aktivujte zařízení. Potom můžete zřídit sdílené složky SMB.  |
| 4. |**[Přenos dat pomocí služby Data Box Gateway](data-box-gateway-deploy-add-shares.md)** |Přidejte sdílené složky a připojte se k nim pomocí protokolu SMB nebo přes systém souborů NFS. |

Teď můžete začít nastavovat Azure Portal.

## <a name="prerequisites"></a>Požadavky

Tady najdete požadavky na konfiguraci prostředku Data Box Gateway, zařízení Data Box Gateway a sítě datacentra.

### <a name="for-the-data-box-gateway-resource"></a>Prostředek Data Box Gateway

Než začnete, ujistěte se, že:

- Pro prostředek brány dat pole by měla podporovat vaše předplatné Microsoft Azure. Předplatná s průběžnými platbami nejsou podporovány.
- Máte účet služby Microsoft Azure Storage a přihlašovací údaje účtu.

### <a name="for-the-data-box-gateway-device"></a>Zařízení Data Box Gateway

Před nasazením virtuálního zařízení se ujistěte, že:

- Máte přístup k systému hostitele s technologií Hyper-V v systému Windows Server 2012 R2 nebo novější nebo VMware (ESXi 6.0 a 6.5, 6.7), kterou lze použít na základě zařízení.
- Hostitelský systém může pro zřízení virtuálního zařízení Data Box vyhradit následující prostředky:
  
  - Minimálně 4 virtuální procesory.
  - Minimálně 8 GB paměti RAM
  - Jedno síťové rozhraní
  - 250GB disk s operačním systémem
  - 2TB virtuální disk pro data systému

### <a name="for-the-datacenter-network"></a>Síť datacentra

Než začnete, ujistěte se, že:

- Síť ve vašem datacentru je nakonfigurovaná podle požadavků na síť pro vaše zařízení Data Box Gateway. Další informace najdete v tématu [požadavky na systém brána pole dat](data-box-gateway-system-requirements.md).

- Normální provozní podmínky při pole brány dat měli byste mít a:

    - Stáhnout minimálně 10 MB/s šířky pásma zajišťující, že zařízení zůstane aktualizované.
    - Minimálně 20 MB/s vyhrazenou nahrávání a stahování šířky pásma pro přenos souborů.

## <a name="create-a-new-resource"></a>Vytvoření nového prostředku

Pokud už pro správu vašich virtuálních zařízení máte existující prostředek Data Box Gateway, přeskočte tento krok a přejděte k části [Získání aktivačního klíče](#get-the-activation-key).

Vytvoření prostředku brány pole dat, proveďte následující kroky na webu Azure Portal.

1. K přihlášení použijte svoje přihlašovací údaje Microsoft Azure:

    - Na webu Azure portal na této adrese URL: [ https://portal.azure.com ](http://portal.azure.com).
    - Nebo na portálu Azure Government na této adrese URL: [ https://portal.azure.us ](https://portal.azure.us). Další podrobnosti najdete v části [připojit se k portálu Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. V levém podokně vyberte **+ vytvořit prostředek**. Vyhledejte **Data Box Edge / Data Box brány**. Vybrat hranu pole Data nebo Data Box brány. Vyberte **Vytvořit**.
3. Vyberte předplatné, které chcete použít pro zařízení Data Box brány. Vyberte oblast, ve které chcete nasadit prostředek brány Data Box. Tato verze východní USA, jihovýchodní Asie a západní Evropa jsou k dispozici. Zvolte umístění, které je nejblíže zeměpisné oblasti, ve které chcete zařízení nasadit. V **brána dat pole** možnosti, vyberte **vytvořit**.

    ![Vyhledání služby Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

4. Na **Základy** kartu, zadejte nebo vyberte následující **podrobnosti o projektu**.
    
    |Nastavení  |Hodnota  |
    |---------|---------|
    |Předplatné    |To se automaticky vyplní podle předchozí výběr. Předplatné je propojené s vaším fakturačním účtem. |
    |Skupina prostředků  |Vyberte existující skupinu nebo vytvořte novou.<br>Další informace o [skupinách prostředků Azure](../azure-resource-manager/resource-group-overview.md).     |

5. Zadejte nebo vyberte následující **Instance podrobnosti**.

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Název   | Popisný název pro identifikaci prostředku.<br>Název musí obsahovat 2 až 50 znaků a může obsahovat písmena, číslice a spojovníky.<br> Název musí začínat a končit na písmeno nebo číslici.        |   
    |Oblast     |Pro tuto verzi východní USA, jihovýchodní Asie a západní Evropa jsou k dispozici pro nasazení vašich prostředků. Všechny oblasti státní správy pro Azure Government, jsou uvedeny v [oblastí Azure](https://azure.microsoft.com/global-infrastructure/regions/) jsou k dispozici. <br> Zvolte umístění, které je nejblíže zeměpisné oblasti, ve které chcete zařízení nasadit.|
    
    ![Vytvoření prostředku Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
6. Vyberte **Zkontrolovat a vytvořit**.
 
7. Na **zkontrolujte + vytvořit** kartu, zkontrolujte **podrobnosti o cenách**, **podmínky použití**a podrobnosti pro váš prostředek. Vyberte **Vytvořit**.

    ![Projděte si podrobnosti o prostředku brány dat pole](media/data-box-gateway-deploy-prep/data-box-gateway-resource1.png)

Vytvoření prostředku trvá několik minut. Po prostředek se úspěšně vytvoří a nasadí, budete upozorněni na to. Vyberte **přejít k prostředku**.

![Projděte si podrobnosti o prostředku brány dat pole](media/data-box-gateway-deploy-prep/data-box-gateway-resource2.png)

## <a name="download-the-virtual-device-image"></a>Stažení image virtuálního zařízení

Po vytvoření prostředku Data Box Gateway si stáhněte odpovídající image virtuálního zařízení, pomocí které můžete ve svém hostitelském systému zřídit virtuální zařízení. Image virtuálního zařízení jsou specifické pro operační systém.

> [!IMPORTANT]
> Software ve službě Data Box Gateway je možné používat pouze s prostředkem Data Box Gateway.

Postupujte podle těchto kroků [webu Azure portal](https://portal.azure.com/) Chcete-li stáhnout bitovou kopii virtuálního zařízení.

1. V prostředku, který jste vytvořili a pak vyberte **přehled**. Pokud máte existující prostředek služby Azure Data Box brány, vyberte prostředek a přejděte k **přehled**. Vyberte **nastavení zařízení**.

    ![Nový prostředek Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. Na **stažení image** dlaždice, vyberte bitovou kopii virtuálního zařízení odpovídající operační systém na hostitelském serveru používají ke zřízení virtuálního počítače. Soubory obrázků jsou přibližně 5.6 GB.
   
   * [VHDX pro Hyper-V ve Windows Serveru 2012 R2 nebo novějším](https://aka.ms/dbe-vhdx-2012)
   * [VMDK pro VMWare ESXi 6.0 a 6.5, 6.7](https://aka.ms/dbe-vmdk).

    ![Stáhnout image virtuálního zařízení Data Box brány](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. Stáhněte soubor na místní disk, rozbalte ho a poznamenejte si umístění rozbaleného souboru.


## <a name="get-the-activation-key"></a>Získání aktivačního klíče

Po zprovoznění prostředků brány Data Box se budete muset získat aktivační klíč. Tento klíč slouží k aktivaci a propojení zařízení Data Box Gateway s prostředkem. Tento klíč můžete získat teď, když jste na webu Azure Portal.

1. Vyberte prostředek, který jste vytvořili a pak vyberte **přehled**. V **nastavení zařízení**, přejděte **konfigurovat a aktivovat** dlaždici.

    ![Konfigurovat a aktivovat dlaždice](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. Vyberte **vygenerovat klíč** vytvořit aktivační kód. Vyberte ikonu kopírování zkopírujte klíč a uložit pro pozdější použití.

    ![Získání aktivačního klíče](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Vypršení platnosti tohoto klíče aktivace tři dny po jeho vygenerování.
> - Pokud vypršela platnost klíče, vygenerujte nový klíč. Starší klíč není platný.

## <a name="next-steps"></a>Další postup

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


