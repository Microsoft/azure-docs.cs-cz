---
title: Konfigurace pravidla WAF omezení IP adres pro Azure Front Door
description: Zjistěte, jak nakonfigurovat pravidlo brány firewall webových aplikací k omezení IP adres pro existující koncový bod Azure Front Door.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 03/26/2020
ms.author: tyao
ms.openlocfilehash: 077f127648688b25d45b433fa2bc94ee011b3f2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336071"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>Konfigurace pravidla omezení PROTOKOLU IP pomocí brány firewall webové aplikace pro azure front door

Tento článek ukazuje, jak nakonfigurovat pravidla omezení IP v bráně firewall webových aplikací (WAF) pro Azure Front Door pomocí portálu Azure, Azure CLI, Azure PowerShell nebo šablony Azure Resource Manager.

Pravidlo řízení přístupu založené na IP adresách je vlastní pravidlo WAF, které umožňuje řídit přístup k webovým aplikacím. Je to tím, že zadává seznam IP adres nebo rozsahy IP adres ve formátu CIDR (Classless Inter-Domain Routing).

Ve výchozím nastavení je vaše webová aplikace přístupná z Internetu. Pokud chcete omezit přístup klientů ze seznamu známých IP adres nebo rozsahů IP adres, můžete vytvořit pravidlo párování IP adres, které obsahuje seznam ADRES IP jako odpovídající hodnoty a nastaví operátor na "Ne" (negovat je true) a akce **blokovat**. Po použití pravidla omezení IP požadavky, které pocházejí z adres mimo tento seznam povolených obdrží 403 Zakázané odpovědi.

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>Konfigurace zásad WAF pomocí portálu Azure

### <a name="prerequisites"></a>Požadavky

