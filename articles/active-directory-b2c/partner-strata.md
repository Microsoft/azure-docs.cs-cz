---
title: Kurz konfigurace Azure Active Directory B2C s využitím vrstev
titleSuffix: Azure AD B2C
description: Naučte se integrovat Azure AD B2C ověřování pomocí whoIam pro ověřování uživatelů.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c7f7f162355b919c395dd0ee6d03b2bc5526e3da
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96936688"
---
# <a name="tutorial-for-extending-azure-ad-b2c-to-protect-on-premises-applications-using-strata"></a>Kurz pro rozšíření Azure AD B2C k ochraně místních aplikací pomocí vrstev

V tomto ukázkovém kurzu se dozvíte, jak integrovat Azure Active Directory (AD) B2C s [Maverics identity Orchestrator](https://www.strata.io/maverics-identity-orchestrator/)pro vrstvy.
Maverics identity Orchestrator rozšiřuje Azure AD B2C k ochraně místních aplikací. Připojuje se k jakémukoli systému identit, transparentně migruje uživatele a přihlašovací údaje, synchronizuje zásady a konfigurace a abstrakce ověřování a správu relací. Použití vrstev v podnicích dokáže rychle přejít ze starší verze na Azure AD B2C bez nutnosti přepisovat aplikace. Toto řešení má následující výhody:

- **Zákaznické jednotné Sign-On (SSO) k místním hybridním aplikacím**: Azure AD B2C podporuje jednotné přihlašování zákazníka s Maverics identity Orchestrator. Uživatelé se přihlásí pomocí účtů, které jsou hostovány v Azure AD B2C nebo IdP (sociální identity Provider). Maverics rozšiřuje jednotné přihlašování do aplikací, které byly historicky zabezpečeny staršími systémy identit, jako je Symantec SiteMinder.

- **Rozšiřování jednotného přihlašování do aplikací bez nutnosti jejich psaní**: pomocí Azure AD B2C můžete spravovat přístup uživatelů a povolit jednotné přihlašování pomocí konektorů Maverics identity Orchestrator SAML nebo OIDC.

- **Snadná konfigurace**: Azure AD B2C poskytuje jednoduché podrobné uživatelské rozhraní pro připojení konektorů Maverics identity Orchestrator nebo OIDC k Azure AD B2C.

## <a name="prerequisites"></a>Předpoklady

Abyste mohli začít, budete potřebovat:

- Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).

- [Klienta Azure AD B2C](./tutorial-create-tenant.md) , který je propojený s vaším předplatným Azure.

