---
title: Zvukové tlačítko a chování LED pro Azure Percept
description: Další informace o tlačítku a stavu LED služby Azure Percept audio
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: fa919acb527084d19ab88b2e7895d4e6ab0b72d3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105609067"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Zvukové tlačítko a chování LED pro Azure Percept

Informace o tlačítku a stavu LED pro zvukové zařízení Azure Percept najdete v následujících pokynech.

## <a name="button-behavior"></a>Chování tlačítka

Pomocí tlačítek můžete řídit chování zařízení.

|Stav tlačítka|Chování|
|------------|----------|
|Vypnutí|Stisknutím klávesy ztlumíte nebo ztlumíte ztlumení pole Mic. Událost tlačítka je vydaná vydaná vydaná po stisknutí.|
|PTT/PTS|Stisknutím PTT obejít stav klíčového slova hledání a aktivujte stav naslouchání příkazu. Opětovným stisknutím klávesy zastavte aktivní dialog agenta a vraťte se do hledání stavu klíčového slova. Událost tlačítka je vydaná vydaná vydaná po stisknutí. PTS funguje pouze v případě, že je stisknuto tlačítko v době, kdy agent mluví, a ne v případě, že agent naslouchá nebo uvažujete.|

## <a name="led-behavior"></a>Chování INDIKÁTORu

Indikátory LED slouží k pochopení stavu, ve kterém je zařízení.

|POD|Stav LED|Stav ušního SoM|
|---|------------|----------------|
|L02|na1x bílé, statické na|Zapnout |
|L02|na1x bílá, 0,5 Hz – bliknutí|Probíhá ověřování. |
|L01 & L02 & L03|3x Blue, static on|Čekání na klíčové slovo|
|L01 & L02 & L03|Blikání pole LED, 20fps |Naslouchat nebo mluvit|
|L01 & L02 & L03|Pole LED, 20fps|Uvažujete|
|L01 & L02 & L03|3x Red, static on |Vypnutí|

## <a name="next-steps"></a>Další kroky

Tipy pro řešení potíží pro zvukové zařízení Azure Percept najdete v této [příručce](./troubleshoot-audio-accessory-speech-module.md).