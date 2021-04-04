---
title: Měření reálných uživatelů s Visual Studio Mobile Center – Azure Traffic Manager
description: Nastavte mobilní aplikaci vyvinutou pomocí aplikace Visual Studio Mobile Center k odeslání Měření reálných uživatelů do Traffic Manager
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: duau
ms.custom: devx-track-js
ms.openlocfilehash: f9e8cdd3eb5c9f441444683fb5efaccc880b2757
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98184607"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Postup odeslání Měření reálných uživatelů pro Traffic Manager v aplikaci Visual Studio Mobile Center

Můžete nastavit mobilní aplikaci vyvinutou pomocí aplikace Visual Studio Mobile Center k odeslání Měření reálných uživatelů k Traffic Manager pomocí následujících kroků:

>[!NOTE]
> Odesílání Měření reálných uživatelů do Traffic Manageru se v současné době podporuje jenom pro Android.

Pokud chcete nakonfigurovat Měření reálných uživatelů, potřebujete získat klíč a instrumentovat svoji aplikaci pomocí balíčku rumu.

## <a name="step-1-obtain-a-key"></a>Krok 1: získání klíče
    
Měření, která provedete a odesíláte Traffic Manager z klientské aplikace, jsou identifikována službou pomocí jedinečného řetězce, který se nazývá klíč Měření reálných uživatelů (RUM). Klíč rumu můžete získat pomocí Azure Portal, REST API nebo pomocí rozhraní PowerShell/CLI.

K získání klíče rumu pomocí Azure Portal použijte následující postup:
1. V prohlížeči se přihlaste k webu Azure Portal. Pokud ještě účet nemáte, můžete si zaregistrovat zkušební verzi na měsíc zdarma.
2. Na panelu hledání na portálu vyhledejte název profilu služby Traffic Manager, který chcete upravit, a pak na tento profil služby Traffic Manager klikněte v zobrazených výsledcích.
3. Na stránce Traffic Manager profil klikněte v části **Nastavení** na **měření reálných uživatelů** .
4. Klikněte na **vygenerovat klíč** a vytvořte nový klíč rumu.
        
   ![Generovat klíč Měření reálných uživatelů](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Obrázek 1: Měření reálných uživatelů generování klíčů**

5. Stránka zobrazuje vygenerovaný klíč rumu a fragment kódu JavaScriptu, který musí být vložen do stránky HTML.
 
   ![Kód JavaScriptu pro Měření reálných uživatelů klíč](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **Obrázek 2: Měření reálných uživatelů klíč a měření JavaScriptu**
 
6. Kliknutím na tlačítko **Kopírovat** ZKOPÍRUJTE klíč rumu. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>Krok 2: instrumentace aplikace pomocí balíčku rumu sady Mobile Center SDK

Pokud s Visual Studio Mobile Center začínáte, přejděte na jeho [Web](https://mobile.azure.com). Podrobné pokyny k integraci sady SDK najdete v tématu [Začínáme s Android SDK](/mobile-center/sdk/getting-started/Android).

Pokud chcete použít Měření reálných uživatelů, proveďte následující postup:

1.  Přidat sadu SDK do projektu

    V rámci verze Preview technologie ATM rumu SDK musíte explicitně odkazovat na úložiště balíčků.

    V souboru **App/Build. Gradle** přidejte následující řádky:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    V souboru **App/Build. Gradle** přidejte následující řádky:

    ```groovy
    dependencies {
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. Spustit sadu SDK

    Otevřete hlavní třídu aktivity vaší aplikace a přidejte následující příkazy importu:

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
- Další informace o [měření reálných uživatelů](traffic-manager-rum-overview.md)
- Informace [o tom, jak Traffic Manager funguje](traffic-manager-overview.md)
- Další informace o [Mobile Center](/mobile-center/)
- [Zaregistrovat](https://mobile.azure.com) se v Mobile Center
- Další informace o [metodách směrování provozu](traffic-manager-routing-methods.md) , které podporuje Traffic Manager
- Informace o tom, jak [vytvořit profil Traffic Manager](./quickstart-create-traffic-manager-profile.md)