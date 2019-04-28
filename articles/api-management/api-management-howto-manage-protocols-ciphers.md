---
title: Správa protokolů a šifer ve službě Azure API Management | Dokumentace Microsoftu
description: Další informace o správě protokolů (TLS nebo SSL) a šifer (DES) ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2019
ms.author: apimpm
ms.openlocfilehash: 91b6cd64a42319b2a5307919c2efe6bc8e5dcd64
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60657792"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Správa protokolů a šifer ve službě Azure API Management

Azure API Management podporuje více verzí protokolu TLS pro stranách klienta i back-endu, jakož i šifrování 3DES.

Tato příručka ukazuje, jak spravovat protokoly a šifer konfigurace pro instance Azure API Management.

![Správa protokolů a šifer v APIM](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle kroků v tomto článku, budete potřebovat:

* Instance služby API Management

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Správa protokolů a 3DES šifer TLS

1. Přejděte do vaší **instance služby API Management** na webu Azure Portal.
2. Vyberte **protokol nastavení** z nabídky.  
3. Povolí nebo zakáže požadované protokoly nebo šifry.
4. Klikněte na **Uložit**. Změny se použijí během hodiny.  

## <a name="next-steps"></a>Další postup

* Další informace o [TLS (Transport Layer Security)](https://docs.microsoft.com/dotnet/framework/network-programming/tls).
* Podívejte se na informace [videa](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o službě API Management.