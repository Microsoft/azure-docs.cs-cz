---
title: 'Kurz: použití režimu sdíleného zařízení s knihovnou Microsoft Authentication Library (MSAL) pro Android | Azure'
titleSuffix: Microsoft identity platform
description: V tomto kurzu se dozvíte, jak připravit zařízení s Androidem ke spuštění ve sdíleném režimu a spustit první pracovní aplikaci pracovních procesů.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 1/15/2020
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 981d3a0c5d01d70625fc0d022318c5bc866f23a0
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95756396"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>Kurz: použití režimu sdíleného zařízení v aplikaci pro Android

V tomto kurzu můžou vývojáři pro Android a Azure Active Directory (Azure AD) získat informace o kódu, ověřovací aplikaci a nastavení klienta potřebného k povolení režimu sdíleného zařízení pro aplikaci pro Android.

V tomto kurzu:

> [!div class="checklist"]
> * Stažení ukázky kódu
> * Povolit a detekovat režim sdíleného zařízení
> * Zjistit režim jednoho nebo více účtů
> * Zjištění přepínače uživatele a povolení globálního přihlašování a odhlašování
> * Nastavte tenanta a zaregistrujte aplikaci v Azure Portal
> * Nastavení zařízení s Androidem v režimu sdíleného zařízení
> * Spuštění ukázkové aplikace

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="developer-guide"></a>Příručka pro vývojáře

Tato část kurzu poskytuje pokyny pro vývojáře k implementaci režimu sdíleného zařízení v aplikaci pro Android pomocí knihovny Microsoft Authentication Library (MSAL). Podívejte se na [kurz k MSAL Androidu](./tutorial-v2-android.md) , kde najdete informace o integraci MSAL s aplikací pro Android, přihlášení uživatele, volání Microsoft graphu a odhlášení uživatele.

### <a name="download-the-sample"></a>Stažení ukázky

