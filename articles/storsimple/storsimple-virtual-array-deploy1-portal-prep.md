---
title: Příprava portálu pro StorSimple Virtual Array | Dokumentace Microsoftu
description: První kurz k nasazení virtuálních polí StorSimple spočívá v přípravě na webu Azure portal
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 68a4cfd3-94c9-46cb-805c-46217290ce02
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/14/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6685c5ab7768176a0c8e7084c8512d5345732d9a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38477920"
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>Nasazení virtuálních polí StorSimple – Příprava na webu Azure portal

![](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)
## <a name="overview"></a>Přehled

Toto je první článek v sérii kurzů nasazení, které jsou nutné k úplné nasazení vaše virtuální pole jako souborový server nebo server iSCSI pomocí modelu Resource Manager. Tento článek popisuje přípravu potřebné k vytvoření a konfiguraci služby Správce zařízení StorSimple před zřízení virtuálního pole. Tento článek taky obsahuje odkazy na kontrolní seznam konfigurace nasazení a konfigurace požadavky.

K dokončení této instalace a procesu konfigurace potřebujete oprávnění správce. Doporučujeme, abyste si kontrolní seznam konfigurace nasazení, než začnete. Příprava portálu trvá méně než 10 minut.

Informace publikované v tomto článku se vztahují k nasazení virtuálních polí StorSimple webu Azure portal a v cloudu Microsoft Azure Government.

### <a name="get-started"></a>Začínáme
Pracovní postup nasazení se skládá z přípravu na portálu, zřízení virtuálního pole ve virtualizovaném prostředí a dokončení instalace. Chcete-li začít s nasazením StorSimple Virtual Array jako souborový server nebo iSCSI server, najdete v následující tabulce zdroje.

#### <a name="deployment-articles"></a>Články nasazení

Nasadit StorSimple Virtual Array, naleznete v následujících článcích v předepsaném pořadí.

