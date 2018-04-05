---
title: Začínáme s Azure Mobile Engagementem pro nasazení aplikací Unity v systému iOS
description: Naučte se používat Azure Mobile Engagement s analytickými funkcemi a nabízenými oznámeními pro aplikace Unity nasazované v zařízeních iOS.
services: mobile-engagement
documentationcenter: unity
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 7ddfbac3-8d13-4ebe-b061-c865f357297f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-unity-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 8270a56daf8e69eede5b3d14b4ff88e0911da11a
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-unity-ios-deployment"></a>Začínáme s Azure Mobile Engagementem pro nasazení aplikací Unity v systému iOS
> [!IMPORTANT]
> Azure Mobile Engagement se vyřadí z provozu 31. 3. 2018. Tato stránka bude odstraněna krátce potom.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

V tomto tématu si ukážeme, jak používat Azure Mobile Engagement, jak porozumět používání aplikací a odesílat nabízená oznámení segmentovaným uživatelům aplikace Unity při nasazení v zařízení iOS.
Tento kurz používá jako výchozí bod klasický kurz pro Unity, hru Roll a Ball. Před pokračováním v integraci Mobile Engagementu, kterou představujeme v kurzu níže, postupujte podle kroků v tomto [kurzu](mobile-engagement-unity-roll-a-ball.md). 

V tomto kurzu budete potřebovat následující:

* [Unity Editor](http://unity3d.com/get-unity)
* [Mobile Engagement Unity SDK](https://aka.ms/azmeunitysdk)
* XCode Editor

> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-ios-get-started).
> 
> 

## <a id="setup-azme"></a>Nastavení Mobile Engagementu pro vaši aplikaci pro iOS
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Připojení aplikace k back-endu Mobile Engagementu
### <a name="import-the-unity-package"></a>Import balíčku Unity
1. Stáhněte si [balíček Mobile Engagement Unity](https://aka.ms/azmeunitysdk) a uložte jej do místního počítače. 
2. Přejděte na stránku **Assets (Prostředky) -> Import Package (Importovat balíček) -> Custom Package (Vlastní balíček)** a vyberte balíček, který jste stáhli v předchozím kroku. 
   
    ![][70] 
3. Zkontrolujte, zda jsou vybrány všechny soubory, a klikněte na tlačítko **Import**. 
   
    ![][71] 
4. Po úspěšném importu se importované soubory SDK zobrazí v projektu.  
   
    ![][72] 

### <a name="update-the-engagementconfiguration"></a>Aktualizace EngagementConfiguration
1. Ve složce SDK otevřete soubor skriptu **EngagementConfiguration** a aktualizujte **IOS\_CONNECTION\_STRING** připojovacím řetězcem, který jste dříve získali z portálu Azure.  
   
    ![][73]
2. Uložte soubor. 

### <a name="configure-the-app-for-basic-tracking"></a>Konfigurace aplikace pro základní sledování
1. Otevřete skript **PlayerController** připojený k objektu Player pro úpravy. 
2. Přidejte následující příkaz using:
   
        using Microsoft.Azure.Engagement.Unity;
3. Do metody `Start()` přidejte následující
   
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

### <a name="deploy-and-run-the-app"></a>Nasazení a spuštění aplikace
1. Připojte zařízení iOS ke svému počítači. 
2. Klikněte na položky **File (Soubor) -> Build Settings (Nastavení sestavení)** 
   
    ![][40]
3. Vyberte **iOS** a potom klikněte na **Switch Platform** (Přepnout platformu)
   
    ![][41]
   
    ![][42]
4. Klikněte na **Player settings** (Nastavení hráče) a zadejte platný identifikátor svazku. 
   
    ![][53]
5. Nakonec klikněte na **Build And Run** (Sestavit a spustit).
   
    ![][54]
6. Můžete být vyzváni k zadání názvu složky, do které se uloží balíček iOS. 
   
    ![][43]
7. Pokud všechno půjde dobře, projekt bude zkompilován a bude možné ho otevřít v aplikaci v Xcodu. 
8. Ujistěte se, že je **identifikátor svazku** v projektu správný.  
   
    ![][75]
9. Nyní spusťte aplikaci v Xcodu, aby se balíček nasadil v připojeném zařízení, a v telefonu by se měla zobrazit vaše hra Unity. 

## <a id="monitor"></a>Připojení aplikace se sledováním v reálném čase
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Povolení nabízených oznámení a zasílání zpráv v aplikaci
Mobile Engagement vám umožňuje v rámci kampaní oslovit uživatele a komunikovat s nimi prostřednictvím nabízených oznámení a zpráv v aplikacích. Tento modul se na portálu Mobile Engagement nazývá REACH.
V aplikaci není nutné provádět žádnou další konfiguraci k odběru oznámení, protože je už takto nastavená.

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[40]: ./media/mobile-engagement-unity-ios-get-started/40.png
[41]: ./media/mobile-engagement-unity-ios-get-started/41.png
[42]: ./media/mobile-engagement-unity-ios-get-started/42.png
[43]: ./media/mobile-engagement-unity-ios-get-started/43.png
[53]: ./media/mobile-engagement-unity-ios-get-started/53.png
[54]: ./media/mobile-engagement-unity-ios-get-started/54.png
[70]: ./media/mobile-engagement-unity-ios-get-started/70.png
[71]: ./media/mobile-engagement-unity-ios-get-started/71.png
[72]: ./media/mobile-engagement-unity-ios-get-started/72.png
[73]: ./media/mobile-engagement-unity-ios-get-started/73.png
[74]: ./media/mobile-engagement-unity-ios-get-started/74.png
[75]: ./media/mobile-engagement-unity-ios-get-started/75.png
