---
title: Reálný uživatel měření do Azure Traffic Manageru s Visual Studio Mobile Center | Microsoft Docs
description: Nastavení mobilní aplikace vyvinuté pomocí Visual Studio Mobile Center odeslat reálného měření uživatele do Traffic Manageru
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 893e84b07b365fb0b534e0ddc021b2249c4174cf
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30181006"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Postup odesílání reálného měření uživatele do Traffic Manageru s Visual Studio Mobile Center

Můžete nastavit mobilní aplikace vyvinuté pomocí Visual Studio Mobile Center k odeslání do Traffic Manageru reálného měření uživatele pomocí následujících kroků:

>[!NOTE]
> V současné době odesílání reálného měření uživatele do Traffic manager je podporována pouze pro Android.

Ke konfiguraci reálného měření uživatele, musíte a získat klíč instrumentace vaší aplikace pomocí RUMU balíčku.

## <a name="step-1-obtain-a-key"></a>Krok 1: Získání klíče
    
Měření trvat a odeslaných do Traffic Manageru z klientské aplikace jsou určeny pomocí jedinečného řetězce, nazývá klíč měření skutečné uživatele (RUM). Můžete získat RUMU klíče pomocí portálu Azure, rozhraní REST API nebo pomocí prostředí PowerShell / rozhraní CLI.

Získat klíč RUM pomocí portálu Azure, následujícím postupem:
   1. V prohlížeči Přihlaste se k portálu Azure. Pokud účet nemáte, můžete zaregistrovat k bezplatné zkušební verzi jeden měsíc.
   2. V panelu vyhledávání na portálu, vyhledejte název profilu Traffic Manager, který chcete upravit a pak klikněte na profil služby Traffic Manager ve výsledcích, zobrazené.
   3. Na stránce profil Traffic Manager, klikněte na tlačítko **reálného měření uživatele** pod **nastavení**.
   4. Klikněte na tlačítko **vygenerovat klíč** se vytvořit nový klíč RUM.
        
   ![Vygenerování klíče reálného měření uživatele](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Obrázek 1: Generování klíče reálného měření uživatele**

   5.   Na stránce zobrazuje RUM klíč, který se generuje a fragment kódu jazyka JavaScript, který potřebuje má být vložen do stránku HTML.
 
   ![Kód jazyka JavaScript pro klíč reálného měření uživatele](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **Obrázek 2: Klíč měření reálný uživatel a měření JavaScript**
 
   6. Klikněte **kopie** tlačítko zkopírujte klíč RUM. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>Krok 2: Instrumentace vaší aplikace pomocí RUMU balíčku sady Mobile Center SDK

Pokud jste pro Visual Studio Mobile Center nové, navštivte jeho [webu](https://mobile.azure.com). Podrobné pokyny k integraci sady SDK najdete v části [Začínáme se službou SDK pro Android](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android).

Pokud chcete používat reálného měření uživatele, proveďte následující postup:

1.  Přidejte sadu SDK do projektu

    Ve verzi Preview sady ATM RUM SDK je třeba explicitně odkazovat úložiště balíčků.

    Ve vašem **app/build.gradle** souboru přidejte následující řádky:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    Ve vašem **app/build.gradle** souboru přidejte následující řádky:

    ```groovy
    dependencies {   
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. Spusťte sadu SDK

    Otevřete vaší aplikace hlavní třída aktivit a přidejte následující příkazy pro import:

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    Vyhledejte `onCreate` zpětného volání ve stejném souboru a přidejte následující kód:

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>Další postup
- Další informace o [reálného měření uživatele](traffic-manager-rum-overview.md)
- Další informace [fungování Traffic Manager](traffic-manager-overview.md)
- Další informace o [Mobile Center](https://docs.microsoft.com/mobile-center/)
- [Zaregistrujte si](https://mobile.azure.com) pro Mobile Center
- Další informace o [metody směrování provozu](traffic-manager-routing-methods.md) podporované nástrojem Traffic Manager
- Zjistěte, jak [vytvořit profil správce provozu](traffic-manager-create-profile.md)