| **#** | **V tomto kroku** | **To provedete...** | **A pomocí těchto dokumentů.** |
| --- | --- | --- | --- |
| 1. |**Nastavit na webu Azure portal** |Vytvoření a konfigurace služby Správce zařízení StorSimple před zřizování StorSimple Virtual Array. |[Příprava portálu](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**Zřízení virtuálního pole** |Pro Hyper-V zřídit a připojte se k StorSimple Virtual Array v hostitelském systému na Windows Server 2012 R2, Windows Server 2012 nebo Windows Server 2008 R2 s technologií Hyper-V. <br></br> <br></br> Pro replikaci z VMware zřídit a připojte se k StorSimple Virtual Array v hostitelském systému, s VMware ESXi 5.0, 5.5 a 6.0.<br></br> |[Zřízení virtuálního pole v Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [Zřízení virtuálního pole v prostředí VMware](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**Nastavit virtuální pole** |Pro souborový server se provést počáteční nastavení, zaregistrujte StorSimple souborového serveru a dokončete nastavování zařízení. Potom můžete zřídit sdílené složky protokolu SMB. <br></br> <br></br> Pro server iSCSI provést počáteční nastavení, zaregistrujte serveru iSCSI StorSimple a dokončete nastavování zařízení. Potom můžete zřídit svazky iSCSI. |[Nastavit virtuální pole jako souborový server](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Nastavit virtuální pole jako serveru iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md) |

Teď můžete začít nastavit na webu Azure portal.

## <a name="configuration-checklist"></a>Kontrolní seznam konfigurace

Kontrolní seznam konfigurace popisuje informace, které je třeba shromáždit před konfigurací softwaru na StorSimple Virtual Array. Připravuje se tyto informace předem pomáhá zjednodušit proces nasazení zařízení StorSimple ve vašem prostředí. Podle toho, jestli je nasazená StorSimple Virtual Array jako souborový server nebo iSCSI server, budete potřebovat následující kontrolní seznamy.

* Stáhněte si [StorSimple Virtual Array soubor serveru kontrolní seznam konfigurace](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).
* Stáhněte si [StorSimple Virtual Array iSCSI kontrolní seznam konfigurace serveru](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Požadavky

Tady najdete požadavky na konfiguraci služby Správce zařízení StorSimple, StorSimple Virtual Array a síti datového centra.

### <a name="for-the-storsimple-device-manager-service"></a>Služba Správce zařízení StorSimple

Než začnete, ujistěte se, že:

* Máte účet Microsoft a přihlašovací údaje účtu.
* Máte účet služby Microsoft Azure Storage a přihlašovací údaje účtu.
* Vaše předplatné Microsoft Azure musí být aktivována pro službu Správce zařízení StorSimple.

### <a name="for-the-storsimple-virtual-array"></a>Pro StorSimple Virtual Array

Před nasazením virtuálního pole, ujistěte se, že:

* Máte přístup k systému hostitele s technologií Hyper-V v systému Windows Server 2008 R2 nebo novější nebo VMware (ESXi 5.0, 5.5 a 6.0), kterou lze použít na základě zařízení.
* Systém hostitele je možné vyhradit následující prostředky pro zřízení virtuálního pole:
  
  * Minimálně 4 jádra.
  * Minimálně 8 GB paměti RAM. Pokud chcete nakonfigurovat virtuální pole jako souborový server, podporuje 8 GB 2 miliony souborů. Je třeba 16 GB paměti RAM pro podporu 2 – 4 miliony souborů.
  * Jedno síťové rozhraní.
  * 500 GB virtuální disk pro data systému.

### <a name="for-the-datacenter-network"></a>Pro síť datových center

Než začnete, ujistěte se, že:

* Sítě ve vašem datovém centru, je nakonfigurovaný podle požadavků na síť pro zařízení StorSimple. Další informace najdete v tématu [požadavky systému virtuálních polí StorSimple](storsimple-ova-system-requirements.md).
* StorSimple Virtual Array má vyhrazenou šířku pásma 5 MB/s sítě Internet (nebo více) vždy k dispozici. Tuto šířku pásma by neměly sdílet s jinými aplikacemi.

## <a name="step-by-step-preparation"></a>Krok za krokem přípravy

Pomocí následujících kroků můžete připravit na portálu služby Správce zařízení StorSimple.

## <a name="step-1-create-a-new-service"></a>Krok 1: Vytvoření nové služby

Jednu instanci služby Správce zařízení StorSimple můžete spravovat více virtuálních polí StorSimple. Pomocí následujících kroků vytvořte instanci služby Správce zařízení StorSimple. Pokud máte existující služby Správce zařízení StorSimple ke správě vaše virtuální pole, tento krok přeskočit a přejít na [krok 2: získání registračního klíče služby](#step-2-get-the-service-registration-key).

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

Proveďte následující kroky v [webu Azure portal](https://portal.azure.com/).

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> Registrační klíč služby slouží k registraci všech zařízení Správce zařízení StorSimple, které se potřebují registrovat ve službě Správce zařízení StorSimple.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>Krok 3: Stažení image virtuálního pole

Jakmile budete mít registrační klíč služby, je potřeba stáhnout bitovou kopii odpovídající virtuální pole ke zřízení virtuálního pole v systému hostitele. Image virtuálního pole jsou specifické pro operační systém a můžete stáhnout ze stránky s rychlým startem na webu Azure Portal.

> [!IMPORTANT]
> Software pro StorSimple Virtual Array může použít jenom ve službě Správce zařízení StorSimple.
> 
> 

Proveďte následující kroky v [webu Azure portal](https://portal.azure.com/).

#### <a name="to-get-the-virtual-array-image"></a>Chcete-li získat image virtuálního pole

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/). 
2. Na webu Azure Portal, klikněte na tlačítko **procházet > Správce zařízení StorSimple**.
3. Vyberte existující službu Správce zařízení StorSimple. V **Správce zařízení StorSimple** okna, klikněte na tlačítko **rychlý Start**. 
4. Klikněte na odkaz odpovídající bitovou kopii, kterou chcete stáhnout z webu Microsoft Download Center. Soubory obrázků jsou přibližně 4,8 GB.
   
   * VHDX pro Hyper-V ve Windows serveru 2012 a novějších verzích
   * Virtuální pevný disk pro technologii Hyper-V ve Windows serveru 2008 R2 a novějších verzích
   * VMDK pro VMWare ESXi 5.0, 5.5 a 6.0
5. Stáhněte a rozbalte soubor na místní disk, a poznamenejte si kde je umístěn soubor rozbaleny.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Volitelný krok: Nakonfigurujte nový účet úložiště pro službu

Tento krok je volitelný a by provádět pouze v případě, že jste nepovolili automatické vytvoření účtu úložiště s vaší službou.

Pokud potřebujete k vytvoření účtu služby Azure storage v jiné oblasti, naleznete v tématu [způsob vytvoření účtu úložiště](../storage/common/storage-create-storage-account.md#create-a-storage-account) podrobné pokyny.

Proveďte následující kroky v [webu Azure portal](https://ms.portal.azure.com/) na stránce služby StorSimple Device Manager přidat existující účet úložiště Microsoft Azure.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Chcete-li přidat přihlašovací údaje účtu úložiště, který má stejné předplatné Azure jako službu Správce zařízení

1. Přejděte do služby Správce zařízení, vyberte a dvojím kliknutím. Tím se otevře **přehled** okno.
2. Vyberte **přihlašovací údaje účtu úložiště** v rámci **konfigurace** oddílu.
3. Klikněte na tlačítko **Add** (Přidat).
4. V **přidání účtu úložiště** okno, postupujte takto:
   
    1. Pro **předplatné**vyberte **aktuální**.
   
    2. Zadejte název účtu služby Azure storage.
   
    3. Vyberte **povolit** vytvořit zabezpečený kanál pro síťovou komunikaci mezi zařízením StorSimple a cloudu. Vyberte **zakázat** pouze v případě, že pracujete v privátním cloudu.
   
    4. Klikněte na tlačítko **Add** (Přidat). Po úspěšném vytvoření účtu úložiště, se zobrazí oznámení.<br></br>
   
     ![Přidat existující přihlašovací údaje účtu úložiště](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Další krok

Dalším krokem je zřízení virtuálního počítače pro StorSimple Virtual Array. V závislosti na hostitelském operačním systému najdete v podrobných pokynů v:

* [Zřízení virtuálního pole StorSimple v Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [Zřízení virtuálního pole StorSimple v prostředí VMware](storsimple-virtual-array-deploy2-provision-vmware.md)

