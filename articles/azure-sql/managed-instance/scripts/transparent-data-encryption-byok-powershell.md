---
title: 'PowerShell: povolení BYOK (Přineste si vlastní klíč) TDE'
titleSuffix: Azure SQL Managed Instance
description: Naučte se konfigurovat spravovanou instanci Azure SQL, abyste mohli začít používat BYOK (uživatelsky-vlastní) transparentní šifrování dat (TDE) pro šifrování v klidovém prostředí pomocí PowerShellu.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: cff98c0acfe06a9dbf9e3d7c7dae1b2411823d28
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84051791"
---
# <a name="transparent-data-encryption-tde-in-a-sql-managed-instance-using-your-own-key-from-azure-key-vault"></a>Transparentní šifrování dat (TDE) ve spravované instanci SQL pomocí vlastního klíče z Azure Key Vault
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Tento ukázkový skript PowerShellu nakonfiguruje transparentní šifrování dat (TDE) s klíčem spravovaným zákazníkem pro Azure SQL Managed instance pomocí klíče z Azure Key Vault. Tato situace se často označuje jako Bring Your Own Key scénář pro TDE. Další informace o TDE s klíčem spravovaným zákazníkem najdete v tématu [TDE Bring Your Own Key do Azure SQL](../../database/transparent-data-encryption-byok-overview.md).

## <a name="prerequisites"></a>Požadavky

- Existující spravovaná instance SQL. Viz [použití PowerShellu k vytvoření spravované instance Azure SQL](create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Použití prostředí PowerShell místně nebo pomocí Azure Cloud Shell vyžaduje AZ PowerShell 2.3.2 nebo novější verze. Pokud potřebujete provést upgrade, přečtěte si téma [instalace Azure PowerShell Module](/powershell/azure/install-az-ps)nebo spusťte následující vzorový skript a nainstalujte modul pro aktuálního uživatele:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="sample-scripts"></a>Ukázkové skripty

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu spravované instance SQL najdete v [skriptech PowerShell spravované instance Azure SQL](../../database/powershell-script-content-guide.md).
