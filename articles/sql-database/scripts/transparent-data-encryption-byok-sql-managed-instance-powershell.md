---
title: 'PowerShell: povolení BYOK TDE-Azure SQL Database Managed instance '
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
ms.date: 11/05/2019
ms.openlocfilehash: ddffda5229c9c0d33c563e3ae7b4a884f0f92dff
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691409"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Správa transparentní šifrování dat ve spravované instanci pomocí vlastního klíče z Azure Key Vault

Tento ukázkový skript PowerShellu nakonfiguruje transparentní šifrování dat (TDE) s klíčem spravovaným zákazníkem pro Azure SQL Managed instance pomocí klíče z Azure Key Vault. Tato situace se často označuje jako Bring Your Own Key scénář pro TDE. Další informace o TDE s klíčem spravovaným zákazníkem najdete v tématu [TDE Bring Your Own Key do Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Požadavky

- Existující spravovaná instance. Viz [použití PowerShellu k vytvoření spravované instance Azure SQL Database](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Použití prostředí PowerShell místně nebo pomocí Azure Cloud Shell vyžaduje AZ PowerShell 2.3.2 nebo novější verze. Pokud potřebujete provést upgrade, přečtěte si téma [instalace Azure PowerShell Module](/powershell/azure/install-az-ps)nebo spusťte následující vzorový skript a nainstalujte modul pro aktuálního uživatele:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="sample-scripts"></a>Ukázkové skripty

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro službu SQL Database najdete v tématu [Skripty PowerShellu pro službu Azure SQL Database](../sql-database-powershell-samples.md).
