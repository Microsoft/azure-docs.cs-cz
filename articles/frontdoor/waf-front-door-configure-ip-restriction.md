---
title: Konfigurace pravidla omezení IP adres s pravidlem brány firewall webových aplikací pro službu Azure front-dveří
description: Naučte se konfigurovat pravidlo firewallu webových aplikací k omezení IP adres pro existující koncový bod služby Azure front-dveří.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: a610a2c01a1e935c55942b621e5b3799cb002fc0
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698645"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door-service"></a>Konfigurace pravidla omezení IP adres pomocí brány firewall webových aplikací pro službu Azure front-dveří
V tomto článku se dozvíte, jak nakonfigurovat pravidla omezení IP adres v bráně firewall webových aplikací (WAF) pro službu Azure front-dveří pomocí Azure CLI, Azure PowerShell nebo šablony Azure Resource Manager.

Pravidlo řízení přístupu na základě IP adresy je vlastní pravidlo WAF, které umožňuje řídit přístup k vašim webovým aplikacím. To se dělá zadáním seznamu IP adres nebo rozsahů IP adres ve formátu CIDR (Classless Inter-Domain Routing).

Ve výchozím nastavení je webová aplikace přístupná z Internetu. Pokud chcete omezit přístup ke klientům ze seznamu známých IP adres nebo rozsahů IP adres, musíte vytvořit dvě pravidla pro odpovídající IP adresy. První pravidlo pro porovnání IP obsahuje seznam IP adres jako vyhovujících hodnot a nastavuje akci, která má být **povolena**. Druhá sekunda s nižší prioritou zablokuje všechny ostatní IP adresy pomocí operátoru **All** a nastaví akci, která mábýt zablokovaná. Po použití pravidla omezení IP adresy budou žádosti, které pocházejí z adres mimo tento seznam povolených, přijmout odpověď 403 Forbidden.  

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Konfigurace zásady WAF pomocí Azure CLI

### <a name="prerequisites"></a>Požadavky
Než začnete konfigurovat zásady omezení IP adres, nastavte prostředí rozhraní příkazového řádku a vytvořte profil služby front-dveří pro Azure.

#### <a name="set-up-the-azure-cli-environment"></a>Nastavení prostředí Azure CLI
1. Nainstalujte rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli)nebo použijte Azure Cloud Shell. Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z webu Azure Portal. Má předinstalované rozhraní Azure CLI, které je nakonfigurované pro použití s vaším účtem. V následujících příkazech rozhraní příkazového řádku vyberte tlačítko **vyzkoušet** a pak se přihlaste ke svému účtu Azure v otevřené relaci Cloud Shell. Po spuštění relace zadejte `az extension add --name front-door` pro přidání rozšíření služby front-dveří pro Azure.
 2. Pokud používáte rozhraní příkazového řádku místně v bash, přihlaste se k Azure `az login`pomocí.

#### <a name="create-an-azure-front-door-service-profile"></a>Vytvoření profilu služby front-dveří pro Azure
Pomocí pokynů popsaných v [rychlém startu vytvořte profil služby Azure front-dveří: Vytvořte si přední dveře pro globální webovou aplikaci](quickstart-create-front-door.md)s vysokou dostupností.

### <a name="create-a-waf-policy"></a>Vytvoření zásady WAF

Vytvořte zásadu WAF pomocí příkazu [AZ Network WAF-Policy Create](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-create) . V následujícím příkladu nahraďte název zásady *IPAllowPolicyExampleCLI* jedinečným názvem zásady.

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Přidat vlastní pravidlo řízení přístupu IP

Pomocí příkazu [AZ Network WAF-Policy Custom-Rule Create](/cli/azure/ext/front-door/network/front-door/waf-policy/rule?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-rule-create) přidejte vlastní pravidlo řízení přístupu IP pro zásadu WAF, kterou jste právě vytvořili.

V následujících příkladech:
-  Nahraďte *IPAllowPolicyExampleCLI* jedinečnými zásadami, které jste vytvořili dříve.
-  Nahraďte *IP adresou-rozsah-1*, *IP adresou-Range-2* a vlastním rozsahem.

Nejprve vytvořte pravidlo povolení protokolu IP pro zadané adresy.

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch ("<ip-address-range-1>","<ip-address-range-2>") \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
Dále vytvořte **blok všechna** pravidla s nižší prioritou, než má předchozí pravidlo **Povolení** . V následujícím příkladu nahraďte *IPAllowPolicyExampleCLI* jedinečnými zásadami, které jste vytvořili dříve.

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
    
### <a name="find-the-id-of-a-waf-policy"></a>Najít ID zásady WAF 
ID zásady WAF najdete pomocí příkazu [AZ Network WAF-Policy show](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show) . V následujícím příkladu nahraďte *IPAllowPolicyExampleCLI* jedinečnými zásadami, které jste vytvořili dříve.

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Propojení zásady WAF s front-end službou Azure front-endu

