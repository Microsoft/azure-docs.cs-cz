---
title: 'Kurz: Integrace Azure Active Directory jednotného přihlašování (SSO) pomocí Maverics identity Orchestrator konektoru SAML | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a konektorem Maverics identity Orchestrator.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: jeedes
ms.openlocfilehash: 31392c1fa3d14d6f1e01a8b302575e9b592e42cd
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183145"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Kurz: Integrace jednotného přihlašování Azure AD s Maverics identity Orchestrator konektorem SAML

Vrstvy poskytují jednoduchý způsob, jak integrovat místní aplikace s Azure Active Directory (Azure AD) pro ověřování a řízení přístupu.

Tento článek vás provede postupem konfigurace Maverics identity Orchestrator na:
* Přírůstkové migrace uživatelů z místního systému identit do služby Azure AD během přihlášení do starší verze místní aplikace.
* Směrovat požadavky na přihlášení z starší verze produktu pro správu přístupu k webu, jako je například CA SiteMinder nebo Oracle Access Manager, do Azure AD.
* Po ověření uživatele vůči službě Azure AD ověřte uživatele k místním aplikacím chráněným pomocí hlaviček protokolu HTTP nebo speciálních souborů cookie relace.

Vrstvy poskytují software, který můžete nasadit místně nebo v cloudu. Pomáhá zjišťovat, připojovat a orchestrovat napříč zprostředkovateli identity a vytvářet distribuovanou správu identit pro hybridní a víceúčelové podniky.

V tomto kurzu se dozvíte, jak migrovat místní webovou aplikaci, která je aktuálně chráněná starší verzí produktu pro správu webového přístupu (CA SiteMinder), aby používala Azure AD k ověřování a řízení přístupu. Zde jsou základní kroky:
1. Nainstalujte Maverics identity Orchestrator.
2. Zaregistrujte podnikovou aplikaci ve službě Azure AD a nakonfigurujte ji tak, aby pro jednotné přihlašování založené na SAML používala konektor kódu Maverics Azure AD SAML pro jednotné přihlašování (SSO).
3. Integrujte Maverics s SiteMinder a uživatelským úložištěm LDAP (Lightweight Directory Access Protocol).
4. Nastavte Trezor klíčů Azure a nakonfigurujte Maverics tak, aby ho používal jako poskytovatele správy tajných kódů.
5. Předvedení migrace uživatelů a abstrakce relace pomocí Maverics k poskytnutí přístupu k místní webové aplikaci Java.

