---
title: Vizualizace textu v sešitech Azure Monitor
description: Seznamte se se všemi vizualizacemi textu v Azure Monitor sešitu.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: e8f2d9495484b781b26962c2946b5bada6c38b4c
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89663986"
---
# <a name="text-visualizations"></a>Vizualizace textu

Sešity umožňují autorům zahrnout do svých sešitů textové bloky. Text může být lidská analýza telemetrie, informace, které uživatelům pomůžou interpretovat vaše data, nadpisy oddílů atd.

[![Snímek obrazovky s Apdexu tabulkou textu](./media/workbooks-text-visualizations/apdex.png)](./media/workbooks-text-visualizations/apdex.png#lightbox)

Text je přidán pomocí ovládacího prvku Markdownu, který poskytuje úplné formátování ovládacího prvku. Mezi ně patří různé styly nadpisů a písem, hypertextové odkazy, tabulky atd.

Režim úprav:

![Snímek obrazovky textového kroku v režimu úprav](./media/workbooks-text-visualizations/text-edit-mode.png)

Režim náhledu:

![Snímek obrazovky textového kroku v režimu úprav na kartě náhled](./media/workbooks-text-visualizations/text-edit-mode-preview.png)

## <a name="add-a-text-control"></a>Přidání ovládacího prvku text

1. Kliknutím na položku panelu nástrojů pro **Úpravy** přepněte sešit do režimu úprav.
2. K přidání ovládacího prvku text do sešitu použijte odkaz **Přidat text** .
3. Přidejte Markdownu do pole Editor.
4. Pomocí možnosti *styl textu* můžete přepínat mezi prostými Markdownu a Markdownu zabalením se standardními informacemi, upozorněním, úspěšným a chybným stylem Azure Portal.
5. Na kartě **Náhled** můžete zjistit, jak bude obsah vypadat. Při úpravách zobrazí náhled obsah v oblasti ScrollBar a omezí jeho velikost; za běhu se ale Markdownu obsah rozbalí, aby vyplnil jakékoli místo, které potřebuje, bez posuvníků.
6. Kliknutím na tlačítko **hotové úpravy** dokončíte úpravy kroku.

> [!TIP]
> Pomocí tohoto [listu Markdownu tahák](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) se dozvíte o různých možnostech formátování.

## <a name="text-styles"></a>Styly textu

Pro krok textu jsou k dispozici následující styly textu:

| Styl     | Vysvětlení                                                                               |
|-----------|-------------------------------------------------------------------------------------------|
| `plain`   | Nepoužívá se žádné další formátování.                                                      |
| `info`    | Styl "informace" na portálu s  `ℹ` ikonou nebo podobnou a obecně modrým pozadím.      |
| `error`   | Styl "Error" na portálu s `❌` ikonou nebo podobnou a obecně červeným pozadím.     |
| `success` | Styl "úspěch" na portálu s `✔` ikonou nebo podobnou a obecně zeleným pozadím.  |
| `upsell`  | Styl "přeprodat" na portálu s `🚀` ikonou nebo podobnou a obecně fialovým pozadím. |
| `warning` | Styl "upozornění" na portálu s `⚠` ikonou nebo podobnou a obecně modrým pozadím.   |

Místo výběru konkrétního stylu můžete také zvolit parametr text jako zdroj stylu. Hodnota parametru musí být jedna z výše uvedených textových hodnot. Absence hodnoty nebo jakékoli nerozpoznané hodnoty bude považována za `plain` style.

Příklad stylu informací:

![Snímek obrazovky s tím, jak vypadá styl informací](./media/workbooks-text-visualizations/text-preview-info-style.png)

Příklad stylu upozornění:

![Snímek obrazovky s tím, jak styl upozornění vypadá](./media/workbooks-text-visualizations/text-warning-style.png)

## <a name="next-steps"></a>Další kroky

* Naučte se, jak [v sešitech vytvořit graf](workbooks-chart-visualizations.md).
* Naučte se vytvářet [mřížku v sešitech](workbooks-grid-visualizations.md).
