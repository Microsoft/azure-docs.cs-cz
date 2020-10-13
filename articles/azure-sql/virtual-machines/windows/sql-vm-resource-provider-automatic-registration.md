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
ms.openlocfilehash: 75f68a4de2db0c4c9102a58da12d80cc273a6e80
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931134"
---
# <a name="automatic-registration-with-sql-vm-resource-provider"></a>Automatická registrace pomocí poskytovatele prostředků virtuálních počítačů SQL
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Povolení funkce automatické registrace v Azure Portal pro automatické registraci všech současných a budoucích SQL Server na virtuálních počítačích Azure pomocí poskytovatele prostředků virtuálních počítačů SQL v odlehčeném režimu.

V tomto článku se naučíte, jak povolit funkci automatické registrace. Případně můžete [zaregistrovat jeden virtuální počítač](sql-vm-resource-provider-register.md)nebo [hromadně zaregistrovat virtuální](sql-vm-resource-provider-bulk-register.md) počítače pomocí poskytovatele prostředků virtuálního počítače SQL. 

## <a name="overview"></a>Přehled

[Poskytovatel prostředků virtuálního počítače SQL](sql-vm-resource-provider-register.md#overview) umožňuje spravovat SQL Server virtuální počítač z Azure Portal. Poskytovatel prostředků navíc umožňuje robustní sadu funkcí, včetně [automatizovaných oprav](automated-patching.md), [automatizovaného zálohování](automated-backup.md)a možností monitorování a správy. Také odemkne [licencování](licensing-model-azure-hybrid-benefit-ahb-change.md) a flexibilitu [edice](change-sql-server-edition.md) . Dříve byly tyto funkce dostupné jenom pro SQL Server imagí virtuálních počítačů nasazených z Azure Marketplace. 

Funkce automatické registrace umožňuje zákazníkům automaticky registrovat všechny aktuální a budoucí SQL Server virtuální počítače v předplatném Azure pomocí poskytovatele prostředků virtuálních počítačů SQL. To se liší od Ruční registrace, která se zaměřuje jenom na aktuální SQL Server virtuální počítače. 

Automatická registrace registruje virtuální počítače s SQL Server v jednoduchém režimu. Abyste mohli využít celou sadu funkcí, je nutné [ručně upgradovat na režim úplné správy](sql-vm-resource-provider-register.md#upgrade-to-full) . 

## <a name="prerequisites"></a>Požadavky

Pokud chcete zaregistrovat SQL Server virtuální počítač s poskytovatelem prostředků, budete potřebovat: 

- [Předplatné Azure](https://azure.microsoft.com/free/)
- [Virtuální počítač s Windows](../../../virtual-machines/windows/quick-create-portal.md) Resource modelem Azure s [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) nasazený do veřejného nebo Azure Governmentho cloudu. 


## <a name="enable"></a>Povolit

Pokud chcete povolit automatickou registraci SQL Server virtuálních počítačů v Azure Portal, postupujte podle těchto pokynů:

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

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete automatickou registraci zakázat pomocí Azure PowerShell, spusťte následující příkaz: 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---


## <a name="next-steps"></a>Další kroky

Upgradujte režim spravovatelnosti na [úplný](sql-vm-resource-provider-register.md#upgrade-to-full) , abyste mohli využít celou sadu funkcí poskytovanou poskytovatelem prostředků virtuálního počítače SQL. 
