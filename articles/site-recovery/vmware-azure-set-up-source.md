---
title: Nastavení zdrojového prostředí pro VMware pro replikaci Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak vytvořit místní prostředí pro replikaci virtuálních počítačů VMware do Azure pomocí Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: ramamill
ms.openlocfilehash: 1380c1bc820a815fae317a86fcd0ee4f46dd9aa5
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952650"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Nastavení zdrojového prostředí pro VMware pro replikaci Azure

Tento článek popisuje, jak nastavit místní vaše zdrojové prostředí pro replikaci virtuálních počítačů VMware do Azure. Její součástí jsou kroky pro výběr vašeho scénáře replikace, nastavení místního počítače jako konfigurační server Site Recovery, a automaticky zjišťování místních virtuálních počítačů. 

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste již:
- [Nastavení prostředků](tutorial-prepare-azure.md) v [webu Azure portal](http://portal.azure.com).
- [Nastavení VMware v místním prostředí](vmware-azure-tutorial-prepare-on-premises.md), včetně vyhrazený účet pro automatické zjišťování.



## <a name="choose-your-protection-goals"></a>Volba cílů ochrany

1. Na webu Azure Portal, přejděte **služby Recovery Services** okně trezoru a vyberte svůj trezor.
2. V nabídce prostředků trezoru, přejděte na **Začínáme** > **Site Recovery** > **krok 1: připravte infrastrukturu**  >  **Cíl ochrany**.

    ![Zvolte cíle.](./media/vmware-azure-set-up-source/choose-goals.png)
3. V **cíl ochrany**vyberte **do Azure**a zvolte **Ano, s VMware vSphere Hypervisor**. Pak klikněte na **OK**.

    ![Zvolte cíle.](./media/vmware-azure-set-up-source/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>Nastavení konfigurace serveru

Prostřednictvím šablonu otevřít virtualizace aplikace OVA () můžete nastavit konfigurační server jako VMware v místním prostředí virtuálních počítačů. [Další informace](concepts-vmware-to-azure-architecture.md) o součástech, které se nainstalují na virtuální počítač VMware.

1. Další informace o [požadavky](vmware-azure-deploy-configuration-server.md#prerequisites) pro nasazení konfiguračního serveru.
2. [Zkontrolujte kapacitu čísla](vmware-azure-deploy-configuration-server.md#capacity-planning) pro nasazení.
3. [Stáhněte si](vmware-azure-deploy-configuration-server.md#download-the-template) a [importovat](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) šablony OVA nastavení VMware v místním prostředí virtuálního počítače, na kterém běží konfigurační server. Licence, které jsou poskytovány s šablonou je o zkušební licenci a je platný po dobu 180 dnů. Po uplynutí této doby zákazník potřebuje aktivovat systém windows s licencí opatřené.
4. Zapněte požadovaný virtuální počítač VMware a [ho zaregistrovat](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) v služby Recovery Services vault.


## <a name="add-the-vmware-account-for-automatic-discovery"></a>Přidání účtu VMware pro automatické zjišťování

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>Připojení k serveru VMware

Pokud chcete povolit Azure Site Recovery za účelem zjištění virtuálních počítačů spuštěných ve vašem místním prostředí, musíte pro připojení k serveru VMware vCenter nebo hostiteli vSphere ESXi za pomocí Site Recovery.

Vyberte **+ vCenter** spustit připojení serveru VMware vCenter nebo hostitele VMware vSphere ESXi.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Běžné problémy
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Další postup
[Nastavení cílového prostředí](./vmware-azure-set-up-target.md) v Azure.
