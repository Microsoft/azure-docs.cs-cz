---
title: 'Kurz: instalace fyzického zařízení Azure FXT Edge souborového'
description: Postup rozbalení, stojanu a zapojení součásti fyzického zařízení Microsoft Azureového úložiště FXT Edge souborového do hybridní mezipaměti
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 48ee6de28bcd76d4c484b77c981062bad1a3754d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219747"
---
# <a name="tutorial-install-azure-fxt-edge-filer"></a>Kurz: instalace Azure FXT Edge souborového

V tomto kurzu se dozvíte, jak nainstalovat uzel hardwaru pro mezipaměť hybridního úložiště Azure FXT Edge souborového. Abyste mohli vytvořit cluster Azure FXT Edge souborového, musíte nainstalovat aspoň tři hardwarové uzly.

Postup instalace zahrnuje rozbalení a rack pro připojení zařízení a připojení ARM pro správu kabelů (CMA) a předního čelního panelu. V samostatném kurzu se dozvíte, jak připojit síťové kabely a připojit se k napájení.

Instalace uzlu Azure FXT Edge souborového trvá přibližně jednu hodinu.

Tento kurz zahrnuje tyto kroky nastavení:

> [!div class="checklist"]
>
> * Rozbalení zařízení
> * Připojení zařízení v racku
> * Nainstalovat přední kryt (volitelné)

## <a name="installation-prerequisites"></a>Instalační požadavky

Než začnete, ujistěte se, že datové centrum a rack budete používat tyto funkce:

