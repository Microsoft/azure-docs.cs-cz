---
title: Azure Cloud Services Def. Schéma LoadBalancerProbe | Microsoft Docs
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
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: 6cd56c9b04fc4657cedf845e7f111005a8dee183
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="azure-cloud-services-definition-loadbalancerprobe-schema"></a>Definice schématu LoadBalancerProbe služby Azure Cloud Services
Sondu nástroje pro vyrovnávání zatížení je sondu zákazník definovaný stavu UDP koncových bodů a koncových bodů v instancí rolí. `LoadBalancerProbe` Není element samostatné; se zkombinuje s webovou roli nebo role pracovního procesu v definičním souboru služby. A `LoadBalancerProbe` mohou využívat více než jednu roli.

Výchozí přípona souboru definice služby je .csdef.

## <a name="the-function-of-a-load-balancer-probe"></a>Funkce sondu nástroje pro vyrovnávání zatížení
Nástroje pro vyrovnávání zatížení Azure zodpovídá za směrování příchozí provoz instance role. Nástroje pro vyrovnávání zatížení Určuje, které instance může přijímat přenosy podle pravidelně zjišťování každou instanci, aby bylo možné zjistit stav této instance. Nástroje pro vyrovnávání zatížení sondy všechny instance několikrát za minutu. Existují dvě různé možnosti pro poskytování stavu instance ke službě Vyrovnávání zatížení – výchozí kontroly nástroje pro vyrovnávání zatížení, nebo nástroj pro vyrovnávání zatížení vlastní probe, které je implementováno definováním LoadBalancerProbe v souboru .csdef.

Sondu nástroje pro vyrovnávání zatížení výchozí využívá Agent hosta ve virtuálním počítači, který přijímá a odpoví odpověď HTTP 200 OK jenom v případě, že instance je ve stavu připraveno (např. Pokud tato instance není zaneprázdněn recyklace, zastavení, stavy atd.). Pokud agenta hosta přestane reagovat s HTTP 200 OK, nástroje pro vyrovnávání zatížení Azure označí instance jako reagovat a zastaví odesílání provozu do této instance. Nástroje pro vyrovnávání zatížení Azure pokračuje na příkaz ping instance, a pokud agenta hosta odpoví HTTP 200, nástroje pro vyrovnávání zatížení Azure odesílá přenosy do této instance znovu. Pokud používáte webovou roli webu kódu obvykle běží v w3wp.exe který není monitorován pomocí prostředky infrastruktury Azure nebo agenta hosta, což znamená selhání v w3wp.exe (např. Odpovědi HTTP 500) není hlášena agenta hosta a zatížení vyrovnávání nebude vědět provést tuto instanci mimo otočení.

Sondu nástroje pro vyrovnávání zatížení vlastní přepíše výchozí kontroly agenta hosta a umožňuje vytvářet vlastní logiky k určení stavu role instance. Nástroje pro vyrovnávání zatížení pravidelně sondy váš koncový bod (každých 15 sekund, ve výchozím nastavení) a instance je považovat za v otočení případě, že odpovídá TCP ACK nebo HTTP 200 během časového limitu (výchozí hodnoty 31 sekund). To může být užitečné k implementaci vlastní logiky k odebrání instance otočení nástroje pro vyrovnávání zatížení, například vrácení stav bez 200, pokud instance je vyšší než 90 % využití procesoru. Pro webové role pomocí w3wp.exe, to také znamená získáte automatické monitorování vašeho webu, protože selhání ve vašem kódu webu vrátit stav bez 200 na sondu nástroje pro vyrovnávání zatížení. Pokud v souboru .csdef nedefinujete LoadBalancerProbe, je výchozí chování nástroje pro vyrovnávání zatížení (výše popsané) použít.

Pokud používáte sondu nástroje pro vyrovnávání zatížení vlastní, je třeba zajistit, že logika vezme v úvahu metodu RoleEnvironment.OnStop. Při použití výchozí kontroly nástroje pro vyrovnávání zatížení, instance se dostala mimo otočení před OnStop volané, ale můžou dál sondu nástroje pro vyrovnávání zatížení vlastní vrátit 200 OK během OnStop události. Pokud používáte OnStop událostí k vyčištění mezipaměti, zastavte službu nebo jinak provádění změn, které můžou ovlivnit chování služby za běhu, budete muset zajistit, že logika testu nástroje pro vyrovnávání zatížení vlastní odebere otočení instance.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Definice schématu základní služby pro sondu nástroje pro vyrovnávání zatížení
 Základní formát souboru definice služby obsahující sondu nástroje pro vyrovnávání zatížení je následující.

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Prvky schématu
`LoadBalancerProbes` Element souboru definice služby obsahuje následující prvky:

- [LoadBalancerProbes Element](#LoadBalancerProbes)
- [LoadBalancerProbe Element](#LoadBalancerProbe)

##  <a name="LoadBalancerProbes"></a> LoadBalancerProbes Element
`LoadBalancerProbes` Element popisuje kolekci sondy nástroje pro vyrovnávání zatížení. Tento element má nadřazený element [LoadBalancerProbe Element](#LoadBalancerProbe). 

##  <a name="LoadBalancerProbe"></a> LoadBalancerProbe Element
`LoadBalancerProbe` Element definuje test stavu pro model. Můžete definovat více sondy nástroje pro vyrovnávání zatížení. 

Následující tabulka popisuje atributy `LoadBalancerProbe` element:

|Atribut|Typ|Popis|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Povinná hodnota. Název sondu nástroje pro vyrovnávání zatížení. Název musí být jedinečný.|
| `protocol`          | `string` | Povinná hodnota. Určuje protokol, koncového bodu. Možné hodnoty jsou `http` nebo `tcp`. Pokud `tcp` je zadána, je vyžadována pro test úspěšný přijaté potvrzení. Pokud `http` je zadána, je vyžadována pro test úspěšný z zadaný identifikátor URI odpovědi 200 OK.|
| `path`              | `string` | Identifikátor URI použitý pro požaduje stav od virtuálního počítače. `path` je požadováno v případě `protocol` je nastaven na `http`. Jinak není povoleno.<br /><br /> Výchozí hodnota neexistuje.|
| `port`              | `integer` | Volitelné. Port pro komunikaci testu. Tato položka je nepovinná pro libovolný koncový bod, protože stejný port se pak použije pro kontrolu. Můžete nastavit jiný port pro jejich zjišťování, také. Možné hodnoty rozsahu od 1 do 65535 (včetně).<br /><br /> Výchozí hodnota je nastavena v koncovém bodě.|
| `intervalInSeconds` | `integer` | Volitelné. Interval v sekundách, jak často se koncový bod pro stav testu. Interval je obvykle mírně méně než půl období přidělené časový limit (v sekundách), odkud dva úplné sondy před přepnutím instance mimo otočení.<br /><br /> Výchozí hodnota je 15, minimální hodnota je 5.|
| `timeoutInSeconds`  | `integer` | Volitelné. Časový limit v sekundách, se použijí pro test, kde žádná odpověď způsobí ukončení dál provoz z doručován ke koncovému bodu. Tato hodnota umožňuje koncové body, které mají být provedeny mimo otočení vyšší nebo nižší než typické doby používané v Azure (které jsou výchozí hodnoty).<br /><br /> Výchozí hodnota je 31, 11 je minimální hodnota.|

## <a name="see-also"></a>Viz také
[Cloudové služby (klasické) definice schématu](schema-csdef-file.md)