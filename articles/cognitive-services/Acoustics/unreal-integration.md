---
title: Projekt akustické Unreal a integrace Wwise
titlesuffix: Azure Cognitive Services
description: Tento článek popisuje integraci projektových Unreal a modulů plug-in Wwise do projektu.
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
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243047"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Projekt akustické Unreal a integrace Wwise
Tento článek popisuje, jak integrovat balíček modulu plug-in s akustickými vlivy projektu do existujícího projektu hry Unreal a Wwise.

Požadavky na software:
* [Unreal Engine](https://www.unrealengine.com/) 4.20 +
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018,1
* [Modul plug-in Wwise pro Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  
  Pokud používáte přímou integraci sady Wwise SDK namísto modulu plug-in Wwise Unreal, přečtěte si článek modul plug-in Wwise akustické a upravte volání rozhraní API.

Chcete-li použít akustické projekty s jiným zvukovým strojem než Wwise, udělejte požadavek na zvýšení na [diskuzi na diskuzi v projektu](https://github.com/microsoft/ProjectAcoustics/issues). Můžete použít modul plug-in Unreale projektů k dotazování na data akustického data a volat rozhraní API pro váš stroj.

## <a name="download-project-acoustics"></a>Stažení akustických projektů
Pokud jste to ještě neudělali, Stáhněte si [projekt s Unreal a Wwise modul plug-in](https://www.microsoft.com/download/details.aspx?id=58090) .

V balíčku je zahrnutý modul plug-in Unreal Engine a modul plug-in Wwise mixer. Modul plug-in Unreal poskytuje integraci editoru a modulu runtime. Během hraní her Unreal modul plug-in z hlediska výpočtů parametry, jako je překrytí pro každý objekt hry pro každý snímek. Tyto parametry jsou přeloženy do volání rozhraní API Wwise.

## <a name="integration-steps"></a>Kroky integrace

Pomocí těchto kroků nainstalujete balíček a nasadíte ho do hry.

### <a name="install-the-project-acoustics-mixer-plug-in"></a>Instalace modulu plug-in pro směšovač akustického a více projektů
1. Otevřete spouštěč Wwise. Na kartě **moduly plug-in** v části **instalovat nové moduly plug-in**vyberte **Přidat z adresáře**.

    ![Instalace modulu plug-in ve Spouštěči Wwise](media/wwise-install-new-plugin.png)

1. Vyberte adresář *AcousticsWwisePlugin\ProjectAcoustics* , který je v balíčku ke stažení. Obsahuje sadu modulů plug-in Wwise mixer.

   Wwise nainstaluje modul plug-in. V seznamu nainstalovaných modulů plug-in v Wwise by se měly objevit akustické projekty.  
![Seznam modulů plug-in nainstalovaných Wwise po instalaci akustického projektu](media/unreal-integration-post-mixer-plugin-install.png)

### <a name="dedeploy-wwise-into-your-game"></a>Nasaďte Wwise do hry.
Wwise se do hry znovu nasaďte i v případě, že už jste Wwise. Tento krok integruje Wwise modul plug-in projektového akustického cloudu.

   > [!NOTE]
   > **Modul plug-in modulu:** Pokud jste Wwise nainstalovali jako herní modul plug-in v projektu Unreal C++ , přeskočte tento krok. Pokud je nainstalovaná jako modul plug-in modulu, například, protože projekt Unreal je jenom v podrobném plánu, nasazení Wwise s modulem plug-in směšovače je složitější. Vytvořte fiktivní prázdný projekt Unreal C++ . Zavřete Editor Unreal, pokud se otevře, a postupujte podle zbývajících kroků k nasazení Wwise do fiktivního projektu. Pak zkopírujte nasazený modul plug-in Wwise.
 
1. Ve Spouštěči Wwise vyberte kartu **modul Unreal** . Vyberte nabídku hamburgerovou "(ikona) vedle položky **Poslední projekty Unreal Engine** a pak vyberte **vyhledat projekt**. Otevřete soubor projektu Unreal vaší hry *.*

    ![Karta Unreal spouštěče Wwise](media/wwise-unreal-tab.png)

1. Vyberte možnost **integrovat Wwise do projektu** nebo **Upravit Wwise v projektu**. Tento krok integruje do svého projektu binární soubory Wwise, včetně modulu plug-in pro směšovač akustického projektu.

   > [!NOTE]
   > **Modul plug-in modulu:** Pokud používáte Wwise jako modul plug-in modulu a vytvořili jste fiktivní projekt, jak je popsáno výše, zkopírujte složku, která Wwise nasazena: *[DummyUProject] \Plugins\Wwise*. Vložte ho přes *[UESource] \Engine\Plugins\Wwise*. *[DummyUProject]* je prázdná cesta k C++ projektu Unreal a *[UESource]* je, kde jsou nainstalovány zdroje modulu Unreal. Po zkopírování složky můžete odstranit fiktivní projekt.

### <a name="add-the-project-acoustics-unreal-plug-in-to-your-game"></a>Přidejte do hry modul plug-in Unrealy projektu.
 
1. Zkopírujte složku *Unreal\ProjectAcoustics* do balíčku modulů plug-in. Vytvoří novou složku *[UProjectDir] \Plugins\ProjectAcoustics*, kde *[UProjectDir]* je složka projektu vaší hry, která obsahuje soubor *. uproject* .

   > [!NOTE]
   > Modul **Plug-in pro modul**: Pokud používáte Wwise jako modul plug-in pro modul, měli byste použít také zvukové aplikace projektu jako modul plug-in Unreal Engine. Místo cílového adresáře citovaného dříve použijte *[UESource] \Engine\Plugins\ProjectAcoustics*.

1. Potvrďte, že se vedle složky *ProjectAcoustics* zobrazuje složka *Wwise* . Obsahuje modul plug-in Wwise spolu s binárními soubory pro modul plug-in směšovače, který jste nasadili dříve.

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Rozšiřování funkcí modulu plug-in Wwise Unreal
Modul plug-in Unreal pro projekt vyžaduje další chování vystavené z rozhraní API modulu plug-in Wwise Unreal na [těchto pokynech](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). Zahrnuli jsme soubor Batch pro automatizaci postupu oprav.

* V *Plugins\ProjectAcoustics\Resources*spusťte *PatchWwise. bat*. Následující příklad obrázku používá ukázkový projekt AcousticsGame.

    ![Okno Průzkumníka Windows se zvýrazněným skriptem k opravě Wwise](media/patch-wwise-script.png)

* Pokud sadu DirectX SDK nemáte nainstalovanou: v závislosti na verzi Wwise, kterou používáte, může být potřeba odkomentovat řádek, který obsahuje `DXSDK_DIR` v *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*:

    ![Editor kódu zobrazující komentář "DXSDK"](media/directx-sdk-comment.png)

* Pokud kompilujete pomocí sady Visual Studio 2019: Chcete-li se vyhnout chybě propojení s Wwise, ručně změňte výchozí hodnotu `VSVersion` v *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* na **vc150**:

    ![Editor kódu zobrazující "VSVersion" změněn na "vc150"](media/vsversion-comment.png)

### <a name="build-the-game-and-check-that-python-is-enabled"></a>Sestavte hru a ověřte, jestli je Python povolený.

1. Zkompilujte hru a ujistěte se, že je správně vytvořena. Pokud se nesestaví, před pokračováním zkontrolujte předchozí kroky pečlivě.

1. Otevřete projekt v editoru Unreal.

    > [!NOTE]
    > **Modul plug-in modulu:** Pokud používáte ProjectAcoustics jako modul plug-in, ujistěte se také, že je povolený v části vestavěné moduly plug-in.

    Měl by se zobrazit nový režim, který indikuje, že jsou integrované projektové akustické.

    ![Režim akustického naplnění v Unreal](media/acoustics-mode-full.png)

1. Potvrďte, že modul plug-in Python pro Unreal je povolený, aby funkce integrace editoru fungovala správně.

    ![Rozšíření Pythonu v editoru Unreal povolená](media/ensure-python.png)

### <a name="set-up-your-wwise-project-to-use-project-acoustics"></a>Nastavení projektu Wwise pro použití akustického projektu

Ukázkový projekt Wwise je součástí stažení ukázek. Doporučujeme, abyste si ho zobrazili spolu s těmito pokyny. Snímky obrazovky dále v tomto článku jsou z tohoto projektu.

#### <a name="bus-setup"></a>Nastavení sběrnice
Modul plug-in projekt akustické Unrealy bude hledat přidružený modul plug-in směšovače na sběrnici, která má přesný název `Project Acoustics Bus`. Vytvořte novou zvukovou sběrnici, která má stejný název. Modul plug-in směšovače může pracovat v různých konfiguracích. Ale teď předpokládáme, že se použije jenom pro zpracování reverb. Tato sběrnice bude mít smíšený signál Reverb pro všechny zdroje, které využívají akustické prostředky. Může namíchat datový proud do jakékoli struktury pro kombinování sběrnic. Příklad je zobrazen zde z ukázkového projektu Wwise, který je součástí stažení ukázkového souboru.

![Wwise sběrnice ukazující sběrnici akustického projektu](media/acoustics-bus.png)

1. Nastavte konfiguraci kanálu na sběrnici na *1,0*, *2,0*, *4,0*, *5,1*nebo *7,1*. Jakékoli jiné nastavení nebude mít na sběrnici žádný výstup.

    ![Možnosti konfigurace kanálu pro sběrnici projektového akustického navýšení](media/acoustics-bus-channel-config.png)

1. Přejděte do části Podrobnosti o akustické sběrnici projektu a ujistěte se, že se vám zobrazí karta **Plug-in směšovače** .

    ![Wwise pomocí karty Plug-in směšovače pro povolenou sběrnici projektu](media/mixer-tab-enable.png)

1. Přejdete na kartu **Plug-in směšovače** a přidejte do ní modul plug-in směšovače akustického prostředí projektu.

    ![Diagram postupu přidání modulu plug-in směšovače z projektu do sběrnice Wwise](media/add-mixer-plugin.png)

#### <a name="actor-mixer-hierarchy-setup"></a>Objekt actor – nastavení hierarchie směšovače
Pro dosažení co nejlepších výsledků aplikuje aplikace na zpracování zvuku digitální signály na všechny zdroje současně. Modul plug-in proto musí fungovat jako modul plug-in směšovače. Wwise vyžaduje, aby byly moduly plug-in směšovače na výstupní sběrnici, i když výstupní sběrnice obvykle přináší suchý výstupní signál. Akustické projekty vyžadují, aby byl suchý signál směrován přes pomocné sběrnice, zatímco mokrý signál se přenese na `Project Acoustics Bus`. Následující postup podporuje postupnou migraci na tento tok signálu.

Řekněme, že máte existující projekt s hierarchií směšovače Actor, která obsahuje *stopách*, *zbraněs*a další na nejvyšší úrovni. Každá z nich má odpovídající výstupní sběrnici pro svou suchou kombinaci. Řekněme, že chcete migrovat stopách pro používání akustických přenosů. Nejdřív vytvořte odpovídající pomocnou sběrnici pro přenos suchého submixu, který je podřízeným prvkem sběrnice stopách Output. Například používáme předponu "suchá" na následujícím obrázku pro uspořádání sběrnice, přestože přesný název není důležitý. Všechny měřiče nebo efekty, které jste měli na sběrnici stopách, budou pořád fungovat stejně jako dřív.

![Doporučené nastavení Wwise suchého míchání](media/wwise-dry-mix-setup.png)

V dalším kroku Upravte výstupní strukturu sběrnice pro stopách herce actor následujícím způsobem, se *sběrnicí projektu s akustickou* sadou jako **výstupní sběrnicí**a *Dry_Footsteps* nastavte jako uživatelskou pomocnou sběrnici.

![Doporučené nastavení sběrnice Wwise actor](media/actor-mixer-bus-settings.png)

Nyní všechny stopáchy nastanou akustickou úpravu a výstup jejich reverb na akustické sběrnici projektu. Suchý signál je směrován přes Dry_Footsteps a pracuje jako obvykle.

Akustické projekty se vztahují pouze na zvuky, které mají 3D umístění na světě. Po [Wwise dokumentaci](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)musí být nastavené vlastnosti umístění, jak je znázorněno na obrázku. V **případě potřeby může být nastavení** prostorového umisťování buď *poloha* , nebo *pozice + orientace* .

![Doporučené nastavení umístění objektu actor Wwise](media/wwise-positioning.png)

Nemůžete nastavit **sběrnici výstupu** na jinou sběrnici, která se vzájemně smíchá proti proudu na *akustické sběrnici projektu*. Wwise ukládá tento požadavek na moduly plug-in směšovače.

Pokud chcete, aby podřízená položka v hierarchii stopách actor-mixer nepoužívala akustické možnosti, můžete k tomu použít "přepsat nadřazenou položku".

Pokud používáte herní nebo uživatelsky definované odeslání pro reverb na jakémkoli směšovači objektů actor ve hře, vypněte je v tomto okně pro směšovač objektů Actor, abyste se vyhnuli použití reverb dvakrát.

#### <a name="spatialization"></a>Prostorová
Modul plug-in Wwise mixer má za následek, že ve výchozím nastavení aplikuje reverb konvoluce, což zanechává Wwise pro posouvání. Když použijete akustické prostředí projektu v této výchozí konfiguraci jen pro reverb, můžete použít libovolnou metodu konfigurace a uspořádání kanálů v suché kombinaci. Takže můžete kombinovat a porovnat téměř všechny spatializer s reverb akustickými projekty. Mezi tyto možnosti patří binaural spatializers a [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound) [založené na Ambisonics](https://www.audiokinetic.com/products/ambisonics-in-wwise/) .
 
Akustické projekty obsahují volitelnou spatializer, která podporuje vykreslování a posouvání HRTF s vysokým rozlišením založeného na objektech. V nastavení modulu plug-in směšovače zaškrtněte políčko **provést Rozprostorování** a zvolte mezi *HRTFem* nebo *posouváním*. Zakažte také, aby uživatelsky definované pomocné moduly odesílaly na všechny suché sběrnice, aby se spatializing dvakrát modulem plug-in a Wwisey směšovače v projektu. Režim rozkládání nejde v reálném čase změnit, protože vyžaduje obnovení ze zvukových bank. Než vyberete Play, restartujte Unreal a potom znovu vygenerujte soundbanks, abyste mohli integrovat změny konfigurace modulu plug-in směšovače, jako je například zaškrtávací políčko **provést** rozložení.

![Nastavení prostorů plug-in Wwise mixer](media/mixer-spatial-settings.png)

Bohužel nejsou v současné době podporovány jiné moduly plug-in spatializer založené na objektech. Jsou implementovány jako moduly plug-in směšovače a Wwise neumožňuje přiřadit více modulů plug-in s jedním objektem actor-mixer.  

### <a name="audio-setup-in-unreal"></a>Nastavení zvuku v Unreal
1. Nejdřív je potřeba, abyste zanesli svou herní úroveň a vytvořili akustický prostředek, který bude umístěný v *Content\Acoustics*. Projděte si [kurz zanesli pro Unreal](unreal-baking.md). Do ukázkového balíčku jsou zahrnuté některé předem vloženými úrovně.

1. Ve scéně vytvořte objekt actor s akustickým místem. Vytvořte pouze jeden z těchto aktérů na úrovni, protože představuje akustické množství pro celou úroveň.

    ![Vytvoření objektu actor s akustickým prostorem v editoru Unreal](media/create-acoustics-space.png)

1. Přiřaďte vloženými akustický datový prostředek k datovým otvoru akustického prostoru v objektu actor. Vaše scéna teď obsahuje zvukové služby.

    ![Přiřazení prostředků akustického materiálu v editoru Unreal](media/acoustics-asset-assign.png)

1. Přidejte prázdný objekt actor. Nakonfigurujte ho následujícím způsobem.

    ![Editor Unreal ukazuje využití částí v prázdném objektu actor.](media/acoustics-component-usage.png)

       
    <sup>1</sup> do objektu actor přidejte zvukovou komponentu akustického přenosu. Tato součást přidá funkci akustického projektu do zvukové komponenty Wwise.
        
    <sup>2</sup> ve výchozím nastavení je vybráno pole **Přehrát v startu** . Toto nastavení aktivuje přidruženou událost Wwise na úrovni spouštění.</li>
         
    <sup>3</sup> pomocí zaškrtávacího políčka **Zobrazit zvukové parametry** můžete vytisknout informace o ladění na obrazovce zdroje.

    ![Panel akustického naUnrealho editoru na zdroji zvuku s povolenými hodnotami ladění](media/debug-values.png)

    <sup>4</sup> přiřaďte událost Wwise pro běžný pracovní postup Wwise.
       
    <sup>5</sup> Ujistěte se, že je vypnuté **používání prostorového zvuku** . Použijete-li pro konkrétní zvukovou komponentu akustické projekty, nemůžete současně použít modul prostorového zvuku Wwise pro akustické využití.</li>
       
Všechno je nastavené. Pohybujte kolem scény a prozkoumejte akustické účinky.

## <a name="next-steps"></a>Další kroky
* Vyzkoušejte si [kurz Unreal/Wwise pro návrh projektu](unreal-workflow.md).
* Naučte [se, jak se vytváří](unreal-baking.md) na scény vaší hry.
