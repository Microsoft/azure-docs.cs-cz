---
title: Předání ověřovacích tokenů službě Azure Media Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak odesílat ověřovací tokeny z klienta do služby doručování klíčů Azure Media Services
services: media-services
keywords: ochrana obsahu, DRM, ověřování tokenů
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 15d4cbc372f5d5ec0d323170189329152ed436e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73684936"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Zjistěte, jak klienti předávají tokeny do služby doručování klíčů Azure Media Services
Zákazníci se často ptají, jak může přehrávač předat žetony službě doručování klíčů Azure Media Services k ověření, aby hráč mohl klíč získat. Media Services podporuje jednoduché webové tokeny (SWT) a JSON web token (JWT) formáty. Ověřování tokenů se používá u všech typů klíčů bez ohledu na to, zda v systému používáte běžné šifrování nebo šifrování obálek Advanced Encryption Standard (AES).

 V závislosti na hráči a platformě, na kterou cílíte, můžete předat žeton se svým hráčem následujícími způsoby:

- Prostřednictvím hlavičky autorizace protokolu HTTP.
    > [!NOTE]
    > Předpona "Nosič" se očekává podle specifikací OAuth 2.0. Ukázkový přehrávač s konfigurací tokenu je hostovaný na [ukázkové stránce](https://ampdemo.azureedge.net/)přehrávače Azure Media Player . Chcete-li nastavit zdroj videa, zvolte **AES (JWT Token)** nebo **AES (SWT Token)**. Token je předán prostřednictvím hlavičky Autorizace.

- Přidáním parametru dotazu URL s "token=tokenvalue".  
    > [!NOTE]
    > Předpona "Nosič" není očekávána. Vzhledem k tomu, že token je odeslán prostřednictvím adresy URL, je třeba brnění token řetězec. Zde je ukázkový kód jazyka C#, který ukazuje, jak na to:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Prostřednictvím pole CustomData.
Tato možnost se používá pouze pro získání licence PlayReady prostřednictvím pole CustomData výzvy pro získání licence PlayReady. V takovém případě musí být token uvnitř dokumentu XML, jak je popsáno zde:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Vložte ověřovací token do prvku Token.

- Prostřednictvím alternativního seznamu stop http živého streamování (HLS). Pokud potřebujete nakonfigurovat ověřování tokenů pro přehrávání AES + HLS v systému iOS/Safari, neexistuje způsob, jak můžete přímo odeslat token. Další informace o tom, jak střídat seznam stop, aby byl povolen tento scénář, naleznete v tomto [příspěvku blogu](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Další kroky

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
