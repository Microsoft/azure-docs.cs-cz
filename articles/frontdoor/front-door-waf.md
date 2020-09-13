---
title: Rychlé škálování a ochrana webové aplikace s využitím front-bran Azure a firewallu webových aplikací Azure (WAF) | Microsoft Docs
description: Tento článek vám pomůže pochopit, jak používat Firewall webových aplikací ve službě Azure front-dveří.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/06/2020
ms.author: duau
ms.openlocfilehash: a0252004b01e64b195b372d72682f6b777012258
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2020
ms.locfileid: "89535427"
---
# <a name="quickly-scale-and-protect-a-web-application-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Rychlé škálování a ochrana webové aplikace s využitím front-bran Azure a firewallu webových aplikací Azure (WAF)

Mnohé webové aplikace nastaly rychlým nárůstem provozu v posledních týdnech souvisejících s COVID-19. Kromě toho tyto webové aplikace také docházejí do nárůstu škodlivého provozu, včetně útoků DOS (Denial of Service). Účinný způsob, jak tyto potřeby zvládnout, horizontální navýšení kapacity pro přepětí provozu a ochranu před útoky, je nastavit přední dveře Azure pomocí Azure WAF jako akceleraci, mezipaměť a vrstvu zabezpečení před webovou aplikací. V tomto článku najdete pokyny k rychlému získání těchto front Azure pomocí instalačního programu Azure WAF pro všechny webové aplikace běžící v systému nebo mimo Azure. 

K nastavení WAF v tomto kurzu budeme používat rozhraní příkazového řádku Azure, ale všechny tyto kroky jsou taky plně podporované v Azure Portal, Azure PowerShell, Azure ARM a Azure REST API. 

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

