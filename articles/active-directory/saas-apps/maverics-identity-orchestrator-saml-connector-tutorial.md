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
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 402f6cd6961108cdf1e9c94fb4f93309fbf15ead
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599022"
---
# <a name="integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Integrace jednotného přihlašování Azure AD s Maverics identity Orchestrator konektorem SAML

Maverics identity Orchestrator přináší jednoduchý způsob, jak integrovat místní aplikace s Azure Active Directory (Azure AD) pro ověřování a řízení přístupu. Maverics Orchestrator je schopný modernizaci ověřování a autorizaci pro aplikace, které se aktuálně spoléhají na hlavičky, soubory cookie a další proprietární metody ověřování. Instance Maverics Orchestrator můžete nasadit místně nebo v cloudu. 

Tento kurz hybridního přístupu ukazuje, jak migrovat místní webovou aplikaci, která je aktuálně chráněná starší verzí produktu pro správu webového přístupu, aby používala k ověřování a řízení přístupu službu Azure AD. Zde jsou základní kroky:

1. Nastavení nástroje Maverics Orchestrator
1. Proxy aplikace
1. Registrace podnikové aplikace v Azure AD
1. Ověřování prostřednictvím Azure a autorizace přístupu k aplikaci
1. Přidat hlavičky pro bezproblémové přístupu k aplikacím
1. Práce s více aplikacemi

## <a name="prerequisites"></a>Požadavky

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Maverics identity Orchestrator s povoleným SSO konektorem SAML. Pokud chcete získat Maverics software, kontaktujte [oddělení prodej](mailto:sales@strata.io).
* Alespoň jedna aplikace, která používá ověřování na základě hlaviček. Příklady fungují v aplikaci s názvem Connectulum, která je hostována v `https://app.connectulum.com` .
* Počítač se systémem Linux, který bude hostitelem nástroje Maverics Orchestrator
  * Operační systém: RHEL 7,7 nebo vyšší, CentOS 7 +
  * Disk: >= 10 GB
  * Paměť: >= 4 GB
  * Porty: 22 (SSH/SCP), 443, 7474
  * Přístup ke kořenu pro úlohy instalace nebo správy
  * Odchozí přenos dat ze serveru, který hostuje Maverics identity Orchestrator, do chráněné aplikace

## <a name="step-1-set-up-the-maverics-orchestrator"></a>Krok 1: nastavení nástroje Maverics Orchestrator

### <a name="install-maverics"></a>Nainstalovat Maverics

1. Získejte nejnovější Maverics ot./min.. Zkopírujte balíček do systému, do kterého chcete nainstalovat software Maverics.

1. Nainstalujte balíček Maverics a nahraďte název souboru místo `maverics.rpm` .

   `sudo rpm -Uvf maverics.rpm`

   Po instalaci Maverics se spustí jako služba v rámci `systemd` . Pokud chcete ověřit, jestli je služba spuštěná, spusťte následující příkaz:

   `sudo systemctl status maverics`

1. Chcete-li restartovat produkt Orchestrator a postupovat podle protokolů, můžete spustit následující příkaz:

   `sudo service maverics restart; sudo journalctl --identifier=maverics -f`

Po instalaci Maverics se `maverics.yaml` v adresáři vytvoří výchozí soubor `/etc/maverics` . Než upravíte konfiguraci tak, aby zahrnovala `appgateways` a `connectors` , váš konfigurační soubor bude vypadat takto:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```

### <a name="configure-dns"></a>Konfigurace služby DNS

Služba DNS bude užitečná, abyste si nemuseli pamatovat na IP adresu serveru Orchestrator.

Pomocí hypotetického protokolu Orchestrator pro 12.34.56.78 v prohlížeči upravte soubor hostitelů vašeho přenosného počítače. V operačních systémech Linux je tento soubor umístěný v `/etc/hosts` . Ve Windows je umístěný na adrese `C:\windows\system32\drivers\etc` .

```
12.34.56.78 sonar.maverics.com
12.34.56.78 connectulum.maverics.com
```

Pokud chcete potvrdit, že je služba DNS nakonfigurovaná podle očekávání, můžete vytvořit požadavek na koncový bod stavu nástroje Orchestrator. V prohlížeči si vyžádejte požadavek http://sonar.maverics.com:7474/status .

### <a name="configure-tls"></a>Konfigurace TLS

Komunikace přes zabezpečené kanály pro komunikaci s nástrojem Orchestrator je zásadní pro zachování zabezpečení. K tomuto účelu můžete přidat dvojici certifikátů nebo klíčů v `tls` části.

Chcete-li vygenerovat certifikát podepsaný svým držitelem a klíč pro server Orchestrator, spusťte v adresáři následující příkaz `/etc/maverics` :

`openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out maverics.crt -keyout maverics.key`

> [!NOTE]
> U produkčních prostředí pravděpodobně budete chtít použít certifikát podepsaný známou certifikační autoritou, aby se předešlo upozorněním v prohlížeči. [Šifrování](https://letsencrypt.org/) je vhodné, pokud hledáte důvěryhodnou certifikační autoritu, je to dobrá a bezplatná možnost.

Nyní použijte nově vygenerovaný certifikát a klíč pro nástroj Orchestrator. Konfigurační soubor by měl nyní obsahovat tento kód:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key
```

