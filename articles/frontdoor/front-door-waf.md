---
title: 'Kurz: škálování a ochrana webové aplikace pomocí front-dveří Azure a firewallu webových aplikací Azure (WAF)'
description: V tomto kurzu se dozvíte, jak používat bránu firewall webových aplikací Azure se službou Azure front-dveří.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2020
ms.author: duau
ms.openlocfilehash: f894fd5ca7bb98c48b106c13b8fe6130b2c76c6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201052"
---
# <a name="tutorial-quickly-scale-and-protect-a-web-application-by-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Kurz: rychlé škálování a ochrana webové aplikace s využitím front-bran Azure a firewallu webových aplikací Azure (WAF)

Mnoho webových aplikací má v nedávné době rychlý nárůst provozu z důvodu COVID-19. U těchto webových aplikací dochází také ke zvýšení škodlivého provozu, včetně útoků DOS (Denial-of-Service). Existuje efektivní způsob, jak škálovat aplikaci pro přepětí provozu a chránit před útoky: nakonfigurujeme přední dveře Azure pomocí Azure WAF jako akceleraci, ukládání do mezipaměti a vrstvu zabezpečení před webovou aplikací. Tento článek poskytuje informace o tom, jak získat přední dvířka Azure s Azure WAF nakonfigurovanou pro libovolnou webovou aplikaci, která běží v nebo mimo Azure. 

K nakonfigurování WAF v tomto kurzu budeme používat rozhraní příkazového řádku Azure CLI. Stejnou věc můžete dosáhnout pomocí Azure Portal, Azure PowerShell, Azure Resource Manager nebo rozhraní Azure REST API. 

V tomto kurzu se naučíte:
> [!div class="checklist"]
> - Vytvořte si přední dveře.
> - Vytvořte zásady Azure WAF.
> - Nakonfigurujte sady pravidel pro zásady WAF.
> - Přidružte zásady WAF k předním dvířkům.
> - Nakonfigurujte vlastní doménu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

- Pokyny v tomto kurzu používají rozhraní příkazového řádku Azure CLI. [Podívejte se na tento průvodce](/cli/azure/get-started-with-azure-cli) , abyste mohli začít s Azure CLI.

  > [!TIP] 
  > Snadný a rychlý způsob, jak začít pracovat s Azure CLI, je [bash v Azure Cloud Shell](../cloud-shell/quickstart.md).

- Zajistěte, aby `front-door` bylo rozšíření přidané do Azure CLI:

   ```azurecli-interactive 
   az extension add --name front-door
   ```

> [!NOTE] 
> Další informace o příkazech použitých v tomto kurzu najdete v tématu [Reference k rozhraní příkazového řádku Azure CLI pro přední dveře](/cli/azure/ext/front-door).

## <a name="create-an-azure-front-door-resource"></a>Vytvoření prostředku front-dveří Azure

```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

`--backend-address`: Plně kvalifikovaný název domény (FQDN) aplikace, kterou chcete chránit. Například, `myapplication.contoso.com`.

`--accepted-protocols`: Určuje protokoly, které má přední vrátka Azure podporovat pro vaši webovou aplikaci. Například, `--accepted-protocols Http Https`.

`--name`: Název prostředku front-dveří Azure.

`--resource-group`: Skupina prostředků, do které chcete umístit tento prostředek front-dveří Azure. Další informace o skupinách prostředků najdete v tématu [Správa skupin prostředků v Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).

V odpovědi, která se zobrazí po spuštění tohoto příkazu, vyhledejte klíč `hostName` . Tuto hodnotu budete potřebovat v pozdějším kroku. `hostName`Je název DNS prostředku služby Azure front-dveří, který jste vytvořili.

## <a name="create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Vytvoření profilu Azure WAF pro použití s prostředky front-dveří Azure

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

`--name`: Název nové zásady Azure WAF.

`--resource-group`: Skupina prostředků, do které chcete tento prostředek WAF umístit. 

Předchozí kód rozhraní příkazového řádku vytvoří zásady WAF, které jsou povolené a které jsou v režimu prevence. 

> [!NOTE] 
> Je možné, že budete chtít vytvořit zásadu WAF v režimu detekce a sledovat, jak detekuje a protokolují škodlivé požadavky (bez blokování) před tím, než se rozhodnete použít režim ochrany.

V odpovědi, která se zobrazí po spuštění tohoto příkazu, vyhledejte klíč `ID` . Tuto hodnotu budete potřebovat v pozdějším kroku. 

`ID`Pole by mělo být v tomto formátu:

/Subscriptions/**ID předplatného**/ResourceGroups/**název skupiny prostředků**/Providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**WAF název zásady**

## <a name="add-managed-rule-sets-to-the-waf-policy"></a>Přidání spravovaných sad pravidel do zásad WAF

Spravované sady pravidel můžete přidat do zásad WAF. Spravovaná sada pravidel je sada pravidel sestavených a spravovaných Microsoftem, která pomáhá chránit před třídou hrozeb. V tomto příkladu přidáváme dvě sady pravidel:
- Výchozí sada pravidel, která pomáhá chránit před běžnými webovými hrozbami. 
- Sada pravidel ochrany robota, která pomáhá chránit před škodlivými roboty.

Přidat výchozí sadu pravidel:

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
   ```

