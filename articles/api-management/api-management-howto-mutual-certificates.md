---
title: Zabezpečení back-endové služby pomocí ověřování klientských certifikátů
titleSuffix: Azure API Management
description: Přečtěte si, jak zabezpečit back-endové služby pomocí ověřování klientských certifikátů v Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/08/2020
ms.author: apimpm
ms.openlocfilehash: 980d3ca52016c65301ea72e4e669c4bafea4c053
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077179"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Postup zabezpečení back-endových služeb s využitím ověřování pomocí klientských certifikátů ve službě Azure API Management

API Management umožňuje zabezpečený přístup k back-endové službě rozhraní API pomocí klientských certifikátů. V této příručce se dozvíte, jak spravovat certifikáty v instanci služby Azure API Management v Azure Portal. Vysvětluje také, jak nakonfigurovat rozhraní API k použití certifikátu pro přístup k back-endové službě.

Informace o správě certifikátů pomocí REST API API Management najdete v tématu věnovaném <a href="/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">certifikační entitě Azure API Management REST API</a>.

## <a name="prerequisites"></a><a name="prerequisites"> </a>Předpoklady

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

V této příručce se dozvíte, jak nakonfigurovat instanci služby API Management tak, aby pro přístup k back-endové službě pro rozhraní API používala ověřování klientským certifikátem. Než budete postupovat podle kroků v tomto článku, měli byste mít nakonfigurované back-end služby pro ověřování klientským certifikátem ([ke konfiguraci ověřování certifikátů v Azure App Service najdete v tomto článku][to configure certificate authentication in Azure WebSites refer to this article]). K certifikátu potřebujete přístup a heslo pro jeho odeslání do služby API Management.

## <a name="upload-a-certificate"></a><a name="step1"> </a>Odeslat certifikát

> [!NOTE]
> Místo nahraného certifikátu můžete použít certifikát uložený ve službě [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) , jak je znázorněno v tomto [příkladu](https://github.com/galiniliev/api-management-policy-snippets/blob/galin/AkvCert/examples/Look%20up%20Key%20Vault%20certificate%20using%20Managed%20Service%20Identity%20and%20call%20backend.policy.xml).

![Přidat klientské certifikáty](media/api-management-howto-mutual-certificates/apim-client-cert-new.png)

Pomocí následujících kroků Nahrajte nový certifikát klienta. Pokud jste instanci služby API Management ještě nevytvořili, přečtěte si kurz [vytvoření instance služby API Management][Create an API Management service instance].

1. V Azure Portal přejděte do své instance služby Azure API Management.
2. V nabídce vyberte **certifikáty** .
3. Klikněte na tlačítko **+Přidat** .
    ![Snímek obrazovky, který zvýrazní tlačítko + Přidat.](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)
4. Vyhledejte certifikát a zadejte jeho ID a heslo.
5. Klikněte na **Vytvořit** .

> [!NOTE]
> Certifikát musí být ve formátu **. pfx** . Certifikáty podepsané svým držitelem jsou povoleny.

Po nahrání se certifikát zobrazí v **certifikátech** .  Máte-li mnoho certifikátů, poznamenejte si kryptografický otisk požadovaného certifikátu, aby bylo možné [Konfigurovat rozhraní API pro použití certifikátu klienta pro ověřování brány][Configure an API to use a client certificate for gateway authentication].

> [!NOTE]
> Pokud chcete vypnout ověřování řetězu certifikátů při použití, například certifikátu podepsaného svým držitelem, postupujte podle kroků popsaných v této [položce](api-management-faq.md#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)s nejčastějšími dotazy.

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Odstranění klientského certifikátu

Pokud chcete certifikát odstranit, klikněte na místní nabídka **...** a vyberte **Odstranit** vedle certifikátu.

![Odstranit klientské certifikáty](media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png)

Pokud je certifikát používán rozhraním API, zobrazí se obrazovka s upozorněním. Chcete-li certifikát odstranit, je třeba nejprve odebrat certifikát ze všech rozhraní API, která jsou nakonfigurována pro jeho použití.

![Odstranit selhání klientských certifikátů](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="configure-an-api-to-use-a-client-certificate-for-gateway-authentication"></a><a name="step2"> </a>Konfigurace rozhraní API pro použití klientského certifikátu pro ověřování brány

1. V nabídce **API Management** na levé straně klikněte na **rozhraní API** a přejděte na rozhraní API.
    ![Povolit klientské certifikáty](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. Na kartě **Návrh** klikněte na ikonu tužky oddílu **back-end** .
3. Změňte **přihlašovací údaje brány** na **certifikát klienta** a vyberte svůj certifikát z rozevíracího seznamu.
    ![Snímek obrazovky, který ukazuje, kde změnit přihlašovací údaje brány a jak vybrat certifikát.](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Klikněte na **Uložit** .

> [!WARNING]
> Tato změna je okamžitě účinná a volání do provozu tohoto rozhraní API použijí certifikát k ověření na back-end serveru.


> [!TIP]
> Když se pro back-end službu rozhraní API zadá certifikát pro ověření brány, bude se jednat o součást zásady pro toto rozhraní API a dá se zobrazit v editoru zásad.

## <a name="self-signed-certificates"></a>Certifikáty podepsané svým držitelem

Pokud používáte certifikáty podepsané svým držitelem, budete muset zakázat ověření řetězu certifikátů, aby API Management komunikovaly se systémem back-end. V opačném případě bude vrácen kód chyby 500. Pokud to chcete nakonfigurovat, můžete použít [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) (pro nový back-end) nebo [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (pro existující rutiny back-endu) PowerShell a nastavit `-SkipCertificateChainValidation` parametr na `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: ./api-management-caching-policies.md
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps