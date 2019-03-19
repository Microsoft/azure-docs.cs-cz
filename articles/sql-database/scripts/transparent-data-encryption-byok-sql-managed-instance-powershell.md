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
ms.date: 03/12/2019
ms.openlocfilehash: fbc62628bfec67bd648188920731f985f990a53d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57837143"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Správa transparentního šifrování dat v Managed Instance pomocí vlastního klíče ze služby Azure Key Vault

Tento ukázkový skript Powershellu nakonfiguruje transparentní šifrování dat (TDE) ve scénáři přineste si vlastní klíč pro spravované Instance Azure SQL, použití klíče ze služby Azure Key Vault. Další informace o transparentní šifrování dat s podporou přineste si vlastní klíč (BYOK), najdete v článku [TDE vlastního klíče do Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, v tomto kurzu vyžaduje AZ PowerShell 1.4.0 nebo novější. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

Pokud se rozhodnete nainstalovat a používat PowerShell místně, tento kurz vyžaduje preview verzi Powershellu AzureRM.Sql balíčku *4.11.6-preview*. Spusťte následující příkaz k instalaci je: `Install-Module -Name AzureRM.Sql -RequiredVersion 4.11.6-preview -AllowPrerelease`

## <a name="sample-scripts"></a>Ukázkové skripty

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro službu SQL Database najdete v tématu [Skripty PowerShellu pro službu Azure SQL Database](../sql-database-powershell-samples.md).
