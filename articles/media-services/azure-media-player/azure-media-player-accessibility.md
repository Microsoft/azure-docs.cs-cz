---
title: Přístupnost přehrávače médií Azure
description: Přečtěte si další informace o nastavení usnadnění přístupu přehrávače Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 2231c2969bbfce1668002ad4f5f719e0b8e13de5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726554"
---
# <a name="accessibility"></a>Usnadnění #

Azure Media Player funguje s funkcemi čtečky obrazovky, jako je Windows Narrator a Apple OSX/iOS VoiceOver. Pro tlačítka uživatelského rozhraní jsou k dispozici alternativní značky a program pro čtení z obrazovky je schopen číst tyto alternativní značky, když k nim uživatel přejde. Další konfigurace lze nastavit na úrovni operačního systému.

## <a name="captions-and-subtitles"></a>Titulky a titulky ##

V současné době Azure Media Player aktuálně podporuje titulky pro pouze události na vyžádání s formátem WebVTT a živé události pomocí CEA 708. Formát TTML není aktuálně podporován. Skryté titulky a titulky v přehrávači umožňují, aby byl přehrávač dostupný a použitelný pro více uživatelů, včetně osob s postižením sluchu nebo kohokoli, kdo si chce obsah přečíst v jiném jazyce. Skryté titulky také zvyšují míru přijetí videa, usnadňují jeho porozumění a umožňují sledovat video v hlučných prostředích, například na pracovišti.  

## <a name="high-contrast-mode"></a>Režim vysokého kontrastu ##

Výchozí ui v Azure Media Player je kompatibilní s většinou režimů zobrazení s vysokým kontrastem zařízení/prohlížeče. Konfigurace lze nastavit na úrovni operačního systému.

## <a name="mobility-options"></a>Možnosti mobility ##

### <a name="tabbing-focus"></a>Fokus tabulátoru ###

Fokus tabulátorů, které jsou k dispozici podle obecných standardů HTML, je k dispozici v Azure Media Player. Chcete-li povolit zaostřování karty, musíte přidat `tabindex=0` (nebo jinou hodnotu, `<video>` pokud `<video ... tabindex=0>...</video>`chápete, jak je řazení karet ovlivněno v HTML) do HTML, jako je tato: . Na některých platformách může být fokus pro ovládací prvky k dispozici pouze v případě, že ovládací prvky jsou viditelné a pokud platforma podporuje tyto funkce.

Jakmile je povoleno zaostření tabulátorem, může koncový uživatel efektivně procházet a ovládat přehrávač videa bez závislosti na myši. Ke každé kontextové nabídce nebo kontrolovatelnému prvku lze navigovat stisknutím tlačítka tabulátoru a výběrem klávesy enter nebo mezerník. Stisknutím klávesy enter nebo mezerníku v místní nabídce se rozbalíte, takže koncový uživatel může pokračovat v procházení tabulátorem a vybrat položku nabídky. Jakmile budete mít kontext položky, kterou chcete vybrat, znovu stiskněte Enter nebo Mezerník a dokončete výběr.

### <a name="hotkeys"></a>Klávesy ###

Azure Media Player podporuje ovládání pomocí klávesové zkratky klávesnice. Ve webovém prohlížeči je jediným způsobem, jak ovládat základní prvek videa, zaměření na přehrávač. Jakmile je zaměření na přehrávač, hot key můžete ovládat funkci přehrávače.  Následující tabulka obsahuje popis různých klávesových zkratek a k nim přidruženého chování:

| Klávesová zkratka              | Chování                                                                |
|----------------------|-------------------------------------------------------------------------|
| F/f                  | Přehrávač zapne/ukončí režim zobrazení na celou obrazovku                                  |
| M/m                  | Hlasitost přehrávače se ztlumí nebo se ztlumení zruší                                          |
| Šipka nahoru a dolů    | Hlasitost přehrávače se zvýší nebo sníží                                    |
| Šipka doleva a doprava | Video se posune dopředu nebo dozadu                                  |
| 0, 1, 2, 3, 4, 5, 6, 7, 8, 9  | Průběh videa se změní\- na 0 % 90 % v závislosti na stisknutém tlačítka |
| Klikněte na akce         | Video se začne přehrávat nebo se pozastaví                                                   |

## <a name="next-steps"></a>Další kroky

<!---Some context for the following links goes here--->
- [Rychlý start přehrávače médií Azure](azure-media-player-quickstart.md)