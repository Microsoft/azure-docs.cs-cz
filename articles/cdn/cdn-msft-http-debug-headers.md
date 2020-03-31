---
title: Ladění hlaviček HTTP pro Azure CDN od Microsoftu | Dokumenty společnosti Microsoft
description: Hlavičky požadavků na ladění mezipaměti poskytují další informace o zásadách mezipaměti použitých na požadovaný prostředek. Tyto hlavičky jsou specifické pro Azure CDN od Microsoftu.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68814074"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Ladění hlavičky HTTP pro Azure CDN od Microsoftu
Hlavička odpovědi ladění `X-Cache`, obsahuje podrobnosti o tom, z jaké vrstvy zásobníku CDN byl obsah obsluhován. Tato hlavička je specifická pro Azure CDN od Microsoftu.

### <a name="response-header-format"></a>Formát záhlaví odpovědi

Hlavička | Popis
-------|------------
X-Cache: TCP_HIT | Tato hlavička je vrácena, když je obsah obsluhován z mezipaměti okraje CDN. 
X-Cache: TCP_REMOTE_HIT | Tato hlavička je vrácena, když je obsah obsluhován z regionální mezipaměti CDN (vrstva štítu původu)
X-Cache: TCP_MISS | Toto záhlaví je vrácena, když je chybě cache a obsah se zobrazí z původu. 


