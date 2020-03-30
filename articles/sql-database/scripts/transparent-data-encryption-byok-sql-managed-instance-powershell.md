---
title: 'PowerShell: Povolit BYOK TDE – spravovaná instance Azure SQL Database '
description: Zjistěte, jak nakonfigurovat spravovanou instanci Azure SQL tak, aby začala používat transparentní šifrování dat (TDE) pomocí prostředí PowerShell.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73691409"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Správa transparentního šifrování dat ve spravované instanci pomocí vlastního klíče z trezoru klíčů Azure

Tento příklad skriptu PowerShellu konfiguruje transparentní šifrování dat (TDE) s klíčem spravovaným zákazníkem pro Azure SQL Managed Instance pomocí klíče z Trezoru klíčů Azure. To se často označuje jako přineste si vlastní klíč scénář pro TDE. Další informace o TDE s klíčem spravovaným zákazníkem najdete v tématu [TDE Bring Your Own Key to Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Požadavky

- Existující spravovaná instance. Viz [Použití PowerShellu k vytvoření spravované instance Azure SQL Database](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Použití obou PowerShell místně nebo pomocí Azure Cloud Shell vyžaduje AZ PowerShell 2.3.2 nebo novější verzi. Pokud potřebujete upgradovat, [přečtěte si téma Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps)nebo spusťte níže uvedený ukázkový skript a nainstalujte modul pro aktuálního uživatele:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="sample-scripts"></a>Ukázkové skripty

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro službu SQL Database najdete v tématu [Skripty PowerShellu pro službu Azure SQL Database](../sql-database-powershell-samples.md).
