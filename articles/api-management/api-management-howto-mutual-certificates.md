---
title: Zabezpečený API Management back-end pomocí ověřování klientského certifikátu
titleSuffix: Azure API Management
description: Naučte se spravovat klientské certifikáty a zabezpečené back-end služby pomocí ověřování klientského certifikátu v Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 01/26/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9d198f34abe826af2dce0c1ed9950600d4c7bbd3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812287"
---
# <a name="secure-backend-services-using-client-certificate-authentication-in-azure-api-management"></a>Zabezpečení back-end služeb pomocí ověřování klientských certifikátů v Azure API Management

API Management umožňuje zabezpečený přístup k back-endové službě rozhraní API pomocí klientských certifikátů. V této příručce se dozvíte, jak spravovat certifikáty v instanci služby Azure API Management Service pomocí Azure Portal. Vysvětluje také, jak nakonfigurovat rozhraní API k použití certifikátu pro přístup k back-endové službě.

API Management certifikáty můžete spravovat také pomocí [REST API API Management](/rest/api/apimanagement/2020-06-01-preview/certificate).

## <a name="certificate-options"></a>Možnosti certifikátu

API Management poskytuje dvě možnosti správy certifikátů používaných k zabezpečení přístupu ke službám back-endu:

* Odkazování na certifikát spravovaný v [Azure Key Vault](../key-vault/general/overview.md) 
* Přidat soubor certifikátu přímo do API Management

Doporučuje se použít certifikáty trezoru klíčů, protože pomáhá vylepšit API Management zabezpečení:

* Certifikáty uložené v trezorech klíčů se dají znovu použít napříč službami.
* V certifikátech uložených v trezorech klíčů se dají použít podrobné [zásady přístupu](../key-vault/general/security-features.md#privileged-access) .
* Certifikáty, které jsou v trezoru klíčů aktualizované, se automaticky otočí v API Management. Po aktualizaci v trezoru klíčů se certifikát v API Management aktualizoval do 4 hodin. Certifikát můžete také aktualizovat ručně pomocí Azure Portal nebo prostřednictvím REST API pro správu.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Pokud jste instanci služby API Management ještě nevytvořili, přečtěte si téma [vytvoření instance služby API Management][Create an API Management service instance].
* Měli byste mít nakonfigurované back-end služby pro ověřování klientských certifikátů. Informace o konfiguraci ověřování certifikátů v Azure App Service najdete v [tomto článku][to configure certificate authentication in Azure WebSites refer to this article]. 
* K certifikátu potřebujete přístup a heslo pro správu v trezoru klíčů Azure nebo nahrajte do služby API Management. Certifikát musí být ve formátu **PFX** . Certifikáty podepsané svým držitelem jsou povoleny.

### <a name="prerequisites-for-key-vault-integration"></a>Předpoklady pro integraci trezoru klíčů

1. Postup vytvoření trezoru klíčů najdete v tématu [rychlý Start: vytvoření trezoru klíčů pomocí Azure Portal](../key-vault/general/quick-create-portal.md).
1. Povolte [spravovanou identitu](api-management-howto-use-managed-service-identity.md) přiřazenou systémem nebo uživatelem v instanci API Management.
1. Přiřaďte [zásady přístupu trezoru klíčů](../key-vault/general/assign-access-policy-portal.md) ke spravované identitě s oprávněními k získání a výpisu certifikátů z trezoru. Postup přidání zásady:
    1. Na portálu přejděte do svého trezoru klíčů.
    1. Vyberte **nastavení > zásady přístupu > + přidat zásady přístupu**.
    1. Vyberte **oprávnění certifikátu** a pak vyberte **získat** a **seznam**.
    1. V části **Vybrat objekt zabezpečení** vyberte název prostředku spravované identity. Pokud používáte identitu přiřazenou systémem, je objekt zabezpečení názvem vaší instance API Management.
1. Vytvořte nebo importujte certifikát do trezoru klíčů. Viz [rychlý Start: nastavení a načtení certifikátu z Azure Key Vault pomocí Azure Portal](../key-vault/certificates/quick-create-portal.md).

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-a-key-vault-certificate"></a>Přidat certifikát trezoru klíčů

Viz [předpoklady pro integraci trezoru klíčů](#prerequisites-for-key-vault-integration).

> [!CAUTION]
> Při použití certifikátu trezoru klíčů v API Management buďte opatrní a neodstraňujte certifikát, Trezor klíčů ani spravovanou identitu, která se používá pro přístup k trezoru klíčů.

Postup přidání certifikátu trezoru klíčů do API Management:

1. V [Azure Portal](https://portal.azure.com)přejděte k instanci API Management.
1. V části **zabezpečení** vyberte **certifikáty**.
1. Vyberte **certifikáty**  >  **+ Přidat**.
1. Do **ID** zadejte název svého výběru.
1. V **certifikát** vyberte **Trezor klíčů**.
1. Zadejte identifikátor certifikátu trezoru klíčů nebo zvolte **Vybrat** a vyberte certifikát z trezoru klíčů.
    > [!IMPORTANT]
    > Pokud zadáte identifikátor certifikátu trezoru klíčů sami, ujistěte se, že nemá informace o verzi. V opačném případě se certifikát v API Management po aktualizaci trezoru klíčů automaticky neotočí.
1. V **klientské identitě** vyberte spravovanou identitu přiřazenou systémem nebo existující uživatelem přiřazenou identitu. Naučte se [přidávat nebo upravovat spravované identity ve službě API Management](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > Identita potřebuje oprávnění k získání a výpisu certifikátu z trezoru klíčů. Pokud jste ještě nenakonfigurovali přístup k trezoru klíčů, API Management vás vyzve, aby mohl automaticky konfigurovat identitu s potřebnými oprávněními.
1. Vyberte **Přidat**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-kv.png" alt-text="Přidat certifikát trezoru klíčů":::

## <a name="upload-a-certificate"></a>Odeslat certifikát

Postup nahrání klientského certifikátu do API Management: 

1. V [Azure Portal](https://portal.azure.com)přejděte k instanci API Management.
1. V části **zabezpečení** vyberte **certifikáty**.
1. Vyberte **certifikáty**  >  **+ Přidat**.
1. Do **ID** zadejte název svého výběru.
1. V rámečku **certifikát** vyberte **vlastní**.
1. Procházením vyberte soubor certifikátu. pfx a zadejte jeho heslo.
1. Vyberte **Přidat**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-add.png" alt-text="Nahrát klientský certifikát":::

Po nahrání se certifikát zobrazí v okně **certifikáty** . Máte-li mnoho certifikátů, poznamenejte si kryptografický otisk požadovaného certifikátu, aby bylo možné konfigurovat rozhraní API pro použití certifikátu klienta pro [ověřování brány](#configure-an-api-to-use-client-certificate-for-gateway-authentication).

> [!NOTE]
> Pokud chcete vypnout ověřování řetězu certifikátů, například certifikát podepsaný svým držitelem, postupujte podle kroků popsaných v [certifikátech podepsaných svým držitelem](#self-signed-certificates)dále v tomto článku.

## <a name="configure-an-api-to-use-client-certificate-for-gateway-authentication"></a>Konfigurace rozhraní API pro použití certifikátu klienta pro ověřování brány

1. V [Azure Portal](https://portal.azure.com)přejděte k instanci API Management.
1. V části **rozhraní API** vyberte **rozhraní API**.
1. Vyberte rozhraní API ze seznamu. 
2. Na kartě **Návrh** vyberte v části **back-end** ikonu Editor.
3. V okně **pověření brány** vyberte certifikát **klienta** a z rozevíracího seznamu vyberte svůj certifikát.
1. Vyberte **Uložit**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png" alt-text="Použít klientský certifikát pro ověřování brány":::

> [!CAUTION]
> Tato změna je okamžitě účinná a volání do provozu tohoto rozhraní API použijí certifikát k ověření na serveru back-end.

> [!TIP]
> Když se pro službu back-end služby rozhraní API zadá certifikát pro ověřování brány, bude se jednat o součást zásady pro toto rozhraní API a dá se zobrazit v editoru zásad.

## <a name="self-signed-certificates"></a>Certifikáty podepsané svým držitelem

Pokud používáte certifikáty podepsané svým držitelem, budete muset zakázat ověření řetězu certifikátů pro API Management ke komunikaci s back-end systémem. V opačném případě bude vrácen kód chyby 500. Pokud to chcete nakonfigurovat, můžete použít [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) rutiny prostředí PowerShell (pro nový back-end) nebo [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (pro stávající back-end) a nastavit `-SkipCertificateChainValidation` parametr na `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="delete-a-client-certificate"></a>Odstranění klientského certifikátu

Certifikát odstraníte tak, že ho vyberete a v místní nabídce (**...**) vyberete **Odstranit** .

:::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png" alt-text="Odstranit certifikát":::

> [!IMPORTANT]
> Pokud na certifikát odkazují nějaké zásady, zobrazí se obrazovka s upozorněním. Chcete-li certifikát odstranit, je třeba nejprve odebrat certifikát ze všech zásad, které jsou nakonfigurovány pro jeho použití.

## <a name="next-steps"></a>Další kroky

* [Postup zabezpečení rozhraní API s využitím ověřování pomocí klientských certifikátů ve službě API Management](api-management-howto-mutual-certificates-for-clients.md)
* Přečtěte si o [zásadách v API Management](api-management-howto-policies.md)


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

