---
title: Příprava portálu aplikace pro pole virtuální zařízení StorSimple | Microsoft Docs
description: První kurz k nasazení pole virtuální zařízení StorSimple spočívá v přípravě portálu Azure
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
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2017
ms.locfileid: "24526549"
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>Nasazení pole virtuálního zařízení StorSimple – Příprava portálu Azure

![](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)
## <a name="overview"></a>Přehled

Toto je první článku řady kurzy nasazení, které jsou potřebné k nasazení zcela virtuální pole jako souborový server nebo server se službou iSCSI pomocí modelu Resource Manager. Tento článek popisuje přípravu potřebné k vytvoření a konfiguraci služby StorSimple Manager zařízení před zřizování virtuální pole. Tento článek taky obsahuje odkazy na kontrolní seznam konfigurace nasazení a konfigurace požadované součásti.

K dokončení této instalace a procesu konfigurace potřebujete oprávnění správce. Doporučujeme, abyste si prošli kontrolní seznam konfigurace nasazení před zahájením. Příprava portálu trvá méně než 10 minut.

Informace zveřejněné v tomto článku se vztahují na nasazení pole virtuální zařízení StorSimple v portálu Azure a cloudu Microsoft Azure Government.

### <a name="get-started"></a>Začínáme
Pracovní postup nasazení se skládá z přípravy na portálu, zřizování virtuální pole ve virtualizovaném prostředí a dokončení instalace. Chcete-li začít s nasazením pole virtuální zařízení StorSimple jako souborový server nebo server se službou iSCSI, naleznete v následující tabulce materiálech.

#### <a name="deployment-articles"></a>Články nasazení

Nasadit pole virtuální zařízení StorSimple, naleznete v následujících článcích v předepsaných pořadí.

| **#** | **V tomto kroku** | **To uděláte...** | **A použijte tyto dokumenty.** |
| --- | --- | --- | --- |
| 1. |**Nastavení portálu Azure** |Vytvoření a konfigurace služby StorSimple Manager zařízení před zřizování pole virtuální zařízení StorSimple. |[Příprava na portálu](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**Zřizování virtuální pole** |Pro Hyper-V zřizování a připojte se k poli virtuální zařízení StorSimple v systému hostitele s technologií Hyper-V v systému Windows Server 2012 R2, Windows Server 2012 nebo Windows Server 2008 R2. <br></br> <br></br> Pro VMware zřizování a připojte se k poli virtuální zařízení StorSimple v hostitelském systému, systémem VMware ESXi 5.0, 5.5 nebo 6.0.<br></br> |[Zřídit virtuální pole Hyper-v](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [Zřídit virtuální pole v prostředí VMware](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**Nastavit virtuální pole** |Pro souborový server provedení počáteční instalace, zaregistrujte zařízení StorSimple souborového serveru a dokončení instalace zařízení. Potom můžete zřídit sdílené složky protokolu SMB. <br></br> <br></br> Pro váš server iSCSI provedení počáteční instalace, zaregistrujte serveru iSCSI StorSimple a dokončení instalace zařízení. Potom můžete zřídit svazky iSCSI. |[Nastavit virtuální pole jako souborový server](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Nastavit virtuální pole jako iSCSI server](storsimple-virtual-array-deploy3-iscsi-setup.md) |

Teď můžete začít nastavení portálu Azure.

## <a name="configuration-checklist"></a>Kontrolní seznam konfigurace

Kontrolní seznam konfigurace popisuje informace, které je třeba shromáždit před konfigurací softwaru na pole virtuální zařízení StorSimple. Příprava předem tyto informace pomáhají zjednodušit proces nasazení zařízení StorSimple ve vašem prostředí. V závislosti na tom, jestli je vaše pole virtuální zařízení StorSimple nasazený jako souborový server nebo server se službou iSCSI, budete potřebovat následující kontrolní seznamy.

* Stažení [StorSimple kontrolní seznam konfigurace virtuální pole souboru](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).
* Stažení [pole virtuální zařízení StorSimple iSCSI kontrolní seznam konfigurace serveru](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Požadavky

Zde zjistíte požadavky konfigurace služby StorSimple Manager zařízení StorSimple virtuální pole a síti datového centra.

### <a name="for-the-storsimple-device-manager-service"></a>Služba Správce zařízení StorSimple

Než začnete, ujistěte se, že:

* Máte účet Microsoft a přihlašovací údaje účtu.
* Máte účet služby Microsoft Azure Storage a přihlašovací údaje účtu.
* Vaše předplatné Microsoft Azure by měl povolit pro službu StorSimple Manager zařízení.

### <a name="for-the-storsimple-virtual-array"></a>Pro pole virtuální zařízení StorSimple

Před nasazením virtuální pole, ujistěte se, že:

* Máte přístup k systému hostitele s technologií Hyper-V v systému Windows Server 2008 R2 nebo novější nebo VMware (ESXi 5.0, 5.5 nebo 6.0), kterou lze použít k zřízení zařízení.
* Systém hostitele je možné vyhradit následující prostředky pro zřízení virtuální pole:
  
  * Minimálně 4 jádra.
  * Alespoň 8 GB paměti RAM. Pokud chcete konfigurovat virtuální pole jako souborový server, 8 GB podporuje 2 miliony souborů. Je nutné 16 GB paměti RAM pro podporu 2 – 4 miliony souborů.
  * Jedno síťové rozhraní.
  * 500 GB virtuální disk pro data systému.

### <a name="for-the-datacenter-network"></a>Pro síť datového centra

Než začnete, ujistěte se, že:

* Síť ve vašem datovém centru je nakonfigurovaná podle síťové požadavky pro zařízení StorSimple. Další informace najdete v tématu [StorSimple virtuální požadavky na systém pole](storsimple-ova-system-requirements.md).
* Pole virtuální zařízení StorSimple nemá vyhrazené 5 MB/s šířky pásma Internetu (nebo má více) vždy k dispozici. Tato šířky pásma nesmí sdílet s jinými aplikacemi.

## <a name="step-by-step-preparation"></a>Krok za krokem přípravy

Použijte následující podrobné pokyny k přípravě portálu pro službu Správce zařízení StorSimple.

## <a name="step-1-create-a-new-service"></a>Krok 1: Vytvoření nové služby

Jednu instanci služby StorSimple Manager zařízení můžete spravovat několik polí virtuální zařízení StorSimple. Pomocí následujících kroků vytvořte instanci služby Správce zařízení StorSimple. Pokud máte existující službu StorSimple Manager zařízení spravovat vaše virtuální pole, přeskočte tento krok a přejděte k [krok 2: získání registračního klíče služby](#step-2-get-the-service-registration-key).

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

Proveďte následující kroky v [portál Azure](https://portal.azure.com/).

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> Registrační klíč služby slouží k registraci všech zařízení StorSimple Manager zařízení, které potřebují registrovat ve službě StorSimple Manager zařízení.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>Krok 3: Stáhnout bitovou kopii virtuálního pole

Až budete mít registrační klíč služby, musíte stáhnout bitovou kopii odpovídající virtuální pole ke zřízení virtuálních pole v systému hostitele. Image virtuálního pole jsou specifické pro operační systém a lze ji stáhnout ze stránky rychlý Start na portálu Azure.

> [!IMPORTANT]
> Software spuštěný na poli virtuální zařízení StorSimple lze použít pouze se službou Správce zařízení StorSimple.
> 
> 

Proveďte následující kroky v [portál Azure](https://portal.azure.com/).

#### <a name="to-get-the-virtual-array-image"></a>Chcete-li získat bitovou kopii virtuálního pole

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/). 
2. Na portálu Azure klikněte na tlačítko **procházet > Správci zařízení StorSimple**.
3. Vyberte existující službu StorSimple Manager zařízení. V **Manager zařízení StorSimple** okně klikněte na tlačítko **rychlý Start**. 
4. Kliknutím na odkaz odpovídající bitovou kopii, kterou chcete stáhnout z webu Microsoft Download Center. Soubory image jsou přibližně 4,8 GB.
   
   * VHDX pro Hyper-V v systému Windows Server 2012 a novější
   * Virtuální pevný disk pro technologii Hyper-V v systému Windows Server 2008 R2 a novější
   * VMDK pro VMWare ESXi 5.0, 5.5 nebo 6.0
5. Stáhněte a rozbalte soubor na místní disk, což poznámku o kterém se nachází soubor rozbalené.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Volitelný krok: Konfigurace nového účtu úložiště pro službu

Tento krok je volitelný a musí provést pouze v případě, že jste nepovolili automatické vytvoření účtu úložiště s vaší služby.

Pokud potřebujete vytvořit účet úložiště Azure v jiné oblasti, přečtěte si téma [postup vytvoření účtu úložiště](../storage/common/storage-create-storage-account.md#create-a-storage-account) podrobné pokyny.

Proveďte následující kroky v [portál Azure](https://ms.portal.azure.com/) na stránce služby StorSimple Manager zařízení přidat existující účet úložiště Microsoft Azure.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Chcete-li přidat přihlašovací údaje účtu úložiště, který má stejné předplatné jako služba Správce zařízení

1. Přejděte do vaší služby Správce zařízení, vyberte a dvakrát na ni klikněte. Tím se otevře **přehled** okno.
2. Vyberte **přihlašovacích údajů účtu úložiště** v rámci **konfigurace** části.
3. Klikněte na tlačítko **Přidat**.
4. V **přidání účtu úložiště** okno, postupujte takto:
   
    1. Pro **předplatné**, vyberte **aktuální**.
   
    2. Zadejte název účtu úložiště Azure.
   
    3. Vyberte **povolit** vytvořit zabezpečený kanál pro síťovou komunikaci mezi zařízení StorSimple a cloudem. Vyberte **zakázat** pouze v případě, že pracujete v privátním cloudu.
   
    4. Klikněte na tlačítko **Přidat**. Upozornění se zobrazí po úspěšném vytvoření účtu úložiště.<br></br>
   
     ![Přidat existující přihlašovací údaje účtu úložiště](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Další krok

Dalším krokem je zřízení virtuálního počítače pro pole virtuální zařízení StorSimple. V závislosti na operačním systému hostitele najdete podrobné pokyny v:

* [Zřídit o virtuální zařízení StorSimple pole technologie Hyper-v](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [Zřídit o virtuální zařízení StorSimple pole v prostředí VMware](storsimple-virtual-array-deploy2-provision-vmware.md)

