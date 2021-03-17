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
ms.openlocfilehash: 3509185baa3a9d7be90c1fa4bd8000da4a8a6fe5
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95562415"
---
## <a name="prepare-for-akv-integration"></a>Příprava na integraci integrace
Pokud chcete použít integraci Azure Key Vault ke konfiguraci SQL Server virtuálního počítače, máte několik požadavků: 

1. [Instalace Azure PowerShellu](#install)
2. [Vytvoření Azure Active Directory](#register)
3. [Vytvoření trezoru klíčů](#createkeyvault)

Následující části popisují tyto požadavky a informace, které potřebujete ke shromáždění, aby později běžely rutiny prostředí PowerShell.

[!INCLUDE [updated-for-az](./updated-for-az.md)]

### <a name="install-azure-powershell"></a><a id="install"></a> Nainstalovat Azure PowerShell
Ujistěte se, že jste nainstalovali nejnovější modul Azure PowerShell. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-az-ps).

### <a name="register-an-application-in-your-azure-active-directory"></a><a id="register"></a> Registrace aplikace v Azure Active Directory

Nejdřív musíte mít ve svém předplatném [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD). Mezi mnoha výhodami vám to umožňuje udělit pro určité uživatele a aplikace oprávnění k trezoru klíčů.

V dalším kroku zaregistrujete aplikaci pomocí AAD. Tím získáte účet instančního objektu, který má přístup k trezoru klíčů, který bude váš virtuální počítač potřebovat. V Azure Key Vault článku najdete tyto kroky v části [Registrace aplikace s Azure Active Directory](../articles/key-vault/general/manage-with-cli2.md#registering-an-application-with-azure-active-directory) nebo můžete zobrazit postup s snímky obrazovky v **části získání identity pro aplikaci** v [tomto blogovém příspěvku](/archive/blogs/kv/azure-key-vault-step-by-step). Před dokončením těchto kroků je potřeba shromáždit během této registrace následující informace, které jsou potřeba později, až povolíte Azure Key Vault integraci na svém VIRTUÁLNÍm počítači s SQL.

* Po přidání aplikace vyhledejte v okně **registrovaná aplikace** **ID aplikace** (známé také jako AAD ClientID nebo AppID).
    ID aplikace je přiřazeno později do parametru **$spName** (hlavní název služby) ve skriptu prostředí PowerShell, aby bylo možné povolit Azure Key Vault integraci.

   ![ID aplikace](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* Během těchto kroků si pro svůj klíč Zkopírujte tajný klíč, jak je znázorněno na následujícím snímku obrazovky. Tento tajný klíč klíče se přiřadí později do parametru **$spSecret** (hlavní klíč služby) ve skriptu PowerShellu.

   ![Tajný kód AAD](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* ID aplikace a tajný klíč budou také použity k vytvoření přihlašovacích údajů v SQL Server.

* Chcete-li mít následující přístupová oprávnění, musíte autorizovat toto nové ID aplikace (nebo ID klienta): **Get**, **wrapKey**, **unwrapKey**. To se provádí pomocí rutiny [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) . Další informace najdete v tématu [přehled Azure Key Vault](../articles/key-vault/general/overview.md).

### <a name="create-a-key-vault"></a><a id="createkeyvault"></a> Vytvoření trezoru klíčů
Abyste mohli použít Azure Key Vault k ukládání klíčů, které budete používat k šifrování na svém VIRTUÁLNÍm počítači, budete potřebovat přístup k trezoru klíčů. Pokud jste svůj Trezor klíčů ještě nevytvořili, vytvořte ho podle kroků uvedených v článku [Začínáme s Azure Key Vault](../articles/key-vault/general/overview.md) . Před dokončením tohoto postupu je několik informací, které potřebujete ke shromáždění během této nastavení, později, když povolíte Azure Key Vault integraci na svém VIRTUÁLNÍm počítači s SQL.

```azurepowershell
New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'
```

Když se dostanete k kroku vytvoření trezoru klíčů, poznamenejte si vrácenou vlastnost **vaultUri** , která je adresa URL trezoru klíčů. V příkladu uvedeném v tomto kroku se zobrazuje název trezoru klíčů ContosoKeyVault, takže adresa URL trezoru klíčů by byla https://contosokeyvault.vault.azure.net/ .

Adresa URL trezoru klíčů se přiřadí později k parametru **$akvURL** ve skriptu PowerShellu, aby se aktivovala Azure Key Vault integrace.

Po vytvoření trezoru klíčů musíme do trezoru klíčů přidat klíč. Tento klíč se bude označovat při vytváření asymetrického klíče v SQL Server později.