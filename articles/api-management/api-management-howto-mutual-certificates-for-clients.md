---
title: Zabezpečte rozhraní API pomocí ověřování klientského certifikátu v API Management
titleSuffix: Azure API Management
description: Naučte se zabezpečit přístup k rozhraním API pomocí klientských certifikátů.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 8c1d126f01580574a83850e63945aa7e513eaeda
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76713146"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Jak zabezpečit rozhraní API pomocí ověřování klientského certifikátu v API Management

API Management poskytuje možnost zabezpečení přístupu k rozhraním API (například klienta k API Management) pomocí klientských certifikátů. Příchozí certifikát a kontrolu vlastností certifikátu můžete ověřit podle požadovaných hodnot pomocí výrazů zásad.

Informace o zabezpečení přístupu k back-endové službě rozhraní API pomocí klientských certifikátů (tj. API Management do back-endu) najdete v tématu [zabezpečení back-endové služby pomocí ověřování klientských certifikátů](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates) .

> [!IMPORTANT]
> Pokud chcete přijímat a ověřovat klientské certifikáty přes HTTP/2 na úrovních pro vývojáře, Basic, Standard nebo Premium, musíte v okně vlastní domény zapnout nastavení vyjednávat klientský certifikát, jak je znázorněno níže.

![Vyjednat klientský certifikát](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> Pokud chcete přijímat a ověřovat klientské certifikáty ve vrstvě spotřeby, musíte zapnout nastavení "požadovat certifikát klienta" v okně vlastní domény, jak je znázorněno níže.

![Požádat o certifikát klienta](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Kontroluje se Vydavatel a předmět.

Níže uvedené zásady je možné nakonfigurovat tak, aby kontrolovaly vystavitele a předmět klientského certifikátu:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Pokud chcete zakázat kontrolu seznamu odvolaných certifikátů, použijte `context.Request.Certificate.VerifyNoRevocation()` místo `context.Request.Certificate.Verify()`.
> Pokud je klientský certifikát podepsaný svým držitelem, musí se napřed API Management [, aby se](api-management-howto-ca-certificates.md) `context.Request.Certificate.Verify()` a `context.Request.Certificate.VerifyNoRevocation()` fungoval.

## <a name="checking-the-thumbprint"></a>Kontrola kryptografického otisku

Níže uvedené zásady se dají nakonfigurovat tak, aby kontrolovaly kryptografický otisk klientského certifikátu:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Pokud chcete zakázat kontrolu seznamu odvolaných certifikátů, použijte `context.Request.Certificate.VerifyNoRevocation()` místo `context.Request.Certificate.Verify()`.
> Pokud je klientský certifikát podepsaný svým držitelem, musí se napřed API Management [, aby se](api-management-howto-ca-certificates.md) `context.Request.Certificate.Verify()` a `context.Request.Certificate.VerifyNoRevocation()` fungoval.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Kontrola kryptografického otisku proti certifikátům odeslaným do API Management

Následující příklad ukazuje, jak kontrolovat kryptografický otisk certifikátu klienta proti certifikátům odeslaným do API Management:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> Pokud chcete zakázat kontrolu seznamu odvolaných certifikátů, použijte `context.Request.Certificate.VerifyNoRevocation()` místo `context.Request.Certificate.Verify()`.
> Pokud je klientský certifikát podepsaný svým držitelem, musí se napřed API Management [, aby se](api-management-howto-ca-certificates.md) `context.Request.Certificate.Verify()` a `context.Request.Certificate.VerifyNoRevocation()` fungoval.

> [!TIP]
> Problém zablokování klientského certifikátu, který je popsaný v tomto [článku](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) , se může projevit několika způsoby, třeba když jsou požadavky zablokované, `403 Forbidden` stavového kódu po vypršení časového limitu `context.Request.Certificate` je `null`. K tomuto problému obvykle dochází `POST` a `PUT` požadavků s délkou obsahu přibližně 60KB nebo větší.
> Aby nedošlo k tomuto problému, zapněte nastavení vyjednávat klientský certifikát pro požadované názvy hostitelů v okně vlastní domény, jak je znázorněno níže. Tato funkce není k dispozici na úrovni spotřeby.

![Vyjednat klientský certifikát](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Další kroky

-   [Postup zabezpečení back-endové služby pomocí ověřování klientských certifikátů](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Postup nahrání certifikátů](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
