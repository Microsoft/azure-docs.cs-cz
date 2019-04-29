---
title: Výměna PCM na vašem zařízení řady StorSimple 8000 | Dokumentace Microsoftu
description: Vysvětluje, jak odstranit a nahradit napájení a chlazení modulu (PCM) na zařízení StorSimple
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
ms.openlocfilehash: 42561570e24aec5edd33248ef1738e53175e480e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60632416"
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Nahraďte napájení a chlazení modulu zařízení StorSimple
## <a name="overview"></a>Přehled
Napájení a chlazení modulu (PCM) v Microsoft Azure StorSimple zařízení se skládá z ke zdroji napájení a chladicí ventilátory, které jsou ovládaná primárním serverem a EBOD skříně. Existuje pouze jeden model PCM, který má certifikaci pro každou skříň. Primární zařízení má certifikaci pro 764 W PCM a skříně EBOD má certifikaci pro 580 W PCM. I když PCMs pro primární skříň a skříně EBOD se liší, nahrazení postup se shoduje.

Tento kurz vysvětluje následující postupy:

* Odebrat PCM
* Instalace náhradního PCM

> [!IMPORTANT]
> Před odebírání a nahrazování PCM, přečtěte si informace bezpečnost v [StorSimple hardwarové komponenty nahrazení](storsimple-8000-hardware-component-replacement.md).


## <a name="before-you-replace-a-pcm"></a>Před nahrazením PCM
Mějte na paměti následující důležité problémy před nahrazením vaše PCM:

* Pokud selže napájení PCM, ponechte vadným modul nainstalovaný, ale odebrat napájecí kabel. Ventilátor nadále přijímat power od skříně a nadále poskytovat správné chlazení. Pokud se nezdaří ventilátor PCM potřebuje vyměnit okamžitě.
* Před odebráním PCM, odpojte napájení PCM vypnutím hlavní přepínač (je-li k dispozici) nebo fyzickým odebráním napájecí kabel. To poskytuje upozornění do vašeho systému, hrozí vypnutí napájení.
* Ujistěte se, že je funkční pro operace pokračování systému před výměnou vadných PCM PCM. Chybný PCM se musí nahradit odpovídajícími plně funkční PCM co nejdříve.
* Nahrazení modulu PCM trvá jenom několik minut, ale je potřeba udělat do 10 minut odebrání neúspěšné PCM, aby nedocházelo k přehřívání.
* Všimněte si, že nahrazení 764 W PCM moduly z objekt pro vytváření neobsahují modulu záložní baterie. Je potřeba odebrat z vadné PCM baterie a vložte ji do modulu nahrazení před provedením nahrazení. Další informace najdete v tématu Jak [odstranit a Vložit modul záložní baterie](storsimple-8000-battery-replacement.md).

## <a name="remove-a-pcm"></a>Odebrat PCM
Až budete připravení k odebrání napájení a chlazení modulu (PCM) ze zařízení s Microsoft Azure StorSimple, postupujte podle těchto pokynů.

> [!NOTE]
> Před odebráním vaše PCM, ověřte, že máte správný náhradní (764 W pro primární skříň) nebo 580 W pro EBOD skříň.

#### <a name="to-remove-a-pcm"></a>Chcete-li odebrat PCM
1. Na portálu Azure classic klikněte na tlačítko **Nastavení > Sledování > Stav hardwaru**. Kontrola stavu součásti PCM pod **sdílené komponenty** tím určíte, které PCM se nezdařilo:
   
   * Pokud má napájení v PCM 0 nepovedlo, stav **napájení v PCM 0** bude mít červenou.
   * Zda napájení v PCM 1 selhala, stav **napájení v PCM 1** bude mít červenou.
   * Pokud ventilátor v PCM 1 selhala, stav buď **chlazení v PCM 0 0** nebo **1 chlazení v PCM 0** bude mít červenou.
