---
title: 'PowerShell: povolení BYOK (Přineste si vlastní klíč) TDE'
titleSuffix: Azure SQL Managed Instance
description: Naučte se konfigurovat spravovanou instanci Azure SQL, abyste mohli začít používat BYOK (uživatelsky-vlastní) transparentní šifrování dat (TDE) pro šifrování v klidovém prostředí pomocí PowerShellu.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, sstein
ms.date: 08/25/2020
ms.openlocfilehash: 34a849fde315b45bdb1df577cf26c91f458abd72
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91323219"
---
# <a name="transparent-data-encryption-in-sql-managed-instance-using-your-own-key-from-azure-key-vault"></a>transparentní šifrování dat ve spravované instanci SQL pomocí vlastního klíče z Azure Key Vault

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Tento ukázkový skript PowerShellu nakonfiguruje transparentní šifrování dat (TDE) s klíčem spravovaným zákazníkem pro spravovanou instanci Azure SQL pomocí klíče z Azure Key Vault. To se často označuje jako scénář BYOK (Přineste si vlastní klíč) pro TDE. Další informace najdete v tématu [Azure SQL transparentní šifrování dat s klíčem spravovaným zákazníkem](../../database/transparent-data-encryption-byok-overview.md).

## <a name="prerequisites"></a>Požadavky

- Existující spravovaná instance. Viz [použití PowerShellu k vytvoření spravované instance](create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Použití prostředí PowerShell místně nebo pomocí Azure Cloud Shell vyžaduje Azure PowerShell 2.3.2 nebo novější verzi. Pokud potřebujete provést upgrade, přečtěte si téma [instalace Azure PowerShell Module](/powershell/azure/install-az-ps)nebo spusťte následující vzorový skript a nainstalujte modul pro aktuálního uživatele:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="sample-scripts"></a>Ukázkové skripty 

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShell najdete v [dokumentaci k Azure PowerShell](/powershell/azure/).

Další ukázkové skripty PowerShellu pro spravovanou instanci SQL najdete v [skriptech PowerShell spravované instance Azure SQL](../../database/powershell-script-content-guide.md).
