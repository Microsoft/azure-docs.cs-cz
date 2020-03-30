---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows
author: rothja
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 2c7d312910c6d38c54b291da34bfb827246c7dad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "79504341"
---
## <a name="prepare-for-akv-integration"></a>Připravte se na integraci AKV
Chcete-li ke konfiguraci virtuálního počítače SQL Serveru použít integraci úložiště klíčů Azure, existuje několik předpokladů: 

1. [Instalace Azure Powershellu](#install)
2. [Vytvoření služby Azure Active Directory](#register)
3. [Vytvoření trezoru klíčů](#createkeyvault)

Následující části popisují tyto požadavky a informace, které je potřeba shromáždit k pozdějšímu spuštění rutin prostředí PowerShell.

[!INCLUDE [updated-for-az](./updated-for-az.md)]

### <a name="install-azure-powershell"></a><a id="install"></a>Instalace Azure PowerShellu
Ujistěte se, že jste nainstalovali nejnovější modul Azure PowerShell. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-az-ps).

### <a name="register-an-application-in-your-azure-active-directory"></a><a id="register"></a>Registrace aplikace ve službě Azure Active Directory

Nejprve musíte mít [azure active directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) ve vašem předplatném. Mezi mnoha výhodami to umožňuje udělit oprávnění k trezoru klíčů pro určité uživatele a aplikace.

Dále zaregistrujte aplikaci u ad. To vám poskytne účet instančního objektu, který má přístup k trezoru klíčů, který bude váš virtuální počítač potřebovat. V článku Azure Key Vault najdete tyto kroky v části [Zaregistrovat aplikaci pomocí Služby Azure Active Directory](../articles/key-vault/key-vault-manage-with-cli2.md#registering-an-application-with-azure-active-directory) nebo se můžete podívat na kroky s snímky obrazovky v **části Získání identity pro aplikaci** [tohoto příspěvku blogu](https://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Před dokončením těchto kroků je potřeba shromáždit následující informace během této registrace, která je potřeba později, když povolíte integraci úložiště klíčů Azure integrace na vašem virtuálním počítači SQL.

* Po přidání aplikace vyhledejte **ID aplikace** (označované také jako AAD ClientID nebo AppID) v okně **Registrovaná aplikace.**
    ID aplikace je přiřazeno později parametru **$spName** (název hlavního_serveru služby) ve skriptu PowerShellu, aby byla povolena integrace trezoru klíčů Azure.

   ![ID aplikace](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* Během těchto kroků při vytváření klíče zkopírujte tajný klíč, jak je znázorněno na následujícím snímku obrazovky. Tento tajný klíč klíče je později přiřazen parametru **$spSecret** (Service Principal Secret) ve skriptu prostředí PowerShell.

   ![Tajný klíč AAD](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* ID aplikace a tajný klíč budou také použity k vytvoření pověření na serveru SQL Server.

* Je nutné autorizovat toto nové ID aplikace (nebo ID klienta) mít následující přístupová oprávnění: **get**, **wrapKey**, **unwrapKey**. To se provádí s rutinou [Set-AzKeyVaultPolicy.](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) Další informace naleznete v [tématu Azure Key Vault overview](../articles/key-vault/key-vault-overview.md).

### <a name="create-a-key-vault"></a><a id="createkeyvault"></a>Vytvoření trezoru klíčů
Abyste mohli pomocí azure key vaultu ukládat klíče, které budete používat pro šifrování ve vašem virtuálním počítači, potřebujete přístup k trezoru klíčů. Pokud jste ještě nenastavili trezor klíčů, vytvořte ho podle kroků v článku [Začínáme s trezorem klíčů Azure.](../articles/key-vault/key-vault-overview.md) Před dokončením těchto kroků je několik informací, které je potřeba shromáždit během tohoto nastavení, které je potřeba později, když povolíte integraci azure key vault u vašeho virtuálního počítače SQL.

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

Když se dostanete do kroku Vytvořit trezor klíčů, poznamenejte si vrácenou vlastnost **vaultUri,** což je adresa URL trezoru klíčů. V příkladu uvedeném v tomto kroku, který je uveden níže, je název trezoru https://contosokeyvault.vault.azure.net/klíčů ContosoKeyVault, proto by adresa URL trezoru klíčů byla .

Adresa URL trezoru klíčů je přiřazena později **$akvURL** parametrve skriptu PowerShellu, aby byla povolena integrace trezoru klíčů Azure.

Po vytvoření trezoru klíčů je třeba přidat klíč do trezoru klíčů, tento klíč bude odkazován při vytvoření asymetrického klíče v SQL Serveru později.