Pokud chcete ověřit, že je protokol TLS nakonfigurovaný podle očekávání, restartujte službu Maverics a vytvořte žádost na koncový bod stavu.

## <a name="step-2-proxy-an-application"></a>Krok 2: proxy aplikace

Dále nakonfigurujte základní proxy server v nástroji Orchestrator pomocí `appgateways` . Tento krok vám pomůže ověřit, jestli má Orchestrator potřebná připojení k chráněné aplikaci.

Konfigurační soubor by měl nyní obsahovat tento kód:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com
```

Pokud chcete potvrdit, že proxy pracuje podle očekávání, restartujte službu Maverics a vyžádejte si požadavek na aplikaci prostřednictvím proxy serveru Maverics. Volitelně můžete vytvořit požadavek na konkrétní prostředky aplikace.

## <a name="step-3-register-an-enterprise-application-in-azure-ad"></a>Krok 3: registrace podnikové aplikace v Azure AD

Teď vytvořte v Azure AD novou podnikovou aplikaci, která se bude používat k ověřování koncových uživatelů.

> [!NOTE]
> Pokud používáte funkce Azure AD, jako je podmíněný přístup, je důležité vytvořit podnikovou aplikaci na místní aplikaci. To umožňuje podmíněný přístup podle aplikace, hodnocení rizik pro jednotlivé aplikace, přiřazená oprávnění pro jednotlivé aplikace atd. Obecně platí, že podniková aplikace ve službě Azure AD se mapuje na konektor Azure v Maverics.

Registrace podnikové aplikace v Azure AD:

1. V tenantovi Azure AD, klikněte na **podnikové aplikace** a pak vyberte **Nová aplikace**. V galerii Azure AD vyhledejte **konektor Maverics identity Orchestrator** a pak ho vyberte.

1. V podokně **vlastností** konektoru Maverics identity Orchestrator Orchestrator nastavte **přiřazení uživatele požadované?** na **ne** , pokud chcete, aby aplikace fungovala pro všechny uživatele ve vašem adresáři.

1. V podokně **Přehled** konektoru Maverics identity Orchestrator Orchestrator vyberte **nastavit jednotné přihlašování** a pak vyberte **SAML**.

1. V podokně přihlášení Maverics identity Orchestrator konektor SAML s odkazem na **bázi SAML** upravte **základní konfiguraci SAML** tak, že vyberete tlačítko **Upravit** (ikona tužky).

   ![Snímek obrazovky s tlačítkem pro úpravu základní konfigurace SAML](common/edit-urls.png)

1. Zadejte **ID entity** `https://sonar.maverics.com` . ID entity musí být v rámci aplikací v tenantovi jedinečné a může to být libovolná hodnota. Tuto hodnotu použijete při definování `samlEntityID` pole pro Azure Connector v další části.

1. Zadejte **adresu URL odpovědi** `https://sonar.maverics.com/acs` . Tuto hodnotu použijete při definování `samlConsumerServiceURL` pole pro Azure Connector v další části.

1. Zadejte **adresu URL pro přihlášení** `https://sonar.maverics.com/` . Toto pole se nepoužívá v Maverics, ale ve službě Azure AD se vyžaduje k tomu, aby uživatelé mohli získat přístup k aplikaci prostřednictvím portálu Azure AD moje aplikace.

1. Vyberte **Uložit**.

1. V části **podpisový certifikát SAML** vyberte tlačítko **Kopírovat** a zkopírujte hodnotu **adresy URL federačních metadat aplikace** a pak ji uložte do svého počítače.

   ![Snímek obrazovky s tlačítkem pro kopírování podpisového certifikátu SAML](common/copy-metadataurl.png)

## <a name="step-4-authenticate-via-azure-and-authorize-access-to-the-application"></a>Krok 4: ověření prostřednictvím Azure a autorizace přístupu k aplikaci

