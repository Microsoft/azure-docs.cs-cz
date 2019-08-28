---
title: Zabezpečená rozhraní API pomocí ověřování klientských certifikátů v API Management – Azure API Management | Microsoft Docs
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
ms.date: 05/30/2019
ms.author: apimpm
ms.openlocfilehash: 263f8495b9dbb0a1c5b3c54301b4b4deab425e31
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072367"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Jak zabezpečit rozhraní API pomocí ověřování klientského certifikátu v API Management

API Management poskytuje možnost zabezpečení přístupu k rozhraním API (například klienta k API Management) pomocí klientských certifikátů. Příchozí certifikát a kontrolu vlastností certifikátu můžete ověřit podle požadovaných hodnot pomocí výrazů zásad.

Informace o zabezpečení přístupu k back-endové službě rozhraní API pomocí klientských certifikátů (tj. API Management do back-endu) najdete v tématu [zabezpečení back-endové služby pomocí ověřování klientských certifikátů](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates) .

> [!IMPORTANT]
> Pokud chcete přijímat a ověřovat klientské certifikáty ve vrstvě spotřeby, musíte nejdřív zapnout nastavení požadovat certifikát klienta v okně vlastní domény, jak je znázorněno níže.

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
> Chcete-li zakázat kontrolu použití `context.Request.Certificate.VerifyNoRevocation()` seznamu odvolaných certifikátů `context.Request.Certificate.Verify()`namísto.
> Pokud je klientský certifikát podepsaný svým držitelem, musí být do API Management a `context.Request.Certificate.VerifyNoRevocation()` pro `context.Request.Certificate.Verify()` práci [odesílány](api-management-howto-ca-certificates.md) kořenové (nebo zprostředkující) certifikáty certifikační autority.

## <a name="checking-the-thumbprint"></a>Kontrola kryptografického otisku

Níže uvedené zásady se dají nakonfigurovat tak, aby kontrolovaly kryptografický otisk klientského certifikátu:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Chcete-li zakázat kontrolu použití `context.Request.Certificate.VerifyNoRevocation()` seznamu odvolaných certifikátů `context.Request.Certificate.Verify()`namísto.
> Pokud je klientský certifikát podepsaný svým držitelem, musí být do API Management a `context.Request.Certificate.VerifyNoRevocation()` pro `context.Request.Certificate.Verify()` práci [odesílány](api-management-howto-ca-certificates.md) kořenové (nebo zprostředkující) certifikáty certifikační autority.

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
> Chcete-li zakázat kontrolu použití `context.Request.Certificate.VerifyNoRevocation()` seznamu odvolaných certifikátů `context.Request.Certificate.Verify()`namísto.
> Pokud je klientský certifikát podepsaný svým držitelem, musí být do API Management a `context.Request.Certificate.VerifyNoRevocation()` pro `context.Request.Certificate.Verify()` práci [odesílány](api-management-howto-ca-certificates.md) kořenové (nebo zprostředkující) certifikáty certifikační autority.

> [!TIP]
> Problém zablokování klientského certifikátu popsaný v tomto [článku](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) se může projevit v několika ohledech, například `403 Forbidden` `context.Request.Certificate` když se požadavky zablokují, požadavky jsou `null`po vypršení časového limitu na stavový kód. Tento problém obvykle ovlivňuje `POST` a `PUT` požadavky s délkou obsahu přibližně 60KB nebo větší.
> Pokud se chcete tomuto problému vyhnout, zapněte v okně vlastní domény nastavení vyjednávat klientský certifikát pro požadované názvy hostitelů, jak je znázorněno níže. Tato funkce není k dispozici na úrovni spotřeby.

![Vyjednat klientský certifikát](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Další postup

-   [Postup zabezpečení back-endové služby pomocí ověřování klientských certifikátů](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Postup nahrání certifikátů](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
