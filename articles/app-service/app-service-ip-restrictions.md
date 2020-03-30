---
title: Omezení přístupu k službě Azure App Service
description: Zjistěte, jak zabezpečit aplikaci ve službě Azure App Service zadáním omezení přístupu.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a02c099871ce9748f4c5f604900a7c4d57bb96b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473519"
---
# <a name="azure-app-service-access-restrictions"></a>Omezení přístupu k službě Azure App Service #

Omezení přístupu umožňují definovat seznam povolených a povolaných položek seřazené s prioritou, který řídí přístup k síti k vaší aplikaci. Seznam může obsahovat IP adresy nebo podsítě virtuální sítě Azure. Pokud existuje jedna nebo více položek, je pak implicitní "odepřít vše", který existuje na konci seznamu.

Možnost omezení přístupu funguje se všemi pracovními zatíženími hostované službou App Service, včetně; webové aplikace, aplikace API, linuxové aplikace, aplikace pro kontejnery linuxu a funkce.

Když je žádost podána do vaší aplikace, adresa FROM se vyhodnotí podle pravidel IP adresy v seznamu omezení přístupu. Pokud je adresa FROM v podsíti, která je nakonfigurována s koncovými body služby na Microsoft.Web, bude zdrojová podsíť porovnána s pravidly virtuální sítě v seznamu omezení přístupu. Pokud není přístup k adrese povolen na základě pravidel v seznamu, služba odpoví stavovým kódem [HTTP 403.](https://en.wikipedia.org/wiki/HTTP_403)

Možnost omezení přístupu je implementována v rolích front-endslužby služby App Service, které jsou před pracovními hostiteli, kde je váš kód spuštěn. Omezení přístupu jsou proto efektivně sítě ACS.

Možnost omezit přístup k webové aplikaci z virtuální sítě Azure (VNet) se nazývá [koncové body služby][serviceendpoints]. Koncové body služby umožňují omezit přístup ke službě s více klienty z vybraných podsítí. Musí být povolena na straně sítě i na službě, se kterou je povolena. Nefunguje omezit provoz na aplikace, které jsou hostované v prostředí služby App Service. Pokud se unavujete v prostředí služby App Service, můžete řídit přístup k aplikaci pomocí pravidel IP adresy.

![tok omezení přístupu](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Přidání a úprava pravidel omezení přístupu na portálu ##

Chcete-li do aplikace přidat pravidlo omezení přístupu, otevřete pomocí nabídky**omezení přístupu** k **síti**>a klikněte na Konfigurovat **omezení přístupu.**

![Možnosti sítě služby App Service](media/app-service-ip-restrictions/access-restrictions.png)  

V uzdu omezení přístupu můžete zkontrolovat seznam pravidel omezení přístupu definovaných pro vaši aplikaci.

![omezení přístupu k seznamu](media/app-service-ip-restrictions/access-restrictions-browse.png)

V seznamu se zobrazí všechna aktuální omezení, která jsou ve vaší aplikaci. Pokud máte omezení virtuální sítě pro vaši aplikaci, tabulka se zobrazí, pokud jsou pro Microsoft.Web povoleny koncové body služby. Pokud vaše aplikace nedefinuje žádná definovaná omezení, bude přístupná odkudkoli.  

## <a name="adding-ip-address-rules"></a>Přidání pravidel IP adresy

Kliknutím na **tlačítko [+] Přidat pravidlo** můžete přidat nové pravidlo omezení přístupu. Jakmile přidáte pravidlo, bude účinné okamžitě. Pravidla jsou vynucena v pořadí podle priority počínaje nejnižším číslem a zvýšením. Existuje implicitní odepřít vše, co je v platnosti po přidání i jedno pravidlo.

Při vytváření pravidla je nutné vybrat povolit/odepřít a také typ pravidla. Jste také povinni zadat hodnotu priority a to, k čemu omezujete přístup.  Volitelně můžete k pravidlu přidat název a popis.  

![přidání pravidla omezení přístupu k protokolu IP](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

Chcete-li nastavit pravidlo založené na adrese IP, vyberte typ protokolu IPv4 nebo IPv6. Zápis IP adresy musí být zadán v zápisu CIDR pro adresy IPv4 i IPv6. Chcete-li zadat přesnou adresu, můžete použít něco jako 1.2.3.4/32, kde první čtyři oktety představují vaši IP adresu a /32 je maska. Zápis CIDR IPv4 pro všechny adresy je 0.0.0.0/0. Chcete-li se dozvědět více o zápisu CIDR, můžete číst [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

## <a name="service-endpoints"></a>Koncové body služby

Koncové body služby umožňují omezit přístup k vybraným podsítím virtuální sítě Azure. Chcete-li omezit přístup k určité podsíti, vytvořte pravidlo omezení s typem virtuální sítě. Můžete vybrat předplatné, virtuální síť a podsíť, se kterými chcete povolit nebo odepřít přístup. Pokud koncové body služby ještě nejsou povoleny s Microsoft.Web pro podsíť, kterou jste vybrali, bude automaticky povolena pro vás, pokud zaškrtnete políčko s žádostí o to, aby to neudělal. Situace, kdy byste ji chtěli povolit v aplikaci, ale ne v podsíti, do značné míry souvisí s tím, pokud máte oprávnění k povolení koncových bodů služby v podsíti nebo ne. Pokud potřebujete získat někoho jiného, kdo povolí koncové body služby v podsíti, můžete zaškrtnout políčko a nechat si aplikaci nakonfigurovat pro koncové body služby v očekávání, že bude povolena později v podsíti. 

![přidání pravidla omezení přístupu virtuální sítě](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Koncové body služby nelze použít k omezení přístupu k aplikacím, které běží v prostředí služby App Service. Když je vaše aplikace v prostředí služby App Service, můžete řídit přístup k aplikaci pomocí pravidel přístupu k IP. 

Pomocí koncových bodů služby můžete aplikaci nakonfigurovat pomocí aplikačních bran nebo jiných zařízení WAF. Můžete také nakonfigurovat vícevrstvé aplikace se zabezpečenými back-endy. Další podrobnosti o některých možnostech najdete v části Síťové funkce a Integrace [služby App Service](networking-features.md) a [Application Gateway s koncovými body služby](networking/app-gateway-with-service-endpoints.md).

## <a name="managing-access-restriction-rules"></a>Správa pravidel omezení přístupu

Kliknutím na libovolný řádek můžete upravit existující pravidlo omezení přístupu. Úpravy jsou účinné okamžitě, včetně změn v pořadí priorit.

![úprava pravidla omezení přístupu](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

Při úpravě pravidla nelze změnit typ mezi pravidlem adresy IP a pravidlem virtuální sítě. 

![úprava pravidla omezení přístupu](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Pokud chcete pravidlo odstranit, **klikněte** na ... na pravidlo a potom klikněte na **Odebrat**.

![odstranit pravidlo omezení přístupu](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>Blokování jedné adresy IP ##

Při přidávání prvního pravidla omezení IP služba přidá explicitní **pravidlo Odepřít vše** s prioritou 2147483647. V praxi bude explicitně **pravidlo Odepřít vše** naposledy spuštěno a zablokuje přístup k libovolné adrese IP, která není explicitně povolena pomocí pravidla **Povolit.**

Pro scénář, kde uživatelé chtějí explicitně blokovat jednu ADRESU IP nebo blok IP adresy, ale povolit přístup ke všemu ostatnímu, je nutné přidat explicitní pravidlo **Povolit vše.**

![blokovat jednu IP adresu](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>SCM stránky 

Kromě toho, že můžete řídit přístup k vaší aplikaci, můžete také omezit přístup k webu scm používanému vaší aplikací. Scm web je koncový bod pro nasazení webu a také konzole Kudu. Můžete samostatně přiřadit omezení přístupu k webu scm z aplikace nebo použít stejnou sadu pro aplikaci i web scm. Když zaškrtnete políčko, abyste měli stejná omezení jako vaše aplikace, je vše prázdné. Pokud odškrtnete políčko, použije se jakékoli nastavení, které jste měli dříve na webu scm. 

![omezení přístupu k seznamu](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Programová manipulace s pravidly omezení přístupu ##

[Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/access-restriction?view=azure-cli-latest) a [Azure PowerShell](https://docs.microsoft.com/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0) má podporu pro úpravy omezení přístupu. Příklad přidání omezení přístupu pomocí azure cli:

```azurecli-interactive
az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
```
Příklad přidání omezení přístupu pomocí Azure PowerShellu:

```azurepowershell-interactive
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
    -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
```

Hodnoty lze nastavit ručně pomocí operace [Azure REST API](https://docs.microsoft.com/rest/api/azure/) PUT v konfiguraci aplikace ve Správci prostředků nebo pomocí šablony Azure Resource Manager. Jako příklad můžete použít resources.azure.com a upravit blok ipSecurityRestrictions k přidání požadovaného JSON.

Umístění těchto informací ve Správci prostředků je:

management.azure.com/subscriptions/**ID předplatného**/resourceGroups/**skupiny prostředků**/providers/Microsoft.Web/sites/ název webové**aplikace**/config/web?api-version=2018-02-01

Syntaxe JSON pro předchozí příklad je:
```json
{
  "properties": {
    "ipSecurityRestrictions": [
      {
        "ipAddress": "122.133.144.0/24",
        "action": "Allow",
        "priority": 100,
        "name": "IP example rule"
      }
    ]
  }
}
```

## <a name="azure-function-app-access-restrictions"></a>Omezení přístupu k aplikacím Azure Function App

Omezení přístupu jsou k dispozici také pro aplikace funkcí se stejnými funkcemi jako plány služby App Service. Povolení omezení přístupu zakáže editor kódu portálu pro všechny nepovolené IP adresy.

## <a name="next-steps"></a>Další kroky
[Omezení přístupu pro aplikace Azure Function Apps](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)

[Integrace aplikační brány s koncovými body služby](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