Vytvořte profil Azure Front Door podle pokynů popsaných v [úvodním panelu: Vytvoření předních dveří pro vysoce dostupnou globální webovou aplikaci](../../frontdoor/quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>Vytvoření zásad waf

1. Na portálu Azure vyberte **Vytvořit prostředek**, do vyhledávacího pole zadejte **bránu firewall webové aplikace** a pak vyberte **Bránu firewall webové aplikace (WAF).**
2. Vyberte **Vytvořit**.
3. Na stránce **Zásadvytvořit waf** použijte k dokončení karty **Základy** následující hodnoty:
   
   |Nastavení  |Hodnota  |
   |---------|---------|
   |Politika pro     |Globální WAF (přední dveře)|
   |Předplatné     |Vyberte své předplatné.|
   |Skupina prostředků     |Vyberte skupinu prostředků, ve které jsou přední dveře.|
   |Název zásad     |Zadejte název zásady.|
   |Stav zásad     |Povoleno|

   Vybrat **další: Nastavení zásad**

1. Na kartě **Nastavení zásad** vyberte **možnost Prevence**. Pro **tělo odezvy bloku**, typ *Jste zablokováni!* takže uvidíte, že platí vaše vlastní pravidlo.
2. Vyberte **další: Spravovaná pravidla**.
3. Vyberte **další: Vlastní pravidla**.
4. Vyberte **Přidat vlastní pravidlo**.
5. Na stránce **Přidat vlastní pravidlo** vytvořte vlastní pravidlo pomocí následujících testovacích hodnot:

   |Nastavení  |Hodnota  |
   |---------|---------|
   |Název vlastního pravidla     |FdWafCustPravidlo|
   |Status     |Povoleno|
   |Typ pravidla     |Shoda|
   |Priorita    |100|
   |Typ shody     |IP adresa|
   |Shoda proměnná|RemoteAddr|
   |Operace|Neobsahuje|
   |IP adresa nebo rozsah|10.10.10.0/24|
   |Pak...|Odepřít provoz|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="Vlastní pravidlo":::

   Vyberte **Přidat**.
6. Vyberte **další: Přidružení**.
7. Vyberte **Přidat front-end hostitele**.
8. V **části Frontend host**vyberte front-end hostitele a vyberte **Přidat**.
9. Vyberte **Zkontrolovat a vytvořit**.
10. Po průchodu ověření zásad vyberte **Vytvořit**.

### <a name="test-your-waf-policy"></a>Otestujte své zásady WAF

1. Po dokončení nasazení zásad WAF přejděte na název hostitele front-endu front-endu.
2. Měla by se zobrazit vlastní bloková zpráva.

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="Test pravidla WAF":::

   > [!NOTE]
   > Privátní IP adresa byla záměrně použita ve vlastním pravidle zaručit pravidlo by aktivovat. Ve skutečném nasazení vytvořte pravidla *povolení* a *zamítnutí* pomocí ip adres pro konkrétní situaci.

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Konfigurace zásad WAF pomocí rozhraní příkazového příkazu Azure

### <a name="prerequisites"></a>Požadavky
Než začnete konfigurovat zásady omezení IP, nastavte prostředí rozhraní příkazového příkazu a vytvořte profil Azure Front Door.

#### <a name="set-up-the-azure-cli-environment"></a>Nastavení prostředí Azure CLI
1. Nainstalujte [azure cli](/cli/azure/install-azure-cli)nebo použijte Azure Cloud Shell. Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z webu Azure Portal. Má předinstalované rozhraní Azure CLI, které je nakonfigurované pro použití s vaším účtem. Vnásledujících příkazech příkazu příkazu příkazu příkazu příkazu příkazu vyberte tlačítko **Try it** a pak se přihlaste ke svému účtu Azure v relaci Cloud Shellu, která se otevře. Po zahájení relace `az extension add --name front-door` zadejte přidání rozšíření Azure Front Door.
 2. Pokud používáte příkazového příkazové konto místně v `az login`Bash, přihlaste se k Azure pomocí .

#### <a name="create-an-azure-front-door-profile"></a>Vytvoření profilu předních dveří Azure
Vytvořte profil Azure Front Door podle pokynů popsaných v [úvodním panelu: Vytvoření předních dveří pro vysoce dostupnou globální webovou aplikaci](../../frontdoor/quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>Vytvoření zásad waf

Vytvořte zásadu WAF pomocí příkazu [vytvořit zásadu az sítě front-door waf-policy.](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-create) V následujícím příkladu nahraďte název *zásady IPAllowPolicyExampleCLI* jedinečným názvem zásady.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Přidání vlastního pravidla řízení přístupu k IP adres

Pomocí příkazu vytvořit vlastní pravidlo zásad [y az sítě waf-policy](/cli/azure/ext/front-door/network/front-door/waf-policy/rule?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-rule-create) přidejte vlastní pravidlo řízení přístupu k IP adresám pro zásadu WAF, kterou jste právě vytvořili.

V následujících příkladech:
-  Nahraďte *IPAllowPolicyExampleCLI* s jedinečnou zásadou vytvořenou dříve.
-  Nahraďte *ip-address-range-1*, *ip-address-range-2* vlastním rozsahem.

Nejprve vytvořte pravidlo povolení ip adresy pro zásadu vytvořenou z předchozího kroku. 
> [!NOTE]
> **--defer** je vyžadováno, protože pravidlo musí mít podmínku shody, která má být přidána v dalším kroku.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Dále přidejte podmínku shody k pravidlu:

```azurecli
az network front-door waf-policy rule match-condition add \
--match-variable RemoteAddr \
--operator IPMatch \
--values "ip-address-range-1" "ip-address-range-2" \
--negate true \
--name IPAllowListRule \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>Nalezení ID zásad waf 
Najděte ID zásad WAF pomocí příkazu [az network front-door waf-policy show.](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show) Nahraďte *IPAllowPolicyExampleCLI* v následujícím příkladu s jedinečnou zásadou, kterou jste vytvořili dříve.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Propojení zásad WAF s hlavním hostitelem front-endu Azure Front Door

Nastavte ID webu Azure Front Door *WebApplicationFirewallPolicyLink* na ID zásady pomocí příkazu [aktualizace předních dveří sítě AZ.](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) Nahraďte *IPAllowPolicyExampleCLI* s jedinečnou zásadou, kterou jste vytvořili dříve.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
V tomto příkladu je zásada WAF použita pro **frontendendpoints[0]**. Zásady WAF můžete propojit s libovolným předním koncem.
> [!Note]
> Chcete-li propojit zásady WAF s front-endem předních dveří Azure, je třeba nastavit vlastnost **WebApplicationFirewallPolicyLink** pouze jednou. Následné aktualizace zásad jsou automaticky použity na front-end.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Konfigurace zásad WAF pomocí Azure PowerShellu

### <a name="prerequisites"></a>Požadavky
Než začnete konfigurovat zásady omezení IP, nastavte prostředí PowerShellu a vytvořte profil Azure Front Door.

#### <a name="set-up-your-powershell-environment"></a>Nastavení prostředí PowerShell
Azure PowerShell poskytuje sadu rutin, které používají model [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) pro správu prostředků Azure.

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) můžete nainstalovat na místní počítač a používat v jakékoli relaci PowerShellu. Podle pokynů na stránce se přihlaste k PowerShellu pomocí přihlašovacích údajů Azure a nainstalujte modul Az.

1. Připojte se k Azure pomocí následujícího příkazu a pak se přihlaste pomocí interaktivního dialogového okna.
    ```
    Connect-AzAccount
    ```
 2. Před instalací modulu Azure Front Door se ujistěte, že máte nainstalovanou aktuální verzi modulu PowerShellGet. Spusťte následující příkaz a znovu otevřete prostředí PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Nainstalujte modul Az.FrontDoor pomocí následujícího příkazu. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Vytvoření profilu předních dveří Azure
Vytvořte profil Azure Front Door podle pokynů popsaných v [úvodním panelu: Vytvoření předních dveří pro vysoce dostupnou globální webovou aplikaci](../../frontdoor/quickstart-create-front-door.md).

### <a name="define-an-ip-match-condition"></a>Definovat podmínku shody IP
Pomocí příkazu [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) definujte podmínku shody IP.
V následujícím příkladu nahraďte *ip-address-range-1*, *ip-address-range-2* vlastním rozsahem.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Vytvoření vlastního pravidla povolení ip adresy

Pomocí příkazu [New-AzFrontDoorWafCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) definujte akci a nastavte prioritu. V následujícím příkladu budou blokovány požadavky, které nejsou z klientských IP adresy, které odpovídají seznamu.

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>Konfigurace zásad WAF
Vyhledejte název skupiny prostředků, která obsahuje profil `Get-AzResourceGroup`Azure Front Door pomocí . Dále nakonfigurujte zásadu WAF s pravidlem IP pomocí [new-azfrontdoorwafpolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Propojení zásad WAF s hlavním hostitelem front-endu Azure Front Door

Propojte objekt zásad WAF s existujícím front-endovým hostitelem a aktualizujte vlastnosti Azure Front Door. Nejprve načtěte objekt Azure Front Door pomocí [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Dále nastavte vlastnost **WebApplicationFirewallPolicyLink** na ID prostředku *$IPAllowPolicyExamplePS*, vytvořené v předchozím kroku pomocí příkazu [Set-AzFrontDoor.](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> V tomto příkladu je zásada WAF použita pro **frontendendpoints[0]**. Zásady WAF můžete propojit s libovolným předním koncem. Chcete-li propojit zásady WAF s front-endem předních dveří Azure, je třeba nastavit vlastnost **WebApplicationFirewallPolicyLink** pouze jednou. Následné aktualizace zásad jsou automaticky použity na front-end.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Konfigurace zásad WAF pomocí šablony Správce prostředků
Chcete-li zobrazit šablonu, která vytváří zásady Azure Front Door a zásady WAF s vlastními pravidly omezení IP, přejděte na [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit profil Azure Front Door](../../frontdoor/quickstart-create-front-door.md).
