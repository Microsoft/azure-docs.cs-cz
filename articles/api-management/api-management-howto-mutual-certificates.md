---
title: Zabezpečené back-endové služby pomocí ověřování klientského certifikátu
titleSuffix: Azure API Management
description: Zjistěte, jak zabezpečit back-endové služby pomocí ověřování klientských certifikátů ve správě rozhraní Azure API.
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
ms.openlocfilehash: b0ddf6dda99ee666e3052b5a70e51c7e4208a374
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80347110"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Postup zabezpečení back-endových služeb s využitím ověřování pomocí klientských certifikátů ve službě Azure API Management

Správa rozhraní API umožňuje zabezpečit přístup k back-endové službě rozhraní API pomocí klientských certifikátů. Tato příručka ukazuje, jak spravovat certifikáty v instanci služby Azure API Management na webu Azure Portal. Také vysvětluje, jak nakonfigurovat rozhraní API pro použití certifikátu pro přístup ke službě back-end.

Informace o správě certifikátů pomocí rozhraní API PRO SPRÁVU ROZHRANÍ API najdete v tématu <a href="https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">entita certifikátu REST ROZHRANÍ API pro správu rozhraní Azure API</a>.

## <a name="prerequisites"></a><a name="prerequisites"> </a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tato příručka ukazuje, jak nakonfigurovat instanci služby API Management tak, aby používala ověřování klientských certifikátů pro přístup ke službě back-end pro rozhraní API. Před provedením kroků v tomto článku byste měli mít back-endovou službu nakonfigurovanou pro ověřování klientských certifikátů[(pro konfiguraci ověřování certifikátů ve službě Azure App Service naleznete v tomto článku).][to configure certificate authentication in Azure WebSites refer to this article] Potřebujete přístup k certifikátu a heslo pro jeho nahrání do služby API Management.

## <a name="upload-a-certificate"></a><a name="step1"> </a>Nahrání certifikátu

> [!NOTE]
> Místo nahraného certifikátu můžete použít certifikát uložený ve službě [Azure Key Vault,](https://azure.microsoft.com/services/key-vault/) jak je znázorněno v tomto [příkladu](https://github.com/galiniliev/api-management-policy-snippets/blob/galin/AkvCert/examples/Look%20up%20Key%20Vault%20certificate%20using%20Managed%20Service%20Identity%20and%20call%20backend.policy.xml).

![Přidání klientských certifikátů](media/api-management-howto-mutual-certificates/apim-client-cert-new.png)

Podle následujících kroků nahrajte nový klientský certifikát. Pokud jste ještě nevytvořili instanci služby Správa rozhraní API, přečtěte si kurz [Vytvoření instance služby Správa rozhraní API][Create an API Management service instance].

1. Přejděte na instanci služby Azure API Management na webu Azure Portal.
2. V nabídce **vyberte certifikáty.**
3. Klikněte na tlačítko **+Přidat**.
    ![Přidání klientských certifikátů](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)
4. Vyhledejte certifikát, zadejte jeho ID a heslo.
5. Klikněte na **Vytvořit**.

> [!NOTE]
> Certifikát musí být ve formátu **.pfx.** Certifikáty podepsané svým držitelem jsou povoleny.

Jakmile je certifikát odeslán, zobrazí se v **certifikátech**.  Pokud máte mnoho certifikátů, poznamenejte si kryptografický otisk požadovaného certifikátu, abyste [nakonfigurovali rozhraní API tak, aby používalo klientský certifikát pro ověřování brány][Configure an API to use a client certificate for gateway authentication].

> [!NOTE]
> Chcete-li vypnout ověřování řetězu certifikátů například při použití certifikátu podepsaného svým držitelem, postupujte podle pokynů popsaných v této položce nejčastějších [dotazů](api-management-faq.md#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end).

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Odstranění klientského certifikátu

Chcete-li certifikát odstranit, klepněte na kontextovou nabídku **...** a vedle certifikátu vyberte **Odstranit.**

![Odstranění klientských certifikátů](media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png)

Pokud je certifikát používán rozhraním API, zobrazí se obrazovka s upozorněním. Chcete-li certifikát odstranit, musíte jej nejprve odebrat ze všech bezdrátových směrovačů, která jsou nakonfigurována pro jeho použití.

![Odstranění klientských certifikátů se nepodařilo](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="configure-an-api-to-use-a-client-certificate-for-gateway-authentication"></a><a name="step2"> </a>Konfigurace rozhraní API pro použití klientského certifikátu pro ověřování brány

1. Klikněte na **rozhraní API** z nabídky **Správa rozhraní API** vlevo a přejděte na rozhraní API.
    ![Povolení klientských certifikátů](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. Na kartě **Návrh** klikněte na ikonu tužky v části **Backend.**
3. Změňte **přihlašovací údaje brány** na certifikát **Klienta** a v rozevíracím seznamu vyberte certifikát.
    ![Povolení klientských certifikátů](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Klikněte na **Uložit**.

> [!WARNING]
> Tato změna je účinná okamžitě a volání operací tohoto rozhraní API použije certifikát k ověření na serveru back-end.


> [!TIP]
> Pokud je certifikát určen pro ověřování brány pro back-endovou službu rozhraní API, stane se součástí zásadpro toto rozhraní API a lze jej zobrazit v editoru zásad.

## <a name="self-signed-certificates"></a>Certifikáty podepsané svým držitelem

Pokud používáte certifikáty podepsané svým držitelem, budete muset zakázat ověřování řetězu certifikátů, aby správa rozhraní API mohla komunikovat se systémem back-end. V opačném případě vrátí kód chyby 500. Chcete-li tento konfigurovat, [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) můžete použít (pro [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) nový back-end) nebo (pro existující `-SkipCertificateChainValidation` back-end) Rutiny prostředí PowerShell a nastavit parametr na `True`.

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: https://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps
