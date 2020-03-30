---
title: Příprava portálu pro virtuální pole StorSimple
description: První kurz nasazení virtuálního pole StorSimple zahrnuje přípravu portálu Azure
author: alkohli
ms.assetid: 68a4cfd3-94c9-46cb-805c-46217290ce02
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7f2c7a6f9c1146627f81dc20386c59a91724b245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254531"
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>Nasazení virtuálního pole StorSimple – příprava portálu Azure

![](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)


## <a name="overview"></a>Přehled

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Toto je první článek v sérii kurzů nasazení potřebných k úplnému nasazení virtuálního pole jako souborového serveru nebo serveru iSCSI pomocí modelu Resource Manager. Tento článek popisuje přípravu potřebnou k vytvoření a konfiguraci služby Správce zařízení StorSimple před zřízením virtuálního pole. Tento článek také odkazuje na kontrolní seznam konfigurace nasazení a požadavky konfigurace.

K dokončení této instalace a procesu konfigurace potřebujete oprávnění správce. Doporučujeme, abyste si před zahájením zkontrolovali kontrolní seznam konfigurace nasazení. Příprava portálu trvá méně než 10 minut.

Informace publikované v tomto článku se vztahují na nasazení virtuálních polí StorSimple na webu Azure Portal a Microsoft Azure Government Cloud.

### <a name="get-started"></a>Začínáme
Pracovní postup nasazení se skládá z přípravy portálu, zřizování virtuálního pole ve virtualizovaném prostředí a dokončení nastavení. Chcete-li začít s nasazením StorSimple Virtual Array jako souborového serveru nebo serveru iSCSI, musíte se obrátit na následující prostředky v tabulce.

#### <a name="deployment-articles"></a>Články o nasazení

Chcete-li nasadit virtuální pole StorSimple, naleznete v následujících článcích v předepsaném pořadí.

