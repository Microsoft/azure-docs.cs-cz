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
ms.openlocfilehash: dfcd9e017675d6ab8799d137b8ac985434a218ba
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56212975"
---
## <a name="prepare-for-akv-integration"></a>Příprava pro integrace se službou AZURE
Konfigurace virtuálního počítače s SQL serverem pomocí integrace Azure Key Vaultu, existuje několik požadavků: 

1. [Instalace Azure Powershellu](#install)
2. [Vytvoření aplikace Azure Active Directory](#register)
3. [Vytvoření trezoru klíčů](#createkeyvault)

Následující části popisují tyto požadavky a informace, které je potřeba shromáždit k později spustit rutiny prostředí PowerShell.

### <a id="install"></a> Instalace Azure Powershellu
Ujistěte se, že máte nainstalovanou nejnovější Azure PowerShell SDK. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azureps-cmdlets-docs).

### <a id="register"></a> Registrace aplikace ve službě Azure Active Directory

Nejprve je potřeba mít [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) v rámci vašeho předplatného. Mezi řadu výhod díky tomu můžete udělit oprávnění k trezoru klíčů pro určité uživatele nebo aplikace.

V dalším kroku registrace aplikace v AAD. Tím získáte účet instančního objektu, který má přístup k trezoru klíčů, který váš virtuální počítač potřebovat. V Azure Key Vault článku najdete postup v [registraci aplikace v Azure Active Directory](../articles/key-vault/key-vault-manage-with-cli2.md#registering-an-application-with-azure-active-directory) oddílu, nebo můžete zobrazit kroky, snímky obrazovky v **získat identitu pro části aplikace**  z [tento příspěvek na blogu](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Před dokončením těchto kroků, potřebujete shromažďovat následující informace během této registrace, který je budete později potřebovat při povolení integrace se službou Azure Key Vault na váš virtuální počítač SQL.

* Po přidání aplikace najít **ID aplikace** na **registrovaná aplikace** okno.
    ID aplikace je přiřazena později **$spName** parametru (název instančního objektu) ve skriptu Powershellu k povolení integrace se službou Azure Key Vault.

   ![ID aplikace](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* Při provádění těchto kroků při vytváření vašeho klíče zkopírujte tajný klíč pro svůj klíč jak je znázorněno na následujícím snímku obrazovky. Tento tajný kód klíče se přiřadí později **$spSecret** parametr (tajný klíč instančního objektu) ve skriptu prostředí PowerShell.

   ![Tajný kód AAD](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* ID aplikace a tajný kód se taky použije k vytvoření přihlašovacích údajů v systému SQL Server.

* Musíte povolit toto nové ID klienta mají následující přístupová oprávnění: **získat**, **wrapKey**, **unwrapKey**. Používá se k tomu [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) rutiny. Další informace najdete v tématu [Přehled služby Azure Key Vault](../articles/key-vault/key-vault-overview.md).

### <a id="createkeyvault"></a> Vytvoření trezoru klíčů
Chcete-li používat Azure Key Vault pro ukládání klíčů, které budete používat pro šifrování ve virtuálním počítači, musíte mít přístup k trezoru klíčů. Pokud už jste nenastavili trezoru klíčů, vytvořte ho provedením kroků v [Začínáme se službou Azure Key Vault](../articles/key-vault/key-vault-overview.md) článku. Před dokončením těchto kroků, se některé informace, které je potřeba shromáždit během této sady až budete později potřebovat při povolení integrace se službou Azure Key Vault na vašem virtuálním počítači SQL.

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

Když dojde k vytvoření služby key vault krok, mějte na paměti vráceného **vaultUri** vlastnost, což je adresa URL služby key vault. V příkladu v tomto kroku, vidíte níže, název trezoru klíčů je ContosoKeyVault, proto trezor klíčů by měla adresa URL https://contosokeyvault.vault.azure.net/.

Adresa URL služby key vault se později přiřadí **$akvURL** parametr Powershellového skriptu pro povolení integrace se službou Azure Key Vault.

Po vytvoření trezoru klíčů, je potřeba přidat klíč do trezoru klíčů, tento klíč se označují, když vytvoříme asymetrický klíč později vytvořit v systému SQL Server.
