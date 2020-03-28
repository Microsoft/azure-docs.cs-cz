---
title: 'Kurz: Instalace fyzického zařízení Azure FXT Edge Filer'
description: Jak rozbalit, rackovat a vytvořit kabel součásti fyzického zařízení mezipaměť hybridního úložiště Microsoft Azure FXT Edge Filer
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 7c77e25d0dcabc49f2e6672645c6bc41e8662ec8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75551025"
---
# <a name="tutorial-install-azure-fxt-edge-filer"></a>Kurz: Instalace azure FXT edge fileru 

Tento kurz popisuje, jak nainstalovat hardwarový uzel pro mezipaměť hybridního úložiště Azure FXT Edge Filer. Chcete-li vytvořit cluster Azure FXT Edge Filer, musíte nainstalovat alespoň tři hardwarové uzly.

Postup instalace zahrnuje vybalení a montáž zařízení do racku a připevnění ramene pro vedení kabelu (CMA) a předního rámečku. Samostatný kurz vysvětluje připojení síťových kabelů a připojení napájení. 

Instalace uzlu Azure FXT Edge Filer trvá přibližně jednu hodinu. 

Tento kurz zahrnuje tyto kroky nastavení: 

> [!div class="checklist"]
> * Rozbalení zařízení
> * Namontujte zařízení do stojanu
> * Instalace předního rámečku (volitelně)

## <a name="installation-prerequisites"></a>Instalační požadavky 

Než začnete, ujistěte se, že datové centrum a rack, které budete používat, mají tyto funkce:

