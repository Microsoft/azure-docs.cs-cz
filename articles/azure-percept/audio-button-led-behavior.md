---
title: Zvukové tlačítko a chování LED pro Azure Percept
description: Další informace o tlačítku a stavu LED služby Azure Percept audio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 1d956e33a84b5509c16400c8f5f8e813d116411a
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095744"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Zvukové tlačítko a chování LED pro Azure Percept

V následujících pokynech najdete informace o tlačítku a stavech LED služby Azure Percept audio.

## <a name="button-behavior"></a>Chování tlačítka

K řízení chování zařízení můžete použít tlačítka.

|Stav tlačítka|  Chování|
|------------|----------|
|Vypnutí|  Stisknutím klávesy ztlumení nebo zrušení ztlumení mikrofonu. Událost tlačítka je vydaná vydaná vydaná po stisknutí.|
|PTT/PTS|   Stisknutím PTT obejít stav klíčového slova hledání a aktivujte stav naslouchání příkazu. Opětovným stisknutím klávesy zastavte aktivní dialog agenta a vraťte se do hledání stavu klíčového slova. Událost tlačítka je vydaná vydaná vydaná po stisknutí. PTS funguje pouze v případě, že je stisknuto tlačítko při mluvení agenta, nikoli v případě, že agent naslouchá nebo je uvažujete.|

## <a name="led-behavior"></a>Chování INDIKÁTORu

Pomocí indikátorů LED můžete pochopit, ve kterém stavu je zařízení.

|POD|   Stav LED|  Stav ušního SoM|
|---|------------|----------------| 
|L02|   na1x bílé, statické na |Zapnout |
|L02|   na1x bílá, 0,5 Hz – bliknutí|  Probíhá ověřování. |
|L01 & L02 & L03|   3x Blue, static on|     Čekání na klíčové slovo|
|L01 & L02 & L03|   Blikání pole LED, 20fps | Naslouchat nebo mluvit|
|L01 & L02 & L03|   Pole LED, 20fps|    Uvažujete|
|L01 & L02 & L03|   3x Red, static on | Vypnutí|

## <a name="next-steps"></a>Další kroky

Tipy pro řešení potíží pro zvukové zařízení Azure Percept najdete v této [příručce](./troubleshoot-audio-accessory-speech-module.md).