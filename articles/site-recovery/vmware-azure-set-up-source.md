---
title: Nastavení zdrojové prostředí pro VMware do Azure s replikací pomocí Azure Site Recovery | Microsoft Docs
description: Tento článek popisuje, jak nastavit v místním prostředí replikace virtuálních počítačů VMware do Azure s Azure Site Recovery.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: b2c564e8d49e39d9cdc09d3fe168388d579de70e
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
ms.locfileid: "29812653"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Nastavení zdrojové prostředí pro VMware do Azure replikace

Tento článek popisuje, jak nastavit místní vaše zdrojové prostředí pro replikaci virtuálních počítačů VMware do Azure. Její součástí jsou kroky pro výběr váš scénář replikace, nastavení se místní počítač jako server konfigurace Site Recovery, a automaticky zjišťování místních virtuálních počítačů. 

## <a name="prerequisites"></a>Požadavky

Článek předpokládá, že již máte:
- [Nastavení prostředků](tutorial-prepare-azure.md) v [portál Azure](http://portal.azure.com).
- [Nastavit místní VMware](vmware-azure-tutorial-prepare-on-premises.md), včetně vyhrazený účet pro automatické zjišťování.



## <a name="choose-your-protection-goals"></a>Volba cílů ochrany

1. V portálu Azure přejděte do **služeb zotavení** okno trezoru a vyberte svůj trezor.
2. V nabídce prostředků úložiště, přejděte na **Začínáme** > **Site Recovery** > **krok 1: připravte infrastrukturu** > **cíl ochrany**.

    ![Zvolte cíle.](./media/vmware-azure-set-up-source/choose-goals.png)
3. V **cíl ochrany**, vyberte **do Azure**a zvolte **Ano, s hypervisoru VMware vSphere**. Pak klikněte na **OK**.

    ![Zvolte cíle.](./media/vmware-azure-set-up-source/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>Nastavení konfigurace serveru

Nastavení konfigurace serveru jako místní virtuálních počítačů VMware, použijte šablonu otevřete virtualizace formát OVF (). [Další informace](concepts-vmware-to-azure-architecture.md) o součástech, které se nainstalují na virtuální počítač VMware. 

1. Další informace o [požadavky](vmware-azure-deploy-configuration-server.md#prerequisites) pro nasazení konfiguračního serveru.
2. [Zkontrolujte kapacitu čísla](vmware-azure-deploy-configuration-server.md#capacity-planning) pro nasazení.
3. [Stáhněte si](vmware-azure-deploy-configuration-server.md#download-the-template) a [importovat](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) OVF šablony (how-k-nasazení konfigurace server.md) nastavit VMware místní virtuální počítač, který běží na konfiguračním serveru.
4. Zapnout virtuální počítač VMware a [registraci](vmware-azure-deploy-configuration-server.md#register-the-configuration-server) v služeb zotavení trezoru.


## <a name="add-the-vmware-account-for-automatic-discovery"></a>Přidejte účet VMware pro automatické zjišťování

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>Připojení k serveru VMware

Povolit Azure Site Recovery se zjistit virtuální počítače spuštěné v místním prostředí, kterou potřebujete připojit VMware vCenter Server nebo hostitelů vSphere ESXi službou Site Recovery.

Vyberte **+ vCenter** zahájíte připojení serveru VMware vCenter server nebo hostiteli ESXi VMware vSphere.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Běžné problémy
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Další postup
[Nastavení cílového prostředí](./vmware-azure-set-up-target.md) v Azure.
