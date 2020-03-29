---
title: Opakování úloh po chybě
description: Zkontrolujte chyby a opakujte je.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: azure-batch
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/15/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 94ed936e619461a2dbf7ec837c2d80e21c01c88e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919987"
---
# <a name="detecting-and-handling-batch-service-errors"></a>Zjišťování a zpracování chyb služby Batch

Je důležité mít na paměti, zkontrolovat chyby při práci s rozhraním API služby REST. Není neobvyklé, že při spuštění dávkových úloh dochází k chybám.

## <a name="common-errors"></a>Běžné chyby 

- Selhání sítě – jedná se o požadavky, které nikdy nedosáhly batch nebo batch odpověď nedosáhla klienta včas.
- Interní serverové chyby - jedná se o standardní odpověď HTTP stavového kódu 5xx.
- Omezení může způsobit chyby, jako je například 429 nebo 503 stavový kód http odpovědi s opakovat po záhlaví.
- 4xx chyby, které zahrnují takové chyby jako JižExists a InvalidOperation. To znamená, že prostředek není ve správném stavu pro přechod stavu.

Podrobné informace o různých typech kódů chyb a specifických kódech chyb naleznete v [tématu Stav dávky a Kódy chyb](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).

## <a name="when-to-retry"></a>Kdy opakovat

Dávková api vás upozorní, pokud dojde k selhání. Všechny mohou být opakovány a všechny obsahují globální obslužnou rutinu opakování pro tento účel. Nejlepší je použít tento vestavěný mechanismus.

Po selhání byste měli chvíli počkat (několik sekund mezi opakovanými pokusy) před opakováním. Pokud opakujíte příliš často nebo příliš rychle, obslužná rutina opakování bude omezení.

### <a name="for-more-information"></a>Další informace  

[Dávková rozhraní API a nástroje](batch-apis-tools.md) odkazují na referenční informace rozhraní API. Rozhraní .NET API má například [třídu RetryPolicyProvider,]( https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet) kde by měla být zadána požadovaná zásada opakování. 

Podrobné informace o jednotlivých rozhraních API a jejich výchozích zásadách opakování naleznete v seznamu [Stav dávky a kódy chyb](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).