2. Vyhledejte neúspěšné PCM na zadní straně primární skříň. Pokud používáte modelu 8600, identifikujte primární skříň pohledem na přední panel LED zobrazení systému jednotky identifikační číslo. Výchozí hodnota je ID jednotky na primární zařízení **00**, že výchozí hodnota je ID jednotky na skříň EBOD **01**. Následující diagram a tabulka vysvětluje přední panel zobrazovat Indikátor.
   
    ![ID systému na přední panel OPS](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **Obrázek 1** přední panel ovládacího prvku zařízení  
   
   | Štítek | Popis |
   |:--- |:--- |
   | 1 |Ztlumit tlačítko |
   | 2 |Napájení systému |
   | 3 |Chyby modulu |
   | 4 |Logické chyby |
   | 5 |Zobrazení ID jednotky |
3. Monitorování indikátorů LED zadní primární skříň lze také identifikovat vadným PCM. Viz následující diagram a tabulce zjistíte, jak najít vadným PCM pomocí indikátorů LED. Například pokud odpovídající LED pro **ventilátor selhání** je lit, ventilátor se nezdařilo. Podobně pokud odpovídající LED pro **AC selhání** je lit, napájení se nezdařilo. 
   
    ![Propojovací rozhraní systému zařízení PCM monitorování indikátorů LED](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **Obrázek 2** PCM of zpět pomocí indikátorů LED
   
   | Štítek | Popis |
   |:--- |:--- |
   | 1 |Výpadku napájení AC. |
   | 2 |Chyba větráku |
   | 3 |Selhání baterie |
   | 4 |PCM OK |
   | 5 |Výpadku napájení řadiče domény |
   | 6 |Baterie v pořádku |
4. Prohlédněte si následující diagram pozadí zařízení StorSimple k vyhledání PCM modulu se nezdařilo. PCM 0 je na levé straně a PCM 1 je na pravé straně. Následující tabulka vysvětluje moduly.
   
     ![Propojovací rozhraní modulů primární skříň zařízení](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **Obrázek 3** zadní zařízení s moduly plug-in 
   
   | Štítek | Popis |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Řadič 0 |
   | 4 |Řadič 1 |
5. Vypněte vadným PCM a odpojit napájecí kabel dodávek. Teď můžete odebrat PCM.
6. Pochopit její podstatu zámek a na straně popisovač PCM mezi thumb a ukazováčkem a vměstnat je dohromady tak, aby popisovač.
   
    ![Otevírání PCM popisovače](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **Obrázek 4** otevírání PCM popisovače
7. Uchopitelný popisovač a odeberte PCM.
   
    ![Odebrání zařízení PCM](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **Obrázek 5** odebrání PCM

## <a name="install-a-replacement-pcm"></a>Instalace náhradního PCM
Postupujte podle těchto pokynů k instalaci PCM v zařízení StorSimple. Ujistěte se, že jste tam vložili modulu záložní baterie před instalací nahrazení PCM (týká se pouze 764 W PCMs). Další informace najdete v tématu Jak [odstranit a Vložit modul záložní baterie](storsimple-8000-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>Chcete-li nainstalovat PCM
1. Ověřte, že máte správný náhradní PCM pro toto zařízení. Primární zařízení musí 764 W PCM a skříně EBOD musí 580 W PCM. By se neměly pokoušet použít 580 PCM W ve primárního skříň nebo 764 PCM W ve skříni EBOD. Následující obrázek ukazuje, kde k identifikaci informací na popisek, který je opatřit PCM.
   
    ![Popisek PCM zařízení](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **Obrázek 6** PCM popisek
2. Vyhledejte poškození skříň, věnujte zvláštní pozornost konektory. 
   
   > [!NOTE]
   > **Pokud jsou všechny pinů ohnuty není nainstalovaný modul.**
   > 
   > 
3. S popisovačem PCM na pozici, otevřít potáhněte modulu do prostoru.
   
    ![Instalace zařízení PCM](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **Obrázek 7** instalace PCM
4. Ruční zavření PCM popisovač. Kliknutím by měl poslechněte si, jak zaujme zámek popisovače.
   
   > [!NOTE]
   > Pokud chcete mít jistotu, že máte zapojení konektor PIN kódy, vám může jemně tug na popisovač bez uvolnění zámek. Pokud PCM snímky navýšení kapacity, znamená, že zámek se zavřel před zapojení konektory.
   
5. Zdroj napájení a PCM, připojte napájecích kabelů.
6. Kmen zabezpečení pomáhajících balíků.
7. Zapněte PCM.
8. Ověřte, že nahrazení proběhlo úspěšně: na webu Azure portal služby StorSimple Device Manager přejděte do svého zařízení a potom do **Nastavení > Sledování > Stav hardwaru**. V části **sdílené komponenty**, stav PCM by měl být zelená.
   
   > [!NOTE]
   > Může trvat několik minut, než nahrazení PCM zcela inicializace.

## <a name="next-steps"></a>Další postup
Další informace o [StorSimple hardwarové komponenty nahrazení](storsimple-8000-hardware-component-replacement.md).

