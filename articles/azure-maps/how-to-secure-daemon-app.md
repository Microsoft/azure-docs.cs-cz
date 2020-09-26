---
title: Jak zabezpečit aplikaci démon
titleSuffix: Azure Maps
description: Pomocí Azure Portal můžete spravovat ověřování a nakonfigurovat tak důvěryhodnou aplikaci démona.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 5af7645db662a238099e013f84b0dc0fee2af62c
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91355852"
---
# <a name="secure-a-daemon-application"></a>Zabezpečení aplikace démona

Následující příručka je určena pro procesy na pozadí, časovače a úlohy, které jsou hostovány v důvěryhodném a zabezpečeném prostředí. Mezi příklady patří webové úlohy Azure, aplikace Azure Functions, služby systému Windows a jakákoli jiná služba Reliable na pozadí.

> [!Tip]
> Microsoft doporučuje implementovat Azure Active Directory (Azure AD) a řízení přístupu na základě role Azure (Azure RBAC) pro produkční aplikace. Přehled konceptů najdete v tématu [Azure Maps Authentication](./azure-maps-authentication.md).

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="scenario-shared-key-authentication"></a>Scénář: ověřování sdíleného klíče

Po vytvoření účtu Azure Maps se vygenerují primární a sekundární klíče. Pokud [ke volání Azure Maps použijete ověřování pomocí sdíleného klíče](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication), doporučujeme použít primární klíč jako klíč předplatného. Sekundární klíč můžete použít ve scénářích, jako je například vracení klíčových změn. Další informace najdete v tématu [ověřování v Azure Maps](https://aka.ms/amauth).

### <a name="securely-store-shared-key"></a>Bezpečně uložit sdílený klíč

Primární a sekundární klíč umožňují autorizaci všem rozhraním API pro účet Maps. Aplikace by měly ukládat klíče v zabezpečeném úložišti, jako je například Azure Key Vault. Aplikace musí načíst sdílený klíč jako Azure Key Vault tajný klíč, aby se zabránilo ukládání sdíleného klíče do konfigurace aplikace ve formátu prostého textu. Informace o tom, jak nakonfigurovat Azure Key Vault, najdete v tématu [Příručka pro vývojáře Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/developers-guide).

Následující kroky popisují tento proces:

1. Vytvořte Azure Key Vault.
2. Vytvořte instanční objekt služby Azure AD tak, že vytvoříte registraci aplikace nebo spravovanou identitu. vytvořený objekt zabezpečení je zodpovědný za přístup k Azure Key Vault.
3. Přiřaďte přístup instančního objektu k oprávnění klíčovým tajným službám Azure `Get` .
4. Dočasně přiřaďte přístup k tajným klíčům `Set` oprávnění pro vás jako vývojář.
5. Nastavte sdílený klíč v tajných klíčích Key Vault a odkazujte tajné ID jako konfiguraci pro aplikaci démona a odeberte oprávnění k tajným klíčům `Set` .
6. Implementujte ověřování Azure AD v aplikaci démona a načtěte tajný klíč sdíleného klíče z Azure Key Vault.
7. Vytvoří Azure Maps REST API žádosti se sdíleným klíčem.

> [!Tip]
> Pokud je aplikace hostovaná v prostředí Azure, měli byste implementovat spravovanou identitu, abyste snížili náklady a složitost správy tajného kódu pro ověřování Azure Key Vault. Pokud [se chcete připojit prostřednictvím spravované identity](https://docs.microsoft.com/azure/key-vault/general/tutorial-net-create-vault-azure-web-app), Projděte si následující kurz Azure Key Vault.

Aplikace démona zodpovídá za načtení sdíleného klíče ze zabezpečeného úložiště. Implementace s Azure Key Vault vyžaduje ověření prostřednictvím služby Azure AD pro přístup ke tajnému kódu. Místo toho doporučujeme přímé ověřování Azure AD pro Azure Maps v důsledku dalších složitosti a provozních požadavků použití ověřování pomocí sdíleného klíče.

> [!IMPORTANT]
> Pro zjednodušení opětovného generování klíčů doporučujeme, aby aplikace používaly vždy jeden klíč. Aplikace potom můžou znovu vygenerovat nepoužitý klíč a nasadit nový znovu vygenerovaný klíč do zabezpečeného úložiště tajného kódu, jako je Azure Key Vault.

## <a name="scenario-azure-ad-role-based-access-control"></a>Scénář: řízení přístupu na základě rolí v Azure AD

Po vytvoření účtu Azure Maps se hodnota Azure Maps nachází na `x-ms-client-id` stránce s podrobnostmi ověřování Azure Portal. Tato hodnota představuje účet, který se bude používat pro REST API požadavky. Tato hodnota by měla být uložena v konfiguraci aplikace a načtena před provedením požadavků HTTP. Cílem scénáře je povolit aplikaci démona ověření ve službě Azure AD a volat Azure Maps rozhraní REST API.

> [!Tip]
> Pro povolení výhod spravovaných komponent identity doporučujeme hostovat na Azure Virtual Machines, Virtual Machine Scale Sets nebo App Services.

### <a name="daemon-hosted-on-azure-resources"></a>Démon hostovaná v prostředcích Azure

Při spuštění v prostředcích Azure nakonfigurujte spravované identity Azure tak, aby umožňovaly nízké náklady, úsilí správy minimálních přihlašovacích údajů. 

V tématu [Přehled spravovaných identit](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) můžete aplikaci povolit přístup ke spravované identitě.

Výhody spravované identity:

* Ověřování kryptografie veřejného klíče certifikátu x509 spravovaného systémem Azure
* Zabezpečení Azure AD s certifikáty x509 místo tajných kódů klienta.
* Azure spravuje a obnovuje všechny certifikáty přidružené k spravovanému prostředku identity.
* Zjednodušená provozní Správa přihlašovacích údajů odebráním jakékoli potřeby zabezpečené služby úložiště tajného kódu, jako je Azure Key Vault. 

### <a name="daemon-hosted-on-non-azure-resources"></a>Démon je hostovaný na prostředcích mimo Azure.

V případě, že nejsou k dispozici spravované identity prostředí mimo Azure, nejsou k dispozici. Proto je nutné nakonfigurovat instanční objekt pomocí registrace aplikace služby Azure AD pro aplikaci démon.

1. V Azure Portal v seznamu služeb Azure vyberte **Azure Active Directory**  >  **Registrace aplikací**  >  **Nová registrace**.  

    > [!div class="mx-imgBorder"]
    > ![Registrace aplikace](./media/how-to-manage-authentication/app-registration.png)

2. Pokud jste svou aplikaci už zaregistrovali, přejděte k dalšímu kroku. Pokud jste svoji aplikaci nezaregistrovali, zadejte **název**, vyberte **typ účtu podpory**a pak vyberte **zaregistrovat**.  

    > [!div class="mx-imgBorder"]
    > ![Podrobnosti registrace aplikace](./media/how-to-manage-authentication/app-create.png)

3. Pokud chcete přiřadit Azure Maps oprávnění k delegovanému rozhraní API, Projděte si aplikaci. Pak v části **Registrace aplikací**vyberte **oprávnění rozhraní API**  >  **Přidat oprávnění**. V části **rozhraní API moje organizace používá**, vyhledejte a vyberte **Azure Maps**.

    > [!div class="mx-imgBorder"]
    > ![Přidat oprávnění rozhraní API pro aplikace](./media/how-to-manage-authentication/app-permissions.png)

4. Zaškrtněte políčko vedle pole přístup k **Azure Maps**a pak vyberte **Přidat oprávnění**.

    > [!div class="mx-imgBorder"]
    > ![Výběr oprávnění rozhraní API pro aplikace](./media/how-to-manage-authentication/select-app-permissions.png)

5. Provedením následujících kroků vytvořte tajný klíč klienta nebo nakonfigurujte certifikát.

    * Pokud vaše aplikace používá ověřování serveru nebo aplikace, přejít na registrační stránku aplikace na **certifikáty & tajných**kódů. Pak buď Nahrajte certifikát veřejného klíče, nebo vytvořte heslo tak, že vyberete **nový tajný klíč klienta**.

        > [!div class="mx-imgBorder"]
        > ![Vytvoření tajného klíče klienta](./media/how-to-manage-authentication/app-keys.png)

    * Po výběru **Přidat**Zkopírujte tajný klíč a bezpečně ho uložte do služby, jako je Azure Key Vault. Přečtěte si [Azure Key Vault příručka pro vývojáře](https://docs.microsoft.com/azure/key-vault/general/developers-guide) a bezpečně uložte certifikát nebo tajný kód. Tento tajný klíč použijete k získání tokenů z Azure AD.

        > [!div class="mx-imgBorder"]
        > ![Přidat tajný klíč klienta](./media/how-to-manage-authentication/add-key.png)

### <a name="grant-role-based-access-for-the-daemon-application-to-azure-maps"></a>Udělení přístupu na základě role pro aplikaci démona k Azure Maps

*Řízení přístupu na základě role Azure (Azure RBAC)* udělíte tak, že do jedné nebo víc Azure Maps definic rolí přiřadíte vytvořenou spravovanou identitu nebo instanční objekt. Pokud chcete zobrazit definice rolí Azure, které jsou k dispozici pro Azure Maps, přejděte na **řízení přístupu (IAM)**. Vyberte **role**a potom vyhledejte role, které začínají na *Azure Maps*. Tyto role Azure Maps jsou role, kterým můžete udělit přístup.

> [!div class="mx-imgBorder"]
> ![Zobrazit dostupné role](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

1. Přejít na **účet Azure Maps**. Vyberte **Řízení přístupu (IAM)** > **Přiřazení rolí**.

    > [!div class="mx-imgBorder"]
    > ![Udělení přístupu pomocí Azure RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Na kartě **přiřazení rolí** **přidejte** přiřazení role. 

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s vybraným doplňkem zobrazí změny přiřazení.](./media/how-to-manage-authentication/add-role-assignment.png)

3. Vyberte předdefinované definice role Azure Maps, například **Azure Maps data Reader** nebo **Přispěvatel dat Azure Maps**. V části **přiřadit přístup k**vyberte možnost **uživatel, skupina nebo instanční objekt služby Azure AD** s přiřazenou **User assigned managed identity**  /  **spravovanou identitou**přiřazenou uživatelem spravované identity. Vyberte objekt zabezpečení. Potom vyberte **Uložit**.

    > [!div class="mx-imgBorder"]
    > ![Postup přidání přiřazení role](./media/how-to-manage-authentication/how-to-add-role-assignment.png)

4. Na kartě přiřazení role můžete potvrdit použití přiřazení role.

## <a name="request-token-with-managed-identity"></a>Požádat o token se spravovanou identitou

Po nakonfigurování spravované identity pro hostující prostředek použijte sadu Azure SDK nebo REST API k získání tokenu pro Azure Maps, další informace najdete v tématu [získání přístupového tokenu](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token). Po této příručce se očekává, že se vrátí přístupový token, který se dá použít u REST APIch požadavků.

## <a name="request-token-with-application-registration"></a>Požádat o token s registrací aplikace

Po registraci aplikace a jejím přidružení k Azure Maps můžete požádat o přístupové tokeny.

* ID prostředku Azure AD `https://atlas.microsoft.com/`
* ID Aplikace Azure AD
* ID klienta Azure AD
* Tajný kód klienta pro registraci Aplikace Azure AD

Požadavek:

```http
POST /<Azure AD Tenant ID>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<Azure AD App ID>&resource=https://atlas.microsoft.com/&client_secret=<client secret>&grant_type=client_credentials
```

Odpověď:

```json
{
    "token_type": "Bearer",
    "expires_in": "...",
    "ext_expires_in": "...",
    "expires_on": "...",
    "not_before": "...",
    "resource": "https://atlas.microsoft.com/",
    "access_token": "ey...gw"
}
```

Podrobnější příklady najdete v tématu [scénáře ověřování pro Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="next-steps"></a>Další kroky

Najděte metriky využití API pro váš účet Azure Maps:
> [!div class="nextstepaction"]
> [Zobrazení metrik využití](how-to-view-api-usage.md)

Prozkoumejte ukázky, které ukazují, jak integrovat Azure AD s Azure Maps:
> [!div class="nextstepaction"]
> [Ukázky Azure Maps](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
