---
title: Měření reálných uživatelů pomocí mobilního centra Visual Studio – Azure Traffic Manager
description: Nastavení mobilní aplikace vyvinuté pomocí aplikace Visual Studio Mobile Center pro odesílání skutečných uživatelských měření do Traffic Manageru
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: 3106334e1fb3e3000cbd09e00e413b34a1b55e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939190"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Jak odeslat skutečná měření uživatelů do Traffic Manageru pomocí mobilního centra Sady Visual Studio

Mobilní aplikace vyvinutá pomocí visual studia Mobile Center můžete nastavit pro odesílání měření skutečných uživatelů do Traffic Manageru podle následujících kroků:

>[!NOTE]
> V současné době je odesílání měření skutečných uživatelů do správce provozu podporováno pouze pro Android.

Chcete-li konfigurovat měření reálných uživatelů, musíte získat klíč a instrumentovat aplikaci s balíčkem RUM.

## <a name="step-1-obtain-a-key"></a>Krok 1: Získání klíče
    
Měření, která provedoute a odešlete do Traffic Manageru z klientské aplikace, jsou službou identifikována pomocí jedinečného řetězce nazývaného Klíč měření skutečných uživatelů (RUM). Klíč RUM můžete získat pomocí portálu Azure, rozhraní REST API nebo pomocí rozhraní PowerShell / CLI.

Chcete-li získat klíč RUM pomocí portálu Azure pomocí následujícího postupu:
1. V prohlížeči se přihlaste k webu Azure Portal. Pokud ještě účet nemáte, můžete si zaregistrovat zkušební verzi na měsíc zdarma.
2. Na panelu hledání na portálu vyhledejte název profilu služby Traffic Manager, který chcete upravit, a pak na tento profil služby Traffic Manager klikněte v zobrazených výsledcích.
3. Na stránce Profil Traffic Manageru klikněte v části **Nastavení**na **Měření skutečných uživatelů** .
4. Chcete-li vytvořit nový klíč RUM, klepněte na **tlačítko Generovat klíč.**
        
   ![Klíč Generovat skutečná měření uživatelů](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Obrázek 1: Generování klíčů skutečných měření uživatelů**

5. Na stránce se zobrazí generovaný klíč RUM a fragment kódu JavaScriptu, který je třeba vložit do stránky HTML.
 
   ![Kód Javascriptu pro klíč Měření reálných uživatelů](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **Obrázek 2: Klíč pro měření skutečných uživatelů a javascript měření**
 
6. Kliknutím na tlačítko **Kopírovat** zkopírujte klíč RUM. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>Krok 2: Instrumentujte svou aplikaci s balíčkem RUM sady Mobile Center SDK

Pokud s mobilním centrem Visual Studia tečujete, navštivte jeho [web](https://mobile.azure.com). Podrobné pokyny k integraci sady SDK najdete [v tématu Začínáme s sadou Android SDK](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android).

Chcete-li použít měření reálných uživatelů, proveďte následující postup:

1.  Přidání sady SDK do projektu

    Během náhledu sady ATM RUM SDK je třeba explicitně odkazovat na úložiště balíčků.

    Do souboru **app/build.gradle** přidejte následující řádky:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    Do souboru **app/build.gradle** přidejte následující řádky:

    ```groovy
    dependencies {
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. Spuštění sady SDK

    Otevřete hlavní třídu aktivit y aplikace a přidejte následující příkazy importu:

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    Vyhledejte `onCreate` zpětné volání ve stejném souboru a přidejte následující kód:

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>Další kroky
- Další informace o [měření chudiny reálných uživatelů](traffic-manager-rum-overview.md)
- [Zjistěte, jak Traffic Manager funguje](traffic-manager-overview.md)
- Další informace o [mobilním centru](https://docs.microsoft.com/mobile-center/)
- [Zaregistrujte se](https://mobile.azure.com) do mobilního centra
- Další informace o [metodách směrování provozu](traffic-manager-routing-methods.md) podporovaných traffic managerem
- Přečtěte si, jak [vytvořit profil Traffic Manageru](traffic-manager-create-profile.md)

