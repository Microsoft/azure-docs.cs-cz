---
title: Kurz přípravy webu Azure Portal k nasazení služby Data Box Gateway | Microsoft Docs
description: První kurz k nasazení služby Azure Data Box Gateway se zabývá přípravou webu Azure Portal.
services: databox-edge-gateway
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 6db713984b62ce3db48b2e72a4b117696bdd6add
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452968"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway-preview"></a>Kurz: Příprava na nasazení Azure Data Box Gateway (Preview)


Toto je první kurz série kurzů nasazení potřebných k dokončení nasazení služby Azure Data Box Gateway. Tento kurz popisuje přípravu webu Azure Portal k nasazení prostředku Data Box Gateway. 

K dokončení této instalace a procesu konfigurace potřebujete oprávnění správce. Příprava portálu trvá méně než 10 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření nového prostředku
> * Stažení image virtuálního zařízení
> * Získání aktivačního klíče

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


> [!IMPORTANT]
> - Data Box Gateway je ve verzi Preview. Před objednáním a nasazením tohoto řešení si přečtěte [podmínky užívání pro předběžné verze systému Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

### <a name="get-started"></a>Začínáme

Pokud chcete nasadit službu Data Box Gateway, postupujte podle následujících kurzů v uvedeném pořadí.

| **#** | **V tomto kroku** | **Použijte tyto dokumenty** |
| --- | --- | --- | 
| 1. |**[Příprava webu Azure Portal pro službu Data Box Gateway](data-box-gateway-deploy-prep.md)** |Před zřízením virtuálního zařízení Data Box Gateway vytvořte a nakonfigurujte prostředek Data Box Gateway. |
| 2. |**[Zřízení služby Data Box Gateway v Hyper-V](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[Zřízení služby Data Box Gateway ve VMware](data-box-gateway-deploy-provision-vmware.md)**|V případě Hyper-V zřiďte virtuální zařízení Data Box Gateway a připojte se k němu v hostitelském systému s Hyper-V ve Windows Serveru 2016 nebo Windows Serveru 2012 R2. <br><br><br> V případě VMware zřiďte virtuální zařízení Data Box Gateway a připojte se k němu v hostitelském systému s VMware ESXi 6.0 nebo 6.5.<br></br> |
| 3. |**[Připojení, nastavení a aktivace služby Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)** |Připojte se k místnímu webovému uživatelskému rozhraní, dokončete nastavení zařízení a aktivujte zařízení. Potom můžete zřídit sdílené složky SMB.  |
| 4. |**[Přenos dat pomocí služby Data Box Gateway](data-box-gateway-deploy-add-shares.md)** |Přidejte sdílené složky a připojte se k nim pomocí protokolu SMB nebo přes systém souborů NFS. |

Teď můžete začít nastavovat Azure Portal.

## <a name="prerequisites"></a>Požadavky

Tady najdete požadavky na konfiguraci prostředku Data Box Gateway, zařízení Data Box Gateway a sítě datacentra.

### <a name="for-the-data-box-gateway-resource"></a>Prostředek Data Box Gateway

Než začnete, ujistěte se, že:

* Ve vašem předplatném Microsoft Azure je povolený prostředek Data Box Gateway.
* Máte účet služby Microsoft Azure Storage a přihlašovací údaje účtu.

### <a name="for-the-data-box-gateway-device"></a>Zařízení Data Box Gateway

Před nasazením virtuálního zařízení se ujistěte, že:

* Máte přístup k hostitelskému systému s Hyper-V ve Windows Serveru 2012 R2 nebo novějším nebo VMware (ESXi 6.0 nebo 6.5), který můžete použít ke zřízení zařízení.
* Hostitelský systém může pro zřízení virtuálního zařízení Data Box vyhradit následující prostředky:
  
  * Minimálně 4 jádra
  * Minimálně 8 GB paměti RAM 
  * Jedno síťové rozhraní
  * 250GB disk s operačním systémem
  * 2TB virtuální disk pro data systému

### <a name="for-the-datacenter-network"></a>Síť datacentra

Než začnete, ujistěte se, že:

* Síť ve vašem datacentru je nakonfigurovaná podle požadavků na síť pro vaše zařízení Data Box Gateway. Další informace najdete v tématu [Systémové požadavky na Data Box Gateway](data-box-gateway-system-requirements.md).

* Vaše služba Data Box Gateway má vyhrazenou šířku internetového pásma 20 Mb/s (nebo více), která je vždy dostupná. Tato šířka pásma by se neměla sdílet s žádnými jinými aplikacemi. Pokud používáte omezení využití sítě, doporučujeme použít šířku internetového pásma 32 Mb/s nebo více, aby omezování fungovalo.

## <a name="create-a-new-resource"></a>Vytvoření nového prostředku

Pomocí následujícího postupu vytvořte nový prostředek Data Box Gateway. 

Pokud už pro správu vašich virtuálních zařízení máte existující prostředek Data Box Gateway, přeskočte tento krok a přejděte k části [Získání aktivačního klíče](#get-the-activation-key).

Provedením následujících kroků na webu Azure Portal vytvořte prostředek Data Box.
1. Pomocí svých přihlašovacích údajů Microsoft Azure se přihlaste k portálu Azure Preview na této adrese URL: [https://aka.ms/databox-edge](https://aka.ms/databox-edge). 

2. Vyberte předplatné, které chcete použít pro Data Box Edge ve verzi Preview. Vyberte oblast, ve které chcete prostředek Data Box Edge nasadit. V možnosti **Data Box Gateway** klikněte na **Vytvořit**.

    ![Vyhledání služby Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

3. Zadejte nebo vyberte pro nový prostředek následující informace.
    
    |Nastavení  |Hodnota  |
    |---------|---------|
    |Název prostředku   | Popisný název pro identifikaci prostředku.<br>Název musí obsahovat 2 až 50 znaků a může obsahovat písmena, číslice a spojovníky.<br> Název musí začínat a končit na písmeno nebo číslici.        |
    |Předplatné    |Předplatné je propojené s vaším fakturačním účtem. |
    |Skupina prostředků  |Vyberte existující skupinu nebo vytvořte novou.<br>Další informace o [skupinách prostředků Azure](../azure-resource-manager/resource-group-overview.md).     |
    |Umístění     |Pro tuto verzi jsou dostupné oblasti USA – východ, Západní USA 2, Jihovýchodní Asie a Západní Evropa. <br> Zvolte umístění, které je nejblíže zeměpisné oblasti, ve které chcete zařízení nasadit.|
    
    ![Vytvoření prostředku Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
4. Klikněte na **OK**.
 
Vytvoření prostředku trvá několik minut. Po úspěšném vytvoření prostředku obdržíte odpovídající oznámení.


## <a name="download-the-virtual-device-image"></a>Stažení image virtuálního zařízení

Po vytvoření prostředku Data Box Gateway si stáhněte odpovídající image virtuálního zařízení, pomocí které můžete ve svém hostitelském systému zřídit virtuální zařízení. Image virtuálních zařízení jsou specifické pro jednotlivé operační systémy a můžete si je stáhnout v okně **Rychlý start** vašeho prostředku na webu Azure Portal.

> [!IMPORTANT]
> Software ve službě Data Box Gateway je možné používat pouze s prostředkem Data Box Gateway.


Na webu [Azure Portal](https://portal.azure.com/) proveďte následující kroky.

1. Klikněte na prostředek, který jste vytvořili, a pak klikněte na **Přehled**. Pokud máte existující prostředek Azure Data Box Gateway, klikněte na něj a přejděte do části **Přehled**.

    ![Nový prostředek Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

4. V pravém podokně rychlého startu klikněte na odkaz odpovídající imagi, kterou chcete stáhnout. Soubory imagí mají velikost přibližně 4,8 GB.
   
   * [VHDX pro Hyper-V ve Windows Serveru 2012 R2 nebo novějším](https://aka.ms/dbe-vhdx-2012)
   * [VMDK pro VMWare ESXi 6.0 nebo 6.5](https://aka.ms/dbe-vmdk)

5. Stáhněte soubor na místní disk, rozbalte ho a poznamenejte si umístění rozbaleného souboru.


## <a name="get-the-activation-key"></a>Získání aktivačního klíče

Po zprovoznění prostředku Data Box Gateway bude potřeba získat aktivační klíč. Tento klíč slouží k aktivaci a propojení zařízení Data Box Gateway s prostředkem.

Aktivační klíč slouží k registraci všech zařízení Data Box Gateway, které je potřeba aktivovat, v prostředku Data Box Gateway. Tento klíč můžete získat teď, když jste na webu Azure Portal.

1. Klikněte na prostředek, který jste vytvořili, a pak klikněte na **Přehled**.

    ![Nový prostředek Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. Kliknutím na **Vygenerovat klíč** vytvořte aktivační klíč. Kliknutím na ikonu kopírování klíč zkopírujte a uložte ho pro pozdější použití.

    ![Získání aktivačního klíče](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - Platnost aktivačního klíče vyprší 3 dny po vygenerování. 
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


