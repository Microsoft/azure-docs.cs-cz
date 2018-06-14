---
title: Nahraďte PCM na vašem zařízení řady StorSimple 8000 | Microsoft Docs
description: Vysvětluje, jak odeberete a nahradíte energii a chlazení modulu (PCM) v zařízení StorSimple
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 7d181e6e434c998573dbea4b541cfacf7a28ee66
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875057"
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Nahrazení energii a chlazení modulu zařízení StorSimple
## <a name="overview"></a>Přehled
Zdroj napájení a chladicí ventilátory, které jsou řízena pomocí primárním serverem a skříně EBOD se skládá energii a chlazení modulu (PCM) v zařízení s Microsoft Azure StorSimple. Existuje jenom jeden model PCM, který je certifikované pro každou skříň. Primární skříň je certifikované pro 764 W PCM a skříň EBOD je certifikované pro 580 W PCM. I když jsou různé PCMs skříni primární a EBOD skříň, postup nahrazení je stejný.

Tento kurz vysvětluje postup:

* Odebrat PCM
* Nainstalovat náhradní PCM

> [!IMPORTANT]
> Před odebráním a nahraďte PCM, informace zabezpečení v [StorSimple hardwarové součásti nahrazení](storsimple-8000-hardware-component-replacement.md).


## <a name="before-you-replace-a-pcm"></a>Před nahrazením PCM
Mějte na paměti následující důležité skutečnosti před nahrazením vaší PCM:

* Pokud napájení PCM selže, nechte vadný modul nainstalovaný, ale odebrat napájecí kabel. Ventilátor budou nadále přijímat power od skříně a pokračovat v poskytování správné chlazení. V případě selhání ventilátoru PCM je nutné vyměnit okamžitě.
* Před odebráním PCM, odpojte napájení PCM vypnutím hlavní přepínač (pokud existuje) nebo fyzicky odebráním napájecí kabel. To poskytuje upozornění do vašeho systému hrozí vypnutí napájení.
* Ujistěte se, že jiné PCM je funkční pro operace trvalá systému před výměnou vadný PCM. Vadný PCM se musí nahradit odpovídajícími plně funkční PCM co nejdříve.
* Nahrazení modulu PCM trvá pouze několik minut, ale jeho musí být dokončeny v rámci 10 minut odebrání selhání PCM, aby nedocházelo k přehřívání.
* Všimněte si, že nahrazení 764 W PCM moduly dodaný z objektu pro vytváření neobsahují modul zálohování baterie. Musíte se k odebrání baterie vaší vadný PCM a vložte ji do modulu nahrazení před provedením nahrazení. Další informace najdete v tématu Jak [vyjměte a vložte modul zálohování baterie](storsimple-8000-battery-replacement.md).

## <a name="remove-a-pcm"></a>Odebrat PCM
Až budete připraveni k odebrání zařízení s Microsoft Azure StorSimple energii a chlazení modulu (PCM), postupujte podle těchto pokynů.

> [!NOTE]
> Před odebráním vaší PCM, ověřte, zda máte správné nahrazení (764 W pro primární skříň) nebo 580 W pro EBOD skříň.

#### <a name="to-remove-a-pcm"></a>Chcete-li odebrat PCM
1. Na portálu Azure classic klikněte na tlačítko **Nastavení > Sledování > Stav hardwaru**. Zkontrolujte stav komponenty PCM pod **sdílené součásti** Chcete-li zjistit, jaké PCM se nezdařilo:
   
   * Pokud se ke zdroji napájení v PCM 0 nezdařila, stav **zdroj napájení v PCM 0** červená.
   * Pokud se ke zdroji napájení v PCM 1 nezdařila, stav **zdroj napájení v PCM 1** červená.
   * Pokud se nezdařila ventilátor v PCM 1, stav buď **chlazení 0 pro PCM 0** nebo **chlazení 1 pro PCM 0** červená.
