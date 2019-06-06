---
title: Konfigurace pravidla omezení IP se pravidlo brány firewall webových aplikací pro službu Azure branou
description: Zjistěte, jak nakonfigurovat pravidlo brány firewall webové aplikace můžete omezit IP adresy pro existující koncový bod Azure branou služby.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud;tyao
ms.openlocfilehash: 88c5c284f26203ff3d6c39810a7b2810c1ebbc5a
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743166"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door-service"></a>Konfigurace pravidla omezení IP s firewallem webových aplikací pro službu Azure branou
Tento článek ukazuje postup při konfiguraci pravidel omezení IP ve firewallu webových aplikací (WAF) služby Azure branou pomocí rozhraní příkazového řádku Azure, Azure Powershellu nebo šablony Azure Resource Manageru.

Pravidlo řízení přístupu na základě adresy IP je vlastní pravidlo WAF, která vám umožní řídit přístup k webovým aplikacím. Dělá to tak, že zadáte seznam IP adres nebo rozsahy IP adres ve formátu notace CIDR (Classless Inter-Domain Routing).

Ve výchozím nastavení je webová aplikace přístupná z Internetu. Pokud chcete omezit přístup pro klienty ze seznamu známé IP adresy nebo rozsahy IP adres, musíte vytvořit dvě IP odpovídající pravidla. První vyhovující pravidlo IP obsahuje seznam IP adres jako odpovídající hodnoty a nastaví akci, **povolit**. Pro druhou kolekci s nižší prioritou, blokuje všechny ostatní IP adresy s použitím **všechny** operátor a hodnotu akce **bloku**. Po použití omezení pravidlo IP přijímat požadavky, které pocházejí z adres mimo tento seznam povolených odezvu 403 Zakázáno.  

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Nakonfigurujte zásady WAF pomocí Azure CLI

### <a name="prerequisites"></a>Požadavky
Než začnete konfigurovat zásady omezení IP, nastavení prostředí rozhraní příkazového řádku a vytvořte profil branou služby Azure.

#### <a name="set-up-the-azure-cli-environment"></a>Nastavení prostředí Azure CLI
1. Nainstalujte [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli), nebo použít Azure Cloud Shell. Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z webu Azure Portal. Má předinstalované rozhraní Azure CLI, které je nakonfigurované pro použití s vaším účtem. Vyberte **vyzkoušet** tlačítko v příkazech rozhraní příkazového řádku, které podle a pak se přihlaste ke svému účtu Azure v relaci služby Cloud Shell, které se otevře. Po spuštění relace, zadejte `az extension add --name front-door` přidat rozšíření branou služby Azure.
 2. Pokud používáte rozhraní příkazového řádku místně v prostředí Bash, přihlaste se k Azure s použitím `az login`.