Přidat sadu pravidel ochrany bot:

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
   ```

`--policy-name`: Název, který jste zadali pro svůj prostředek Azure WAF.

`--resource-group`: Skupina prostředků, do které jste umístili prostředek WAF.

## <a name="associate-the-waf-policy-with-the-azure-front-door-resource"></a>Přidružte zásady WAF k prostředku front-dveří Azure.

V tomto kroku přidružíme zásadu WAF, kterou jsme vytvořili u prostředku front-dveří Azure, který je před vaší webovou aplikací:

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

`--name`: Název, který jste zadali pro prostředek front-dveří Azure.

`--resource-group`: Skupina prostředků, do které jste umístili prostředek front-dveří Azure.

`--set`: Je místo, kde aktualizujete `WebApplicationFirewallPolicyLink` atribut `frontendEndpoint` přidruženého k prostředku front-dveří Azure pomocí nové zásady WAF. Měli byste mít ID zásady WAF z odpovědi, kterou jste dostali při vytváření profilu WAF dříve v tomto kurzu.

 > [!NOTE] 
> Předchozí příklad je použitelný, pokud nepoužíváte vlastní doménu. Pokud pro přístup k webovým aplikacím nepoužíváte žádné vlastní domény, můžete přejít k další části. V takovém případě poskytnete zákazníkům `hostName` získanou získanou, když jste vytvořili prostředek front-dveří Azure. Použijí tyto možnosti `hostName` k přechodu do vaší webové aplikace.

## <a name="configure-the-custom-domain-for-your-web-application"></a>Konfigurace vlastní domény pro webovou aplikaci

Vlastní název domény vaší webové aplikace je ten, který zákazníci používají k odkazování na vaši aplikaci. Například www.contoso.com. Zpočátku tento název domény odkazoval na umístění, ve kterém byl spuštěný, než zavedete přední dvířka Azure. Po přidání front-WAF a aplikací Azure do front aplikace by položka DNS, která odpovídá této vlastní doméně, odkazovala na prostředek front-dveří Azure. Tuto změnu můžete provést tak, že znovu namapujete položku na serveru DNS na přední dveře Azure `hostName` , které jste si poznamenali při vytváření prostředku front-dveří Azure.

Konkrétní kroky aktualizace záznamů DNS budou záviset na vašem poskytovateli služeb DNS. Pokud používáte Azure DNS k hostování názvu DNS, můžete si podle pokynů v dokumentaci [aktualizovat záznam DNS](../dns/dns-operations-recordsets-cli.md) a nasměrovat ho na přední dveře Azure `hostName` . 

Je důležité si uvědomit, že pokud potřebujete, aby se vaši zákazníci dostali na svůj web pomocí vrcholu zóny (například contoso.com). V takovém případě je nutné použít Azure DNS a jeho [typ záznamu alias](../dns/dns-alias.md) pro hostování názvu DNS. 

Také je potřeba aktualizovat konfiguraci front-dveří pro Azure, aby se do ní [přidala vlastní doména](./front-door-custom-domain.md) , aby bylo možné toto mapování znát.

Nakonec, pokud používáte vlastní doménu k dosažení vaší webové aplikace a chcete povolit protokol HTTPS. Musíte [nastavit certifikáty pro vlastní doménu v Azure front-dveří](./front-door-custom-domain-https.md). 

## <a name="lock-down-your-web-application"></a>Uzamčení webové aplikace

Doporučujeme, abyste měli jistotu, že s vaší webovou aplikací můžou komunikovat jenom okraje front-end pro Azure. Tím zajistíte, že nikdo nebude moci obejít ochranu před předními dvířky Azure a přistupovat k vaší aplikaci přímo. Pokud chcete dosáhnout této uzamčené funkce, přečtěte si téma [návody uzamčení přístupu k back-endu jenom na přední dveře Azure?](./front-door-faq.md#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky používané v tomto kurzu, odeberte skupinu prostředků, přední dveře a zásady WAF pomocí příkazu [AZ Group Delete](/cli/azure/group#az-group-delete) :

```azurecli-interactive
  az group delete \
    --name <>
```
`--name`: Název skupiny prostředků pro všechny prostředky použité v tomto kurzu.

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak řešit potíže s předními dveřmi, přečtěte si příručky k odstraňování potíží:

> [!div class="nextstepaction"]
> [Řešení běžných potíží se směrováním](front-door-troubleshoot-routing.md)