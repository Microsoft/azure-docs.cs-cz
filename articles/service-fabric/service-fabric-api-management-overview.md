---
title: Azure Service Fabric se správa rozhraní API – přehled | Dokumentace Microsoftu
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
ms.openlocfilehash: 01b67cc0c20710fcf7c9a072e0ba3baaf286852a
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52423639"
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Service Fabric s Azure API Management – přehled

Cloudové aplikace obvykle potřebují front-end bránu, která poskytuje jediný bod příjmu příchozího přenosu od uživatelů, zařízení nebo dalších aplikací. V Service Fabric, může být brána Bezstavová služba, jako [aplikace ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), nebo jiná služba navržená pro příjem provozu, jako například [Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [služby IoT Hub](https://docs.microsoft.com/azure/iot-hub/), nebo [Azure API Management](https://docs.microsoft.com/azure/api-management/).

Tento článek je úvodem do jako brána pro vaše aplikace Service Fabric pomocí Azure API Management. API Management se integruje přímo do Service Fabric umožňuje publikovat rozhraní API s bohatou sadou pravidel směrování pro back-end služby Service Fabric. 

## <a name="availability"></a>Dostupnost

> [!IMPORTANT]
> Tato funkce je dostupná v **Premium** a **Developer** podporu úrovních služby API Management kvůli požadované virtuální sítě.

## <a name="architecture"></a>Architektura

Běžné architektury Service Fabric používá jednostránkovou webovou aplikaci, která provádí volání HTTP na back endovým službám, které zveřejňují rozhraní API HTTP. [Začínáme – ukázkové aplikace Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) ukazuje příklad této architektury.

V tomto scénáři bezstavovou webovou službu slouží jako brány do aplikace Service Fabric. Tento přístup vyžaduje, abyste napsal webovou službu, která se dá požadavky HTTP proxy server na back endové služby, jak je znázorněno v následujícím diagramu:

![Service Fabric s Azure API Management přehled topologie][sf-web-app-stateless-gateway]

Jak aplikace jejich složitost v, tak proveďte brány, které se musí poskytnout rozhraní API před řadu back endové služby. Azure API Management je určený k řešení složitých rozhraní API pomocí pravidel směrování, řízení přístupu, omezení četnosti, sledování, protokolování událostí a ukládání odpovědí do mezipaměti s minimálním úsilím na vaší straně. Azure API Management podporuje zjišťování služby Service Fabric, překlad oddílů a replik výběr inteligentně směrovat požadavky přímo na back endové služby v Service Fabric, takže není nutné psát bezstavové brány rozhraní API. 

V tomto scénáři webové uživatelské rozhraní i nadále obsluhuje prostřednictvím webové služby, při volání rozhraní API protokolu HTTP jsou spravovaná a směrovat pomocí Azure API Management, jak je znázorněno v následujícím diagramu:

![Service Fabric s Azure API Management přehled topologie][sf-apim-web-app]

## <a name="application-scenarios"></a>Scénáře aplikací

Služby v Service Fabric může být Bezstavová nebo stavová a že může rozdělit na oddíly jedním ze tří režimů: singleton, rozsah int-64 a s názvem. Rozlišení koncového bodu služby vyžaduje identifikaci konkrétní oddíl konkrétní instanci služby. Při překladu koncový bod služby, název instance služby (například `fabric:/myapp/myservice`) stejně jako konkrétního oddílu služba musí být zadána, s výjimkou oddílů singleton.

Azure API Management můžete použít s libovolnou kombinací jakékoli schéma dělení, bezstavové služby a stavové služby.

## <a name="send-traffic-to-a-stateless-service"></a>Posílání provozu do bezstavové služby

V nejjednodušším případě přenosy předávaly do instance bezstavové služby. K dosažení tohoto cíle, API Management operace obsahuje zásady zpracování příchozích pomocí Service Fabric back-end systém, který se mapuje na instanci konkrétní bezstavové služby v Service Fabric back endu. Požadavky odeslané na tuto službu se posílají náhodné repliky instance bezstavové služby.

#### <a name="example"></a>Příklad:
V následujícím scénáři aplikace Service Fabric obsahuje Bezstavová služba s názvem `fabric:/app/fooservice`, který zpřístupňuje interní rozhraní HTTP API. Název instance služby je dobře známý a může být pevně zakódované přímo v zásadách zpracování příchozích API Management. 

![Service Fabric s Azure API Management přehled topologie][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Posílání provozu do stavové služby

Podobně jako bezstavové služby scénář, provozu může být přeposílán instanci stavové služby. V takovém případě operaci rozhraní API Management obsahuje zásady zpracování příchozích pomocí Service Fabric back-end systém, který mapuje žádost na konkrétní oddíl s konkrétním *stavová* instance služby. Oddíl pro mapování každý požadavek je počítaný metodou lambda pomocí vstupu z příchozího požadavku HTTP, jako je například hodnota v cestě adresy URL. Zásady můžou nakonfigurovat na odesílání žádostí, pouze primární replikou, nebo náhodného repliky pro operace čtení.

#### <a name="example"></a>Příklad:

V následujícím scénáři aplikace Service Fabric obsahuje dělené stavové služby s názvem `fabric:/app/userservice` , která zveřejní vnitřní rozhraní HTTP API. Název instance služby je dobře známý a může být pevně zakódované přímo v zásadách zpracování příchozích API Management.  

Služba je rozdělen na oddíly pomocí schématu oddílu Int64 dva oddíly a klíče rozsahu, který zahrnuje `Int64.MinValue` k `Int64.MaxValue`. Back-end zásady vypočítá klíče oddílu v rámci tohoto rozsahu převedením `id` hodnota zadaná v cestě adresy URL požadavku na 64bitové celé číslo, i když libovolný algoritmus lze zde compute klíč oddílu. 

![Service Fabric s Azure API Management přehled topologie][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Odesílat provoz do více bezstavové služby

V pokročilejších scénářích můžete definovat operace, která se mapuje žádosti více než jednu instanci služby API Management. V takovém případě každé operace obsahuje zásady, které mapuje požadavky na konkrétní instanci služby podle hodnoty z příchozího požadavku HTTP, jako třeba řetězec cesty nebo dotazu adresy URL a v případě stavových služeb, oddílu v rámci instance služby. 

K dosažení tohoto cíle, API Management operace obsahuje zásady zpracování příchozích pomocí Service Fabric back-end systém, který se mapuje na instanci bezstavové služby v Service Fabric podle hodnoty získané z příchozího požadavku protokolu HTTP back endu. Požadavky na instanci služby se posílají náhodné repliky instance služby.

#### <a name="example"></a>Příklad:

V tomto příkladu je vytvořena nová instance bezstavovou službu pro každého uživatele, aplikace se dynamicky generované názvem pomocí následující vzorec:
 
 - `fabric:/app/users/<username>`

 Každá služba má jedinečný název, ale nejsou známy názvy předem, protože služby jsou vytvořeny v reakci na uživatele nebo správce vstup a proto nemůže být pevně zakódovaný do zásad služby APIM nebo pravidla směrování. Místo toho se v definici zásad back endu z vygeneruje název služby, do které chcete odeslat žádost o `name` hodnota zadaná v cestě adresy URL požadavku. Příklad:

  - Požadavek na `/api/users/foo` přesměrovala do instance služby `fabric:/app/users/foo`
  - Požadavek na `/api/users/bar` přesměrovala do instance služby `fabric:/app/users/bar`

![Service Fabric s Azure API Management přehled topologie][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Odesílat provoz do více stavové služby

Podobně jako v příkladu Bezstavová služba, API Management operace můžete mapovat požadavky na více než jeden **stavová** instance služby, v které takovém také může být zapotřebí provést překlad oddílů pro každou instanci stavové služby.

K dosažení tohoto cíle, API Management operace obsahuje zásady zpracování příchozích pomocí Service Fabric back-end systém, který se mapuje na instanci stavové služby v Service Fabric podle hodnoty získané z příchozího požadavku protokolu HTTP back endu. Kromě mapování požadavku na konkrétní instanci služby, žádost lze také mapovat do konkrétního oddílu v rámci instance služby, případně na primární repliku nebo náhodného sekundární repliky v rámci oddílu.

#### <a name="example"></a>Příklad:

V tomto příkladu je vytvořena nová instance stavové služby pro každého uživatele, aplikace se dynamicky generované názvem pomocí následující vzorec:
 
 - `fabric:/app/users/<username>`

 Každá služba má jedinečný název, ale nejsou známy názvy předem, protože služby jsou vytvořeny v reakci na uživatele nebo správce vstup a proto nemůže být pevně zakódovaný do zásad služby APIM nebo pravidla směrování. Místo toho se v definici zásad back endu z vygeneruje název služby, do které chcete odeslat žádost o `name` zadat hodnotu cesty adresy URL požadavku. Příklad:

  - Požadavek na `/api/users/foo` přesměrovala do instance služby `fabric:/app/users/foo`
  - Požadavek na `/api/users/bar` přesměrovala do instance služby `fabric:/app/users/bar`

Každá instance služby je také rozdělit na oddíly pomocí dva oddíly a klíče rozsahu, který zahrnuje schéma oddílu Int64 `Int64.MinValue` k `Int64.MaxValue`. Back-end zásady vypočítá klíče oddílu v rámci tohoto rozsahu převedením `id` hodnota zadaná v cestě adresy URL požadavku na 64bitové celé číslo, i když libovolný algoritmus lze zde compute klíč oddílu. 

![Service Fabric s Azure API Management přehled topologie][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>Další postup

Postupujte podle [kurzu](service-fabric-tutorial-deploy-api-management.md) nastavení vašeho prvního clusteru Service Fabric pomocí rozhraní API Management a tok žádostí prostřednictvím služby API Management k vašim službám.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png