---
title: Integraci sady Azure Mobile Engagement Android SDK
description: Nejnovější aktualizace a postupy pro Android SDK pro Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 585341f9-3f39-459a-af42-864e400a0128
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: a01bd74649fa28e0d86cea4ed03da80326c06234
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="release-notes"></a>Poznámky k verzi
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 


## <a name="431-07172017"></a>4.3.1 (07/17/2017)
* Opravte havárii, která se může občas nastat při volání metody `EngagementAgentUtils.isInDedicatedEngagementProcess`, které používá také `EngagementApplication` třídy.

## <a name="430-06272017"></a>4.3.0 (06/27/2017)
* 8 podpora pro Android (předchozí verze sady SDK nebudou fungovat na Android 8).
* Žádné další závislosti na podporu knihovny.
* Odebrat `EngagementFragmentActivity` třídy.
* Z důvodu [omezení provádění pozadí](https://developer.android.com/preview/features/background.html) na Android 8 může zpoždění protokoly v pozadí, dokud uživatel pracuje se zařízení, to bude mít dopad na kampaň nabízených **doručené** a  **Systémové oznámení zobrazené** statistiky je zpožděno. Pokud je zařízení v režimu spánku (oznámení bude i nadále zobrazovat, bude prstence a zavibrovat v reálném čase bez problémů).
* Z důvodu [omezení umístění pozadí](https://developer.android.com/preview/features/background-location-limits.html), reálném čase na Android 8 nebude často aktualizovat umístění pozadí.

## <a name="424-03302017"></a>4.2.4 (03/30/2017)
* Vyřešte oznámení v aplikaci barvy textu na Android 7, aby byla stejná jako starší verze Android.

## <a name="423-08102016"></a>4.2.3 (08/10/2016)
* Žádné další zámek Wi-Fi.
* Opravte zablokování při volání metody getDeviceId před init (počínaje 4.2.0 chyb).

## <a name="422-05172016"></a>4.2.2 (05/17/2016)
* Zlepšení stability.

## <a name="421-05102016"></a>4.2.1 (05/10/2016)
* Zabezpečení: zakážete webové zobrazení místního souboru přístup.
* Zabezpečení: odeberte `EngagementPreferenceActivity` třídu, která rozšiřuje zastaralé a nezabezpečená `PreferenceActivity` třídy.
* Zabezpečení: reach aktivity jsou nyní zdokumentované používat `exported="false"`, tento příznak lze také v předchozích verzích sady SDK.

## <a name="420-03112016"></a>4.2.0 (03/11/2016)
* Sada SDK je nyní licencováno v rámci MIT.
* Povolí použití identifikátoru vlastní zařízení během inicializace SDK.

## <a name="415-02012016"></a>4.1.5 (02/01/2016)
* Zlepšení stability.

## <a name="414-01262016"></a>4.1.4 (01/26/2016)
* Zlepšení stability.

## <a name="413-1292015"></a>4.1.3 (12/9/2015)
* Zlepšení stability.

## <a name="412-11252015"></a>4.1.2 (11/25/2015)
* Zlepšení stability.

## <a name="411-11042015"></a>4.1.1 (11/04/2015)
* Zlepšení stability.

## <a name="410-08252015"></a>4.1.0 (08/25/2015)
* Zpracujte nový model oprávnění pro Android M.
* Můžete teď nakonfigurovat funkce umístění za běhu místo použití `AndroidManifest.xml`.
* Oprava chyby oprávnění: Pokud používáte `ACCESS_FINE_LOCATION`, pak `ACCESS_COARSE_LOCATION` už není potřeba.
* Zlepšení stability.

## <a name="400-07062015"></a>4.0.0 (07/06/2015)
* Provedení analytickými funkcemi a nabízenými spolehlivější interní protokol změn.
* Nativního nabízení (GCM/ADM) se teď také používá pro oznámení v aplikaci, musíte nakonfigurovat přihlašovací údaje nativního nabízení pro jakýkoli typ nabízené kampaně.
* Opravte velký obrázek oznámení: byly zobrazené pouze 10s po se instaluje.
* Oprava chyby ve webovém zobrazení: Kliknutím na odkaz se také provádí výchozí adresa URL akce.
* Opravte výjimečných havárie související se správou místní úložiště.
* Opravte dynamické konfigurace řetězec správy.
* Aktualizujte smlouvy EULA.

## <a name="300-02172015"></a>3.0.0 (02/17/2015)
* Počáteční verzi Azure Mobile Engagement
* Konfigurace appId je nahrazena konfiguraci řetězec připojení.
* Odebrat rozhraní API odesílat a přijímat libovolné protokolu XMPP zprávy z libovolné protokolu XMPP entit.
* Odebrat rozhraní API pro odesílání a přijímání zpráv mezi zařízeními.
* Vylepšení zabezpečení.
* Sledování Google Play a SmartAd odebrat.

