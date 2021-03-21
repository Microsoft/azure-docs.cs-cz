---
title: Automatická registrace pomocí rozšíření agenta SQL IaaS
description: Naučte se, jak povolit funkci automatického registrace k automatické registraci všech minulých a budoucích SQL Server virtuálních počítačů pomocí rozšíření agenta SQL IaaS pomocí Azure Portal.
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.openlocfilehash: 139852949a3744fd603cb197b2e27fa32679aae0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042415"
---
# <a name="automatic-registration-with-sql-iaas-agent-extension"></a>Automatická registrace pomocí rozšíření agenta SQL IaaS
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Povolení funkce automatické registrace v Azure Portal pro automatické registraci všech současných a budoucích SQL Server v Azure Virtual Machines (virtuálních počítačích) pomocí [rozšíření agenta SQL IaaS](sql-server-iaas-agent-extension-automate-management.md) v jednoduchém režimu. 

V tomto článku se naučíte, jak povolit funkci automatické registrace. Případně můžete [zaregistrovat jeden virtuální počítač](sql-agent-extension-manually-register-single-vm.md)nebo [hromadně zaregistrovat virtuální](sql-agent-extension-manually-register-vms-bulk.md) počítače pomocí rozšíření agenta SQL IaaS. 

## <a name="overview"></a>Přehled

Když zaregistrujete SQL Server virtuální počítač s [rozšířením agenta SQL IaaS](sql-server-iaas-agent-extension-automate-management.md) , abyste mohli odemknout celou sadu výhod. 

Když je povolená Automatická registrace, úloha se denně spustí, aby se zjistilo, jestli je SQL Server nainstalovaná na všech neregistrovaných virtuálních počítačích v předplatném. To se provádí zkopírováním binárních souborů rozšíření agenta SQL IaaS na virtuální počítač a následným spuštěním jednorázového nástroje, který kontroluje SQL Server podregistr registru. Pokud se zjistí podregistr SQL Server, virtuální počítač se zaregistruje s rozšířením v jednoduchém režimu. Pokud v registru neexistuje žádný podregistr SQL Server, binární soubory se odeberou.

Po povolení automatické registrace u předplatného se všechny aktuální a budoucí virtuální počítače, které mají nainstalovaný SQL Server, zaregistrují s rozšířením agenta SQL IaaS **v prostém režimu bez výpadků a bez restartování služby SQL Server**. Abyste mohli využít celou sadu funkcí, je nutné [ručně upgradovat na režim úplné správy](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full) . 

> [!IMPORTANT]
> Rozšíření agenta SQL IaaS shromažďuje data pro účely výslovného účelu poskytování dobrovolných výhod při používání SQL Server v rámci Azure Virtual Machines. Microsoft nebude tato data používat pro audity licencování bez předchozího souhlasu zákazníka. Další informace najdete v tématu [SQL Server ochrany osobních údajů](/sql/sql-server/sql-server-privacy#non-personal-data) .

## <a name="prerequisites"></a>Předpoklady

Pokud chcete zaregistrovat SQL Server virtuální počítač s rozšířením, budete potřebovat: 

- [Předplatné Azure](https://azure.microsoft.com/free/) a minimální oprávnění [role přispěvatele](../../../role-based-access-control/built-in-roles.md#all) .
- Model prostředků Azure [Windows Server 2008 R2 (nebo novější)](../../../virtual-machines/windows/quick-create-portal.md) , který je [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) nasazený do veřejného nebo Azure Government cloudu. Systém Windows Server 2008 není podporován. 


## <a name="enable"></a>Povolit

Pokud chcete povolit automatickou registraci SQL Server virtuálních počítačů v Azure Portal, postupujte podle následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejděte na stránku prostředku [**virtuální počítače SQL**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Výběrem **automatického SQL Server registrace virtuálního počítače** otevřete stránku **Automatická registrace** . 

   :::image type="content" source="media/sql-agent-extension-automatic-registration-all-vms/automatic-registration.png" alt-text="Výběrem automatické registrace SQL Server virtuálního počítače otevřete stránku Automatická registrace.":::

1. Z rozevíracího seznamu vyberte své předplatné. 
1. Přečtěte si podmínky a pokud souhlasíte **, vyberte Souhlasím**. 
1. Vyberte možnost **Registrovat** , pokud chcete funkci povolit, a automaticky Zaregistrujte všechny aktuální a budoucí SQL Server virtuální počítače s rozšířením agenta SQL IaaS. Tím se služba SQL Server nerestartuje na žádném z virtuálních počítačů. 

## <a name="disable"></a>Zakázat

Pomocí rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) nebo [Azure PowerShell](/powershell/azure/install-az-ps) zakažte funkci automatického registrace. Pokud je funkce automatické registrace zakázaná, SQL Server virtuální počítače přidané do předplatného musí být ručně zaregistrované pomocí rozšíření agenta SQL IaaS. Tato akce nezruší registraci stávajících SQL Server virtuálních počítačů, které již byly zaregistrovány.



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

Upgradujte režim spravovatelnosti na [úplný](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full) , abyste mohli využít celou sadu funkcí poskytovanou rozšířením agenta SQL IaaS. 
