---
title: 'PowerShell: Povolit BYOK TDE-Azure SQL Database Managed instance | Microsoft Docs'
description: Naučte se konfigurovat spravovanou instanci Azure SQL, abyste mohli začít používat BYOK transparentní šifrování dat (TDE) pro šifrování v klidovém prostředí pomocí PowerShellu.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 04/19/2019
ms.openlocfilehash: d8f0f4a8e603a9040d166b00682077cff23abd8d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569694"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault-preview"></a>Správa transparentní šifrování dat ve spravované instanci pomocí vlastního klíče z Azure Key Vault (Preview)

Tento ukázkový skript PowerShellu nakonfiguruje transparentní šifrování dat (TDE) ve scénáři Bring Your Own Key (Preview) pro spravovanou instanci Azure SQL pomocí klíče z Azure Key Vault. Další informace o podpoře TDE with Bring Your Own Key (BYOK) najdete v článku [TDE Bring Your Own Key do Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Požadavky

- Existující spravovaná instance. Viz [použití PowerShellu k vytvoření spravované instance Azure SQL Database](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Použití PowerShellu místně nebo pomocí Azure Cloud Shell vyžaduje AZ PowerShell 1.1.1-Preview nebo novější verzi Preview. Pokud potřebujete upgradovat, přečtěte si téma [install Azure PowerShell Module](/powershell/azure/install-az-ps)nebo run the Sample Script a nainstalujte modul.

`Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease -Force`

Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="sample-scripts"></a>Ukázkové skripty

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro službu SQL Database najdete v tématu [Skripty PowerShellu pro službu Azure SQL Database](../sql-database-powershell-samples.md).
