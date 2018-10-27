---
title: Měření reálných uživatelů pro Azure Traffic Manager se službou Visual Studio Mobile Center | Dokumentace Microsoftu
description: Nastavení mobilní aplikace vyvinuté pomocí sady Visual Studio Mobile Center posílat měření Real User Measurements do Traffic Manageru
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: eec13db8bdbe1f40a51df14077adb8740e977f5d
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50138408"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Jak odeslat měření Real User Measurements do Traffic Manager se službou Visual Studio Mobile Center

Můžete nastavit mobilní aplikace vyvinuté pomocí sady Visual Studio Mobile Center posílat měření Real User Measurements do Traffic Manageru pomocí následujících kroků:

>[!NOTE]
> V současné době odesílání měření Real User Measurements do Traffic manager je podporována pouze pro Android.

Pokud chcete nakonfigurovat měření Real User Measurements, potřebujete a získat klíč instrumentace vaší aplikace v rámci spuštění balíčku.

## <a name="step-1-obtain-a-key"></a>Krok 1: Získání klíče
    
Měření trvat a odeslané do Traffic Manageru z klientské aplikace jsou označeny pomocí jedinečného řetězce, volá se klíč měření reálných skutečných uživatelů (REÁLNÝCH). Můžete získat spuštění klíče pomocí webu Azure portal, rozhraní REST API nebo pomocí Powershellu nebo rozhraní příkazového řádku.

Získání klíče REÁLNÝCH pomocí webu Azure portal následujícím postupem:
   1. Z prohlížeče Přihlaste se k webu Azure portal. Pokud ještě nemáte účet, můžete se zaregistrovat k bezplatné zkušební verzi na jeden měsíc.
   2. Na panelu hledání na portálu vyhledejte název profilu Traffic Manageru, který chcete upravit a poté klikněte na profil Traffic Manageru ve výsledcích, který je zobrazeno.
   3. Na stránce profil Traffic Manageru, klikněte na tlačítko **měření Real User Measurements** pod **nastavení**.
   4. Klikněte na tlačítko **vygenerovat klíč** a vytvořte nový klíč REÁLNÝCH.
        
   ![Vygenerovat klíč měření Real User Measurements](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Obrázek 1: Vygenerovat klíč měření Real User Measurements**

   5.   Na stránce zobrazí REÁLNÝCH klíč, který je generován a fragment kódu jazyka JavaScript, které je potřeba vložit do HTML stránky.
 
   ![Kód jazyka JavaScript pro měření Real User Measurements klíč](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **Obrázek 2: Klíč měření reálných uživatelů a JavaScript měření**
 
   6. Klikněte na tlačítko **kopírování** tlačítko REÁLNÝCH klíč si zkopírujte. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>Krok 2: Instrumentace vaší aplikace v rámci spuštění balíčku sady Mobile Center SDK

Pokud jste ještě Visual Studio Mobile Center, najdete jeho [webu](https://mobile.azure.com). Podrobné pokyny k integraci sady SDK najdete v části [Začínáme se službou SDK pro Android](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android).

Pokud chcete použít měření Real User Measurements, proveďte následující postup:

1.  Přidejte sadu SDK do projektu

    Ve verzi preview SDK REÁLNÝCH ATM je potřeba explicitně odkazovat na úložiště balíčků.

    Ve vaší **app/build.gradle** souboru přidejte následující řádky:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    Ve vaší **app/build.gradle** souboru přidejte následující řádky:

    ```groovy
    dependencies {   
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. Spuštění sady SDK

    Otevřete třídu hlavní aktivity vaší aplikace a přidejte následující příkazy pro import:

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    Hledat `onCreate` zpětného volání ve stejném souboru a přidejte následující kód:

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>Další postup
- Další informace o [měření Real User Measurements](traffic-manager-rum-overview.md)
- Přečtěte si [jak funguje Traffic Manager](traffic-manager-overview.md)
- Další informace o [Mobile Center](https://docs.microsoft.com/mobile-center/)
- [Zaregistrujte](https://mobile.azure.com) pro Mobile Center
- Další informace o [metody směrování provozu](traffic-manager-routing-methods.md) podporované nástrojem Traffic Manager
- Zjistěte, jak [vytvořit profil služby Traffic Manager](traffic-manager-create-profile.md)

