---
title: Kurz konfigurace Azure Active Directory B2C pomocí Jumio
titleSuffix: Azure AD B2C
description: V tomto kurzu nakonfigurujete Azure Active Directory B2C s Jumio pro ověřování pomocí automatického ID a zabezpečení zákaznických dat.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: e344c849a8e9021daea9caebacec3289b99d03e6
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256663"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>Kurz pro konfiguraci Jumio s využitím Azure Active Directory B2C

V tomto ukázkovém kurzu poskytujeme pokyny, jak integrovat Azure Active Directory B2C (Azure AD B2C) s [Jumio](https://www.jumio.com/). Jumio je ověřovací služba ID, která umožňuje automatizované ověřování ID v reálném čase, které vám pomůže ochránit zákaznická data.

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, budete potřebovat:

- Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).

- [Klienta Azure AD B2C](./tutorial-create-tenant.md) , který je propojený s vaším předplatným Azure.

## <a name="scenario-description"></a>Popis scénáře

Integrace Jumio zahrnuje tyto komponenty:

- Azure AD B2C: autorizační Server, který je zodpovědný za ověření přihlašovacích údajů uživatele. Označuje se také jako zprostředkovatel identity.

- Jumio: služba, která přebírá podrobnosti ID poskytnuté uživatelem a ověřuje je.

- Zprostředkující rozhraní REST API: rozhraní API, které implementuje integraci mezi Azure AD B2C a službou Jumio.

- Azure Blob Storage: služba, která poskytuje vlastní soubory uživatelského rozhraní do zásad Azure AD B2C.

V následujícím diagramu architektury se zobrazuje implementace.

![Diagram architektury Azure AD B2C integrace s Jumio.](./media/partner-jumio/jumio-architecture-diagram.png)

|Krok | Description |
|:-----| :-----------|
| 1. | Uživatel dostane na stránku, ať už se přihlásí, nebo se zaregistruje a vytvoří účet. Azure AD B2C shromažďuje atributy uživatele.
| 2. | Azure AD B2C volá rozhraní API prostřední vrstvy a předá ho atributům uživatele.
| 3. | Rozhraní API pro střední vrstvu shromažďuje atributy uživatelů a transformuje je do formátu, který může Jumio API spotřebovat. Pak pošle atributy do Jumio.
| 4. | Jakmile Jumio tyto informace zpracuje a zpracuje, vrátí výsledek do prostřední vrstvy API.
| 5. | Rozhraní API pro střední vrstvu zpracovává informace a pošle zpátky relevantní informace pro Azure AD B2C.
| 6. | Azure AD B2C přijímá informace zpátky z rozhraní API střední vrstvy. Pokud se zobrazí reakce na selhání, zobrazí se uživateli chybová zpráva. Pokud se zobrazí odpověď úspěšnosti, je uživatel ověřen a zapsán do adresáře.

## <a name="sign-up-with-jumio"></a>Zaregistrujte se pomocí Jumio

