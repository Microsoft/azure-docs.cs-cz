---
title: Zabezpečený back endovým službám pomocí klienta ověření certifikátu – Azure API Management | Dokumentace Microsoftu
description: Informace o zabezpečení back endovým službám pomocí ověření klientského certifikátu ve službě Azure API Management.
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
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 2b0db6f9a1e9cd660ce1b1e7af3e7e1c85815c16
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43045244"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Jak zabezpečit back endovým službám pomocí klienta ověření certifikátu ve službě Azure API Management

API Management umožňuje zabezpečený přístup ke službě back endové rozhraní API pomocí klientských certifikátů. Tato příručka ukazuje, jak spravovat certifikáty v instanci služby Azure API Management na webu Azure Portal. Také vysvětluje postup konfigurace rozhraní API pro použití certifikátu pro přístup k back-end služby.

Informace o správě certifikátů pomocí REST API služby API Management najdete v tématu <a href="https://docs.microsoft.com/en-us/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">entita Azure API Management REST API certifikát</a>.

## <a name="prerequisites"> </a>Požadované součásti

Tato příručka ukazuje, jak nakonfigurovat instanci služby API Management používat ověřování pomocí certifikátu klienta pro přístup k back endovou službu pro rozhraní API. Před provedením kroků v tomto článku, měli byste mít vaše back-end služba nakonfigurovaná pro ověření certifikátu klienta ([ke konfiguraci ověřování certifikátů na Azure WebSites najdete v tomto článku] [ to configure certificate authentication in Azure WebSites refer to this article]). Je nutné použít certifikát a heslo pro jeho odeslání do služby API Management.

## <a name="step1"> </a>Nahrání certifikátu klienta

![Přidat klientské certifikáty](media/api-management-howto-mutual-certificates/apim-client-cert.png)

Postupujte podle následujících kroků, abyste nahrát nový klientský certifikát. Pokud jste instanci služby API Management ještě nevytvořili, projděte si kurz [vytvoření instance služby API Management][Create an API Management service instance].

1. Přejděte k vaší instanci služby Azure API Management na webu Azure Portal.
2. Vyberte **klientské certifikáty** z nabídky.
3. Klikněte na tlačítko **+ přidat** tlačítko.  
    ![Přidat klientské certifikáty](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)  
4. Vyhledejte certifikát, zadejte jeho ID a heslo.  
5. Klikněte na možnost **Vytvořit**.

> [!NOTE]
> Certifikát musí být v **.pfx** formátu. Certifikáty podepsané svým držitelem jsou povoleny.

Jakmile je certifikát nahraný, zobrazí **klientské certifikáty**.  Pokud máte velký počet certifikátů, poznamenejte si kryptografický otisk certifikátu požadované za účelem [konfigurace rozhraní API používat klientský certifikát pro ověřování brány][Configure an API to use a client certificate for gateway authentication].

> [!NOTE]
> Chcete-li vypnout ověřování řetězu certifikátů při použití, například certifikát podepsaný svým držitelem, postupujte podle kroků popsaných v tomto dokumentu [položky](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end).

## <a name="step1a"> </a>Odstranit klientský certifikát

Pokud chcete odstranit certifikát, klikněte na tlačítko kontextové nabídky **...**  a vyberte **odstranit** vedle certifikátu.

![Odstranění klientské certifikáty](media/api-management-howto-mutual-certificates/apim-client-cert-delete.png)

Pokud tento certifikát se používá rozhraní API, se zobrazí obrazovka upozornění. Pokud chcete odstranit certifikát, musíte nejprve odeberte certifikát z libovolné rozhraní API, které jsou nakonfigurovány pro použití.

![Odstranit certifikáty klienta se nezdařilo](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="step2"> </a>Konfigurace rozhraní API používat klientský certifikát pro ověřování brány

1. Klikněte na tlačítko **rozhraní API** z **API Management** nabídky na levé straně a přejděte do rozhraní API.  
    ![Povolit klientské certifikáty](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. V **návrhu** kartu a potom klikněte na ikonu tužky z **back-endu** oddílu. 
3. Změnit **brány pověření** k **klientský certifikát** a z rozevíracího seznamu vyberte svůj certifikát.  
    ![Povolit klientské certifikáty](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Klikněte na **Uložit**. 

> [!WARNING]
> Tato změna je hned platná a volání operace tohoto rozhraní API bude certifikát použít k ověření v back endového serveru.


> [!TIP]
> Pokud certifikát pro ověřování brány pro back-end službu rozhraní API, se stane součástí zásady pro toto rozhraní API a lze je zobrazit v editoru zásad.

## <a name="self-signed-certificates"></a>Certifikáty podepsané svým držitelem

Pokud používáte certifikáty podepsané svým držitelem, je potřeba zakázat ověřování řetězu certifikátů v pořadí pro službu API Management ke komunikaci s back-end systému. V opačném případě vrátí kód 500 – Chyba. Chcete-li nastavit tuto konfiguraci, můžete použít [ `New-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend) (pro nový back-end) nebo [ `Set-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend) (pro existující back-end) rutiny prostředí PowerShell a nastavte `-SkipCertificateChainValidation` parametr `True`.

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
