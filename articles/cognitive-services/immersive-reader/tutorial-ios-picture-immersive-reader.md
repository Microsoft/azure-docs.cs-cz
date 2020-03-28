---
title: 'Kurz: Vytvořte aplikaci pro iOS, která pořídí fotografii a spustí ji v immersive Reader (Swift)'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte aplikaci pro iOS od začátku a přidáte obrázek do funkce Immersive Reader.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 69ff58d6cdabe49000b00afecfc6b4ad1a3f2daa
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841842"
---
# <a name="tutorial-create-an-ios-app-that-launches-the-immersive-reader-with-content-from-a-photo-swift"></a>Kurz: Vytvořte aplikaci pro iOS, která spustí Immersive Reader s obsahem z fotografie (Swift)

[Immersive Reader](https://www.onenote.com/learningtools) je inkluzivně navržený nástroj, který implementuje osvědčené techniky pro zlepšení porozumění čtení.

[Rozhraní API pro čtení služby Computer Vision Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text) detekuje textový obsah v bitové kopii pomocí nejnovějších modelů rozpoznávání společnosti Microsoft a převede identifikovaný text na strojově čitelný datový proud znaků.

V tomto kurzu vytvoříte aplikaci pro iOS od začátku a integrujete rozhraní READ API a immersive Reader pomocí sady Immersive Reader SDK. Úplný pracovní ukázka tohoto kurzu je k dispozici [zde](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/samples/picture-to-immersive-reader-swift).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* Prostředek immersive Reader nakonfigurovaný pro ověřování Azure Active Directory. Podle [těchto pokynů](./how-to-create-immersive-reader.md) se připravte. Při konfiguraci vlastností ukázkového projektu budete potřebovat některé hodnoty vytvořené zde. Uložte výstup relace do textového souboru pro budoucí použití.
* Použití této ukázky vyžaduje předplatné Azure služby Computer Vision Cognitive Service. [Vytvořte prostředek služby Computer Vision Cognitive Service na webu Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision).

## <a name="create-an-xcode-project"></a>Vytvoření projektu Xcode

Vytvořte nový projekt v Xcode.

![Nový projekt](./media/ios/xcode-create-project.png)

Zvolte **aplikace s jedním zobrazením**.

![Nová aplikace s jedním zobrazením](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>Získejte SDK CocoaPod
Nejjednodušší způsob, jak používat Immersive Reader SDK je přes CocoaPods. Instalace pomocí kakaopodů:
1. [Nainstalujte KakaoPods](http://guides.cocoapods.org/using/getting-started.html) - Postupujte podle návodu, jak začít instalovat Kakaopody.
2. Vytvořte podfile `pod init` spuštěním v kořenovém adresáři projektu Xcode.
3.  Přidejte KakaoPod do podsouboru `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'`přidáním . Podfile by měl vypadat takto, přičemž název cíle nahrazuje obrázek k pohlcujícímu čtenáři:
 ```ruby
  platform :ios, '9.0'

  target 'picture-to-immersive-reader-swift' do
  use_frameworks!
  # Pods for picture-to-immersive-reader-swift
  pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'
  end
```
4. V terminálu, v adresáři projektu Xcode, `pod install` spusťte příkaz k instalaci immersive Reader SDK pod.
5. Přidejte `import immersive_reader_sdk` do všech souborů, které je třeba odkazovat na sadu SDK.
6. Otevřete `.xcworkspace` soubor a ne soubor, `.xcodeproj` ujistěte se, že projekt otevřete.

## <a name="acquire-an-azure-ad-authentication-token"></a>Získání ověřovacího tokenu Azure AD

Potřebujete některé hodnoty z předpokladu konfigurace azure ad konfigurace výše uvedený krok pro tuto část. Vraťte se k textovému souboru, který jste uložili z této relace.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

V hlavní složce projektu, která obsahuje soubor ViewController.swift, vytvořte soubor třídy Swift s názvem Constants.swift. Třídu nahraďte následujícím kódem a případně přidejte hodnoty. Uchovávejte tento soubor jako místní soubor, který existuje pouze v počítači, a ujistěte se, že tento soubor není potvrzen do správy zdrojového kódu, protože obsahuje tajné klíče, které by neměly být zveřejněny. Doporučujeme, abyste v aplikaci neuchovávali tajemství. Místo toho doporučujeme použít back-endovou službu k získání tokenu, kde tajné klíče mohou být uchovávány mimo aplikaci a mimo zařízení. Koncový bod rozhraní API back-endu by měl být zabezpečen za nějakou formou ověřování (například [OAuth),](https://oauth.net/2/)aby se zabránilo neoprávněným uživatelům v získávání tokenů, které by bylo možné použít proti vaší službě Immersive Reader a fakturaci; že práce je nad rámec tohoto kurzu.

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>Nastavení spuštění aplikace bez scénáře

Otevřete soubor AppDelegate.swift a nahraďte soubor následujícím kódem.

## <a name="add-functionality-for-taking-and-uploading-photos"></a>Přidání funkcí pro pořizování a nahrávání fotografií

Přejmenujte ViewController.swift na PictureLaunchViewController.swift a nahraďte soubor následujícím kódem.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

Nastavte schéma archivu v Xcode výběrem simulátoru nebo cíle zařízení.
![Schéma archivu](./media/ios/xcode-archive-scheme.png)<br/>
![Výběr cíle](./media/ios/xcode-select-target.png)

V Xcode stiskněte Ctrl + R nebo klikněte na tlačítko play pro spuštění projektu a aplikace by se měla spustit na určeném simulátoru nebo zařízení.

V aplikaci byste měli vidět:

![Ukázková aplikace](./media/ios/picture-to-immersive-reader-ipad-app.png)

Uvnitř aplikace pořiďte nebo nahrajte fotografii textu stisknutím tlačítka "Pořídit fotografii" nebo "Vybrat fotografii z knihovny" a immersive Reader se pak spustí s zobrazením textu z fotografie.

![Asistivní čtečka](./media/ios/picture-to-immersive-reader-ipad.png)

## <a name="next-steps"></a>Další kroky

* Seznamte se s [referenční sadou Immersive Reader SDK](./reference.md)
