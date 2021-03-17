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
ms.topic: sample
ms.date: 10/31/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: b5b8c82aa420b62e3b6e68ee53352eb9f77988f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86506685"
---
# <a name="api-management-policy-samples"></a>Ukázky zásad API Managementu

[Zásady](../api-management-howto-policies.md) jsou vynikající funkcí systému, která vydavatelům umožňuje měnit chování rozhraní API prostřednictvím konfigurace. Zásady představují kolekci příkazů, které se postupně provádí na základě požadavku nebo odezvy z rozhraní API. Následující tabulka obsahuje odkazy na ukázky se stručným popisem každé ukázky.

| Příchozí zásady | Popis |
| ---------------- | ----------- |
| [Přidání hlavičky Forwarded umožňující back-endovému rozhraní API vytvářet správné adresy URL](./set-header-to-enable-backend-to-construct-urls.md) | Ukazuje, jak do příchozí žádosti přidat hlavičku Forwarded, která back-endovému rozhraní API umožní vytvářet správné adresy URL.                                                                                                        |
| [Přidání hlavičky obsahující ID korelace](./add-correlation-id.md)                                                             | Ukazuje, jak do příchozího požadavku přidat hlavičku obsahující ID korelace.                                                                                                                                        |
| [Přidávání funkcí do back-endové služby a uložení odpovědi do mezipaměti](./cache-response.md)                                             | Ukazuje, jak do back-endové služby přidat funkce. Příkladem je přijmutí názvu místa namísto zeměpisné šířky a délky v rozhraní API předpovědi počasí.                                                                    |
| [Autorizace přístupu na základě deklarací JWT](./authorize-request-based-on-jwt-claims.md)                                              | Ukazuje, jak autorizovat přístup ke konkrétním metodám HTTP v rozhraní API na základě deklarací JWT.                                                                                                                                       |
| [Autorizace požadavků na používání externího objektu Authorizer](./authorize-request-using-external-authorizer.md)                                                   | Ukazuje, jak používat externí objekt Authorizer pro zabezpečení přístupu API.                                                                                                                                                               |
| [Autorizace přístupu pomocí tokenu Google OAuth](./use-google-as-oauth-token-provider.md)                                            | Ukazuje, jak autorizovat přístup k vašim koncovým bodům s využitím Googlu jako poskytovatele tokenu OAuth.                                                                                                                                    |
| [Filtrování IP adres při použití služby Application Gateway](./filter-ip-addresses-when-using-appgw.md) | Ukazuje, jak filtrovat IP adresy v zásadách, pokud se k instanci API Managementu přistupuje prostřednictvím služby Application Gateway.
| [Vygenerování sdíleného přístupového podpisu a předání požadavku do úložiště Azure](./generate-shared-access-signature.md)                  | Ukazuje, jak vygenerovat [sdílený přístupový podpis](../../storage/common/storage-sas-overview.md) pomocí výrazů a předat požadavek do úložiště Azure se zásadami rewrite-uri. |
| [Získání tokenu přístupu OAuth2 z AAD a jeho předání do back-endu](./use-oauth2-for-authorization.md)                             | Poskytuje příklad použití OAuth2 k autorizaci mezi bránou a back-endem. Ukazuje, jak získat token přístupu z AAD a předat ho do back-endu.                                                    |
| [Získání tokenu X-CSRF z brány SAP pomocí zásad odeslání žádosti](./get-x-csrf-token-from-sap-gateway.md)                           | Ukazuje, jak implementovat vzor X-CSRF používaný mnoho rozhraními API. Tento příklad je konkrétně pro bránu SAP.                                                                                                                           |
| [Směrování požadavku na základě velikosti jeho obsahu](./route-requests-based-on-size.md)                                            | Ukazuje, jak směrovat požadavky na základě velikosti jejich obsahu.                                                                                                                                                       |
| [Odeslání informací o kontextu požadavku back-endové službě](./send-request-context-info-to-backend-service.md)                    | Ukazuje, jak odeslat určité kontextové informace back-endové službě kvůli protokolování nebo zpracování.                                                                                                                                |
| [Nastavení doby uložení odpovědi v mezipaměti](./set-cache-duration.md)                                                                          | Ukazuje, jak nastavit dobu uložení odpovědi v mezipaměti pomocí hodnoty maxAge v hlavičce Cache-Control zaslané back-endem.                                                                                                             |
| **Odchozí zásady** | **Popis** |
| [Filtrování obsahu odpovědi](./filter-response-content.md)                                                                         | Ukazuje, jak filtrovat datové prvky z datové části odpovědi na základě produktu přidruženého k požadavku.                                                                                                        |
| **Zásady při chybě** | **Popis** |
| [Protokolování chyb do Stackify](./log-errors-to-stackify.md)                                                                           | Ukazuje, jak přidat zásadu protokolování chyb k zasílání chyb do Stackify za účelem protokolování.                                                                                                                                            |
