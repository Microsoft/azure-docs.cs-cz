---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: f71f394f190c5de1c3d64d02fd6113817ecda1ea
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105152776"
---
[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Skupina komunikačních služeb Azure, která **volá Hero Sample pro Android** , ukazuje, jak lze pomocí komunikačních služeb volat Android SDK vytvořit skupinu volání, která zahrnuje hlasové a video. V tomto rychlém startu se dozvíte, jak nastavit a spustit ukázku. Pro kontext je k dispozici přehled ukázky.

## <a name="download-code"></a>Stažení kódu

Vyhledá finální kód pro tento rychlý Start na [GitHubu](https://github.com/Azure-Samples/communication-services-android-calling-hero).

## <a name="overview"></a>Přehled

Ukázka je nativní aplikace pro Android, která používá sady SDK služby Azure Communications Services k sestavování volání, které nabízí volání hlasu i audiovizuálního rozhraní. Aplikace používá serverovou komponentu ke zřízení přístupových tokenů, které se pak používají k inicializaci sady Azure Communication Services SDK. Pokud chcete tuto komponentu na straně serveru nakonfigurovat, využijte Azure Functions kurzu pro [důvěryhodnou službu](../../tutorials/trusted-service-tutorial.md) .

Ukázka vypadá takto:

:::image type="content" source="../media/calling/landing-page-android.png" alt-text="Snímek obrazovky znázorňující cílovou stránku ukázkové aplikace":::

Když stisknete tlačítko spustit nové volání, aplikace pro Android vytvoří nové volání a spojí ho. Aplikace také umožňuje připojit stávající volání služby Azure Communication Services zadáním ID existujícího volání.

Po připojení k volání se zobrazí výzva, abyste aplikaci udělili oprávnění k přístupu k fotoaparátu a mikrofonu. Zobrazí se také výzva k zadání zobrazovaného názvu.

:::image type="content" source="../media/calling/pre-call-android.png" alt-text="Snímek obrazovky znázorňující obrazovku před voláním ukázkové aplikace":::

Jakmile nakonfigurujete své zobrazované jméno a zařízení, můžete se připojit k volání. Zobrazí se hlavní plátno volání, kde se nachází základní volající prostředí.

:::image type="content" source="../media/calling/main-app-android.png" alt-text="Snímek obrazovky znázorňující hlavní obrazovku ukázkové aplikace":::

Součásti hlavní volající obrazovky:

- **Galerie médií**: hlavní fáze, kde se zobrazují účastníci. Pokud má účastník zapnutou kameru, zobrazí se zde jeho video kanál. Každý účastník má jednotlivou dlaždici, která zobrazuje zobrazované jméno a datový proud videa (když je nějaký). Galerie podporuje více účastníků a je aktualizována při přidání nebo odebrání účastníků do volání.
- **Panel akcí**: tady je místo, kde se nacházejí ovládací prvky primárního volání. Tyto ovládací prvky umožňují zapnout nebo vypnout video a mikrofon, sdílet obrazovku a opustit volání.

Níže najdete další informace o požadavcích a krocích pro nastavení ukázky.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. Podrobnosti najdete v článku o [Vytvoření účtu zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Android Studio](https://developer.android.com/studio) spuštěný ve vašem počítači
- Prostředek služby Azure Communication Services. Podrobnosti najdete v tématu [vytvoření prostředku komunikace Azure](../../quickstarts/create-communication-resource.md).
- Funkce Azure, která spouští [koncový bod ověřování](../../tutorials/trusted-service-tutorial.md) pro načtení přístupových tokenů.

## <a name="running-sample-locally"></a>Místní spuštění ukázky

Ukázku volání skupiny lze spustit místně pomocí Android Studio. Vývojáři mohou aplikaci otestovat buď pomocí fyzického zařízení, nebo emulátoru.

### <a name="before-running-the-sample-for-the-first-time"></a>Před prvním spuštěním ukázky

1. Otevřete Android Studio a vyberte `Open an Existing Project`
2. Otevřete `AzureCalling` složku ve staženém vydání pro ukázku.
3. Rozbalte položku aplikace nebo prostředky, které chcete aktualizovat `appSettings.properties` . Nastavte hodnotu klíče `communicationTokenFetchUrl` na adresu URL vašeho koncového bodu pro ověřování nastaveného jako požadavek.

### <a name="run-sample"></a>Spustit ukázku

Sestavte a spusťte ukázku v Android Studio.

## <a name="optional-securing-an-authentication-endpoint"></a>Volitelné Zabezpečení koncového bodu ověřování

V případě demonstračních účely Tato ukázka ve výchozím nastavení používá k načtení tokenu služby Azure Communication Services veřejně přístupný koncový bod. V produkčních scénářích doporučujeme použít vlastní zabezpečený koncový bod ke zřízení vlastních tokenů.

V případě další konfigurace Tato ukázka podporuje připojení ke koncovému bodu chráněného **Azure Active Directory** (Azure AD), takže přihlášení uživatele vyžaduje, aby aplikace mohla načíst token služby Azure Communication Services. Viz následující postup:

1. Povolte Azure Active Directory ověřování ve vaší aplikaci.  
   - [Registrace aplikace v rámci Azure Active Directory (použití nastavení platformy Android)](../../../active-directory/develop/tutorial-v2-android.md) 
    - [Konfigurace App Service nebo Azure Functions aplikace pro použití přihlášení Azure AD](../../../app-service/configure-authentication-provider-aad.md)

2. V části Azure Active Directory registrace aplikací přejít na stránku Přehled vaší registrované aplikace. Poznamenejte si `Package name` , `Signature hash` , `MSAL Configutaion`

:::image type="content" source="../media/calling/aad-overview-android.png" alt-text="Konfigurace Azure Active Directory Azure Portal.":::

3. Úpravou `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` a nastavením `isAADAuthEnabled` povolíte Azure Active Directory
4. Upravte `AndroidManifest.xml` a nastavte `android:path` hodnotu hash podpisu úložiště klíčů. Volitelné. Aktuální hodnota používá hodnotu hash z balíčku Debug. dataúložiště. Pokud se používá jiné úložiště klíčů, je nutné ho aktualizovat.)
   ```
   <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data
                    android:host="com.azure.samples.communication.calling"
                    android:path="/Signature hash" <!-- do not remove /. The current hash in AndroidManifest.xml is for debug.keystore. -->
                    android:scheme="msauth" />
            </intent-filter>
        </activity>
   ```
5. Zkopírujte konfiguraci MSAL pro Android z Azure Portal a vložte do `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` . Include "account_mode": "SINGLE"
   ```
      {
         "client_id": "",
         "authorization_user_agent": "DEFAULT",
         "redirect_uri": "",
         "account_mode" : "SINGLE",
         "authorities": [
            {
               "type": "AAD",
               "audience": {
               "type": "AzureADMyOrg",
               "tenant_id": ""
               }
            }
         ]
      }
   ```

6. Upravte `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` a nastavte hodnotu klíče `communicationTokenFetchUrl` na adresu URL vašeho koncového bodu zabezpečeného ověřování.
7. Upravit `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` a nastavit hodnotu klíče `aadScopes` z `Azure Active Directory` `Expose an API` rozsahů

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služby Communications Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené. Přečtěte si další informace o [vyčištění prostředků](../../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Další kroky

>[!div class="nextstepaction"]
>[Stažení ukázky z GitHubu](https://github.com/Azure-Samples/communication-services-android-calling-hero)

Další informace najdete v následujících článcích:

- Seznámení s [používáním volání sady SDK](../../quickstarts/voice-video-calling/calling-client-samples.md)
- Další informace o [volání funkce](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>Další materiály ke čtení

- [GitHub pro komunikaci Azure](https://github.com/Azure/communication) – další příklady a informace najdete na oficiální stránce GitHubu.
- [Ukázky](./../overview.md) – další ukázky a příklady najdete na stránce s přehledem ukázek.
- [Funkce volání komunikace Azure](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features) – Další informace o volání sady Android SDK –[volání sady SDK pro Android komunikaci Azure](https://search.maven.org/artifact/com.azure.android/azure-communication-calling)
