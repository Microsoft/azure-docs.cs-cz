---
title: Konfigurace pravidla omezení IP s pravidlem brány firewall webové aplikace pro Azure branou
description: Zjistěte, jak nakonfigurovat IP adresu omezení WAF pravidel pro existující koncový bod branou.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: kumud;tyao
ms.openlocfilehash: dae2bb8ece9ef56c0999e0f89abbf6f8d8e950e2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242929"
---
# <a name="configure-an-ip-restriction-rule-with-web-application-firewall-for-azure-front-door-preview"></a>Konfigurace pravidla omezení IP s firewallem webových aplikací pro Azure branou (Preview)
 Tento článek ukazuje, jak nakonfigurovat IP omezení pravidel firewallu webových aplikací (WAF) pro branou pomocí rozhraní příkazového řádku Azure, Azure Powershellu nebo Azure Resource Manageru šablony.

Pravidlo řízení přístupu IP adresu na základě je vlastní pravidlo WAF, která umožňuje řídit přístup k webovým aplikacím tak, že zadáte seznam IP adres nebo rozsahy IP adres v podobě notace CIDR (Classless Inter-Domain Routing).

Ve výchozím nastavení je webová aplikace přístupná z Internetu. Pokud chcete omezit přístup k vaší webové aplikace pouze pro klienty ze seznamu známé IP adresy nebo rozsahy IP adres, musíte vytvořit dvě IP odpovídající pravidla. První vyhovující pravidlo IP obsahuje seznam IP adres jako odpovídající hodnoty a nastavte akci na možnost "Povolit". Pro druhou kolekci s nižší prioritou, je blokovat všechny ostatní IP adresy pomocí operátoru "Vše" a nastavte akci "Blok". Po použití omezení pravidlo IP všechny žádosti pocházející z adresy mimo tento seznam povolených obdrží odpověď 403 (zakázáno).  

> [!IMPORTANT]
> Funkce omezení WAF IP pro Azure branou je momentálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="configure-waf-policy-with-azure-cli"></a>Konfigurace WAF zásady pomocí Azure CLI

### <a name="prerequisites"></a>Požadavky
Než začnete konfigurovat zásady omezení IP, nastavení prostředí rozhraní příkazového řádku a vytvořte profil branou.

#### <a name="set-up-azure-cli-environment"></a>Nastavení prostředí Azure CLI
1. Nainstalujte [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli), nebo použít Azure Cloud Shell. Služba Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z portálu Azure Portal. Má Azure CLI předem nainstalovaná a nakonfigurovaná pro použití s vaším účtem. Vyberte **vyzkoušet** tlačítko v rozhraní příkazového řádku příkazy, které následují. Výběr **vyzkoušet** Cloud Shell, která dokáže přihlásit ke svému účtu Azure se vyvolá. Jakmile se spustí relaci cloud shell zadejte `az extension add --name front-door` přidat přední dveře rozšíření.
 2. Pokud používáte rozhraní příkazového řádku místně v prostředí Bash, přihlaste se k Azure s využitím `az login`.

