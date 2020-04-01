---
title: Použití režimu sdíleného zařízení s MSAL Android | Azure
description: Přečtěte si, jak připravit zařízení Android tak, aby běželo ve sdíleném režimu a jak spustit pracovní aplikaci první linie.
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 1/15/2020
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: bf7e6bb22ce89d6be3f79efad1f1a3679e8780e7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77086063"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>Kurz: Použití režimu sdíleného zařízení v aplikaci pro Android

> [!NOTE]
> Tato funkce je ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="developer-guide"></a>Příručka pro vývojáře

Tato příručka poskytuje pokyny pro vývojáře k implementaci režimu sdíleného zařízení v aplikaci pro Android pomocí knihovny Microsoft Authentication Library (MSAL). Podívejte se na [kurz MSAL pro Android,](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android) kde se podívejte, jak integrovat MSAL s aplikací pro Android, přihlásit se k uživateli, zavolat microsoft graph a odhlásit uživatele.

### <a name="download-the-sample"></a>Stažení ukázky

Klonujte [ukázkovou aplikaci](https://github.com/Azure-Samples/ms-identity-android-java/) z GitHubu. Ukázka má schopnost pracovat v [režimu jednoho nebo více účtů](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account).

### <a name="add-the-msal-sdk-to-your-local-maven-repository"></a>Přidání sady MSAL SDK do místního úložiště Maven

Pokud nepoužíváte ukázkovou aplikaci, přidejte knihovnu MSAL jako závislost do souboru build.gradle, například takto:

```gradle
dependencies{
  implementation 'com.microsoft.identity.client.msal:1.0.+'
}
```

### <a name="configure-your-app-to-use-shared-device-mode"></a>Konfigurace aplikace tak, aby používala režim sdíleného zařízení

Další informace o nastavení konfiguračního souboru naleznete v [dokumentaci](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration) ke konfiguraci.

Nastavte `"shared_device_mode_supported"` `true` tak, aby v konfiguračním souboru MSAL.

Pravděpodobně neplánujete podporovat režim více účtů. To může být, pokud nepoužíváte sdílené zařízení a uživatel se může přihlásit do aplikace s více než jedním účtem současně. Pokud ano, `"account_mode"` `"SINGLE"`nastavte na . To zaručuje, že vaše `ISingleAccountPublicClientApplication`aplikace bude vždy získat , a výrazně zjednodušuje integraci MSAL. Výchozí hodnota `"account_mode"` je `"MULTIPLE"`, takže je důležité změnit tuto hodnotu v konfiguračním souboru, pokud používáte `"single account"` režim.

Tady je příklad souboru auth_config.json, který je součástí**hlavního**>**nezpracovaného** adresáře>**ukázkové**>aplikace: **app**

```json
{
 "client_id":"Client ID after app registration at https://aka.ms/MobileAppReg",
 "authorization_user_agent":"DEFAULT",
 "redirect_uri":"Redirect URI after app registration at https://aka.ms/MobileAppReg",
 "account_mode":"SINGLE",
 "broker_redirect_uri_registered": true,
 "shared_device_mode_supported": true,
 "authorities":[
  {
   "type":"AAD",
   "audience":{
     "type": "AzureADandPersonalMicrosoftAccount",
     "tenant_id":"common"
   }
  }
 ]
}
```

### <a name="detect-shared-device-mode"></a>Detekce režimu sdíleného zařízení

Režim sdíleného zařízení umožňuje konfigurovat zařízení Android tak, aby byla sdílena více zaměstnanci, a zároveň poskytovat správu zařízení podporovanou službou Microsoft Identity. Zaměstnanci se mohou přihlásit ke svým zařízením a rychle přistupovat k informacím o zákaznících. Po dokončení směny nebo úkolu se budou moci jediným kliknutím odhlásit ze všech aplikací na sdíleném zařízení a zařízení bude okamžitě připraveno pro další zaměstnance.

Slouží `isSharedDevice()` k určení, zda je aplikace spuštěná na zařízení, které je v režimu sdíleného zařízení. Vaše aplikace může použít tento příznak k určení, zda by měla odpovídajícím způsobem upravit uživatelské uživatelské nastavení.

Tady je fragment kódu, který ukazuje, jak `isSharedDevice()`byste mohli použít .  Je to z `SingleAccountModeFragment` třídy v ukázkové aplikaci:

```Java
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ?"Shared" :"Non-Shared");
```

### <a name="initialize-the-publicclientapplication-object"></a>Inicializovat objekt PublicClientApplication

Pokud nastavíte `"account_mode":"SINGLE"` v konfiguračním souboru MSAL, `ISingleAccountPublicCLientApplication`můžete bezpečně přetypovat vrácený aplikační objekt jako .

```java
private ISingleAccountPublicClientApplication mSingleAccountApp;

/*Configure your sample app and save state for this activity*/ 
PublicClientApplication.create(this.getApplicationCOntext(),
  R.raw.auth_config, 
  new PublicClientApplication.ApplicationCreatedListener(){
  @Override
  public void onCreated(IPublicClientApplication application){
  mSingleAccountApp = (ISingleAccountPublicClientApplication)application;
  loadAccount();
  }
  @Override
  public void onError(MsalException exception{
  /*Fail to initialize PublicClientApplication */
  }
});  
```

### <a name="detect-single-vs-multiple-account-mode"></a>Detekce režimu jednoho a více účtů

Pokud píšete aplikaci, která se bude používat jenom pro pracovníky první ho řádku na sdíleném zařízení, doporučujeme napsat aplikaci tak, aby podporovala pouze režim jednoho účtu. To zahrnuje většinu aplikací, které jsou zaměřené na úkoly, jako jsou aplikace lékařských záznamů, fakturační aplikace a většina obchodních aplikací. Tím se zjednoduší vývoj, protože mnoho funkcí sady SDK nebude nutné přizpůsobit.

Pokud vaše aplikace podporuje více účtů a režim sdíleného zařízení, musíte provést kontrolu typu a přetypovat do příslušného rozhraní, jak je znázorněno níže.

```java
private IPublicClientApplication mApplication;

        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        } else if (mApplication instanceOf    ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Získání přihlášeného uživatele a zjištění, zda se uživatel v zařízení změnil

Metoda `loadAccount` načte účet přihlášeného uživatele. Metoda `onAccountChanged` určuje, zda se přihlášený uživatel změnil, a pokud ano, vyčistěte:

```java 
private void loadAccount()
{
  mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback()
  {
    @Overide
    public void onAccountLoaded(@Nullable IAccount activeAccount)
    {
      if (activeAccount != null)
      {
        signedInUser = activeAccount;
        mSingleAccountApp.acquireTokenSilentAsync(SCOPES,"http://login.microsoftonline.com/common",getAuthSilentCallback());
      }
    }
    @Override
    public void on AccountChanged(@Nullable IAccount priorAccount, @Nullable Iaccount currentAccount)
    {
      if (currentAccount == null)
      {
        //Perform a cleanup task as the signed-in account changed.
        updateSingedOutUI();
      }
    }
    @Override 
    public void onError(@NonNull Exception exception) 
    {
    }
  }
}  
```

### <a name="globally-sign-in-a-user"></a>Globální přihlášení uživatele

Následující značky v uživateli v celém zařízení k jiným aplikacím, které používají MSAL s ověřovací aplikací:

```java
private void onSignInClicked()
{
  mSingleAccountApp.signIn(getActivity(), SCOPES, null, getAuthInteractiveCallback());
}
```

### <a name="globally-sign-out-a-user"></a>Globální odhlášení uživatele

Následující odebere účet pro přihlášení a vymaže tokeny uložené v mezipaměti nejen z aplikace, ale také ze zařízení, které je v režimu sdíleného zařízení:

```java
private void onSignOutClicked()
{
  mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback()
  {
    @Override
    public void onSignOut()
    {
      updateSignedOutUI();
    }
    @Override
    public void onError(@NonNull MsalException exception)
    {
      /*failed to remove account with an exception*/
    }
  });
}
```

## <a name="administrator-guide"></a>Příručka správce

Následující kroky popisují nastavení aplikace na webu Azure Portal a přepání zařízení do režimu sdíleného zařízení.

### <a name="register-your-application-in-azure-active-directory"></a>Registrace aplikace ve službě Azure Active Directory

Nejprve zaregistrujte aplikaci v rámci vašeho klienta organizace. Potom zadejte tyto hodnoty níže v auth_config.json, aby vaše aplikace správně spustit.

Informace o tom, jak to provést, naleznete [v seznamu Zaregistrovat přihlášku](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android#register-your-application).

> [!NOTE]
> Při registraci aplikace použijte příručku pro rychlý start na levé straně a pak vyberte **Android**. Tím se dostanete na stránku, kde budete **požádáni** o zadání názvu balíčku a **hash podpisu** pro vaši aplikaci. Ty jsou velmi důležité, aby konfigurace aplikace bude fungovat. Potom obdržíte konfigurační objekt, který můžete použít pro svou aplikaci, kterou vyjmete a vložíte do souboru auth_config.json.

![Obrazovka](media/tutorial-v2-shared-device-mode/register-app.png) registrace aplikací: Měli byste vybrat **Provést tuto změnu pro mě** a pak zadat hodnoty, které si rychlý start vyžádá na webu Azure Portal. Až to bude hotové, vygenerujeme všechny konfigurační soubory, které potřebujete.

![Informační obrazovka konfigurace aplikace](media/tutorial-v2-shared-device-mode/config-info.png)

## <a name="set-up-a-tenant"></a>Nastavení tenanta

Pro účely testování nastavte ve svém tenantovi následující: alespoň dva zaměstnance, jednoho správce cloudových zařízení a jednoho globálního správce. Na webu Azure Portal nastavte správce cloudových zařízení úpravou rolí organizace. Na webu Azure Portal můžete přistupovat ke svým organizačním rolím výběrem rolí **Azure Active Directory** > a**správce cloudových zařízení****Administrators** > . Přidejte uživatele, kteří mohou zařízení převést do sdíleného režimu.

## <a name="set-up-an-android-device-in-shared-mode"></a>Nastavení zařízení Android ve sdíleném režimu

### <a name="download-the-authenticator-app"></a>Stažení aplikace Authenticator

Stáhněte si aplikaci Microsoft Authenticator app z obchodu Google Play. Pokud jste již aplikaci stáhli, ujistěte se, že se jedná o nejnovější verzi.

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>Nastavení aplikace Authenticator & registraci zařízení v cloudu

Spusťte aplikaci Authenticator a přejděte na stránku hlavního účtu. Jakmile se zobrazí stránka **Přidat účet,** můžete zařízení sdílet.

![Obrazovka přidání účtu authenticator](media/tutorial-v2-shared-device-mode/authenticator-add-account.png)

 Přejděte do podokna **Nastavení** pomocí pravého řádku nabídek. V části **Pracovní & školní účty**vyberte **Registrace zařízení** .
 
 ![Obrazovka přidání účtu authenticator](media/tutorial-v2-shared-device-mode/authenticator-settings.png)

 Po kliknutí na toto tlačítko budete vyzváni k autorizaci přístupu ke kontaktům zařízení. To je způsobeno integrací účtu Android na zařízení. Zvolte **povolit**.

 ![Obrazovka přidání účtu authenticator](media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png)

Správce cloudových zařízení by měl zadat svůj e-mail organizace v části **Nebo se zaregistrovat jako sdílené zařízení**. Potom klikněte na **tlačítko Registrovat jako sdílené zařízení** a zadejte jejich přihlašovací údaje.

![obrazovka registračního zařízení](media/tutorial-v2-shared-device-mode/register-device.png)

![sign-in](media/tutorial-v2-shared-device-mode/sign-in.png)

Zařízení je nyní ve sdíleném režimu.

![obrazovka registračního zařízení](media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png)

 Všechna přihlášení a přihlášení v zařízení budou globální, což znamená, že se vztahují na všechny aplikace, které jsou v zařízení integrované s msal a microsoft authenticator. Nyní můžete nasadit aplikace do zařízení, které používá funkce režimu sdíleného zařízení.

## <a name="view-the-shared-device-in-the-azure-portal"></a>Zobrazení sdíleného zařízení na webu Azure Portal

Jakmile zařízení přepnete do sdíleného režimu, vaše organizace ho ozve a bude sledováno ve vašem tenantovi organizace. Sdílená zařízení si můžete zobrazit tak, že se **podíváte** na typ spojení v okně Azure Active Directory na vašem portálu Azure.

![Blade všech zařízení na webu Azure Portal](media/tutorial-v2-shared-device-mode/registered-device-screen.png)

## <a name="running-the-sample-app"></a>Spuštění ukázkové aplikace

Ukázková aplikace je jednoduchá aplikace, která bude volat rozhraní GRAPH API vaší organizace. n první spuštění budete vyzváni k souhlasu, protože aplikace je pro váš zaměstnanecký účet nová.

![Informační obrazovka konfigurace aplikace](media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png)

## <a name="next-steps"></a>Další kroky

Další informace o sdíleném režimu v [režimu Sdílené zařízení pro zařízení se systémem Android](shared-device-mode.md)