- Instance [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro ukládání tajných kódů, které používá Maverics identity Orchestrator Slouží k připojení k Azure AD B2C nebo jiným poskytovatelům atributů, jako je například adresář protokolu LDAP (Lightweight Directory Access Protocol) nebo databáze.

- Instance [Maverics identity Orchestrator](https://www.strata.io/maverics-identity-orchestrator/) , která je nainstalovaná a spuštěná na virtuálním počítači Azure nebo na místním serveru podle vašeho výběru. Informace o tom, jak získat software a získat přístup k dokumentaci pro instalaci a konfiguraci, najdete v tématu [o kontaktech](https://www.strata.io/contact/)

- Místní aplikace, kterou provedete přechodem ze starší verze systému identity na Azure AD B2C.

## <a name="scenario-description"></a>Popis scénáře

Integrace Maverics do vrstev zahrnuje následující součásti:

- **Azure AD B2C**: autorizační Server, který je zodpovědný za ověření přihlašovacích údajů uživatele. Ověření uživatelé můžou k místním aplikacím přistupovat pomocí místního účtu uloženého v adresáři Azure AD B2C.

- **Externí IDP nebo Enterprise**: může to být libovolný poskytovatel OpenID Connect, Facebook, Google nebo GitHub. Přečtěte si informace o použití [externích zprostředkovatelů identity](./technical-overview.md#external-identity-providers) s Azure AD B2C.  

- **Maverics identity Orchestrator**: služba, která orchestruje přihlašování uživatelů a transparentně předává identity aplikacím prostřednictvím hlaviček protokolu HTTP.

V následujícím diagramu architektury se zobrazuje implementace.

![Obrázek – zobrazení architektury Azure AD B2C integrace s Mavericsy pro povolení přístupu k hybridním aplikacím](./media/partner-strata/strata-architecture-diagram.png)

| Postup | Popis |
|:-------|:---------------|
| 1. | Uživatel vytvoří žádost o přístup k místní hostované aplikaci. Maverics identity Orchestrator proxy vyžádá požadavek, který uživatel odeslal do aplikace.|
| 2. | Nástroj Orchestrator zkontroluje stav ověřování uživatele. Pokud neobdrží token relace nebo poskytnutý token relace není platný, pošle uživatel, aby Azure AD B2C k ověření.|
| 3. | Azure AD B2C odešle požadavek na ověření do nakonfigurovaného společenského IdPu.|
| 4. | IdP vyzve uživatele k zadání přihlašovacích údajů. V závislosti na IdP může uživatel vyžadovat službu Multi-Factor Authentication (MFA).|
| 5. | IdP odesílá odpověď ověřování zpět na Azure AD B2C. V případě potřeby může uživatel během tohoto kroku vytvořit místní účet v adresáři Azure AD B2C.|
| 6. | Azure AD B2C odešle požadavek uživatele na koncový bod zadaný během registrace aplikace Orchestrator v tenantovi Azure AD B2C.|
| 7. | Nástroj Orchestrator vyhodnotí zásady přístupu a vypočte hodnoty atributů, které se mají zahrnout do hlaviček HTTP předaných do aplikace. Během tohoto kroku může nástroj Orchestrator zavolat na další poskytovatele atributů a načíst informace potřebné k tomu, aby byly správně nastaveny hodnoty hlaviček. Nástroj Orchestrator nastaví hodnoty hlaviček a odešle požadavek do aplikace.|
| 8. | Uživatel je teď ověřený a má přístup k aplikaci.|

## <a name="get-maverics-identity-orchestrator"></a>Získat Maverics identity Orchestrator
Pokud chcete získat software, který budete používat k integraci své starší verze místní aplikace s Azure AD B2C, obraťte se na [vrstvy](https://www.strata.io/contact/). Po získání softwaru postupujte podle následujících kroků a určete požadavky na produkt Orchestrator a proveďte požadované kroky instalace a konfigurace.

## <a name="configure-your-azure-ad-b2c-tenant"></a>Konfigurace tenanta Azure AD B2C

1. **Registrace aplikace**

   a. [Zaregistrujte nástroj Orchestrator jako aplikaci](./tutorial-register-applications.md?tabs=app-reg-ga) v tenantovi Azure AD B2C.
   >[!Note]
   >Po nakonfigurování instance Orchestrator budete potřebovat název tenanta a identifikátor, ID klienta, tajný klíč klienta, nakonfigurované deklarace identity a identifikátor URI přesměrování později.

   b. Udělte aplikacím Microsoft MS Graph API oprávnění. Vaše aplikace bude potřebovat následující oprávnění: `offline_access` , `openid` .

   c. Přidejte identifikátor URI pro přesměrování vaší aplikace. Tento identifikátor URI se bude shodovat s `oauthRedirectURL` parametrem konfigurace konektoru Azure AD B2C Orchestrator, například `https://example.com/oidc-endpoint` .

2. **Vytvoření toku uživatele**: Vytvořte [uživatelský tok pro registraci a přihlašování](./tutorial-create-user-flows.md).

3. **Přidat IDP**: vyberte, abyste se přihlásili ke svému uživateli pomocí místního účtu nebo společenského nebo podnikového [IdPu](./tutorial-add-identity-providers.md).

4. **Definovat atributy uživatele**: definujte atributy, které mají být shromážděny při registraci.

5. **Zadat deklarace identity aplikace**: Určete atributy, které se mají vrátit do aplikace přes instanci nástroje Orchestrator. Nástroj Orchestrator využívá atributy z deklarací vrácených Azure AD B2C a může načítat další atributy z jiných systémů s připojenými identitami, jako jsou adresáře a databáze LDAP. Tyto atributy se nastavují v hlavičkách HTTP a odesílají do nadřazené místní aplikace.

## <a name="configure-maverics-identity-orchestrator"></a>Konfigurace Maverics identity Orchestrator

V následujících částech Vás provedeme kroky potřebnými ke konfiguraci vaší instance nástroje Orchestrator. Další technickou podporu a dokumentaci získáte na [úrovní](https://www.strata.io/contact/)kontaktů.

### <a name="maverics-identity-orchestrator-server-requirements"></a>Požadavky na server Maverics identity Orchestrator

Instanci nástroje Orchestrator můžete spustit na jakémkoli serveru, ať už místní, nebo ve veřejné cloudové infrastruktuře, jako je Azure, AWS nebo GCP.

- Operační systém: REHL 7,7 nebo vyšší, CentOS 7 +

- Disk: 10 GB (malý)

- Paměť: 16 GB

- Porty: 22 (SSH/SCP), 443, 80

- Přístup ke kořenu pro úlohy instalace nebo správy

- Maverics identity Orchestrator se spouští jako `maverics` uživatel. `systemd`

- Odchozí přenos dat ze serveru hostujícího Maverics identity Orchestrator s možností přístupu ke klientovi Azure AD.

### <a name="install-maverics-identity-orchestrator"></a>Nainstalovat Maverics identity Orchestrator

1. Získejte nejnovější balíček Maverics ot./min.. Balíček umístěte do systému, na který chcete nainstalovat Maverics. Pokud kopírujete soubor na vzdáleného hostitele, je užitečným nástrojem [SCP](https://www.ssh.com/ssh/scp/) .

2. Chcete-li nainstalovat balíček Maverics, spusťte následující příkaz, který nahradí název souboru místo `maverics.rpm` .

   `sudo rpm -Uvf maverics.rpm`

   Ve výchozím nastavení je Maverics nainstalován v `/usr/local/bin` adresáři.

3. Po instalaci Maverics se v nástroji spustí jako služba `systemd` .  Pokud chcete ověřit, jestli je spuštěná služba Maverics, spusťte následující příkaz:

   `sudo service maverics status`

  Pokud byla instalace produktu Orchestrator úspěšná, měla by se zobrazit zpráva podobná této:

```
Redirecting to /bin/systemctl status maverics.service
  maverics.service - Maverics
  Loaded: loaded (/etc/systemd/system/maverics.service; enabled; vendor preset: disabled)
  Active: active (running) since Thu 2020-08-13 16:48:01 UTC; 24h ago
  Main PID: 330772 (maverics)
  Tasks: 5 (limit: 11389)
  Memory: 14.0M
  CGroup: /system.slice/maverics.service
          └─330772 /usr/local/bin/maverics --config /etc/maverics/maverics.yaml
  ```

4. Pokud se nepovede spustit službu Maverics, prozkoumejte problém spuštěním následujícího příkazu:

   `journalctl --unit=maverics.service --reverse`

   Nejnovější položka protokolu se zobrazí na začátku výstupu.

Po instalaci Maverics se `maverics.yaml` v adresáři vytvoří výchozí soubor `/etc/maverics` .

Nakonfigurujte nástroj Orchestrator, aby chránil aplikaci. Integraci s Azure AD B2C, ukládáním a načítáním tajných kódů z [Azure Key Vault](https://azure.microsoft.com/services/key-vault/?OCID=AID2100131_SEM_bf7bdd52c7b91367064882c1ce4d83a9:G:s&ef_id=bf7bdd52c7b91367064882c1ce4d83a9:G:s&msclkid=bf7bdd52c7b91367064882c1ce4d83a9). Definujte umístění, ze kterého má nástroj Orchestrator načíst konfiguraci.

### <a name="supply-configuration-using-environment-variables"></a>Zadání konfigurace pomocí proměnných prostředí

Poskytněte konfiguraci pro instance nástroje Orchestrator prostřednictvím proměnných prostředí.

`MAVERICS_CONFIG`

Tato proměnná prostředí oznamuje instanci nástroje Orchestrator, která YAML konfigurační soubory, které se mají použít, a umístění, kde je lze najít při spuštění nebo restartování. Nastavte proměnnou prostředí v `/etc/maverics/maverics.env` .

### <a name="create-the-orchestrators-tls-configuration"></a>Vytvoření konfigurace TLS nástroje Orchestrator

`tls`Pole v `maverics.yaml` deklaruje konfigurace zabezpečení transportní vrstvy, které vaše instance nástroje Orchestrator použije. Konektory můžou používat objekty TLS a Orchestrator Server.

`maverics`Klíč je vyhrazený pro server Orchestrator. Všechny ostatní klíče jsou k dispozici a lze je použít k vložení objektu TLS do daného konektoru.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```

### <a name="configure-the-azure-ad-b2c-connector"></a>Konfigurace konektoru Azure AD B2C

Orchestrace používá konektory pro integraci s ověřováním a zprostředkovateli atributů. V tomto případě tato orchestrace brány aplikace používá konektor Azure AD B2C jako poskytovatele ověřování i atributu. Azure AD B2C používá ke ověřování IdP sociální sítě a potom funguje jako poskytovatel atributu pro nástroj Orchestrator, který předává atributy v deklaracích nastavených v hlavičkách protokolu HTTP.  

Tato konfigurace konektoru odpovídá aplikaci zaregistrovanou v klientovi Azure AD B2C.

1. Zkopírujte ID klienta, tajný klíč a identifikátor URI pro přesměrování z konfigurace aplikace ve vašem tenantovi.

2. Zadejte název konektoru, který je zobrazený jako `azureADB2C` a nastavte konektor `type` na `azure` . Poznamenejte si název konektoru, protože tato hodnota se používá v dalších parametrech konfigurace níže.

3. Pro tuto integraci `authType` by měl být nastaven na `oidc` .

4. Nastavte ID klienta, které jste zkopírovali v kroku 1, jako hodnotu `oauthClientID` parametru.

5. Nastavte tajný klíč klienta, který jste zkopírovali v kroku 1, jako hodnotu `oauthClientSecret` parametru.

6. Nastavte identifikátor URI pro přesměrování, který jste zkopírovali v kroku 1, jako hodnotu `oauthRedirectURL` parametru.

7. Konektor Azure AD B2C OIDC používá známý OIDC koncový bod pro zjišťování metadat, včetně adres URL a podpisových klíčů. Nastavte hodnotu `oidcWellKnownURL` na koncový bod vašeho tenanta.

```yaml
connectors:
  name: azureADB2C
  type: azure
  oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
  oauthRedirectURL: https://example.com/oidc-endpoint
  oauthClientID: <azureADB2CClientID>
  oauthClientSecret: <azureADB2CClientSecret>
  authType: oidc
```

### <a name="define-azure-ad-b2c-as-your-authentication-provider"></a>Definování Azure AD B2C jako poskytovatele ověřování

Zprostředkovatel ověřování určuje, jak provést ověřování pro uživatele, který nepodal platnou relaci jako součást požadavku prostředku aplikace. Konfigurace ve vašem tenantovi Azure AD B2C určuje, jak se má uživatel vystavit za přihlašovací údaje a použít další zásady ověřování. Pokud například chcete vyžadovat druhý faktor k dokončení procesu ověřování a rozhodnout, které deklarace identity se mají vrátit do brány aplikace Orchestrator po úspěšném ověření.

Hodnota pro `authProvider` musí odpovídat hodnotě vašeho konektoru `name` .

```yaml
authProvider: azureADB2C
```

### <a name="protect-your-on-premises-app-with-an-orchestrator-app-gateway"></a>Ochrana místní aplikace pomocí služby Orchestrator App Gateway

Konfigurace služby Application Gateway nástroje Orchestrator deklaruje, jak Azure AD B2C chránit vaši aplikaci a jak mají uživatelé k aplikaci přistupovat.

1. Vytvořte název pro vaši bránu aplikace. Jako identifikátor vaší aplikace můžete použít popisný název nebo plně kvalifikovaný název hostitele.

2. Nastavte `location` . Tento příklad používá kořen aplikace `/` , ale může to být libovolná cesta URL vaší aplikace.

3. Definujte chráněnou aplikaci v `upstream` používání hostitele: konvence portu: `https://example.com:8080` .

4. Nastavte hodnoty pro chybové a neoprávněné stránky.

5. Definujte názvy hlaviček protokolu HTTP a hodnoty atributů, které musí aplikace poskytnout, aby bylo možné navázat ověřování a řídit přístup k aplikaci. Názvy hlaviček jsou libovolné a obvykle odpovídají konfiguraci aplikace. Hodnoty atributu jsou v oboru názvů konektorem, který je dodává. V následujícím příkladu jsou hodnoty vrácené z Azure AD B2C předpony s názvem konektoru, `azureADB2C` kde přípona je název atributu, který obsahuje požadovanou hodnotu, například `given_name` .

6. Nastavte zásady, které se mají vyhodnotit a vymáhat. Jsou definovány tři akce: `allowUnauthenticated` , a `allowAnyAuthenticated` `allowIfAny` . Každá akce je přidružena k `resource` a a zásada je vyhodnocena `resource` .

>[!NOTE]
>Obojí `headers` a `policies` pomocí rozšíření služby JavaScript nebo GoLang implementujte libovolnou logiku, která významně vylepšuje výchozí možnosti.

```yaml
appgateways:
  - name: Sonar
    location: /
    upstream: https://example.com:8080
    errorPage: https://example.com:8080/sonar/error
    unauthorizedPage: https://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

### <a name="use-azure-key-vault-as-your-secrets-provider"></a>Použití Azure Key Vault jako poskytovatele tajných kódů

Je důležité zabezpečit tajné klíče, které nástroj Orchestrator používá pro připojení k Azure AD B2C a jakýmkoli jiným systémům identity. Maverics bude `maverics.yaml` v tomto kurzu jako poskytovatel tajných kódů Azure Key Vault používat výchozí načtení tajných kódů do prostého textu.

Podle pokynů [vytvořte novou Key Vault](../key-vault/secrets/quick-create-portal.md) , kterou vaše instance Orchestrator použije jako poskytovatele tajných kódů. Přidejte svoje tajná klíč do trezoru a poznamenejte si je z `SECRET NAME` daného klíče. Například, `AzureADB2CClientSecret`.

Chcete-li deklarovat hodnotu jako tajný kód v `maverics.yaml` konfiguračním souboru, zabalte tajný klíč pomocí lomených závorek:

```yaml
connectors:
  - name: AzureADB2C
    type: azure
    oauthClientID: <AzureADB2CClientID>
    oauthClientSecret: <AzureADB2CClientSecret>
```

Hodnota zadaná v lomených závorkách musí odpovídat `SECRET NAME` danému tajnému klíči v Azure Key Vault.

Pro načtení tajných kódů z Azure Key Vault nastavte proměnnou prostředí `MAVERICS_SECRET_PROVIDER` v souboru `/etc/maverics/maverics.env` s přihlašovacími údaji nalezenými v azure-credentials.jssouboru pomocí následujícího vzoru:

`MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

### <a name="put-everything-together"></a>Umístit všechno dohromady

Tady je postup, jak se konfigurace nástroje Orchestrator zobrazí po dokončení konfigurací uvedených výše.

```yaml
version: 0.4.2
listenAddress: ":443"
tls:
  maverics:
    certFile: certs/maverics.crt
    keyFile: certs/maverics.key

authProvider: azureADB2C

connectors:
  - name: azureADB2C
    type: azure
    oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
    oauthRedirectURL: https://example.com/oidc-endpoint
    oauthClientID: <azureADB2CClientID>
    oauthClientSecret: <azureADB2CClientSecret>
    authType: oidc

appgateways:
  - name: Sonar
    location: /
    upstream: http://example.com:8080
    errorPage: http://example.com:8080/sonar/accessdenied
    unauthorizedPage: http://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

## <a name="test-the-flow"></a>Testování toku

1. Přejděte na adresu URL místní aplikace `https://example.com/sonar/dashboard` .

2. Nástroj Orchestrator by měl přesměrovat na stránku, kterou jste nakonfigurovali v uživatelském toku.

3. Vyberte IdP ze seznamu na stránce.

4. Až budete přesměrováni na IdP, zadejte své přihlašovací údaje podle požadavků, včetně tokenu MFA, pokud to vyžaduje tento IdP.

5. Po úspěšném ověření by se měla přesměrovat na Azure AD B2C, která předává požadavek aplikace na identifikátor URI pro přesměrování nástroje Orchestrator.

6. Nástroj Orchestrator vyhodnocuje zásady, vypočítává hlavičky a odesílá uživatele do nadřazeného aplikace.  

7. Měla by se zobrazit požadovaná aplikace.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- [Vlastní zásady v Azure AD B2C](./custom-policy-overview.md)

- [Začínáme s vlastními zásadami v Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