Pokyny v tomto blogu používají rozhraní příkazového řádku Azure (CLI). Podívejte se na tento průvodce, abyste [mohli začít s Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

*Tip: snadný & rychlý způsob, jak začít pracovat s Azure CLI, je [bash v Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)*

Zajistěte, aby do Azure CLI bylo přidané rozšíření front-dveří.

```azurecli-interactive 
az extension add --name front-door
```

Poznámka: Další podrobnosti níže uvedených příkazů najdete v referenčních informacích k rozhraní příkazového [řádku Azure CLI pro přední dveře](https://docs.microsoft.com/cli/azure/ext/front-door/?view=azure-cli-latest).

## <a name="step-1-create-an-azure-front-door-afd-resource"></a>Krok 1: vytvoření prostředku Azure front-dveří (AFD)


```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

**--back-endu-adresa**: back-end adresa je název plně kvalifikovaného názvu domény (FQDN) aplikace, kterou chcete chránit. Například myapplication.contoso.com

**--Accept-Protocols**: přijaté protokoly určují, jaké všechny protokoly má AFD podporovat pro vaši webovou aplikaci. Příkladem může být--Accepted-Protocols http https.

**--Name**: zadejte název pro prostředek AFD.

**--Resource-Group**: Skupina prostředků, ve které chcete tento prostředek AFD umístit.  Další informace o skupinách prostředků najdete v téma Správa skupin prostředků v Azure.

V odpovědi, kterou získáte od úspěšného provedení tohoto příkazu, vyhledejte klíč "název hostitele" a poznamenejte si jeho hodnotu, která se má použít v pozdějším kroku. Název hostitele je název DNS prostředku AFD, který jste vytvořili.

## <a name="step-2-create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Krok 2: vytvoření profilu Azure WAF pro použití s prostředky front-dveří Azure

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

--Name – zadejte název pro zásady Azure WAF

--Resource-Group skupina prostředků, do které chcete tento prostředek WAF umístit. 

Výše uvedený kód rozhraní příkazového řádku vytvoří zásadu WAF, která je povolená a je v režimu prevence. 

Poznámka: možná budete chtít vytvořit WAF v režimu detekce a sledovat, jak detekuje & protokolování škodlivých požadavků (a ne bez blokování) před tím, než se rozhodnete změnit režim ochrany.

V odpovědi, kterou získáte od úspěšného provedení tohoto příkazu, vyhledejte klíč "ID" a poznamenejte si jeho hodnotu, která se má použít v pozdějším kroku. Pole ID by mělo být ve formátu

/Subscriptions/**ID předplatného**/ResourceGroups/**název skupiny prostředků**/Providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**WAF název zásady**

## <a name="step-3-add-managed-rulesets-to-this-waf-policy"></a>Krok 3: Přidání spravovaného rulesetsu do této zásady WAF

V zásadách WAF můžete přidat spravované rulesetsy, které jsou sadou pravidel sestavených a spravovaných Microsoftem, a zajistit ochranu před celými třídami hrozeb. V tomto příkladu přidáváme dvě takové RuleSets (1) výchozí RuleSet, které chrání před běžnými webovými hrozbami a (2) robot Protection RuleSet, který chrání před škodlivým roboty

(1) přidat výchozí RuleSet

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
```

(2) Přidání správce robotů RuleSet

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
```

--Policy-Name název, který jste zadali pro svůj prostředek Azure WAF

--Resource-Group skupina prostředků, do které jste umístili tento prostředek WAF.

## <a name="step-4-associate-the-waf-policy-with-the-afd-resource"></a>Krok 4: přidružení zásady WAF k prostředku AFD

V tomto kroku přidružíme zásadu WAF, kterou jsme vytvořili s prostředkem AFD, který se nachází před webovou aplikací.

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

– Pojmenujte název, který jste zadali pro prostředek AFD.

--Resource-Group skupina prostředků, do které jste umístili prostředek front-dveří Azure.

--Nastavte tuto hodnotu, když aktualizujete atribut WebApplicationFirewallPolicyLink pro frontendEndpoint přidružený k vašemu prostředku AFD s nově vytvořenými zásadami WAF. ID zásady WAF se dá najít z odpovědi, kterou jste dostali z kroku #2 výše.

Poznámka: výše uvedený příklad je určen pro případ, kdy nepoužíváte vlastní doménu.

Pokud pro přístup k webovým aplikacím nepoužíváte žádné vlastní domény, můžete přeskočit krok #5. V takovém případě budete koncovým uživatelům poskytovat název hostitele, který jste získali v kroku #1 k přechodu do vaší webové aplikace.

## <a name="step-5-configure-custom-domain-for-your-web-application"></a>Krok 5: konfigurace vlastní domény pro webovou aplikaci

Zpočátku se vlastní název domény vaší webové aplikace (například www.contoso.com) odkazoval na místo, kde jste měli spuštěný před tím, než se zavedl AFD. Po této změně architektury přidávání AFD + WAF do popředí aplikace by teď položka DNS odpovídající této vlastní doméně měla odkazovat na tento prostředek AFD. To se dá udělat tak, že přemapujete tuto položku na serveru DNS na název hostitele AFD, který jste si poznamenali v kroku #1.

Konkrétní kroky pro aktualizaci záznamů DNS budou záviset na poskytovateli služby DNS, ale pokud používáte Azure DNS k hostování názvu DNS, můžete v dokumentaci použít postup [aktualizace záznamu DNS](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-cli) a nasměrovat na název hostitele AFD. 

Jedním z klíčových věcí k poznámení je, že pokud potřebujete, aby vaši uživatelé mohli přejít na web pomocí vrcholu zóny, například contoso.com, musíte použít Azure DNS a [typ záznamu aliasu](https://docs.microsoft.com/azure/dns/dns-alias) pro hostování názvu DNS. 

Kromě toho je také potřeba aktualizovat konfiguraci AFD, aby se do ní [přidala Tato vlastní doména](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain) , aby AFD toto mapování pochopilo.

Nakonec, pokud používáte vlastní doménu k dosažení vaší webové aplikace a chcete povolit protokol HTTPS, musíte mít [certifikáty pro vlastní nastavení domény v AFD](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain-https). 

## <a name="step-6-lock-down-your-web-application"></a>Krok 6: uzamknutí webové aplikace

Jedním z volitelných osvědčených postupů je zajistit, aby s webovou aplikací mohli komunikovat jenom AFD hrany. Tato akce zajistí, že nikdo nebude moci obejít ochranu AFD a přistupovat k aplikacím přímo. Toto uzamčení můžete provést tak, že přejdete do [části Nejčastější dotazy v AFD](https://docs.microsoft.com/azure/frontdoor/front-door-faq) a odkazujete na otázku týkající se uzamykání back-endu pro přístup pouze pomocí AFD.
