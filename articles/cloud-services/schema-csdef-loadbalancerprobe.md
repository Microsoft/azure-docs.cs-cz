---
title: Schéma Azure Cloud Services def. LoadBalancerProbe | Microsoft Docs
description: LoadBalancerProbe definovaný zákazníkem je sonda stavu koncových bodů v instancích rolí. Kombinuje s webovými rolemi nebo rolemi pracovního procesu v definičním souboru služby.
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 14
author: georgewallace
ms.author: tagore
ms.openlocfilehash: 6d0e84b6724d9df4162d4be3e06a9952087a53a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "79537342"
---
# <a name="azure-cloud-services-definition-loadbalancerprobe-schema"></a>LoadBalancerProbe schéma definice Azure Cloud Services
Test nástroje pro vyrovnávání zatížení je sonda stavu definovaná zákazníkem koncových bodů a koncových bodů UDP v instancích rolí. Nejedná se `LoadBalancerProbe` o samostatný element; v kombinaci s webovou rolí nebo rolí pracovního procesu v definičním souboru služby. `LoadBalancerProbe`Může být použit více než jednou rolí.

Výchozí přípona souboru definice služby je. csdef.

## <a name="the-function-of-a-load-balancer-probe"></a>Funkce testu nástroje pro vyrovnávání zatížení
Azure Load Balancer zodpovídá za směrování příchozího provozu do instancí rolí. Nástroj pro vyrovnávání zatížení určuje, které instance můžou přijímat přenosy pravidelným zjišťováním jednotlivých instancí, aby bylo možné určit stav této instance. Nástroj pro vyrovnávání zatížení vyhledá každou instanci víckrát za minutu. Existují dvě různé možnosti pro poskytování stavu instance nástroji pro vyrovnávání zatížení – výchozí sondu pro vyrovnávání zatížení nebo vlastní test nástroje pro vyrovnávání zatížení, který je implementován definováním LoadBalancerProbe v souboru. csdef.

