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
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: c3060765022cabcb877041927886b59d6725c7cf
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Jak zabezpečit back endové služby pomocí klienta pro ověřování pomocí certifikátu ve službě Azure API Management
API Management poskytuje možnost zabezpečený přístup ke službě back endové rozhraní API pomocí klientských certifikátů. Tato příručka ukazuje, jak ke správě certifikátů v rozhraní API portálu vydavatele a postup konfigurace rozhraní API používat certifikát pro přístup k jeho back endové službě.

Informace o správě certifikátů pomocí rozhraní API REST API správy najdete v tématu <a href="https://docs.microsoft.com/en-us/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">Azure API Management REST API certifikát entity</a>.

## <a name="prerequisites"> </a>Požadavky
Tento průvodce vám ukáže, jak nakonfigurovat instanci služby API Management používat ověřování pomocí certifikátu klienta pro přístup k službě back-end pro rozhraní API. Před provedením postupu v tomto tématu, byste měli mít služby back-end, který je nakonfigurován pro ověřování pomocí certifikátu klienta ([konfigurace ověřování pomocí certifikátu v weby Azure najdete v tomto článku] [ to configure certificate authentication in Azure WebSites refer to this article]), a mít přístup k certifikátu a heslo pro certifikát pro odesílání v portál vydavatele služby API Management.

## <a name="step1"> </a>Nahrajte certifikát klienta
Začněte tak, že na webu Azure Portal dané služby API Management kliknete na **Portál vydavatele**. Tím přejdete na portál vydavatele služby API Management.

![Portál vydavatele rozhraní API][api-management-management-console]

> Pokud jste instanci služby API Management ještě nevytvořili, přečtěte si téma [vytvoření instance API Management][Create an API Management service instance].
> 
> 

Klikněte na tlačítko **zabezpečení** z **API Management** nabídky na levé straně a klikněte na **klientské certifikáty**.

![Klientské certifikáty][api-management-security-client-certificates]

Chcete-li nahrát nový certifikát, klikněte na tlačítko **nahrání certifikátu**.

![Nahrání certifikátu][api-management-upload-certificate]

Přejděte na svůj certifikát a pak zadejte heslo pro certifikát.

> Certifikát musí být v **.pfx** formátu. Certifikáty podepsané svým držitelem jsou povoleny.
> 
> 

![Nahrání certifikátu][api-management-upload-certificate-form]

Klikněte na tlačítko **nahrát** na kterou odešlete certifikát.

> V tuto chvíli je ověřit heslo certifikátu. Chybová zpráva se zobrazí, pokud je nesprávná.
> 
> 

![Nahraný certifikát][api-management-certificate-uploaded]

Po nahrání certifikátu se zobrazí na **klientské certifikáty** kartě. Pokud máte víc certifikátů, zajistěte, aby poznámku o předmět nebo poslední čtyři znaky kryptografický otisk, které se používají k výběru certifikátu, při konfiguraci rozhraní API používat certifikáty, jak je popsané v následující [konfigurace rozhraní API pro použití klientský certifikát pro ověřování brány] [ Configure an API to use a client certificate for gateway authentication] části.

> Chcete-li vypnout ověřování řetězu certifikátů při použití, například certifikát podepsaný svým držitelem, postupujte podle kroků popsaných v této – nejčastější dotazy [položky](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end).
> 
> 

## <a name="step1a"> </a>Odstranit certifikát klienta
Pokud chcete odstranit certifikát, klikněte na tlačítko **odstranit** vedle požadovaný certifikát.

![Odstranit certifikát][api-management-certificate-delete]

Klikněte na tlačítko **Ano, odstraňte jej** k potvrzení.

![Potvrzení odstranění][api-management-confirm-delete]

Pokud se certifikát používá rozhraní API, se zobrazí obrazovka upozornění. Chcete-li odstranit certifikát nutno nejprve odstranit certifikát z jakékoli rozhraní API, které jsou nakonfigurovány pro použití.

![Potvrzení odstranění][api-management-confirm-delete-policy]

## <a name="step2"> </a>Konfigurace rozhraní API používat klientský certifikát pro ověřování brány
Klikněte na tlačítko **rozhraní API** z **API Management** nabídky na levé straně klikněte na název požadované rozhraní API a klikněte na tlačítko **zabezpečení** kartě.

![Zabezpečení rozhraní API][api-management-api-security]

Vyberte **klientské certifikáty** z **s přihlašovacími údaji** rozevíracího seznamu.

![Klientské certifikáty][api-management-mutual-certificates]

Vyberte požadovaný certifikát z **klientský certifikát** rozevíracího seznamu. Pokud máte víc certifikátů, že můžete se podívat na předmět nebo poslední čtyři znaky kryptografický otisk, jak je uvedeno v předchozí části zjistit správný certifikát.

![Vybrat certifikát][api-management-select-certificate]

Klikněte na tlačítko **Uložit** se uložit změnu konfigurace rozhraní API.

> Tuto změnu je hned platná, a volání operace tohoto rozhraní API bude používat certifikát k ověření na serveru back-end.
> 
> 

![Uložit změny rozhraní API][api-management-save-api]

> Pokud je zadán pro ověřování brány pro back endové službě rozhraní API, se stane součástí zásad pro toto rozhraní API a lze zobrazit v editoru zásad.
> 
> 

![Zásady certifikátu][api-management-certificate-policy]

## <a name="self-signed-certificates"></a>Certifikáty podepsané svým držitelem

Pokud používáte certifikáty podepsané svým držitelem, budete muset zakázat ověřování řetězu certifikátů v pořadí pro API Management ke komunikaci s back-end systému, jinak vrátí chybu 500 kódu. To můžete nakonfigurovat, můžete použít [ `New-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend) (pro nový back-end) nebo [ `Set-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend) (pro existující back-end) rutiny prostředí PowerShell a nastavte `-SkipCertificateChainValidation` parametru `True`.

```
$context = New-AzureRmApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzureRmApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

[api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
[api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
[api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
[api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
[api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
[api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
[api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
[api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
[api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
[api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
[api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
[api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
[api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png



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



