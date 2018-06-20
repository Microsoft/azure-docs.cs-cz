---
title: Ukázky zásad Azure API Managementu | Microsoft Docs
description: Přečtěte si o zásadách, které jsou k dispozici pro použití v Azure API Managementu.
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: sample
ms.date: 10/31/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: c58396379db2d9073c1f7d3a4c3aa68dfd06079e
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830179"
---
# <a name="api-management-policy-samples"></a>Ukázky zásad API Managementu

[Zásady](api-management-howto-policies.md) jsou vynikající funkcí systému, která vydavatelům umožňuje měnit chování rozhraní API prostřednictvím konfigurace. Zásady představují kolekci příkazů, které se postupně provádí na základě požadavku nebo odezvy z rozhraní API. Následující tabulka obsahuje odkazy na ukázky se stručným popisem každé ukázky.

|                                                                                                                                                                      |                                                                                                                                                                                                                             |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Příchozí zásady**                                                                                                                                                 |                                                                                                                                                                                                                             |
| [Přidání hlavičky Forwarded umožňující back-endovému rozhraní API vytvářet správné adresy URL](./policies/set-header-to-enable-backend-to-construct-urls.md?toc=api-management/toc.json) | Ukazuje, jak do příchozí žádosti přidat hlavičku Forwarded, která back-endovému rozhraní API umožní vytvářet správné adresy URL.                                                                                                        |
| [Přidání hlavičky obsahující ID korelace](./policies/add-correlation-id.md?toc=api-management/toc.json)                                                             | Ukazuje, jak do příchozího požadavku přidat hlavičku obsahující ID korelace.                                                                                                                                        |
| [Přidávání funkcí do back-endové služby a uložení odpovědi do mezipaměti](./policies/cache-response.md?toc=api-management/toc.json)                                             | Ukazuje, jak do back-endové služby přidat funkce. Příkladem je přijmutí názvu místa namísto zeměpisné šířky a délky v rozhraní API předpovědi počasí.                                                                    |
| [Autorizace přístupu na základě deklarací JWT](./policies/authorize-request-based-on-jwt-claims.md?toc=api-management/toc.json)                                              | Ukazuje, jak autorizovat přístup ke konkrétním metodám HTTP v rozhraní API na základě deklarací JWT.                                                                                                                                       |
| [Autorizace požadavků na používání externího objektu Authorizer](./policies/authorize-request-using-external-authorizer.md)                                                   | Ukazuje, jak používat externí objekt Authorizer pro zabezpečení přístupu API.                                                                                                                                                               |
| [Autorizace přístupu pomocí tokenu Google OAuth](./policies/use-google-as-oauth-token-provider.md?toc=api-management/toc.json)                                            | Ukazuje, jak autorizovat přístup k vašim koncovým bodům s využitím Googlu jako poskytovatele tokenu OAuth.                                                                                                                                    |
| [Vygenerování sdíleného přístupového podpisu a předání požadavku do úložiště Azure](./policies/generate-shared-access-signature.md?toc=api-management/toc.json)                  | Ukazuje, jak vygenerovat [sdílený přístupový podpis](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) pomocí výrazů a předat požadavek do úložiště Azure se zásadami rewrite-uri. |
| [Získání tokenu přístupu OAuth2 z AAD a jeho předání do back-endu](./policies/use-oauth2-for-authorization.md?toc=api-management/toc.json)                             | Poskytuje příklad použití OAuth2 k autorizaci mezi bránou a back-endem. Ukazuje, jak získat token přístupu z AAD a předat ho do back-endu.                                                    |
| [Získání tokenu X-CSRF z brány SAP pomocí zásad odeslání žádosti](./policies/get-x-csrf-token-from-sap-gateway.md?toc=api-management/toc.json)                           | Ukazuje, jak implementovat vzor X-CSRF používaný mnoho rozhraními API. Tento příklad je konkrétně pro bránu SAP.                                                                                                                           |
| [Směrování požadavku na základě velikosti jeho obsahu](./policies/route-requests-based-on-size.md?toc=api-management/toc.json)                                            | Ukazuje, jak směrovat požadavky na základě velikosti jejich obsahu.                                                                                                                                                       |
| [Odeslání informací o kontextu požadavku back-endové službě](./policies/send-request-context-info-to-backend-service.md?toc=api-management/toc.json)                    | Ukazuje, jak odeslat určité kontextové informace back-endové službě kvůli protokolování nebo zpracování.                                                                                                                                |
| [Nastavení doby uložení odpovědi v mezipaměti](./policies/set-cache-duration.md?toc=api-management/toc.json)                                                                          | Ukazuje, jak nastavit dobu uložení odpovědi v mezipaměti pomocí hodnoty maxAge v hlavičce Cache-Control zaslané back-endem.                                                                                                             |
| **Odchozí zásady**                                                                                                                                                |                                                                                                                                                                                                                             |
| [Filtrování obsahu odpovědi](./policies/filter-response-content.md?toc=api-management/toc.json)                                                                         | Ukazuje, jak filtrovat datové prvky z datové části odpovědi na základě produktu přidruženého k požadavku.                                                                                                        |
| **Zásady při chybě**                                                                                                                                                |                                                                                                                                                                                                                             |
| [Protokolování chyb do Stackify](./policies/log-errors-to-stackify.md?toc=api-management/toc.json)                                                                           | Ukazuje, jak přidat zásadu protokolování chyb k zasílání chyb do Stackify za účelem protokolování.                                                                                                                                            |
