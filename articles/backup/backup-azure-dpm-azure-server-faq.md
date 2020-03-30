---
title: Nejčastější dotazy k serveru Azure Backup Server a DPM
description: V tomto článku naleznete odpovědi na běžné otázky týkající se Microsoft Azure Backup Server (MABS) a DPM (Správce ochrany dat).
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/05/2019
ms.openlocfilehash: 35957a1e8a3d6c3d9be06d9d44dbcd47efa0e6ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173163"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Azure Backup Server a DPM – nejčastější dotazy

## <a name="general-questions"></a>Obecné otázky

Tento článek odpovídá na nejčastější dotazy týkající se serveru Zálohování Azure a aplikace DPM.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server"></a>Mohu použít server Azure Backup k vytvoření zálohy úplného obnovení (BMR) pro fyzický server?

Ano.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Mohu server zaregistrovat do více trezorů?

Ne. Server DPM nebo Azure Backup lze zaregistrovat pouze do jednoho trezoru.

### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Můžu použít DPM k zálohování aplikací v Azure Stacku?

Ne. Azure Backup můžete použít k ochraně Azure Stack, Azure Backup nepodporuje použití DPM k zálohování aplikací v Azure Stacku.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Pokud jsem nainstaloval agenta Azure Backup k ochraně svých souborů a složek, můžu nainstalovat System Center DPM pro zálohování místních úloh do Azure?

Ano. Ale měli byste nejdřív nastavit DPM a pak nainstalovat agenta Azure Backup.  Instalace součástí v tomto pořadí zajišťuje, že agent Azure Backup pracuje s DPM. Instalace agenta před instalací aplikace DPM není doporučena ani podporována.

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Proč nemůžu po instalaci UR7 a nejnovějšího agenta Azure Backup přidat externí server DPM?

U serverů DPM se zdroji dat, které jsou chráněné do cloudu (pomocí kumulativní aktualizace starší než kumulativní aktualizace 7), musíte počkat alespoň jeden den po instalaci agenta UR7 a nejnovějšího agenta Azure Backup, abyste spustili **přidat externí server DPM**. K nahrání metadat skupin ochrany DPM do Azure je potřeba jednodenní časové období. Metadata skupiny ochrany se nahrají poprvé prostřednictvím noční úlohy.

## <a name="vmware-and-hyper-v-backup"></a>Zálohování v systému VMware a technologie Hyper-V

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Je možné zálohovat servery VMware vCenter do Azure?

Ano. Azure Backup Server můžete použít k zálohování hostitelů VMware vCenter Server a ESXi do Azure.

- [Přečtěte si další informace](backup-mabs-protection-matrix.md) o podporovaných verzích.
- Chcete-li zálohovat server VMware, [postupujte takto.](backup-azure-backup-server-vmware.md)

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>Potřebuji samostatnou licenci k obnovení úplného místního clusteru VMware/Hyper-V?

Pro ochranu v oblasti VMware/Hyper-V nepotřebujete samostatné licence.

- Pokud jste zákazníkem System Center, použijte správce ochrany dat system center (DPM) k ochraně virtuálních měn VMware.
- Pokud nejste zákazníkem System Center, můžete použít Azure Backup Server (průběžně platby) k ochraně virtuálních měn VMware.

## <a name="sharepoint"></a>SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>Můžu obnovit položku SharePointu do původního umístění, pokud je SharePoint nakonfigurován pomocí SQL AlwaysOn (s ochranou na disku)?

Ano, položku lze obnovit na původní sharepointový web.

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>Můžu obnovit databázi SharePointu do původního umístění, pokud je SharePoint nakonfigurován pomocí SQL AlwaysOn?

Vzhledem k tomu, že databáze služby SharePoint jsou konfigurovány v sql alwayson, nelze je změnit, pokud není odebrána skupina dostupnosti. V důsledku toho aplikace DPM nemůže obnovit databázi do původního umístění. Databázi serveru SQL Server můžete obnovit do jiné instance serveru SQL Server.

## <a name="next-steps"></a>Další kroky

Přečtěte si další nejčastější dotazy:

- [Přečtěte si další informace](backup-support-matrix-mabs-dpm.md) o matici podpory Azure Backup Server a DPM.
- [Přečtěte si další informace](backup-azure-mabs-troubleshoot.md) o pokynech pro řešení potíží s Azure Backup Server a DPM.
