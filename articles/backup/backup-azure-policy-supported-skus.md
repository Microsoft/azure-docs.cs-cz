---
title: Podporované SKU virtuálních počítačů pro Azure Policy
description: Článek popisující podporované skladové položky virtuálních počítačů (od vydavatele, nabídky image a SKU image), které jsou podporované pro předdefinované zásady Azure, které poskytuje zálohování
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 4658d1beffe707682e173491edea1eac0db9c811
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82183615"
---
# <a name="supported-vm-skus-for-azure-policy"></a>Podporované SKU virtuálních počítačů pro Azure Policy

Azure Backup poskytuje vestavěnou zásadu (pomocí Azure Policy), kterou je možné přiřadit ke **všem virtuálním počítačům Azure v zadaném umístění v rámci předplatného nebo skupiny prostředků**. Když se tato zásada přiřadí k danému oboru, všechny nové virtuální počítače vytvořené v tomto oboru se automaticky nakonfigurují pro zálohování do **existujícího trezoru ve stejném umístění a předplatném**. Následující tabulka uvádí všechny SKU virtuálních počítačů podporované touto zásadou.

## <a name="supported-vms"></a>Podporované virtuální počítače *

**Název zásady:** Konfigurace zálohování na virtuálních počítačích umístění do existujícího centrálního trezoru ve stejném umístění

Vydavatel obrázku | Nabídka Image | SKU image
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | Windows Server 2008 R2 SP1 (2008-R2-SP1)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2008 R2 SP (2008-R2-SP1 – smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 Datacenter (2012 – Datacenter)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 Datacenter (2012 – Datacenter – smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 R2 Datacenter (2012 – R2 – Datacenter)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 R2 Datacenter (2012 – R2 – Datacenter – smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter (2016 – Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter – Server Core (2016-Datacenter-Server-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Datacenter – Server Core (2016-Datacenter-Server-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Datacenter (2016 – Datacenter – smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core s kontejnery (2016-Datacenter-with-Containers)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Hostitel relace vzdálené plochy 2016 (2016-Datacenter – s-vzdálené relace)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter (2019 – Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core (2019 – Datacenter – jádro)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter Server Core (2019-Datacenter-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core s kontejnery (2019-Datacenter-Core-with-Containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter Server Core s kontejnery (2019-Datacenter-Core-with-Containers – smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter (2019 – Datacenter – smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter s kontejnery (2019-Datacenter-with-Containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter s kontejnery (2019 – Datacenter – s kontejnery – smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter (zh-CN) (2019-Datacenter – zhCN)
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter – Core-1709 – smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1709-with-Containers-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1803-with-Containers-smalldisk
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | Všechny SKU imagí
MicrosoftSQLServer | SQL2016SP1 – WS2016 | Všechny SKU imagí
MicrosoftSQLServer | SQL2016 – WS2016 | Všechny SKU imagí
MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | Všechny SKU imagí
MicrosoftSQLServer | SQL2012SP3-WS2012R2 | Všechny SKU imagí
MicrosoftSQLServer | SQL2016-WS2012R2 | Všechny SKU imagí
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | Všechny SKU imagí
MicrosoftSQLServer | SQL2012SP3-WS2012R2-BYOL | Všechny SKU imagí
MicrosoftSQLServer | SQL2014SP1-WS2012R2-BYOL | Všechny SKU imagí
MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | Všechny SKU imagí
MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | Všechny SKU imagí
MicrosoftRServer | MLServer – WS2016 | Všechny SKU imagí
MicrosoftVisualStudio | VisualStudio | Všechny SKU imagí
MicrosoftVisualStudio | Windows | Všechny SKU imagí
MicrosoftDynamicsAX | Dynamics | Pre-REQ-AX7-OneBox-U8
Microsoft-ADS | Windows – data-věda – virtuální počítač | Všechny SKU imagí
MicrosoftWindowsDesktop | Windows-10 | Všechny SKU imagí
RedHat | RHEL | 6,7, 6,8, 6,9, 6,10, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7
RedHat | RHEL – SAP – HANA | 6,7, 7,2, 7,3
SUSE | SLES | 12. X
SUSE | SLES – HPC | 12. X
SUSE | SLES-HPC-priorita | 12. X
SUSE | SLES – SAP | 12. X
SUSE | SLES-SAP-BYOS | 12. X
SUSE | SLES-Priority | 12. X
SUSE | SLES – BYOS | 12. X
SUSE | SLES – SAPCAL | 12. X
SUSE | SLES-Standard | 12. X
Canonical | UbuntuServer | 14.04.0 – LTS
Canonical | UbuntuServer | 14.04.1 – LTS
Canonical | UbuntuServer | 14.04.2-LTS
Canonical | UbuntuServer | 14.04.3 – LTS
Canonical | UbuntuServer | 14.04.4-LTS
Canonical | UbuntuServer | 14.04.5 – DENNĚ – LTS
Canonical | UbuntuServer | 14.04.5-LTS
Canonical | UbuntuServer | 16,04-DENNĚ – LTS
Canonical | UbuntuServer | 16.04-LTS
Canonical | UbuntuServer | 16.04.0 – LTS
Canonical | UbuntuServer | 18,04-DENNĚ – LTS
Canonical | UbuntuServer | 18,04 – LTS
Oracle | Oracle-Linux | 6,8, 6,9, 6,10, 7,3, 7,4, 7,5, 7,6
OpenLogic | CentOS | 6. X, 7. X
OpenLogic | CentOS – LVM | 6. X, 7. X
OpenLogic | CentOS – SRIOV | 6. X, 7. X
Cloudera | Cloudera-CentOS – OS | 7. X
