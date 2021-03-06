---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: ddematheu2
manager: chpalm
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: dademath
ms.openlocfilehash: 287520f2964fba7c3c3804853e9356a8c77b2d06
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498775"
---
[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-android-ios.md)]

Skupina komunikačních služeb Azure, která **volá Hero Sample pro iOS** , ukazuje, jak se komunikační služby, které volají sadu iOS SDK, dají použít k vytvoření skupinového volajícího prostředí, které zahrnuje hlasové a video. V tomto rychlém startu se dozvíte, jak nastavit a spustit ukázku. Pro kontext je k dispozici přehled ukázky.

## <a name="download-code"></a>Stažení kódu

Vyhledejte projekt pro tuto ukázku na [GitHubu](https://github.com/Azure-Samples/communication-services-ios-calling-hero). Verzi ukázky s [týmovou spolupráci](../../concepts/teams-interop.md) můžete najít v samostatné [větvi](https://github.com/Azure-Samples/communication-services-ios-calling-hero/tree/feature/teams_interop).

## <a name="overview"></a>Přehled

Ukázka je nativní aplikace pro iOS, která používá sady SDK služby Azure Communications pro iOS k sestavení volání, které umožňuje volání hlasu i audiovizuálního rozhraní. Aplikace používá serverovou komponentu ke zřízení přístupových tokenů, které se pak používají k inicializaci sady Azure Communication Services SDK. Pokud chcete tuto komponentu na straně serveru nakonfigurovat, využijte Azure Functions kurzu pro [důvěryhodnou službu](../../tutorials/trusted-service-tutorial.md) .

Ukázka vypadá takto:

:::image type="content" source="../media/calling/landing-page-ios.png" alt-text="Snímek obrazovky znázorňující cílovou stránku ukázkové aplikace":::

Když stisknete tlačítko spustit nové volání, aplikace pro iOS vytvoří nové volání a spojí ho. Aplikace umožňuje připojit stávající volání služby Azure Communication Services zadáním ID existujícího volání.

Po připojení k volání se zobrazí výzva, abyste aplikaci udělili oprávnění k přístupu k fotoaparátu a mikrofonu. Zobrazí se také výzva k zadání zobrazovaného názvu.

:::image type="content" source="../media/calling/pre-call-ios.png" alt-text="Snímek obrazovky znázorňující obrazovku před voláním ukázkové aplikace":::

Jakmile nakonfigurujete své zobrazované jméno a zařízení, můžete se připojit k volání. Zobrazí se hlavní plátno volání, kde se nachází základní volající prostředí.

:::image type="content" source="../media/calling/main-app-ios.png" alt-text="Snímek obrazovky znázorňující hlavní obrazovku ukázkové aplikace":::

Součásti hlavní volající obrazovky:

- **Galerie médií**: hlavní fáze, kde se zobrazují účastníci. Pokud má účastník zapnutou kameru, zobrazí se zde jeho video kanál. Každý účastník má jednotlivou dlaždici, která zobrazuje zobrazované jméno a datový proud videa (když je nějaký). Galerie podporuje více účastníků a je aktualizována při přidání nebo odebrání účastníků do volání.
- **Panel akcí**: tady je místo, kde se nacházejí ovládací prvky primárního volání. Tyto ovládací prvky umožňují zapnout nebo vypnout video a mikrofon, sdílet obrazovku a opustit volání.

Níže najdete další informace o požadavcích a krocích pro nastavení ukázky.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. Podrobnosti najdete v článku o [Vytvoření účtu zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Počítač Mac se systémem [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)spolu s platným certifikátem pro vývojáře nainstalovaným do řetězce klíčů.
- Prostředek služby Azure Communication Services. Podrobnosti najdete v tématu [vytvoření prostředku komunikace Azure](../../quickstarts/create-communication-resource.md).
- Funkce Azure, která spouští [koncový bod ověřování](../../tutorials/trusted-service-tutorial.md) pro načtení přístupových tokenů.

## <a name="running-sample-locally"></a>Místní spuštění ukázky

Ukázku volání skupiny lze spustit místně pomocí XCode. Vývojáři mohou aplikaci otestovat buď pomocí fyzického zařízení, nebo emulátoru.

### <a name="before-running-the-sample-for-the-first-time"></a>Před prvním spuštěním ukázky

1. Nainstalujte závislosti spuštěním `pod install` .
2. Otevřete `AzureCalling.xcworkspace` v Xcode.
3. Aktualizace `AppSettings.plist` . Nastavte hodnotu `communicationTokenFetchUrl` klíče na adresu URL vašeho koncového bodu ověřování.

### <a name="run-sample"></a>Spustit ukázku

Sestavte a spusťte ukázku v XCode.

## <a name="optional-securing-an-authentication-endpoint"></a>Volitelné Zabezpečení koncového bodu ověřování

Pro demonstrační účely Tato ukázka ve výchozím nastavení používá k načtení přístupového tokenu služby Azure Communication Services veřejně přístupný koncový bod. V produkčních scénářích doporučujeme použít vlastní zabezpečený koncový bod ke zřízení vlastních tokenů.

V případě další konfigurace Tato ukázka podporuje připojení ke koncovému bodu chráněného **Azure Active Directory** (Azure AD), takže přihlášení uživatele vyžaduje, aby aplikace mohla načíst přístupový token služby Azure Communication Services. Viz následující postup:

1. Povolte Azure Active Directory ověřování ve vaší aplikaci.  
   - [Registrace aplikace v rámci Azure Active Directory (použití nastavení platformy iOS/macOS)](../../../active-directory/develop/tutorial-v2-ios.md) 
    - [Konfigurace App Service nebo Azure Functions aplikace pro použití přihlášení Azure AD](../../../app-service/configure-authentication-provider-aad.md)
2. V části Azure Active Directory registrace aplikací přejít na stránku Přehled vaší registrované aplikace. Poznamenejte si `Application (client) ID` , `Directory (tenant) ID` , `Application ID URI`

:::image type="content" source="../media/calling/aad-overview.png" alt-text="Konfigurace Azure Active Directory Azure Portal.":::

3. Otevřete `AppSettings.plist` v Xcode přidejte následující klíčové hodnoty:
   - `communicationTokenFetchUrl`: Adresa URL pro vyžádání tokenu služby Azure Communication Services 
   - `isAADAuthEnabled`: Logická hodnota určující, zda je vyžadováno ověřování tokenu služby Azure Communication Services.
   - `aadClientId`: ID aplikace (klienta)
   - `aadTenantId`: Vaše ID adresáře (tenant)
   - `aadRedirectURI`: Identifikátor URI přesměrování by měl být v tomto formátu: `msauth.<app_bundle_id>://auth`
   - `aadScopes`: Pro autorizaci je pole oborů oprávnění požadováno uživatelem. Přidejte `<Application ID URI>/user_impersonation` do pole, abyste udělili přístup ke koncovému bodu ověřování.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služby Communications Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené. Přečtěte si další informace o [vyčištění prostředků](../../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Další kroky

>[!div class="nextstepaction"]
>[Stažení ukázky z GitHubu](https://github.com/Azure-Samples/communication-services-ios-calling-hero)

Další informace najdete v následujících článcích:

- Seznámení s [používáním volání sady SDK](../../quickstarts/voice-video-calling/calling-client-samples.md)
- Další informace o [volání funkce](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>Další materiály ke čtení

- [GitHub pro komunikaci Azure](https://github.com/Azure/communication) – další příklady a informace najdete na oficiální stránce GitHubu.
- [Ukázky](./../overview.md) – další ukázky a příklady najdete na stránce s přehledem ukázek.
- [Funkce volání komunikace Azure](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features) – Další informace o volání sady iOS SDK –[volání sady SDK pro iOS komunikace Azure](https://github.com/Azure/Communication/releases/)
