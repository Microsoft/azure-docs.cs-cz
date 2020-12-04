---
title: Přehled služby Azure Service Fabric s API Management
description: Tento článek představuje úvod k používání služby Azure API Management jako brány k vašim Service Fabricm aplikacím.
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: 32f47d62cc9dda7cc88421dbf616bf69ffe152fc
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575682"
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Service Fabric se službou Azure API Management – Přehled

Cloudové aplikace obvykle potřebují front-end bránu, která poskytuje jediný bod příjmu příchozího přenosu od uživatelů, zařízení nebo dalších aplikací. V Service Fabric může být bránou Bezstavová služba, jako je například [aplikace ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)nebo jiná služba určená pro příchozí přenos dat, jako je například [Event Hubs](../event-hubs/index.yml), [IoT Hub](../iot-hub/index.yml)nebo [Azure API Management](../api-management/index.yml).

Tento článek představuje úvod k používání služby Azure API Management jako brány k vašim Service Fabricm aplikacím. API Management se integruje přímo s Service Fabric, což vám umožní publikovat rozhraní API s bohatou sadou pravidel směrování pro vaše back-end Service Fabric služby.

## <a name="availability"></a>Dostupnost

> [!IMPORTANT]
> Tato funkce je dostupná na úrovních úrovně **Premium** a **Developer** API Management v důsledku podpory požadované virtuální sítě.

## <a name="architecture"></a>Architektura

Společná architektura Service Fabric používá jednostránkovou webovou aplikaci, která provádí volání HTTP do back-endové služby, které zveřejňují rozhraní API protokolu HTTP. Příklad této architektury zobrazuje [ukázková aplikace Service Fabric Začínáme](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) .

V tomto scénáři slouží Bezstavová webová služba jako brána do aplikace Service Fabric. Tento přístup vyžaduje, abyste napsali webovou službu, která může proxy požadavky HTTP na back-endové služby, jak je znázorněno na následujícím diagramu:

![Diagram, který ukazuje, jak Bezstavová webová služba slouží jako brána v aplikaci Service Fabric.][sf-web-app-stateless-gateway]

Vzhledem k tomu, že aplikace se výrazně mění, udělejte od služby back-end nesčetných brány, které musí představovat rozhraní API. Služba Azure API Management je navržená tak, aby zpracovávala složitá rozhraní API pomocí pravidel směrování, řízení přístupu, omezení četnosti, monitorování, protokolování událostí a ukládání odpovědí do mezipaměti s minimálními nároky na vaši část. Azure API Management podporuje zjišťování služby Service Fabric, rozlišení oddílů a výběr repliky k inteligentnímu směrování požadavků přímo do back-endové služby v Service Fabric, takže nemusíte psát vlastní bezstavovou bránu API. 

V tomto scénáři se webové uživatelské rozhraní pořád obsluhuje prostřednictvím webové služby, zatímco volání HTTP API se spravují a směrují prostřednictvím Azure API Management, jak je znázorněno na následujícím diagramu:

![Diagram znázorňující, jak se webové uživatelské rozhraní pořád obsluhuje prostřednictvím webové služby, zatímco volání HTTP API se spravují a směrují prostřednictvím Azure API Management.][sf-apim-web-app]

## <a name="application-scenarios"></a>Scénáře aplikací

Služby v Service Fabric můžou být bezstavové nebo stavové a můžou být rozdělené do oddílů pomocí jednoho ze tří schémat: singleton, int-64 rozsah a s názvem. Řešení koncového bodu služby vyžaduje určení konkrétního oddílu určité instance služby. Při vyhodnocování koncového bodu služby musí být zadána jak název instance služby (například), tak i `fabric:/myapp/myservice` konkrétní oddíl služby, s výjimkou případů, kdy je oddíl singleton.

Azure API Management lze použít s libovolnou kombinací bezstavových služeb, stavových služeb a všech schémat dělení.

## <a name="send-traffic-to-a-stateless-service"></a>Odesílání provozu do bezstavové služby

V nejjednodušším případě se přenos přepošle na bezstavovou instanci služby. Aby to bylo možné, operace API Management obsahuje zásady příchozího zpracování s back-end Service Fabric, které se mapují na určitou stavovou instanci služby v Service Fabric back-endu. Požadavky odeslané do této služby se odesílají do náhodné instance služby.

**Příklad**

V následujícím scénáři Service Fabric aplikace obsahuje bezstavovou službu s názvem `fabric:/app/fooservice` , která zveřejňuje interní rozhraní HTTP API. Název instance služby je dobře známý a může být pevně zakódovaný přímo v API Management zásady příchozího zpracování. 

