---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování pomocí nástroje Maverics identity Orchestrator SAML Connector | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a konektorem Maverics identity Orchestrator.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9cad791f-8746-4584-bf4e-e281b709fb2b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e923e051116b70b2db35d1ac710a40941305aae
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214303"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maverics-identity-orchestrator-saml-connector"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s nástrojem Maverics identity Orchestrator konektor SAML

## <a name="introduction"></a>Úvod

Vrstvy poskytují jednoduchý způsob, jak integrovat místní aplikace se službou Azure AD za účelem ověřování a řízení přístupu.

Tato příručka vás seznámí s postupem konfigurace Maverics identity Orchestrator &trade; na:
* Přírůstkové migrace uživatelů z místního systému identit do služby Azure AD během přihlášení do starší verze místní aplikace.
* Směrovat žádosti o přihlášení ze starší verze produktu pro správu webového přístupu, jako je například CA SiteMinder nebo Oracle Access Manager, do Azure AD.
* Po ověření uživatele vůči službě Azure AD ověřte uživatele k místním aplikacím, které jsou chráněné pomocí hlaviček protokolu HTTP nebo speciálních souborů cookie relace.

Vrstvy poskytují software, který se nasazuje místně nebo v cloudu za účelem zjišťování, připojování a orchestrace napříč poskytovateli identity za účelem vytvoření distribuované správy identit pro hybridní a víceúčelové podniky.

V tomto kurzu se dozvíte, jak migrovat místní webovou aplikaci, která je aktuálně chráněná starší verzí produktu pro správu webového přístupu (CA SiteMinder), aby používala Azure AD k ověřování a řízení přístupu.
1. Nainstalovat Maverics identity Orchestrator&trade;
2. Zaregistrujte si podnikovou aplikaci pomocí Azure AD a nakonfigurujte ji tak, aby pro &trade; jednotné přihlašování založené na SAML používala konektor kódu Maverics Azure AD SAML Zero.
3. Integrujte Maverics s SiteMinder a uživatelským úložištěm LDAP.
4. Nastavte Azure Key Vault a nakonfigurujte Maverics tak, aby ho používal jako poskytovatele správy tajných kódů.
5. Předvedení migrace uživatelů a abstrakce relace pomocí Maverics k poskytnutí přístupu k místní webové aplikaci Java.

Další pokyny k instalaci a konfiguraci najdete na stránce https://strata.io/docs

## <a name="prerequisites"></a>Předpoklady

- Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
- Maverics identity Orchestrator Orchestrator Connector – jednotné přihlašování (SSO) s povoleným odběrem. Pokud chcete získat Maverics software, kontaktujte prosím sales@strata.io

## <a name="install-maverics-identity-orchestratortrade"></a>Nainstalovat Maverics identity Orchestrator&trade;

Pokud chcete začít s instalací Maverics identity Orchestrator, přečtěte si pokyny k instalaci v tématu. https://strata.io/docs

## <a name="system-requirements"></a>Požadavky na systém
### <a name="supported-operating-systems"></a>Podporované operační systémy
* RHEL 7 nebo novější
* CentOS 7 nebo novější

### <a name="dependencies"></a>Závislosti
* systemd

## <a name="installation"></a>Instalace

1. Získejte nejnovější balíček Maverics ot./min.. Zkopírujte balíček do systému, do kterého chcete nainstalovat software Maverics.

2. Nainstalujte balíček Maverics a nahraďte název souboru místo `maverics.rpm` .

    `sudo rpm -Uvf maverics.rpm`

3. Po instalaci Maverics se v nástroji spustí jako služba `systemd` . Pokud chcete ověřit, jestli je služba spuštěná, spusťte následující příkaz.

    `sudo systemctl status maverics`

Ve výchozím nastavení je Maverics nainstalován v `/usr/local/bin` adresáři.

