---
title: Komunikační služby Azure – známé problémy
description: Přečtěte si o známých problémech s komunikačními službami Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/03/2020
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: e9e4b747d9d0ab39a1d0ecef6cf45e4cc0f9e2c5
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628136"
---
# <a name="known-issues-azure-communication-services"></a>Známé problémy: komunikační služby Azure

Tento článek poskytuje informace o známých problémech spojených s komunikačními službami Azure.

## <a name="video-streaming-quality-on-chromeandroid"></a>Kvalita streamování videa v Chrome/Androidu 

Výkon streamování videa může být degradován v Chrome Androidu.

### <a name="possible-causes"></a>Možné příčiny
Kvalita vzdálených datových proudů závisí na rozlišení vykreslování na straně klienta, které bylo použito k inicializaci tohoto datového proudu. Když se přihlásíte k odběru vzdáleného datového proudu, příjemce určí své vlastní rozlišení založené na dimenzích vykreslování odesílatele na straně klienta.

## <a name="bluetooth-headset-microphones-are-not-detected"></a>Mikrotelefony s mikrofonem Bluetooth se nezjistily

Může docházet k problémům s připojením sluchátka s mikrofonem Bluetooth ke volání komunikačních služeb.

### <a name="possible-causes"></a>Možné příčiny
K dispozici není možnost vybrat mikrofon Bluetooth v iOS.


## <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Opakované přepínání grafických zařízení může způsobit, že se streamování videa dočasně zastaví.

Přepínání mezi grafickými zařízeními může způsobit, že se datový proud videa během získání datového proudu z vybraného zařízení zastaví.

### <a name="possible-causes"></a>Možné příčiny
Streamování mezi mediálními zařízeními a přepínání mezi nimi je výpočetní výkon. Přepínání často může způsobit snížení výkonu. Vývojářům Doporučujeme zastavit jeden datový proud zařízení před tím, než začnete s jiným.
