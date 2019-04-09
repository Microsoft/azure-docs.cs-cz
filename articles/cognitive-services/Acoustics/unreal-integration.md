---
title: Projektů Unreal Akustika a Wwise integrace
titlesuffix: Azure Cognitive Services
description: Tento návod popisuje integraci projektu Akustika Unreal a Wwise modulů plug-in do projektu.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: c6baa9f8330338c1e5fdc9ee0b5a8cc8b344e871
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006495"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Projektů Unreal Akustika a Wwise integrace
Tento návod obsahuje kroky podrobné integrace balíčku modulu plug-in Akustika projektu do existujícího projektu her Unreal a Wwise. 

Požadavky na software:
* [Unreal Engine](https://www.unrealengine.com/) 4.20 nebo 4.21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1.\*
* [Modul plug-in Wwise pro Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  * Pokud používáte přímou integraci sady Wwise SDK namísto použití modulů plug-in Wwise Unreal, naleznete v projektu Akustika Unreal modulu plug-in a upravte volání rozhraní API Wwise.

Pokud chcete pomocí projektu Akustika zvuku modul než Wwise, kontaktujte nás na [projektu Akustika fóra](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics). Projekt Akustika Unreal modul plug-in můžete použít k dotazování na data Akustika a volat rozhraní API pro modul.

## <a name="download-project-acoustics"></a>Stáhněte si Akustika projektu
Pokud jste tak dosud neučinili, stáhněte si [projektu Akustika Unreal & Wwise balíček modulu plug-in](https://www.microsoft.com/download/details.aspx?id=58090)). 

V balíčku jsme zahrnuli plug-in mixer Wwise a modulu plug-in Unreal Engine. Modul plug-in pro Unreal poskytuje editor a modulu runtime integrace. Během hratelnost modul plug-in pro projekt Akustika Unreal vypočítá parametry, jako jsou vhodné pro každého objektu každý snímek. Tyto parametry jsou převedeny na volání rozhraní API Wwise.

## <a name="review-integration-steps"></a>Projděte si postup, integrace

Existují tyto hlavní kroky k instalaci balíčku a nasaďte ho ve svých hrách.
1. Instalace modulu plug-in mixer Wwise Akustika projektu
2. (Re) nasaďte Wwise her. Tento krok rozšíří mixer modulu plug-in do projektu her.
3. Přidání projektu Akustika Unreal modulu plug-in pro hry
4. Rozšíření funkcí na Wwise Unreal modulu plug-in
5. Vytvořte hru a zkontrolujte, že je povolená Pythonu
6. Nastavení projektu Wwise používat Akustika projektu
7. Zvukový instalační program v Unreal

## <a name="1-install-the-project-acoustics-mixer-plugin"></a>1. Instalace modulu plug-in mixer Akustika projektu
* Otevřete Wwise spouštěče a pak v **moduly plug-in** ve skupině **nainstalovat nové moduly plug-in**vyberte **přidat z adresáře**. 

    ![Snímek obrazovky Instalace modulu plug-in ve Spouštěči Wwise](media/wwise-install-new-plugin.png)

* Zvolte `AcousticsWwisePlugin\ProjectAcoustics` adresář, který je zahrnutý v balíčku, který jste stáhli. Obsahuje sadu Wwise mixer modulu plug-in.

* Wwise nainstaluje modul plug-in. Akustika projektu by měl nyní zobrazí v seznamu nainstalovaných modulů plug-in Wwise.
![Seznam modulů plug-in snímek obrazovky Wwise nainstalovat po instalaci Akustika projektu](media/unreal-integration-post-mixer-plugin-install.png)

## <a name="2-redeploy-wwise-into-your-game"></a>2. (Re) nasadit Wwise do hry
I v případě, že jste už integrovali Wwise znovu nasaďte Wwise her. Vybere modul plug-in Wwise Akustika projektu.

* **Modul plug-in:** Pokud máte nainstalován jako herní modul plug-in v projektu Unreal C++ Wwise, tento krok přeskočte. Pokud je nainstalována jako modul plug-in, místo toho pro instanci, protože Unreal projektu je nasazení jenom, Wwise podrobného plánu s modulem plug-in naše mixer je složitější. Vytvoření projektu jazyka Unreal C++ fiktivní, prázdný, zavřete ho, pokud se otevře Unreal editor a postupujte podle zbývajících pokynů nasaďte Wwise do tohoto projektu fiktivní. Zkopírujte si nasazené Wwise modulu plug-in.
 
* Ze Spouštěče Wwise, klikněte na tlačítko **Unreal Engine** kartu a potom klikněte na "hamburgerové" nabídky vedle **posledních projektů Unreal Engine** a vyberte **vyhledat projektu**. Otevřete projekt Unreal svoji hru `.uproject` souboru.

    ![Snímek obrazovky z Wwise spouštěcí program pro Unreal kartu](media/wwise-unreal-tab.png)

* Pak klikněte na tlačítko **integrovat Wwise v projektu** nebo **upravit Wwise v projektu**. Tento krok (re) integruje do vašeho projektu, nyní včetně modulu plug-in mixer Akustika projektu Wwise binární soubory.

* **Modul plug-in:** Pokud používáte Wwise jako modul plug-in a vytvoření fiktivní projektu jak je uvedeno výše, zkopírujte složku Wwise nasazení: `[DummyUProject]\Plugins\Wwise` a vložte ji místo `[UESource]\Engine\Plugins\Wwise`. `[DummyUProject]` je prázdnou cestu projektu Unreal C++ a `[UESource]` je, kde máte nainstalovat Unreal Engine zdroje. Po dokončení kopírování, mohou odstranit projekt, fiktivní.

## <a name="3-add-the-project-acoustics-unreal-plugin-to-your-game"></a>3. Přidání projektu Akustika Unreal modulu plug-in pro hry
 
* Kopírování `Unreal\ProjectAcoustics` složky v modulu plug-in balíček a vytvořte novou složku `[UProjectDir]\Plugins\ProjectAcoustics`, kde `UProjectDir` je obsahující složku projektu svoji hru `.uproject` souboru.
  * **Modul plug-in**: Pokud používáte Wwise jako modul plug-in, měli byste použít Akustika projektu jako Unreal engine modul plug-in také. Místo výše uvedené cílový adresář, použijte: `[UESource]\Engine\Plugins\ProjectAcoustics`.

* Ověřte `Wwise` složce společně s `ProjectAcoustics` složky. Obsahuje modul plug-in Wwise spolu s binární soubory pro modul plug-in mixer, kterou jste zpětný nasadili v kroku 2 výše.

## <a name="4-extend-wwises-unreal-plugin-functionality"></a>4. Rozšíření funkcí na Wwise Unreal modulu plug-in
* Projekt Akustika Unreal plugin vyžaduje další chování být zveřejněna před modul plug-in Wwise Unreal rozhraní API za [tyto pokyny](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). Přidali jsme k automatizaci oprav procedury dávkového souboru. 
* Uvnitř `Plugins\ProjectAcoustics\Resources`spuštěním `PatchWwise.bat`. Na obrázku níže využívá naše AcousticsGame ukázkového projektu.

    ![Zvýraznění okno snímek obrazovky Průzkumníka Windows k dispozici skript pro opravu Wwise](media/patch-wwise-script.png)

* Pokud nemáte nainstalované rozhraní DirectX SDK, budete muset zakomentovali řádek obsahující DXSDK_DIR v `[UProject]\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![Snímek obrazovky zobrazující DXSDK zakomentované editoru kódu](media/directx-sdk-comment.png)

## <a name="5-build-game-and-check-python-is-enabled"></a>5. Vytvořte hru a zkontrolujte, že je povolená Pythonu

* Kompilace svoji hru a ujistěte se, že je správně sestavena. V opačném případě zkontrolujte pečlivě před pokračováním v předchozích krocích. 
* Otevřete svůj projekt v editoru Unreal. 
* **Modul plug-in:** Pokud používáte ProjectAcoustics jako modul plug-in, také zajistěte, že je povoleno, uvedený v části "integrované" moduly plug-in.
* Měli byste vidět nový režim, což znamená, že byla integrována Akustika projektu.

    ![Snímek obrazovky sady Unreal zobrazující Akustika režimu úplné](media/acoustics-mode-full.png)

* Ověřte, že máte modul plug-in pro Python pro Unreal povolena. Toto je nezbytné pro integraci editor správně fungovat.

    ![Snímek obrazovky s povolením rozšíření Pythonu v Unreal editoru](media/ensure-python.png)

## <a name="6-wwise-project-setup"></a>6. Nastavení projektu Wwise

Příklad Wwise projektu je součástí ukázky ke stažení. Doporučujeme prohlédnout spolu tyto pokyny. Snímky obrazovky níže pocházejí z tohoto projektu.

### <a name="bus-setup"></a>Instalace Service Bus
* Projekt Akustika Unreal modul plug-in bude hledat mixer přidružený modul plug-in na Service bus s tímto ***přesné*** název: `Project Acoustics Bus`. Vytvořte nový zvukový Service bus s tímto názvem. Modul plug-in mixer můžete pracovat v různých konfiguracích, ale prozatím předpokládáme, že se použije k pouze dozvuku zpracování. Smíšené dozvuku signálu pro všechny zdroje, které používají Akustika budou mít tento Service bus. Můžete kombinovat upstream do jakékoli Service bus kombinování strukturu, příklad je uveden níže, z našich Wwise ukázkový projekt součástí vzorku ke stažení.

    ![Sběrnice Wwise snímek obrazovky zobrazující Akustika projektu Service Bus](media/acoustics-bus.png)

* Konfigurace kanálu na sběrnici se musí nastavit na jednu z: `1.0, 2.0, 4.0, 5.1 or 7.1`. Další konfigurace způsobí žádný výstup této sběrnice.

    ![Snímek obrazovky s možností konfigurace kanálu Akustika projektu Service Bus](media/acoustics-bus-channel-config.png)

* Teď přejděte do projektu Akustika sběrnice podrobnosti a ujistěte se, že se zobrazí na kartě Mixer modulu Plug-in

    ![Snímek obrazovky Wwise ukazující, jak povolit kartu Mixer modulu Plug-In pro Service Bus Akustika projektu](media/mixer-tab-enable.png)

* Potom přejděte na kartu Mixer modulu Plug-in a přidat mixer Akustika projektu modulu plug-in k odběru sběrnice

    ![Service bus Screenshow Wwise ukazující, jak přidat modul plug-in Mixer Akustika projektu](media/add-mixer-plugin.png)

### <a name="actor-mixer-hierarchy-setup"></a>Nastavení hierarchie mixer objektu actor
* Z důvodů výkonu projektu Akustika platí DSP zvuku ke všem zdrojům současně. To vyžaduje modul plug-in pro provoz jako modul plug-in mixer. Wwise vyžaduje mixer moduly plug-in na sběrnici výstup, i když Service bus výstup obvykle nese signál suchého výstup. Projekt Akustika vyžaduje suchého signál ho směrovat pomocí sběrnice aux, zatímco vlhkou signál, který je prováděn `Project Acoustics Bus`. Následující proces podporuje postupné migrace na tento tok signál.

* Dejme tomu, že máte existující projekt obsahující stopách zbraní a ostatní na nejvyšší úrovni hierarchie mixer objektu actor. Každý má odpovídající výstupní Service bus pro jeho suchého kombinace. Dejme tomu, že chcete provést migraci stopách používat Akustika umožňuje. Nejprve vytvořte odpovídající Service bus pomocná provádět jejich suchého submix, který je podřízeným prvkem Service bus stopách výstup. Například jsme používali předponu "Suchého" v následujícím obrázku uspořádat, ačkoli přesným názvem není důležité. Všechny měřiče nebo efekty, které jste měli na sběrnici stopách bude i nadále fungovat jako předtím.

    ![Snímek obrazovky doporučená Wwise suchého kombinace nastavení](media/wwise-dry-mix-setup.png)

* Změňte výstupní struktury sběrnice pro objekt actor – mixer stopách následujícím způsobem, s Service Bus Akustika projektu nastavit jako výstupní Service Bus a Dry_Footsteps nastavit jako uživatelem definované aux sběrnice.

    ![Snímek obrazovky doporučená nastavení sběrnice Mixer Wwise objektu Actor](media/actor-mixer-bus-settings.png)

* Nyní všechny stopách získat Akustika zpracování a výstupní jejich dozvuku na sběrnici Akustika projektu. Suchého signálu je směrován přes Dry_Footsteps a spatialized jako obvykle.

* Projekt Akustika platí jenom pro zvuky, které mají 3D umístění na světě. Následující [Wwise dokumentaci](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/), vlastnosti umístění musíte nastavit, jak je znázorněno. Nastavení "zvuk 3D" může být buď "Umístění" nebo "Pozice + orientace" podle potřeby.

    ![Snímek obrazovky doporučená Wwise objektu Actor umístění nastavení](media/wwise-positioning.png)

* Nastavení Service Bus výstup do jiné sběrnice, který kombinuje upstream do **Akustika projektu Service Bus** nebude fungovat. Wwise ukládá tento požadavek na moduly plug-in mixer.

* Pokud chcete v hierarchii objektu actor mixer stopách nechcete použít Akustika podřízený, vždycky vám pomůže "přepisuje nadřazené" v něm se odhlásit.

* Pokud používáte odešle hru nebo uživatelem definované pro dozvuku na libovolný objekt actor mixer ve hře, vypněte ty na tento objekt actor mixer k Vyhněte se použití dozvuku dvakrát.

### <a name="spatialization"></a>Prostorový zvuk
Ve výchozím nastavení použije modul plug-in mixer projektu Akustika Wwise konvoluce dozvuku, byste museli opustit Wwise provedete posouvání zvuk. Při použití projektu Akustika v této konfiguraci jen dozvuku výchozí, jste použít libovolnou metodu konfigurace a zvuk kanál na kombinaci vaší suchého umožňuje kombinovat a párovat téměř jakékoli spatializer s dozvuku Akustika projektu. Vaše možnosti jsou [na základě Ambisonics binaural spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/) a [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
Projekt Akustika zahrnuje volitelné spatializer, který podporuje jak založenou na objektech ve vysokém rozlišení HRTF vykreslování a posouvání. Zaškrtněte políčko "Provádět zvuk" na stránce nastavení modulu plug-in mixer a vybrat mezi HRTF nebo pro posouvání a zakázat nastavení výše suchého sběrnice, aby spatializing dvakrát, i pomocí modulu plug-in mixer Akustika projektu a Wwise odešle aux definovaný uživatelem. Prostorový zvuk režimu nelze změnit v reálném čase, protože vyžaduje opětovné vygenerování zvukové bank. Je nutné restartovat Unreal a potom znovu vygenerovat soundbanks před tím play, aby přebíral změny konfigurace modulu plug-in mixer například zaškrtávací políčko "provádět zvuk.

![Nastavení snímku obrazovky z Wwise Mixer modulu plug-in zvuk](media/mixer-spatial-settings.png)

Bohužel další moduly plug-in založenou na objektech spatializer nemůže být v tuto chvíli nepodporuje a jsou implementovány jako moduly plug-in mixer Wwise aktuálně neumožňuje více modulů plug-in mixer přiřazené do jednoho objektu actor mixer.  

## <a name="7-audio-setup-in-unreal"></a>7. Zvukový instalační program v Unreal
* Nejprve budete muset vytvoření vašich her úrovních, abyste získali Akustika asset, který se umístí do `Content\Acoustics`. Poraďte [Unreal kurz vytvoření](unreal-baking.md) a pokračovat zde. Některé předem dokončené úrovně jsou součástí ukázkového balíčku.
* Vytvořte prvek "actor" Akustika místa ve scéně. Vytvořte pouze jeden z těchto objektů actor v úrovni protože představuje Akustika celý úrovně. 

    ![Snímek obrazovky Unreal editor znázorňující vytvoření Akustika místo objektu actor](media/create-acoustics-space.png)

* Dokončené akustický datovému assetu nyní přiřadíte slotu Akustika Data objektu actor Akustika místa. Vaše scéna má teď Akustika!

    ![Snímek obrazovky Unreal editoru s howing Akustika Asset přiřazení](media/acoustics-asset-assign.png)

* Nyní přidejte prvek "actor" prázdný a postupujte takto:

    ![Snímek obrazovky Unreal editor zobrazující využití Akustika komponenty v prázdný objekt actor](media/acoustics-component-usage.png)

1. Přidejte komponentu Akustika zvuk na objekt actor. Tato součást rozšiřuje komponentu Wwise zvuku s funkcemi pro Akustika projektu.
2. Ve výchozím nastavení, která se aktivuje přidružené Wwise události na úrovni spuštění je zaškrtnuté políčko Play na začátku pole.
3. Pomocí zaškrtávacího políčka Zobrazit Akustika parametry pro tisk na obrazovce informace o zdroji pro ladění.
    ![Snímek obrazovky Unreal panely editor Akustika zdrojem zvuku s hodnotami ladění povoleno](media/debug-values.png)
4. Přiřadit události Wwise na obvyklý pracovní postup Wwise
5. Ujistěte se, že použití prostorový zvuk je vypnutý. V tuto chvíli používáte Akustika projektu pro konkrétní komponentu zvuku, nelze použít současně Wwise na prostorový zvuk modul pro Akustika.

Nyní je vše připraveno. Pohyb scény a prozkoumejte zvukové efekty!

## <a name="next-steps"></a>Další postup
* [Návrh](unreal-workflow.md) kurzu Akustika projektu v Unreal/Wwise
* [Zjistěte, jak se vytváří](unreal-baking.md) pro herní scény 
