---
title: Používání databáze SQL v Azure zásobníku | Microsoft Docs
description: Zjistěte, jak můžete nasadit databází SQL jako služba na Azure zásobníku a rychlé kroky k nasazení adaptér zprostředkovatele prostředků systému SQL Server.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 55d0e51606e8768a01c0b5a7766dbafe24d97a0d
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36307821"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Databáze SQL pro použití v zásobníku Microsoft Azure

Použití systému SQL Server prostředků zprostředkovatele adaptéru rozhraní API ke zveřejnění databází SQL jako službu [zásobník Azure](azure-stack-poc.md). Po instalaci poskytovatele prostředků a připojte ho k jedné nebo více instancí systému SQL Server, můžete vytvořit vás a uživatele:

- Databáze pro nativní cloudové aplikace.
- Weby, které používají SQL.
- Úlohy, které používají SQL.

Zprostředkovatel prostředků neposkytuje všechny databáze schopnosti správy [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Například elastické fondy, které automaticky přidělit prostředky nejsou podporovány. Ale podporuje zprostředkovatele prostředků, podobně jako vytvářet, číst, aktualizovat a odstranit operace v databázi systému SQL Server. Další informace o poskytovateli prostředků rozhraní API najdete v tématu [Windows Azure Pack SQL serveru prostředků zprostředkovatele REST referenční dokumentace rozhraní API](https://msdn.microsoft.com/library/dn528529.aspx).

>[!NOTE]
Zprostředkovatel prostředků systému SQL Server rozhraní API není kompatibilní s Azure SQL Database.

## <a name="sql-resource-provider-adapter-architecture"></a>Architektura adaptér zprostředkovatele prostředků SQL

Zprostředkovatel prostředků se skládá z následujících součástí:

- **SQL prostředků zprostředkovatele adaptér virtuálního počítače (VM)**, což je virtuální počítač Windows serveru, který spouští služby poskytovatele.
- **Zprostředkovatel prostředků**, který zpracovává žádosti a databázi prostředkům přistupuje.
- **Servery, které jsou hostiteli systému SQL Server**, které poskytují kapacitu pro databáze názvem hostitelskými servery.

Musíte vytvořit aspoň jednu instanci systému SQL Server nebo poskytovat přístup k externí instance systému SQL Server.

> [!NOTE]
> Hostitelské servery, které jsou nainstalovány v Azure zásobníku integrované systémy musí být vytvořeny z předplatného klienta. Nemohou být vytvářeny z předplatného výchozího zprostředkovatele. Musí se vytvořit z klienta portálu nebo pomocí prostředí PowerShell s odpovídající přihlášení. Všechny hostitelské servery jsou fakturovatelné virtuální počítače a musí mít licenci. Správce služby můžete být vlastníkem předplatného klienta.

## <a name="next-steps"></a>Další postup

[Nasazení poskytovatele prostředků SQL serveru](azure-stack-sql-resource-provider-deploy.md)
