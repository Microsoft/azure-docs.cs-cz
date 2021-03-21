---
title: Konfigurace spravované instance s minimální verzí TLS
description: Informace o tom, jak nakonfigurovat minimální verzi TLS pro spravovanou instanci
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: ''
ms.date: 05/25/2020
ms.openlocfilehash: 17d430946f3cba1aa4680d1eaf8979fa4338bc22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92788396"
---
# <a name="configure-minimal-tls-version-in-azure-sql-managed-instance"></a>Konfigurace minimální verze protokolu TLS ve službě Azure SQL Managed Instance
Nastavení verze [protokolu TLS (minimální přenosná vrstva zabezpečení)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) umožňuje zákazníkům řídit verzi TLS, kterou používá jejich spravovaná instance SQL Azure.

V současné době podporujeme protokoly TLS 1.0, 1.1 a 1.2. Nastavení minimální verze protokolu TLS zajišťuje podporu následných novějších verzí TLS. Například vyberte verzi TLS vyšší než 1,1. se zajistí, že se budou přijímat pouze připojení přes protokol TLS 1.1 a 1.2 a připojení přes protokol TLS 1.0 se budou zamítat. Po otestování, že vaše aplikace podporují protokol TLS 1.2, doporučujeme nastavit minimální verzi protokolu TLS na tuto verzi, která obsahuje opravy ohrožení zabezpečení zjištěných v předchozích verzích a představuje nejvyšší podporovanou verzi protokolu TLS ve službě Azure SQL Managed Instance.

Pro zákazníky s aplikacemi, které spoléhají na starší verze TLS, doporučujeme nastavit minimální verzi TLS podle požadavků vašich aplikací. Pro zákazníky, kteří spoléhají na aplikace, aby se připojili pomocí nešifrovaného připojení, doporučujeme nenastavit žádnou minimální verzi TLS. 

Další informace najdete v tématu [požadavky na TLS pro SQL Database připojení](../database/connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

Po nastavení minimální verze protokolu TLS se pokusy o přihlášení od klientů, kteří používají verzi protokolu TLS nižší než minimální verze protokolu TLS serveru, nezdaří s následující chybou:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Nastavení minimální verze protokolu TLS prostřednictvím PowerShellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické. Následující skript vyžaduje [modul Azure PowerShell](/powershell/azure/install-az-ps).

Následující skript prostředí PowerShell ukazuje, jak `Get` a `Set` vlastnost **Minimální verze protokolu TLS** na úrovni instance:

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group).MinimalTlsVersion

# Update Minimal TLS Version Property
Set-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Nastavení minimální verze protokolu TLS prostřednictvím rozhraní příkazového řádku Azure

> [!IMPORTANT]
> Všechny skripty v této části vyžadují rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Rozhraní příkazového řádku Azure v prostředí bash

Následující skript rozhraní příkazového řádku ukazuje, jak změnit nastavení **Minimální verze protokolu TLS** v prostředí bash:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql mi show -n sql-instance-name -g resource-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql mi update -n sql-instance-name -g resource-group --set minimalTlsVersion="1.2"
```