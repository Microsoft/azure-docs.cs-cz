---
title: Opakování úloh po chybě
description: Vyhledejte chyby a zkuste je znovu.
ms.topic: article
ms.date: 02/15/2020
ms.custom: seodec18
ms.openlocfilehash: 8addc4418f268a2c27b730543bdb309ef45fd5f4
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116532"
---
# <a name="detecting-and-handling-batch-service-errors"></a>Zjišťování a zpracování chyb služby Batch

Při práci s rozhraním API služby REST je důležité pamatovat na chyby. Při spouštění dávkových úloh není běžné, že dojde k chybám.

## <a name="common-errors"></a>Běžné chyby 

- Selhání sítě – Jedná se o požadavky, které nikdy nedorazily do dávky, nebo odpověď na dávkovou úlohu nedostala klienta v čase.
- Interní chyby serveru – jedná se o standardní 5xx stavového kódu odpovědi HTTP.
- Omezování může způsobit chyby, jako je například 429 nebo 503 odpovědi HTTP na stavový kód s hlavičkou Retry-After.
- 4xx chyby, které zahrnují takové chyby jako už existuje a InvalidOperation. To znamená, že prostředek není ve správném stavu pro přechod stavu.

Podrobné informace o různých typech chybových kódů a konkrétních chybových kódů najdete v tématu [stav služby Batch a kódy chyb](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).

## <a name="when-to-retry"></a>Kdy opakovat

Rozhraní API dávky vás upozorní, pokud dojde k selhání. Všechny tyto akce se dají opakovat a všechny zahrnují pro tento účel globální obslužnou rutinu opakování. Je nejvhodnější použít tento vestavěný mechanismus.

Po selhání byste před opakováním pokusu měli počkat (několik sekund mezi opakováními). Pokud se znovu pokusíte příliš často nebo příliš rychle, obslužná rutina opakování omezí.

### <a name="for-more-information"></a>Další informace  

[Rozhraní API a nástroje Batch](batch-apis-tools.md) odkazují na referenční informace rozhraní API. Rozhraní .NET API má například [třídu RetryPolicyProvider]( https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet) , kde by měla být zadána požadovaná zásada opakování. 

Podrobné informace o každém rozhraní API a jejich výchozích zásadách pro opakování najdete v tématu [stav dávky a kódy chyb](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).