Naklonujte [ukázkovou aplikaci](https://github.com/Azure-Samples/ms-identity-android-java/) z GitHubu. Ukázka nabízí možnost pracovat v [režimu jednoho nebo více účtů](./single-multi-account.md).

### <a name="add-the-msal-sdk-to-your-local-maven-repository"></a>Přidání sady MSAL SDK do místního úložiště Maven

Pokud ukázkovou aplikaci nepoužíváte, přidejte knihovnu MSAL jako závislost do souboru Build. Gradle, například takto:

```gradle
dependencies{
  implementation 'com.microsoft.identity.client.msal:1.0.+'
}
```

### <a name="configure-your-app-to-use-shared-device-mode"></a>Konfigurace aplikace tak, aby používala režim sdíleného zařízení

Další informace o nastavení konfiguračního souboru najdete v [dokumentaci ke konfiguraci](./msal-configuration.md) .

`"shared_device_mode_supported"` `true` V KONFIGURAČNÍM souboru MSAL nastavte na.

Možná neplánujete podporovat režim více účtů. To může být v případě, že nepoužíváte sdílené zařízení a uživatel se může přihlásit k aplikaci s více než jedním účtem ve stejnou dobu. Pokud ano, nastavte `"account_mode"` na `"SINGLE"` . To zaručuje, že vaše aplikace bude vždycky získávat `ISingleAccountPublicClientApplication` a významně zjednodušuje integraci MSAL. Výchozí hodnota `"account_mode"` je `"MULTIPLE"` , takže je důležité změnit tuto hodnotu v konfiguračním souboru, pokud používáte `"single account"` režim.

Tady je příklad auth_config.jsv souboru zahrnutém do hlavního umístění v **aplikaci** > **main** > **res** > **raw** ukázkové aplikace:

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

### <a name="detect-shared-device-mode"></a>Detekovat režim sdíleného zařízení

Režim sdíleného zařízení umožňuje nakonfigurovat zařízení s Androidem, která se mají sdílet s více zaměstnanci, a zajistit tak správu zařízení pomocí Microsoft identity. Zaměstnanci se můžou ke svým zařízením přihlašovat a rychle získat informace o zákaznících. Až se dokončí s jejich posunem nebo úlohou, budou se moct pomocí jediného kliknutí přihlásit ke všem aplikacím na sdíleném zařízení a zařízení se hned připraví na dalšího zaměstnance.

Použijte `isSharedDevice()` k určení, jestli je aplikace spuštěná na zařízení, které je v režimu sdíleného zařízení. Vaše aplikace by mohla pomocí tohoto příznaku určit, jestli má odpovídajícím způsobem upravit uživatelské rozhraní.

Zde je fragment kódu, který ukazuje, jak můžete použít `isSharedDevice()` .  Je z `SingleAccountModeFragment` třídy v ukázkové aplikaci:

```Java
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ?"Shared" :"Non-Shared");
```

### <a name="initialize-the-publicclientapplication-object"></a>Inicializovat objekt PublicClientApplication

Pokud jste nastavili `"account_mode":"SINGLE"` v konfiguračním souboru MSAL, můžete objekt vrácené aplikace bezpečně přetypovat jako `ISingleAccountPublicCLientApplication` .

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

### <a name="detect-single-vs-multiple-account-mode"></a>Zjišťování jednoduchého režimu a více účtů

Pokud píšete aplikaci, která se bude používat jenom pro první pracovní procesy na sdíleném zařízení, doporučujeme napsat aplikaci tak, aby podporovala pouze režim jednoho účtu. To zahrnuje většinu aplikací, které jsou zaměřeny na úlohy, jako jsou lékařské záznamy aplikací, fakturační aplikace a většina obchodních aplikací. Tím se zjednoduší váš vývoj, protože celá řada funkcí sady SDK nebude muset vyhovovat.

Pokud vaše aplikace podporuje víc účtů a také režim sdíleného zařízení, musíte provést kontrolu typu a přetypovat na příslušné rozhraní, jak je znázorněno níže.

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

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Získá přihlášeného uživatele a určí, jestli se na zařízení změnil nějaký uživatel.

`loadAccount`Metoda načte účet přihlášeného uživatele. `onAccountChanged`Metoda určuje, zda se přihlášený uživatel změnil, a pokud ano, vyčištění:

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

### <a name="globally-sign-in-a-user"></a>Globálně přihlašovat uživatele

Následující znaménko uživatele v zařízení k jiným aplikacím, které používají MSAL, s aplikací ověřovatele:

```java
private void onSignInClicked()
{
  mSingleAccountApp.signIn(getActivity(), SCOPES, null, getAuthInteractiveCallback());
}
```

### <a name="globally-sign-out-a-user"></a>Globálně odhlásit uživatele

Následující příkaz odebere přihlášený účet a smaže tokeny uložené v mezipaměti nejen z aplikace, ale také ze zařízení, které je v režimu sdíleného zařízení:

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

Následující kroky popisují nastavení aplikace v Azure Portal a vložení zařízení do režimu sdíleného zařízení.

### <a name="register-your-application-in-azure-active-directory"></a>Registrace aplikace v Azure Active Directory

Nejdřív Zaregistrujte svoji aplikaci v tenantovi vaší organizace. Pak zadejte následující hodnoty níže v auth_config.jspro, aby vaše aplikace běžela správně.

Informace o tom, jak to provést, najdete v tématu [Registrace aplikace](./tutorial-v2-android.md#register-your-application).

> [!NOTE]
> Při registraci aplikace prosím použijte příručku pro rychlý Start na levé straně a pak vyberte **Android**. Tím přejdete na stránku, kde budete požádáni o zadání **názvu balíčku** a **hodnoty hash podpisu** pro vaši aplikaci. To je velmi důležité, abyste zajistili, že konfigurace vaší aplikace bude fungovat. Pak získáte objekt konfigurace, který můžete použít pro vaši aplikaci, kterou vyjmete a vložíte do svého auth_config.jsdo souboru.

:::image type="content" source="media/tutorial-v2-shared-device-mode/register-app.png" alt-text="Stránka pro rychlý Start Azure Portal konfigurace vaší aplikace pro Android":::

Měli byste vybrat možnost **provést tuto změnu pro mě** a pak zadat hodnoty, na které se rychlý Start zeptá v Azure Portal. Až to bude hotové, vygenerujeme všechny konfigurační soubory, které potřebujete.

:::image type="content" source="media/tutorial-v2-shared-device-mode/config-info.png" alt-text="Konfigurace stránky projektu v rychlém startu Azure Portal":::

## <a name="set-up-a-tenant"></a>Nastavení tenanta

Pro účely testování nastavte ve vašem tenantovi následující: aspoň dva zaměstnance, jednoho správce cloudového zařízení a jednoho globálního správce. V Azure Portal nastavte správce cloudového zařízení úpravou organizačních rolí. V Azure Portal přejděte k rolím organizace tak, že vyberete **Azure Active Directory**  >  **role a správci**  >  **cloudového zařízení správce**. Přidejte uživatele, kteří můžou umístit zařízení do sdíleného režimu.

## <a name="set-up-an-android-device-in-shared-mode"></a>Nastavení zařízení s Androidem ve sdíleném režimu

### <a name="download-the-authenticator-app"></a>Stáhnout ověřovací aplikaci

Stáhněte si aplikaci Microsoft Authenticator z Google Play Storu. Pokud jste aplikaci již stáhli, ujistěte se, že je její nejnovější verze.

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>Nastavení aplikace ověřovatele & registraci zařízení v cloudu

Spusťte aplikaci ověřovatele a přejděte na stránku hlavní účet. Jakmile se zobrazí stránka **Přidat účet** , budete připraveni zařízení nastavit jako sdílenou.

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-add-account.png" alt-text="Obrazovka Přidat účet ověřovatele":::

Přejděte do podokna **Nastavení** pomocí panelu nabídek na pravé straně. V části **pracovní & školní účty** vyberte **registrace zařízení** .

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-settings.png" alt-text="Obrazovka nastavení ověřovatele":::

Po kliknutí na toto tlačítko budete požádáni o autorizaci přístupu k kontaktům zařízení. Důvodem je integrace účtu Androidu na zařízení. Vyberte možnost **udělit**.

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png" alt-text="Obrazovka pro potvrzení povolení přístupu ověřovatele":::

Správce cloudového zařízení by měl zadat svůj e-mailovou adresu organizace v rámci **nebo zaregistrovat jako sdílené zařízení**. Pak klikněte na tlačítko **registrovat jako sdílené zařízení** a zadejte své přihlašovací údaje.

:::image type="content" source="media/tutorial-v2-shared-device-mode/register-device.png" alt-text="Obrazovka registrace zařízení v aplikaci":::

:::image type="content" source="media/tutorial-v2-shared-device-mode/sign-in.png" alt-text="Snímek obrazovky aplikace zobrazující přihlašovací stránku Microsoftu":::

Zařízení je nyní ve sdíleném režimu.

:::image type="content" source="media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png" alt-text="Obrazovka aplikace, která zobrazuje režim sdíleného zařízení povolený":::

 Všechna přihlášení a přihlášení v zařízení budou globální, což znamená, že se vztahují na všechny aplikace, které jsou integrované s MSAL a Microsoft Authenticator na zařízení. Aplikace teď můžete nasadit do zařízení, které používá funkce režimu sdíleného zařízení.

## <a name="view-the-shared-device-in-the-azure-portal"></a>Zobrazení sdíleného zařízení v Azure Portal

Jakmile zařízení umístíte do sdíleného režimu, bude známo vaší organizaci a bude sledováno v tenantovi vaší organizace. Sdílená zařízení si můžete zobrazit tak, že v okně Azure Active Directory svého Azure Portal prohlížíte **typ spojení** .

:::image type="content" source="media/tutorial-v2-shared-device-mode/registered-device-screen.png" alt-text="Podokno všechna zařízení zobrazená v Azure Portal":::

## <a name="running-the-sample-app"></a>Spuštění ukázkové aplikace

Ukázková aplikace je jednoduchá aplikace, která bude volat Graph API vaší organizace. Při prvním spuštění se zobrazí výzva k vyjádření souhlasu s tím, že je aplikace pro váš účet zaměstnance nová.

:::image type="content" source="media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png" alt-text="Obrazovka informace o konfiguraci aplikace":::

## <a name="next-steps"></a>Další kroky

Další informace o práci s knihovnou ověřování Microsoft a režimem sdíleného zařízení na zařízeních s Androidem:

> [!div class="nextstepaction"]
> [Režim sdíleného zařízení pro zařízení s Androidem](msal-android-shared-devices.md)