#### <a name="create-an-azure-front-door-service-profile"></a>Vytvoření profilu branou služby Azure
Vytvoření profilu branou služby Azure podle pokynů v tématu [rychlý start: Vytvoření branou pro vysoce dostupná globální webovou aplikaci](quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>Vytvořit zásadu WAF

Vytvořit zásadu WAF pomocí [az waf – zásady sítě vytvořit](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create) příkazu. V příkladu, způsobem, nahraďte název zásady *IPAllowPolicyExampleCLI* s jedinečný název zásady.

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Přidat vlastní pravidlo řízení přístupu IP

Použití [vytvořit vlastní pravidlo az sítě waf zásady](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create) příkaz pro přidání vlastní IP pravidlo řízení přístupu pro zásady WAF jste právě vytvořili.

V následujících příkladech:
-  Nahraďte *IPAllowPolicyExampleCLI* s jedinečné zásady vytvořili dříve.
-  Nahraďte *ip adresa rozsahu 1*, *ip adresa rozsahu 2* s vlastní rozsah.

Nejprve vytvořte IP povolit pravidlo pro zadané adresy.

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch "<ip-address-range-1>","<ip-address-range-2>" \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
Dále vytvořte **blokovat veškerý** pravidla s nižší prioritou než ta předchozí **povolit** pravidlo. Znovu nahraďte *IPAllowPolicyExampleCLI* v následujícím příkladu nahraďte jedinečné zásady, kterou jste vytvořili dříve.

```azurecli
az network waf-policy custom-rule create \
  --name IPDenyAllRule\
  --priority 2 \
  --rule-type MatchRule \
  --match-condition RemoteAddr Any
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
    
### <a name="find-the-id-of-a-waf-policy"></a>Najít ID zásad WAF 
Najít ID WAF zásad [az sítě waf-policy show](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show) příkazu. Nahraďte *IPAllowPolicyExampleCLI* v následujícím příkladu nahraďte jedinečné zásady, kterou jste vytvořili dříve.

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Odkaz zásady WAF do hostitele front-endu Azure branou služby

Nastavení služby Azure branou *WebApplicationFirewallPolicyLink* ID pro ID zásad s použitím [az sítě branou – aktualizace](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) příkazu. Nahraďte *IPAllowPolicyExampleCLI* s jedinečné zásady, kterou jste vytvořili dříve.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
V tomto příkladu je pro použije zásada z WAF **FrontendEndpoints [0]** . Zásady WAF můžete propojit některé z vašich front-endů.
> [!Note]
> Je nutné nastavit **WebApplicationFirewallPolicyLink** vlastnost pouze jednou k propojení zásadu WAF do front-endem branou služby Azure. Další zásady aktualizace automaticky nainstalují do front-endu.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Konfigurace WAF zásady pomocí Azure Powershellu

### <a name="prerequisites"></a>Požadavky
Než začnete konfigurovat zásady omezení IP, nastavení prostředí PowerShell a vytvořte profil branou služby Azure.

#### <a name="set-up-your-powershell-environment"></a>Nastavení prostředí PowerShell
Prostředí Azure PowerShell poskytuje sadu rutin, které použijte [Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) model pro správu prostředků Azure.

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) můžete nainstalovat na místní počítač a používat v jakékoli relaci PowerShellu. Postupujte podle pokynů na stránce pro přihlášení k prostředí PowerShell s použitím přihlašovacích údajů Azure a pak nainstalujte modul Az.

1. Připojení k Azure pomocí následujícího příkazu a pak použít interaktivní dialogové okno pro přihlášení.
    ```
    Connect-AzAccount
    ```
 2. Než nainstalujete modul branou služby Azure, ujistěte se, že máte aktuální verzi nainstalovaným modulem PowerShellGet. Spusťte následující příkaz a poté ji znovu otevřete prostředí PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Pomocí následujícího příkazu nainstalujte modul Az.FrontDoor. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-service-profile"></a>Vytvoření profilu branou služby Azure
Vytvoření profilu branou služby Azure podle pokynů v tématu [rychlý start: Vytvoření branou pro vysoce dostupná globální webovou aplikaci](quickstart-create-front-door.md).

### <a name="define-an-ip-match-condition"></a>Definujte podmínku shody IP
Použití [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) příkaz, který definuje podmínku shody IP.
V následujícím příkladu nahraďte *ip adresa rozsahu 1*, *ip adresa rozsahu 2* s vlastní rozsah.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue ["ip-address-range-1", "ip-address-range-2"]
```
Vytvořit integrační balíček *všechny podmínky odpovídají* pravidlo s použitím následujícího příkazu:
```powershell
$IPMatchALlCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty Any        
  ```
    
### <a name="create-a-custom-ip-allow-rule"></a>Vytvořit vlastní pravidlo povolit IP

Použití [New-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) příkaz Definovat akce a nastavit prioritu. V následujícím příkladu bude možné požadavky od klienta IP adresy, které se shodují se seznamem.

```powershell
$IPAllowRule = New-AzFrontDoorCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Allow -Priority 1
```
Vytvoření **blokovat veškerý** pravidla s nižší prioritou než předchozí IP **povolit** pravidlo.
```powershell
$IPBlockAll = New-AzFrontDoorCustomRuleObject `
-Name "IPDenyAll" `
-RuleType MatchRule `
-MatchCondition $IPMatchALlCondition `
-Action Block `
-Priority 2
```

### <a name="configure-a-waf-policy"></a>Nakonfigurujte zásady WAF
Najít název skupiny prostředků, který obsahuje profil branou služby Azure s použitím `Get-AzResourceGroup`. Dalším kroku nakonfigurujte zásadu WAF se IP adresa **blokovat veškerý** pravidlo s použitím [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Odkaz zásady WAF do hostitele front-endu Azure branou služby

Propojte objekt zásad WAF existující front-endu hostitele a aktualizace Azure branou vlastnosti služby. Nejdřív, načtěte objekt branou služby Azure s použitím [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Dále nastavte **WebApplicationFirewallPolicyLink** vlastnost ID prostředku *$IPAllowPolicyExamplePS*, které byly vytvořeny v předchozím kroku, s použitím [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)příkazu.

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> V tomto příkladu je pro použije zásada z WAF **FrontendEndpoints [0]** . Zásady WAF můžete propojit některé z vašich front-endů. Je nutné nastavit **WebApplicationFirewallPolicyLink** vlastnost pouze jednou k propojení zásadu WAF do front-endem branou služby Azure. Další zásady aktualizace automaticky nainstalují do front-endu.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Nakonfigurujte zásady WAF pomocí šablony Resource Manageru
Pokud chcete zobrazit šablonu, která vytvoří zásadu branou služby Azure a zásadu WAF s vlastní pravidla pro omezení IP, přejděte na [Githubu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Další postup

- Zjistěte, jak [vytvořit profil služby Azure branou](quickstart-create-front-door.md).
