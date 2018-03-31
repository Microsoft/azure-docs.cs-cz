---
title: Integraci sady Android SDK pro Azure Mobile Engagement
description: Popisuje, jak integrovat Azure Mobile Engagement SDK do aplikace pro Android
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a91ed04f-f3ce-4692-a6dd-b56a28d7dee8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo;ricksal
ms.openlocfilehash: d02bcfc7521df9c18edc534e679e597ad2328e6f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="android-sdk-integration-for-azure-mobile-engagement"></a>Integraci sady Android SDK pro Azure Mobile Engagement
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

> [!div class="op_single_selector"]
> * [Univerzální platforma Windows](mobile-engagement-windows-store-sdk-overview.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
> * [iOS](mobile-engagement-ios-sdk-overview.md)
> * [Android](mobile-engagement-android-sdk-overview.md)
> 
> 

Tento dokument popisuje všech integrace a konfigurace dostupných možnostech pro Azure Mobile Engagement Android SDK.

## <a name="prerequisites"></a>Požadavky
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="advanced-features"></a>Pokročilé funkce
### <a name="reporting-features"></a>Funkce vytváření sestav
Můžete přidat tyto funkce:

1. [Rozšířené možnosti vytváření sestav](mobile-engagement-android-advanced-reporting.md)
2. [Možnosti zasílání sestav umístění](mobile-engagement-android-location-reporting.md)
3. [Rozšířené možnosti konfigurace](mobile-engagement-android-advanced-configuration.md)

### <a name="notifications"></a>Oznámení:
[Postup při integraci Reach (oznámení) v aplikacích pro Android](mobile-engagement-android-integrate-engagement-reach.md)

1. Zasílání zpráv cloudu Google (GCM): [postup GCM integrovat Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
2. Službu Amazon Device Messaging (ADM): [postup ADM integrovat Mobile Engagement](mobile-engagement-android-adm-integrate.md)

### <a name="tag-plan-implementation"></a>Implementace plánu značky:
[Jak používat rozšířené Mobile Engagement označování rozhraní API v aplikacích pro Android](mobile-engagement-android-use-engagement-api.md)

## <a name="release-notes"></a>Poznámky k verzi

### <a name="431-07172017"></a>4.3.1 (07/17/2017)
* Opravte havárii, která se může občas nastat při volání metody `EngagementAgentUtils.isInDedicatedEngagementProcess`, které používá také `EngagementApplication` třídy.

### <a name="430-06272017"></a>4.3.0 (06/27/2017)
* 8 podpora pro Android (předchozí verze sady SDK nebudou fungovat na Android 8).
* Žádné další závislosti na podporu knihovny.
* Odebrat `EngagementFragmentActivity` třídy.
* Z důvodu [omezení provádění pozadí](https://developer.android.com/preview/features/background.html) na Android 8 může zpoždění protokoly v pozadí, dokud uživatel pracuje se zařízení, to bude mít dopad na kampaň nabízených **doručené** a  **Systémové oznámení zobrazené** statistiky je zpožděno. Pokud je zařízení v režimu spánku (oznámení bude i nadále zobrazovat, bude prstence a zavibrovat v reálném čase bez problémů).
* Z důvodu [omezení umístění pozadí](https://developer.android.com/preview/features/background-location-limits.html), reálném čase na Android 8 nebude často aktualizovat umístění pozadí.

Všechny verze najdete v tématu [dokončení poznámky k verzi](mobile-engagement-android-release-notes.md).

## <a name="upgrade-procedures"></a>Postupy upgradu
Pokud již máte integrovanou starší verze naše sady SDK do své aplikace, projděte si [postupy upgradu](mobile-engagement-android-upgrade-procedure.md).

