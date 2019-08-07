---
title: Ladit hlavičky HTTP pro Azure CDN od Microsoftu | Microsoft Docs
description: Hlavičky požadavků mezipaměti ladění poskytují další informace o zásadách mezipaměti použitých u požadovaného prostředku. Tato záhlaví jsou specifická pro Azure CDN od Microsoftu.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: magattus
ms.openlocfilehash: 297c65c1cd89163b8663819f844dc6c2a83fd1bf
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814074"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Ladit hlavičku protokolu HTTP pro Azure CDN od Microsoftu
Hlavička odpovědi na ladění poskytuje `X-Cache`podrobnosti o tom, ze které vrstvy zásobníku CDN byl obsah obsluhován. Tato hlavička je specifická pro Azure CDN od Microsoftu.

### <a name="response-header-format"></a>Formát hlavičky odpovědi

Záhlaví | Popis
-------|------------
Mezipaměť X: TCP_HIT | Tato hlavička se vrátí při doručování obsahu z mezipaměti Edge pro CDN. 
Mezipaměť X: TCP_REMOTE_HIT | Tato hlavička se vrátí, když se obsah obsluhuje z místní mezipaměti CDN (původní vrstva ochrany).
Mezipaměť X: TCP_MISS | Tato hlavička se vrátí, když dojde k neúspěšnému vytvoření mezipaměti a obsah bude obsluhován od počátku. 


