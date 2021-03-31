---
title: Live video Analytics o kvótách IoT Edge a omezeních – Azure
description: Tento článek popisuje Live video Analytics o IoT Edge kvót a omezeních.
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 68c7b91bb1051348b5a8e52f841d443894f0a632
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97400520"
---
# <a name="quotas-and-limitations"></a>Kvóty a omezení

V tomto článku se vyčíslují kvóty a omezení pro živé analýzy videí v modulu IoT Edge.

## <a name="maximum-period-of-disconnected-use"></a>Maximální doba odpojeného použití

Modul Edge může tolerovat dočasnou ztrátu připojení k Internetu. Pokud modul zůstane odpojený déle než 36 hodin, deaktivuje všechny spuštěné instance grafů. Všechna další volání přímé metody budou zablokována.

Aby bylo možné obnovit činnost modulu Edge do provozního stavu, budete muset obnovit připojení k Internetu, aby modul mohl úspěšně komunikovat s účtem Azure Media Service.

## <a name="maximum-number-of-graph-instances"></a>Maximální počet instancí grafu

Podporuje se maximálně 1000 instancí grafu na modul (vytvořené prostřednictvím GraphInstanceSet).

## <a name="maximum-number-of-graph-topologies"></a>Maximální počet topologií grafu

Podporuje se maximálně 50 topologií grafů na modul (vytvořené prostřednictvím GraphTopologySet).

## <a name="limitations-on-graph-topologies-at-preview"></a>Omezení pro topologie grafu ve verzi Preview

V rámci verze Preview existují omezení různých uzlů, která se dají vzájemně propojit v topologii mediálních grafů.

* Zdroj RTSP
   * Pro každou topologii grafu je povolený jenom jeden zdroj RTSP.
* Procesor detekce pohybu
   * Musí být okamžitě podřízené zdroji RTSP.
   * Nelze použít pro procesor rozšíření HTTP nebo gRPC.
* Procesor brány signálu
   * Musí být okamžitě podřízené zdroji RTSP.
* Jímka assetu 
   * Musí být okamžitě podřízená se zdrojem nebo procesorem brány signálu pro RTSP.
* Jímka souborů
   * Musí být okamžitě podřízená z procesoru brány signálu.
   * Nelze okamžitě nacházet z procesoru rozšíření HTTP nebo gRPC nebo procesoru pro detekci pohybu.
* IoT Hub jímka
   * Nemůže být okamžitě podřízená IoT Hub zdroji.

## <a name="limitations-on-media-service-operations-at-preview"></a>Omezení operací Media Service ve verzi Preview

V době vydání verze Preview nepodporuje Live video Analytics na IoT Edge následující:

* Možnost migrovat účet mediální služby z jednoho předplatného na jiný bez přerušení.
* Možnost použít více než jeden účet úložiště s účtem služby Media Service.
* Možnost dynamického nastavení informací o instančních objektech v požadovaných vlastnostech modulu bez restartování.

Můžete používat jenom kamery protokolu IP, které podporují protokol RTSP. Kamery protokolu IP, které podporují protokol RTSP, najdete na stránce vyhovující [ONVIF produktů](https://www.onvif.org/conformant-products) . Vyhledejte zařízení, která jsou v souladu s profily G, S nebo T.

Dále byste měli tyto kamery nakonfigurovat tak, aby používaly H. 264 video a AAC audio. Ostatní kodeky se aktuálně nepodporují. 

## <a name="next-steps"></a>Další kroky

[Přehled](overview.md)
