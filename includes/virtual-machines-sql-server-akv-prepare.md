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
ms.openlocfilehash: 19be449528481b4e35cad4418f82f2250917966b
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
## <a name="prepare-for-akv-integration"></a>Příprava pro integrace se službou AZURE
Použití Azure Key Vault integrace ke konfiguraci virtuálního počítače SQL serveru, existuje několik předpokladů: 

1. [Instalace prostředí Azure Powershell](#install)
2. [Vytvoření služby Azure Active Directory](#register)
3. [Vytvoření trezoru klíčů](#createkeyvault)

Následující části popisují tyto požadavky a informace, které je třeba shromáždit později rutin prostředí PowerShell.

### <a id="install"></a> Instalace prostředí Azure PowerShell
Ujistěte se, že jste nainstalovali nejnovější sadu Azure PowerShell SDK. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azureps-cmdlets-docs).

### <a id="register"></a> Zaregistrovat aplikaci v Azure Active Directory

Nejdřív je potřeba mít [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) v rámci vašeho předplatného. Mezi řadu výhod to umožňuje udělit oprávnění k trezoru klíčů pro některé uživatele a aplikace.

V dalším kroku zaregistrujte aplikaci v AAD. Tím získáte účet instanční objekt, který má přístup k trezoru klíčů, který bude potřebovat virtuálního počítače. V článku Azure Key Vault, můžete najít tyto kroky v [zaregistrovat aplikaci s Azure Active Directory](../articles/key-vault/key-vault-get-started.md#register) oddílu, nebo můžete zobrazit kroky se snímky obrazovky v **získat identity pro oddílu aplikace**  z [tomto příspěvku na blogu](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Před dokončením těchto kroků, která potřebujete shromáždit tyto informace během této registrace, který je potřeba později, když povolíte na virtuální počítač SQL Azure Key Vault integrace.

* Po přidání aplikace najít **ID aplikace** na **registrovaná aplikace** okno.
    ID aplikace je přiřazen později **$spName** parametr (Service Principal name) ve skriptu prostředí PowerShell povolit Azure Key Vault integraci.

   ![ID aplikace](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* Při provádění těchto kroků při vytváření klíče zkopírujte tajný klíč pro váš klíč znázorněné na následujícím snímku obrazovky. Tento klíč tajný klíč je přiřazen později **$spSecret** parametr (Service Principal tajný klíč) ve skriptu prostředí PowerShell.

   ![Tajný klíč AAD](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* ID aplikace a tajný klíč se taky použije k vytvoření přihlašovacích údajů v systému SQL Server.

* Je nutné autorizovat toto nové ID klienta do mají následující přístupová oprávnění: **šifrování**, **dešifrovat**, **wrapKey**, **unwrapKey**, **přihlašovací**, a **ověřte**. To lze provést pomocí [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625.aspx) rutiny. Další informace najdete v tématu [autorizovat aplikaci pro použití klíče nebo tajného klíče](../articles/key-vault/key-vault-get-started.md#authorize).

### <a id="createkeyvault"></a> Vytvoření trezoru klíčů
Abyste mohli používat Azure Key Vault pro ukládání klíčů, které chcete použít pro šifrování v virtuálního počítače, potřebujete přístup k trezoru klíčů. Pokud již jste nenastavili trezoru klíčů, vytvořte ho pomocí následujících kroků v [Začínáme s Azure Key Vault](../articles/key-vault/key-vault-get-started.md) článku. Před dokončením těchto kroků, je některé informace, které je třeba shromáždit během této sady až budete později potřebovat když povolíte na virtuální počítač SQL Azure Key Vault integrace.

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

Když získáte vytvořením krok trezoru klíčů, Všimněte si vrácený **vaultUri** vlastnosti, která je adresa URL trezoru klíčů. V příkladu v tomto kroku, vidíte níže, název trezoru klíčů je ContosoKeyVault, proto trezoru klíčů by měla adresa URL https://contosokeyvault.vault.azure.net/.

Adresa URL trezoru klíčů je přiřazen později **$akvURL** parametr ve skriptu prostředí PowerShell povolit Azure Key Vault integraci.

Po vytvoření trezoru klíčů, musíme přidá klíč do trezoru klíčů, tento klíč se odkazuje, při vytváření asymetrický klíč později vytvořit v systému SQL Server.
