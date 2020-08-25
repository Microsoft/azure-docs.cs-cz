---
title: Kurz konfigurace Azure Active Directory B2C pomocí Jumio
titleSuffix: Azure AD B2C
description: Kurz konfigurace Azure Active Directory B2C s Jumio pro ověřování automatizovaného ID, zabezpečení zákaznických dat
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 47e06c84eb2e0463b31b7bdea5897ceca4339419
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817419"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>Kurz pro konfiguraci Jumio s využitím Azure Active Directory B2C

V tomto ukázkovém kurzu poskytujeme pokyny pro integraci Azure AD B2C s [Jumio](https://www.jumio.com/). Jumio je ověřovací služba ID, která umožňuje automatické ověřování ID v reálném čase a zabezpečení zákaznických dat.

## <a name="prerequisites"></a>Předpoklady

Abyste mohli začít, budete potřebovat:

- Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).

- [Tenant Azure AD B2C](https://review.docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant). Tenant je propojený s vaším předplatným Azure.

## <a name="scenario-description"></a>Popis scénáře

Integrace Jumio zahrnuje tyto komponenty:

- Azure AD B2C – autorizační Server zodpovědný za ověření přihlašovacích údajů uživatele, označovaný také jako zprostředkovatel identity

- Jumio – služba, která přebírá podrobnosti ID poskytnuté uživatelem a ověřuje je.

- Mezilehlé rozhraní REST API – toto rozhraní API implementuje integraci mezi Azure AD B2C a službou Jumio.

- BLOB Storage – slouží k poskytnutí vlastních souborů uživatelského rozhraní do zásad Azure AD B2C.

V následujícím diagramu architektury se zobrazuje implementace.

![Snímek obrazovky pro jumio – architektura – diagram](./media/partner-jumio/jumio-architecture-diagram.png)

|Krok | Popis |
|:-----| :-----------|
| 1. | Uživatel dorazí na přihlašovací stránku. Uživatelé si vyberou přihlášení a vytvoří nový účet a na stránce se zadají informace. Azure AD B2C shromažďuje atributy uživatele.
| 2. | Azure AD B2C volá rozhraní API střední vrstvy a předá ho atributům uživatele.
| 3. | Rozhraní API prostřední vrstvy shromažďuje atributy uživatelů a transformuje je do formátu, který může Jumio API spotřebovat. Poté po odeslání do Jumio.
| 4. | Jakmile Jumio tyto informace zpracuje a zpracuje, vrátí výsledek do prostřední vrstvy API.
| 5. | Rozhraní API střední vrstvy zpracovává tyto informace a odesílá zpátky relevantní informace pro Azure AD B2C.
| 6. | Azure AD B2C přijímá informace zpět z rozhraní API střední vrstvy. Pokud se zobrazí reakce na selhání, zobrazí se uživateli chybová zpráva. Pokud se zobrazí odpověď úspěšnosti, je uživatel ověřen a zapsán do adresáře.

## <a name="onboard-with-jumio"></a>Zprovoznění s Jumio

1. Pokud chcete vytvořit účet Jumio, kontaktujte [Jumio](https://www.jumio.com/contact/) .

2. Po vytvoření účtu se v konfiguraci rozhraní API použijí informace. Následující části popisují proces.

## <a name="configure-azure-ad-b2c-with-jumio"></a>Konfigurace Azure AD B2C pomocí Jumio

### <a name="part-1---deploy-the-api"></a>Část 1 – nasazení rozhraní API

Nasaďte poskytnutý [kód rozhraní API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api) do služby Azure. Kód lze publikovat ze sady Visual Studio, a to podle těchto [pokynů](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

>[!NOTE]
>Abyste mohli Azure AD nakonfigurovat pomocí požadovaných nastavení, budete potřebovat adresu URL nasazené služby.

### <a name="part-2---deploy-the-client-certificate"></a>Část 2 – nasazení klientského certifikátu

1. Volání rozhraní API Jumio je chráněno klientským certifikátem. Vytvořte certifikát podepsaný svým držitelem pomocí ukázkového kódu PowerShellu uvedeného níže:

``` PowerShell
$cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
$cert.Thumbprint
$pwdText = "Your password"
$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

```

2. Certifikát se pak vyexportuje do umístění zadaného pro { ``your-local-path`` }.

3. Podle pokynů uvedených v tomto [dokumentu](https://docs.microsoft.com/azure/app-service/configure-ssl-certificate#upload-a-private-certificate)importujte certifikát do služby Azure App Service.

### <a name="part-3---create-a-signingencryption-key"></a>Část 3 – Vytvoření podpisového nebo šifrovacího klíče

Vytvořte náhodný řetězec s délkou větší než 64 znaků, který obsahuje pouze abecedy nebo čísla.

Příklad: ``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

Pomocí níže uvedeného skriptu PowerShellu můžete vytvořit alfanumerickou hodnotu o délce 64 znaků.

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="part-4---configure-the-api"></a>Část 4 – Konfigurace rozhraní API

Nastavení aplikace je možné [nakonfigurovat ve službě App Service v Azure](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings). S touto metodou se dají nastavení bezpečně nakonfigurovat bez jejich kontroly do úložiště. Pro rozhraní REST API musíte zadat následující nastavení:

| Nastavení aplikace | Zdroj | Poznámky |
| :-------- | :------------| :-----------|
|JumioSettings:AuthUsername | Konfigurace účtu Jumio |     |
|JumioSettings:AuthPassword | Konfigurace účtu Jumio |     |
|AppSettings: SigningCertThumbprint|Kryptografický otisk vytvořeného certifikátu podepsaného svým držitelem|  |
|AppSettings: IdTokenSigningKey| Podpisový klíč vytvořený pomocí PowerShellu | |
| AppSettings: IdTokenEncryptionKey |Šifrovací klíč vytvořený pomocí PowerShellu
| AppSettings: IdTokenIssuer | Vystavitel, který se má použít pro token JWT (upřednostňuje se hodnota identifikátoru GUID) |
| AppSettings: IdTokenAudience  | Cílová skupina, která se má použít pro token JWT (upřednostňuje se hodnota identifikátoru GUID) |
|AppSettings: BaseRedirectUrl | Základní adresa URL zásady B2C | https://{your-tenant-Name}. b2clogin. com/{ID aplikace}|
| WEBSITE_LOAD_CERTIFICATES| Kryptografický otisk vytvořeného certifikátu podepsaného svým držitelem |

### <a name="part-5---deploy-the-ui"></a>Část 5 – nasazení uživatelského rozhraní

1. Nastavte [kontejner úložiště objektů BLOB ve svém účtu úložiště](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container).

2. Soubory uživatelského rozhraní uložte ze [složky uživatelského rozhraní](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI) do kontejneru objektů BLOB.

#### <a name="update-ui-files"></a>Aktualizovat soubory uživatelského rozhraní

1. V souborech uživatelského rozhraní přejdete do složky [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue)

2. Otevřete každý soubor HTML.

3. Vyhledejte a nahraďte {Your-UI-BLOB-Container-URL} adresou URL kontejneru objektů BLOB.

4. Vyhledejte a nahraďte {The-Intermediate-API-URL} adresou URL služby zprostředkující API App Service.

>[!NOTE]
> Jako osvědčený postup doporučujeme, aby zákazníci přidávají oznámení o souhlasu na stránce kolekce atributů. Upozorněte uživatele, že informace budou odeslány službám třetích stran pro ověření identity.

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>Část 6 – konfigurace zásad Azure AD B2C

1. Ve složce policies (zásady) vyberte [zásady Azure AD B2C](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies) .

2. Pomocí tohoto [dokumentu](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) stáhněte [LocalAccounts Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) .

3. Nakonfigurujte zásady pro klienta Azure AD B2C.

>[!NOTE]
>Aktualizujte zadané zásady tak, aby se vztahovaly k vašemu konkrétnímu tenantovi.

## <a name="test-the-user-flow"></a>Testování toku uživatele

1. Otevřete Azure AD B2C tenant a v části zásady vyberte **Architektura prostředí identity**.

2. Vyberte dříve vytvořenou **SignUpSignIn**.

3. Vyberte **Spustit tok uživatele** a vyberte nastavení:

   a. **Aplikace**: vyberte registrovanou aplikaci (ukázka je JWT).

   b. **Adresa URL odpovědi**: vyberte **adresu URL pro přesměrování** .

   c. Vyberte **Spustit tok uživatele**.

4. Projděte si registrační tok a vytvořte účet.

5. Služba Jumio bude volána během toku po vytvoření atributu uživatele. Pokud tok není úplný, ověřte, že uživatel není uložen v adresáři.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- [Vlastní zásady v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Začínáme s vlastními zásadami v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
