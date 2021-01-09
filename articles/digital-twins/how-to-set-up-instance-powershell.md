---
title: Nastavení instance a ověřování (PowerShell)
titleSuffix: Azure Digital Twins
description: Přečtěte si, jak nastavit instanci služby Azure Digital revlákens pomocí Azure PowerShell
author: baanders
ms.author: baanders
ms.date: 12/16/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 65495da13bc6c9ed91c1ecbd587c16c949136d73
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98040683"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-powershell"></a>Nastavení instance a ověřování digitálních vláken Azure (PowerShell)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Tento článek popisuje postup **Nastavení nové instance digitálního vlákna Azure**, včetně vytvoření instance a nastavení ověřování. Po dokončení tohoto článku budete mít instanci digitálních vláken Azure, která je připravená na zahájení programování.

Tato verze tohoto článku prochází těmito kroky ručně, jednou po jednom, pomocí Azure PowerShell.

* Pokud chcete projít tyto kroky ručně pomocí Azure Portal, přečtěte si článek verze tohoto článku na portálu: [*Postup: nastavení instance a ověřování (portál)*](how-to-set-up-instance-portal.md).
* Chcete-li provést automatickou instalaci pomocí skriptu nasazení, přečtěte si skriptovaná verze tohoto článku: [*Postupy: nastavení instance a ověřování (skriptované)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prepare-your-environment"></a>Příprava prostředí

1. Pokud se rozhodnete použít Azure PowerShell lokálně:
   1. [Nainstalujte modul AZ PowerShell](/powershell/azure/install-az-ps).
   1. Připojte se k účtu Azure pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .
1. Pokud se rozhodnete použít Azure Cloud Shell:
   1. Další informace najdete v tématu [přehled Azure Cloud Shell](../cloud-shell/overview.md) .
   1. Na panelu s ikonami Cloud Shell se ujistěte, že je Cloud Shell nastavená tak, aby běžela verze prostředí PowerShell.

      :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Okno Cloud Shell znázorňující výběr verze prostředí PowerShell":::

1. Pokud máte více předplatných Azure, vyberte příslušné předplatné, ve kterém se prostředky mají fakturovat. Vyberte konkrétní předplatné pomocí rutiny [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. Pokud používáte digitální vlákna Azure s tímto předplatným, je nutné zaregistrovat poskytovatele prostředků **Microsoft. DigitalTwins** .

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.DigitalTwins
   ```

> [!IMPORTANT]
> I když je modul PowerShell **AZ. DigitalTwins** ve verzi Preview, musíte ho nainstalovat samostatně pomocí `Install-Module` rutiny. Jakmile bude tento powershellový modul obecně dostupný, stane se součástí budoucích verzí modulu Az PowerShellu a bude ve výchozím nastavení dostupný v rámci Azure Cloud Shellu.

```azurepowershell-interactive
Install-Module -Name Az.DigitalTwins
```

## <a name="create-the-azure-digital-twins-instance"></a>Vytvoření instance digitálních vláken Azure

V této části **vytvoříte novou instanci digitálních vláken Azure** pomocí Azure PowerShell.
Budete muset zadat:

* [Skupina prostředků Azure](../azure-resource-manager/management/overview.md). Pokud ještě nemáte existující skupinu prostředků, můžete ji vytvořit pomocí rutiny [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) .

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <name-for-your-resource-group> -Location <region>
  ```

* Oblast pro nasazení. Pokud chcete zjistit, které oblasti podporují digitální vlákna Azure, přejděte na [*produkty Azure dostupné v jednotlivých oblastech*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Název vaší instance. Název nové instance musí být jedinečný v rámci oblasti vašeho předplatného. Pokud má vaše předplatné jinou instanci digitálních vláken Azure v oblasti, která už používá zadaný název, zobrazí se výzva k výběru jiného názvu.

Použijte své hodnoty v následujícím příkladu pro vytvoření instance:

```azurepowershell-interactive
New-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> -Location <region>
```

### <a name="verify-success-and-collect-important-values"></a>Ověřit úspěšné a shromažďovat důležité hodnoty

Pokud se instance úspěšně vytvořila, výsledek bude vypadat podobně jako v následujícím výstupu, který obsahuje informace o prostředku, který jste vytvořili:

```Output
Location   Name                                         Type
--------   ----                                         ----
<region>   <name-for-your-Azure-Digital-Twins-instance> Microsoft.DigitalTwins/digitalTwinsInstances
```

> [!TIP]
> Tyto vlastnosti můžete zobrazit společně se všemi vlastnostmi vaší instance, a to kdykoli spuštěním a vytvořením `Get-AzDigitalTwinsInstance` potrubí na `Select-Object -Property *` .

```azurepowershell-interactive
Get-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> |
  Select-Object -Property *
```

Poznamenejte si název **hostitele**, **název** a **zdroj** instance služby Azure Digital revlákens. Jedná se o důležité hodnoty, které možná budete potřebovat při práci s instancí digitálních vláken Azure, k nastavení ověřování a souvisejících prostředků Azure. Pokud budou jiné uživatele programovat s instancí, měli byste je s těmito hodnotami sdílet.

Teď máte k dispozici instanci digitálních vláken Azure, která je připravená k přechodu. V dalším kroku poskytnete příslušnému uživateli Azure oprávnění ke správě.

## <a name="set-up-user-access-permissions"></a>Nastavení uživatelských oprávnění pro přístup

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Určete **objectID** pro účet Azure AD uživatele, kterému má být přiřazena role pomocí rutiny [Get-AzAdUser](/powershell/module/az.resources/get-azaduser) .

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName <Azure-AD-user-principal-name-of-user-to-assign>
```

K přiřazení role použijte následující příkaz. Musí ho spustit uživatel s [dostatečnými oprávněními](#prerequisites-permission-requirements) v předplatném Azure. Příkaz vyžaduje předání **identifikátoru objectID** pro účet služby Azure AD, kterému má být přiřazena role. Také vyžaduje, abyste používali stejné ID předplatného, název skupiny prostředků a název instance digitálního vlákna Azure, který jste zvolili dříve.

```azurepowershell-interactive
$Params = @{
  ObjectId = '<Azure-AD-user-object-id-of-user-to-assign>'
  RoleDefinitionName = 'Azure Digital Twins Data Owner'
  Scope = '/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<name-for-your-Azure-Digital-Twins-instance>'
}
New-AzRoleAssignment @Params
```

Výsledkem tohoto příkazu jsou informace o vytvořeném přiřazení role.

### <a name="verify-success"></a>Ověřit úspěch

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Teď máte k dispozici instanci digitálních vláken Azure, která je připravená k použití, a máte přiřazená oprávnění ke správě IT.

## <a name="next-steps"></a>Další kroky

Podívejte se, jak připojit klientskou aplikaci k instanci s ověřovacím kódem:
* [*Postupy: psaní kódu ověřování aplikace*](how-to-authenticate-client.md)
