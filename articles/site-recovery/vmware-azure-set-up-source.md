---
title: Nastavení zdrojového prostředí pro VMware pro replikaci Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak vytvořit místní prostředí pro replikaci virtuálních počítačů VMware do Azure pomocí Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 7d81b7f0d84eb8751e4e95a31be58ed49a96bb2d
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834989"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Nastavení zdrojového prostředí pro VMware pro replikaci Azure

Tento článek popisuje, jak nastavit místní vaše zdrojové prostředí pro replikaci virtuálních počítačů VMware do Azure. Její součástí jsou kroky pro výběr vašeho scénáře replikace, nastavení místního počítače jako konfigurační server Site Recovery, a automaticky zjišťování místních virtuálních počítačů. 

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste již:

- Plánované nasazení díky [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md). To vám umožní přidělit dostatečnou šířku pásma v závislosti na vaší denní frekvenci změn dat, abyste splňovat váš požadovaný cíl bodu obnovení (RPO).
- [Nastavení prostředků](tutorial-prepare-azure.md) v [webu Azure portal](http://portal.azure.com).
- [Nastavení VMware v místním prostředí](vmware-azure-tutorial-prepare-on-premises.md), včetně vyhrazený účet pro automatické zjišťování.

## <a name="choose-your-protection-goals"></a>Volba cílů ochrany

1. V okně **Trezory Recovery Services** vyberte název trezoru. Pro tento scénář používáme **ContosoVMVault**.
2. V části **Začínáme** vyberte Site Recovery. Pak vyberte **Příprava infrastruktury**.
3. V části **Cíl ochrany** > **Kde jsou vaše počítače umístěné?** vyberte **Místní**.
4. V části **Kam chcete počítače replikovat?** vyberte **Do Azure**.
5. V části **Máte počítače ve virtuální podobě?** vyberte **Ano, s VMware vSphere Hypervisor**. Pak vyberte **OK**.

## <a name="set-up-the-configuration-server"></a>Nastavení konfigurace serveru

Prostřednictvím šablonu otevřít virtualizace aplikace OVA () můžete nastavit konfigurační server jako VMware v místním prostředí virtuálních počítačů. [Další informace](concepts-vmware-to-azure-architecture.md) o součástech, které se nainstalují na virtuální počítač VMware.

1. Další informace o [požadavky](vmware-azure-deploy-configuration-server.md#prerequisites) pro nasazení konfiguračního serveru.
2. [Zkontrolujte kapacitu čísla](vmware-azure-deploy-configuration-server.md#capacity-planning) pro nasazení.
3. [Stáhněte si](vmware-azure-deploy-configuration-server.md#download-the-template) a [importovat](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) šablony OVA nastavení VMware v místním prostředí virtuálního počítače, na kterém běží konfigurační server. Licence, které jsou poskytovány s šablonou je o zkušební licenci a je platný po dobu 180 dnů. Po uplynutí této doby zákazník potřebuje aktivovat systém windows s licencí opatřené.
4. Zapněte požadovaný virtuální počítač VMware a [ho zaregistrovat](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) v služby Recovery Services vault.

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>Azure Site Recovery vyloučení složek z antivirového programu

### <a name="if-antivirus-software-is-active-on-source-machine"></a>Pokud je aktivní na zdrojovém počítači antivirový software

Pokud zdrojový počítač má aktivní antivirový software, by se měly vyloučit složku instalace. Ano, vyloučit složky *C:\ProgramData\ASR\agent* hladký průběh replikace.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>Pokud Software antivirové ochrany v programu je aktivní na konfiguračním serveru

Vyloučit následující složky z antivirového softwaru pro hladký průběh replikace a aby se zabránilo problémům s připojením

- C:\Program Files\Microsoft Azure Recovery Services Agent.
- C:\Program Files\Microsoft Azure Site Recovery Provider
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manageru 
- Kolekce chyb nástroj pro C:\Program Files\Microsoft Azure Site Recovery 
 - C:\thirdparty
 - C:\Temp
 - C:\strawberry
 - C:\ProgramData\MySQL
 - Soubory (x86) \MySQL C:\Program
 - C:\ProgramData\ASR
 - C:\ProgramData\Microsoft Azure Site Recovery
 - C:\ProgramData\ASRLogs
 - C:\ProgramData\ASRSetupLogs
 - C:\ProgramData\LogUploadServiceLogs
 - C:\Inetpub
 - Instalační adresář serveru Azure Site Recovery. Příklad: soubory E:\Program (x86) \Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>Pokud Software antivirové ochrany v programu je aktivní na horizontální navýšení kapacity zpracování server nebo hlavní cíl

Vyloučit následující složky z antivirového softwaru

1. Agent C:\Program Files\Microsoft Azure Recovery Services
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. Proces serveru instalační adresář, například s vyrovnáváním zatížení Azure Site Recovery: C:\Program Files (x86) \Microsoft Azure Site Recovery

## <a name="common-issues"></a>Běžné problémy
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Další postup
[Nastavení cílového prostředí](./vmware-azure-set-up-target.md) 