#### <a name="create-front-door-profile"></a>Vytvoření profilu branou
Vytvoření profilu branou podle pokynů v tématu [rychlý start: Vytvoření profilu branou](quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Vytvořit zásadu WAF

Vytvořit zásadu WAF se [az waf – zásady sítě vytvořit](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create) příkazu. V následujícím příkladu nahraďte název zásady *IPAllowPolicyExampleCLI* s jedinečný název zásady.

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-custom-ip-access-control-rule"></a>Přidat vlastní pravidlo řízení přístupu IP

Přidat vlastní pravidlo řízení přístupu IP WAF zásady vytvořili v předchozím kroku s [vytvořit vlastní pravidlo az sítě waf zásady](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create) příkazu. 

V následujícím příkladu:
-  Nahraďte *IPAllowPolicyExampleCLI* s jedinečné zásady vytvořili dříve.
-  Nahraďte *ip adresa rozsahu 1*, *ip adresa rozsahu 2* s vlastní rozsah.

Nejprve vytvořte IP povolit pravidlo pro zadané adresy.

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch ["<ip-address-range-1>","<ip-address-range-2>"] \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
V dalším kroku vytvoření bloku všechny IP pravidla s nižší prioritou než předchozí IP pravidlo povolit. Nahradit *IPAllowPolicyExampleCLI* s jedinečné zásady vytvořili dříve.

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

### <a name="find-waf-policy-id"></a>Najít ID zásad WAF
Najít ID zásad WAF s [az sítě waf-policy show](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show) příkazu. Nahradit *IPAllowPolicyExampleCLI* s jedinečné zásady vytvořili dříve.

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>Odkaz WAF zásad na hostitele front-endu branou

Nastavte branou *WebApplicationFirewallPolicyLink* ID pro ID zásad s [az sítě branou – aktualizace](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) příkazu. Nahradit *IPAllowPolicyExampleCLI* s jedinečné zásady vytvořili dříve.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
V tomto příkladu platí zásady WAF pro FrontendEndpoints [0]. Zásady WAF může propojit k některé z vašich front endů.
> [!Note]
> Je potřeba nastavit **WebApplicationFirewallPolicyLink** vlastnost jednou propojit WAF zásady s branou front-endu. Další zásady aktualizace automaticky nainstalují do front-endu.

## <a name="configure-waf-policy-with-azure-powershell"></a>Konfigurace WAF zásady pomocí Azure Powershellu

### <a name="prerequisites"></a>Požadavky
Než začnete konfigurovat zásady omezení IP, nastavení prostředí PowerShell a vytvořte profil branou.

#### <a name="set-up-your-powershell-environment"></a>Nastavení prostředí PowerShell
Prostředí Azure PowerShell poskytuje sadu rutin, které ke správě vašich prostředků Azure využívají model [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) můžete nainstalovat na místní počítač a používat v jakékoli relaci PowerShellu. Na stránce, přihlaste se pomocí přihlašovacích údajů Azure, postupujte podle pokynů a nainstalujte modul prostředí PowerShell Az.

##### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Připojení k Azure pomocí interaktivního dialogové okno pro přihlášení
```
Connect-AzAccount

```
Než nainstalujete modul branou, ujistěte se, že máte aktuální verzi modul PowerShellGet nainstalovaný. Spusťte následující příkaz a pak znova otevřete prostředí PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

##### <a name="install-azfrontdoor-module"></a>Instalace modulu Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Vytvoření profilu branou
Vytvoření profilu branou podle pokynů v tématu [rychlý start: Vytvoření profilu branou](quickstart-create-front-door.md)

### <a name="define-ip-match-condition"></a>Definujte podmínku shody IP
Použití [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) příkaz, který definuje podmínku shody IP. V následujícím příkladu nahraďte *ip adresa rozsahu 1*, *ip adresa rozsahu 2* s vlastní rozsah.

```powershell
  $IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
    -MatchVariable  RemoteAddr `
    -OperatorProperty IPMatch `
    -MatchValue ["ip-address-range-1", "ip-address-range-2"]
```
Vytvoření porovnání IP všechny podmínky pravidla
```powershell
  $IPMatchALlCondition = New-AzFrontDoorWafMatchConditionObject `
    -MatchVariable  RemoteAddr `
    -OperatorProperty Any
    
```

### <a name="create-a-custom-ip-allow-rule"></a>Vytvořit vlastní pravidlo povolit IP
   Použití [New-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) příkaz Definovat akce a nastavit prioritu. V následujícím příkladu bude možné požadavky od klienta IP adresy, které se shodují se seznamem. 

```powershell
  $IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
    -Name "IPAllowRule" `
    -RuleType MatchRule `
    -MatchCondition $IPMatchCondition `
    -Action Allow -Priority 1
```
S nižší prioritou než předchozí IP pravidlo povolit vytvoří blok všechny pravidlo protokolu IP.

```powershell
  $IPBlockAll = New-AzFrontDoorWafCustomRuleObject `
    -Name "IPDenyAll" `
    -RuleType MatchRule `
    -MatchCondition $IPMatchALlCondition `
    -Action Block `
    -Priority 2
   ```

### <a name="configure-waf-policy"></a>Konfigurace zásad WAF
Najít název skupiny prostředků, která obsahuje pomocí profilu branou `Get-AzResourceGroup`. V dalším kroku nakonfigurujte zásady WAF s pravidlem bloku IP pomocí [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>Odkaz WAF zásad na hostitele front-endu branou

Objekt zásad WAF propojit existujícího hostitele front-endu branou a aktualizovat vlastnosti branou. Nejdřív načtěte pomocí objektu branou [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Dále nastavte front-endu *WebApplicationFirewallPolicyLink* vlastnost ID prostředku z *$IPAllowPolicyExamplePS* v předchozím kroku se vytvoří [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) příkazu.

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> V tomto příkladu platí zásady WAF pro FrontendEndpoints [0]. Zásady WAF může propojit k některé z vašich front endů. Je potřeba nastavit *WebApplicationFirewallPolicyLink* vlastnost jednou propojit WAF zásady s branou front-endu. Další zásady aktualizace automaticky nainstalují do front-endu.


## <a name="configure-waf-policy-with-resource-manager-template"></a>Konfigurace zásad WAF pomocí šablony Resource Manageru
Zobrazit šablonu, která vytvoří přední dveře a zásadu WAF s vlastní pravidla pro omezení IP [tady](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Další postup

- Zjistěte, jak [vytvořit profil branou](quickstart-create-front-door.md).
