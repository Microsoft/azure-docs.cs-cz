---
title: Opakování úloh po chybě
description: Vyhledejte chyby a zkuste je znovu.
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
ms.openlocfilehash: eda567fda13d6caca679d0ce4947e042eca9530d
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77652006"
---
# <a name="detecting-and-handling-batch-service-errors"></a>Zjišťování a zpracování chyb služby Batch

Při práci s rozhraním API služby REST je důležité pamatovat na chyby. Při spouštění dávkových úloh není běžné, že dojde k chybám.

## <a name="common-errors"></a>Běžné chyby 

- Selhání sítě – Jedná se o požadavky, které nikdy nedorazily do dávky, nebo odpověď na dávkovou úlohu nedostala klienta v čase.
- Interní chyby serveru – jedná se o standardní 5xx stavového kódu odpovědi HTTP.
- Omezování může způsobit chyby, jako je například 429 nebo 503 odpovědi HTTP na stavový kód s hlavičkou Retry-After.
- 4xx chyby, které zahrnují takové chyby jako už existuje a InvalidOperation. To znamená, že prostředek není ve správném stavu pro přechod stavu.

## <a name="when-to-retry"></a>Kdy opakovat

Rozhraní API dávky vás upozorní, pokud dojde k selhání. Všechny tyto akce se dají opakovat a všechny zahrnují pro tento účel globální obslužnou rutinu opakování. Je nejvhodnější použít tento vestavěný mechanismus.

Po selhání byste před opakováním pokusu měli počkat (několik sekund mezi opakováními). Pokud se znovu pokusíte příliš často nebo příliš rychle, obslužná rutina opakování omezí.


Podrobné informace o každém rozhraní API a jejich výchozích zásadách pro opakování najdete v tématu [stav dávky a kódy chyb](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).
