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
ms.date: 04/09/2019
ms.openlocfilehash: c08b5559fd599fb297f294a54aed67c65676aee4
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496229"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Správa transparentního šifrování dat v Managed Instance pomocí vlastního klíče ze služby Azure Key Vault

Tento ukázkový skript Powershellu nakonfiguruje transparentní šifrování dat (TDE) ve scénáři přineste si vlastní klíč pro spravované Instance Azure SQL, použití klíče ze služby Azure Key Vault. Další informace o transparentní šifrování dat s podporou přineste si vlastní klíč (BYOK), najdete v článku [TDE vlastního klíče do Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

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
