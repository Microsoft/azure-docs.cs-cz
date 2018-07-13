---
title: Azure Cloud Services Def. Schéma LoadBalancerProbe | Dokumentace Microsoftu
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 113374a8-8072-4994-9d99-de391a91e6ea
caps.latest.revision: 14
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: f7b0ba3b4797149798037dee0188850eff6baf1d
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39003284"
---
# <a name="azure-cloud-services-definition-loadbalancerprobe-schema"></a>Schéma LoadBalancerProbe definice služby Azure Cloud Services
Test paměti nástroje pro vyrovnávání zatížení je sondu stavu definované odběratele koncové body protokolu UDP a koncových bodů v instancích rolí. `LoadBalancerProbe` Není samostatný prvek; se zkombinuje s webovou roli nebo roli pracovního procesu v definičním souboru služby. A `LoadBalancerProbe` mohou být využívána více než jednu roli.

Výchozí přípona pro definiční soubor služby je .csdef.

## <a name="the-function-of-a-load-balancer-probe"></a>Funkce kontroly nástroje pro vyrovnávání zatížení
Nástroj pro vyrovnávání zatížení Azure zodpovídá za směrování příchozího provozu vaší instancí rolí. Nástroje pro vyrovnávání zatížení Určuje, která instance může přijímat provoz pravidelně zjišťováním pokaždé, aby bylo možné určit stav této instance. Nástroje pro vyrovnávání zatížení sondy každá instance více než jednou za minutu. Existují dvě různé možnosti pro poskytování stavu instance nástroji pro vyrovnávání zatížení – výchozí kontroly nástroje pro vyrovnávání zatížení, nebo nástroj pro vyrovnávání zatížení vlastní sondy, které je implementováno definováním LoadBalancerProbe v souboru .csdef.

Výchozí kontroly nástroje pro vyrovnávání zatížení využívá Agent hosta ve virtuálním počítači, který naslouchá a jako odpověď vrátí odpověď HTTP 200 OK pouze v případě, instance je ve stavu připraveno (např. když instance není v zaneprázdněno, recyklace, zastavení, stavy atd.). Pokud Agent hosta přestane reagovat s HTTP 200 OK, nástroje pro vyrovnávání zatížení Azure označí instance jako reagovat a zastaví odesílání provozu do této instance. Azure Load Balancer i nadále odešlete zprávu ping na instanci a pokud Agent hosta odpoví HTTP 200, nástroje pro vyrovnávání zatížení Azure odesílá provoz do této instance znovu. Při použití webová role webu kódu obvykle běží v w3wp.exe který není monitorován pomocí prostředků infrastruktury Azure nebo agenta hosta, což znamená, že selhání v w3wp.exe (např.) Odpovědi HTTP 500) není hlášena na zatížení a agent hosta nástroje pro vyrovnávání neví, abyste mohli tuto instanci ze smyčky.

Test paměti nástroje pro vyrovnávání zatížení vlastní přepíše výchozí kontroly agenta hosta a umožňuje vytvářet vlastní logiky určit stav role instance. Nástroje pro vyrovnávání zatížení pravidelně sondy váš koncový bod (každých 15 sekund, ve výchozím nastavení) a instance je považovat za v oběhu Pokud odpoví TCP ACK nebo HTTP 200 v časovém limitu (výchozí hodnoty 31 sekund). To může být užitečné implementovat vlastní logiku k odebrání instance oběhu nástroje pro vyrovnávání zatížení, například vrací stav než 200, pokud instance je víc než 90 % využití procesoru. V případě webových rolí pomocí w3wp.exe to také znamená, že získáte automatické monitorování vašeho webu, protože chyby v kódu webu návratový stav než 200 pro test paměti nástroje pro vyrovnávání zatížení. Pokud nedefinujete v souboru .csdef LoadBalancerProbe, pak je výchozí chování nástroje pro vyrovnávání zatížení (výše popsané) použít.

