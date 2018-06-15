---
title: Azure Service Fabric se API Management přehled | Microsoft Docs
description: Tento článek je úvodem do jako brána pro vaše aplikace Service Fabric pomocí Azure API Management.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: vturecek
ms.openlocfilehash: 6bf7ea90bb5351411984110fd8fb05c2f8cb0650
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205157"
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Service Fabric s Azure API Management – přehled

Cloudové aplikace obvykle potřebují front-end bránu, která poskytuje jediný bod příjmu příchozího přenosu od uživatelů, zařízení nebo dalších aplikací. V Service Fabric bránu být jakékoli bezstavové služby, jako [aplikace ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), nebo jiné služby, které jsou určené pro příchozí provoz, například [Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [IoT Hub](https://docs.microsoft.com/azure/iot-hub/), nebo [Azure API Management](https://docs.microsoft.com/azure/api-management/).

Tento článek je úvodem do jako brána pro vaše aplikace Service Fabric pomocí Azure API Management. API Management se integruje přímo s Service Fabric, že vám umožní publikovat rozhraní API s bohatou sadu pravidel směrování k vašim službám Service Fabric back-end. 

## <a name="architecture"></a>Architektura
Běžné architektura Service Fabric používá jednostránkovou webovou aplikaci, která provádí volání HTTP na back endové služby, které zveřejňují rozhraní API HTTP. [Service Fabric Začínáme ukázkovou aplikaci](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) ukazuje příklad této architektury.

V tomto scénáři bezstavové webové služby slouží jako brány do aplikace Service Fabric. Tento postup vyžaduje, abyste zápisu webová služba, která může požadavky HTTP proxy server na back endové služby, jak je znázorněno v následujícím diagramu:

![Service Fabric s Azure API Management přehled topologie][sf-web-app-stateless-gateway]

Jelikož aplikace v složitost, takže to brány, které musí představovat rozhraní API před řadu back endové služby. Azure API Management je určený k řešení komplexní rozhraní API pomocí pravidel směrování, řízení přístupu, omezení rychlosti, sledování, protokolování událostí a ukládání odpovědí do mezipaměti s minimálním úsilím na vaší straně. Azure API Management podporuje zjišťování služby Service Fabric, oddílu řešení a výběr repliky inteligentně směrovat požadavky přímo na back endové služby v Service Fabric, takže není nutné zapsat bezstavové brány rozhraní API. 

V tomto scénáři webového uživatelského rozhraní je stále obsluhovat pomocí webové služby, při volání rozhraní API HTTP jsou spravovaná a směrován přes Azure API Management, jak je znázorněno v následujícím diagramu:

![Service Fabric s Azure API Management přehled topologie][sf-apim-web-app]

## <a name="application-scenarios"></a>Scénáře aplikací

Služby v Service Fabric může být bezstavové nebo stavová a jejich může být rozděleny do oddílů pomocí jednoho ze tří schémata: singleton, int-64 rozsahu a s názvem. Rozlišení koncového bodu služby vyžaduje identifikace na konkrétní oddíl instance konkrétní služby. Při rozpoznávání koncový bod služby, obě instance názvu služby (například `fabric:/myapp/myservice`) a také konkrétní oddíl služby se musí určit, s výjimkou oddílů singleton.

Azure API Management můžete použít s libovolnou kombinaci bezstavové služby, stavové služby a všechny schéma rozdělení oddílů.

## <a name="send-traffic-to-a-stateless-service"></a>Odesílá data na bezstavové služby

V nejjednodušším případě přenosy předávaly do instance bezstavové služby. K dosažení tohoto API Management operace obsahuje zásady zpracování příchozích s Service Fabric back-end, který se mapuje na instanci konkrétní bezstavové služby v Service Fabric back-end. Náhodné repliky instance bezstavové služby jsou odesílány požadavky odeslané na tuto službu.

#### <a name="example"></a>Příklad:
V následujícím scénáři aplikace Service Fabric obsahuje bezstavové služby s názvem `fabric:/app/fooservice`, která zpřístupňuje rozhraní API interní HTTP. Název instance služby je dobře známé a může být pevně přímo v zásadách zpracování příchozí API Management. 

![Service Fabric s Azure API Management přehled topologie][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Odesílá data na stavové služby

Podobně jako bezstavové služby scénář, provoz mohou být přenesena do instance stavové služby. V takovém případě API Management operace obsahuje zásady zpracování příchozích s Service Fabric back-end, který mapuje žádost na konkrétní oddíl konkrétní *stateful* instance služby. Oddíl mapovat každý požadavek pro výpočet je metodou lambda pomocí některé vstup z příchozího požadavku HTTP, jako je například hodnotu do cesty URL. Zásady lze nakonfigurovat na odesílání žádostí pouze primární replikou, nebo náhodné repliky pro operace čtení.

#### <a name="example"></a>Příklad:

V následujícím scénáři aplikace Service Fabric obsahuje oddílů stavové služby s názvem `fabric:/app/userservice` který zpřístupňuje rozhraní API interní HTTP. Název instance služby je dobře známé a může být pevně přímo v zásadách zpracování příchozí API Management.  

Služba je rozdělen na oddíly pomocí schéma oddílu Int64 dva oddíly a klíče rozsah, který zahrnuje `Int64.MinValue` k `Int64.MaxValue`. Zásady back-end vypočítá klíč oddílu v tomto rozsahu převedením `id` hodnota zadaná v žádosti o cestě adresy URL 64bitové celé číslo, i když libovolný algoritmus lze zde výpočetní klíč oddílu. 

![Service Fabric s Azure API Management přehled topologie][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Odesílá data na více bezstavové služby

V pokročilejších scénářích můžete definovat API Management operace, která mapuje požadavky na více než jedna instance služby. V takovém případě každé operace obsahuje zásady, která mapuje požadavky na instanci konkrétní služby založené na hodnotách z příchozí požadavek HTTP, jako třeba řetězce adresy URL cestu nebo dotaz a v případě stavové služby, oddílu v rámci instance služby. 

K dosažení tohoto API Management operace obsahuje zásady zpracování příchozích s Service Fabric back-end, který se mapuje na instanci bezstavové služby v Service Fabric na základě hodnot načíst z příchozího požadavku HTTP back-end. Požadavky na instanci služby jsou odesílány náhodných repliky instance služby.

#### <a name="example"></a>Příklad:

V tomto příkladu je vytvořena nová instance bezstavové služby pro každého uživatele aplikace s názvem dynamicky generovaném pomocí následujícího vzorce:
 
 - `fabric:/app/users/<username>`

 Každá služba má jedinečný název, ale nejsou známé názvy počáteční, protože služby jsou vytvořeny v reakci na uživatele nebo správce vstup a proto nemůže být pevně zakódované do APIM zásady nebo pravidla směrování. Místo toho název služby, do které chcete odeslat žádost o je vytvořen v definici zásady back-end z `name` hodnota zadaná pro cestu adresy URL požadavku. Příklad:

  - Požadavek na `/api/users/foo` se směruje na instanci služby `fabric:/app/users/foo`
  - Požadavek na `/api/users/bar` se směruje na instanci služby `fabric:/app/users/bar`

![Service Fabric s Azure API Management přehled topologie][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Odesílá data na více stavové služby

Podobně jako v příkladu bezstavové služby API Management operace můžete mapovat požadavky na více než jeden **stateful** instance služby, ve které je případ také můžete využít řešení oddílu pro každou instanci stavové služby.

K dosažení tohoto API Management operace obsahuje zásady zpracování příchozích s Service Fabric back-end, který se mapuje na instanci stavové služby v Service Fabric na základě hodnot načíst z příchozího požadavku HTTP back-end. Kromě mapování žádost o instanci určité služby, požadavek lze také mapovat na konkrétní oddíl v rámci instance služby a volitelně primární repliku nebo náhodné sekundární repliky v oddílu.

#### <a name="example"></a>Příklad:

V tomto příkladu je vytvořena nová instance stavové služby pro každého uživatele aplikace s názvem dynamicky generovaném pomocí následujícího vzorce:
 
 - `fabric:/app/users/<username>`

 Každá služba má jedinečný název, ale nejsou známé názvy počáteční, protože služby jsou vytvořeny v reakci na uživatele nebo správce vstup a proto nemůže být pevně zakódované do APIM zásady nebo pravidla směrování. Místo toho název služby, do které chcete odeslat žádost o je vytvořen v definici zásady back-end z `name` hodnota poskytnutá cesta URL požadavku. Příklad:

  - Požadavek na `/api/users/foo` se směruje na instanci služby `fabric:/app/users/foo`
  - Požadavek na `/api/users/bar` se směruje na instanci služby `fabric:/app/users/bar`

Každá instance služby je také rozdělen na oddíly pomocí schéma oddílu Int64 dva oddíly a klíče rozsah, který zahrnuje `Int64.MinValue` k `Int64.MaxValue`. Zásady back-end vypočítá klíč oddílu v tomto rozsahu převedením `id` hodnota zadaná v žádosti o cestě adresy URL 64bitové celé číslo, i když libovolný algoritmus lze zde výpočetní klíč oddílu. 

![Service Fabric s Azure API Management přehled topologie][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>Další postup

Postupujte podle [kurzu](service-fabric-tutorial-deploy-api-management.md) nastavit první clusteru Service Fabric pomocí API Management a toku požadavky prostřednictvím rozhraní API správy k vašim službám.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png