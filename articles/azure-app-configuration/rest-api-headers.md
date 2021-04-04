---
title: Konfigurace aplikace Azure – REST API – hlavičky
description: Referenční stránky pro hlavičky používané s konfigurací aplikace Azure REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 80b20b0b55219766872166685c0b1257f3c39c55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932570"
---
# <a name="headers"></a>Hlavičky

Tento článek obsahuje odkazy na referenční stránky pro hlavičky používané s konfigurací aplikace Azure REST API.

## <a name="request-headers"></a>Hlavičky požadavku

V následující tabulce jsou popsány běžné hlavičky požadavků používané v konfiguraci aplikací Azure.

| Hlavička | Popis | Příklad |
|--|--|--|
| **Autorizace** | Slouží k [ověření](./rest-api-authentication-index.md) požadavku na službu. Viz [část 14,8](https://tools.ietf.org/html/rfc2616#section-14.8) | `Authorization: HMAC-SHA256 Credential=<Credential>&SignedHeaders=Host;x-ms-date;x-ms-content-sha256&Signature=<Signature>` |
| **Přijetí** | Informuje server o tom, jaký typ média bude klient přijímat v odpovědi HTTP. Viz [část 14,1](https://tools.ietf.org/html/rfc2616#section-14.1) | `Accept: application/vnd.microsoft.appconfig.kv+json;` |
| **Přijmout – datum a čas** | Vyžádá Server, aby vrátil jeho obsah jako reprezentace jeho předchozího stavu. Hodnota tohoto záhlaví je požadovaná hodnota DateTime daného stavu. Viz [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) | `Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **Typ obsahu** | Obsahuje typ média obsahu v rámci textu požadavku HTTP. Viz [část 14,17](https://tools.ietf.org/html/rfc2616#section-14.17) | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **Date** (Datum) | Hodnota DateTime, kterou byl vydán požadavek HTTP. Tato hlavička se používá v [ověřování HMAC](./rest-api-authentication-hmac.md). Viz [část 14,18](https://tools.ietf.org/html/rfc2616#section-14.18) | `Date: Fri, 11 May 2018 18:48:36 GMT` |
| **Hostitel** | Určuje tenanta, pro který se žádost vystavila. Tato hlavička se používá v [ověřování HMAC](./rest-api-authentication-hmac.md). Viz [část 14,23](https://tools.ietf.org/html/rfc2616#section-14.23) | `Host: contoso.azconfig.io` |
| **If-Match** | Slouží k vytvoření podmíněného požadavku HTTP. Tento požadavek by měl být úspěšný jenom v případě, že ETag cílového prostředku odpovídá hodnotě této hlavičky. Hodnota * odpovídá libovolnému ETag. Viz [část 14,24](https://tools.ietf.org/html/rfc2616#section-14.24) | `If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **If-None-Match** | Slouží k vytvoření podmíněného požadavku HTTP. Tento požadavek by měl být úspěšný pouze v případě, že značka ETag cílového prostředku neodpovídá hodnotě této hlavičky. Hodnota * odpovídá libovolnému ETag. Viz [část 14,26](https://tools.ietf.org/html/rfc2616#section-14.26) | `If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Sync – token** | Slouží k povolení konzistence v reálném čase během sekvence požadavků. | `Sync-Token: jtqGc1I4=MDoyOA==;sn=28` |
| **x-MS-Client-Request-ID** | Jedinečný identifikátor poskytnutý klientem, který se používá ke sledování zpátečního přenosu požadavku. | `x-ms-client-request-id: 00000000-0000-0000-0000-000000000000` |
| **x-MS-Content-SHA256** | SHA256 výtah textu požadavku HTTP. Tato hlavička se používá v [ověřování HMAC](./rest-api-authentication-hmac.md). | `x-ms-content-sha256: 47DEQpj8HBSa+/TImW+5JCeuQeRkm5NMpJWZG3hSuFU=` |
| **x-MS-Date** | Tato hlavička se dá nastavit a použít místo `Date` záhlaví, pokud není k záhlaví kalendářního data možné přistupovat. Tato hlavička se používá v [ověřování HMAC](./rest-api-authentication-hmac.md). | `x-ms-date: Fri, 11 May 2018 18:48:36 GMT` |
| **x-MS-Return-Client-Request-ID** | Používá se ve spojení s `x-ms-client-request-id` hlavičkou. Pokud je hodnota této hlavičky true, server bude vyzván k vrácení hodnoty `x-ms-client-request-id` hlavičky požadavku. | `x-ms-return-client-request-id: true` |

## <a name="response-headers"></a>Hlavičky odpovědi

Server může ve svých odpovědích zahrnovat následující hlavičky protokolu HTTP.

| Hlavička | Popis | Příklad |
|--|--|--|
| **Typ obsahu** | Obsahuje typ média obsahu v těle odpovědi HTTP. Viz [část 14,17](https://tools.ietf.org/html/rfc2616#section-14.17) | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **Značk** | Neprůhledný token představující stav daného prostředku. Dá se použít v podmíněných operacích. Viz [část 14,19](https://tools.ietf.org/html/rfc2616#section-14.19) | `ETag: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Naposledy změněno** | V této části najdete popis poslední změny požadovaného prostředku. Formátováno jako [Datum http](https://tools.ietf.org/html/rfc2616#section-3.3.1). Viz [část 14,29](https://tools.ietf.org/html/rfc2616#section-14.29) | `Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT` |
| **Odkaz** | Obsahuje odkazy na prostředky, které se vztahují k odpovědi. Tato hlavička se používá pro stránkování pomocí _následujícího_ odkazu. Viz [RFC 5988](https://tools.ietf.org/html/rfc5988) | `Link: </kv?after={token}>; rel="next"` |
| **Soupisky – DateTime** | Indikuje, že obsah obsažený v odpovědi představuje předchozí stav. Hodnota tohoto záhlaví je DateTime tohoto stavu. Viz [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) | `Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **opakovat po – MS** | Poskytuje navrhované období (v milisekundách), po které bude klient čekat před opakováním neúspěšné žádosti. | `retry-after-ms: 10` |
| **x-MS-Request-ID** | Jedinečné ID vygenerované serverem, které se používá ke sledování žádosti v rámci služby. | `x-ms-request-id: 00000000-0000-0000-0000-000000000000` |
| **Webová – ověření** | Slouží k tomu, aby se klienti vyvolali pro ověřování a měli důvody, proč se pokus o ověření nezdařil. Viz [část 14,47](https://tools.ietf.org/html/rfc2616#section-14.47) | `WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Signature"` |
