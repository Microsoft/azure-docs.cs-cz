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
ms.openlocfilehash: 5d18b0265d2b1c114ed9ef326241ed531e015288
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49385135"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Správa protokolů a šifer ve službě Azure API Management

Azure API Management podporuje více verzí protokolu TLS pro stranách klienta i back-endu, jakož i šifrování 3DES.

Tato příručka ukazuje, jak spravovat protokoly a šifer konfigurace pro instance Azure API Management.

![Správa protokolů a šifer v APIM](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

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