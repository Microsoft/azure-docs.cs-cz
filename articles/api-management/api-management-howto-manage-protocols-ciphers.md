---
title: Správa protokolů a šifer ve správě rozhraní Azure API | Dokumenty společnosti Microsoft
description: Zjistěte, jak spravovat protokoly (TLS) a šifry (DES) ve správě rozhraní API Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: 7f87389016286c9f1b91abc77155be9e94005371
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335873"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Správa protokolů a šifer ve službě Azure API Management

Azure API Management podporuje více verzí protokolu TLS pro klientské i back-endové strany, stejně jako šifra 3DES.

Tato příručka ukazuje, jak spravovat konfigurace protokolů a šifrování pro instanci Azure API Management.

![Správa protokolů a šifer v APIM](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle kroků v tomto článku, musíte mít:

* Instance správy rozhraní API

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Jak spravovat protokoly TLS a šifru 3DES

1. Přejděte na **instanci správy rozhraní API** na webu Azure Portal.
2. V nabídce vyberte **nastavení protokolu.**  
3. Povolte nebo zakažte požadované protokoly nebo šifry.
4. Klikněte na **Uložit**. Změny budou provedeny do jedné hodiny.  

## <a name="next-steps"></a>Další kroky

* Další informace o [protokolu TLS (Transport Layer Security)](https://docs.microsoft.com/dotnet/framework/network-programming/tls).
* Podívejte se na další [videa](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o správě rozhraní API.