Pomocí příkazu [AZ Network front-dvířk Update](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) nastavte *WebApplicationFirewallPolicyLink* ID služby Azure front-dveří na ID zásad. Nahraďte *IPAllowPolicyExampleCLI* vašimi jedinečnými zásadami, které jste vytvořili dříve.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
V tomto příkladu se zásada WAF aplikuje na **FrontendEndpoints [0]** . Zásady WAF můžete propojit s libovolným front-endy.
> [!Note]
> Aby bylo možné propojit zásady WAF na front-end služby Azure front-endu, musíte nastavit vlastnost **WebApplicationFirewallPolicyLink** jenom jednou. Následné aktualizace zásad se automaticky aplikují na front-end.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Konfigurace zásady WAF pomocí Azure PowerShell

### <a name="prerequisites"></a>Požadavky
Než začnete konfigurovat zásady omezení IP adres, nastavte prostředí PowerShell a vytvořte profil služby front-dveří pro Azure.

#### <a name="set-up-your-powershell-environment"></a>Nastavení prostředí PowerShell
Azure PowerShell poskytuje sadu rutin, které používají model [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) pro správu prostředků Azure.

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) můžete nainstalovat na místní počítač a používat v jakékoli relaci PowerShellu. Podle pokynů na stránce se přihlaste do PowerShellu pomocí vašich přihlašovacích údajů Azure a pak nainstalujte modul AZ Module.

1. Pomocí následujícího příkazu se připojte k Azure a pak se přihlaste pomocí interaktivního dialogu.
    ```
    Connect-AzAccount
    ```
 2. Než nainstalujete modul služby front-dveří pro Azure, ujistěte se, že máte nainstalovanou aktuální verzi modulu PowerShellGet. Spusťte následující příkaz a pak znovu otevřete PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Pomocí následujícího příkazu nainstalujte modul AZ. FrontDoor. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-service-profile"></a>Vytvoření profilu služby front-dveří pro Azure
Pomocí pokynů popsaných v [rychlém startu vytvořte profil služby Azure front-dveří: Vytvořte si přední dveře pro globální webovou aplikaci](quickstart-create-front-door.md)s vysokou dostupností.

### <a name="define-an-ip-match-condition"></a>Definování podmínky shody IP
K definování podmínky shody IP použijte příkaz [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) .
V následujícím příkladu nahraďte *IP adresou-rozsah-1*, *IP adresou-Range-2* vlastním rozsahem.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
```
Pomocí následujícího příkazu vytvořte shodu protokolu IP s veškerým pravidlem *podmínky* :
```powershell
$IPMatchALlCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty Any        
  ```
    
### <a name="create-a-custom-ip-allow-rule"></a>Vytvoření vlastního pravidla povolení IP adresy

Pomocí příkazu [New-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) definujte akci a nastavte prioritu. V následujícím příkladu budou povoleny požadavky z IP adres klientů, které se shodují se seznamem.

```powershell
$IPAllowRule = New-AzFrontDoorCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Allow -Priority 1
```
Vytvoří **blok všechna** pravidla s nižší prioritou, než je předchozí pravidlo **Povolení** protokolu IP.
```powershell
$IPBlockAll = New-AzFrontDoorCustomRuleObject `
-Name "IPDenyAll" `
-RuleType MatchRule `
-MatchCondition $IPMatchALlCondition `
-Action Block `
-Priority 2
```

### <a name="configure-a-waf-policy"></a>Konfigurace zásad WAF
Vyhledejte název skupiny prostředků, která obsahuje profil služby front-dveří pro Azure, pomocí `Get-AzResourceGroup`. V dalším kroku nakonfigurujte pomocí [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)zásadu WAF s protokolem IP s **blokováním všechna** pravidla.

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Propojení zásady WAF s front-end službou Azure front-endu

Propojte objekt zásad WAF s existujícím front-end hostitelem a aktualizujte vlastnosti služby front-endu Azure. Nejdřív načtěte objekt služby front-dveří Azure pomocí [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Dále nastavte vlastnost **WebApplicationFirewallPolicyLink** na ID prostředku *$IPAllowPolicyExamplePS*vytvořené v předchozím kroku pomocí příkazu [set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) .

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> V tomto příkladu se zásada WAF aplikuje na **FrontendEndpoints [0]** . Zásady WAF můžete propojit s libovolným front-endy. Aby bylo možné propojit zásady WAF na front-end služby Azure front-endu, musíte nastavit vlastnost **WebApplicationFirewallPolicyLink** jenom jednou. Následné aktualizace zásad se automaticky aplikují na front-end.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Konfigurace zásady WAF pomocí šablony Správce prostředků
Pokud chcete zobrazit šablonu, která vytvoří zásady služby front-dveří Azure a zásady WAF s vlastními pravidly omezení IP adres, přečtěte si [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Další postup

- Přečtěte si, jak [vytvořit profil služby front-dveří pro Azure](quickstart-create-front-door.md).
