---
title: Zvukové tlačítko a chování LED pro Azure Percept
description: Další informace o tlačítku a stavu LED služby Azure Percept audio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: c7ee2289680bb50fabb8e6eb2a3ea0466bd58afb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662566"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Zvukové tlačítko a chování LED pro Azure Percept

V následujících pokynech najdete informace o tlačítku a stavech LED služby Azure Percept audio.

## <a name="button-behavior"></a>Chování tlačítka

K řízení chování zařízení můžete použít tlačítka.

|Stav tlačítka|  Chování|
|------------|----------|
|Vypnutí|  Stisknutím klávesy ztlumení nebo zrušení ztlumení mikrofonu. Událost tlačítka je vydaná vydaná vydaná po stisknutí.|
|PTT/PTS|   Stisknutím PTT obejít stav klíčového slova hledání a aktivujte stav naslouchání příkazu. Opětovným stisknutím klávesy zastavte aktivní dialog agenta a vraťte se do hledání stavu klíčového slova.|

## <a name="led-behavior"></a>Chování INDIKÁTORu

Pomocí indikátorů LED můžete pochopit, ve kterém stavu je zařízení.

|POD|   Stav LED|  Stav ušního SoM|
|---|------------|----------------| 
|L02|   na1x bílé, statické na |Zapnout |
|L02|   na1x bílá, 0,5 Hz – bliknutí|  Probíhá ověřování. |
|L01 & L02 & L03|   3x Blue, static on|     Zjištěno klíčové slovo|
|L01 & L02 & L03|   Blikání pole LED, 20fps | Naslouchat nebo mluvit|
|L01 & L02 & L03|   Pole LED, 20fps|    Uvažujete|
|L01 & L02 & L03|   3x Red, static on | Vypnutí|

## <a name="next-steps"></a>Další kroky

Tipy pro řešení potíží pro zvukové zařízení Azure Percept najdete v této [příručce](./troubleshoot-audio-accessory-speech-module.md).