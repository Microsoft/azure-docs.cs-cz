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
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: a55d16a35b5eec1af2b24d02e158905493615999
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441108"
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
2. Vyberte **SSL** z nabídky.  
    ![Správa protokolů a šifer v APIM - nabídky](./media/api-management-howto-manage-protocols-ciphers/api-management-menu.png)
3. Povolí nebo zakáže požadované protokoly nebo šifry.
4. Klikněte na **Uložit**. Změny se použijí během hodiny.  
    ![Správa protokolů a šifer v APIM - uložit](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers-save.png)

## <a name="next-steps"></a>Další postup

* Další informace o [TLS (Transport Layer Security)](https://docs.microsoft.com/dotnet/framework/network-programming/tls).
* Podívejte se na informace [videa](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o službě API Management.