* Dostupná patice 1U na stojanu, kam máte v úmyslu zařízení připojit.
* Elektrické napájení a chladicí systémy, které vyhovují potřebám Azure FXT Edge souborového. (Přečtěte si informace o [Power and Thermal](fxt-specs.md#power-and-thermal-specifications) pro plánování a nastavení velikosti instalace.)  

  > [!NOTE]
  > Chcete-li plně využít výhod těchto dvou redundantních jednotek napájení (PSUs), použijte při připojování elektrického napájení jednotky distribuce napájení ve dvou různých okruhech větví. Podrobnosti najdete v tématu [připojení napájecích kabelů](fxt-network-power.md#connect-power-cables) .  

## <a name="unpack-the-hardware-node"></a>Rozbalení uzlu hardwaru

Každý uzel Azure FXT Edge souborového je dodán v jednom poli. Provedením těchto kroků rozbalíte zařízení.

1. Umístěte krabici na rovný povrch.

2. Zkontrolujte krabici a výplňový materiál, jestli nejsou pomačkané, pořezané nebo poškozené vodou nebo jestli nenesou stopy jiného zjevného poškození. Pokud je box nebo balení vážně poškozené, neotevírejte ho. Obraťte se na podporu Microsoftu, která vám pomůže určit, jestli je zařízení v dobrém funkčním stavu.

3. Rozbalte krabici. Ujistěte se, že obsahuje následující položky:
   * Jedno zařízení FXT se samostatnou skříňkou
   * Dva napájecí kabely
   * Jeden přední kryt a klíč
   * Jedno sestavení pro železniční sadu
   * Jeden ARM pro správu kabelů (CMA)
   * Brožura Pokyny k instalaci CMA
   * Brožura Pokyny k instalaci stojanu
   * Bezpečnostní brožura, informace o prostředí a regulativní informace

Pokud jste neobdrželi všechny uvedené položky, obraťte se na dodavatele zařízení a požádejte ho o podporu.

Ujistěte se, že má zařízení dostatek času na dosažení stejné teploty, než ji nainstalujete nebo zapnete. Pokud si všimnete kondenzace v jakékoli části zařízení, před instalací nástroje počkejte aspoň 24 hodin.

Dalším krokem je připojení zařízení k stojanu.

## <a name="rack-the-device"></a>Usazení zařízení do racku

Zařízení souborového pro Azure FXT Edge se musí nainstalovat na standardní Rack s 19 mm.

Mezipaměť hybridního úložiště Azure FXT Edge souborového se skládá ze tří nebo více zařízení Azure FXT Edge souborového. Opakujte kroky pro instalaci stojanu pro každé zařízení, které je součástí systému.

### <a name="rack-install-prerequisites"></a>Požadavky na instalaci stojanu

* Než začnete, přečtěte si pokyny pro bezpečnost v brožuře zabezpečení, environmentální a regulativní informace, které byly dodány s vaším zařízením.

  > [!NOTE]
  > Při zdvihání uzlu vždy používejte dvě osoby, včetně toho, kdy ji nainstalujete do stojanu, nebo odebrat z racku.

* Identifikujte typ instalace železničního prostředí, který se používá v racku vašeho zařízení.
  * Pro čtvercové nebo kulaté otvory modulu snap-in se řiďte pokyny pro železnici bez nástrojů.
  * V případě vláken s vlákny pro vlákna se řiďte pokyny pro nástroj.
  
    Pro konfiguraci nástroje pro připojení k nástroji je nutné zadat osm šrouby, typ 10-32, 12-24, M5 nebo M6. Průměr horních šroubů musí být menší než 10 mm (0,4).

### <a name="identify-the-rail-kit-contents"></a>Identifikace obsahu v sadě kolejnice

Vyhledejte komponenty pro instalaci sestavení se sadou pro železnici:

1. Dvě A7 Dell ReadyRails II – klouzavé železniční sestavení (1)
1. Dva popruhy zavěšení a smyček (2)

![Očíslovaný nákres obsahu železniční sady](media/fxt-install/identify-rail-kit-contents-400.png)

### <a name="rail-assembly---tool-less-rails-square-hole-or-round-hole-racks"></a>Železniční sestavení – bez nástrojů kolejnice (racky na druhou nebo kulatou díru)

Pro racky s přidanými čtverci nebo kulatými otvory pomocí tohoto postupu Sestavte a nainstalujte kolejnice.

1. Umístěte vlevo a vpravo zakončení železnic s označením **vpřed** směrem k dál. Každou koncovou položku umístěte do otvorů na přední straně svislé příruby. první

2. Zarovnejte jednotlivé konce v dolní části a horních děr racku v prostoru, který chcete připojit. (2)

3. Zapojte se do konce kolejnice až do úplného umístění na vertikálním přírubě regálu a západky se klikne na místo. Zopakováním těchto kroků Umístěte přední konec části na vertikální přírubu. 1

> [!TIP]
> Pokud chcete tyto kolejnice odebrat, Získejte tlačítko pro vydání zámků na konci středníku (4) a odinstalujte jednotlivé kolejnice.

![Diagram instalace a odebrání kolejnicí bez nástrojů, se číslovaným postupem](media/fxt-install/installing-removing-tool-less-rails-400.png)

### <a name="rail-assembly---tooled-rails-threaded-hole-racks"></a>Železniční montážní kolejnice (racky s vlákny)

U racků s otvory s více vlákny Sestavte a nainstalujte kolejnice pomocí tohoto postupu.

1. Odeberte PIN kódy z front-a zadních závorek pomocí Screwdriver s nepřímým připojením. první
1. Umožňuje načíst a otočit podsestavení západky kolejnice a odebrat je z montážní hranaté závorky. (2)
1. Připojte levé a pravé montážní kolejnice k přednímu vertikálnímu přírubě na racku pomocí dvou párů šroubů. 1
1. Posunutí vlevo a vpravo od zadních závorek směrem k zadním svislým přírubám a jejich připojení pomocí dvou párů šroubů. 4

![Diagram instalace a odebrání prodaných kolejnic, se číslovaným postupem](media/fxt-install/installing-removing-tooled-rails-400.png)

### <a name="install-the-system-in-the-rack"></a>Instalace systému do stojanu

Pomocí těchto kroků připojte zařízení Azure FXT Edge souborového v racku.

1. Vydejte vnitřní snímek ze stojanu, dokud se nezamkne. první
1. Vyhledejte zadní kolejnici Standoff na všech stranách zařízení a snižte jejich množství na zadních \ slotů v sestaveních snímků. (2)
1. Otáčejte zařízení směrem dolů, dokud se všechny standoffs kolejnice neusadí do přihrádek. 1
1. Zařízení nahrajte do výše, dokud se neklikne na místo na zámek.
1. Stiskněte tlačítka zámku pro vydanou verzi na obou kolejnicích (4) a prosuňte zařízení do stojanu.

![Instalace systému v rackovém diagramu se číslovaným postupem](media/fxt-install/installing-system-rack-400.png)

### <a name="remove-the-system-from-the-rack"></a>Odebrat systém ze stojanu

Pokud chcete zařízení odebrat ze stojanu, postupujte podle tohoto postupu.

1. Vyhledá páčky zámku na stranách vnitřních kolejnic (1).
2. Odemkněte jednotlivé páčky tím, že je překročíte k umístění vydané verze (2).
3. Potáhne strany systému pevně a předají ho do té doby, než se standoffs kolejnice na přední straně J slotů. Zvedněte systém směrem od stojanu a umístěte ho na plochu úrovně (3).

![Obrázek odebrání systému z racku s postupem číslovaným](media/fxt-install/removing-system-rack-400.png)

### <a name="engage-the-slam-latch"></a>Zapojit západku Slam

1. Lícem dozadu, vyhledejte západku Slam (1) na obou stranách systému.
2. Zámky se při vložení systému do stojanu automaticky zapojí.

K uvolnění zámků při odebírání systému je potřeba je načíst (2).

K zabezpečení systému do racku pro expedici nebo v jiných nestabilních prostředích jsou k dispozici volitelné šrouby pevného připojení. Najděte šroub pod každým západkou a sečtěte je pomocí #2 Phillips Screwdriver (3).

![Očíslovaná ilustrace poutavé a uvolňování západky Slam](media/fxt-install/engaging-releasing-slam-latch-400.png)

### <a name="install-the-cable-management-arm"></a>Instalace ARM pro správu kabelů

K dispozici je volitelná technologie ARM pro správu kabelů (CMA) s FXT Edge souborového. Tištěné pokyny k její instalaci najdete v balíčku.

1. Rozbalení a identifikace součástí sady pro správu kabelů ARM:
   * CMAový zásobník (1)
   * CMA (2)
   * Zábalení nylonových kabelů (3)
   * CMA závorky přílohy (4)
   * Kabel ukazatele stavu (5)

   > [!TIP]
   > Pokud chcete zabezpečit CMA pro expedici v racku, Projděte si propojení na košíkech i na panelu a cinch je pevně. Zabezpečení CMA tímto způsobem také zajistí zabezpečení vašeho systému v nestabilním prostředí.

   ![Ilustrace CMA částí](media/fxt-install/cma-kit-400.png)

2. Nainstalujte si CMAový panel.

   Zásobník CMA poskytuje podporu a slouží jako zachovávání pro CMA.

   1. Zarovnejte a propojte každou stranu zásobníku a uveďte přijímače na vnitřních okrajích kolejnic.
   1. Nahrajte do zásobníku před tím, než se klikne na místo. první
   1. Chcete-li odebrat zásobník, přetáhněte tlačítka pro uvolnění zámků do středu a vyžádejte si zásobník z přijímače (2).

   ![Obrázek instalace na panelu CMA](media/fxt-install/cma-tray-install-400.png)

3. Nainstalujte závorky příloh CMA.

   > [!NOTE]
   >
   > * CMA můžete připojit buď k pravé nebo levé montážní kolejnici, v závislosti na tom, jak budete chtít směrovat kabely ze systému.
   > * Pro usnadnění připojte CMA na straně opačným směrem k napájení (strana A). Pokud je připojená k straně B, musí být CMA odpojený, aby bylo možné odebrat vnější zdroj napájení.
   > * Před odebráním napájení vždy odeberte tento zásobník.

   ![Obrázek instalace CMA závorky](media/fxt-install/cma-bracket-l-r-install-400.png)

   1. Vyberte příslušnou hranatou závorku CMA pro stranu, do které chcete CMA připojit (stranu B nebo stranu A).
   1. Nainstalujte hranatou závorku CMA s odpovídající stranou A nebo stranou B na zadní straně snímku.
   1. V závorkách zarovnejte otvory s kolíky na snímku. Vložte hranatou závorku dolů, dokud se nezamkne.

4. Nainstalujte CMA.

   1. Na zadní straně systému připadá západku na front-end CMA v nejvnitřnější závorce sestavení snímku, dokud se západka (1) nezabývá.
   1. Na konec vnější hranaté závorky připadá na další západku, dokud západka nezačne (2).
   1. Pokud chcete CMA odebrat, odpojte obě tlačítka verze CMA v horní části vnitřních a vnějších zámků (3).

   ![Ilustrace hlavní instalace CMA](media/fxt-install/cma-install-400.png)

   CMA se dá otočit od systému po přístup a službu. Na zavěšeném konci zvedněte CMA od zásobníku, abyste ho mohli zrušit jeho umístění (1). Po rozmístění z tohoto zásobníku si CMA od systému (2).

   ![Ilustrace CMA otočeného otevření pro službu](media/fxt-install/cma-swing-over-tray-400.png)

## <a name="install-the-front-bezel-optional"></a>Nainstalovat přední kryt (volitelné)

V této části se dozvíte, jak nainstalovat a odebrat přední kryt (faceplate) pro hardware souborového Azure FXT Edge.

Instalace nástroje pro čelního čelního panelu:

1. Vyhledejte a odeberte klíč, který je součástí balíčku pro čelního panelu.
1. Zarovnejte rám s přední částí skříně a vložte PIN kódy na pravé straně krytky do otvorů na pravé straně přípojného skříňky uzlu.
1. Připadá na levý konec rámu na skříň. Stiskněte kryt, dokud na něj nekliknete na tlačítko na levé straně.
1. Uzamkněte kryt s klíčem.

Postup odebrání čelního krytu:

1. Odemkněte kryt pomocí klíče pro čelního panelu.
1. Na levé straně stiskněte tlačítko pro vydání a získejte levý konec krytu mimo skříň.
1. Odpojte pravé zakončení a odeberte čelního panelu.

   ![Obrázek znázorňující tlačítko pro vydání na levé straně čelního panelu a postup, jak ho odebrat z levé strany](media/fxt-install/remove-bezel-edited-600.png)

## <a name="next-steps"></a>Další kroky

Po rozbalení a zabalení zařízení pokračujte v instalaci připojením síťových kabelů a připojením elektrického napájení k Azure FXT Edge souborového.

> [!div class="nextstepaction"]
> [Zapojení síťových portů a napájení z elektrické sítě](fxt-network-power.md)
