---
title: Rychlý start aplikace Akustika projektu s jednotou
titlesuffix: Azure Cognitive Services
description: Ukázkový obsah můžete použít k experimentování s ovládacími prvky návrhu aplikace Project Acoustics v aplikaci Unity a nasazení do plochy systému Windows.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fabdd221ef99414eae0156babbd76dedb1f0745d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72243026"
---
# <a name="project-acoustics-unity-quickstart"></a>Rychlý start aplikace Akustika aplikace Akustika Unity
Použijte ukázkový obsah aplikace Project Acoustics pro Unity k experimentování s ovládacími prvky návrhu podporovanými simulací.

Požadavky na software:
* [Unity 2018.2+](https://unity3d.com) pro Windows
* [Ukázkový obsah aplikace Akustika projektu](https://www.microsoft.com/download/details.aspx?id=57346)

Co je součástí ukázkového balíčku?
* Scéna Unity s geometrií, zdroji zvuku a ovládacími prvky hry
* Modul plug-in Akustika projektu
* Pečená akustika aktiva pro scénu

## <a name="import-the-sample-package"></a>Import ukázkového balíčku
Importujte ukázkový balíček do nového projektu Unity.
1. V unity, přejděte na vlastní**balíček** > **importu** >  **prostředků**.

    ![Možnosti balíčku importu Unity](media/import-package.png)  

1. Zvolte **ProjectAcoustics.unitypackage**.

1. Vyberte tlačítko **Importovat** pro integraci balíčku Unity do projektu.  
  
    ![Dialogové okno Balíček pro import unity](media/import-dialog.png)  

Chcete-li importovat balíček do existujícího projektu, naleznete [v tématu Unity integrace](unity-integration.md) další kroky a poznámky.

>[!NOTE]
>Po dokončení importu se v protokolu konzoly zobrazí několik chybových zpráv. Pokračujte dalším krokem a restartujte unity.

## <a name="restart-unity"></a>Restartovat jednotu
Péct část sady nástrojů akustiky vyžaduje .NET 4. *x* skriptovací runtime verze. Import balíčku aktualizuje nastavení přehrávače Unity. Toto nastavení se projeví až po restartování unity. Chcete-li ověřit, zda se nastavení projevilo, otevřete nastavení **přehrávače:**

![Nabídka Nastavení projektu Unity](media/player-settings.png)  

![Panel Unity Player Settings s vybranou položkou .NET 4.x](media/net45.png)  

>[!NOTE]
>Tento snímek obrazovky byl pořízen z Unity 2018. *x*. Obrázek se může lišit v novějších verzích Unity.

## <a name="open-the-project-acoustics-bake-window"></a>Otevření okna Pečení akustiky projektu
V unity vyberte **akustiku** v nabídce **Okno.**

![Editor Unity s možností Akustika zvýrazněnou v nabídce Okno](media/window-acoustics.png)

Otevře se plovoucí okno **Akustika.** Toto okno je místo, kde můžete nastavit vlastnosti akustické simulace.

![Editor Unity s otevřeným oknem Akustika](media/unity-editor-plugin-window.png)  

## <a name="experiment-with-the-design-controls"></a>Experimentujte s ovládacími prvky návrhu
Otevřete ukázkovou scénu ve složce *ProjectAcousticsSample* a vyberte tlačítko přehrát v editoru Unity. Pomocí kláves W, A, S a D a myši se pohybovat. Chcete-li porovnat, jak scéna zní s akustikou a bez ní, vyberte klávesu R, dokud překryvný text nezčervená a nezobrazí se "Akustika: Zakázáno". Pokud chcete zobrazit klávesové zkratky pro další ovládací prvky, vyberte F1. Můžete také kliknout pravým tlačítkem myši a vybrat akci a potom klepnutím levým tlačítkem myši tuto akci provést.

Skript *AcousticsAdjust* je připojen ke zdrojům zvuku ve vzorové scéně. Umožňuje parametry návrhu pro jeden zdroj.

![Skript Unity AcousticsAdjust](media/acoustics-adjust.png)

V následujících částech se prozkoumejte některé efekty, které můžete vytvořit pomocí dostupných ovládacích prvků. Podrobné informace o jednotlivých ovládacích prvekech naleznete v [kurzu project acoustics unity design .](unity-workflow.md)

### <a name="modify-distance-based-attenuation"></a>Upravit útlum založený na vzdálenosti
Zpracování zvukového digitálního signálu v modulu plug-in spatializer **project Acoustics** Unity respektuje útlum založený na zdroji, který je integrován do editoru Unity. Ovládací prvky pro útlum na základě vzdálenosti jsou v součásti **Zdroj zvuku,** která je v panelu **Inspektor** zdrojů zvuku v části **3D nastavení zvuku**:

![Panel volby zeslaby vzdálenosti Unity](media/distance-attenuation.png)

Aplikace Project Acoustics vypočítá v poli "oblast simulace", které je soustředěno kolem umístění přehrávače. Akustika aktiva ve vzorku balíčku byly pečené na simulaci oblasti velikosti 45 metrů kolem hráče. Takže útlum zvuku by měl být navržen tak, aby klesl na 0 na asi 45 m.

### <a name="modify-occlusion-and-transmission"></a>Změna okluze a přenosu
* Pokud je multiplikátor **okluze** větší než 1 (výchozí hodnota je 1), je okluze zveličena. Chcete-li, aby byl efekt okluze jemnější, nastavte jej na méně než 1.

* Chcete-li povolit přenos přes stěnu, posuňte jezdec **Přenos (dB)** od nejnižšího nastavení.

### <a name="modify-wetness-for-a-source"></a>Změna vlhkosti zdroje
* Chcete-li změnit, jak rychle se vlhkost mění se vzdáleností, použijte **percepční pokřivení vzdálenosti**. Prostřednictvím simulace aplikace Project Acoustics vypočítá úrovně za mokra, které poskytují vnímavé vzdálenosti a plynule se liší podle vzdálenosti. Zvýšením pokřivení vzdálenosti tento efekt zveličujete zvýšením úrovní vlhkosti souvisejících se vzdáleností. Hodnoty deformace menší než 1, aby vzdálenost-založené dozvuku změnit jemnější.

   Chcete-li provést jemnější úpravy tohoto efektu, změňte nastavení **Vlhkost (dB).**

* Chcete-li prodloužit dobu rozpadu v celém prostoru, upravte **měřítko doby rozpadu**. Pokud je výsledkem simulace pro konkrétní dvojici umístění zdrojového posluchače doba rozpadu 1,5 sekundy a **měřítko doby rozpadu** je nastaveno na 2, doba rozpadu, která je použita na zdroj, je 3 sekundy.

## <a name="next-steps"></a>Další kroky
* Přečtěte si podrobnosti o [ovládacích prvcích návrhu aplikace Project Acoustics založených na jednotě](unity-workflow.md).
* Dále prozkoumejte koncepty procesu [návrhu](design-process.md).
* [Vytvořte si účet Azure a](create-azure-account.md) prozkoumejte procesy před pečením a pečením.
