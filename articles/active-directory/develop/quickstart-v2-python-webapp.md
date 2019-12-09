---
title: Microsoft Identity Platform Python Web App Starter | Azure
description: Naučte se implementovat přihlašování Microsoftu na webové aplikaci v Pythonu pomocí OAuth2.
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev
ms.openlocfilehash: 0d6b96683a04fa5d2814246b3c22aa52956a8bbb
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74920510"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Rychlý Start: Přidání přihlašování do webové aplikace v Pythonu pomocí Microsoftu

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

V tomto rychlém startu se dozvíte, jak integrovat webovou aplikaci v Pythonu s platformou Microsoft identity. Vaše aplikace se přihlásí k uživateli, získá přístupový token pro volání rozhraní API Microsoft Graph a vytvoří požadavek na rozhraní Microsoft Graph API.

Po dokončení průvodce bude aplikace přijímat přihlašovacíky osobních účtů Microsoft (včetně outlook.com, live.com a dalších) a pracovních nebo školních účtů z jakékoli společnosti nebo organizace, která používá Azure Active Directory.

![Ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-python-webapp/python-quickstart.svg)

## <a name="prerequisites"></a>Předpoklady

K provedení této ukázky budete potřebovat:

- [Python 2.7 +](https://www.python.org/downloads/release/python-2713) nebo [Python 3 +](https://www.python.org/downloads/release/python-364/)
- [Baňka](http://flask.pocoo.org/), [baňka – relace](https://pythonhosted.org/Flask-Session/), [žádosti](https://requests.kennethreitz.org//en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start
>
> Máte dvě možnosti, jak spustit aplikaci pro rychlý Start: Express (možnost 1) a ruční (možnost 2).
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Možnost 1: Registrace a automatická konfigurace aplikace a následné stažení vzorového kódu
>
> 1. Přejít na [Registrace aplikací Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Vyberte **Nová registrace**.
> 1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
> 1. Podle pokynů stáhněte a automaticky nakonfigurujte novou aplikaci.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2: Registrace a ruční konfigurace aplikace a vzorového kódu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace
>
> Pokud chcete zaregistrovat aplikaci a ručně přidat informace o registraci aplikace ke svému řešení, postupujte následovně:
>
> 1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
> 1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
> 1. Přejděte na stránku [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) Microsoft Identity Platform for Developers.
> 1. Vyberte **Nová registrace**.
> 1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
>      - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `python-webapp`.
>      - V části **podporované typy účtů**vyberte **účty v libovolném organizačním adresáři a osobní účty Microsoft**.
>      - V části **identifikátor URI pro přesměrování** vyberte v rozevíracím seznamu **webovou** platformu a nastavte hodnotu na `http://localhost:5000/getAToken`.
>      - Vyberte **Zaregistrovat**. Na stránce **Přehled** aplikace si poznamenejte hodnotu **ID aplikace (klienta)** pro pozdější použití.
> 1. V nabídce vlevo vyberte **certifikáty & tajných** kódů a v části **tajné klíče klienta** klikněte na **nový tajný klíč klienta** :
>
>      - Zadejte popis klíče (instance tajného kódu aplikace).
>      - Vyberte dobu trvání klíče **v intervalu 1 roku**.
>      - Po kliknutí na tlačítko **Přidat**se zobrazí hodnota klíče.
>      - Zkopírujte hodnotu klíče. Budete ho potřebovat později.
> 1. Vyberte oddíl **oprávnění rozhraní API** .
>
>      - Klikněte na tlačítko **Přidat oprávnění** a pak na
>      - Ujistěte se, že je vybraná karta **rozhraní API Microsoftu** .
>      - V části *běžně používaná rozhraní Microsoft API* klikněte na **Microsoft Graph**
>      - V části **delegovaná oprávnění** zkontrolujte, že jsou zaškrtnutá správná oprávnění: **User. ReadBasic. All**. V případě potřeby použijte vyhledávací pole.
>      - Vyberte tlačítko **Přidat oprávnění** .
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Krok 1: Nakonfigurujte si aplikaci na portálu Azure Portal
>
> Ukázku kódu pro tento rychlý Start, který funguje, je třeba:
>
> 1. Přidejte adresu URL odpovědi jako `http://localhost:5000/getAToken`.
> 1. Vytvořte tajný klíč klienta.
> 1. Přidat uživatele. ReadBasic rozhraní API Microsoft Graph. všechna delegovaná oprávnění.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Provést tyto změny pro mě]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-aspnet-webapp/green-check.png) Vaše aplikace je nakonfigurovaná s tímto atributem

#### <a name="step-2-download-your-project"></a>Krok 2: Stáhněte si projekt

[Stažení ukázky kódu](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

#### <a name="step-3-configure-the-application"></a>Krok 3: Konfigurace aplikace

1. Extrahujte soubor ZIP do místní složky bližší ke kořenové složce, třeba **C:\Azure-Samples**.
1. Pokud používáte integrované vývojové prostředí, otevřete ukázku v oblíbeném INTEGROVANÉm vývojovém prostředí (volitelné).
1. Otevřete soubor **app_config. py** , který najdete v kořenové složce, a nahraďte ho následujícím fragmentem kódu:

```python
CLIENT_ID = "Enter_the_Application_Id_here"
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
```

> [!div renderon="docs"]
> Kde:
>
> - `Enter_the_Application_Id_here` je ID aplikace, kterou jste zaregistrovali.
> - `Enter_the_Client_Secret_Here` – je **tajný klíč klienta** , který jste vytvořili v části **certifikáty & tajných** kódů pro zaregistrovanou aplikaci.
> - `Enter_the_Tenant_Name_Here` – hodnota **ID adresáře (tenant)** aplikace, kterou jste zaregistrovali.

#### <a name="step-4-run-the-code-sample"></a>Krok 4: spuštění ukázky kódu

1. Budete muset nainstalovat MSAL knihovny Pythonu, architekturu baněk, baňky pro správu relací na straně serveru a požadavky pomocí PIP následujícím způsobem:

   ```Shell
   pip install -r requirements.txt
   ```

2. Spusťte app.py z prostředí nebo příkazového řádku:

   ```Shell
   python app.py
   ```
   > [!IMPORTANT]
   > Tato aplikace rychlý Start používá k identifikaci jako důvěrného klienta tajný klíč klienta. Vzhledem k tomu, že se tajný klíč klienta přidá do souborů projektu jako prostý text, doporučuje se místo toho použít certifikát namísto tajného klíče klienta před tím, než aplikaci vyberou jako produkční aplikaci. Další informace o použití certifikátu najdete v [těchto pokynech](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials).

## <a name="more-information"></a>Další informace

### <a name="getting-msal"></a>Získání MSAL
MSAL je knihovna používaná k přihlašování uživatelů a žádosti o tokeny používané pro přístup k rozhraní API chráněnému platformou Microsoft identity.
MSAL Python můžete do své aplikace přidat pomocí PIP.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>Inicializace knihovny MSAL
Odkaz na MSAL Python můžete přidat přidáním následujícího kódu do horní části souboru, kde budete používat MSAL:

```Python
import msal
```

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o webových aplikacích, které přihlásí uživatele, a potom zavolá webová rozhraní API:

> [!div class="nextstepaction"]
> [Scénář: webové aplikace, které přihlásí uživatele](scenario-web-app-sign-user-overview.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