Po instalaci Maverics se `maverics.yaml` v adresáři vytvoří výchozí soubor `/etc/maverics` . Než upravíte konfiguraci tak, aby zahrnovala `workflows` a `connectors` , váš konfigurační soubor bude vypadat takto:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```
## <a name="config-options"></a>Možnosti konfigurace
### <a name="version"></a>Verze
`version`Pole deklaruje, která verze konfiguračního souboru se používá. Pokud tento parametr nezadáte, použije se nejnovější verze konfigurace.

```yaml
version: 0.1
```
### <a name="listen-address"></a>Adresa naslouchání
`listenAddress` deklaruje, na které adrese bude Orchestrator naslouchat. Pokud je část hostitel této adresy prázdná, bude Orchestrator naslouchat všem dostupným IP adresám jednosměrového a libovolného vysílání v místním systému. Pokud je oddíl port v adrese prázdný, vybere se automaticky číslo portu.

```yaml
listenAddress: ":453"
```
### <a name="tls"></a>TLS

`tls`Pole deklaruje mapu objektů zabezpečení transportní vrstvy. Objekty TLS mohou být použity konektory i serverem Orchestrator. Všechny dostupné možnosti TLS najdete v dokumentaci k `transport` balíčku.

Microsoft Azure vyžaduje komunikaci přes protokol TLS při použití jednotného přihlašování založeného na SAML, najdete [tady](https://letsencrypt.org/getting-started/) Další informace, které vám pomůžou vygenerovat své certifikáty.

`maverics`Klíč je vyhrazený pro server Orchestrator. Všechny ostatní klíče jsou k dispozici a lze je použít k vložení objektu TLS do daného konektoru.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```  
### <a name="include-files"></a>Zahrnuté soubory

`connectors` a `workflows` lze je definovat ve vlastních, samostatných konfiguračních souborech a odkazovat `maverics.yaml` pomocí `includeFiles` následujícího příkladu.
```yaml
includeFiles:
  - workflow/sessionAbstraction.yaml
  - connector/AzureAD-saml.yaml
  - connector/siteminder.yaml
  ```

V tomto kurzu se používá jediný `maverics.yaml` konfigurační soubor.

## <a name="using-azure-key-vault-as-your-secrets-provider"></a>Použití Azure Key Vault jako poskytovatele tajných kódů

### <a name="secret-management"></a>Správa tajných klíčů

Maverics je možné integrovat s různými řešeními pro správu tajných kódů, aby bylo možné načíst tajné kódy. Aktuální integrace zahrnují soubor, Hashicorp trezor a Azure Key Vault. Pokud není zadané žádné řešení pro správu tajných kódů, Maverics použije výchozí načtení tajných kódů v prostém textu `maverics.yaml` .
Chcete-li deklarovat hodnotu jako tajný kód v `maverics.yaml` konfiguračním souboru, zabalte tajný klíč pomocí lomených závorek:

  ```yaml
  connectors:
  - name: AzureAD
    type: AzureAD
    apiToken: <AzureADAPIToken>
    oauthClientID: <AzureADOAuthClientID>
    oauthClientSecret: <AzureADOAuthClientSecret>
  ```

### <a name="file"></a>Soubor

Chcete-li načíst tajné kódy ze souboru, přidejte `MAVERICS_SECRET_PROVIDER` do souboru proměnnou prostředí  `/etc/maverics/maverics.env` s:

`MAVERICS_SECRET_PROVIDER=secretfile:///<PATH TO SECRETS FILE>`

Pak restartujte službu maverics: `sudo systemctl restart maverics`

`secrets.yaml`Obsah souboru může být vyplněn libovolným počtem `secrets` .
```yaml
secrets:
  AzureADAPIToken: aReallyGoodToken
  AzureADOAuthClientID: aReallyUniqueID
  AzureADOAuthClientSecret: aReallyGoodSecret
```
### <a name="azure-key-vault"></a>Azure Key Vault

