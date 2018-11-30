---
title: SQL Server na virtuálním počítači Azure zpráva k vydání verze | Dokumentace Microsoftu
description: Další informace o nové funkce a vylepšení systému SQL Server na Virtuálním počítači Azure
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2018
ms.author: mathoma
ms.openlocfilehash: 44aee447c7f935cd67ca55902c53c5f6f9eb6fda
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52500271"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>SQL Server na virtuálním počítači Azure zpráva k vydání verze

Azure umožňuje nasadit virtuální počítač pomocí image vytvořené systému SQL Server. Tento článek obsahuje seznam nových funkcí a vylepšení, které můžete očekávat v nejnovější verzi systému SQL Server nasazený na virtuálním počítači Azure. 


## <a name="november-2018"></a>. Listopadu 2018
- **Nového poskytovatele prostředků SQL**: existuje nového poskytovatele prostředků pro virtuální počítače s SQL, které umožňují lepší správu vašeho virtuálního počítače. Další informace o registraci vašeho virtuálního počítače najdete v tématu [zaregistrovat starší verze virtuálních počítačů SQL s poskytovatelem prostředků pro nové](virtual-machines-windows-sql-ahb.md#register-legacy-sql-vm-with-new-resource-provider).
- **Přepnout licenčního modelu na**: Nyní můžete přepínat mezi model plateb za využití a přinést vlastní licenci pro váš virtuální počítač SQL s použitím Azure CLI nebo Powershellu. Další informace najdete v tématu [jak změnit licenční model virtuálního počítače SQL](virtual-machines-windows-sql-ahb.md)



## <a name="additional-resources"></a>Další zdroje informací:

**Virtuální počítače s Windows**:

* [Přehled SQL serveru na virtuálním počítači Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Zřídit Windows SQL Server VM](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrace databáze systému SQL Server na Virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md)
* [Vysoká dostupnost a zotavení po havárii pro SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Osvědčené postupy z hlediska výkonu pro SQL Server na Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Modely aplikací a vývojové strategie pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Virtuální počítače s Linuxem**:

* [Přehled SQL serveru na virtuálním počítači s Linuxem](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Zřízení virtuálního počítače s SQL Server Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Nejčastější dotazy (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Dokumentace k SQL Serveru na Linuxu](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
