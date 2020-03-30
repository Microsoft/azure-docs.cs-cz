---
title: Azure Cloud Services Def. LoadBalancerProbe schéma | Dokumenty společnosti Microsoft
description: Zákazníkem definované LoadBalancerProbe je sonda stavu koncových bodů v instancích role. Kombinuje se s webovými nebo pracovními rolemi v souboru definice služby.
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 14
author: georgewallace
ms.author: tagore
ms.openlocfilehash: 6d0e84b6724d9df4162d4be3e06a9952087a53a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537342"
---
# <a name="azure-cloud-services-definition-loadbalancerprobe-schema"></a>Schéma načítání probúvazeva definice cloudových služeb Azure
Sonda pro vyrovnávání zatížení je zákazníkem definovaná sonda stavu koncových bodů UDP a koncových bodů v instancích rolí. Není `LoadBalancerProbe` samostatný prvek; je kombinován s webovou rolí nebo rolí pracovního procesu v souboru definice služby. A `LoadBalancerProbe` může být použit více než jednu roli.

Výchozí přípona pro soubor definice služby je .csdef.

## <a name="the-function-of-a-load-balancer-probe"></a>Funkce sondy pro vyrovnávání zatížení
Azure Balancer je zodpovědný za směrování příchozíprovoz do instancí rolí. Vyrovnávání zatížení určuje, které instance mohou přijímat provoz pravidelným zkoumáním každé instance za účelem určení stavu této instance. Vyrovnávání zatížení sonduje každou instanci vícekrát za minutu. Existují dvě různé možnosti pro poskytování stavu instance pro nástroje pro vyrovnávání zatížení – výchozí sonda nástroje pro vyrovnávání zatížení nebo vlastní sonda nástroje pro vyrovnávání zatížení, která je implementována definováním LoadBalancerProbe v souboru .csdef.

Výchozí sonda nástroje pro vyrovnávání zatížení využívá agenta hosta uvnitř virtuálního počítače, který naslouchá a reaguje s odpovědí HTTP 200 OK pouze v případě, že instance je ve stavu Připraveno (například když instance není v zaneprázdněn, Recyklace, Zastavení, atd. stavy). Pokud agent hosta neodpoví http 200 OK, nástroj pro vyrovnávání zatížení Azure označí instanci jako nereagující a přestane odesílat provoz do této instance. Nástroj pro vyrovnávání zatížení Azure pokračuje v příkazu ping instance a pokud agent hosta odpoví pomocí protokolu HTTP 200, nástroj pro vyrovnávání zatížení Azure znovu odešle provoz do této instance. Při použití webové role kód webu obvykle běží v w3wp.exe, který není sledován fabric Azure nebo host agent, což znamená selhání v w3wp.exe (např. Http 500 odpovědi) není hlášena agenthost a nástroj pro vyrovnávání zatížení neví, aby tuto instanci z rotace.

Vlastní sonda provyrovnávání zatížení přepíše výchozí sondu agenta hosta a umožňuje vytvořit vlastní logiku k určení stavu instance role. Nástroj pro vyrovnávání zatížení pravidelně sonduje koncový bod (ve výchozím nastavení každých 15 sekund) a instance je považována za rotační, pokud reaguje s TCP ACK nebo HTTP 200 v časovém limitu (výchozí 31 sekund). To může být užitečné pro implementaci vlastní logiky k odebrání instancí z rotace nástroje pro vyrovnávání zatížení, například vrácení stavu bez 200, pokud je instance vyšší než 90 % procesoru. Pro webové role pomocí w3wp.exe to také znamená, že získáte automatické sledování vašich webových stránek, protože selhání v kódu webu vrátí stav non-200 sondě nástroje pro vyrovnávání zatížení. Pokud nedefinujete LoadBalancerProbe v souboru .csdef, použije se výchozí chování pro vyrovnávání zatížení (jak bylo popsáno výše).