| **#** | **V tomto kroku** | **Uděláte to ...** | **A použijte tyto dokumenty.** |
| --- | --- | --- | --- |
| 1. |**Nastavení portálu Azure** |Před zřízením virtuálního pole StorSimple vytvořte a nakonfigurujte službu StorSimple Device Manager. |[Příprava portálu](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**Zřízení virtuálního pole** |V případě technologie Hyper-V zřizujte a připojujte se k virtuálnímu poli StorSimple v hostitelském systému se systémem Hyper-V v systémech Windows Server 2012 R2, Windows Server 2012 nebo Windows Server 2008 R2. <br></br> <br></br> Pro vmware, zřízení a připojení k StorSimple Virtual Array na hostitelském systému se systémem VMware ESXi 5.0, 5.5, 6.0 nebo 6.5.<br></br> |[Zřízení virtuálního pole v technologii Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [Zřízení virtuálního pole ve společnosti VMware](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**Nastavení virtuálního pole** |Pro souborový server proveďte počáteční nastavení, zaregistrujte souborový server StorSimple a dokončete nastavení zařízení. Potom můžete zřídit sdílené složky SMB. <br></br> <br></br> Pro server iSCSI proveďte počáteční nastavení, zaregistrujte server StorSimple iSCSI a dokončete nastavení zařízení. Potom můžete zřídit svazky iSCSI. |[Nastavení virtuálního pole jako souborového serveru](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Nastavení virtuálního pole jako serveru iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md) |

Teď můžete začít nastavovat Azure Portal.

## <a name="configuration-checklist"></a>Kontrolní seznam konfigurace

Kontrolní seznam konfigurace popisuje informace, které je třeba shromáždit před konfigurací softwaru ve virtuálním poli StorSimple. Příprava těchto informací předem pomáhá zefektivnit proces nasazování zařízení StorSimple ve vašem prostředí. V závislosti na tom, zda je vaše virtuální pole StorSimple nasazeno jako souborový server nebo server iSCSI, potřebujete jeden z následujících kontrolních seznamů.

* Stáhněte si [kontrolní seznam konfigurace souborového serveru StorSimple Virtual Array](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).
* Stáhněte si [kontrolní seznam konfigurace serveru StorSimple Virtual Array iSCSI](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Požadavky

Zde naleznete předpoklady konfigurace pro službu StorSimple Device Manager, vaše virtuální pole StorSimple a síť datového centra.

### <a name="for-the-storsimple-device-manager-service"></a>Služba Správce zařízení StorSimple

Než začnete, ujistěte se, že:

* Máte účet Microsoft a přihlašovací údaje účtu.
* Máte účet služby Microsoft Azure Storage a přihlašovací údaje účtu.
* Vaše předplatné Microsoft Azure by mělo být povoleno pro službu StorSimple Device Manager.

### <a name="for-the-storsimple-virtual-array"></a>Pro virtuální pole StorSimple

Před nasazením virtuálního pole se ujistěte, že:

* Máte přístup k hostitelskému systému se systémem Hyper-V v systému Windows Server 2008 R2 nebo novějším nebo v systému VMware (ESXi 5.0, 5.5, 6.0 nebo 6.5), který lze použít k zřízení zařízení.
* Hostitelský systém je schopen vyhradit následující prostředky pro zřízení virtuálního pole:
  
  * Minimálně 4 jádra
  * Minimálně 8 GB paměti RAM Pokud plánujete nakonfigurovat virtuální pole jako souborový server, 8 GB podporuje 2 miliony souborů. Potřebujete 16 GB PAMĚTI RAM pro podporu 2 - 4 miliony souborů.
  * Jedno síťové rozhraní
  * 500 GB virtuální disk pro systémová data.

### <a name="for-the-datacenter-network"></a>Síť datacentra

Než začnete, ujistěte se, že:

* Síť v datovém centru je nakonfigurována podle síťových požadavků pro vaše zařízení StorSimple. Další informace naleznete v tématu [StorSimple Virtual Array System Requirements](storsimple-ova-system-requirements.md).
* Vaše virtuální pole StorSimple má vždy k dispozici vyhrazenou šířku pásma internetu (nebo více) o rychlosti 5 Mb/s. Tato šířka pásma by se neměla sdílet s žádnými jinými aplikacemi.

## <a name="step-by-step-preparation"></a>Krok za krokem příprava

Pomocí následujících podrobných pokynů připravte portál pro službu StorSimple Device Manager.

## <a name="step-1-create-a-new-service"></a>Krok 1: Vytvoření nové služby

Jedna instance služby StorSimple Device Manager může spravovat více virtuálních polí StorSimple. Pomocí následujících kroků vytvořte instanci služby Správce zařízení StorSimple. Pokud máte existující službu Správce zařízení StorSimple pro správu virtuálních polí, přeskočte tento krok a přejděte ke [kroku 2: Získání registračního klíče služby](#step-2-get-the-service-registration-key).

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

> [!IMPORTANT]
> Pokud jste nepovolili automatické vytvoření účtu úložiště při vytvoření služby, po úspěšném vytvoření služby bude nutné vytvořit alespoň jeden účet úložiště.
> 
> * Pokud jste nevytvořili účet úložiště automaticky, najdete podrobné pokyny k vytvoření účtu v tématu [Konfigurace nového účtu úložiště pro službu](#optional-step-configure-a-new-storage-account-for-the-service).
> * Pokud jste automatické vytvoření účtu úložiště povolili, pokračujte na [krok 2: Získání registračního klíče služby](#step-2-get-the-service-registration-key).
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>Krok 2: Získání registračního klíče služby

Po vytvoření a spuštění služby Správce zařízení StorSimple je nutné získat registrační klíč služby. Tento klíč se používá k registraci a připojení zařízení StorSimple ke službě.

Na webu [Azure Portal](https://portal.azure.com/) proveďte následující kroky.

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> Registrační klíč služby se používá k registraci všech zařízení Správce zařízení StorSimple, která se potřebují zaregistrovat ve službě StorSimple Device Manager.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>Krok 3: Stažení obrazu virtuálního pole

Po vytvoření registračního klíče služby budete muset stáhnout příslušnou bitovou kopii virtuálního pole, abyste zřizovali virtuální pole v hostitelském systému. Image virtuálního pole jsou specifické pro operační systém a lze je stáhnout ze stránky Rychlý start na webu Azure Portal.

> [!IMPORTANT]
> Software spuštěný na virtuálním poli StorSimple lze použít pouze se službou StorSimple Device Manager.
> 
> 

Na webu [Azure Portal](https://portal.azure.com/) proveďte následující kroky.

#### <a name="to-get-the-virtual-array-image"></a>Získání image virtuálního pole

1. Přihlaste se k [portálu Azure](https://portal.azure.com/). 
2. Na webu Azure Portal klikněte na **Procházet > Správci zařízení StorSimple**.
3. Vyberte existující službu Správce zařízení StorSimple. V okně **Správce zařízení StorSimple** klepněte na tlačítko **Rychlý start**. 
4. Klikněte na odkaz odpovídající obrázku, který chcete stáhnout ze služby Stažení softwaru. Soubory imagí mají velikost přibližně 4,8 GB.
   
   * VHDX pro Hyper-V na Windows Server 2012 a novějších
   * Virtuální disk VHD pro technologii Hyper-V v systému Windows Server 2008 R2 a novějších
   * VMDK pro VMWare ESXi 5.0, 5.5, 6.0 nebo 6.5
5. Stáhněte soubor na místní disk, rozbalte ho a poznamenejte si umístění rozbaleného souboru.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Volitelný krok: Konfigurace nového účtu úložiště pro službu

Tento krok je volitelný a měl by být proveden pouze v případě, že jste nepovolili automatické vytvoření účtu úložiště se službou.

Pokud potřebujete vytvořit účet úložiště Azure v jiné oblasti, přečtěte [si, jak vytvořit účet úložiště](../storage/common/storage-account-create.md) pro podrobné pokyny.

Pomocí následujících kroků na [webu Azure portal](https://ms.portal.azure.com/) na stránce služby StorSimple Device Manager přidejte existující účet úložiště Microsoft Azure.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Přidání přihlašovacích údajů účtu úložiště, které má stejné předplatné Azure jako služba Správce zařízení

1. Přejděte do služby Správce zařízení, vyberte ji a poklikejte na ni. Tím se otevře okno **Přehled.**
2. V části **Konfigurace** vyberte **přihlašovací údaje účtu úložiště.**
3. Klikněte na **Přidat**.
4. V okně **Přidat účet úložiště** postupujte takto:
   
   1. V **případě předplatného**vyberte **aktuální**.
   
   2. Zadejte název svého účtu úložiště Azure.
   
   3. Výběrem **možnosti Povolit** vytvoříte zabezpečený kanál pro síťovou komunikaci mezi zařízením StorSimple a cloudem. Vyberte **Zakázat** pouze v případě, že pracujete v privátním cloudu.
   
   4. Klikněte na **Přidat**. Budete upozorněni po úspěšném vytvoření účtu úložiště.<br></br>
   
      ![Přidání existujícího přihlašovacího údaje účtu úložiště](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Další krok

Dalším krokem je zřízení virtuálního počítače pro vaše virtuální pole StorSimple. V závislosti na vašem hostitelském operačním systému najdete podrobné pokyny v jednom z následujících témat:

* [Zřízení virtuálního pole StorSimple v technologii Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [Zřízení virtuálního pole StorSimple ve společnosti VMware](storsimple-virtual-array-deploy2-provision-vmware.md)

