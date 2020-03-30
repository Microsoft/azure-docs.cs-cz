---
title: Aktualizace cenové úrovně tématu nebo domény Sítě událostí Azure
description: Tento článek popisuje, jak aktualizovat cenovou úroveň tématu nebo domény Azure Event Grid (základní na premium, premium na základní) pomocí portálu Azure, Azure CLI a Azure PowerShell.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 1e92a9f0b9ba007b289c634995b04f4f1575310a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79300138"
---
# <a name="update-pricing-tier"></a>Aktualizovat cenovou úroveň 
Tento článek ukazuje, jak aktualizovat cenovou úroveň tématu nebo domény Azure Event Grid pomocí portálu Azure, Azure CLI a Azure PowerShellu. 

## <a name="use-azure-portal"></a>Použití webu Azure Portal
Tato část ukazuje, jak změnit cenovou úroveň tématu nebo domény na webu Azure Portal. 

### <a name="overview-page"></a>Stránka s přehledem
Cenovou úroveň tématu nebo domény můžete změnit na stránce **Přehled.** Následující příklad ukazuje, jak upgradovat téma ze základní úrovně na úroveň premium. Kroky k přechodu z úrovně premium na základní úroveň jsou podobné.

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na stránku tématu nebo domény. 
2. Na stránce **Přehled** vyberte aktuální cenovou úroveň (v následujícím příkladu je **základní**.)
    
    ![Výběr aktuální cenové úrovně](./media/update-tier/select-tier.png)
3. Na stránce **Cenová úroveň** změňte úroveň a vyberte **OK**. 

    ![Aktualizace cenové úrovně](./media/update-tier/change-tier.png)
4. Zkontrolujte stav operace na stránce **Oznámení.**

    ![Aktualizovat stav](./media/update-tier/status.png)    
5. Potvrďte, že se na stránce **Přehled** zobrazuje aktualizovaná vrstva. 

    ![Aktualizovat stav](./media/update-tier/changed-tier.png)

### <a name="networking-page"></a>Stránka Sítě
Můžete **upgradovat** ze základní vrstvy na úroveň premium na stránce **Sítě.** Na této stránce však nelze přejít z úrovně premium na základní úroveň. 

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na stránku tématu nebo domény. 
2. Na stránce **Síť** přepněte na kartu **Soukromá připojení koncového bodu (preview).** 
3. Pokud je aktuální cenová úroveň **základní**, zobrazí se následující zpráva. Vyberte ji. 

    ![Aktualizovat úroveň na stránce připojení privátního koncového bodu](./media/update-tier/private-endpoint-connections-page.png)
4. Na stránce **Aktualizovat na úroveň ocenění premium** vyberte **Ano**. 
    
    ![Potvrdit upgrade](./media/update-tier/message-private-endpoint-connection.png)
5. Zkontrolujte stav operace na stránce **Oznámení.**

    ![Aktualizovat stav](./media/update-tier/status.png)



## <a name="use-azure-cli"></a>Použití Azure CLI
V této části se zobrazí postup, jak pomocí příkazů Azure CLI změnit cenovou úroveň tématu nebo domény. 

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Upgrade tématu ze základního na prémiový

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Downgrade téma z prémie na základní

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Upgrade domény ze základní na prémiovou

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Přechod na nižší verzi domény z prémiového na základní

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```



## <a name="use-azure-powershell"></a>Použití Azure Powershell
V této části se zobrazí postup, jak pomocí příkazů Prostředí PowerShell změnit cenovou úroveň tématu nebo domény. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Příprava tokenu a záhlaví pro volání rozhraní REST API 
Spusťte následující příkazy předpokladů, abyste získali ověřovací token pro použití s voláním rozhraní REST API a autorizací a dalšími informacemi o záhlaví. 

```azurepowershell-interactive
# replace <CLIENT ID> and <CLIENT SECRET>
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get the authentication token. Replace <TENANT ID>
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token `
    -Body $body `
    -ContentType 'application/x-www-form-urlencoded'

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
$Headers.Add("Content-Type","application/json")
```

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Upgrade tématu ze základního na prémiový

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Downgrade téma z prémie na základní

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Upgrade domény ze základní na prémiovou

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Přechod na nižší verzi domény z prémiového na základní

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

## <a name="next-steps"></a>Další kroky
Pro témata a domény úrovně premium můžete nakonfigurovat soukromé koncové body tak, aby omezovaly přístup pouze z vybraných virtuálních sítí. Podrobné pokyny naleznete v tématu [Konfigurace soukromých koncových bodů](configure-private-endpoints.md).