V dalším kroku vložte podnikovou aplikaci, kterou jste právě vytvořili, pro použití při konfiguraci konektoru Azure v Maverics. Tato `connectors` Konfigurace spárovaná s `idps` blokem umožňuje nástroji Orchestrator ověřovat uživatele.

Konfigurační soubor by měl nyní obsahovat následující kód. Nezapomeňte nahradit `METADATA_URL` hodnotu adresy URL federačních metadat aplikace z předchozího kroku.

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

Pokud chcete potvrdit, že ověřování funguje podle očekávání, restartujte službu Maverics a vytvořte požadavek na prostředek aplikace prostřednictvím proxy serveru Maverics. Před přístupem k prostředku byste měli být přesměrováni na Azure pro ověřování.

## <a name="step-5-add-headers-for-seamless-application-access"></a>Krok 5: přidání hlaviček pro bezproblémové přístupu k aplikacím

Zatím neposíláte hlavičky do nadřazené aplikace. Pojďme do žádosti přidat, když `headers` projde proxy Maverics a povolíte tak nadřazené aplikaci identifikovat uživatele.

Konfigurační soubor by měl nyní obsahovat tento kód:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

Pokud chcete potvrdit, že ověřování funguje podle očekávání, vytvořte žádost o prostředek aplikace prostřednictvím proxy serveru Maverics. Chráněná aplikace by teď měla dostávat hlavičky na žádosti. 

Pokud vaše aplikace očekává odlišná záhlaví, můžete klíče hlaviček upravovat. Všechny deklarace identity, které se vracejí z Azure AD jako součást toku SAML, jsou k dispozici pro použití v hlavičkách. Můžete například zahrnout další hlavičku `secondary_email: azureSonarApp.email` , kde `azureSonarApp` je název konektoru a `email` je deklarací vrácenými z Azure AD. 

## <a name="step-6-work-with-multiple-applications"></a>Krok 6: práce s více aplikacemi

Teď se podíváme na to, co je potřeba k proxy serveru pro víc aplikací, které jsou na různých hostitelích. Pokud chcete dosáhnout tohoto kroku, nakonfigurujte jinou Aplikační bránu, jinou podnikovou aplikaci v Azure AD a další konektor.

Konfigurační soubor by měl nyní obsahovat tento kód:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp
  - name: azureConnectulumApp

appgateways:
  - name: sonar
    host: sonar.maverics.com
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

  - name: connectulum
    host: connectulum.maverics.com
    location: /
    # Replace https://app.connectulum.com with the address of your protected application
    upstream: https://app.connectulum.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureConnectulumApp.authenticated}}", "true"]

    headers:
      email: azureConnectulumApp.name
      firstname: azureConnectulumApp.givenname
      lastname: azureConnectulumApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com

  - name: azureConnectulumApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://connectulum.maverics.com/acs
    samlEntityID: https://connectulum.maverics.com
```

Možná jste si všimli, že kód přidá `host` pole do definic služby App Gateway. `host`Pole umožňuje, aby nástroj Maverics Orchestrator rozlišil, ke kterému nadřazenému hostiteli má provoz proxy.

Pokud chcete potvrdit, že nově přidaná aplikace App Gateway funguje podle očekávání, vytvořte žádost na `https://connectulum.maverics.com` .

## <a name="advanced-scenarios"></a>Pokročilé scénáře

### <a name="identity-migration"></a>Migrace identity

Nemůžete mít k dispozici nástroj pro správu webového přístupu, ale nemáte možnost migrovat uživatele bez resetování hesel? Maverics Orchestrator podporuje migraci identity pomocí `migrationgateways` .

### <a name="web-server-gateways"></a>Brány webového serveru

Nechcete přepracovat síť a provoz proxy prostřednictvím nástroje Maverics Orchestrator? Nejedná se o problém. Maverics Orchestrator se dá spárovat s branami webového serveru (moduly) a nabízet stejná řešení bez proxy.

## <a name="wrap-up"></a>Zabalit

V tuto chvíli jste nainstalovali nástroj Maverics Orchestrator, vytvořili a nakonfigurovali podnikovou aplikaci ve službě Azure AD a nakonfigurovali jste Orchestrator to proxy na chráněnou aplikaci a přitom vyžadovali ověřování a vynucování zásad. Pokud chcete získat další informace o tom, jak se Maverics Orchestrator dá použít pro případy použití distribuované správy identit, obraťte se na [vrstvy](mailto:sales@strata.io).

## <a name="next-steps"></a>Další kroky

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)
