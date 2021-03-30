---
title: Nastavení zdrojů pro zotavení po havárii VMware do Azure pomocí Azure Site Recovery
description: Tento článek popisuje, jak nastavit místní prostředí pro replikaci virtuálních počítačů VMware do Azure pomocí Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: afd3979690b8952c915a49099ee04b3d416031fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88189726"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Nastavení zdrojového prostředí pro replikaci z VMware do Azure

Tento článek popisuje, jak nastavit zdrojové místní prostředí pro replikaci virtuálních počítačů VMware do Azure. Tento článek obsahuje kroky pro výběr scénáře replikace, nastavení místního počítače jako konfiguračního serveru Site Recovery a automatické zjišťování místních virtuálních počítačů.

## <a name="prerequisites"></a>Požadavky

Článek předpokládá, že jste již:

- Naplánovalo se nasazení pomocí [Plánovač nasazení služby Azure Site Recovery](site-recovery-deployment-planner.md). To vám pomůže přidělit dostatečnou šířku pásma na základě denní frekvence změny dat, aby splňovala požadovaný cíl bodu obnovení (RPO).
- [Nastavte prostředky](tutorial-prepare-azure.md) v [Azure Portal](https://portal.azure.com).
- [Nastavte místní VMware](vmware-azure-tutorial-prepare-on-premises.md), včetně vyhrazeného účtu pro automatické zjišťování.

## <a name="choose-your-protection-goals"></a>Výběr cílů ochrany

1. V okně **Trezory Recovery Services** vyberte název trezoru. Pro tento scénář používáme **ContosoVMVault**.
2. V části **Začínáme** vyberte Site Recovery. Pak vyberte **Příprava infrastruktury**.
3. V nabídce **cíl ochrany**  >  ,**kde jsou umístěny vaše počítače**, vyberte možnost **místní**.
4. V části **Kam chcete počítače replikovat?** vyberte **Do Azure**.
5. V části **Máte počítače ve virtuální podobě?** vyberte **Ano, s VMware vSphere Hypervisor**. Pak vyberte **OK**.

## <a name="set-up-the-configuration-server"></a>Nastavení konfiguračního serveru

Konfigurační server můžete nastavit jako místní virtuální počítač VMware prostřednictvím šablony aplikace virtualizace (Open Virtualization Application). [Přečtěte si další informace](./vmware-azure-architecture.md) o součástech, které se nainstalují na virtuálním počítači VMware.

1. Přečtěte si informace o [požadavcích](vmware-azure-deploy-configuration-server.md#prerequisites) pro nasazení konfiguračního serveru.
2. [Ověřte čísla kapacity](vmware-azure-deploy-configuration-server.md#sizing-and-capacity-requirements) pro nasazení.
3. [Stáhněte](vmware-azure-deploy-configuration-server.md#download-the-template) a [importujte](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) šablonu vajíček a nastavte místní virtuální počítač VMware, který spouští konfigurační server. Licence, která je součástí šablony, je zkušební licence a platí po dobu 180 dnů. Po tomto období musí zákazník aktivovat Windows pomocí předem získané licence.
4. Zapněte virtuální počítač VMware a [Zaregistrujte ho](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) v úložišti Recovery Services.

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>Azure Site Recovery vyloučení složky z antivirového programu

### <a name="if-antivirus-software-is-active-on-source-machine"></a>Pokud je na zdrojovém počítači aktivní antivirový software

Pokud má zdrojový počítač aktivní antivirový software, měla by být vyloučena instalační složka. Proto vylučte složku *C:\ProgramData\ASR\agent* pro zajištění hladké replikace.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>Pokud je antivirový software na konfiguračním serveru aktivní

Vyloučení následujících složek z antivirového softwaru pro zajištění hladké replikace a předcházení problémům s připojením

- C:\Program Files\Microsoft Azure Recovery Services agent.
- C:\Program Files\Microsoft Azure Site Recovery poskytovatel
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manager 
- C:\Program Files\Microsoft Azure Site Recovery – Nástroj pro shromažďování chyb 
  - C:\thirdparty
  - C:\Temp
  - C:\strawberry
  - C:\ProgramData\MySQL
  - C:\Program Files (x86) \MySQL
  - C:\ProgramData\ASR
  - C:\ProgramData\Microsoft Azure Site Recovery
  - C:\ProgramData\ASRLogs
  - C:\ProgramData\ASRSetupLogs
  - C:\ProgramData\LogUploadServiceLogs
  - C:\inetpub
  - Instalační adresář serveru Site Recovery. Příklad: E:\Program Files (x86) \Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>Pokud je antivirový software aktivní na procesovém serveru nebo hlavním cíli s možností horizontálního rozšíření kapacity

Vyloučit následující složky z antivirového softwaru

1. C:\Program Files\Microsoft Azure Recovery Services Agent
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. Azure Site Recovery instalačního adresáře s vyrovnáváním zatížení pro procesový Server, například: C:\Program Files (x86) \Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-the-linux-master-target"></a>Pokud je antivirový software v hlavním cíli systému Linux aktivní,

Vyloučit následující složky z antivirového softwaru

1.  /usr/local/ASR
2.  /usr/local/InMage
3.  /var/log/vxlogs
4.  /var/log
5.  /var/log/ApplicationPolicyLogs
6.  /var/log/ASRsetuptelemetry
7.  /var/log/ASRsetuptelemetry_uploaded


## <a name="next-steps"></a>Další kroky
[Nastavení cílového prostředí](./vmware-azure-set-up-target.md) 
