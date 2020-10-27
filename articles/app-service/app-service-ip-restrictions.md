---
title: Omezení přístupu Azure App Service
description: Přečtěte si, jak zabezpečit aplikaci v Azure App Service zadáním omezení přístupu.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 6f1a94ae070419c38efb481e8f3967aec6a212d0
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533950"
---
# <a name="azure-app-service-access-restrictions"></a>Omezení přístupu Azure App Service

Omezení přístupu umožňují definovat prioritní seřazený seznam povolených a zakázaných aplikací, který řídí přístup k síti pro vaši aplikaci. Seznam může zahrnovat IP adresy nebo podsítě Azure Virtual Network. Pokud existuje jedna nebo více položek, je na konci seznamu implicitní implicitní "Odepřít vše".

Schopnost omezení přístupu funguje se všemi App Servicemi hostovanými pracovními zatíženími, včetně; webové aplikace, aplikace API, aplikace pro Linux, aplikace pro kontejner pro Linux a funkce.

Po podání žádosti do vaší aplikace se adresa z vyhodnotí na základě pravidel IP adres v seznamu omezení přístupu. Pokud je adresa v podsíti, která je nakonfigurovaná s koncovými body služby na Microsoft. Web, pak se zdrojová podsíť porovná s pravidly virtuální sítě v seznamu omezení přístupu. Pokud adresa nemá povolený přístup na základě pravidel v seznamu, služba odpoví pomocí kódu stavu [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) .

Funkce omezení přístupu je implementovaná v App Service rolích front-endu, které jsou v nadřazeném hostiteli hostitele, kde se váš kód spouští. Proto jsou omezení přístupu efektivně v síti seznamy ACL.

Možnost omezit přístup k vaší webové aplikaci z Azure Virtual Network (VNet) se nazývá [koncové body služby][serviceendpoints]. Koncové body služby umožňují omezit přístup ke službě pro více tenantů z vybraných podsítí. Musí být povolená jak na straně sítě, tak i na službě, se kterou se povoluje. Nefunguje tak, aby se omezil provoz na aplikace, které jsou hostované v App Service Environment. Pokud jste v App Service Environment, můžete řídit přístup k aplikaci pomocí pravidel IP adres.