Pokud chcete vytvořit účet Jumio, kontaktujte [Jumio](https://www.jumio.com/contact/).

## <a name="configure-azure-ad-b2c-with-jumio"></a>Konfigurace Azure AD B2C pomocí Jumio

Po vytvoření účtu Jumio použijte účet ke konfiguraci Azure AD B2C. Následující části popisují proces v pořadí.

### <a name="deploy-the-api"></a>Nasazení rozhraní API

Nasaďte poskytnutý [kód rozhraní API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api) do služby Azure. Kód můžete publikovat ze sady Visual Studio pomocí následujících [pokynů](/visualstudio/deployment/quickstart-deploy-to-azure).

>[!NOTE]
>Abyste mohli Azure AD nakonfigurovat pomocí požadovaných nastavení, budete potřebovat adresu URL nasazené služby.

### <a name="deploy-the-client-certificate"></a>Nasazení klientského certifikátu

1. Klientský certifikát pomáhá chránit volání rozhraní Jumio API. Pomocí následujícího ukázkového kódu PowerShellu vytvořte certifikát podepsaný svým držitelem:

   ``` PowerShell
   $cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
   $cert.Thumbprint
   $pwdText = "Your password"
   $pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
   Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

   ```

   Certifikát se pak vyexportuje do umístění určeného pro ``{your-local-path}`` .

3. Importujte certifikát do Azure App Service podle pokynů v [tomto článku](../app-service/configure-ssl-certificate.md#upload-a-private-certificate).

### <a name="create-a-signingencryption-key"></a>Vytvoření podpisového nebo šifrovacího klíče

Vytvoří náhodný řetězec s délkou větší než 64 znaků, který obsahuje pouze písmena a číslice.

Příklad: ``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

K vytvoření řetězce použijte následující skript prostředí PowerShell:

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="configure-the-api"></a>Konfigurace rozhraní API

[Nastavení aplikace můžete nakonfigurovat v Azure App Service](../app-service/configure-common.md#configure-app-settings). Pomocí této metody můžete bezpečně nakonfigurovat nastavení bez jejich rezervace do úložiště. Pro rozhraní REST API musíte zadat následující nastavení:

| Nastavení aplikace | Zdroj | Poznámky |
| :-------- | :------------| :-----------|
|JumioSettings:AuthUsername | Konfigurace účtu Jumio |     |
|JumioSettings:AuthPassword | Konfigurace účtu Jumio |     |
|AppSettings: SigningCertThumbprint|Kryptografický otisk vytvořeného certifikátu podepsaného svým držitelem|  |
|AppSettings: IdTokenSigningKey| Podpisový klíč vytvořený pomocí PowerShellu | |
| AppSettings: IdTokenEncryptionKey |Šifrovací klíč vytvořený pomocí PowerShellu
| AppSettings: IdTokenIssuer | Vystavitel, který se má použít pro token JWT (upřednostňuje se hodnota identifikátoru GUID) |
| AppSettings: IdTokenAudience  | Cílová skupina, která se má použít pro token JWT (upřednostňuje se hodnota identifikátoru GUID) |
|AppSettings: BaseRedirectUrl | Základní adresa URL zásad Azure AD B2C | https://{your-tenant-Name}. b2clogin. com/{ID aplikace}|
| WEBSITE_LOAD_CERTIFICATES| Kryptografický otisk vytvořeného certifikátu podepsaného svým držitelem |

### <a name="deploy-the-ui"></a>Nasazení uživatelského rozhraní

1. Nastavte [kontejner úložiště objektů BLOB ve svém účtu úložiště](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container).

2. Soubory uživatelského rozhraní uložte ze [složky uživatelského rozhraní](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI) v kontejneru objektů BLOB.

#### <a name="update-ui-files"></a>Aktualizovat soubory uživatelského rozhraní

1. V souborech uživatelského rozhraní přejdete do složky [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue).

2. Otevřete každý soubor HTML.

3. Vyhledejte `{your-ui-blob-container-url}` adresu URL vašeho kontejneru objektů BLOB a nahraďte ji.

4. Vyhledejte `{your-intermediate-api-url}` adresu URL služby zprostředkující API App Service a nahraďte ji.

>[!NOTE]
> Jako osvědčený postup doporučujeme přidat oznámení o souhlasu na stránce kolekce atributů. Upozorněte uživatele, že se informace odesílají do služeb třetích stran pro ověření identity.

### <a name="configure-the-azure-ad-b2c-policy"></a>Konfigurace zásad Azure AD B2C

1. Ve složce policies (zásady) vyberte [zásady Azure AD B2C](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies) .

2. Pomocí [tohoto článku](tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack) si stáhněte [LocalAccounts Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts).

3. Nakonfigurujte zásady pro klienta Azure AD B2C.

>[!NOTE]
>Aktualizujte zadané zásady tak, aby se vztahovaly k vašemu konkrétnímu tenantovi.

## <a name="test-the-user-flow"></a>Testování toku uživatele

1. Otevřete klienta Azure AD B2C. V části **zásady** vyberte **Architektura prostředí identity**.

2. Vyberte dříve vytvořenou **SignUpSignIn**.

3. Vyberte **Spustit tok uživatele** a potom:

   a. V poli **aplikace** vyberte registrovanou aplikaci (ukázka je JWT).

   b. V poli **Adresa URL odpovědi** vyberte **adresu URL pro přesměrování**.

   c. Vyberte **Spustit tok uživatele**.

4. Projděte si tok registrace a vytvořte účet.

5. Po vytvoření atributu uživatele bude služba Jumio volána během toku. Pokud tok není úplný, ověřte, že uživatel není uložen v adresáři.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- [Vlastní zásady v Azure AD B2C](./custom-policy-overview.md)

- [Začínáme s vlastními zásadami v Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
