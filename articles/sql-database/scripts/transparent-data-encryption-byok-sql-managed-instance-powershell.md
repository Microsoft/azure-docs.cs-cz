---
title: 'PowerShell: Povolit transparentní šifrování dat BYOK – Azure SQL Database Managed Instance | Dokumentace Microsoftu'
description: Další informace o konfiguraci spravované Instance Azure SQL chcete začít používat BYOK transparentní šifrování dat (TDE) pro šifrování v klidovém stavu pomocí Powershellu.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 04/19/2019
ms.openlocfilehash: 8eb924b3dc6ff912db402596c763dd69b85147a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60390711"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault-preview"></a>Správa transparentního šifrování dat v Managed Instance pomocí vlastního klíče ze služby Azure Key Vault (Preview)

Tento ukázkový skript Powershellu nakonfiguruje transparentní šifrování dat (TDE) ve scénáři přineste si vlastní klíč (preview) pro spravované Instance Azure SQL, použití klíče ze služby Azure Key Vault. Další informace o transparentní šifrování dat s podporou přineste si vlastní klíč (BYOK), najdete v článku [TDE vlastního klíče do Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Požadavky

- Existující spravovanou instanci. Zobrazit [použití Powershellu k vytvoření služby Azure SQL Database managed instance](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Místně pomocí obou Powershellu nebo Azure Cloud Shell vyžaduje AZ PowerShell 1.1.1-preview nebo novější verzi preview. Pokud potřebujete upgrade, přečtěte si téma [instalace modulu Azure PowerShell](/powershell/azure/install-az-ps), nebo níže uvedený ukázkový skript k instalaci modulu.

`Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease -Force`

Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="sample-scripts"></a>Ukázkové skripty

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro službu SQL Database najdete v tématu [Skripty PowerShellu pro službu Azure SQL Database](../sql-database-powershell-samples.md).
