---
title: 'Kurz: Vytvoření aplikace pro iOS, která přijímá fotografii a spustí ji v moderní čtečce (SWIFT)'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte aplikaci pro iOS od začátku a přidáte obrázek do funkce moderního čtečky.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metang
ms.openlocfilehash: 73084f5116083b190b6fa31e504ffc5e8f6f39f6
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636489"
---
# <a name="tutorial-create-an-ios-app-that-launches-the-immersive-reader-with-content-from-a-photo-swift"></a>Kurz: Vytvoření aplikace pro iOS, která spustí moderní čtečku s obsahem z fotky (SWIFT)

[Moderní čtečka](https://www.onenote.com/learningtools) je celkově navržený nástroj, který implementuje osvědčené techniky pro zlepšení porozumění čtení.

[Rozhraní API pro čtení Počítačové zpracování obrazu Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text) detekuje textový obsah v imagi pomocí nejnovějších modelů rozpoznávání od Microsoftu a převede identifikovaný text na datový proud znaků, který je strojově čitelný.

V tomto kurzu vytvoříte aplikaci pro iOS od začátku a integrujete rozhraní API pro čtení a moderní čtečku s využitím sady moderní čtečky. Kompletní pracovní ukázka tohoto kurzu je k dispozici [zde](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/ios).

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/cognitive-services/), ještě než začnete.

## <a name="prerequisites"></a>Předpoklady

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* Prostředek moderního čtecího zařízení nakonfigurovaný pro ověřování Azure Active Directory. Pomocí [těchto pokynů](./how-to-create-immersive-reader.md) si můžete nastavit. Při konfiguraci ukázkových vlastností projektu budete potřebovat některé z hodnot, které jsou zde vytvořeny. Uložte výstup vaší relace do textového souboru pro budoucí referenci.
* Použití této ukázky vyžaduje předplatné Azure pro službu Počítačové zpracování obrazu pro rozpoznávání. [Vytvořte prostředek služby počítačové zpracování obrazu vnímání v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision).

## <a name="create-an-xcode-project"></a>Vytvoření projektu Xcode

Vytvoří nový projekt v Xcode.

![Nový projekt](./media/ios/xcode-create-project.png)

Vyberte **aplikaci s jedním zobrazením** .

![Nová aplikace s jedním zobrazením](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>Získat CocoaPod sady SDK
Nejjednodušší způsob, jak použít sadu moderního čtecího zařízení sady SDK, je prostřednictvím CocoaPods. Instalace prostřednictvím Cocoapods:
1. [Instalace CocoaPods](http://guides.cocoapods.org/using/getting-started.html) – postupujte podle příručky Začínáme a nainstalujte CocoaPods.
2. Vytvořte souboru podfile spuštěním `pod init` v kořenovém adresáři vašeho projektu Xcode.
3.  Přidejte CocoaPod do svého souboru podfile přidáním `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'` . Váš souboru podfile by měl vypadat jako na následujícím obrázku s názvem vašeho cíle, který nahrazuje obrázkový a rychlý čtenář – SWIFT:
 ```ruby
  platform :ios, '9.0'

  target 'picture-to-immersive-reader-swift' do
  use_frameworks!
  # Pods for picture-to-immersive-reader-swift
  pod 'immersive-reader-sdk', :git => 'https://github.com/microsoft/immersive-reader-sdk.git'
  end
```
4. V terminálu v adresáři projektu Xcode spusťte příkaz `pod install` pro instalaci sady moderní čtečka SDK pod.
5. Přidejte `import immersive_reader_sdk` do všech souborů, které vyžadují odkaz na sadu SDK.
6. Zajistěte otevření projektu otevřením `.xcworkspace` souboru, nikoli `.xcodeproj` souboru.

## <a name="acquire-an-azure-ad-authentication-token"></a>Získání ověřovacího tokenu Azure AD

Pro tuto část budete potřebovat některé hodnoty z kroku podmínka konfigurace ověřování Azure AD výše. Vraťte se zpět k textovému souboru, který jste si uložili do této relace.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

V hlavní složce projektu, která obsahuje soubor soubor viewcontroller. SWIFT, vytvořte soubor třídy SWIFT s názvem konstanty. SWIFT. Nahraďte třídu následujícím kódem a přidejte do svých hodnot, kde je to možné. Uložte tento soubor jako místní soubor, který existuje pouze v počítači, a ujistěte se, že tento soubor nechcete potvrdit do správy zdrojových kódů, protože obsahuje tajné kódy, které by neměly být zveřejněny. Doporučuje se, abyste v aplikaci nezachovali tajné klíče. Místo toho doporučujeme použít back-end službu k získání tokenu, kde tajné klíče lze uchovávat mimo aplikaci a z zařízení. Koncový bod rozhraní API back-endu by měl být zabezpečený za nějakým druhem ověřování (například [OAuth](https://oauth.net/2/)), aby se zabránilo neoprávněným uživatelům v získání tokenů k použití proti službě s moderní čtečkou a fakturaci. Tato práce překračuje rámec tohoto kurzu.

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>Nastavení spuštění aplikace bez scénáře

Otevřete AppDelegate. SWIFT a nahraďte soubor následujícím kódem.

## <a name="add-functionality-for-taking-and-uploading-photos"></a>Přidání funkce pro pořízení a nahrávání fotek

Přejmenujte soubor viewcontroller. SWIFT na PictureLaunchViewController. SWIFT a nahraďte soubor následujícím kódem.

## <a name="build-and-run-the-app"></a>Sestavte a spusťte aplikaci.

Nastavte schéma archivu v Xcode tak, že vyberete simulátor nebo cíl zařízení.
![Schéma archivu](./media/ios/xcode-archive-scheme.png)<br/>
![Výběr cíle](./media/ios/xcode-select-target.png)

V Xcode stiskněte kombinaci kláves CTRL + R nebo klikněte na tlačítko Přehrát a spusťte projekt a aplikace by se měla spustit na zadaném simulátoru nebo zařízení.

V aplikaci byste měli vidět:

![Ukázková aplikace](./media/ios/picture-to-immersive-reader-ipad-app.png)

V aplikaci můžete přebírat nebo nahrajte fotografii textu tak, že kliknete na tlačítko "pořídit fotografii" nebo "zvolte fotografii z knihovny" a moderní čtečka pak začne zobrazovat text z fotografie.

![Asistivní čtečka](./media/ios/picture-to-immersive-reader-ipad.png)

## <a name="next-steps"></a>Další kroky

* Prozkoumejte [referenční materiály k sadě pro moderní čtečku](./reference.md)