Pokud používáte vlastní sondu provyrovnávání zatížení, musíte zajistit, že vaše logika bere v úvahu metodu RoleEnvironment.OnStop. Při použití výchozí sonda provyrovnávání zatížení, instance je převzata z otočení před OnStop volána, ale vlastní sonda vyrovnávání zatížení může nadále vracet 200 OK během OnStop události. Pokud používáte událost OnStop k vyčištění mezipaměti, zastavení služby nebo jiného provádění změn, které mohou ovlivnit chování za běhu vaší služby, musíte zajistit, aby vaše vlastní logika sondy vyrovnávání zatížení odebere instanci z rotace.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Schéma definice základní služby pro sondu pro vyrovnávání zatížení
 Základní formát souboru definice služby obsahující sondu pro vyrovnávání zatížení je následující.

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Prvky schématu
Prvek `LoadBalancerProbes` souboru definice služby obsahuje následující prvky:

- [LoadBalancerProbes Element](#LoadBalancerProbes)
- [LoadBalancerProbe Element](#LoadBalancerProbe)

##  <a name="loadbalancerprobes-element"></a><a name="LoadBalancerProbes"></a>LoadBalancerProbes Element
Prvek `LoadBalancerProbes` popisuje kolekci sond y vyrovnávání zatížení. Tento prvek je nadřazeným prvkem [prvku LoadBalancerProbe](#LoadBalancerProbe). 

##  <a name="loadbalancerprobe-element"></a><a name="LoadBalancerProbe"></a>LoadBalancerProbe Element
Prvek `LoadBalancerProbe` definuje sondu stavu pro model. Můžete definovat více sond pro vyrovnávání zatížení. 

Následující tabulka popisuje atributy `LoadBalancerProbe` prvku:

|Atribut|Typ|Popis|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Povinná hodnota. Název sondy pro vyrovnávání zatížení. Název musí být jedinečný.|
| `protocol`          | `string` | Povinná hodnota. Určuje protokol koncového bodu. Možné hodnoty jsou `http` nebo `tcp`. Pokud `tcp` je zadán, přijaté potvrzení je vyžadováno pro sondu, která má být úspěšná. Pokud `http` je zadán, 200 OK odpověď ze zadaného identifikátoru URI je vyžadována pro sondu, aby byla úspěšná.|
| `path`              | `string` | Identifikátor URI používaný pro vyžádání stavu z virtuálního soudu. `path`je vyžadována, `protocol` `http`pokud je nastavena na . V opačném případě není povoleno.<br /><br /> Není k dispozici žádná výchozí hodnota.|
| `port`              | `integer` | Nepovinný parametr. Port pro komunikaci sondy. To je volitelné pro libovolný koncový bod, jako stejný port pak bude použit pro sondu. Můžete také nakonfigurovat jiný port pro jejich zjišťování. Možné hodnoty jsou v rozsahu od 1 do 65535 včetně.<br /><br /> Výchozí hodnota je nastavena koncovým bodem.|
| `intervalInSeconds` | `integer` | Nepovinný parametr. Interval v sekundách, jak často sonda koncový bod pro stav stavu. Obvykle interval je o něco menší než polovina přidělené časového období (v sekundách), který umožňuje dvě úplné sondy před přijetím instance z rotace.<br /><br /> Výchozí hodnota je 15, minimální hodnota je 5.|
| `timeoutInSeconds`  | `integer` | Nepovinný parametr. Časový čas v sekundách, aplikovaný na sondu, kde žádná odpověď bude mít za následek zastavení další provoz z dodaného do koncového bodu. Tato hodnota umožňuje koncové body, které mají být převzaty z rotace rychleji nebo pomaleji než typické časy používané v Azure (které jsou výchozí hodnoty).<br /><br /> Výchozí hodnota je 31, minimální hodnota je 11.|

## <a name="see-also"></a>Viz také
[Schéma definice cloudové služby (klasické)](schema-csdef-file.md)