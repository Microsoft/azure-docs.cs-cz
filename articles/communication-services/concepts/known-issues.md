---
title: Komunikační služby Azure – časté otázky/známé problémy
description: Další informace o komunikačních službách Azure
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/03/2020
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: e58335f1e266af651eb5867ca98e9ec979803b94
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101655883"
---
# <a name="faq--known-issues"></a>Nejčastější dotazy/známé problémy
Tento článek poskytuje informace o známých problémech a nejčastějších dotazech týkajících se komunikačních služeb Azure.

## <a name="faq"></a>Časté otázky

### <a name="why-is-the-quality-of-my-video-degraded"></a>Proč kvalita videa je degradovaná?

Kvalita datových proudů videí je určena velikostí nástroje pro vykreslování na straně klienta, který se použil k inicializaci tohoto datového proudu. Když se přihlásíte k odběru vzdáleného datového proudu, příjemce určí své vlastní rozlišení založené na dimenzích vykreslování odesílatele na straně klienta.

### <a name="why-is-it-not-possible-to-enumerateselect-micspeaker-devices-on-safari"></a>Proč není možné vytvořit výčet a vybrat zařízení MIC/mluvčí v Safari?

Aplikace nemůžou vytvořit výčet/mluvčí zařízení (jako Bluetooth) v Safari iOS/iPadu. Jedná se o omezení operačního systému – existuje vždycky jenom jedno zařízení.

Pro Safari v MacOS – aplikace nemůže zobrazit výčet/vybrat mluvčího prostřednictvím komunikačních služeb Správce zařízení – tyto musí být vybrány prostřednictvím operačního systému. Pokud používáte Chrome v MacOS, může aplikace zobrazit nebo vybrat zařízení prostřednictvím komunikačních služeb Správce zařízení.

## <a name="known-issues"></a>Známé problémy

V této části najdete informace o známých problémech spojených s komunikačními službami Azure.

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Opakované přepínání grafických zařízení může způsobit, že se streamování videa dočasně zastaví.

Přepínání mezi grafickými zařízeními může způsobit, že se datový proud videa během získání datového proudu z vybraného zařízení zastaví.

#### <a name="possible-causes"></a>Možné příčiny
Streamování mezi mediálními zařízeními a přepínání mezi nimi je výpočetní výkon. Přepínání často může způsobit snížení výkonu. Vývojářům Doporučujeme zastavit jeden datový proud zařízení před tím, než začnete s jiným.
