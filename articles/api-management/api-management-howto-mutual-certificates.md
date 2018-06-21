---
title: Back endové služby pomocí klienta Secure certifikát ověřování – Azure API Management | Microsoft Docs
description: Zjistěte, jak zabezpečit back endové služby pomocí ověření klientského certifikátu ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 844a7ea1c2dd8f7dbb4984fc148575529ac154db
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36292853"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Jak zabezpečit back endové služby pomocí klienta pro ověřování pomocí certifikátu ve službě Azure API Management

Správa API umožňuje zabezpečený přístup ke službě back endové rozhraní API pomocí klientských certifikátů. Tato příručka ukazuje, jak spravovat certifikáty v instanci služby Azure API Management na portálu Azure. Také vysvětluje, jak nakonfigurovat rozhraní API používat certifikát pro přístup k back endové službě.

Informace o správě certifikátů pomocí rozhraní API REST API správy najdete v tématu <a href="https://docs.microsoft.com/en-us/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">Azure API Management REST API certifikát entity</a>.

## <a name="prerequisites"> </a>Požadavky

Tento průvodce vám ukáže, jak nakonfigurovat instanci služby API Management používat ověřování pomocí certifikátu klienta pro přístup k službě back-end pro rozhraní API. Před provedením postupu v tomto článku, měli byste služby back-end, který je nakonfigurován pro ověřování pomocí certifikátu klienta ([konfigurace ověřování pomocí certifikátu v weby Azure najdete v tomto článku] [ to configure certificate authentication in Azure WebSites refer to this article]). Je nutné použít certifikát a heslo pro odesílání ve službě API Management.

## <a name="step1"> </a>Nahrajte certifikát klienta

![Přidat klientské certifikáty](media/api-management-howto-mutual-certificates/apim-client-cert.png)

Postupujte podle následujících kroků a nahrát nový klientský certifikát. Pokud jste dosud instanci služby API Management ještě nevytvořili, najdete v části kurzu [vytvoření instance API Management][Create an API Management service instance].

1. Přejděte na instanci služby Azure API Management na portálu Azure.
2. Vyberte **klientské certifikáty** z nabídky.
3. Klikněte **+ přidat** tlačítko.  
    ![Přidat klientské certifikáty](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)  
4. Vyhledejte certifikát, zadejte jeho ID a heslo.  
5. Klikněte na možnost **Vytvořit**.

> [!NOTE]
> Certifikát musí být v **.pfx** formátu. Certifikáty podepsané svým držitelem jsou povoleny.

Po nahrání certifikátu se zobrazí v **klientské certifikáty**.  Pokud máte velký počet certifikátů, poznamenejte si kryptografický otisk certifikátu požadované za účelem [konfigurace rozhraní API používat klientský certifikát pro ověřování brány][Configure an API to use a client certificate for gateway authentication].

> [!NOTE]
> Chcete-li vypnout ověřování řetězu certifikátů při použití, například certifikát podepsaný svým držitelem, postupujte podle kroků popsaných v této – nejčastější dotazy [položky](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end).

## <a name="step1a"> </a>Odstranit certifikát klienta

Pokud chcete odstranit certifikát, klikněte na tlačítko kontextovou nabídku **...**  a vyberte **odstranit** vedle certifikátu.

![Odstranění klientské certifikáty](media/api-management-howto-mutual-certificates/apim-client-cert-delete.png)

Pokud se certifikát používá rozhraní API, se zobrazí obrazovka upozornění. Pokud chcete odstranit certifikát, je nutno nejprve odstranit certifikát z všechny rozhraní API, které jsou nakonfigurovány pro použití.

![Odstraňte certifikáty klienta se nezdařilo](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="step2"> </a>Konfigurace rozhraní API používat klientský certifikát pro ověřování brány

1. Klikněte na tlačítko **rozhraní API** z **API Management** nabídky na levé straně a přejděte do rozhraní API.  
    ![Povolit klientské certifikáty](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. V **návrhu** klikněte na ikonu tužky z **back-end** části. 
3. Změna **přihlašovací údaje brány** k **klientského certifikátu** a vyberte certifikát z rozevíracího seznamu.  
    ![Povolit klientské certifikáty](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Klikněte na **Uložit**. 

> [!WARNING]
> Tuto změnu je hned platná, a volání operace tohoto rozhraní API bude používat certifikát k ověření na serveru back-end.


> [!TIP]
> Pokud je zadán pro ověřování brány pro back endové službě rozhraní API, se stane součástí zásad pro toto rozhraní API a lze zobrazit v editoru zásad.

## <a name="self-signed-certificates"></a>Certifikáty podepsané svým držitelem

Pokud používáte certifikáty podepsané svým držitelem, musíte zakázat ověřování řetězu certifikátů v pořadí pro API Management ke komunikaci s back-end systému. V opačném případě vrátí kód chybu 500. To můžete nakonfigurovat, můžete použít [ `New-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend) (pro nový back-end) nebo [ `Set-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend) (pro existující back-end) rutiny prostředí PowerShell a nastavte `-SkipCertificateChainValidation` parametru `True`.

```
$context = New-AzureRmApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzureRmApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
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

[Azure API Management REST API Certificate entity]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps
