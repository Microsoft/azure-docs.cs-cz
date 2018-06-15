---
title: Tokeny ověřování předat Azure Media Services | Microsoft Docs
description: Zjistěte, jak odeslat ověřovací tokeny z klienta ke službě Azure Media Services doručení klíče
services: media-services
keywords: Ochrana obsahu, DRM, ověření pomocí tokenu
documentationcenter: ''
author: dbgeorge
manager: jasonsue
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: dwgeo
ms.openlocfilehash: b6aca2928465b73e35ac15f01bb776b1f69add0b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788449"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Zjistěte, jak klienti předat tokeny ke službě Azure Media Services doručení klíče
Zákazníci často požádejte jak přehrávač můžete předat tokeny ke službě Azure Media Services doručení klíče pro ověřování tak přehrávač můžete získat klíč. Služba Media Services podporuje jednoduchého webového tokenu (SWT) a formátů JSON Web Token (JWT). Ověření pomocí tokenu je použita k libovolnému typu klíč, bez ohledu na to, jestli používáte běžným šifrováním nebo Advanced Encryption (Standard AES) obálky šifrování v systému.

 V závislosti na player a platformy, na které cílí můžete předat token s vaší player následujícími způsoby:

- Prostřednictvím protokolu HTTP autorizační hlavičky.
    > [!NOTE]
    > Předpona "Nosiče" je očekáván podle specifikace OAuth 2.0. Ukázka přehrávač tokenu konfigurace hostitelem je Azure Media Player [ukázkové stránky](http://ampdemo.azureedge.net/). Pokud chcete nastavit zdroj videa, zvolte **AES (JWT Token)** nebo **AES (SWT Token)**. Token je předán prostřednictvím autorizační hlavičky.

- Prostřednictvím přidání adresy URL parametr s dotazu "tokenu = tokenvalue."  
    > [!NOTE]
    > Předpona "Nosiče" není očekáván. Protože token se budou odesílat prostřednictvím adresy URL, je třeba k obraně řetězec tokenu. Tady je ukázkový kód C#, který ukazuje, jak to udělat:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Přes pole CustomData.
Tato možnost slouží k získání licence PlayReady pouze přes pole CustomData výzvy získání licence PlayReady. V takovém případě token musí být uvnitř dokumentu XML podle postupu popsaného tady:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Uveďte ověřovací token v elementu Token.

- Pomocí alternativní stop HTTP Live Streaming (HLS). Pokud potřebujete nakonfigurovat ověření pomocí tokenu pro AES + HLS přehrávání na iOS nebo Safari, není k dispozici způsob, jak můžete přímo odeslat v tokenu. Další informace o tom, jak chcete-li povolit tento scénář alternativní najdete [příspěvku na blogu](http://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Další postup

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]