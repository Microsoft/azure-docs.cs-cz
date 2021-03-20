---
title: Azure Media Player přístupnost
description: Přečtěte si další informace o nastavení usnadnění Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 2231c2969bbfce1668002ad4f5f719e0b8e13de5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "81726554"
---
# <a name="accessibility"></a>Usnadnění #

Azure Media Player funguje s funkcemi čtečky obrazovky, jako je Windows Narrator a Apple OSX/iOS VoiceOver. Pro tlačítka uživatelského rozhraní jsou k dispozici alternativní značky a čtečka obrazovky je schopná přečtení těchto alternativních značek, když na ně uživatel přejde. Další konfigurace je možné nastavit na úrovni operačního systému.

## <a name="captions-and-subtitles"></a>Titulky a titulky ##

V tuto chvíli Azure Media Player aktuálně podporuje popisky jenom pro události na vyžádání s formátem WebVTT a živými událostmi s využitím CEA 708. Formát TTML se v tuto chvíli nepodporuje. Skryté titulky a titulky v přehrávači umožňují, aby byl přehrávač dostupný a použitelný pro více uživatelů, včetně osob s postižením sluchu nebo kohokoli, kdo si chce obsah přečíst v jiném jazyce. Skryté titulky také zvyšují míru přijetí videa, usnadňují jeho porozumění a umožňují sledovat video v hlučných prostředích, například na pracovišti.  

## <a name="high-contrast-mode"></a>Režim vysokého kontrastu ##

Výchozí uživatelské rozhraní v Azure Media Player je kompatibilní s většinou režimů zobrazení vysokého kontrastu zařízení nebo prohlížeče. Konfigurace je možné nastavit na úrovni operačního systému.

## <a name="mobility-options"></a>Možnosti mobility ##

### <a name="tabbing-focus"></a>Fokus s kartami ###

Fokus s kartami, který poskytuje obecné standardy HTML, je k dispozici v Azure Media Player. Aby bylo možné povolit zaostření tabulátorem, je nutné přidat `tabindex=0` (nebo jinou hodnotu, pokud rozumíte tomu, jak je ovlivněno řazení karet v HTML) k HTML, `<video>` jako je například: `<video ... tabindex=0>...</video>` . Na některých platformách může být fokus pro ovládací prvky přítomen pouze v případě, že jsou ovládací prvky viditelné a pokud platforma tyto funkce podporuje.

Když je fokus s kartami povolený, může koncový uživatel efektivně Procházet a ovládat přehrávač videa bez závislosti na jejich myši. Na každou kontextovou nabídku nebo na přizpůsobený element se dá přejít tak, že kliknete na tlačítko tabulátoru a zvolíte ENTER nebo MEZERNÍK. Při stisknutí klávesy ENTER nebo mezerník v místní nabídce dojde k jejímu rozbalení, aby koncový uživatel mohl pokračovat v procházení klávesami a vybrat položku nabídky. Jakmile budete mít kontext položky, kterou chcete vybrat, znovu stiskněte Enter nebo Mezerník a dokončete výběr.

### <a name="hotkeys"></a>Klávesové zkratky ###

Azure Media Player podporuje řízení prostřednictvím klávesové zkratky klávesnice. Ve webovém prohlížeči je jediným způsobem, jak řídit podkladový prvek videa, zaměření na přehrávač. Jakmile je hráč v přehrávači soustředění, může ovládat funkce přehrávače klávesou Hot.  Následující tabulka obsahuje popis různých klávesových zkratek a k nim přidruženého chování:

| Klávesová zkratka              | Chování                                                                |
|----------------------|-------------------------------------------------------------------------|
| F/f                  | Přehrávač zapne/ukončí režim zobrazení na celou obrazovku                                  |
| M/m                  | Hlasitost přehrávače se ztlumí nebo se ztlumení zruší                                          |
| Šipka nahoru a dolů    | Hlasitost přehrávače se zvýší nebo sníží                                    |
| Šipka doleva a doprava | Video se posune dopředu nebo dozadu                                  |
| 0, 1, 2, 3, 4, 5, 6, 7, 8, 9  | Průběh videa se v \- závislosti na stisknutí klávesy změní na 0% 90%. |
| Kliknout na akce         | Video se začne přehrávat nebo se pozastaví                                                   |

## <a name="next-steps"></a>Další kroky

<!---Some context for the following links goes here--->
- [Rychlý Start Azure Media Player](azure-media-player-quickstart.md)