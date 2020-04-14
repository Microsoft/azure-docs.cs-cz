---
title: Ladění hlaviček HTTP pro Azure CDN od Microsoftu | Dokumenty společnosti Microsoft
description: Hlavičky požadavků na ladění mezipaměti poskytují další informace o zásadách mezipaměti použitých na požadovaný prostředek. Tyto hlavičky jsou specifické pro Azure CDN od Microsoftu.
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
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260407"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Ladění hlavičky HTTP pro Azure CDN od Microsoftu
Hlavička odpovědi ladění `X-Cache`, obsahuje podrobnosti o tom, z jaké vrstvy zásobníku CDN byl obsah obsluhován. Tato hlavička je specifická pro Azure CDN od Microsoftu.

### <a name="response-header-format"></a>Formát záhlaví odpovědi

Hlavička | Popis
-------|------------
X-Cache: TCP_HIT | Tato hlavička je vrácena, když je obsah obsluhován z mezipaměti okraje CDN. 
X-Cache: TCP_REMOTE_HIT | Tato hlavička je vrácena, když je obsah obsluhován z regionální mezipaměti CDN (vrstva štítu původu)
X-Cache: TCP_MISS | Toto záhlaví je vrácena, když je chybě cache a obsah se zobrazí z původu. 