![Diagram, který zobrazuje Service Fabric aplikace obsahuje bezstavovou službu, která zveřejňuje interní rozhraní HTTP API.][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Odeslání provozu do stavové služby

Podobně jako u bezstavových služeb se přenos dá přeslat na instanci stavové služby. V takovém případě API Management operace obsahuje zásadu příchozího zpracování s back-end Service Fabric, která mapuje požadavek na konkrétní oddíl určité instance *stavové* služby. Oddíl, na který má být namapován každý požadavek, je vypočítán prostřednictvím metody lambda pomocí nějakého vstupu z příchozího požadavku HTTP, jako je například hodnota v cestě URL. Zásady je možné nakonfigurovat tak, aby odesílaly požadavky jenom na primární repliku, nebo na náhodnou repliku pro operace čtení.

**Příklad**

V následujícím scénáři Service Fabric aplikace obsahuje dělenou stavovou službu s názvem `fabric:/app/userservice` , která zveřejňuje interní rozhraní HTTP API. Název instance služby je dobře známý a může být pevně zakódovaný přímo v API Management zásady příchozího zpracování.  

Služba je rozdělená pomocí schématu oddílu Int64 se dvěma oddíly a rozsahem klíče, který se rozpíná `Int64.MinValue` na `Int64.MaxValue` . Zásada back-end vypočítá klíč oddílu v tomto rozsahu převodem `id` hodnoty zadané v cestě požadavku URL na 64 celé číslo, i když zde můžete použít libovolný algoritmus, který vypočítá klíč oddílu. 

![Service Fabric s Azure API Management topologii – přehled][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Odesílání provozu do více bezstavových služeb

V pokročilejších scénářích můžete definovat operaci API Management, která mapuje požadavky na více než jednu instanci služby. V takovém případě každá operace obsahuje zásadu, která mapuje požadavky na konkrétní instanci služby na základě hodnot z příchozího požadavku HTTP, jako je například cesta URL nebo řetězec dotazu, a v případě stavových služeb oddíl v instanci služby.

Aby to bylo možné, operace API Management obsahuje zásady příchozího zpracování s back-end Service Fabric, který se mapuje na bezstavovou instanci služby v Service Fabric back-endu na základě hodnot načtených z příchozího požadavku HTTP. Požadavky na službu jsou odesílány do náhodné instance služby.

**Příklad**

V tomto příkladu je nová Bezstavová instance služby vytvořena pro každého uživatele aplikace s dynamicky generovaným názvem pomocí následujícího vzorce:

- `fabric:/app/users/<username>`

  Každá služba má jedinečný název, ale názvy nejsou známy, protože služby jsou vytvořeny v reakci na vstup uživatele nebo správce, a proto nemohou být pevně zakódovány do zásad APIM nebo pravidel směrování. Místo toho se název služby, pro kterou se má odeslat žádost, vygeneruje v definici zásady back-endu z `name` hodnoty zadané v cestě žádosti URL. Příklad:

  - Požadavek na `/api/users/foo` směrování do instance služby `fabric:/app/users/foo`
  - Požadavek na `/api/users/bar` směrování do instance služby `fabric:/app/users/bar`

![Diagram, který zobrazuje příklad, ve kterém je vytvořena nová Bezstavová instance služby pro každého uživatele aplikace s dynamicky generovaným názvem.][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Odesílání provozu do více stavových služeb

Podobně jako v případě nestavové služby může operace API Management mapovat žádosti na více než jednu instanci **stavové** služby. v takovém případě možná budete muset pro každou stavovou instanci služby provést rozlišení oddílu.

Aby to bylo možné, operace API Management obsahuje zásady příchozího zpracování s back-end Service Fabric, který se mapuje na instanci stavové služby v Service Fabric back-endu na základě hodnot načtených z příchozího požadavku HTTP. Kromě mapování požadavku na konkrétní instanci služby je možné požadavek také namapovat na konkrétní oddíl v rámci instance služby a volitelně buď na primární repliku, nebo na náhodnou sekundární repliku v rámci oddílu.

**Příklad**

V tomto příkladu se vytvoří nová stavová instance pro každého uživatele aplikace s dynamicky generovaným názvem pomocí následujícího vzorce:

- `fabric:/app/users/<username>`

  Každá služba má jedinečný název, ale názvy nejsou známy, protože služby jsou vytvořeny v reakci na vstup uživatele nebo správce, a proto nemohou být pevně zakódovány do zásad APIM nebo pravidel směrování. Místo toho se název služby, pro kterou se má odeslat žádost, vygeneruje v definici zásady back-endu z `name` hodnoty zadané v cestě URL požadavku. Příklad:

  - Požadavek na `/api/users/foo` směrování do instance služby `fabric:/app/users/foo`
  - Požadavek na `/api/users/bar` směrování do instance služby `fabric:/app/users/bar`

Každá instance služby je také rozdělená pomocí schématu oddílu Int64 se dvěma oddíly a rozsahem klíče, který se rozpíná `Int64.MinValue` na `Int64.MaxValue` . Zásada back-end vypočítá klíč oddílu v tomto rozsahu převodem `id` hodnoty zadané v cestě požadavku URL na 64 celé číslo, i když zde můžete použít libovolný algoritmus, který vypočítá klíč oddílu. 

![Diagram znázorňující, že každá instance služby je také rozdělená pomocí schématu oddílu Int64 se dvěma oddíly a rozsahem klíče, který pokrývá Int64. MinValue do Int64. MaxValue.][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>Další kroky

Postupujte podle tohoto [kurzu](service-fabric-tutorial-deploy-api-management.md) a nastavte svůj první Service Fabric cluster s požadavky API Management a flow prostřednictvím API Management do vašich služeb.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png