![tok omezení přístupu](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Přidání a úprava pravidel omezení přístupu na portálu ##

Pokud chcete do aplikace přidat pravidlo omezení přístupu, otevřete pomocí nabídky **Network** > **omezení přístupu k** síti a klikněte na **konfigurovat omezení přístupu** .

![Možnosti App Service sítě](media/app-service-ip-restrictions/access-restrictions.png)  

V uživatelském rozhraní omezení přístupu si můžete prohlédnout seznam pravidel omezení přístupu definovaných pro vaši aplikaci.

![Snímek obrazovky s omezením přístupu v Azure Portal zobrazuje seznam pravidel omezení přístupu definovaných pro vybranou aplikaci.](media/app-service-ip-restrictions/access-restrictions-browse.png)

V seznamu se zobrazí všechna aktuální omezení, která jsou ve vaší aplikaci. Pokud máte v aplikaci omezení virtuální sítě, tabulka zobrazí, jestli jsou pro Microsoft. Web povolené koncové body služby. Pokud vaše aplikace nemá žádná definovaná omezení, bude vaše aplikace přístupná odkudkoli.  

## <a name="adding-ip-address-rules"></a>Přidávání pravidel IP adres

Kliknutím na **[+] přidat pravidlo** můžete přidat nové pravidlo omezení přístupu. Jakmile přidáte pravidlo, stane se hned platným. Pravidla se vynutila v pořadí podle priority počínaje nejnižším číslem a se zaměří. Je-li k dispozici implicitní pravidlo odepření, je nutné, když přidáte dokonce jedno pravidlo.

Při vytváření pravidla musíte vybrat možnost povolit/odepřít a také typ pravidla. Je také nutné zadat hodnotu priority a k čemu se omezuje přístup.  Volitelně můžete přidat název a popis pravidla.  

![Přidat pravidlo omezení přístupu k IP adrese](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

Pokud chcete nastavit pravidlo na základě IP adresy, vyberte typ protokolu IPv4 nebo IPv6. Zápis IP adres musí být zadán v zápisu CIDR pro adresy IPv4 i IPv6. Pokud chcete zadat konkrétní adresu, můžete použít něco jako 1.2.3.4/32, kde první čtyři oktety reprezentují vaši IP adresu a/32 je maska. Zápis IPv4 protokolu IPv4 pro všechny adresy je 0.0.0.0/0. Pokud se chcete dozvědět víc o zápisu CIDR, můžete číst [Inter-Domain směrování bez třídy](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

## <a name="service-endpoints"></a>Koncové body služby

Koncové body služby umožňují omezit přístup k vybraným podsítím virtuální sítě Azure. Chcete-li omezit přístup k určité podsíti, vytvořte pravidlo omezení s typem Virtual Network. Můžete vybrat předplatné, virtuální síť a podsíť, ke kterým chcete povolit nebo odepřít přístup. Pokud koncové body služby již nejsou povoleny s Microsoft. Web pro podsíť, kterou jste vybrali, bude automaticky povolena, pouze pokud jste zaškrtli políčko s výzvou k provedení. Situace, kdy byste ji chtěli povolit v aplikaci, ale ne v podsíti, je z velké části spojená s tím, že máte oprávnění k povolení koncových bodů služby v podsíti, nebo ne. Pokud potřebujete někomu jinému, aby se povolily koncové body služby v podsíti, můžete zaškrtnout políčko a nechat aplikaci nakonfigurovanou pro koncové body služby ve předvídání jejího povolení později v podsíti. 

![Přidat pravidlo omezení přístupu k virtuální síti](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Koncové body služby nelze použít k omezení přístupu k aplikacím, které běží v App Service Environment. Když je vaše aplikace v App Service Environment, můžete řídit přístup k aplikaci pomocí pravidel přístupu IP. 

Pomocí koncových bodů služby můžete nakonfigurovat aplikaci pomocí aplikačních bran nebo jiných zařízení WAF. Vícevrstvé aplikace můžete konfigurovat také pomocí zabezpečených back-endu. Další informace o některých možnostech najdete v článku [funkce sítě a App Service](networking-features.md) a [Application Gateway integraci s koncovými body služby](networking/app-gateway-with-service-endpoints.md).

> [!NOTE]
> - Koncové body služby se aktuálně nepodporují u webových aplikací, které používají IP SSL virtuální IP adresy (VIP).
> - Omezení počtu řádků IP adres nebo koncových bodů služby je 512. Pokud požadujete víc než 512 řádků omezení, doporučujeme, abyste provedli samostatné bezpečnostní produkty, jako jsou například přední vrátka Azure, Azure App Gateway nebo Firewall webových aplikací (WAF).
>

## <a name="managing-access-restriction-rules"></a>Správa pravidel omezení přístupu

Můžete kliknout na libovolný řádek a upravit existující pravidlo omezení přístupu. Úpravy jsou okamžitě platné, včetně změn v pořadí podle priority.

![Snímek obrazovky dialogového okna Upravit omezení IP adres v Azure Portal zobrazující pole pro existující pravidlo omezení přístupu](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

Když upravujete pravidlo, nemůžete změnit typ pravidla IP adresy a pravidla Virtual Network. 

![Snímek obrazovky dialogového okna Upravit omezení IP adres v Azure Portal zobrazující nastavení pravidla Virtual Network](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Pokud chcete pravidlo odstranit, klikněte na **...** v pravidle a pak klikněte na **Odebrat** .

![Odstranit pravidlo omezení přístupu](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>Blokování jedné IP adresy ##

Při přidání prvního pravidla omezení protokolu IP bude služba přidat explicitní pravidlo **Odepřít všechna** pravidla s prioritou 2147483647. V praxi se explicitně spustí pravidlo **Odepřít všechna** pravidla a zablokuje přístup k jakékoli IP adrese, která není explicitně povolená pomocí pravidla **Povolit** .

V případě scénáře, kdy uživatelé chtějí explicitně blokovat jednu IP adresu nebo blok IP adres, ale umožňují všem jiným přístupům, je nutné přidat explicitní pravidlo **Allow all** .

![blokovat jednu IP adresu](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>Web SCM 

Kromě toho, že je možné řídit přístup k vaší aplikaci, můžete také omezit přístup k webu SCM, který používá vaše aplikace. Web SCM je koncovým bodem nasazení webu a také konzolou Kudu. Můžete samostatně přiřadit omezení přístupu k webu SCM z aplikace nebo použít stejnou sadu pro aplikaci i web SCM. Když zaškrtnete políčko, aby měla stejná omezení jako vaše aplikace, vše je prázdné. Pokud zrušíte jeho zrušení, budou použita všechna nastavení, která jste předtím použili na webu SCM. 

![Snímek obrazovky s omezením přístupu v Azure Portal ukazuje, že pro web SCM nebo aplikaci nejsou nastavená žádná omezení přístupu.](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Programová manipulace s pravidly omezení přístupu ##

[Azure CLI](/cli/azure/webapp/config/access-restriction?view=azure-cli-latest) a [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0) podporují úpravy omezení přístupu. Příklad přidání omezení přístupu pomocí Azure CLI:

```azurecli-interactive
az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
```
Příklad přidání omezení přístupu pomocí Azure PowerShell:

```azurepowershell-interactive
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
    -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
```

Hodnoty lze také ručně nastavit pomocí operace Put služby [Azure REST API](/rest/api/azure/) Put v konfiguraci aplikace v Správce prostředků nebo pomocí šablony Azure Resource Manager. Jako příklad můžete použít resources.azure.com a upravit blok ipSecurityRestrictions pro přidání požadovaného formátu JSON.

Umístění pro tyto informace v Správce prostředků:

**ID předplatného** Management.Azure.com/Subscriptions//resourceGroups/ **skupiny prostředků** /Providers/Microsoft.Web/Sites/ **název webové aplikace** /config/Web? API-Version = 2018-02-01

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

## <a name="azure-functions-access-restrictions"></a>Omezení přístupu Azure Functions

Pro aplikace Function App jsou k dispozici také omezení přístupu se stejnými funkcemi jako App Service plány. Povolením omezení přístupu se zakáže Editor kódu portálu pro jakékoli nepovolené IP adresy.

## <a name="next-steps"></a>Další kroky
[Omezení přístupu pro Azure Functions](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)

[Integrace Application Gateway s koncovými body služby](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
