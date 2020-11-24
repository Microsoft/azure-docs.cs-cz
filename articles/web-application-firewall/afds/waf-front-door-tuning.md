---
title: Ladění firewallu webových aplikací (WAF) pro přední dveře Azure
description: V tomto článku se dozvíte, jak optimalizovat WAF pro přední dveře.
services: web-application-firewall
author: mohitkusecurity
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: mohitku
ms.reviewer: tyao
ms.openlocfilehash: a24f9e78de34b17977a1876cbefb473cc2610db0
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95549946"
---
# <a name="tuning-web-application-firewall-waf-for-azure-front-door"></a>Ladění firewallu webových aplikací (WAF) pro přední dveře Azure
 
Sada výchozích pravidel spravovaná pomocí Azure je založená na [sadě pravidel OWASP Core](https://github.com/SpiderLabs/owasp-modsecurity-crs/tree/v3.1/dev) a je navržená tak, aby byla pevná. Často se očekává, že pravidla WAF je třeba vyladit tak, aby vyhovovala konkrétním potřebám aplikace nebo organizace pomocí WAF. To se obvykle dosahuje definováním vyloučení pravidel, vytvářením vlastních pravidel a dokonce i zakázáním pravidel, která mohou způsobovat problémy nebo falešně pozitivních hodnot. Existuje několik věcí, které můžete provést v případě, že požadavky, které by měly projít Firewall webových aplikací (WAF), jsou blokované.

Nejdřív se ujistěte, že jste si přečetli [WAF přehled front-dveří](afds-overview.md) a [zásady WAF pro dokumenty front-dveří](waf-front-door-create-portal.md) . Také se ujistěte, že jste povolili [monitorování a protokolování WAF](waf-front-door-monitor.md). Tyto články vysvětlují, jak WAF funkce, jak pravidlo WAF nastaví práci a jak přistupovat k protokolům WAF.
 
## <a name="understanding-waf-logs"></a>Principy protokolů WAF
 
Účelem protokolu WAF je zobrazení všech požadavků, které jsou porovnány nebo blokovány rozhraním WAF. Jedná se o kolekci všech vyhodnocených požadavků, které jsou shodné nebo blokované. Pokud si všimnete, že WAF blokuje požadavek, který by neměl (falešně pozitivní), můžete provést několik věcí. Nejprve upřesněte a najděte konkrétní požadavek. V případě potřeby můžete [nakonfigurovat vlastní zprávu odpovědi](./waf-front-door-configure-custom-response-code.md) , která bude obsahovat `trackingReference` pole pro snadnou identifikaci události a provádění dotazu protokolu na konkrétní hodnotě. Prohlédněte si protokoly a vyhledejte konkrétní identifikátor URI, časové razítko nebo IP adresu klienta žádosti. Když najdete související položky protokolu, můžete začít působit na falešně pozitivních hodnot. 
 
Řekněme například, že máte legitimní provoz obsahující řetězec `1=1` , který chcete předat vaší WAF. Tato žádost vypadá nějak takto:

```
POST http://afdwafdemosite.azurefd.net/api/Feedbacks HTTP/1.1
Host: afdwafdemosite.azurefd.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 55

UserId=20&captchaId=7&captchaId=15&comment="1=1"&rating=3
```

Pokud si vyzkoušíte požadavek, WAF blokuje přenos, který obsahuje váš *1 = 1* řetězec v jakémkoli parametru nebo poli. Toto je řetězec často spojený s útokem na injektáže SQL. Můžete procházet protokoly a zobrazit časové razítko požadavku a pravidla, která jsou blokovaná/spárována.
 
V následujícím příkladu prozkoumáme `FrontdoorWebApplicationFirewallLog` protokol generovaný z důvodu shody pravidla.
 
V poli "requestUri" vidíte, že žádost byla provedena `/api/Feedbacks/` konkrétně. Dál najdete ID pravidla `942110` v poli "Rule". Pokud znáte ID pravidla, můžete přejít do [oficiálního úložiště OWASP sady pravidel ModSecurity](https://github.com/coreruleset/coreruleset) a vyhledat ho podle ID tohoto [pravidla](https://github.com/coreruleset/coreruleset/blob/v3.1/dev/rules/REQUEST-942-APPLICATION-ATTACK-SQLI.conf) a přesně pochopit, na kterém pravidle odpovídá. 
 
Poté, co zkontrolujete `action` pole, uvidíme, že toto pravidlo je nastavené na blokování požadavků na základě porovnání, a potvrzujeme, že žádost byla ve skutečnosti BLOKOVANÁ WAF, protože `policyMode` je nastavená na `prevention` . 
 
Teď si probereme informace v `details` poli. Tady vidíte `matchVariableName` `matchVariableValue` informace a. Zjistíme, že toto pravidlo se aktivovalo, protože v poli webové aplikace je vstup od někoho *1 = 1* `comment` .
 
```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```
 
K dispozici je také hodnota pro kontrolu protokolů přístupu za účelem rozšíření vašich znalostí o dané události WAF. Níže najdete `FrontdoorAccessLog` protokol, který se vygeneroval jako odpověď na výše uvedenou událost.
 
Tyto protokoly vidíte v závislosti na hodnotě, která je `trackingReference` shodná s hodnotou. Mezi různými poli, která poskytují obecný přehled, jako je například `userAgent` a `clientIP` , zavoláme `httpStatusCode` na `httpStatusDetails` pole a. Tady si můžete ověřit, že klient obdržel odpověď HTTP 403, která naprosto potvrdí, že tato žádost byla zamítnutá a zablokovaná. 
 
```json
{
    "time": "2020-09-24T16:43:04.5430764Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "httpMethod": "POST",
        "httpVersion": "1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "requestBytes": "2160",
        "responseBytes": "324",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.83 Safari/537.36",
        "clientIp": "1.1.1.1",
        "socketIp": "1.1.1.1",
        "clientPort": "53566",
        "timeToFirstByte": "0.01",
        "timeTaken": "0.011",
        "securityProtocol": "",
        "routingRuleName": "DemoBERoutingRule",
        "rulesEngineMatchNames": [],
        "backendHostname": "13.88.65.130:3000",
        "isReceivedFromClient": true,
        "httpStatusCode": "403",
        "httpStatusDetails": "403",
        "pop": "WST",
        "cacheStatus": "CONFIG_NOCACHE"
    }
}
```

## <a name="resolving-false-positives"></a>Řešení falešně pozitivních hodnot
 
Aby se zajistilo, že bude mít informovaná rozhodnutí o zpracování falešně pozitivních hodnot, je důležité se seznámit s technologiemi, které vaše aplikace používá. Řekněme například, že ve vašem technologickém zásobníku není SQL Server a že se s těmito pravidly dostanete falešně pozitivní výsledky. Zakázání těchto pravidel neznamená nutně oslabení zabezpečení. 

S těmito informacemi a znalostmi, které pravidlo 942110 odpovídá `1=1` řetězci v našem příkladu, můžeme udělat několik věcí, které zabrání zablokování této legitimní žádosti:
 
* Použití seznamů vyloučení
  * Další informace o seznamech vyloučení najdete v tématu [Firewall webových aplikací (WAF) se seznamy vyloučení služby front](waf-front-door-exclusion.md) -and. 
* Změna akcí WAF
  * Další informace o akcích, které je možné provést, když požadavek odpovídá podmínkám pravidla, najdete v tématu [WAF Actions](afds-overview.md#waf-actions) .
* Použít vlastní pravidla
  * Další informace o vlastních pravidlech najdete v tématu [vlastní pravidla pro Firewall webových aplikací pomocí služby Azure front-dveří](waf-front-door-custom-rules.md) .
* Zakázat pravidla 

> [!TIP]
> Když vybíráte přístup, který povoluje legitimní požadavky prostřednictvím WAF, zkuste to udělat tak, jak se dá dělat co nejpřesněji. Je například lepší použít seznam vyloučení, než pravidlo zcela zakážete.

### <a name="using-exclusion-lists"></a>Používání seznamů vyloučení

Jednou z výhod použití seznamu vyloučení je, že pro daný požadavek už nebude kontrolována pouze proměnná porovnávání, kterou jste vybrali k vyloučení. To znamená, že si můžete vybrat mezi konkrétními hlavičkami požadavků, soubory cookie požadavků, argumenty řetězce dotazu nebo argumenty post požadavku, které se mají vyloučit, pokud je splněna určitá podmínka, a to na rozdíl od prověření celé žádosti. V ostatních nespecifikovaných proměnných žádosti se bude pořád kontrolovat normálně.
 
Je důležité vzít v úvahu, že vyloučení jsou globální nastavení. To znamená, že nakonfigurované vyloučení se bude vztahovat na veškerý provoz procházející vaším WAF, ne jenom na konkrétní webovou aplikaci nebo identifikátor URI. Například to může být problém, pokud *1 = 1* je platný požadavek v těle určité webové aplikace, ale ne pro jiné pod stejnou zásadou WAF. Pokud má smysl použít pro různé aplikace různé seznamy vyloučení, zvažte použití různých zásad WAF pro každou aplikaci a jejich použití pro všechny front-endové aplikace.
 
Při konfiguraci seznamů vyloučení pro spravovaná pravidla se můžete rozhodnout vyloučit všechna pravidla v sadě pravidel, všechna pravidla v rámci skupiny pravidel nebo individuální pravidlo. Seznam vyloučení se dá nakonfigurovat pomocí [PowerShellu](https://docs.microsoft.com/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-4.7.0&viewFallbackFrom=azps-3.5.0), rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext_front_door_az_network_front_door_waf_policy_managed_rules_exclusion_add), [rozhraní REST API](https://docs.microsoft.com/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)nebo Azure Portal.

* Vyloučení na úrovni pravidla
  * Použití vyloučení na úrovni pravidla znamená, že zadaná vyloučení nebudou analyzována pouze proti tomuto konkrétnímu pravidlu, zatímco budou stále analyzována všemi ostatními pravidly v sadě pravidel. Toto je nejpřesnější úroveň pro vyloučení a dá se použít k vyladění spravované sady pravidel na základě informací, které najdete v protokolech WAF při řešení potíží s událostí.
* Vyloučení na úrovni skupiny pravidel
  * Použití vyloučení na úrovni skupiny pravidel znamená, že se určená vyloučení nebudou analyzovat proti této konkrétní sadě typů pravidel. Pokud například vyberete *SQLI* jako vyloučenou skupinu pravidel, znamená to, že vyloučení definovaného požadavku nebudou zkontrolována žádným z pravidel specifických pro SQLI, ale přesto by byla kontrolována pravidly v jiných skupinách, například *php*, *RFI* nebo *XSS*. Tento typ vyloučení může být užitečný v případě, že aplikace není náchylná k určitým typům útoků. Například aplikace, která nemá žádné databáze SQL, by mohla mít vyloučená všechna *SQLI* pravidla, aniž by došlo k neškodné úrovni zabezpečení.
* Vyloučení na úrovni sady pravidel 
  * Použití vyloučení na úrovni sady pravidel znamená, že určená vyloučení se nebudou analyzovat proti žádným pravidlům zabezpečení dostupným v dané sadě pravidel. Toto je komplexní vyloučení, takže by se mělo používat pečlivě.

V tomto příkladu budeme provádět vyloučení na nejpodrobnější úrovni (použití vyloučení na jedno pravidlo) a hledáme, že se má vylučovat **text požadavku POST** proměnné shody s názvem, který obsahuje `comment` . To je zřejmé, protože v protokolu brány firewall vidíte podrobnosti o porovnávání proměnných: `"matchVariableName": "PostParamValue:comment"` . Atribut je `comment` . Tento název atributu můžete také najít několika dalšími způsoby, viz [hledání názvů atributů žádosti](#finding-request-attribute-names).

![Pravidla vyloučení](../media/waf-front-door-tuning/exclusion-rules.png)

![Vyloučení pravidla pro konkrétní pravidlo](../media/waf-front-door-tuning/exclusion-rule.png)

V některých případech existují případy, kdy se konkrétní parametry předávají do WAF způsobem, který nemusí být intuitivní. Například je k dispozici token, který se předává při ověřování pomocí Azure Active Directory. Tento token, `__RequestVerificationToken` obvykle se předává jako soubor cookie žádosti. V některých případech, kdy jsou soubory cookie zakázány, je tento token také předán jako argument post žádosti. Z tohoto důvodu je potřeba zajistit, aby se v případě, že se má `__RequestVerificationToken` v seznamu vyloučení `RequestCookieNames` a `RequestBodyPostArgsNames` .

Vyloučení u názvu pole (*selektor*) znamená, že hodnota již nebude vyhodnocena WAF. Samotný název pole se ale nadále vyhodnocuje a ve výjimečných případech se může shodovat s pravidlem WAF a aktivovat akci.

![Vyloučení pravidla pro sadu pravidel](../media/waf-front-door-tuning/exclusion-rule-selector.png)

### <a name="changing-waf-actions"></a>Změna akcí WAF

Dalším způsobem, jak zpracovat chování pravidel WAF, je vybrat akci, kterou provede, když požadavek odpovídá podmínkám pravidla. K dispozici jsou tyto akce: [povolení, blokování, protokolování a přesměrování](afds-overview.md#waf-actions).

V tomto příkladu jsme změnili výchozí *blok* akce na akci *protokolu* na pravidle 942110. Tím dojde k tomu, že WAF protokoluje požadavek a pokračuje ve vyhodnocování stejného požadavku proti zbývajícím pravidlům s nižší prioritou.

![Akce WAF](../media/waf-front-door-tuning/actions.png)

Po provedení stejné žádosti můžeme přejít zpět k protokolům a my se vám zobrazí, že se jedná o shodu s ID pravidla 942110 a že `action_s` pole nyní indikuje *protokol* místo *bloku*. Pak jsme rozrozšířili dotaz protokolu tak, aby obsahoval `trackingReference_s` informace, a zjistili, co s touto žádostí něco stalo.

![Protokol zobrazující více shod pravidla](../media/waf-front-door-tuning/actions-log.png)

V důsledku toho se při zpracování pravidla 942110 v milisekundách zobrazuje odlišná shoda pravidel SQLI. Stejný požadavek se shoduje s ID pravidla 942310 a tentokrát se aktivoval výchozí *blok* akce.

Další výhodou použití akce *log* během vyladění WAF nebo řešení potíží je, že můžete zjistit, jestli je v rámci určité skupiny pravidel odpovídající a blokující daný požadavek. Pak můžete vytvořit vyloučení na příslušné úrovni, tj. na úrovni skupiny pravidla nebo pravidla. 

### <a name="using-custom-rules"></a>Použití vlastních pravidel

Jakmile zjistíte, co se shoduje s pravidlem WAF, můžete pomocí vlastních pravidel upravit způsob, jakým WAF reaguje na událost. Vlastní pravidla se zpracovávají před spravovanými pravidly, můžou obsahovat víc než jednu podmínku a jejich akce můžou být [povolené, zamítnout, protokolovat nebo přesměrovat](afds-overview.md#waf-actions). Když dojde ke shodě pravidla, modul WAF zastaví zpracování. To znamená, že se už nespouštějí další vlastní pravidla s nižší prioritou a spravovaná pravidla.

V následujícím příkladu jsme vytvořili vlastní pravidlo se dvěma podmínkami. První podmínka hledá `comment` hodnotu v textu požadavku. Druhá podmínka hledá `/api/Feedbacks/` hodnotu v identifikátoru URI požadavku.

Použití vlastního pravidla vám umožní být nejpřesnější při vyladění pravidel WAF a pro práci s falešně pozitivními výsledky. V takovém případě neprovádíme akci pouze v závislosti na `comment` hodnotě textu žádosti, která může existovat na více webech nebo aplikacích v rámci stejné zásady WAF. Zahrnutím další podmínky, která se taky shodují s konkrétním identifikátorem URI žádosti `/api/Feedbacks/` , zajistíme, že toto vlastní pravidlo bude skutečně platit pro tento explicitní případ použití, který jsme prověřenéi. Tím je zajištěno, že stejný útok, pokud je proveden proti různým podmínkám, by byl stále zkontrolován a zakázán modulem WAF.

![Protokol](../media/waf-front-door-tuning/custom-rule.png)

Při prohlížení protokolu vidíte, že `ruleName_s` pole obsahuje název zadaný pro vlastní pravidlo, které jsme vytvořili: `redirectcomment` . V `action_s` poli vidíte, že pro tuto událost byla provedena akce *přesměrování* . V tomto `details_matches_s` poli vidíte podrobnosti obou podmínek.

### <a name="disabling-rules"></a>Zakázání pravidel

Dalším způsobem, jak získat falešně pozitivní hodnotu, je zakázat pravidlo, které se shoduje se vstupem, na který WAF mysleli. Vzhledem k tomu, že jste analyzovali protokoly WAF a zúžené pravidlo na 942110, můžete ho zakázat v Azure Portal. Další informace najdete v tématu [přizpůsobení pravidel firewallu webových aplikací pomocí Azure Portal](../ag/application-gateway-customize-waf-rules-portal.md#disable-rule-groups-and-rules).
 
Zakázání pravidla je výhodou v případě, že jste si jisti, že všechny požadavky splňující danou konkrétní podmínku jsou ve skutečnosti legitimní požadavky, nebo když jste si jisti, že pravidlo neplatí jenom pro vaše prostředí (například zakázání pravidla pro vložení SQL, protože máte back-endy bez SQL). 
 
Zakázání pravidla ale představuje globální nastavení, které platí pro všechny hostitele front-endu přidružené k zásadám WAF. Pokud se rozhodnete zakázat pravidlo, může dojít k ohrožení zabezpečení vystavené bez ochrany nebo zjišťování pro žádné jiné servery front-end přidružené k zásadám WAF.
 
Pokud chcete zakázat spravované pravidlo pomocí Azure PowerShell, přečtěte si [`PSAzureManagedRuleOverride`](https://docs.microsoft.com/powershell/module/az.frontdoor/new-azfrontdoorwafmanagedruleoverrideobject?view=azps-4.7.0&preserve-view=true) dokumentaci k objektu. Pokud chcete použít rozhraní příkazového řádku Azure, přečtěte si [`az network front-door waf-policy managed-rules override`](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/override?view=azure-cli-latest&preserve-view=true) dokumentaci.

![Pravidla WAF](../media/waf-front-door-tuning/waf-rules.png)

## <a name="finding-request-fields"></a>Hledání polí žádosti

Pomocí proxy serveru, jako je [Fiddler](https://www.telerik.com/fiddler), můžete kontrolovat jednotlivé požadavky a určit, která konkrétní pole webové stránky se nazývají. To je užitečné v případě, že potřebujeme vyloučit určitá pole z kontroly pomocí seznamů vyloučení v WAF.

### <a name="finding-request-attribute-names"></a>Hledání názvů atributů žádosti
 
V tomto příkladu vidíte pole, ve kterém `1=1` byl řetězec zadán `comment` . Tato data byla předána v těle žádosti POST.

![Fiddler žádost o zobrazení textu](../media/waf-front-door-tuning/fiddler-request-attribute-name.png)

Toto pole můžete vyloučit. Další informace o seznamech vyloučení najdete v tématu [seznamy vyloučení brány firewall webových aplikací](./waf-front-door-exclusion.md). Vyhodnocování můžete v tomto případě vyloučit konfigurací následujícího vyloučení:

![Pravidlo vyloučení](../media/waf-front-door-tuning/fiddler-request-attribute-name-exclusion.png)

Můžete také prostudovat protokoly brány firewall a získat informace, abyste viděli, co je třeba přidat do seznamu vyloučení. Pokud chcete povolit protokolování, přečtěte si téma [monitorování metrik a protokolů v dopředných dveřích Azure](./waf-front-door-monitor.md).

Zkontrolujte protokol brány firewall v `PT1H.json` souboru po dobu, po kterou došlo k žádosti, kterou chcete zkontrolovat. `PT1H.json` soubory jsou k dispozici v kontejnerech účtu úložiště, kde `FrontDoorWebApplicationFirewallLog` `FrontDoorAccessLog` jsou uloženy protokoly a diagnostiky.

V tomto příkladu vidíte pravidlo, které zablokovalo požadavek (se stejným odkazem na transakci) a kdy došlo ve stejnou dobu:

```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```

Vaše znalosti o tom, jak pravidlo spravované v Azure stanoví práci (viz téma [Firewall webových aplikací na frontách Azure](afds-overview.md)), víte, že pravidlo s vlastností *blok* je blokováno na základě dat odpovídajících v textu žádosti. Můžete se podívat v podrobnostech, které se shodují se vzorem ( `1=1` ), a pole má název `comment` . Postupujte podle výše uvedených kroků, abyste vyloučili název příspěvku, který obsahuje text žádosti `comment` .

### <a name="finding-request-header-names"></a>Vyhledávají se názvy hlaviček požadavků.

Fiddler je užitečný nástroj znovu pro vyhledání názvů hlaviček požadavků. Na následujícím snímku obrazovky vidíte záhlaví této žádosti o získání, což zahrnuje typ obsahu, uživatelský agent atd. Pomocí hlaviček požadavků můžete také vytvořit vyloučení a vlastní pravidla v WAF.

![Žádost Fiddler zobrazující hlavičku](../media/waf-front-door-tuning/fiddler-request-header-name.png)

Další možností zobrazení hlaviček žádostí a odpovědí je prohledání v vývojářských nástrojích v prohlížeči, jako je například Edge nebo Chrome. Můžete stisknout klávesu F12 nebo kliknout pravým tlačítkem – > **zkontrolovat**  ->  **vývojářské nástroje** a vybrat kartu **síť** . načtěte webovou stránku a klikněte na žádost, kterou chcete zkontrolovat.

![Žádost o inspektora sítě](../media/waf-front-door-tuning/network-inspector-request.png)

### <a name="finding-request-cookie-names"></a>Hledání názvů souborů cookie žádosti

Pokud požadavek obsahuje soubory cookie, může být vybrána karta soubory cookie, aby se zobrazila v Fiddler. Informace o souborech cookie je také možné použít k vytvoření vyloučení nebo vlastních pravidel v WAF.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [bráně firewall webových aplikací Azure](../overview.md).
- Přečtěte si, jak [vytvořit Front Door](../../frontdoor/quickstart-create-front-door.md).
