---
title: Azure Mobile Engagement iOS SDK poznámky k verzi | Microsoft Docs
description: Nejnovější aktualizace a postupy pro iOS SDK pro Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a43ff0f6-90d5-4b3c-8d7a-a1db21bc776b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 0268d65d2c0ea3cefeb8f06793838bc263e443bd
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement-ios-sdk-release-notes"></a>Poznámky k verzi sady Azure Mobile Engagement iOS SDK
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 


## <a name="410-07172017"></a>4.1.0 (07/17/2017)
* Odznaky s pevnou vymazat v pozadí.
* Opravené upozornění na XCode 9 o volání rozhraní API není z hlavní fronty.
* Vyřešený nevracení paměti v hlasování Reach.
* Podpora pro iOS vyřadit 6.X. Od této verze cíl nasazení vaší aplikace musí být minimálně iOS 7.

## <a name="401-12132016"></a>4.0.1 (12/13/2016)
* Vylepšení protokolu doručení v pozadí.

## <a name="400-09122016"></a>4.0.0 (09/12/2016)
* Opravené oznámení není reakcí na zařízeních s iOS 10.
* Přestat používat XCode 7.

## <a name="324-06302016"></a>3.2.4 (06/30/2016)
* Opravené agregace mezi technické protokoly a další protokoly.

## <a name="323-06072016"></a>3.2.3 (06/07/2016)
* Pevné chybě, kde není hlášené zpětnou vazbu o doručení, když je aplikace na pozadí.
* Optimalizovaná odesílání technické protokoly.

## <a name="322-04072016"></a>3.2.2 (04/07/2016)
* Opravené chyby na zrušení požadavku HTTP, které někdy dochází k chybě.

## <a name="321-12112015"></a>3.2.1 (12/11/2015)
* Pevné zpoždění při aktivaci novou instanci aplikace podle oznámení s přímých odkazů

## <a name="320-10082015"></a>3.2.0 (10/08/2015)
* Povolit Bitcode v sadě SDK, chcete-li pracovat s **Xcode 7**.
* Opravené chyby související s oznámení v aplikaci.
* Provedené v případě nízký stav baterie. a dalších takových scénářů oznámení v aplikaci.
* Odebrat protokoly navíc konzoly generované 3. stran knihovny.

## <a name="310-08262015"></a>3.1.0 (08/26/2015)
* Opravte chyby kompatibility iOS 9 s knihovnou třetích stran. Že způsobuje dojde k chybě při odesílání dotazuje výsledky, informace o aplikaci nebo doplňující data.

## <a name="300-06192015"></a>3.0.0 (06/19/2015)
* Mobile Engagement používá bezobslužných nabízených oznámení.
* Podpora pro iOS vyřadit 4.X. Od této verze cíl nasazení vaší aplikace musí být minimálně iOS 6.

## <a name="220-05212015"></a>2.2.0 (05/21/2015)
* Id zařízení Mobile Engagement pro zařízení < iOS 6 teď vychází identifikátor GUID generovány v době instalace.

## <a name="210-04242015"></a>2.1.0 (04/24/2015)
* Přidání Swift kompatibility.
* Když kliknete na oznámení, provést akci, kterou je adresa URL vpravo po otevření aplikace.
* Přidané chybí hlavička souboru v balíčku sady SDK.
* Oprava problému při ohlašování havárií Mobile Engagement byla zakázána.

## <a name="200-02172015"></a>2.0.0 (02/17/2015)
* Počáteční verzi Azure Mobile Engagement
* Konfigurace appId/sdkKey je nahrazena konfiguraci řetězec připojení.
* Odebrat rozhraní API odesílat a přijímat libovolné protokolu XMPP zprávy z libovolné protokolu XMPP entit.
* Odebrat rozhraní API pro odesílání a přijímání zpráv mezi zařízeními.
* Vylepšení zabezpečení.
* Sledování SmartAd odebrat.
