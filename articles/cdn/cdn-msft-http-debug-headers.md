---
title: Ladit hlavičky HTTP pro Azure CDN od Microsoftu | Microsoft Docs
description: Hlavičky požadavků mezipaměti ladění poskytují další informace o zásadách mezipaměti použitých u požadovaného prostředku. Tato záhlaví jsou specifická pro Azure CDN od Microsoftu.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: allensu
ms.openlocfilehash: 2475bdce3ab8f153cc837601964bf4a2e90a470c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "81260407"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Ladit hlavičku protokolu HTTP pro Azure CDN od Microsoftu
Hlavička odpovědi na ladění `X-Cache` poskytuje podrobnosti o tom, ze které vrstvy zásobníku CDN byl obsah obsluhován. Tato hlavička je specifická pro Azure CDN od Microsoftu.

### <a name="response-header-format"></a>Formát hlavičky odpovědi

Hlavička | Popis
-------|------------
X-mezipaměť: TCP_HIT | Tato hlavička se vrátí při doručování obsahu z mezipaměti Edge pro CDN. 
X-mezipaměť: TCP_REMOTE_HIT | Tato hlavička se vrátí, když se obsah obsluhuje z místní mezipaměti CDN (původní vrstva ochrany).
X-mezipaměť: TCP_MISS | Tato hlavička se vrátí, když dojde k neúspěšnému vytvoření mezipaměti a obsah bude obsluhován od počátku. 