Následující kroky ukazují, jak nastavit Azure Key Vault pomocí [Azure Portal](https://portal.azure.com) nebo pomocí [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest):

1. [Přihlaste](https://portal.azure.com) se pomocí Azure Portal nebo použijte příkaz CLI:
    ```shell
    az login
    ```

2. [Vytvořte nový trezor](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault)nebo použijte příkaz CLI:
    ```shell
    az keyvault create --name "[VAULT_NAME]" --resource-group "[RESOURCE_GROUP]" --location "[REGION]"
    ```

3. [Přidejte tajné kódy do Key Vault](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault)nebo použijte příkaz CLI:
    ```shell
    az keyvault secret set --vault-name "[VAULT_NAME]" --name "[SECRET_NAME]" --value "[SECRET_VALUE]"
    ```

4. [Registrace aplikace pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)nebo použití příkazu CLI:
    ```shell
    az ad sp create-for-rbac -n "MavericsKeyVault" --skip-assignment > azure-credentials.json
    ```

5. [Autorizace aplikace pro použití tajného klíče](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault)nebo použití příkazu CLI:
    ```shell
    az keyvault set-policy --name "[VAULT_NAME]" --spn [APPID] --secret-permissions list get
    #APPID can be found in the azure-credentials.json
    generated in the previous step
    ```

Pokud chcete načíst tajné kódy z trezoru klíčů Azure, nastavte `MAVERICS_SECRET_PROVIDER` v souboru proměnnou prostředí `/etc/maverics/maverics.env` s přihlašovacími údaji, které se našly v azure-credentials.jsv souboru, pomocí následujícího vzoru: `MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

Pak restartujte službu maverics: `sudo systemctl restart maverics`

## <a name="configure-your-application-in-azure-ad-for-saml-based-sso"></a>Konfigurace aplikace ve službě Azure AD pro jednotné přihlašování založené na SAML

1. Ve vašem tenantovi Azure Active Directory přejděte na `Enterprise applications` , vyhledejte `Maverics Identity Orchestrator SAML Connector` ho a vyberte.

2. Na ' Maverics identity Orchestrator SAML Connector ' | Na stránce Vlastnosti nastavte `User assignment required?` na ne, aby aplikace mohla fungovat pro nově migrované uživatele.

3. Na ' Maverics identity Orchestrator SAML Connector ' | Na stránce Přehled vyberte `Setup single sign-on` a pak vyberte `SAML` .

4. Na ' Maverics identity Orchestrator SAML Connector ' | Přihlašování založené na SAML, úprava základní konfigurace SAML.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

5. `Entity ID`Zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.maverics.org` . `Entity ID`Musí být jedinečný napříč aplikacemi v tenantovi. Uložte zde zadanou hodnotu, která bude součástí konfigurace Maverics.

6. Nastavte adresu URL odpovědi pomocí následujícího vzoru: `https://<AZURECOMPANY.COM>/<MY_APP>/` . 

7. Nastavte přihlašovací adresu URL pomocí následujícího vzoru: `https://<AZURE-COMPANY.COM>/<MY_APP>/<LOGIN PAGE>` a klikněte na Uložit.

8. Přejděte do části podpisový certifikát SAML a kliknutím na tlačítko Kopírovat zkopírujte adresu URL federačních metadat aplikace a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

## <a name="maverics-identity-orchestrator-azure-ad-saml-connector-configuration"></a>Konfigurace konektoru SAML Azure AD Maverics identity Orchestrator

Maverics identity Orchestrator Azure AD Connector podporuje: 
- OpenID Connect
- SAML Connect 

1. Pro povolení jednotného přihlašování založeného na SAML nastavte `authType: saml` .

1. Vytvořte hodnotu pro `samlMetadataURL` : `samlMetadataURL:https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`

1. Nyní definujte adresu URL, na kterou bude Azure přesměrován zpět do vaší aplikace po přihlášení pomocí přihlašovacích údajů Azure.
`samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>`

1. Zkopírujte hodnotu z EntityID nakonfigurovaného výše: `samlEntityID: https://<SUBDOMAIN>.maverics.org`

1. Zkopírujte hodnotu z adresy URL odpovědi, kterou bude služba Azure AD používat k odeslání odpovědi SAML.
`samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>`

1. Vygenerujte podpisový klíč JWT, který se používá k ochraně &trade; informací o relaci Maverics identity Orchestrator pomocí [nástroje OpenSSL](https://www.openssl.org/source/):

    ```shell 
    openssl rand 64 | base64
    ```
1. Zkopírujte odpověď do `jwtSigningKey` konfigurační vlastnosti: `jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==`

## <a name="attributes-and-attribute-mapping"></a>Mapování atributů a atributů
Mapování atributů se používá k definování mapování uživatelských atributů ze zdrojového adresáře místního uživatele do služby Azure AD při zřizování uživatelů.

Atributy určují uživatelská data, která se mohou vracet do aplikace v deklaraci identity, předaná do souborů cookie relace nebo předaná aplikaci v proměnných hlaviček protokolu HTTP.

## <a name="configure-maverics-identity-orchestrator-azure-ad-saml-connector-yaml"></a>Konfigurace Maverics identity Orchestrator Azure AD SAML Connector YAML

Konfigurace konektoru služby Azure AD pro Maverics identity Orchestrator bude vypadat takto:
```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: <SIGNING KEY>
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="migrate-users-to-azure-ad"></a>Migrace uživatelů do Azure AD

Pomocí této konfigurace můžete postupně migrovat uživatele z produktu pro správu webového přístupu, jako je CA SiteMinder, Oracle Access Manager nebo IBM Tivoli; adresář LDAP; nebo databáze SQL.

## <a name="configure-your-application-permissions-in-azure-ad-to-create-users"></a>Konfigurace oprávnění aplikace v Azure AD pro vytváření uživatelů

1. Ve vašem tenantovi Azure Active Directory přejděte na `App registrations` a vyberte aplikaci Maverics identity Orchestrator SAML Connector.

2. Na ' Maverics identity Orchestrator SAML Connector ' | Certifikáty & tajných kódů, vyberte `New client secret` a vyberte možnost při vypršení platnosti. Kliknutím na tlačítko Kopírovat zkopírujte tajný klíč a uložte ho do svého počítače.

3. Na ' Maverics identity Orchestrator SAML Connector ' | Oprávnění rozhraní API, vyberte a `Add permission` potom v nabídce požádat o oprávnění API vyberte `Microsoft Graph` a pak `Application permissions` . Na další obrazovce vyberte `User.ReadWrite.All` a pak vyberte `Add permissions` . To vás provede zpátky do oprávnění API, které vyberete `Grant admin consent` .


## <a name="configure-the-maverics-identity-orchestrator-saml-connector-yaml-for-user-migration"></a>Konfigurace Maverics identity Orchestrator konektoru SAML YAML pro migraci uživatelů

Pokud chcete povolit pracovní postup migrace uživatelů, přidejte tyto další vlastnosti do konfiguračního souboru:
1. Nastavte adresu URL Azure graphu: `graphURL: https://graph.microsoft.com`
1. Nastavte adresu URL tokenu OAuth za vzor: `oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`
1. Nastavte tajný klíč klienta, který se vygeneroval výše: `oauthClientSecret: <CLIENT SECRET>`


Vaše konečná konfigurace konektoru Azure AD pro Maverics identity Orchestrator bude vypadat takto:
```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  graphURL: https://graph.microsoft.com
  oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/oauth2/v2.0/token
  oauthClientID: <APP ID>
  oauthClientSecret: <NEW CLIENT SECRET>
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="configure-the-maverics-zero-code-connectortrade-for-siteminder"></a>Konfigurace konektoru Maverics s nulovým kódem &trade; pro SiteMinder

Konektor SiteMinder se používá k migraci uživatelů do Azure AD a k přihlašování uživatelů k starším místním aplikacím chráněným pomocí SiteMinder pomocí nově vytvořených identit a přihlašovacích údajů Azure AD.

Pro tento kurz je SiteMinder nakonfigurovaný na ochranu starší verze aplikace pomocí ověřování pomocí formulářů a použití `SMSESSION` souboru cookie. Aby bylo možné integrovat s aplikací, která využívá ověřování a relaci prostřednictvím hlaviček protokolu HTTP, bude nutné přidat do konektoru konfiguraci emulace hlaviček.

Tento příklad mapuje `username` atribut na `SM_USER` hlavičku protokolu http:
```yaml
  headers:
    SM_USER: username
```

Nastavte na `proxyPass` umístění, do kterého se mají požadavky vystavovat proxy. Obvykle je to hostitel chráněné aplikace.

`loginPage` by se měla shodovat s adresou URL přihlašovacího formuláře, který aktuálně používá SiteMinder při přesměrování uživatelů na ověřování.

```yaml
connectors:
- name: siteminder-login-form
  type: siteminder
  loginType: form
  loginPage: /siteminderagent/forms/login.fcc
  proxyPass: http://host.company.com
```

## <a name="configure-the-maverics-zero-code-connectortrade-for-ldap"></a>Konfigurace konektoru pro Maverics s nulovým kódem &trade; pro LDAP

Pokud jsou aplikace chráněny produktem WAM, například SiteMinder, uživatelské identity a atributy jsou obvykle uloženy v adresáři LDAP.

Tato konfigurace konektoru ukazuje, jak se připojit k adresáři LDAP nakonfigurovanému jako úložiště uživatele pro SiteMinder, aby bylo možné shromažďovat správné informace o profilu uživatele v rámci pracovního postupu migrace a aby se v Azure AD mohl vytvořit odpovídající uživatel.

* `baseDN` Určuje umístění v adresáři, proti kterému bude provedeno hledání LDAP.

* `url` je adresa a port serveru LDAP, ke kterému se má připojit.

* `serviceAccountUsername` je uživatelské jméno použité pro připojení k serveru LDAP, obvykle vyjádřené jako rozlišující název vazby, například `CN=Directory Manager` .

* `serviceAccountPassword` heslo použité pro připojení k serveru LDAP. Tato hodnota je uložena v instanci Azure Key Vault nakonfigurovanou dříve.  

* `userAttributes` definuje seznam atributů souvisejících s uživatelem, pro které se má dotazovat. Tyto atributy jsou později namapovány na odpovídající atributy služby Azure AD.

```yaml
- name: company-ldap
  type: ldap
  url: "ldap://ldap.company.com:389"
  baseDN: ou=People,o=company,c=US
  serviceAccountUsername: uid=admin,ou=Admins,o=company,c=US
  serviceAccountPassword: <vaulted-password>
  userAttributes:
    - uid
    - cn
    - givenName
    - sn
    - mail
    - mobile
```

## <a name="configure-the-migration-workflow"></a>Konfigurace pracovního postupu migrace

Konfigurace pracovního postupu migrace určuje, jak bude Maverics migrovat uživatele z SiteMinder/LDAP do Azure AD.

Tento pracovní postup:
- Používá konektor SiteMinder k proxy přihlášení SiteMinder. Přihlašovací údaje uživatele se ověřují prostřednictvím ověřování SiteMinder a pak se předají do dalších kroků pracovního postupu.
- Načte atributy profilu uživatele z úložiště uživatele SiteMinder.
- Vytvoří požadavek na rozhraní Microsoft Graph API, aby vytvořil uživatele v tenantovi Azure AD.

Kroky:
1. Dejte pracovnímu postupu název, třeba migrace SiteMinder do služby Azure AD.
2. Zadejte `endpoint` , což je cesta http, na které je pracovní postup vystaven, který aktivuje pracovní `actions` postup v reakci na požadavky. `endpoint`Obvykle odpovídá aplikaci proxy, např. `/my_app` . Hodnota musí zahrnovat úvodní i koncové lomítko.
3. Přidejte `actions` k pracovnímu postupu vhodné.
    - Definujte `login` metodu pro konektor SiteMinder. Hodnota konektoru se musí shodovat s hodnotou názvu v konfiguraci konektoru.
     - Definujte `getprofile` metodu pro konektor LDAP.
     - Definujte `createuser` pro konektor AzureAD.

    ```yaml
      workflows:
      - name: SiteMinder to Azure AD Migration
        endpoint: /my_app/
        actions:
        - connector: siteminder-login-form
          method: login
        - connector: company-ldap
          method: getprofile
        - connector: AzureAD
          method: createuser
    ```
### <a name="verify-the-migration-workflow"></a>Ověření pracovního postupu migrace

1. Pokud služba Maverics ještě není spuštěná, spusťte ji spuštěním následujícího příkazu: `sudo systemctl start maverics`

2. Přejít na adresu URL přihlášení k proxy: `http://host.company.com/my_app` .
3. Zadejte přihlašovací údaje uživatele, které se použijí k přihlášení k aplikaci při ochraně pomocí SiteMinder.
4. Přejít na domovskou > uživatele | Všichni uživatelé ověří, jestli je váš uživatel vytvořený v tenantovi Azure AD.  

## <a name="configure-the-session-abstraction-workflow"></a>Konfigurace pracovního postupu abstrakce relace

Pracovní postup abstrakce relace přesune ověřování a řízení přístupu pro starší verzi místní webové aplikace do služby Azure AD.

Konektor Azure používá `login` metodu pro přesměrování uživatele na přihlašovací adresu URL, protože žádná relace neexistuje.

Po ověření se token relace vytvořený jako výsledek předává Maverics a metoda konektoru SiteMinder `emulate` se používá k emulaci relace založené na souborech cookie nebo relaci založené na hlavičkách a pak žádost seupraví o všechny další atributy, které aplikace vyžaduje.

1. Dejte pracovnímu postupu název, třeba abstrakce relace SiteMinder.
2. Zadejte `endpoint` , který odpovídá aplikaci proxy. Hodnota musí zahrnovat úvodní i koncové lomítko, např. `/my_app/` .
3. Přidejte `actions` k pracovnímu postupu vhodné.
    - Definujte `login` metodu pro Azure Connector. `connector`Hodnota se musí shodovat s `name` hodnotou v konfiguraci konektoru.
    - Definujte `emulate` metodu pro konektor SiteMinder.

     ```yaml
      - name: SiteMinder Session Abstraction
        endpoint: /my_app/
        actions:
      - connector: azure
        method: login
      - connector: siteminder-login-form
        method: emulate
     ```
### <a name="verify-the-session-abstraction-workflow"></a>Ověřit pracovní postup abstrakce relace

1. Přejít na adresu URL aplikace s proxy serverem: `https://<AZURECOMPANY.COM>/<MY_APP>` . Uživatel bude přesměrován na přihlašovací stránku proxy.
2. Zadejte přihlašovací údaje uživatele Azure AD.
3. Uživatel by měl být přesměrován do aplikace, jako by byl ověřen přímo pomocí SiteMinder.
