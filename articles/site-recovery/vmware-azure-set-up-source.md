---
title: Nastavení nastavení zdroje pro zotavení po havárii v systému VMware do Azure pomocí Azure Site Recovery
description: Tento článek popisuje, jak nastavit místní prostředí pro replikaci virtuálních počítačů VMware do Azure pomocí Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: ff01aed92669acb193ff149ea9298550134f42a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257053"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Nastavení zdrojového prostředí pro replikaci VMware do Azure

Tento článek popisuje, jak nastavit zdrojové místní prostředí pro replikaci virtuálních počítačů VMware do Azure. Článek obsahuje kroky pro výběr scénáře replikace, nastavení místního počítače jako konfiguračního serveru site recovery a automatické zjišťování místních virtuálních počítačů.

## <a name="prerequisites"></a>Požadavky

Článek předpokládá, že již máte:

- Naplánovali jste nasazení pomocí [Plánovače nasazení webu Azure .](site-recovery-deployment-planner.md) To vám pomůže přidělit dostatečnou šířku pásma na základě denní rychlosti změny dat, abyste splnili požadovaný cíl bodu obnovení (RPO).
- [Nastavte prostředky](tutorial-prepare-azure.md) na [webu Azure Portal](https://portal.azure.com).
- [Nastavte místní vmware](vmware-azure-tutorial-prepare-on-premises.md), včetně vyhrazeného účtu pro automatické zjišťování.

## <a name="choose-your-protection-goals"></a>Vyberte si cíle ochrany

1. V okně **Trezory Recovery Services** vyberte název trezoru. Pro tento scénář používáme **ContosoVMVault**.
2. V části **Začínáme** vyberte Site Recovery. Pak vyberte **Příprava infrastruktury**.
3. V **adrese Protection goal** > **Where are your machines located**vyberte **On-premises**.
4. V části **Kam chcete počítače replikovat?** vyberte **Do Azure**.
5. V části **Máte počítače ve virtuální podobě?** vyberte **Ano, s VMware vSphere Hypervisor**. Pak vyberte **OK**.

## <a name="set-up-the-configuration-server"></a>Nastavení konfiguračního serveru

Konfigurační server můžete nastavit jako místní virtuální počítač VMware prostřednictvím šablony Open Virtualization Application (OVA). [Další informace](concepts-vmware-to-azure-architecture.md) o součástech, které budou nainstalovány ve virtuálním počítači VMware.

1. Seznamte se s [požadavky](vmware-azure-deploy-configuration-server.md#prerequisites) pro nasazení konfiguračního serveru.
2. [Zkontrolujte čísla kapacity](vmware-azure-deploy-configuration-server.md#sizing-and-capacity-requirements) pro nasazení.
3. [Stáhněte](vmware-azure-deploy-configuration-server.md#download-the-template) a [importujte](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) šablonu OVA a nastavte místní virtuální počítač VMware, který spouští konfigurační server. Licence poskytnutá se šablonou je zkušební licence a je platná po dobu 180 dnů. Zaúčtovat toto období, zákazník musí aktivovat okna s pořízenou licencí.
4. Zapněte virtuální počítač VMware a [zaregistrujte ho](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) v trezoru služby Recovery Services.

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>Vyloučení složky Azure Site Recovery z antivirového programu

### <a name="if-antivirus-software-is-active-on-source-machine"></a>Pokud je antivirový software aktivní ve zdrojovém počítači

Pokud je zdrojový počítač aktivní antivirový software, instalační složka by měla být vyloučena. Proto vylučte složku *C:\ProgramData\ASR\agent* pro hladkou replikaci.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>Pokud je antivirový software aktivní na konfiguračním serveru

Vylučte následující složky z antivirového softwaru pro hladkou replikaci a zamezení problémům s připojením

- C:\Program Files\Agent služby Microsoft Azure Recovery Services.
- C:\Program Files\Zprostředkovatel obnovení webu Microsoft Azure
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manager 
- C:\Program Files\Nástroj kolekce chyb obnovení webu Microsoft Azure 
  - C:\třetí strana
  - C:\temp
  - C:\jahoda
  - C:\ProgramData\MySQL
  - C:\Programové soubory (x86)\MySQL
  - C:\ProgramData\ASR
  - C:\ProgramData\Obnovení webu Microsoft Azure
  - C:\ProgramData\ASRLogs
  - C:\ProgramData\ASRSetupLogs
  - C:\ProgramData\LogUploadServiceLogs
  - C:\inetpub
  - Instalační adresář serveru site recovery. Příklad: E:\Program Files (x86)\Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>Pokud je antivirový software aktivní na horizontálním navýšení kapacity procesního serveru/hlavního cíle

Vyloučit následující složky z antivirového softwaru

1. C:\Program Files\Agent služby Microsoft Azure Recovery Services
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Obnovení webu Microsoft Azure
7. Adresář instalace serveru s vyrovnáváním zatížení webu Azure Site Recovery, příklad: C:\Program Files (x86)\Microsoft Azure Site Recovery


## <a name="next-steps"></a>Další kroky
[Nastavení cílového prostředí](./vmware-azure-set-up-target.md) 
