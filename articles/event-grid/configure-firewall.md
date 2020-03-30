---
title: Konfigurace ip firewallu pro témata nebo domény Azure Event Grid (Preview)
description: Tento článek popisuje, jak nakonfigurovat nastavení brány firewall pro témata nebo domény služby Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: b195872ca1002970fa96ae133d5eb47a9267796d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299865"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains-preview"></a>Konfigurace ip firewallu pro témata nebo domény Azure Event Grid (Preview)
Ve výchozím nastavení jsou téma a doména přístupné z internetu, pokud je požadavek dodáván s platným ověřováním a autorizací. S ip firewallem ji můžete dále omezit pouze na sadu adres IPv4 nebo rozsahy adres IPv4 v zápisu [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) Vydavatelé pocházející z jakékoli jiné IP adresy budou odmítnuti a obdrží odpověď 403 (Forbidden). Další informace o funkcích zabezpečení sítě podporovaných programem Event Grid naleznete v [tématu Zabezpečení sítě pro program Event Grid](network-security.md).

Tento článek popisuje, jak nakonfigurovat nastavení brány firewall IP pro témata nebo domény služby Azure Event Grid.

## <a name="use-azure-portal"></a>Použití webu Azure Portal
V této části se ukazuje, jak pomocí portálu Azure vytvořit příchozí pravidla brány firewall IP. Kroky uvedené v této části jsou určeny pro témata. Podobné kroky můžete použít k vytvoření příchozích pravidel IP pro **domény**. 

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na téma nebo doménu mřížky událostí a přepněte na kartu **Síť.**
2. Výběrem **možnosti Veřejné sítě** povolíte přístup k prostředku všem sítím, včetně Internetu. 

    Provoz můžete omezit pomocí pravidel brány firewall založených na protokolu IP. Zadejte jednu adresu IPv4 nebo rozsah adres IP v zápisu mezidoménového směrování (CIDR) bez třídy. 

    ![Stránka Veřejných sítí](./media/configure-firewall/public-networks-page.png)
3. Vyberte **soukromé koncové body pouze** pro povolení přístupu k tomuto prostředku pouze privátním koncovým bodům. Ke správě připojení použijte kartu **Soukromá připojení koncového bodu** na této stránce. 

    ![Stránka Veřejných sítí](./media/configure-firewall/private-endpoints-page.png)
4. Na panelu nástrojů vyberte **Uložit**. 



## <a name="use-azure-cli"></a>Použití Azure CLI
V této části se zobrazí, jak pomocí příkazů Azure CLI vytvářet témata s pravidly příchozí IP adresy. Kroky uvedené v této části jsou určeny pro témata. Podobné kroky můžete použít k vytvoření příchozích pravidel IP pro **domény**. 


### <a name="enable-public-network-access-for-an-existing-topic"></a>Povolení přístupu k veřejné síti pro existující téma
Ve výchozím nastavení je přístup k veřejné síti povolen pro témata a domény. Provoz můžete omezit konfigurací příchozích pravidel brány firewall IP. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Enabled\""}}"
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Zakázání přístupu k veřejné síti pro existující téma
Pokud je přístup k veřejné síti pro téma nebo doménu zakázán, není povolen provoz přes veřejný internet. K těmto prostředkům budou mít přístup pouze soukromá připojení koncového bodu. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Disabled\""}}"
```

### <a name="create-topic-with-inbound-ip-rules"></a>Vytvoření tématu s pravidly příchozí IP adresy
Následující ukázkový příkaz příkazu PŘÍKAZKového příkazu vytvoří téma mřížky událostí s pravidly příchozí IP adresy v jednom kroku. 

```azurecli-interactive
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"",\""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}
```

### <a name="create-topic-first-and-then-add-inbound-ip-rules"></a>Nejprve vytvořte téma a pak přidejte příchozí pravidla IP
Tento příklad nejprve vytvoří téma mřížky událostí a poté přidá příchozí pravidla IP pro toto téma v samostatném příkazu. Aktualizuje také příchozí pravidla IP, která byla nastavena v druhém příkazu. 

```azurecli-interactive

# create the event grid topic first
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
    --body {\""location\"":\""<LOCATION>\""}

# add inbound IP rules
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" 
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"", \""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}

# later, update topic with additional ip rules or remove them. 
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" 
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"", \""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""}, {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}
```


## <a name="use-powershell"></a>Použití prostředí PowerShell
V této části se ukazuje, jak pomocí příkazů Azure PowerShellu vytvářet témata Azure Event Grid s pravidly vstupní brány firewall IP. Kroky uvedené v této části jsou určeny pro témata. Podobné kroky můžete použít k vytvoření příchozích pravidel IP pro **domény**. 

### <a name="prerequisite"></a>Požadavek
Postupujte podle pokynů z [funkce Postup: Pomocí portálu vytvořte instanční objekt azure a služby Azure AD, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md) k vytvoření aplikace Azure Active Directory a poznamenejte si následující hodnoty:

- ID adresáře (tenanta)
- ID aplikace (klienta)
- Tajný klíč aplikace (klienta)

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

### <a name="enable-public-network-access-for-an-existing-topic"></a>Povolení přístupu k veřejné síti pro existující téma
Ve výchozím nastavení je přístup k veřejné síti povolen pro témata a domény. Provoz můžete omezit konfigurací příchozích pravidel brány firewall IP. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Zakázání přístupu k veřejné síti pro existující téma
Pokud je přístup k veřejné síti pro téma nebo doménu zakázán, není povolen provoz přes veřejný internet. K těmto prostředkům budou mít přístup pouze soukromá připojení koncového bodu. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>Vytvoření tématu mřížky událostí s příchozími pravidly v jednom kroku

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that inbound IP rules are included. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# create the event grid topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5
```


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>Nejprve vytvořte téma mřížky událostí a poté přidejte příchozí pravidla IP

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that no inbound IP rules are specified. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled";}} | ConvertTo-Json -Depth 5

# create the Event Grid topic
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body`

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

# prepare the body for REST PUT method. Notice that it includes inbound IP rules now. This feature available in both basic and premium tiers.
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"}, @{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# update the topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was updated
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AzURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

```

## <a name="next-steps"></a>Další kroky

* Informace o dodávkách událostí monitorování naleznete v [tématu Sledování doručování zpráv v programu Event Grid](monitor-event-delivery.md).
* Další informace o ověřovacím klíči naleznete v tématu [Zabezpečení a ověřování mřížky událostí](security-authentication.md).
* Další informace o vytvoření předplatného Služby Azure Event Grid najdete v [tématu schéma předplatného služby Event Grid](subscription-creation-schema.md).
