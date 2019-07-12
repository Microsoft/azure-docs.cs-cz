---
title: Azure Backup serveru a aplikace DPM – nejčastější dotazy
description: 'Odpovědi na běžné dotazy týkající se: Azure Backup serveru a aplikace DPM.'
services: backup
author: srinathvasireddy
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: srinathv
ms.openlocfilehash: 7a598038ee435b67b9ad8f06bdec2490bc1c53c3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705107"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Azure Backup Server a DPM – nejčastější dotazy
Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Azure Backup serveru a aplikace DPM.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Mohu použít server Azure Backup k vytvoření zálohy úplného obnovení (BMR) pro fyzický server? <br/>
Ano.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Můžete zaregistrovat server pro více trezorů?
Ne. Na server DPM nebo Azure Backup lze zaregistrovat pouze pro jeden trezor.


### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Můžete použít aplikace DPM k zálohování aplikace ve službě Azure Stack?
Ne. Azure Backup můžete použít k ochraně služby Azure Stack, Azure Backup nepodporuje použití DPM k zálohování aplikace ve službě Azure Stack.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Pokud je nainstalován agent Azure Backup chránit soubory a složky, je možné nainstalovat System Center DPM k zálohování místních úloh do Azure?
Ano. Ale doporučujeme nejprve nastavení aplikace DPM a potom nainstalovat agenta Azure Backup.  Instalují se součásti v tomto pořadí zajistí, že Azure Backup agent bude fungovat s DPM. Instalace agenta před instalací aplikace DPM se nedoporučuje nebo nepodporuje.

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Proč nelze přidat externí DPM server po instalaci UR7 a nejnovější verzi agenta Azure Backup?
Pro servery aplikace DPM se zdroji dat, které jsou chráněné do cloudu (s použitím kumulativní aktualizace starší než kumulativní aktualizace 7), musíte počkat aspoň jeden den po instalaci UR7 a nejnovější verzi agenta Azure Backup, aby vám začali **přidat externí DPM server**. Jeden den časové období, je potřeba nahrát metadat skupiny ochrany DPM do Azure. Při prvním průchodu noční úlohu nahraje metadat skupiny ochrany.

## <a name="vmware-and-hyper-v-backup"></a>Zálohování VMware a Hyper-V

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Je možné zálohovat servery VMware vCenter do Azure?
Ano. Použití Azure Backup serveru k zálohování serveru VMware vCenter a ESXi do Azure.

- [Další informace](backup-mabs-protection-matrix.md) informace o podporovaných verzích.
- [Postupujte podle těchto kroků](backup-azure-backup-server-vmware.md) k zálohování serveru VMware.

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>Budu potřebovat samostatné licence služby obnovení úplné místní cluster VMware nebo Hyper-V?
Není potřebujete samostatné licencování pro ochranu VMware nebo Hyper-V.

- Pokud jste zákazníkem produktu System Center, použijte System Center Data Protection Manager (DPM) k ochraně virtuálních počítačů VMware.
- Pokud si nejste zákazník System Center, můžete použít Azure Backup serveru (průběžné platby) k ochraně virtuálních počítačů VMware.


## <a name="sharepoint"></a>SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>Můžete obnovit Sharepointových položek do původního umístění, pokud je služba SharePoint nakonfigurována pomocí AlwaysOn serveru SQL (ochrana na disku)?
Ano, položka je možné obnovit do původního webu služby SharePoint.

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>Můžete obnovit do původního umístění databáze služby SharePoint, pokud je služba SharePoint nakonfigurována s použitím SQL AlwaysOn?
Protože SharePoint databází nakonfigurovaných v SQL AlwaysOn, jejich nelze upravit, pokud je skupina dostupnosti odebrána. Aplikace DPM v důsledku toho nelze obnovit databázi do původního umístění. Můžete obnovit databázi systému SQL Server na jinou instanci systému SQL Server.

## <a name="next-steps"></a>Další postup

Přečtěte si další nejčastější dotazy:

- [Další informace](backup-support-matrix-mabs-dpm.md) matice podpory o Azure Backup serveru a aplikace DPM.
- [Další informace](backup-azure-mabs-troubleshoot.md) o Azure Backup serveru a aplikace DPM pokyny pro odstraňování potíží.
