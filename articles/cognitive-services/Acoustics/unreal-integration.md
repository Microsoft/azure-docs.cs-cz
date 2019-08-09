---
title: Projekt akustické Unreal a integrace Wwise
titlesuffix: Azure Cognitive Services
description: Tento postup popisuje integraci projektu akustické Unreal a Wwise moduly plug-in do projektu.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 3fe9a28a99ea8becbfc40e1e64d1f5b109caace3
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854375"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Projekt akustické Unreal a integrace Wwise
Tento postup poskytuje podrobné kroky pro integraci balíčku modulu plug-in projekt akustického prostředí do stávajícího projektu hry Unreal a Wwise. 

Požadavky na software:
* [Unreal Engine](https://www.unrealengine.com/) 4,20 nebo 4,21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018,1.\*
* [Modul plug-in Wwise pro Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  * Pokud používáte přímou integraci sady Wwise SDK namísto použití modulů plug-in Wwise Unreal, Projděte si modul plug-in Wwise akustické Unreal a upravte volání rozhraní API.

Pokud chcete použít akustické projekty se zvukovým strojem jiným než Wwise, udělejte žádost o zvýšení na diskuzi na [diskuzi na vláknech projektu](https://github.com/microsoft/ProjectAcoustics/issues). Můžete použít modul plug-in Unrealů projektu k dotazování na data akustického data a pak volání rozhraní API pro váš stroj.

## <a name="download-project-acoustics"></a>Stažení akustických projektů
Pokud jste to ještě neudělali, Stáhněte si [balíček modulu plug-in Unreal & Wwise](https://www.microsoft.com/download/details.aspx?id=58090). 

Do balíčku jsme zahrnuli modul plug-in Unreal Engine a modul plug-in Wwise mixer. Modul plug-in Unreal poskytuje integraci editoru a modulu runtime. Během hraní her je projekt Akustickě Unreal modulem plug-in, jako je překrytí pro každý objekt hry každý snímek. Tyto parametry jsou přeloženy na volání rozhraní API Wwise.

## <a name="review-integration-steps"></a>Kontrola kroků integrace

Existují tyto hlavní kroky pro instalaci balíčku a jeho nasazení ve hře.
1. Instalace modulu plug-in Wwise mixer v projektu
2. (Znovu) nasaďte Wwise do hry. Tento krok rozšíří modul plug-in směšovače do projektu hry.
3. Přidejte do hry modul plug-in Unrealy akustického projektu.
4. Rozšiřování funkcí Wwise modulů plug-in Unreal
5. Sestavení hry a kontroly Pythonu je povolené
6. Nastavení projektu Wwise pro použití akustického projektu
7. Nastavení zvuku v Unreal

## <a name="1-install-the-project-acoustics-mixer-plugin"></a>1. Instalace modulu plug-in pro směšovač akustického projektu
* Spusťte spouštěč Wwise a pak na kartě **moduly plug-** in v části **instalovat nové moduly plug-in**vyberte **Přidat z adresáře**. 

    ![Snímek obrazovky instalace modulu plug-in ve Spouštěči Wwise](media/wwise-install-new-plugin.png)

* `AcousticsWwisePlugin\ProjectAcoustics` Vyberte adresář, který byl zahrnut do balíčku, který jste stáhli. Obsahuje sadu prostředků modulu plug-in Wwise mixer.

* Wwise bude modul plug-in nainstalovat. V seznamu nainstalovaných modulů plug-in v Wwise by se teď měly zobrazit akustické akustické projekty.
![Snímek obrazovky s nainstalovaným modulem plug-in Wwise po instalaci akustického projektu](media/unreal-integration-post-mixer-plugin-install.png)

## <a name="2-redeploy-wwise-into-your-game"></a>2. (Znovu) nasazení Wwise do hry
Znovu nasaďte Wwise do své hry i v případě, že jste už integraci Wwise. Tím se vybere Wwise modul plug-in pro projekt.

* **Modul plug-in stroje:** Pokud jste Wwise nainstalovali jako herní modul plug-in v projektu C++ Unreal, přeskočte tento krok. Pokud je nainstalovaná jako modul plug-in modulu, například protože váš projekt Unreal je jenom v podrobném plánu, nasazení Wwise pomocí našeho zásuvného modulu Mixer je složitější. Vytvořte fiktivní, prázdný projekt Unreal C++ , zavřete jej, pokud se otevře Editor Unreal, a postupujte podle zbývajících kroků k nasazení Wwise do tohoto fiktivního projektu. Pak zkopírujte nasazený modul plug-in Wwise.
 
* Z Wwise spouštěče klikněte na kartu **modul Unreal** , potom klikněte na nabídku hamburgerovou "vedle položky **Poslední projekty Unreal Engine** a vyberte **Procházet pro projekt**. Otevřete soubor projektu `.uproject` Unreal vaší hry.

    ![Snímek karty Unreal spouštěče Wwise](media/wwise-unreal-tab.png)

* Pak klikněte na možnost **integrace Wwise v aplikaci Project** nebo **Upravit Wwise v projektu**. Tento krok (znovu) integruje do svého projektu binární soubory Wwise, a to teď včetně modulu plug-in pro směšovač akustického projektu.

* **Modul plug-in stroje:** Pokud používáte Wwise jako modul plug-in modulu a vytvořili jste fiktivní projekt výše, zkopírujte Wwise nasazené složky: `[DummyUProject]\Plugins\Wwise` a vložte ji přes. `[UESource]\Engine\Plugins\Wwise` `[DummyUProject]`je prázdná cesta k C++ projektu Unreal a `[UESource]` je tam, kde máte nainstalované zdroje Unreal Engine. Po dokončení kopírování můžete zástupný projekt odstranit.

## <a name="3-add-the-project-acoustics-unreal-plugin-to-your-game"></a>3. Přidejte do hry modul plug-in Unrealy akustického projektu.
 
* Zkopírujte složku do balíčku modulu plug-in a vytvořte novou složku `[UProjectDir]\Plugins\ProjectAcoustics`, kde `UProjectDir` `.uproject` je složka projektu vaší hry obsahující soubor. `Unreal\ProjectAcoustics`
  * **Modul plug-in stroje**: Pokud používáte Wwise jako modul plug-in modulu, měli byste použít také zvukové aplikace projektu jako modul plug-in Unreal Engine. Místo výše uvedeného cílového adresáře použijte: `[UESource]\Engine\Plugins\ProjectAcoustics`.

* Potvrďte, že `Wwise` se `ProjectAcoustics` vedle složky zobrazuje složka. Obsahuje modul plug-in Wwise spolu s binárními soubory pro modul plug-in mixer, který jste (znovu) nasadili v kroku 2 výše.

## <a name="4-extend-wwises-unreal-plugin-functionality"></a>4. Rozšiřování funkcí Wwise modulů plug-in Unreal
* Modul plug-in Unreal pro projekt vyžaduje dodatečné chování z rozhraní API modulu plug-in Wwise Unreal na [těchto pokynech](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). Zahrnuli jsme soubor Batch pro automatizaci postupu oprav. 
* Uvnitř `Plugins\ProjectAcoustics\Resources`spusťte .`PatchWwise.bat` Následující příklad obrázku používá ukázkový projekt AcousticsGame.

    ![Snímek obrazovky okna Průzkumníka Windows, který vyzvýrazňuje zadaný skript k opravě Wwise](media/patch-wwise-script.png)

* Pokud sadu DirectX SDK nemáte nainstalovanou, budete muset komentovat řádek obsahující DXSDK_DIR.`[UProject]\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![Snímek obrazovky editoru kódu zobrazující DXSDK s komentářem](media/directx-sdk-comment.png)

## <a name="5-build-game-and-check-python-is-enabled"></a>5. Sestavení hry a kontroly Pythonu je povolené

* Zkompilujte hru a ujistěte se, že je správně vytvořena. V opačném případě před pokračováním zkontrolujte pečlivě předchozí kroky. 
* Otevřete projekt v editoru Unreal. 
* **Modul plug-in stroje:** Pokud používáte modul plug-in modulu ProjectAcoustics jako modul plug-in, zajistěte, aby byl povolený, uvedený v části integrované moduly plug-in.
* Měl by se zobrazit nový režim, který indikuje, že jsou v projektu akustické akustické.

    ![Snímek obrazovky s Unrealem ukazujícím na plný režim akustického režimu](media/acoustics-mode-full.png)

* Potvrďte, že máte povolený modul plug-in Python pro Unreal. To je nutné, aby integrace editoru správně fungovala.

    ![Snímek obrazovky s povolením rozšíření Pythonu v editoru Unreal](media/ensure-python.png)

## <a name="6-wwise-project-setup"></a>6. Nastavení projektu Wwise

Ukázkový projekt Wwise je součástí stažení ukázek. Doporučujeme vám, abyste si ho prohledali společně s těmito pokyny. Snímky obrazovky níže jsou pořízeny z tohoto projektu.

### <a name="bus-setup"></a>Nastavení sběrnice
* Modul plug-in Unreal projektu bude hledat přidružený modul plug-in směšovače na sběrnici s tímto přesným názvem: `Project Acoustics Bus`. Vytvořte novou zvukovou sběrnici s tímto názvem. Modul plug-in směšovače může pracovat v různých konfiguracích, ale v současné době předpokládáme, že se použije jenom k reverb zpracování. Tato sběrnice bude mít smíšený signál Reverb pro všechny zdroje, které využívají akustické prostředky. Může sloučit datový proud do jakékoli struktury směšování sběrnice. příklad je uveden níže, z našeho ukázkového projektu Wwise, který je zahrnutý v ukázkovém souboru ke stažení.

    ![Snímek obrazovky Wwise busses znázorňující sběrnici akustického projektu](media/acoustics-bus.png)

* Konfigurace kanálu na sběrnici musí být nastavená na jednu z těchto: `1.0, 2.0, 4.0, 5.1 or 7.1`. Jiné konfigurace nebudou mít v této sběrnici žádný výstup.

    ![Snímek obrazovky s možnostmi konfigurace kanálu pro sběrnici projektového akustického množství](media/acoustics-bus-channel-config.png)

* Teď přejděte k podrobnostem o akustické sběrnici projektu a ujistěte se, že se zobrazí karta plug-in směšovače.

    ![Snímek obrazovky s Wwisem, který ukazuje, jak povolit kartu modulu plug-in směšovače pro sběrnici projektového akustického množství](media/mixer-tab-enable.png)

* Pak přejdete na kartu plug-in směšovače a přidejte do sběrnice modul plug-in směšovače projektu.

    ![Screenshow sběrnice Wwise, která ukazuje, jak přidat modul plug-in směšovače akustického množství projektu](media/add-mixer-plugin.png)

### <a name="actor-mixer-hierarchy-setup"></a>Objekt actor – nastavení hierarchie směšovače
* Z důvodu výkonu akustické účinky projektu aplikují na všechny zdroje současně zvuk DSP. To vyžaduje, aby modul plug-in pracoval jako modul plug-in směšovače. Wwise vyžaduje, aby byly na výstupní sběrnici směšovací moduly plug-in, i když výstupní sběrnice obvykle dosahuje suchého výstupního signálu. Akustické projekty vyžadují, aby byl suchý signál směrován přes AUX busses, zatímco je na něm zajištěný `Project Acoustics Bus`signál za mokrého signálu. Následující postup podporuje postupnou migraci na tento tok signálu.

* Řekněme, že máte existující projekt s hierarchií pro míchání objektů actor obsahující stopách, zbraněmi a ostatními na nejvyšší úrovni. Každá z nich má odpovídající výstupní sběrnici pro svou suchou kombinaci. Řekněme, že chcete migrovat stopách, aby se použily akustické hodnoty. Nejdřív vytvořte odpovídající pomocnou sběrnici, která bude mít za sebou jejich suchou submix, která je podřízenou položkou výstupní sběrnice stopách. Například na obrázku níže používáme předponu "suchá", ale přesný název není důležitý. Všechny měřiče nebo účinky, které jste měli na sběrnici stopách, budou pořád fungovat stejně jako dřív.

    ![Snímek obrazovky s doporučeným nastavením Wwise suchého míchání](media/wwise-dry-mix-setup.png)

* Pak upravte výstupní strukturu sběrnice pro stopách objektu actor – mixer následujícím způsobem, se sběrnicí projektu s akustickou a nastavenou jako výstupní sběrnicí a Dry_Footsteps jako uživatelsky definovaná pomocná sběrnice.

    ![Snímek obrazovky doporučených nastavení sběrnice Wwise actor](media/actor-mixer-bus-settings.png)

* Nyní všechny stopáchy nastanou akustickou úpravu a výstup jejich reverb na akustické sběrnici projektu. Suchý signál je směrován přes Dry_Footsteps a pracuje jako obvykle.

* Akustické projekty se vztahují pouze na zvuky, které mají 3D umístění na světě. Po [Wwise dokumentaci](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)musí být nastavené vlastnosti umístění, jak je znázorněno na obrázku. V případě potřeby může být nastavení "prostorové rozkládá" buď "poloha" nebo "poloha + orientace".

    ![Snímek obrazovky s doporučeným nastavením umístění objektu actor Wwise](media/wwise-positioning.png)

* Nastavení výstupní sběrnice na jinou sběrnici, která smíchá s tím, že nefunguje streamování na **akustické sběrnici projektu** , nebude fungovat. Wwise ukládá tento požadavek na směšovací moduly plug-in.

* Pokud chcete, aby podřízená položka v hierarchii stopách actor-mixer nepoužívala akustické možnosti, můžete k tomu použít možnost "přepsat nadřazenou položku", která ji odsouhlasí.

* Pokud používáte herní nebo uživatelsky definovaná odeslání pro reverb na jakémkoli směšovači objektů actor ve hře, vypněte je u tohoto objektu pro směšovač objektů Actor, abyste se vyhnuli použití reverb dvakrát.

### <a name="spatialization"></a>Prostorová
Ve výchozím nastavení používá modul plug-in Wwise mixer v projektu reverb konvoluce, který vynechává Wwise pro posouvání. Při používání akustických dat projektu v této výchozí konfiguraci jen pro reverb můžete použít veškerou metodu konfigurace a uspořádání kanálů v suchém míchání, což vám umožní míchat a porovnat prakticky všechny spatializery s reverb akustickými možnostmi projektů. Mezi tyto možnosti patří binaural spatializers a [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound) [založené na Ambisonics](https://www.audiokinetic.com/products/ambisonics-in-wwise/) .
 
Akustické projekty obsahují volitelný spatializer, který podporuje vykreslování HRTF s vysokým rozlišením založeného na objektech a posouvání. Zaškrtněte políčko provést rozpracované prostředí v nastavení modulu plug-in směšovače a vyberte mezi HRTFem nebo posouváním a zakažte uživatelsky definované odeslání nastavené výše do všech suchých bussesů, aby se předešlo spatializing dvakrát, s akustickým modulem plug-in a Wwise. Režim rozkládání nejde v reálném čase změnit, protože vyžaduje obnovení ze zvukových bank. Musíte restartovat Unreal a pak znovu vygenerovat soundbanks, abyste mohli začít hrát změny konfigurace modulu plug-in mixer, jako je zaškrtávací políčko provést rozložení.

![Snímek obrazovky s nastavením modulu plug-in Wwise mixer](media/mixer-spatial-settings.png)

Některé moduly plug-in spatializer založené na objektech bohužel nejde v tuto chvíli podporovat, protože jsou implementované jako směšovací moduly plug-in a Wwise v současnosti nepovoluje více modulů plug-in přiřazených jednomu objektu pro směšovač objektů actor.  

## <a name="7-audio-setup-in-unreal"></a>7. Nastavení zvuku v Unreal
* Nejprve budete muset zanesli svou herní úroveň a vydávat akustickou prostředek, který bude umístěn v `Content\Acoustics`. Projděte si [kurz Unreal zanesli](unreal-baking.md) a pokračujte tady. Do ukázkového balíčku jsou zahrnuté některé předem vloženými úrovně.
* Ve scéně vytvořte objekt actor s akustickým místem. Vytvořte pouze jeden z těchto aktérů na úrovni, protože představuje akustické množství pro celou úroveň. 

    ![Snímek obrazovky s editorem Unreal ukazující vytvoření objektu actor pro prostor s akustickým místem](media/create-acoustics-space.png)

* Teď přiřadíte akustickou datovou Asset vloženými k datovým otvorům s akustickým místem v objektu actor. Vaše scéna teď obsahuje zvukové služby.

    ![Snímek obrazovky s přiřazením prostředků howing v editoru Unreal](media/acoustics-asset-assign.png)

* Nyní přidejte prázdný objekt actor a proveďte následující akce:

    ![Snímek obrazovky s editorem Unreal zobrazující akustické využití komponent v prázdném objektu actor](media/acoustics-component-usage.png)

1. Přidejte do objektu actor akustickou zvukovou komponentu. Tato součást rozšiřuje zvukovou komponentu Wwise o funkce pro akustické projekty.
2. Ve výchozím nastavení je zaškrtnuto políčko přehrát při startu, které aktivuje přidruženou událost Wwise při spuštění na úrovni.
3. Pomocí zaškrtávacího políčka Zobrazit parametry akustického tisku můžete vytisknout informace o ladění zdroje na obrazovce.
    ![Snímek obrazovky s Unreal Editor zvuku na zvukovém zdroji s povolenými hodnotami ladění](media/debug-values.png)
4. Přiřazení události Wwise pro běžný pracovní postup Wwise
5. Ujistěte se, že je vypnuté používání prostorového zvuku. V tuto chvíli, pokud pro konkrétní zvukovou komponentu používáte akustické projekty, nemůžete současně použít modul prostorového zvuku Wwise pro akustické práci.

Nyní je vše připraveno. Pohybujte kolem scény a prozkoumejte akustické účinky.

## <a name="next-steps"></a>Další kroky
* Kurz [návrhu](unreal-workflow.md) pro akustické a projektové akustické práce v Unreal/Wwise
* [Přečtěte si, jak se vytváří](unreal-baking.md) na scény vaší hry. 
