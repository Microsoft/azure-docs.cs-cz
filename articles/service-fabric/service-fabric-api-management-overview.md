---
title: Azure Service Fabric s přehledem správy rozhraní API
description: Tento článek je úvod k použití Azure API Management jako brány do aplikací Service Fabric.
author: vturecek
ms.topic: conceptual
ms.date: 06/22/2017
ms.author: vturecek
ms.openlocfilehash: 2a331715d4e4538cfdda8d958ff549a81b627b79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028547"
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Service Fabric se službou Azure API Management – Přehled

Cloudové aplikace obvykle potřebují front-end bránu, která poskytuje jediný bod příjmu příchozího přenosu od uživatelů, zařízení nebo dalších aplikací. V Service Fabric, brána může být bezstavové služby, jako je [například aplikace ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)nebo jiné služby určené pro příchozí přenosy dat, jako jsou [centra událostí](https://docs.microsoft.com/azure/event-hubs/), [IoT Hub](https://docs.microsoft.com/azure/iot-hub/)nebo Azure API [Management](https://docs.microsoft.com/azure/api-management/).

Tento článek je úvod k použití Azure API Management jako brány do aplikací Service Fabric. Správa rozhraní API se integruje přímo s Service Fabric, což vám umožní publikovat rozhraní API s bohatou sadou směrovacích pravidel do vašich back-end service fabric služeb.

## <a name="availability"></a>Dostupnost

> [!IMPORTANT]
> Tato funkce je dostupná na úrovních **Premium** a **Developer** pro správu rozhraní API z důvodu požadované podpory virtuální sítě.

## <a name="architecture"></a>Architektura

Společná architektura Service Fabric používá jednostránkovou webovou aplikaci, která provádí volání HTTP pro back-endové služby, které zveřejňují http api. [Ukázková aplikace začínáme služby](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) ukazuje příklad této architektury.

V tomto scénáři bezstavové webové služby slouží jako brána do aplikace Service Fabric. Tento přístup vyžaduje, abyste napsali webovou službu, která může proxy požadavky HTTP pro back-endové služby, jak je znázorněno v následujícím diagramu:

![Service Fabric s přehledem topologie azure api managementu][sf-web-app-stateless-gateway]

Jak aplikace rostou ve složitosti, tak se brány, které musí představovat rozhraní API před nesčetné back-endové služby. Azure API Management je navržený pro zpracování složitých rozhraní API s pravidly směrování, řízením přístupu, omezením rychlosti, monitorováním, protokolováním událostí a ukládáním odpovědí do mezipaměti s minimální prací z vaší strany. Azure API Management podporuje zjišťování služby Service Fabric, rozlišení oddílu a výběr replik inteligentně směrovat požadavky přímo do back-endových služeb v Service Fabric, takže nemusíte psát vlastní bezstavovou bránu rozhraní API. 

V tomto scénáři webové uživatelské rozhraní se stále obsluhuje prostřednictvím webové služby, zatímco volání rozhraní HTTP API jsou spravovány a směrovány prostřednictvím Azure API Management, jak je znázorněno v následujícím diagramu:

![Service Fabric s přehledem topologie azure api managementu][sf-apim-web-app]

## <a name="application-scenarios"></a>Scénáře aplikací

Služby v Service Fabric může být bezstavové nebo stavové a mohou být rozděleny pomocí jednoho ze tří schémat: singleton, int-64 rozsah a pojmenované. Řešení koncového bodu služby vyžaduje identifikaci konkrétního oddílu konkrétní instance služby. Při řešení koncového bodu služby musí být zadán název `fabric:/myapp/myservice`instance služby (například ) i konkrétní oddíl služby, s výjimkou oddílu singleton.

Azure API Management lze použít s libovolnou kombinaci bezstavové služby, stavové služby a jakékoli schéma dělení.

## <a name="send-traffic-to-a-stateless-service"></a>Odeslání provozu službě bez stavové služby

V nejjednodušším případě je provoz předán do instance služby bez stavů. K dosažení tohoto cíle obsahuje operace správy rozhraní API zásady příchozí zpracování s back-end service fabric, který mapuje na konkrétní bezstavové instance služby v back-end service fabric. Požadavky odeslané do této služby jsou odesílány do náhodné instance služby.

**Příklad**

V následujícím scénáři aplikace Service Fabric obsahuje `fabric:/app/fooservice`bezstavovou službu s názvem , která zveřejňuje interní rozhraní HTTP API. Název instance služby je dobře známý a může být pevně zakódován přímo v zásadách příchozího zpracování správy rozhraní API. 

![Service Fabric s přehledem topologie azure api managementu][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Odeslání provozu stavové službě

Podobně jako scénář bezstavové služby, provoz může být předán a stavové instance služby. V tomto případě operace správy rozhraní API obsahuje zásady příchozízpracování s back-end service fabric, který mapuje požadavek na konkrétní oddíl konkrétní instance *stavové* služby. Oddíl mapovat každý požadavek na je vypočítán pomocí metody lambda pomocí některé vstup z příchozího požadavku HTTP, jako je například hodnota v cestě URL. Zásada může být nakonfigurována tak, aby odesílala požadavky pouze primární replikě nebo na náhodnou repliku pro operace čtení.

**Příklad**

V následujícím scénáři aplikace Service Fabric obsahuje rozdělenou `fabric:/app/userservice` stavovou službu s názvem, která zveřejňuje interní rozhraní HTTP API. Název instance služby je dobře známý a může být pevně zakódován přímo v zásadách příchozího zpracování správy rozhraní API.  

Služba je rozdělena pomocí schématu oddílů Int64 se dvěma `Int64.MinValue` oddíly a rozsahem klíčů, který se rozprostírá do `Int64.MaxValue`. Zásady back-end vypočítá klíč oddílu v rámci `id` tohoto rozsahu převedením hodnoty uvedené v cestě požadavku URL na 64bitové celé číslo, i když jakýkoli algoritmus lze použít zde k výpočtu klíče oddílu. 

![Service Fabric s přehledem topologie azure api managementu][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Odeslání provozu do více bezstavových služeb

V pokročilejších scénářích můžete definovat operaci správy rozhraní API, která mapuje požadavky na více než jednu instanci služby. V tomto případě každá operace obsahuje zásadu, která mapuje požadavky na konkrétní instanci služby na základě hodnot z příchozího požadavku HTTP, jako je například cesta URL nebo řetězec dotazu, a v případě stavových služeb oddíl v rámci instance služby.

K dosažení tohoto cíle operace správy rozhraní API obsahuje zásady příchozí zpracování s back-end service fabric, který mapuje na bezstavové instance služby v service fabric back-end na základě hodnot načtených z příchozího požadavku HTTP. Požadavky na službu jsou odesílány do náhodné instance služby.

**Příklad**

V tomto příkladu je vytvořena nová instance bezstavové služby pro každého uživatele aplikace s dynamicky generovaným názvem pomocí následujícího vzorce:

- `fabric:/app/users/<username>`

  Každá služba má jedinečný název, ale názvy nejsou známy předem, protože služby jsou vytvořeny v reakci na vstup uživatele nebo správce a proto nemohou být pevně zakódovány do zásad APIM nebo směrovacích pravidel. Místo toho je v definici zásad back-endu generován název služby, `name` na kterou má být odeslán požadavek, z hodnoty uvedené v cestě požadavku na adresu URL. Například:

  - Požadavek `/api/users/foo` na je směrován do instance služby`fabric:/app/users/foo`
  - Požadavek `/api/users/bar` na je směrován do instance služby`fabric:/app/users/bar`

![Service Fabric s přehledem topologie azure api managementu][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Odeslání provozu do více stavových služeb

Podobně jako v příkladu služby bez stavové, operace správy rozhraní API můžete mapovat požadavky na více než jednu instance **stavové** služby, v takovém případě budete také muset provést rozlišení oddílu pro každou instanci stavové služby.

K dosažení tohoto cíle obsahuje operace správy rozhraní API zásady příchozí zpracování s back-end service fabric, který se mapuje na stavové instance služby v service fabric back-end na základě hodnot načtených z příchozího požadavku HTTP. Kromě mapování požadavku na konkrétní instanci služby může být požadavek také mapován na konkrétní oddíl v rámci instance služby a volitelně na primární repliku nebo náhodnou sekundární repliku v rámci oddílu.

**Příklad**

V tomto příkladu je vytvořena nová instance stavové služby pro každého uživatele aplikace s dynamicky generovaným názvem pomocí následujícího vzorce:

- `fabric:/app/users/<username>`

  Každá služba má jedinečný název, ale názvy nejsou známy předem, protože služby jsou vytvořeny v reakci na vstup uživatele nebo správce a proto nemohou být pevně zakódovány do zásad APIM nebo směrovacích pravidel. Místo toho je v definici zásad back-endu z `name` hodnoty, která je poskytnuta, cesta požadavku url, vygenerován název služby, na kterou má být odeslán požadavek na požadavek. Například:

  - Požadavek `/api/users/foo` na je směrován do instance služby`fabric:/app/users/foo`
  - Požadavek `/api/users/bar` na je směrován do instance služby`fabric:/app/users/bar`

Každá instance služby je také rozdělena pomocí schématu oddílů Int64 `Int64.MinValue` se `Int64.MaxValue`dvěma oddíly a rozsahem klíčů, který se rozprostírá do . Zásady back-end vypočítá klíč oddílu v rámci `id` tohoto rozsahu převedením hodnoty uvedené v cestě požadavku URL na 64bitové celé číslo, i když jakýkoli algoritmus lze použít zde k výpočtu klíče oddílu. 

![Service Fabric s přehledem topologie azure api managementu][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>Další kroky

Podle [kurzu](service-fabric-tutorial-deploy-api-management.md) nastavte svůj první cluster Service Fabric se správou rozhraní API a požadavky na tok prostřednictvím správy rozhraní API do vašich služeb.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png