Pokud používáte sondy nástroje pro vyrovnávání zatížení vlastní, musíte zajistit, že svoji logiku vezme v úvahu RoleEnvironment.OnStop metoda. Při použití výchozí kontroly nástroje pro vyrovnávání zatížení, instance je vyřazen ze smyčky před OnStop volána, ale můžete dál sondy nástroje pro vyrovnávání zatížení vlastní vrátit 200 OK během události OnStop. Pokud používáte OnStop událostí k vyčištění mezipaměti, zastavte službu nebo jinak provádění změn, které můžou ovlivnit chování za běhu služby, je potřeba zajistit, že svoji logiku sondy nástroje pro vyrovnávání zatížení vlastní odebere instanci snadné vyřadit z provozu.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Služby na úrovni Basic definice schématu pro test nástroje pro vyrovnávání zatížení
 Základní formát souboru definice služby obsahující test nástroje pro vyrovnávání zatížení je následujícím způsobem.

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Prvky schématu
`LoadBalancerProbes` Element definičního souboru služby obsahuje následující prvky:

- [LoadBalancerProbes – Element](#LoadBalancerProbes)
- [LoadBalancerProbe Element](#LoadBalancerProbe)

##  <a name="LoadBalancerProbes"></a> LoadBalancerProbes – Element
`LoadBalancerProbes` Element popisuje kolekci testům nástroje pro vyrovnávání zatížení. Tento element je nadřazeného elementu [LoadBalancerProbe Element](#LoadBalancerProbe). 

##  <a name="LoadBalancerProbe"></a> LoadBalancerProbe Element
`LoadBalancerProbe` Element definuje sondu stavu modelu. Můžete definovat více testům nástroje pro vyrovnávání zatížení. 

Následující tabulka popisuje atributy `LoadBalancerProbe` element:

|Atribut|Typ|Popis|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Povinná hodnota. Název sondy nástroje pro vyrovnávání zatížení. Název musí být jedinečný.|
| `protocol`          | `string` | Povinná hodnota. Určuje protokol koncového bodu. Možné hodnoty jsou `http` nebo `tcp`. Pokud `tcp` není zadána, přijatý ACK, je třeba test proběhne úspěšně. Pokud `http` je zadaná, odpověď 200 OK ze zadaného identifikátoru URI, je třeba test proběhne úspěšně.|
| `path`              | `string` | Identifikátor URI použitý pro posílání požadavků na stav z virtuálního počítače. `path` je vyžadována, pokud `protocol` je nastavena na `http`. V opačném případě není povoleno.<br /><br /> Neexistuje žádná výchozí hodnota.|
| `port`              | `integer` | Volitelné. Port pro komunikaci se test paměti. Tato položka je nepovinná pro libovolný koncový bod, protože stejný port pak bude sloužit pro test paměti. Můžete nastavit jiný port pro jejich zjišťování, také. Možné hodnoty rozsahu od 1 do 65535 (včetně).<br /><br /> Výchozí hodnota je nastavena pomocí koncového bodu.|
| `intervalInSeconds` | `integer` | Volitelné. Interval v sekundách, jak často probe koncový bod pro stav. Interval obvykle je o něco méně než půl přidělený časový limit (v sekundách) umožňující dvě úplné sondy před přepnutím instance ze smyčky.<br /><br /> Výchozí hodnota je 15, minimální hodnota je 5.|
| `timeoutInSeconds`  | `integer` | Volitelné. Časový limit v sekundách, použít pro test, pokud žádná odpověď způsobí zastavení další provoz z musí doručit do koncového bodu. Tato hodnota umožňuje koncové body, které mají být provedeny ze smyčky vyšší nebo nižší než typické doby použít v Azure (což je výchozí nastavení).<br /><br /> Výchozí hodnota je 31, 11 je minimální hodnota.|

## <a name="see-also"></a>Viz také
[Cloudové služby (klasické) schématu definice](schema-csdef-file.md)