---
title: Příprava portálu pro virtuální pole StorSimple
description: První kurz nasazení StorSimple Virtual Array zahrnuje přípravu Azure Portal
author: alkohli
ms.assetid: 68a4cfd3-94c9-46cb-805c-46217290ce02
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c1a3d0b05f04ad8c182d428843479160666b96ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87070329"
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>Nasazení StorSimple Virtual Array – Příprava Azure Portal

![Diagram znázorňující kroky, které jsou potřeba k nasazení virtuálního pole První krok je označený jako začátek a zvýrazní se.](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)


## <a name="overview"></a>Přehled

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Toto je první článek v sérii kurzů pro nasazení požadovaných k úplnému nasazení virtuálního pole jako souborového serveru nebo serveru iSCSI pomocí modelu Správce prostředků. Tento článek popisuje přípravu nutnou k vytvoření a konfiguraci služby StorSimple Správce zařízení před zřízením virtuálního pole. Tento článek také odkazuje na kontrolní seznam konfigurace nasazení a požadavky na konfiguraci.

K dokončení této instalace a procesu konfigurace potřebujete oprávnění správce. Než začnete, doporučujeme zkontrolovat kontrolní seznam konfigurace nasazení. Příprava portálu trvá méně než 10 minut.

Informace publikované v tomto článku se vztahují na nasazení virtuálních polí StorSimple v cloudu Azure Portal a Microsoft Azure Government.

### <a name="get-started"></a>Začínáme
Pracovní postup nasazení se skládá z přípravy portálu, zřizování virtuálního pole ve virtualizovaném prostředí a dokončení instalace. Chcete-li začít s nasazením virtuálního pole StorSimple jako souborovým serverem nebo serverem iSCSI, je nutné, abyste se přečtěte na následujících seřazeních prostředků.

#### <a name="deployment-articles"></a>Články o nasazení

Pokud chcete nasadit virtuální pole StorSimple, přečtěte si následující články v předepsané posloupnosti.

