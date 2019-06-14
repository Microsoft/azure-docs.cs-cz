---
title: Zabezpečení rozhraní API klienta pomocí certifikátu ověřování ve službě API Management – Azure API Management | Dokumentace Microsoftu
description: Zjistěte, jak zabezpečit přístup k rozhraním API pomocí klientských certifikátů
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: apimpm
ms.openlocfilehash: 5427c4050b6b70c18da7a1899d16e448c41e81c6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427336"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Jak zabezpečit rozhraní API klienta pomocí certifikátu ověřování ve službě API Management

API Management umožňuje zabezpečený přístup k rozhraní API (například klienta do API managementu) pomocí klientských certifikátů. Můžete ověřit příchozí certifikát a zkontrolovat vlastnosti certifikátu na požadované hodnoty pomocí výrazů zásad.

Informace o zabezpečení přístupu ke službě back endové rozhraní API s použitím klientské certifikáty (tj, API Management na back-end), najdete v části [jak ověřování pomocí certifikátů zabezpečit back endovým službám pomocí klienta](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> Přijímat a ověřovat klientské certifikáty na úrovni Consumption musíte nejprve povolit "Žádost o klientský certifikát" nastavení v okně "Vlastní domény", jak je znázorněno níže.

![Žádost o certifikát klienta](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Kontroluje se vydavatel a předmět

Ke kontrole vydavatel a předmět certifikátu klienta lze nakonfigurovat následující zásady:

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
> Kontrola, zda používání seznamu odvolaných certifikátů zakázat `context.Request.Certificate.VerifyNoRevocation()` místo `context.Request.Certificate.Verify()`.
> Pokud je klientský certifikát podepsaný svým držitelem, root (nebo zprostředkující) Certifikační autority musí být [nahráli](api-management-howto-ca-certificates.md) do API managementu pro `context.Request.Certificate.Verify()` a `context.Request.Certificate.VerifyNoRevocation()` pracovat.

## <a name="checking-the-thumbprint"></a>Kontroluje se kryptografický otisk

Zkontrolujte kryptografický otisk klientského certifikátu lze nakonfigurovat následující zásady:

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
> Kontrola, zda používání seznamu odvolaných certifikátů zakázat `context.Request.Certificate.VerifyNoRevocation()` místo `context.Request.Certificate.Verify()`.
> Pokud je klientský certifikát podepsaný svým držitelem, root (nebo zprostředkující) Certifikační autority musí být [nahráli](api-management-howto-ca-certificates.md) do API managementu pro `context.Request.Certificate.Verify()` a `context.Request.Certificate.VerifyNoRevocation()` pracovat.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Kontroluje se kryptografický otisk před certifikáty nahráli do API managementu

Následující příklad ukazuje, jak zkontrolujte kryptografický otisk klientského certifikátu proti certifikátů odeslaných do rozhraní API Management:

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
> Kontrola, zda používání seznamu odvolaných certifikátů zakázat `context.Request.Certificate.VerifyNoRevocation()` místo `context.Request.Certificate.Verify()`.
> Pokud je klientský certifikát podepsaný svým držitelem, root (nebo zprostředkující) Certifikační autority musí být [nahráli](api-management-howto-ca-certificates.md) do API managementu pro `context.Request.Certificate.Verify()` a `context.Request.Certificate.VerifyNoRevocation()` pracovat.

> [!TIP]
> Klient certifikátu zablokování problém popsaný v tomto [článku](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) může projevit několika způsoby, třeba požadavky ukotvit, výsledek žádosti `403 Forbidden` stavový kód po vypršení časového limitu, `context.Request.Certificate` je `null`. Tento problém se obvykle týká `POST` a `PUT` žádosti se délka obsahu přibližně 60 kB nebo větší.
> Aby se zabránilo výskytu tohoto problému zapněte nastavení "Certifikát klienta vyjednat" pro požadovaného názvy hostitelů v okně "Vlastní domény", jak je znázorněno níže. Tato funkce není k dispozici na úrovni Consumption.

![Vyjednávání klientského certifikátu](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Další postup

-   [Jak zabezpečit back endovým službám pomocí klienta ověření certifikátu](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Jak nahrát certifikáty](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
