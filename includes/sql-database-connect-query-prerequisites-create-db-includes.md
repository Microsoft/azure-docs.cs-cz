---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 01/28/2019
ms.author: genemi
ms.openlocfilehash: edeaa932abe4d1882f957d0ed26aaddd97dabe3f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55198057"
---
<!-- sql-database-connect-query-prerequisites-create-db-includes.md -->

- Azure SQL Database umístí na [logický server](https://docs.microsoft.com/azure/sql-database/sql-database-single-index) nebo [Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index). Databázi můžete vytvořit jedním z následujících postupů:

| Logický Server | MI |
| --- | --- |
| [Azure Portal](../articles/sql-database/sql-database-get-started-portal.md) | [Azure Portal](../articles/sql-database/sql-database-managed-instance-get-started.md) |
| [Rozhraní příkazového řádku](../articles/sql-database/sql-database-get-started-cli.md) | |
| [PowerShell](../articles/sql-database/sql-database-get-started-powershell.md) | |

- **Logický Server pouze** -pravidlo nakonfigurované brány firewall na úrovni serveru, které vám umožní připojit se ke svému logickému serveru. Další informace najdete v tématu [vytvořit pravidlo brány firewall na úrovni serveru](../articles/sql-database/sql-database-get-started-portal-firewall.md).
- **Spravovaná Instance pouze** -nakonfigurované připojení z počítače, který přistupuje k Managed Instance. Můžete použít následující možnosti:
  - [Virtuální počítač Azure](../articles/sql-database/sql-database-managed-instance-configure-vm.md) ve stejné virtuální síti Azure jako Managed Instance, který lze připojit k instanci.
  - [Připojení point-to-site](../articles/sql-database/sql-database-managed-instance-configure-p2s.md) v počítači, který vám umožní připojit počítače k virtuální síti, ve kterém je umístí Managed Instance a použít Managed Instance jako žádný jiný SQL Server ve vaší síti.