* K dispozici 1U slot na stojanu, kde máte v úmyslu připojit zařízení.
* Systémy střídavého napájení a chlazení, které splňují potřeby zařízení Azure FXT Edge Filer. (Přečtěte si informace o [výkonu a tepelném prostředí,](fxt-specs.md#power-and-thermal-specifications) které vám pomohou s plánováním a dimenzováním instalace.)  

  > [!NOTE] 
  > Chcete-li plně využít výhod dvou redundantních napájecích jednotek (PSU), použijte při připojování střídavého proudu napájecí jednotky na dvou různých oborových obvodech. Podrobnosti například [Připojit napájecí kabely.](fxt-network-power.md#connect-power-cables)  

## <a name="unpack-the-hardware-node"></a>Rozbalení hardwarového uzlu 

Každý uzel Azure FXT Edge Filer se dodává v jednom poli. Chcete-li zařízení rozbalit, proveďte tyto kroky.

1. Umístěte krabici na rovný povrch.

2. Zkontrolujte krabici a výplňový materiál, jestli nejsou pomačkané, pořezané nebo poškozené vodou nebo jestli nenesou stopy jiného zjevného poškození. Pokud je krabice nebo obal vážně poškozen, neotvírejte ji. Obraťte se na podporu Microsoftu, která vám pomůže určit, jestli je zařízení v dobrém funkčním stavu.

3. Rozbalte krabici. Ujistěte se, že obsahuje následující položky:
   * Jedno jedno skříňové FXT zařízení
   * Dva napájecí kabely
   * Jeden přední rámeček a klíč
   * Sestava jedné sady kolejnic
   * Jedno rameno pro vedení kabelů (CMA)
   * Brožura s pokyny k instalaci CMA
   * Brožura s pokyny k instalaci do racku
   * Brožura o bezpečnosti, životním prostředí a regulačních informacích

Pokud jste neobdrželi všechny uvedené položky, požádejte o podporu dodavatele zařízení. 

Ujistěte se, že zařízení má dostatek času na dosažení stejné teploty jako místnost před instalací nebo zapnutím. Pokud si všimnete kondenzace na jakékoli části zařízení, počkejte alespoň 24 hodin před instalací.

Dalším krokem je montáž zařízení do racku.

## <a name="rack-the-device"></a>Usazení zařízení do racku

Zařízení Azure FXT Edge Filer musí být nainstalované ve standardním 19palcovém racku. 

Mezipaměť hybridního úložiště Azure FXT Edge Filer se skládá ze tří nebo více zařízení Azure FXT Edge Filer. Opakujte kroky instalace racku pro každé zařízení, které je součástí vašeho systému. 

### <a name="rack-install-prerequisites"></a>Předpoklady pro instalaci do racku

* Než začnete, přečtěte si bezpečnostní pokyny v brožované příručce Pro bezpečnost, životní prostředí a regulační informace, která byla dodána s vaším zařízením.

  > [!NOTE]
  > Při zvedání uzlu vždy používejte dvě osoby, včetně instalace do stojanu nebo jeho vyjmutí ze stojanu. 

* Určete typ železniční instalace používané s rackem zařízení. 
  * U čtvercových nebo kulatých stojanů pro uchopení díry postupujte podle pokynů lišty bez nářadí.
  * U závitových stojanů s otvory postupujte podle pokynů pro osazení. 
  
    Pro konfiguraci montáže obrožné kolejnice je třeba dodat osm šroubů typu 10-32, 12-24, M5 nebo M6. Průměr hlavy šroubů musí být menší než 10 mm (0,4").

### <a name="identify-the-rail-kit-contents"></a>Identifikujte obsah železniční sady

Vyhledejte součásti pro instalaci sestavy železniční sady:

1. Dvě posuvné kolejnice A7 Dell ReadyRails II (1)
1. Dva popruhy na háček a smyčku (2)

![Číslovaný výkres obsahu soupravy kolejnice](media/fxt-install/identify-rail-kit-contents-400.png)

### <a name="rail-assembly---tool-less-rails-square-hole-or-round-hole-racks"></a>Montáž kolejnic - lisovací lišty bez nářadí (čtvercové otvory nebo kulaté otvory)

U regálů se čtvercovými nebo kulatými otvory se přichycení mařite a instalaci kolejnic postupujte podle tohoto postupu. 

1. Umístěte koncové a pravé části kolejnice označené **směrem dopředu** směrem dovnitř. Umístěte každý koncový díl tak, aby se nasazuje do otvorů na přední straně svislých přírub racku. (1)

2. Zarovnejte každý koncový kus ve spodním a horním otvoru stojanu v prostoru, který chcete namontovat. (2)

3. Zapněte zadní konec kolejnice tak, aby se plně nenasadila na svislou přírubu stojanu a západka nezapadla na místo. Opakujte tyto kroky tak, aby byl přední konec umístěte a usadit na svislou přírubu stojanu. (3)

> [!TIP]
> Chcete-li odstranit kolejnice, vytáhněte tlačítko pro uvolnění západky na středovém bodě koncového dílu (4) a sesaďte každou kolejnici.

![Diagram instalace a demontáže kolejnic bez nástrojů s kroky očíslovanými](media/fxt-install/installing-removing-tool-less-rails-400.png)

### <a name="rail-assembly---tooled-rails-threaded-hole-racks"></a>Montáž kolejnic - obrobené kolejnice (závitové otvory)

U regálů se závitovými otvory postupujte podle tohoto postupu pro montáž a instalaci kolejnic.

1. Odstraňte kolíky z předních a zadních montážních držáků plochým šroubovákem. (1)
1. Zatáhněte a otočte podsestavami západky kolejnice, abyste je odstranili z montážních držáků. (2)
1. Připojte levé a pravé montážní lišty k předním svislým přírubám racku pomocí dvou párů šroubů. (3)
1. Posuňte levou a pravou zadní konzolu dopředu proti zadním svislým přírubám racku a připevněte je pomocí dvou párů šroubů. (4)

![Diagram instalace a demontáže obrožlicích kolejnic s kroky očíslovanými](media/fxt-install/installing-removing-tooled-rails-400.png)

### <a name="install-the-system-in-the-rack"></a>Nainstalujte systém do stojanu

Podle těchto kroků připojte zařízení Azure FXT Edge Filer do racku.

1. Vytáhněte vnitřní posuvné lišty ze stojanu, dokud se nezablokují na místo. (1)
1. Umístěte patovou situaci zadní kolejnice na obou stranách zařízení a spusťte je do zadních omeků J na skluzavce. (2) 
1. Otáčejte zařízením směrem dolů, dokud nebudou všechny patové situace na kolejnici usazeny v drážkách J. (3)
1. Zatlačte zařízení dovnitř, dokud zacvaknou páčky zámku na místo.
1. Stiskněte tlačítka zámku pro uvolnění posuvu na obou lištách (4) a zasuňte zařízení do stojanu.

![Instalace systému do diagramu racku s kroky očíslovanými](media/fxt-install/installing-system-rack-400.png)

### <a name="remove-the-system-from-the-rack"></a>Vyjměte systém ze stojanu

Chcete-li zařízení vyjmout ze stojanu, postupujte podle tohoto postupu. 

1. Umístěte zajišťovací páčky po stranách vnitřních kolejnic (1).
2. Odemkněte každou páčku otočením do polohy uvolnění (2).
3. Pevně uchopte strany systému a zatahěte jej dopředu, dokud nebudou patové situace kolejnice v přední části otečků J. Zvedněte systém nahoru a pryč od stojanu a umístěte jej na rovný povrch (3).

![Ilustrace odstranění systému ze stojanu s kroky očíslovanými](media/fxt-install/removing-system-rack-400.png)

### <a name="engage-the-slam-latch"></a>Zapněte slam západku

1. Směrem dopředu vyhledejte západku slamu (1) na obou stranách systému.
2. Západky se automaticky zapínají, když je systém zatlačen do stojanu. 

Chcete-li při vyjímací části systému uvolnit západky, vytáhněte je nahoru (2).

Volitelné šrouby s tvrdou montáží jsou k dispozici pro zajištění systému do racku pro přepravu nebo v jiných nestabilních prostředích. Pod každou západkou najděte šroub a utáhněte je #2 křížovým šroubovákem (3).

![Očíslovaná ilustrace zapojení a uvolnění západky slam](media/fxt-install/engaging-releasing-slam-latch-400.png)

### <a name="install-the-cable-management-arm"></a>Instalace ramene pro správu kabelů 

S filerem FXT Edge Filer je k dispozici volitelné rameno pro správu kabelů (CMA). Tištěné pokyny pro jeho instalaci jsou uvedeny v balení. 

1. Rozbalte a identifikujte součásti sady ramen pro vedení kabelů:
   * Zásobník CMA (1)
   * CmA (2)
   * Nylonové kabelové kravatové obaly (3)
   * CmA upevňovací konzoly (4)
   * Kabel indikátoru stavu (5) 

   > [!TIP] 
   > Chcete-li zajistit CMA pro přepravu v racku, smyčka kravata omotákolem obou košů a zásobníku a cinch je pevně. Zajištění CMA tímto způsobem také zajistí váš systém v nestabilním prostředí.

   ![Ilustrace cma dílů](media/fxt-install/cma-kit-400.png)

2. Nainstalujte zásobník CMA.

   Zásobník CMA poskytuje podporu a působí jako držák pro CMA. 

   1. Zarovnejte a zapojte obě strany zásobníku pomocí držáků přijímače na vnitřních okrajích kolejnic. 
   1. Zatlačte přihrádku dopředu, dokud nezacvakne na místo. (1)
   1. Chcete-li zásobník vyjmout, stlačte tlačítka pro uvolnění západky směrem ke středu a vytáhněte přihrádku z držáků přijímače (2).

   ![Ilustrace instalace zásobníku CMA](media/fxt-install/cma-tray-install-400.png)

3. Nainstalujte držáky přílohcma. 

   > [!NOTE]
   >
   > * CmA můžete připojit k pravé nebo levé montážní kolejnici v závislosti na tom, jak chcete vést kabely ze systému. 
   > * Pro větší pohodlí namontujte CMA na stranu naproti napájecím zdrojům (strana A). Je-li namontován na straně B, cma musí být odpojen, aby se odstranil vnější napájecí zdroj. 
   > * Před vyjmutím napájecích zdrojů vždy vyjměte zásobník. 

   ![Ilustrace instalace držáku CMA](media/fxt-install/cma-bracket-l-r-install-400.png)

   1. Vyberte příslušný držák cma pro stranu, na kterou chcete cma (strana B nebo strana A).
   1. Na zadní stranu kluzné lišty namontujte upevňovací držák CMA s odpovídajícím bočním označením A nebo bočním označením B.
   1. Zarovnejte otvory na držáku s kolíky na kluzné kolejnici. Zatlačte držák dolů, dokud nezapadne na své místo. 

4. Nainstalujte CMA.

   1. Na zadní straně systému nasaďte západku na předním konci CMA na nejvnitřnější držák sklíčka, dokud západka nezapadne (1). 
   1. Druhou západku namontujte na konec vnějšího držáku, dokud západka nezapadne (2). 
   1. Chcete-li cma vyjmout, odpojte obě západky stisknutím uvolňovacích tlačítek CMA v horní části vnitřního a vnějšího krytu zámku (3).

   ![Ilustrace hlavní instalace CMA](media/fxt-install/cma-install-400.png)

   CMA lze otáčet mimo systém pro přístup a servis. Na sklopném konci zvedněte CMA od zásobníku, aby se odsuzoval (1). Po sejmutí z přihrádky odhoupne cma od systému (2).

   ![Ilustrace CMA otočené otevřené pro službu](media/fxt-install/cma-swing-over-tray-400.png)

## <a name="install-the-front-bezel-optional"></a>Instalace předního rámečku (volitelně)

Tato část vysvětluje, jak nainstalovat a odebrat přední rámeček (čelní panel) pro hardware Azure FXT Edge Filer. 

Instalace předního rámečku: 

1. Vyhledejte a vyjměte klíč rámečku, který je součástí balíčku rámečku. 
1. Zarovnejte rámeček s přední částí šasi a vložte kolíky na pravé straně rámečku do otvorů na pravé straně příruby stojanu uzlu. 
1. Namontovat levý konec rámečku na podvozek. Stiskněte rámeček, dokud tlačítko na levé straně nezacvakne na místo.
1. Uzamkněte rámeček klíčem.

Odstranění předního rámečku: 
1. Odemkněte rámeček pomocí klíče rámečku.
1. Stiskněte uvolňovací tlačítko na levé straně a vytáhněte levý konec rámečku od podvozku.
1. Odpojit pravý konec a vyjměte rámeček.
   
   ![Obrázek znázorňující uvolňovací tlačítko na levo od rámečku a jeho odstranění vytažením z levé strany](media/fxt-install/remove-bezel-edited-600.png)

## <a name="next-steps"></a>Další kroky

Po vybalení a racku zařízení pokračujte v nastavení připojením síťových kabelů a připojením střídavého napájení k azure FXT Edge Filer.

> [!div class="nextstepaction"]
> [Kabel síťové porty a napájení](fxt-network-power.md)