2. Vyhledání chybných PCM na zadní straně primární skříň. Pokud používáte 8600 model, určete primární skříň podle identifikační číslo jednotky systému zobrazený na přední panel DIODU zobrazení. Výchozí hodnota je ID jednotky zobrazené na primární skříň **00**, zatímco výchozí hodnota je ID jednotky zobrazené na skříni EBOD **01**. Následující obrázek a tabulka popisují přední panel DIODU zobrazení.
   
    ![ID systému na přední panel OPS](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **Obrázek 1** přední panel zařízení  
   
   | Štítek | Popis |
   |:--- |:--- |
   | 1 |Tlačítko pro ztlumení |
   | 2 |Napájení systému |
   | 3 |Selhání modulu |
   | 4 |Logické chyby |
   | 5 |Zobrazení ID jednotky |
3. Monitorování indikátoru LED zadní primární skříň lze také identifikovat vadný PCM. Viz následující obrázek a tabulka pochopit, jak vyhledávat vadný PCM pomocí indikátory LED. Například pokud DIODU odpovídající k **ventilátor nezdaří** je lit, ventilátoru se nezdařilo. Podobně pokud DIODU odpovídající k **AC selhání** je lit, napájení se nezdařilo. 
   
    ![Propojovací rozhraní zařízení PCM monitorování ukazatele LED](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **Obrázek 2** zpět of PCM s indikátor LED
   
   | Štítek | Popis |
   |:--- |:--- |
   | 1 |Výpadku napájení ze sítě |
   | 2 |Ventilátor selhání |
   | 3 |Selhání stav baterie. |
   | 4 |PCM OK |
   | 5 |Řadič domény výpadku proudu |
   | 6 |Dobrý stav baterie |
4. Naleznete na následující obrázek pozadí zařízení StorSimple najít PCM modulu se nezdařilo. PCM 0 je na levé straně a PCM 1 je na pravé straně. Následující tabulka vysvětluje moduly.
   
     ![Propojovací rozhraní systému modulů skříň primární zařízení](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **Obrázek 3** zadní zařízení s moduly plug-in 
   
   | Štítek | Popis |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Řadič 0 |
   | 4 |Řadič 1 |
5. Vadný PCM vypnout a odpojit napájecí kabel napájení. Nyní můžete odebrat PCM.
6. Pochopit zámek a na straně popisovač PCM mezi Flash a ukazováčkem a vměstnat je dohromady a otevřít popisovač.
   
    ![Otevírání PCM popisovač](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **Obrázek 4** otevírání popisovač PCM
7. Uchopitelný popisovač a odeberte PCM.
   
    ![Odebrání zařízení PCM](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **Obrázek 5** odebrání PCM

## <a name="install-a-replacement-pcm"></a>Nainstalovat náhradní PCM
Postupujte podle těchto pokynů k instalaci PCM v zařízení StorSimple. Ujistěte se, že jste vložili modul zálohování baterie před instalací nahrazení PCM (týká se pouze 764 W PCMs). Další informace najdete v tématu Jak [vyjměte a vložte modul zálohování baterie](storsimple-8000-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>Chcete-li nainstalovat PCM
1. Ověřte, zda máte správné náhradou PCM tento skříň. Primární skříň musí 764 W PCM a skříň EBOD musí 580 W PCM. Byste neměli používat 580 PCM W ve skříni primární nebo 764 PCM W ve skříni EBOD. Následující obrázek ukazuje, kde k identifikaci informací na štítek, který je opatřit PCM.
   
    ![Popisek PCM zařízení](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **Obrázek 6** PCM popisek
2. Zkontrolujte, zda škody skříň, věnujte zvláštní pozornost konektory. 
   
   > [!NOTE]
   > **Pokud jsou všechny kódy PIN konektor ohnuty není nainstalovaný modul.**
   > 
   > 
3. S popisovačem PCM v otevřené poloze posuňte modul do skříni.
   
    ![Instalace zařízení PCM](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **Obrázek 7** instalaci PCM
4. Ruční zavření PCM popisovač. A klikněte měli vědět, jak zapojí západky popisovač.
   
   > [!NOTE]
   > Aby se zajistilo, že konektor PIN mít pověření, můžete můžete jemně tug na popisovač bez uvolnění zámek. Pokud PCM snímky, znamená to, že zámek se zavřel před konektory pověření.
   
5. Připojte kabely power ke zdroji napájení a PCM.
6. Zabezpečte kmen balících pomoc.
7. Zapněte PCM.
8. Ověřit, pokud chcete nahrazení byla úspěšná: služby StorSimple Manager zařízení na portálu Azure, přejděte na zařízení a pak na **Nastavení > Sledování > Stav hardwaru**. V části **sdílené součásti**, by měly být stav PCM.
   
   > [!NOTE]
   > Ho může trvat několik minut nahrazení PCM úplně inicializovat.

## <a name="next-steps"></a>Další kroky
Další informace o [StorSimple hardwarové součásti nahrazení](storsimple-8000-hardware-component-replacement.md).