Další pokyny k instalaci a konfiguraci najdete na [webu vrstvy](https://www.strata.io).

## <a name="prerequisites"></a>Požadavky

- Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
- Předplatné Maverics identity Orchestrator s povoleným SSO konektorem SAML. Pokud chcete získat Maverics software, kontaktujte [oddělení prodej](mailto:sales@strata.io).

## <a name="install-maverics-identity-orchestrator"></a>Nainstalovat Maverics identity Orchestrator

Pokud chcete začít s instalací Maverics identity Orchestrator, přečtěte si [pokyny k instalaci](https://www.strata.io).

### <a name="system-requirements"></a>Požadavky na systém
* Podporované operační systémy
  * RHEL 7 nebo novější
  * CentOS 7 nebo novější

* Závislosti
  * systemd

### <a name="installation"></a>Instalace

1. Získejte nejnovější balíček Maverics RedHat Package Manageru (ot./min.). Zkopírujte balíček do systému, do kterého chcete nainstalovat software Maverics.

2. Nainstalujte balíček Maverics a nahraďte název souboru místo `maverics.rpm` .

    `sudo rpm -Uvf maverics.rpm`

3. Po instalaci Maverics se spustí jako služba v rámci `systemd` . Pokud chcete ověřit, jestli je služba spuštěná, spusťte následující příkaz:

    `sudo systemctl status maverics`

Ve výchozím nastavení je Maverics nainstalován v adresáři */usr/local/bin* .

Po instalaci Maverics se v adresáři */etc/maverics* vytvoří výchozí soubor *Maverics. yaml* . Než upravíte konfiguraci tak, aby zahrnovala `workflows` a `connectors` , váš konfigurační soubor bude vypadat takto:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```
## <a name="configuration-options"></a>Možnosti konfigurace
### <a name="version"></a>Verze
`version`Pole deklaruje, která verze konfiguračního souboru se používá. Pokud není zadaná verze, použije se nejnovější verze konfigurace.

```yaml
version: 0.1
```
### <a name="listenaddress"></a>listenAddress
`listenAddress` deklaruje, na které adrese bude produkt Orchestrator naslouchat. Pokud je část hostitele adresy prázdná, bude Orchestrator naslouchat všem dostupným IP adresám jednosměrového a libovolného vysílání v místním systému. Pokud je oddíl port v adrese prázdný, vybere se automaticky číslo portu.

```yaml
listenAddress: ":453"
```
### <a name="tls"></a>TLS

`tls`Pole deklaruje mapu objektů TLS (Transport Layer Security). Objekty TLS mohou být používány konektory a serverem Orchestrator. Všechny dostupné možnosti TLS najdete v dokumentaci k `transport` balíčku.

Microsoft Azure vyžaduje komunikaci přes protokol TLS při použití jednotného přihlašování založeného na SAML. Informace o generování certifikátů najdete na webu, který [je šifrovaný](https://letsencrypt.org/getting-started/).

`maverics`Klíč je vyhrazený pro server Orchestrator. Všechny ostatní klíče jsou k dispozici a lze je použít k vložení objektu TLS do daného konektoru.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```  
### <a name="include-files"></a>Soubory k zahrnutí

Můžete definovat `connectors` a `workflows` vlastní samostatné konfigurační soubory a odkazovat je v souboru *maverics. yaml* pomocí `includeFiles` , pro následující příklad:

```yaml
includeFiles:
  - workflow/sessionAbstraction.yaml
  - connector/AzureAD-saml.yaml
  - connector/siteminder.yaml
  ```

V tomto kurzu se používá jediný konfigurační soubor *maverics. yaml* .

## <a name="use-azure-key-vault-as-your-secrets-provider"></a>Použití Azure Key Vault jako poskytovatele tajných kódů

### <a name="manage-secrets"></a>Správa tajných kódů

Pro načtení tajných kódů se Maverics může integrovat s různými řešeními pro správu tajných kódů. Aktuální integrace zahrnují soubor, trezor Hashicorp a Azure Key Vault. Pokud není zadané žádné řešení pro správu tajných kódů, Maverics výchozí načtení tajných klíčů v prostém textu ze souboru *Maverics. yaml* .

Pokud chcete v konfiguračním souboru *maverics. yaml* deklarovat jako tajný kód, vložte tajnou hodnotu do lomených závorek:

  ```yaml
  connectors:
  - name: AzureAD
    type: AzureAD
    apiToken: <AzureADAPIToken>
    oauthClientID: <AzureADOAuthClientID>
    oauthClientSecret: <AzureADOAuthClientSecret>
  ```

### <a name="load-secrets-from-a-file"></a>Načtení tajných kódů ze souboru

1. Chcete-li načíst tajné kódy ze souboru, přidejte do `MAVERICS_SECRET_PROVIDER` souboru */etc/maverics/maverics.env* proměnnou prostředí pomocí:

   `MAVERICS_SECRET_PROVIDER=secretfile:///<PATH TO SECRETS FILE>`

2. Restartujte službu Maverics spuštěním:

   `sudo systemctl restart maverics`

Obsah souboru *tajných kódů. yaml* může být vyplněn libovolným počtem `secrets` .

```yaml
secrets:
  AzureADAPIToken: aReallyGoodToken
  AzureADOAuthClientID: aReallyUniqueID
  AzureADOAuthClientSecret: aReallyGoodSecret
```
### <a name="set-up-an-azure-key-vault"></a>Nastavení trezoru klíčů Azure

Trezor klíčů Azure můžete nastavit buď pomocí Azure Portal, nebo pomocí Azure CLI.

**Použití webu Azure Portal**
1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. [Vytvořte nový trezor klíčů](../../key-vault/general/quick-create-portal.md).
1. [Přidejte tajné klíče do trezoru klíčů](../../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault).
1. [Registrace aplikace ve službě Azure AD](../develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
1. [Autorizovat aplikaci pro použití tajného klíče](../../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault).

**Použití Azure CLI**

1. Otevřete rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli)a potom zadejte následující příkaz:

    ```azurecli
    az login
    ```

1. Vytvořte nový trezor klíčů spuštěním následujícího příkazu:
    ```azurecli
    az keyvault create --name "[VAULT_NAME]" --resource-group "[RESOURCE_GROUP]" --location "[REGION]"
    ```

1. Přidejte tajné klíče do trezoru klíčů spuštěním následujícího příkazu:
    ```azurecli
    az keyvault secret set --vault-name "[VAULT_NAME]" --name "[SECRET_NAME]" --value "[SECRET_VALUE]"
    ```

1. Zaregistrujte aplikaci ve službě Azure AD spuštěním následujícího příkazu:
    ```azurecli
    az ad sp create-for-rbac -n "MavericsKeyVault" --skip-assignment > azure-credentials.json
    ```

1. Autorizovat aplikaci pro použití tajného klíče spuštěním následujícího příkazu:
    ```azurecli
    az keyvault set-policy --name "[VAULT_NAME]" --spn [APPID] --secret-permissions list get
    #APPID can be found in the azure-credentials.json
    generated in the previous step
    ```

1. Pokud chcete načíst tajné kódy z trezoru klíčů Azure, nastavte proměnnou prostředí `MAVERICS_SECRET_PROVIDER` v souboru */etc/maverics/maverics.env* pomocí přihlašovacích údajů, které najdete v *azure-credentials.js* v souboru, v následujícím formátu:
 
   `MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

1. Restartujte službu Maverics: `sudo systemctl restart maverics`

## <a name="configure-your-application-in-azure-ad-for-saml-based-sso"></a>Konfigurace aplikace ve službě Azure AD pro jednotné přihlašování založené na SAML

1. V tenantovi služby Azure AD klikněte na **podnikové aplikace**, vyhledejte **konektor Maverics identity Orchestrator** a pak ho vyberte.

1. V podokně **vlastností** konektoru Maverics identity Orchestrator, nastavte **přiřazení uživatele jako požadované?** na **ne** , aby aplikace mohla fungovat pro nově migrované uživatele.

1. V podokně **Přehled** konektoru Maverics identity Orchestrator Orchestrator vyberte **nastavit jednotné přihlašování** a pak vyberte **SAML**.

1. V podokně přihlášení Maverics identity Orchestrator konektor SAML s odkazem na **bázi SAML** upravte **základní konfiguraci SAML** tak, že vyberete tlačítko **Upravit** (ikona tužky).

   ![Snímek obrazovky s tlačítkem pro úpravu základní konfigurace SAML](common/edit-urls.png)

1. Zadejte **ID entity** zadáním adresy URL v následujícím formátu: `https://<SUBDOMAIN>.maverics.org` . ID entity musí být v rámci aplikací v tenantovi jedinečné. Uložte zde zadanou hodnotu, která bude součástí konfigurace Maverics.

1. Zadejte **adresu URL odpovědi** v následujícím formátu: `https://<AZURECOMPANY.COM>/<MY_APP>/` . 

1. Zadejte **přihlašovací adresu URL** v následujícím formátu: `https://<AZURE-COMPANY.COM>/<MY_APP>/<LOGIN PAGE>` . 

1. Vyberte **Uložit**.

1. V části **podpisový certifikát SAML** vyberte tlačítko **Kopírovat** a zkopírujte **adresu URL federačních metadat aplikace** a pak ji uložte do svého počítače.

    ![Snímek obrazovky s tlačítkem pro kopírování podpisového certifikátu SAML](common/copy-metadataurl.png)

## <a name="configure-maverics-identity-orchestrator-azure-ad-saml-connector"></a>Konfigurace Maverics identity Orchestrator Azure AD SAML Connector

Maverics identity Orchestrator Azure AD Connector podporuje OpenID Connect a SAML Connect. Pro konfiguraci konektoru postupujte takto: 

1. Pro povolení jednotného přihlašování založeného na SAML nastavte `authType: saml` .

1. Vytvořte hodnotu pro `samlMetadataURL` v následujícím formátu: `samlMetadataURL:https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>` .

1. Zadejte adresu URL, na kterou bude Azure přesměrován zpět do vaší aplikace po přihlášení uživatelů pomocí svých přihlašovacích údajů Azure. Použijte následující formát: `samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>` .

1. Zkopírujte hodnotu z dříve nakonfigurovaného EntityID: `samlEntityID: https://<SUBDOMAIN>.maverics.org` .

1. Zkopírujte hodnotu z adresy URL odpovědi, kterou bude služba Azure AD používat k odeslání odpovědi SAML: `samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>` .

1. Vygenerujte podpisový klíč JSON Web Token (JWT), který se používá k ochraně informací o relaci Orchestrator identity Maverics pomocí [nástroje OpenSSL](https://www.openssl.org/source/):

    ```console 
    openssl rand 64 | base64
    ```
1. Zkopírujte odpověď do `jwtSigningKey` konfigurační vlastnosti: `jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==` .

## <a name="attributes-and-attribute-mapping"></a>Mapování atributů a atributů
Mapování atributů se používá k definování mapování uživatelských atributů ze zdrojového adresáře místního uživatele do tenanta služby Azure AD po nastavení uživatele.

Atributy určují, která uživatelská data mohou být vrácena do aplikace v deklaraci identity, předána do souborů cookie relace nebo předána aplikaci v proměnných hlaviček protokolu HTTP.

## <a name="configure-the-maverics-identity-orchestrator-azure-ad-saml-connector-yaml-file"></a>Konfigurace Maverics identity Orchestrator Azure AD SAML konektoru YAML

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

## <a name="migrate-users-to-an-azure-ad-tenant"></a>Migrace uživatelů do tenanta Azure AD

Pomocí této konfigurace můžete postupně migrovat uživatele z produktu pro správu webového přístupu, jako je například certifikační autorita SiteMinder, Oracle Access Manager nebo IBM Tivoli. Můžete je také migrovat z adresáře LDAP (Lightweight Directory Access Protocol) nebo databáze SQL.

### <a name="configure-your-application-permissions-in-azure-ad-to-create-users"></a>Konfigurace oprávnění aplikace v Azure AD pro vytváření uživatelů

1. V tenantovi služby Azure AD klikněte na `App registrations` a vyberte aplikaci **konektoru Maverics identity Orchestrator** .

1. V **konektoru Maverics identity Orchestrator pro identitu | Certifikáty & tajných klíčů** , vyberte `New client secret` a vyberte možnost při vypršení platnosti. Kliknutím na tlačítko **Kopírovat** Zkopírujte tajný klíč a uložte ho do svého počítače.

1. V **konektoru Maverics identity Orchestrator pro identitu | V podokně oprávnění rozhraní API** vyberte **Přidat oprávnění** a potom v podokně **oprávnění API pro žádosti** vyberte oprávnění **Microsoft Graph** a **aplikace**. 

1. Na další obrazovce vyberte **User. celoobrazovkového. All** a pak vyberte **Přidat oprávnění**. 

1. Zpátky v podokně **oprávnění rozhraní API** vyberte **udělit souhlas správce**.

### <a name="configure-the-maverics-identity-orchestrator-saml-connector-yaml-file-for-user-migration"></a>Konfigurace souboru YAML konektoru Maverics identity Orchestrator pro migraci uživatelů

Pokud chcete povolit pracovní postup migrace uživatelů, přidejte tyto další vlastnosti do konfiguračního souboru:
1. Zadejte **adresu URL Azure graphu** v následujícím formátu: `graphURL: https://graph.microsoft.com` .
1. Zadejte **adresu URL tokenu OAuth** v následujícím formátu: `oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>` .
1. Zadejte dříve generovaný tajný klíč klienta v následujícím formátu: `oauthClientSecret: <CLIENT SECRET>` .


Konečný konfigurační soubor konektoru Azure AD pro Maverics identity Orchestrator bude vypadat takto:

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

### <a name="configure-maverics-zero-code-connector-for-siteminder"></a>Konfigurace konektoru Maverics s nulovým kódem pro SiteMinder

Pomocí konektoru SiteMinder migrujete uživatele do tenanta služby Azure AD. Přihlásíte uživatele do starší verze místních aplikací, které jsou chráněny pomocí SiteMinder, pomocí nově vytvořených identit a přihlašovacích údajů služby Azure AD.

Pro tento kurz je SiteMinder nakonfigurovaný na ochranu starší verze aplikace pomocí ověřování založeného na formulářích a `SMSESSION` souboru cookie. Aby bylo možné integrovat s aplikací, která využívá ověřování a informace o relacích pomocí hlaviček protokolu HTTP, je nutné přidat do konektoru konfiguraci emulace hlaviček.

Tento příklad mapuje `username` atribut na `SM_USER` hlavičku protokolu http:

```yaml
  headers:
    SM_USER: username
```

Nastavte `proxyPass` na umístění, na které jsou požadavky proxy. Obvykle je toto umístění hostitelem chráněné aplikace.

`loginPage` by se měl shodovat s adresou URL formuláře, který se aktuálně používá v SiteMinder při přesměrování uživatelů na ověřování.

```yaml
connectors:
- name: siteminder-login-form
  type: siteminder
  loginType: form
  loginPage: /siteminderagent/forms/login.fcc
  proxyPass: http://host.company.com
```

### <a name="configure-maverics-zero-code-connector-for-ldap"></a>Konfigurace konektoru pro Maverics s nulovým kódem pro LDAP

Pokud jsou aplikace chráněny produktem pro správu webového přístupu (WAM), jako je například SiteMinder, uživatelské identity a atributy jsou obvykle uloženy v adresáři LDAP.

Tato konfigurace konektoru ukazuje, jak se připojit k adresáři LDAP. Konektor je nakonfigurovaný jako úložiště uživatele pro SiteMinder, aby se mohly shromáždit správné informace o profilu uživatele během migrace pracovního postupu a aby se odpovídající uživatel mohl vytvořit v Azure AD.

* `baseDN` Určuje umístění v adresáři, proti kterému bude provedeno hledání LDAP.

* `url` je adresa a port serveru LDAP, ke kterému se má připojit.

* `serviceAccountUsername` je uživatelské jméno, které se používá pro připojení k serveru LDAP, obvykle vyjádřené jako rozlišující název vazby (například `CN=Directory Manager` ).

* `serviceAccountPassword` je heslo, které se používá pro připojení k serveru LDAP. Tato hodnota je uložená v dřív nakonfigurované instanci trezoru klíčů Azure.  

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

### <a name="configure-the-migration-workflow"></a>Konfigurace pracovního postupu migrace

Konfigurace pracovního postupu migrace určuje, jak Maverics migruje uživatele z SiteMinder nebo LDAP do Azure AD.

Tento pracovní postup:
- Používá konektor SiteMinder k proxy přihlášení SiteMinder. Přihlašovací údaje uživatele se ověřují prostřednictvím ověřování SiteMinder a pak se předají do dalších kroků pracovního postupu.
- Načte atributy profilu uživatele z úložiště uživatele SiteMinder.
- Vytvoří požadavek na rozhraní Microsoft Graph API, aby vytvořil uživatele v tenantovi Azure AD.

Pokud chcete nakonfigurovat pracovní postup migrace, udělejte toto:

1. Dejte pracovnímu postupu název (třeba **migrace SiteMinder do Azure AD**).
1. Zadejte `endpoint` , což je cesta http, na které je pracovní postup vystavený, který aktivuje tento `actions` pracovní postup v reakci na požadavky. `endpoint`Obvykle odpovídá aplikaci, která je proxy serverem (například `/my_app` ). Hodnota musí zahrnovat úvodní i koncové lomítko.
1. Přidejte `actions` k pracovnímu postupu vhodné.

   a. Definujte `login` metodu pro konektor SiteMinder. Hodnota konektoru se musí shodovat s hodnotou názvu v konfiguraci konektoru.

   b. Definujte `getprofile` metodu pro konektor LDAP.

   c.  Definujte `createuser` metodu pro konektor AzureAD.

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

1. Pokud služba Maverics ještě není spuštěná, spusťte ji spuštěním následujícího příkazu: 

   `sudo systemctl start maverics`

1. Přejít na adresu URL přihlášení k proxy, `http://host.company.com/my_app` .
1. Zadejte přihlašovací údaje uživatele, které se použijí pro přihlášení k aplikaci, když jsou chráněné pomocí SiteMinder.
4. Přejít na **domácí**  >  **uživatele | Všichni uživatelé** ověří, jestli je uživatel vytvořený v Tenantovi Azure AD.  

### <a name="configure-the-session-abstraction-workflow"></a>Konfigurace pracovního postupu abstrakce relace

Pracovní postup abstrakce relace přesune ověřování a řízení přístupu pro starší verzi místní webové aplikace do tenanta Azure AD.

Konektor Azure používá `login` metodu pro přesměrování uživatele na adresu URL pro přihlášení za předpokladu, že žádná relace neexistuje.

Po ověření se token relace vytvořený jako výsledek předává do Maverics. Metoda konektoru SiteMinder `emulate` slouží k emulaci relace založené na souborech cookie nebo relaci založené na hlavičkách a pak žádost upraví o všechny další atributy, které aplikace požaduje.

1. Zadejte název pracovního postupu (například **abstrakce relace SiteMinder**).
1. Zadejte `endpoint` , který odpovídá aplikaci, která je proxy. Hodnota musí zahrnovat úvodní i koncové lomítko (například `/my_app/` ).
1. Přidejte `actions` k pracovnímu postupu vhodné.

   a. Definujte `login` metodu pro Azure Connector. `connector`Hodnota se musí shodovat s `name` hodnotou v konfiguraci konektoru.

   b. Definujte `emulate` metodu pro konektor SiteMinder.

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

1. Přejít na adresu URL aplikace s proxy serverem, `https://<AZURECOMPANY.COM>/<MY_APP>` . 
    
    Budete přesměrováni na přihlašovací stránku s připojeným proxy serverem.

1. Zadejte přihlašovací údaje uživatele Azure AD.

   Měli byste být přesměrováni na aplikaci, jako by byla ověřena přímo pomocí SiteMinder.
