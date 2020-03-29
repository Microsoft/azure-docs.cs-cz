---
title: Integrace projektu Akustika Neskutečná a Wwise
titlesuffix: Azure Cognitive Services
description: Tento článek popisuje integraci modulů plug-in Project Acoustics Unreal a Wwise do projektu.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: e57212a3002390754aaebc5f2aa9ffb10af230a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243047"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Integrace projektu Akustika Neskutečná a Wwise
Tento článek popisuje, jak integrovat balíček modulu plug-in Akustika projektu do stávajícího herního projektu Unreal a Wwise.

Požadavky na software:
* [Unreal Motor](https://www.unrealengine.com/) 4.20+
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1
* [Wwise plug-in pro Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  
  Pokud používáte přímou integraci wwise SDK namísto wwise unreal plug-in, podívejte se na projekt Acoustics Unreal plug-in a upravit wwise volání rozhraní API.

Chcete-li použít aplikaci Project Acoustics s jiným zvukovým enginem než Wwise, vytvořte žádost o vylepšení v [diskusním fóru aplikace Project Acoustics](https://github.com/microsoft/ProjectAcoustics/issues). Můžete použít modul plug-in Project Acoustics Unreal k dotazování na akustika data a volání rozhraní API do vašeho motoru.

## <a name="download-project-acoustics"></a>Stažení Akustika projektu
Stáhněte si [balíček project acoustics unreal a wwise plug-in,](https://www.microsoft.com/download/details.aspx?id=58090) pokud jste tak již neučinili.

Součástí balení je zásuvný modul Unreal Engine a zásuvný modul Wwise mixer. Modul plug-in Unreal poskytuje integraci editoru a modulu runtime. Během hry, Project Acoustics Unreal plug-in vypočítá parametry, jako je okluze pro každý herní objekt pro každý snímek. Tyto parametry jsou přeloženy do wwise volání rozhraní API.

## <a name="integration-steps"></a>Integrační kroky

Podle těchto kroků nainstalujte balíček a nasaďte ho ve hře.

### <a name="install-the-project-acoustics-mixer-plug-in"></a>Instalace modulu plug-in mixeru Project Acoustics
1. Otevřete wwise launcher. Na kartě **Moduly plug-in** vyberte v části **Instalovat nové moduly plug-in**možnost **Přidat z adresáře**.

    ![Instalace plug-in u spouštěče Wwise](media/wwise-install-new-plugin.png)

1. Vyberte adresář *AcousticsWwisePlugin\ProjectAcoustics,* který je v balíčku ke stažení. Obsahuje zásuvný balíček Wwise mixer.

   Wwise nainstaluje plug-in. Akustika projektu by se měla zobrazit v seznamu nainstalovaných modulů plug-in ve Wwise.  
![Seznam modulů plug-in nainstalovaných ve wwise po instalaci aplikace Project Acoustics](media/unreal-integration-post-mixer-plugin-install.png)

### <a name="dedeploy-wwise-into-your-game"></a>Dedeploy Wwise do hry
Znovu nasávejte Wwise do své hry, i když jste již wwise integrovali. Tento krok integruje modul plug-in Project Acoustics Wwise.

   > [!NOTE]
   > **Zásuvný modul motoru:** Pokud máte Wwise nainstalován jako herní modul plug-in v projektu Unreal C++, tento krok přeskočte. Pokud je nainstalován místo toho jako modul plug-in motoru, například proto, že váš projekt Unreal je pouze Blueprint, wwise nasazení s naším mixer plug-in je složitější. Vytvořte fiktivní prázdný projekt Unreal C++. Zavřete Unreal editoru, pokud se otevře a postupujte podle zbývající postup k nasazení Wwise do fiktivního projektu. Potom zkopírujte nasazený modul plug-in Wwise.
 
1. Ve spouštěči Wwise vyberte kartu **Unreal Engine.** **Recent Unreal Engine Projects** **Browse for project** Otevřete soubor *projektu Unreal* ve hře Unreal.

    ![Karta Wwise launcher Unreal](media/wwise-unreal-tab.png)

1. Vyberte **integrovat Wwise v projektu** nebo Upravit **Wwise v projektu**. Tento krok integruje wwise binární soubory do projektu, včetně modulu plug-in mixer aplikace Project Acoustics.

   > [!NOTE]
   > **Zásuvný modul motoru:** Pokud používáte wwise jako modul plug-in motoru a vytvořili jste fiktivní projekt, jak je popsáno výše, zkopírujte složku, kterou Wwise nasadil: *[DummyUProject]\Plugins\Wwise*. Vložte jej přes *[UESource]\Engine\Plugins\Wwise*. *[DummyUProject]* je prázdná cesta projektu Unreal C++ a *[UESource]* je místo, kde jsou nainstalovány zdroje Unreal Engine. Po zkopírování složky můžete odstranit fiktivní projekt.

### <a name="add-the-project-acoustics-unreal-plug-in-to-your-game"></a>Přidejte do hry modul plug-in Project Acoustics Unreal
 
1. Zkopírujte složku *Unreal\ProjectAcoustics* v balíčku modulu plug-in. Vytvořte novou složku *[UProjectDir]\Plugins\ProjectAcoustics*, kde *[UProjectDir]* je složka projektu vaší hry, která obsahuje soubor *.uproject.*

   > [!NOTE]
   > **Modul plug-in motoru**: Pokud používáte Wwise jako modul plug-in motoru, měli byste použít projekt Akustika jako unreal modul plug-in motoru stejně. Místo dříve citovaného cílového adresáře použijte *[UESource]\Engine\Plugins\ProjectAcoustics*.

1. Potvrďte, že se vedle složky *ProjectAcoustics* zobrazí složka *Wwise.* Obsahuje modul plug-in Wwise spolu s binárními soubory modulu plug-in mixeru, který jste nasadili dříve.

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Rozšíření funkce zásuvných modulů Wwise Unreal
Modul plug-in Project Acoustics Unreal vyžaduje další chování vystavené z rozhraní API modulu plug-in Wwise Unreal podle [těchto pokynů](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). Zahrnuli jsme dávkový soubor pro automatizaci postupu oprav.

* Uvnitř *Plugins\ProjectAcoustics\Resources*, spusťte *PatchWwise.bat*. Následující ukázkový obrázek používá náš ukázkový projekt AcousticsGame.

    ![Okno Průzkumníka Windows se zvýrazněným skriptem pro opravu Wwise](media/patch-wwise-script.png)

* Pokud nemáte nainstalovanou sadku DirectX SDK: V závislosti na verzi wwise, kterou používáte, `DXSDK_DIR` bude možná nutné komentovat řádek, který obsahuje v *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*:

    ![Editor kódu zobrazující 'DXSDK' komentoval](media/directx-sdk-comment.png)

* Pokud kompilujete pomocí sady Visual Studio 2019: Chcete-li obejít chybu propojení s Wwise, ručně změňte výchozí `VSVersion` hodnotu v *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* na **vc150**:

    ![Editor kódu zobrazující "VSVersion" změněn na "vc150"](media/vsversion-comment.png)

### <a name="build-the-game-and-check-that-python-is-enabled"></a>Sestavení hry a kontrola, zda je Python povolen

1. Zkompilujte hru a ujistěte se, že se staví správně. Pokud se nevytvoří, pečlivě zkontrolujte předchozí kroky, než budete pokračovat.

1. Otevřete projekt v editoru Unreal.

    > [!NOTE]
    > **Zásuvný modul motoru:** Pokud používáte ProjectAcoustics jako modul plug-in motoru, ujistěte se také, že je povolena v rámci "vestavěný" plug-in.

    Měli byste vidět nový režim, který označuje, že aplikace Akustika projektu byla integrována.

    ![Režim akustiky plný v unreal](media/acoustics-mode-full.png)

1. Zkontrolujte, zda je povolen modul plug-in Pythonu pro Unreal, takže integrace editoru funguje správně.

    ![Rozšíření Pythonu v editoru Unreal byla povolena](media/ensure-python.png)

### <a name="set-up-your-wwise-project-to-use-project-acoustics"></a>Nastavení projektu Wwise pro použití aplikace Project Acoustics

Příklad Wwise projekt je součástí ukázky ke stažení. Doporučujeme, abyste si ji prohlédli spolu s těmito pokyny. Snímky obrazovky dále v tomto článku jsou z tohoto projektu.

#### <a name="bus-setup"></a>Nastavení sběrnice
Modul plug-in Project Acoustics Unreal vyhledá přidružený modul plug-in mixeru `Project Acoustics Bus`na sběrnici, která má přesný název . Vytvořte novou zvukovou sběrnici se stejným názvem. Modul plug-in mixéru může pracovat v různých konfiguracích. Ale prozatím předpokládáme, že bude použit pouze pro zpracování reverbu. Tato sběrnice bude mít smíšený signál reverb u všech zdrojů, které používají akustiku. Může se promíchat proti proudu do jakékoliv struktury pro míchání autobusů. Příklad je zde zobrazen z ukázkového projektu Wwise, který je součástí ukázkového stahování.

![Wwise autobusy zobrazující Projekt Acoustics Bus](media/acoustics-bus.png)

1. Nastavte konfiguraci kanálu na sběrnici na *1.0*, *2.0*, *4.0*, *5.1*nebo *7.1*. Jakékoli jiné nastavení bude mít za následek žádný výstup na sběrnici.

    ![Možnosti konfigurace kanálu pro sběrač akustiky projektu](media/acoustics-bus-channel-config.png)

1. Přejděte do podrobností o sběrnici Akustiky projektu a ujistěte se, že vidíte záložku **Modul plug-in mixeru.**

    ![Wwise s kartou Plug-in mixeru pro aktivovanou sběrnici Akustiky projektu](media/mixer-tab-enable.png)

1. Přejděte na kartu **Modul plug-in mixeru** a přidejte do sběrnice modul plug-in mixer akustiky projektu.

    ![Diagram přidání modulu plug-in mixážního modulu Project Acoustics Mixer do sběrnice Wwise](media/add-mixer-plugin.png)

#### <a name="actor-mixer-hierarchy-setup"></a>Nastavení hierarchie mixeru actoru
Pro dosažení nejlepšího výkonu aplikace Project Acoustics aplikuje zpracování zvukového digitálního signálu na všechny zdroje současně. Takže plug-in musí fungovat jako modul plug-in mixéru. Wwise vyžaduje, aby moduly plug-in byly na výstupní sběrnici, ačkoli výstupní sběrnice obvykle nese suchý výstupní signál. Aplikace Project Acoustics vyžaduje, aby byl suchý návěsksměrován `Project Acoustics Bus`přes pomocné sběrnice, zatímco mokrý signál je přenášen na . Následující proces podporuje postupnou migraci na tento tok signálu.

Řekněme, že máte existující projekt s hierarchií mixeru actor, který obsahuje *kroky*, *zbraně*a další na nejvyšší úrovni. Každý z nich má odpovídající výstupní sběrnici pro svou suchou směs. Řekněme, že chcete migrovat kroky k použití akustiky. Nejprve vytvořte odpovídající aux sběrnici pro přepravu suchého submixu, který je dítětem výstupní sběrnice kroků. Například jsme použili předponu "Dry" na následujícím obrázku k uspořádání autobusů, i když přesný název není důležitý. Jakékoliv metry nebo efekty, které jste měli na stopách autobusu bude i nadále fungovat jako dříve.

![Doporučené nastavení suché směsi Wwise](media/wwise-dry-mix-setup.png)

Dále upravte strukturu výstupu sběrnice pro mixer objektu actor kroky následujícím způsobem, s *úlohou akustika projektu* nastavenou jako **výstupní sběrnici**a *Dry_Footsteps* nastavena jako uživatelem definovaná pomocná sběrnice.

![Doporučené Wwise actor mixer bus nastavení](media/actor-mixer-bus-settings.png)

Nyní všechny kroky získat akustickou léčbu a výstup jejich reverb na projektu Acoustics Bus. Suchý signál je veden přes Dry_Footsteps a prostorově jako obvykle.

Aplikace Project Acoustics se vztahuje pouze na zvuky, které mají 3D umístění na světě. Po [wwise dokumentaci](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/), polohovací vlastnosti musí být nastaveny tak, jak je znázorněno. Nastavení **3D prostorové izace** může být podle potřeby buď *Poloha,* *nebo Poloha + Orientace.*

![Doporučená nastavení umístění objektu Wwise Actor](media/wwise-positioning.png)

**Výstupní sběrnici** nelze nastavit na jinou sběrnici, která se promíchá proti proudu do *aplikace Project Acoustics Bus*. Wwise ukládá tento požadavek na mixer plug-inů.

Pokud chcete, aby dítě ve stopách hierarchie actor-mixer nepoužívalo akustiku, můžete ji odhlásit pomocí příkazu "přepsat nadřazený objekt".

Pokud používáte hry definované nebo uživatelem definované odešle pro reverb na libovolné actor-mixer ve hře, vypněte je na tomto actor-mixer, aby se zabránilo použití reverb dvakrát.

#### <a name="spatialization"></a>Prostorová izace
Modul plug-in mixer Aplikace Akustika Wwise standardně aplikuje reverb konvoluce a ponechá wwise prostorovou prostorizaci posouvání. Při použití aplikace Project Acoustics v této výchozí konfiguraci pouze pro dozvuku můžete použít libovolnou konfiguraci kanálu a metodu prostorové konfigurace v suchém mixu. Takže můžete kombinovat téměř všechny prostorové idivace s reverb projektu Akustika. Mezi vaše možnosti patří [binaurální prostorové izátory založené na ambisonics](https://www.audiokinetic.com/products/ambisonics-in-wwise/) a [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
Aplikace Project Acoustics obsahuje volitelný prostorový idezátor, který podporuje vykreslování a posouvání HRTF s vysokým rozlišením na základě objektu. Zaškrtněte políčko **Provést prostorovou orientaci** v nastavení modulu plug-in mixeru a zvolte mezi *HRTF* nebo *Posouváním*. Také zakázat uživatelem definované aux odešle do všech suché sběrnice, aby se zabránilo spatializing dvakrát modul plug-in mixer project acoustics a Wwise. Režim prostorové izace nelze změnit v reálném čase, protože vyžaduje regeneraci zvukové banky. Restartujte Unreal a poté regenerovat zvukové banky, než vyberete možnost Přehrát a integrujete změny konfigurace modulu plug-in mixeru, jako je například políčko **Provést prostorovou konfiguraci.**

![Nastavení prostorového zasávací modulu Wwise Mixer](media/mixer-spatial-settings.png)

Bohužel ostatní moduly plug-in prostorového izátoru založené na objektech nejsou aktuálně podporovány. Jsou implementovány jako moduly plug-in mixeru a Wwise neumožňuje přiřazení více modulů plug-in mixeru k jednomu modulu actor-mixer.  

### <a name="audio-setup-in-unreal"></a>Nastavení zvuku v Unreal
1. Nejprve je třeba upéct herní úroveň, abyste vytvořili akustický podklad, který bude umístěn v *aplikaci Content\Acoustics*. Podívejte se na [Unreal Pečeme tutorial](unreal-baking.md). Některé předpečené hladiny jsou zahrnuty v balení vzorku.

1. Vytvořte ve své scéně objekt actor prostoru akustiky. Vytvořte pouze jeden z těchto aktérů v úrovni, protože představuje akustiku pro celou úroveň.

    ![Vytvoření akustického vesmírného herce v editoru Unreal](media/create-acoustics-space.png)

1. Přiřaďte datový prostředek akustické akustika do datového slotu Akustika na objektu actor prostoru akustika. Vaše scéna má nyní akustiku!

    ![Přiřazení majetku akustiky v editoru Unreal](media/acoustics-asset-assign.png)

1. Přidejte prázdný objekt actor. Nakonfigurujte jej následujícím způsobem.

    ![Editor Unreal zobrazuje použití komponenty Akustika v prázdném objektu actor](media/acoustics-component-usage.png)

       
    <sup>1</sup> Přidejte do objektu actor zvukovou součást Akustiky. Tato součást přidává funkci akustika projektu do zvukové součásti Wwise.
        
    <sup>2</sup> Ve výchozím nastavení je vybráno pole **Přehrát na startu.** Toto nastavení aktivuje přidruženou událost Wwise při spuštění na úrovni.</li>
         
    <sup>3</sup> K tisku informací o ladění zdroje na obrazovce použijte zaškrtávací **políčko Zobrazit parametry akustiky.**

    ![Panel Unreal editor Acoustics na zdroji zvuku s povolenými ladicími hodnotami](media/debug-values.png)

    <sup>4</sup> Přiřaďte událost Wwise podle obvyklého pracovního postupu Wwise.
       
    <sup>5</sup> Ujistěte se, že je vypnutý **režim Použití prostorového zvuku.** Pokud používáte aplikaci Project Acoustics pro určitou zvukovou součást, nelze současně použít modul prostorového zvuku Wwise pro akustiku.</li>
       
Nyní je vše připraveno. Pohybovat po scéně a prozkoumat akustické účinky!

## <a name="next-steps"></a>Další kroky
* Vyzkoušejte [kurz návrhu akustiky projektu Unreal/Wwise](unreal-workflow.md).
* Naučte [se, jak to udělat peče pro](unreal-baking.md) vaše herní scény.
