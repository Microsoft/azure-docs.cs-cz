---
title: Automatická registrace pomocí poskytovatele prostředků virtuálních počítačů SQL
description: Naučte se, jak povolit funkci automatického registrace k automatické registraci všech minulých a budoucích SQL Server virtuálních počítačů pomocí poskytovatele prostředků SQL VM pomocí Azure Portal.
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/21/2020
ms.openlocfilehash: 23ecc3bdfb0ca85caf219fc262348937923f53c3
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286121"
---
# <a name="automatic-registration-with-sql-vm-resource-provider"></a>Automatická registrace pomocí poskytovatele prostředků virtuálních počítačů SQL
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Povolení funkce automatické registrace v Azure Portal pro automatické registraci všech současných a budoucích SQL Server na Azure Virtual Machines (virtuálních počítačů) pomocí poskytovatele prostředků virtuálního počítače SQL v odlehčeném režimu. Když se zaregistrujete do poskytovatele prostředků virtuálního počítače SQL, nainstaluje se [rozšíření agenta SQL IaaS](sql-server-iaas-agent-extension-automate-management.md).

V tomto článku se naučíte, jak povolit funkci automatické registrace. Případně můžete [zaregistrovat jeden virtuální počítač](sql-vm-resource-provider-register.md)nebo [hromadně zaregistrovat virtuální](sql-vm-resource-provider-bulk-register.md) počítače pomocí poskytovatele prostředků virtuálního počítače SQL. 

## <a name="overview"></a>Přehled

Když zaregistrujete SQL Server virtuální počítač pomocí poskytovatele prostředků virtuálního počítače SQL, nainstaluje se [rozšíření agenta SQL IaaS](sql-server-iaas-agent-extension-automate-management.md). 

Když je povolená Automatická registrace, úloha se denně spustí, aby se zjistilo, jestli je SQL Server nainstalovaná na všech neregistrovaných virtuálních počítačích v předplatném. To se provádí zkopírováním binárních souborů rozšíření agenta SQL IaaS na virtuální počítač a následným spuštěním jednorázového nástroje, který kontroluje SQL Server podregistr registru. Pokud se zjistí podregistr SQL Server, virtuální počítač se zaregistruje u [poskytovatele prostředků virtuálního počítače SQL](sql-vm-resource-provider-register.md) ve zjednodušeném režimu. Pokud v registru neexistuje žádný podregistr SQL Server, binární soubory se odeberou.

Po povolení automatické registrace u předplatného se všechny aktuální a budoucí virtuální počítače, které mají nainstalované SQL Server, zaregistrují u poskytovatele prostředků virtuálního počítače SQL v jednoduchém režimu. Abyste mohli využít celou sadu funkcí, je nutné [ručně upgradovat na režim úplné správy](sql-vm-resource-provider-register.md#upgrade-to-full) . 

> [!IMPORTANT]
> Rozšíření agenta SQL IaaS shromažďuje data pro účely výslovného účelu poskytování dobrovolných výhod při používání SQL Server v rámci Azure Virtual Machines. Microsoft nebude tato data používat pro audity licencování bez předchozího souhlasu zákazníka. Další informace najdete v tématu [SQL Server ochrany osobních údajů](/sql/sql-server/sql-server-privacy#non-personal-data) .

## <a name="prerequisites"></a>Předpoklady

Pokud chcete zaregistrovat SQL Server virtuální počítač s poskytovatelem prostředků, budete potřebovat: 

- [Předplatné Azure](https://azure.microsoft.com/free/)
- Model prostředků Azure [Windows Server 2008 R2 (nebo novější)](../../../virtual-machines/windows/quick-create-portal.md) , který je [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) nasazený do veřejného nebo Azure Government cloudu. Systém Windows Server 2008 není podporován. 


## <a name="enable"></a>Povolit

Pokud chcete povolit automatickou registraci SQL Server virtuálních počítačů v Azure Portal, postupujte podle následujících kroků:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Přejděte na stránku prostředku [**virtuální počítače SQL**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Výběrem **automatického SQL Server registrace virtuálního počítače** otevřete stránku **Automatická registrace** . 

   :::image type="content" source="media/sql-vm-resource-provider-automatic-registration/automatic-registration.png" alt-text="Výběrem automatické registrace SQL Server virtuálního počítače otevřete stránku Automatická registrace.":::

1. Z rozevíracího seznamu vyberte své předplatné. 
1. Přečtěte si podmínky a pokud souhlasíte **, vyberte Souhlasím**. 
1. Vyberte **Registrovat** , pokud chcete funkci povolit, a automaticky Zaregistrujte všechny aktuální a budoucí SQL Server virtuální počítače s poskytovatelem prostředků SQL VM. Tím se služba SQL Server nerestartuje na žádném z virtuálních počítačů. 

## <a name="disable"></a>Zakázat

Pomocí rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) nebo [Azure PowerShell](/powershell/azure/install-az-ps) zakažte funkci automatického registrace. Pokud je funkce automatické registrace zakázaná, SQL Server virtuální počítače přidané do předplatného musí být ručně zaregistrované u poskytovatele prostředků SQL VM. Tato akce nezruší registraci stávajících SQL Server virtuálních počítačů, které již byly zaregistrovány.



# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete zakázat automatickou registraci pomocí Azure CLI, spusťte následující příkaz: 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Pokud chcete automatickou registraci zakázat pomocí Azure PowerShell, spusťte následující příkaz: 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="enable-for-multiple-subscriptions"></a>Povolit pro více předplatných

Funkci automatické registrace můžete pro několik předplatných Azure povolit pomocí PowerShellu. 

To můžete provést pomocí těchto kroků:

1. Uložte [Tento skript](https://github.com/microsoft/tigertoolbox/blob/master/AzureSQLVM/RegisterSubscriptionsToSqlVmAutomaticRegistration.ps1) do `.ps1` souboru, například `EnableBySubscription.ps1` . 
1. Přejděte do umístění, do kterého jste skript uložili, pomocí příkazového řádku pro správu nebo okna PowerShellu. 
1. Připojte se k Azure ( `az login` ).
1. Spusťte skript a předejte SubscriptionIds jako parametry jako   
   `.\EnableBySubscription.ps1 -SubscriptionList SubscriptionId1,SubscriptionId2`

   Například: 

   ```console
   .\EnableBySubscription.ps1 -SubscriptionList a1a1a-aa11-11aa-a1a1-a11a111a1,b2b2b2-bb22-22bb-b2b2-b2b2b2bb
   ```

Neúspěšné registrace se ukládají do `RegistrationErrors.csv` umístění ve stejném adresáři, ze kterého jste uložili a spustili `.ps1` skript. 

## <a name="next-steps"></a>Další kroky

Upgradujte režim spravovatelnosti na [úplný](sql-vm-resource-provider-register.md#upgrade-to-full) , abyste mohli využít celou sadu funkcí poskytovanou poskytovatelem prostředků virtuálního počítače SQL. 
