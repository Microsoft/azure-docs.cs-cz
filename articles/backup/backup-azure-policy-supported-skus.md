---
title: Podporované SKU virtuálních počítačů pro Azure Policy
description: Článek popisující podporované skladové položky virtuálních her (podle vydavatele, nabídky bitové kopie a skladové položky bitové kopie), které jsou podporované pro integrované zásady Azure poskytované službou Backup
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1b6a94b0f57ecfea946654c6cae38ac335335e00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980111"
---
# <a name="supported-vm-skus-for-azure-policy"></a>Podporované SKU virtuálních počítačů pro Azure Policy

Azure Backup poskytuje integrované zásady (pomocí zásad Azure), které se dá přiřadit **ke všem virtuálním montovnám Azure v určeném umístění v rámci předplatného nebo skupiny prostředků.** Pokud je tato zásada přiřazena k danému oboru, všechny nové virtuální počítače vytvořené v tomto oboru jsou automaticky nakonfigurovány pro zálohování do **existujícího trezoru ve stejném umístění a předplatném**. V následující tabulce jsou uvedeny všechny virtuální virtuální společnosti skum podporované touto zásadou.

### <a name="supported-vms"></a>**Podporované virtuální hody**

**Název zásady:** Konfigurace zálohování na virtuálních počítačích umístění do existujícího centrálního trezoru ve stejném umístění

Vydavatel obrázků | Nabídka obrázků | Skladová položka obrázku
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | Windows Server 2008 R2 SP1 (2008-R2-SP1)
MicrosoftWindowsServer | WindowsServer | [malý disk] Systém Windows Server 2008 R2 SP (2008-R2-SP1-smalldisk)
MicrosoftWindowsServer | WindowsServer | Datové centrum Windows Serveru 2012 (2012-Datacenter)
MicrosoftWindowsServer | WindowsServer | [malý disk] Datacentrum Windows Serveru 2012 (2012-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Datové centrum Windows Server 2012 R2 (2012-R2-Datacenter)
MicrosoftWindowsServer | WindowsServer | [malý disk] Datové centrum Windows Server 2012 R2 (2012-R2-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Datové centrum Windows Serveru 2016 (Datacenter 2016)
MicrosoftWindowsServer | WindowsServer | Datové centrum Windows Server 2016 – jádro serveru (2016-Datacenter-Server-Core)
MicrosoftWindowsServer | WindowsServer | [malý disk] Datacentrum Windows Serveru 2016 – jádro serveru (2016-Datacenter-Server-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | [malý disk] Datové centrum Windows Serveru 2016 (2016-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Jádro serveru datového centra Windows Server 2019 s kontejnery (2016-Datacenter-with-Containers)
MicrosoftWindowsServer | WindowsServer | Hostitel relací vzdálené plochy Windows Serveru 2016 2016 (2016-Datacenter-with-RDSH)
MicrosoftWindowsServer | WindowsServer | Datové centrum Windows Serveru 2019 (Datacenter 2019)
MicrosoftWindowsServer | WindowsServer | Jádro serveru datového centra Windows Server 2019 (2019-Datacenter-Core)
MicrosoftWindowsServer | WindowsServer | [malý disk] Windows Server 2019 Jádro datového centra (2019-Datacenter-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | Jádro serveru datového centra Windows Server 2019 s kontejnery (2019-Datacenter-Core-with-Containers)
MicrosoftWindowsServer | WindowsServer | [malý disk] Jádro serveru datového centra Windows Server 2019 s kontejnery (2019-Datacenter-Core-with-Containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | [malý disk] Datové centrum Windows Serveru 2019 (2019-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Datové centrum Windows Serveru 2019 s kontejnery (2019-Datacenter-with-Containers)
MicrosoftWindowsServer | WindowsServer | [malý disk] Datové centrum Windows Serveru 2019 s kontejnery (2019-Datacenter-with-Containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter (zh-cn) (2019-Datacenter-zhcn)
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datové centrum-Core-1709-malý disk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datové centrum-Core-1709-s kontejnery-malý disk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datové centrum-Core-1803-s kontejnery-malý disk
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | Všechny image skus
MicrosoftSQLServer | SQL2016SP1-WS2016 | Všechny image skus
MicrosoftSQLServer | SQL2016-WS2016  | Všechny image skus
MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | Všechny image skus
MicrosoftSQLServer | SQL2012SP3-WS2012R2 | Všechny image skus
MicrosoftSQLServer | SQL2016-WS2012R2 | Všechny image skus
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | Všechny image skus
MicrosoftSQLServer | SQL2012SP3-WS2012R2-BYOL | Všechny image skus
MicrosoftSQLServer | SQL2014SP1-WS2012R2-BYOL | Všechny image skus
MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | Všechny image skus
MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | Všechny image skus
Server MicrosoftRServer | MLServer-WS2016 | Všechny image skus
MicrosoftVisualStudio | VisualStudio | Všechny image skus
MicrosoftVisualStudio | Windows | Všechny image skus
MicrosoftDynamicsAX | Dynamics  | Pre-Req-AX7-Onebox-U8
microsoft-reklamy | windows-data-science-vm | Všechny image skus
MicrosoftWindowsDesktop | Windows-10 | Všechny image skus
RedHat | RHEL | 6.7, 6.8, 6.9, 6.10, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7
RedHat | RHEL-SAP-HANA | 6.7, 7.2, 7.3
SUSE | SLES | 12,X
SUSE | SLES-HPC | 12,X
SUSE | SLES-HPC-Priorita | 12,X
SUSE | SLES-SAP | 12,X
SUSE | SLES-SAP-BYOS | 12,X
SUSE | SLES-Priorita | 12,X
SUSE | SLES-BYOS | 12,X
SUSE | SLES-SAPCAL | 12,X
SUSE | SLES-Standard | 12,X
Canonical | UbuntuServer | 14.04.0-LTS
Canonical | UbuntuServer | 14.04.1-LTS
Canonical | UbuntuServer | 14.04.2-LTS
Canonical | UbuntuServer | 14.04.3-LTS
Canonical | UbuntuServer | 14.04.4-LTS
Canonical | UbuntuServer | 14.04.5-DENNÍ-LTS
Canonical | UbuntuServer | 14.04.5-LTS
Canonical | UbuntuServer | 16.04-DENNÍ-LTS
Canonical | UbuntuServer | 16.04-LTS
Canonical | UbuntuServer | 16.04.0-LTS
Canonical | UbuntuServer | 18.04-DENNÍ-LTS
Canonical | UbuntuServer | 18.04-LTS
Oracle | Společnost Oracle-Linux | 6.8, 6.9, 6.10, 7.3, 7.4, 7.5, 7.6
OpenLogic | CentOS | 6.X, 7.X
OpenLogic | Centos–LVM | 6.X, 7.X
OpenLogic | Centos–SRIOV | 6.X, 7.X
cloudera | cloudera-centos-os | 7,X
