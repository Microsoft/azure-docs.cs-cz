---
title: 'Rychlý start: Vytvořte aplikaci pro iOS, která spustí moderní čtečku (SWIFT).'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vytvoříte aplikaci pro iOS od začátku a přidáte funkce moderního čtečky.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 08/01/2019
ms.author: metan
ms.openlocfilehash: 64fd7508244f2123cc10ee96ec4f805050aedfaa
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899531"
---
# <a name="quickstart-create-an-ios-app-that-launches-the-immersive-reader-swift"></a>Rychlý start: Vytvořte aplikaci pro iOS, která spustí moderní čtečku (SWIFT).

[Moderní čtečka](https://www.onenote.com/learningtools) je celkově navržený nástroj, který implementuje osvědčené techniky pro zlepšení porozumění čtení.

V tomto rychlém startu vytvoříte aplikaci pro iOS od začátku a integrujete moderní čtečku pomocí sady moderního čtecího zařízení. Kompletní pracovní vzorek tohoto rychlého startu je k dispozici [zde](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/samples/quickstart-swift).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* Prostředek moderního čtecího zařízení nakonfigurovaný pro ověřování Azure Active Directory (Azure AD). Pomocí [těchto pokynů](./azure-active-directory-authentication.md) si můžete nastavit. Při konfiguraci ukázkových vlastností projektu budete potřebovat některé z hodnot, které jsou zde vytvořeny. Uložte výstup vaší relace do textového souboru pro budoucí referenci.

## <a name="create-an-xcode-project"></a>Vytvoření projektu Xcode

Vytvoří nový projekt v Xcode.

![Nový projekt](./media/ios/xcode-create-project.png)

Vyberte **aplikaci s jedním zobrazením**.

![Nová aplikace s jedním zobrazením](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>Získat CocoaPod sady SDK
Nejjednodušší způsob, jak použít sadu moderního čtecího zařízení sady SDK, je prostřednictvím CocoaPods. Instalace prostřednictvím Cocoapods:
1. [Instalace Cocoapods](http://guides.cocoapods.org/using/getting-started.html) – postupujte podle příručky Začínáme a nainstalujte Cocoapods.
2. Vytvořte souboru podfile spuštěním `pod init` v kořenovém adresáři vašeho projektu Xcode.
3.  Přidejte CocoaPod do svého souboru podfile `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'`přidáním. Váš Pofile by měl vypadat jako v následujícím seznamu s názvem vašeho cíle, který nahrazuje rychlé spuštění-SWIFT:
 ```ruby
  platform :ios, '9.0'

  target 'quickstart-swift' do
  use_frameworks!
  # Pods for quickstart-swift
  pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'
  end
```
4. V terminálu v adresáři projektu Xcode spusťte příkaz `pod install` pro instalaci sady moderní čtečka SDK pod.
5. Přidejte `import immersive_reader_sdk` do všech souborů, které vyžadují odkaz na sadu SDK.
6. Zajistěte otevření projektu otevřením `.xcworkspace` souboru, `.xcodeproj` nikoli souboru.

## <a name="acquire-an-azure-ad-authentication-token"></a>Získání ověřovacího tokenu Azure AD

Pro tuto část budete potřebovat některé hodnoty z kroku podmínka konfigurace ověřování Azure AD výše. Vraťte se zpět k textovému souboru, který jste si uložili do této relace.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

V hlavní složce projektu, která obsahuje soubor soubor viewcontroller. SWIFT, vytvořte soubor třídy SWIFT s názvem konstanty. SWIFT. Nahraďte třídu následujícím kódem a přidejte do svých hodnot, kde je to možné. Uložte tento soubor jako místní soubor, který existuje pouze v počítači, a ujistěte se, že tento soubor nechcete potvrdit do správy zdrojových kódů, protože obsahuje tajné kódy, které by neměly být zveřejněny. Doporučuje se, abyste v aplikaci nezachovali tajné klíče. Místo toho doporučujeme použít back-end službu k získání tokenu, kde tajné klíče lze uchovávat mimo aplikaci a z zařízení. Koncový bod rozhraní API back-endu by měl být zabezpečený za nějakým druhem ověřování (například [OAuth](https://oauth.net/2/)), aby se zabránilo neoprávněným uživatelům v získání tokenů k použití proti službě s moderní čtečkou a fakturaci. Tato práce překračuje rámec tohoto rychlého startu.

[!code-swift[Constants](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/Constants.swift)]

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>Nastavení spuštění aplikace bez scénáře

Otevřete AppDelegate. SWIFT a nahraďte soubor následujícím kódem.

[!code-swift[AppDelegate](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/AppDelegate.swift)]

## <a name="create-the-launch-view-controller-and-add-sample-content"></a>Vytvoření kontroleru zobrazení pro spuštění a přidání ukázkového obsahu

Přejmenujte soubor viewcontroller. SWIFT na LaunchViewController. SWIFT a nahraďte soubor následujícím kódem.

[!code-swift[LaunchViewController](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/LaunchViewController.swift)]

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

Nastavte schéma archivu v Xcode tak, že vyberete simulátor nebo cíl zařízení.
![Schéma archivu](./media/ios/xcode-archive-scheme.png)<br/>
![Vybrat cíl](./media/ios/xcode-select-target.png)

V Xcode stiskněte kombinaci kláves CTRL + R nebo klikněte na tlačítko Přehrát a spusťte projekt a aplikace by se měla spustit na zadaném simulátoru nebo zařízení.

V aplikaci byste měli vidět:

![Ukázková aplikace](./media/ios/sample-app-ipad.png)

Po kliknutí na tlačítko "moderní čtenář" se zobrazí moderní čtečka, která se spustí s obsahem aplikace.

![Asistivní čtečka](./media/ios/immersive-reader-ipad.png)

## <a name="next-steps"></a>Další kroky

* Prozkoumejte [sadu moderního čtecího zařízení pro iOS SDK](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS) a [referenční informace pro moderní ČTEČKu iOS SDK](./ios-reference.md)