Výchozí sonda nástroje pro vyrovnávání zatížení využívá agenta hosta uvnitř virtuálního počítače, který naslouchá a reaguje s odpovědí HTTP 200 OK pouze v případě, že instance je ve stavu připraveno (například když instance není v zaneprázdněném stavu, recyklace, zastavení atd.). Pokud Agent hosta přestane reagovat pomocí protokolu HTTP 200 OK, Azure Load Balancer označí instanci jako nereagující a zastaví odesílání provozu do této instance. Azure Load Balancer nadále instanci příkazového testu a Agent hosta odpoví HTTP 200, Azure Load Balancer odesílá provoz do této instance znovu. Při použití webové role váš kód webu obvykle běží v w3wp.exe, kterou nesleduje agent Azure Fabric nebo host, což znamená selhání v w3wp.exe (např. K agentovi hosta není hlášena odpověď HTTP 500 a nástroj pro vyrovnávání zatížení neví, že tuto instanci převezme mimo rotaci.

Vlastní test nástroje pro vyrovnávání zatížení přepíše výchozí test hostovaného agenta a umožní vám vytvořit vlastní logiku, která určí stav instance role. Nástroj pro vyrovnávání zatížení pravidelně kontroluje váš koncový bod (ve výchozím nastavení každých 15 sekund) a instance se považuje za rotaci, pokud odpoví protokolem TCP ACK nebo HTTP 200 v rámci časového limitu (výchozí nastavení je 31 sekund). To může být užitečné při implementaci vlastní logiky k odebrání instancí z rotace nástroje pro vyrovnávání zatížení, například vrácení stavu, který není 200, pokud je instance vyšší než 90% CPU. U webových rolí využívajících w3wp.exe to také znamená, že získáte automatické monitorování webu, protože chyby ve vašem kódu webu vrátí do testu nástroje pro vyrovnávání zatížení stav, který není 200. Pokud v souboru. csdef nedefinujete LoadBalancerProbe, použije se výchozí chování nástroje pro vyrovnávání zatížení (jak je popsáno výše).

Pokud používáte vlastní test nástroje pro vyrovnávání zatížení, je nutné zajistit, aby vaše logika porovnala metodu RoleEnvironment.. stop. Při použití výchozího testu nástroje pro vyrovnávání zatížení se instance před zavoláním metody převezme mimo špičku, ale vlastní test nástroje pro vyrovnávání zatížení může v případě události při zastavení pokračovat v vracení 200 OK. Pokud k vyčištění mezipaměti, zastavení služby nebo jinak provádění změn, které mohou ovlivnit chování služby za běhu, používáte událost při zastavení, je nutné zajistit, aby vaše vlastní logika testu vyrovnávání zátěže odebrala instanci z rotace.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Základní schéma definice služby pro test nástroje pro vyrovnávání zatížení
 Základní formát definičního souboru služby obsahující test nástroje pro vyrovnávání zatížení je následující.

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Prvky schématu
`LoadBalancerProbes`Element definičního souboru služby zahrnuje následující prvky:

- [Element LoadBalancerProbes](#LoadBalancerProbes)
- [Element LoadBalancerProbe](#LoadBalancerProbe)

##  <a name="loadbalancerprobes-element"></a><a name="LoadBalancerProbes"></a> Element LoadBalancerProbes
`LoadBalancerProbes`Element popisuje kolekci sond nástroje pro vyrovnávání zatížení. Tento prvek je nadřazeným prvkem [elementu LoadBalancerProbe](#LoadBalancerProbe). 

##  <a name="loadbalancerprobe-element"></a><a name="LoadBalancerProbe"></a> Element LoadBalancerProbe
`LoadBalancerProbe`Prvek definuje sondu stavu pro model. Můžete definovat několik sond nástroje pro vyrovnávání zatížení. 

Následující tabulka popisuje atributy `LoadBalancerProbe` prvku:

|Atribut|Typ|Popis|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Povinná hodnota. Název testu nástroje pro vyrovnávání zatížení. Název musí být jedinečný.|
| `protocol`          | `string` | Povinná hodnota. Určuje protokol koncového bodu. Možné hodnoty jsou `http` nebo `tcp`. Je-li `tcp` parametr zadán, je pro úspěšné dokončení testu vyžadováno přijaté potvrzení. Je-li `http` zadán parametr, je k úspěšnému dokončení testu nutná odpověď 200 OK ze zadaného identifikátoru URI.|
| `path`              | `string` | Identifikátor URI, který se používá pro vyžádání stavu z virtuálního počítače. `path` je vyžadováno, pokud `protocol` je nastaven na `http` . V opačném případě není povolena.<br /><br /> Není k dispozici žádná výchozí hodnota.|
| `port`              | `integer` | Nepovinný parametr. Port pro komunikaci sondy. To je volitelné pro libovolný koncový bod, protože stejný port se pak použije pro test. Pro své zjišťování můžete také nakonfigurovat jiný port. Možné hodnoty jsou v rozsahu od 1 do 65535, včetně.<br /><br /> Výchozí hodnota je nastavená koncovým bodem.|
| `intervalInSeconds` | `integer` | Nepovinný parametr. Interval (v sekundách), jak často se má testovat koncový bod pro stav. Interval je typicky menší než polovina přiděleného časového limitu (v sekundách), který umožňuje dvě úplné sondy před převzetím instance mimo rotaci.<br /><br /> Výchozí hodnota je 15, minimální hodnota je 5.|
| `timeoutInSeconds`  | `integer` | Nepovinný parametr. Časový limit (v sekundách), který se použije na test, kdy žádná odpověď nevede k zastavení dalšího provozu v doručení do koncového bodu. Tato hodnota umožňuje, aby koncové body byly rychlejší nebo pomalejší než běžné časy používané v Azure (což jsou výchozí nastavení).<br /><br /> Výchozí hodnota je 31, minimální hodnota je 11.|

## <a name="see-also"></a>Viz také
[Schéma definice cloudové služby (Classic)](schema-csdef-file.md)