| **#** | **V tomto kroku** | **Uděláte to...** | **A použijte tyto dokumenty.** |
| --- | --- | --- | --- |
| 1. |**Nastavení Azure Portal** |Vytvořte a nakonfigurujte službu StorSimple Správce zařízení před zřízením virtuálního pole StorSimple. |[Příprava portálu](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**Zřízení virtuálního pole** |Pro Hyper-V zajistěte a připojte se k virtuálnímu poli StorSimple v hostitelském systému, na kterém běží Hyper-V v systému Windows Server 2012 R2, Windows Server 2012 nebo Windows Server 2008 R2. <br></br> <br></br> Pro VMware zajistěte a připojte se k StorSimple virtuálnímu poli na hostitelském systému, na kterém běží VMware ESXi 5,0, 5,5, 6,0 nebo 6,5.<br></br> |[Zřízení virtuálního pole v Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [Zřízení virtuálního pole ve VMware](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**Nastavení virtuálního pole** |Pro souborový server proveďte počáteční nastavení, zaregistrujte souborový server StorSimple a dokončete instalaci zařízení. Potom můžete zřídit sdílené složky SMB. <br></br> <br></br> Pro váš server iSCSI proveďte počáteční nastavení, zaregistrujte svůj StorSimple Server iSCSI a dokončete instalaci zařízení. Potom můžete zřizovat svazky iSCSI. |[Nastavení virtuálního pole jako souborového serveru](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Nastavení virtuálního pole jako serveru iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md) |

Teď můžete začít nastavovat Azure Portal.

## <a name="configuration-checklist"></a>Kontrolní seznam konfigurace

Kontrolní seznam konfigurace popisuje informace, které je třeba shromáždit před tím, než nakonfigurujete software ve virtuálním poli StorSimple. Když se tyto informace připraví předem, pomůže vám to zjednodušit proces nasazení zařízení StorSimple ve vašem prostředí. V závislosti na tom, jestli je vaše virtuální pole StorSimple nasazené jako souborový server nebo server iSCSI, potřebujete jeden z následujících kontrolních seznamů.

* Stáhněte si [Kontrolní seznam konfigurace souborového serveru StorSimple Virtual Array](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).
* Stáhněte si [Kontrolní seznam konfigurace serveru iSCSI Virtual Array StorSimple](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Předpoklady

Tady najdete požadavky na konfiguraci pro službu StorSimple Správce zařízení, vaše virtuální pole StorSimple a síť datacenter.

### <a name="for-the-storsimple-device-manager-service"></a>Služba Správce zařízení StorSimple

Než začnete, ujistěte se, že:

* Máte účet Microsoft a přihlašovací údaje účtu.
* Máte účet služby Microsoft Azure Storage a přihlašovací údaje účtu.
* Pro službu StorSimple Správce zařízení by mělo být povolené vaše předplatné Microsoft Azure.

### <a name="for-the-storsimple-virtual-array"></a>Pro virtuální pole StorSimple

Před nasazením virtuálního pole se ujistěte, že:

* Máte přístup k hostitelskému systému, na kterém běží Hyper-V na Windows serveru 2008 R2 nebo novějším, nebo VMware (ESXi 5,0, 5,5, 6,0 nebo 6,5), který se dá použít k zřízení zařízení.
* Hostitelský systém dokáže vyhradit následující prostředky pro zřízení vašeho virtuálního pole:
  
  * Minimálně 4 jádra
  * Minimálně 8 GB paměti RAM Pokud plánujete nakonfigurovat virtuální pole jako souborový server, 8 GB podporuje soubory 2 000 000. Pro podporu 2-4 milionů souborů potřebujete 16 GB paměti RAM.
  * Jedno síťové rozhraní
  * Virtuální disk 500 GB pro systémová data.

### <a name="for-the-datacenter-network"></a>Síť datacentra

Než začnete, ujistěte se, že:

* Síť v datacentru je nakonfigurovaná podle požadavků na síť pro vaše zařízení StorSimple. Další informace najdete v tématu [požadavky na systém StorSimple Virtual Array](storsimple-ova-system-requirements.md).
* Vaše virtuální pole StorSimple má ve všech případech vyhrazenou šířku pásma 5 MB/s (nebo více). Tato šířka pásma by se neměla sdílet s žádnými jinými aplikacemi.

## <a name="step-by-step-preparation"></a>Krok za krokem Příprava

Použijte následující podrobné pokyny k přípravě portálu pro službu StorSimple Správce zařízení.

## <a name="step-1-create-a-new-service"></a>Krok 1: Vytvoření nové služby

Jedna instance služby StorSimple Správce zařízení může spravovat více virtuálních polí StorSimple. Pomocí následujících kroků vytvořte instanci služby Správce zařízení StorSimple. Pokud máte existující službu StorSimple Správce zařízení ke správě virtuálních polí, přeskočte tento krok a přejděte ke [kroku 2: získání registračního klíče služby](#step-2-get-the-service-registration-key).

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
> Registrační klíč služby se používá k registraci všech zařízení StorSimple Správce zařízení, která se musí registrovat ve službě StorSimple Správce zařízení.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>Krok 3: stažení image virtuálního pole

Až budete mít registrační klíč služby, budete muset stáhnout příslušnou image virtuálního pole a zřídit tak virtuální pole v hostitelském systému. Obrázky virtuálních polí jsou specifické pro operační systém a je možné je stáhnout ze stránky rychlé zprovoznění Azure Portal.

> [!IMPORTANT]
> Software spuštěný ve virtuálním poli StorSimple se dá použít jenom u služby StorSimple Správce zařízení.
> 
> 

Na webu [Azure Portal](https://portal.azure.com/) proveďte následující kroky.

#### <a name="to-get-the-virtual-array-image"></a>Získání obrázku virtuálního pole

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/). 
2. V Azure Portal klikněte na **procházet > Správce zařízení StorSimple**.
3. Vyberte existující službu StorSimple Správce zařízení. V okně **StorSimple Správce zařízení** klikněte na **rychlé zprovoznění**. 
4. Klikněte na odkaz odpovídající imagi, kterou chcete stáhnout z webu Microsoft Download Center. Soubory imagí mají velikost přibližně 4,8 GB.
   
   * VHDX pro Hyper-V v systému Windows Server 2012 a novější
   * VHD pro Hyper-V v systému Windows Server 2008 R2 a novějším
   * VMDK pro VMWare ESXi 5,0, 5,5, 6,0 nebo 6,5
5. Stáhněte soubor na místní disk, rozbalte ho a poznamenejte si umístění rozbaleného souboru.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Volitelný krok: konfigurace nového účtu úložiště pro službu

Tento krok je nepovinný a měl by se provádět jenom v případě, že jste nepovolili automatické vytvoření účtu úložiště s vaší službou.

Pokud potřebujete vytvořit účet úložiště Azure v jiné oblasti, přečtěte si téma [Vytvoření účtu úložiště](../storage/common/storage-account-create.md) pro podrobné pokyny.

V [Azure Portal](https://ms.portal.azure.com/) na stránce služby Správce zařízení StorSimple proveďte následující kroky a přidejte existující účet úložiště Microsoft Azure.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Přidání přihlašovacích údajů účtu úložiště, které mají stejné předplatné Azure jako služba Správce zařízení

1. Přejděte ke službě Správce zařízení, vyberte ji a dvakrát na ni klikněte. Tím se otevře okno **Přehled** .
2. V části **Konfigurace** vyberte **přihlašovací údaje účtu úložiště** .
3. Klikněte na **Přidat**.
4. V okně **Přidat účet úložiště** udělejte toto:
   
   1. V **předplatném** vyberte **aktuální**.
   
   2. Zadejte název svého účtu úložiště Azure.
   
   3. Pokud chcete vytvořit zabezpečený kanál pro síťovou komunikaci mezi zařízením StorSimple a cloudem, vyberte **Povolit** . Vyberte možnost **Zakázat** pouze v případě, že pracujete v rámci privátního cloudu.
   
   4. Klikněte na **Přidat**. Po úspěšném vytvoření účtu úložiště se zobrazí oznámení.<br></br>
   
      ![Přidat existující přihlašovací údaje účtu úložiště](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Další krok

Dalším krokem je zřídit virtuální počítač pro virtuální pole StorSimple. V závislosti na vašem hostitelském operačním systému najdete podrobné pokyny v jednom z následujících témat:

* [Zřízení virtuálního pole StorSimple v Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [Zřízení virtuálního pole StorSimple ve VMware](storsimple-virtual-array-deploy2-provision-vmware.md)

