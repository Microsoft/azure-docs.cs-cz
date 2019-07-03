---
title: Kurz týkající se instalace fyzického zařízení s Azure FXT hrany Filer | Dokumentace Microsoftu
description: Jak Vybalte, namontujte do racku a fyzickým zařízením součást mezipaměť Microsoft Azure FXT hrany Filer hybridní úložiště
services: ''
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: ed9eca88e5ccc386b25acb95fa729a3cfb95cbd0
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543454"
---
# <a name="tutorial-install-azure-fxt-edge-filer"></a>Kurz: Instalace Azure FXT hrany vyfiltrovat 

Tento kurz popisuje instalace uzlu hardware pro mezipaměť Azure FXT hrany Filer hybridní úložiště. Musíte nainstalovat alespoň tři uzly hardwaru k vytvoření clusteru Azure FXT hrany vyfiltrovat.

Postup instalace zahrnuje při rozbalování a připojení zařízení a připojení na starosti správu kabel (CMA) a přední stojanu. Samostatné kurz vysvětluje připojení síťové kabely a propojování napájení. 

Bude trvat přibližně jednu hodinu, než instalace uzlu Azure FXT hrany vyfiltrovat. 

V tomto kurzu zahrnuje tyto kroky: 

> [!div class="checklist"]
> * Rozbalení zařízení
> * Připojení zařízení do racku
> * Nainstalujte přední rám (volitelné)

## <a name="installation-prerequisites"></a>Požadavky na instalaci 

Než začnete, ujistěte se, že datové centrum a stojanu, které se použijí se tyto funkce:

* K dispozici 1U patice na racku které hodláte připojovat zařízení.
* AC zdroj napájení a chlazení systémy, které splňují potřeby Filer Azure FXT hrany. (Čtení [výkon a teplotní specifikace](fxt-specs.md#power-and-thermal-specifications) nápovědu k plánování a velikosti instalace.)  

  > [!NOTE] 
  > Plně využít dvě jednotky dodavatelského redundantní napájení (PSUs), pomocí jednotek pro distribuci napájení na dva okruhy jinou větev, při připojování napájení ze sítě. Čtení [připojení napájecích kabelů](fxt-network-power.md#connect-power-cables) podrobnosti.  

## <a name="unpack-the-hardware-node"></a>Rozbalení uzlu hardware 

Každý uzel Azure FXT hrany Filer se dodává v jediné pole. Dokončete tyto kroky pro jeho usazení do zařízení.

1. Umístěte krabici na rovný povrch.

2. Zkontrolujte krabici a výplňový materiál, jestli nejsou pomačkané, pořezané nebo poškozené vodou nebo jestli nenesou stopy jiného zjevného poškození. Pokud pole nebo balení je vážně poškozen, neotevírejte ho. Obraťte se na podporu Microsoftu, která vám pomůže určit, jestli je zařízení v dobrém funkčním stavu.

3. Rozbalte krabici. Ujistěte se, že zahrnuje následující položky:
   * Jedno zařízení FXT jeden skříň
   * Dva napájecí kabely
   * Jeden přední a klíč
   * Sestavení sady jednoho lišty
   * Na starosti správu jeden kabel (CMA)
   * Brožura CMA instalační pokyny
   * Brožura pokyny k instalaci do racku
   * Brožura bezpečnost, životní prostředí a regulační informace

Pokud jste nedostali všechny položky uvedené, požádejte o podporu dodavatele zařízení. 

Zajistěte, aby že měl dostatek času k dosažení stejného teploty jako místnosti před instalací nebo ukončit v zařízení. Pokud si všimnete kondenzaci na libovolnou část zařízení, počkejte aspoň 24 hodin před instalací.

Dalším krokem je stojan připojení vašich zařízení.

## <a name="rack-the-device"></a>Usazení zařízení do racku

Zařízení Azure FXT hrany Filer musí být nainstalován v standardní 19 palec stojanu. 

Mezipaměť Azure FXT hrany Filer hybridní úložiště tvoří tři nebo více Azure FXT hrany Filer zařízení. Opakujte kroky instalace stojan pro každé zařízení, která je součástí systému. 

### <a name="rack-install-prerequisites"></a>Instalace požadovaných součástí do racku

* Než začnete, přečtěte si pokyny bezpečný přístup z více v bezpečnost, životní prostředí a informace o dodržování legislativních brožura dodaný s vaším zařízením.

  > [!NOTE]
  > Vždy používejte dva uživatelé při zrušení uzlu, včetně toho, když ji nainstalovat do racku nebo ji odeberte z racku. 

* Určete typ instalace lišty použít s rack vaše zařízení. 
  * Čtvereček nebo round hole modul snap-in stojany postupujte podle pokynů lišty bez nástrojů.
  * Zřetězené hole stojany postupujte podle pokynů tooled lišty. 
  
    Pro tooled lišty konfigurace připojení budete muset zadat osm šrouby zadejte 10 až 32, 12 až 24, M5 nebo M6. Hlavní průměr šrouby musí být menší než 10 mm (0.4").

### <a name="identify-the-rail-kit-contents"></a>Identifikaci obsahu kit lišty

Vyhledejte součásti pro instalaci sestavení lišty kit:

1. Dva A7 Dell ReadyRails II klouzavé lišty sestavení (1)
1. Dvě připojit a opakovat popruhů (2)

![Číslované vykreslování obsahu kit lišty](media/fxt-install/identify-rail-kit-contents-400.png)

### <a name="rail-assembly---tool-less-rails-square-hole-or-round-hole-racks"></a>Lišty sestavení – nástroj bez rails (čtvereček hole nebo stojany round hole)

Stojany s čtvereček nebo round modul snap-in děr pomocí následujícího postupu Poskládejte si a nainstalujte rails. 

1. Umístit části koncové levé a pravé lišty označené jako **přední** směřující dovnitř. Umístění každého jednotlivého end tak, aby ho míst mezery na straně front-přírub svislé stojanu. (1)

2. Zarovnává jednotlivé koncové mezery dolní a horní-of-rack v místa, které chcete připojit. (2)

3. Zapojení back-endu lišty dokud plně licencovaná místa na svislé rack ohraničení a zámek klikne na místě. Opakujte tyto kroky pro nastavení pozice a pracovní stanice je tímto druhem front-endu na svislé rack ohraničení. (3)

> [!TIP]
> Odeberte rails, o přijetí změn tlačítko vydání západku na střední část end (4) a vyjměte každý lišty.

![Diagram instalace a odebrání nástroje bez rails s číslované kroky](media/fxt-install/installing-removing-tool-less-rails-400.png)

### <a name="rail-assembly---tooled-rails-threaded-hole-racks"></a>Sestavení lišty - tooled rails (stojany hole seznam s vlákny)

Stojany s vlákny děr pomocí následujícího postupu Poskládejte si a nainstalujte rails.

1. Odebrání přední a zadní připojení závorek s paušální šikmý šroubovák kódy PIN. (1)
1. O přijetí změn a otočení podsestav západku lišty odebrat z připojení uvedených v závorkách. (2)
1. Vlevo a vpravo připojení rails k front-svislé rack přírub pomocí dvě dvojice šroubů připojte. (3)
1. Snímek zpět levé a pravé závorky dál proti přírub zadní svislé stojan a připojte je pomocí dvě dvojice šroubů. (4)

![Diagram instalace a odebrání tooled rails s číslované kroky](media/fxt-install/installing-removing-tooled-rails-400.png)

### <a name="install-the-system-in-the-rack"></a>Instalace systému do racku

Postupujte podle těchto kroků připojit zařízení Azure FXT hrany vyfiltrovat v racku.

1. O přijetí změn rails vnitřní snímek mimo racku, dokud se Uzamknout na místě. (1)
1. Vyhledejte standoff zadní lišty na každé straně zařízení a snížit do zadní J sloty na sestaveních snímku. (2) 
1. Otočení zařízení směrem dolů, dokud všechny standoffs lišty sedí ve J slotů. (3)
1. Push dovnitř zařízení, dokud páky zámek, klikněte na tlačítko na místě.
1. Stisknutím tlačítka snímku verze zámku na obou rails (4) a přesuňte ovladač zařízení do racku.

![Instalace systému v diagramu rack číslované kroky](media/fxt-install/installing-system-rack-400.png)

### <a name="remove-the-system-from-the-rack"></a>Odebrání racku systému

Chcete-li odebrat zařízení z racku, podle následujícího postupu. 

1. Vyhledejte páky zámek na stranách vnitřní rails (1).
2. Každá úroveň odemknete otočením až po jeho uvolnění pozice (2).
3. Pevně pochopit její podstatu stranách systému a vložit jej vpřed, dokud standoffs lišty do přední části J slotů. Zvedněte systému nahoru a od racku a umístěte ho na úrovni surface (3).

![Obrázek odebrání systému stojan, číslované kroky](media/fxt-install/removing-system-rack-400.png)

### <a name="engage-the-slam-latch"></a>Zapojení slam západky

1. Na obou stranách systému směřující popředí, vyhledejte slam západku (1).
2. Zámků zapojení automaticky, protože systém je vloženo do racku. 

Uvolnit zámků při odebírání systému, že tyto změny (2).

Volitelné šrouby pevné připojení jsou k dispozici pro zabezpečení systému rack k odeslání nebo v jiných prostředích nestabilní. Najít šroubovacím za každý zámek a posílit pomocí #2 Phillips šroubovák (3).

![Číslované obrázek zapojení a uvolnění slam západky](media/fxt-install/engaging-releasing-slam-latch-400.png)

### <a name="install-the-cable-management-arm"></a>Nainstalujte na starosti správu kabel 

Na starosti správu volitelné kabel (CMA) je součástí Filer FXT hrany. Tištěné pokynů pro instalaci jsou k dispozici v balíčku. 

1. Rozbalení a identifikaci součástí cable management arm kit:
   * Na hlavním panelu CMA (1)
   * CMA (2)
   * Tie kabel nylonem zabalí (3)
   * CMA přílohy závorky (4)
   * Stav indikátoru kabel (5) 

   > [!TIP] 
   > K zabezpečení CMA k odeslání do racku, smyčky tie zabalí kolem košíky a na hlavním panelu a je pevně cinch. Zabezpečení CMA tímto způsobem bude také zabezpečit systém v nestabilním prostředí.

   ![Obrázek CMA částí](media/fxt-install/cma-kit-400.png)

2. Nainstalujte CMA na hlavním panelu.

   Na hlavním panelu CMA poskytuje podporu a funguje jako umístěte pro CMA. 

   1. Zarovnat a zapojte každé straně zásobník do závorek příjemce na vnitřní okraje rails. 
   1. Push panelu dopředu, dokud ho klikne na místě. (1)
   1. Na hlavním panelu odebrat, vměstnat tlačítka uvolňovací – směrem k středu a o přijetí změn na hlavním panelu mimo příjemce hranaté závorky (2).

   ![Obrázek CMA instalace na hlavním panelu](media/fxt-install/cma-tray-install-400.png)

3. Nainstalujte závorky CMA přílohy. 

   > [!NOTE]
   >
   > * Můžete připojit CMA směrem doprava nebo doleva připojení železniční, v závislosti na tom, jak máte v úmyslu kabely trasy ze systému. 
   > * Pro usnadnění práce připojte CMA na straně opačný napájení (A na straně). Pokud se připojí na straně B, musí CMA odpojená Pokud chcete odebrat vnější napájení. 
   > * Před odebráním výkon poskytuje vždy odeberte do zásobníku. 

   ![Obrázek instalace CMA závorka](media/fxt-install/cma-bracket-l-r-install-400.png)

   1. Vyberte odpovídající závorku CMA přílohy pro stranu, ve které chcete připojit CMA (nebo na straně A straně B).
   1. Nainstalujte závorky přílohy CMA s odpovídající strana A nebo B na straně označení zádi lišty snímku.
   1. Zarovnejte děr ve závorka s PIN kódy na lišty snímku. Doručit bez vyžádání závorka dokud ho uzamkne na místě. 

4. Nainstalujte CMA.

   1. Zádi systému vejít zámek na front-endu CMA na nejvnitřnější závorka sestavení snímku, dokud se zámek zaujme (1). 
   1. Přizpůsobit zámek na konci nejkrajnější závorka, dokud se zámek zaujme (2). 
   1. Odebrat CMA, musí se vypnout obou zámky stisknutím tlačítka CMA verze v horní části krytech vnitřní a vnější západku (3).

   ![Obrázek hlavního CMA instalace](media/fxt-install/cma-install-400.png)

   CMA lze otočit mimo systém, hledá access a služby. Na konci zavěšené lift CMA od panelu pro vyjměte ho (1). Po jeho unseated ze zásobníku, postupná CMA mimo systém (2).

   ![Otočí obrázek CMA otevřen pro služby](media/fxt-install/cma-swing-over-tray-400.png)

## <a name="install-the-front-bezel-optional"></a>Nainstalujte přední rám (volitelné)

Tato část vysvětluje, jak instalovat a odebírat přední rám (rámem) pro Azure FXT hrany Filer hardwaru. 

Chcete-li nainstalovat přední rám: 

1. Vyhledejte a odstraňte lůžkem klíče, který je zahrnutý v balíčku lůžkem. 
1. Zarovnat lůžkem s přední skříň a vložit PIN kódy na pravé straně lůžkem do otvorů v uzlu na pravé straně rack připojení ohraničení. 
1. Přizpůsobit na levý konec lůžkem na skříň. Stisknutím klávesy lůžkem, dokud klikne na tlačítko na levé straně na místě.
1. Uzamčení lůžkem s klíčem.

Chcete-li odebrat přední rám: 
1. Odemknete lůžkem lůžkem klíč.
1. Na levé straně klikněte na tlačítko vydání a o přijetí změn na levý konec lůžkem od skříni.
1. Pravém konci vyjmutí a odeberte lůžkem.
   
   ![Obrázek znázorňující tlačítko verze v levé části lůžkem a jak odebrat roztažením ven z levé strany](media/fxt-install/remove-bezel-edited-600.png)

## <a name="next-steps"></a>Další postup

Po rozbalení nachází a namontovali do racku zařízení, pokračujte v instalaci tak, že připojení síťové kabely a připojení k Azure FXT hrany Filer napájení ze sítě.

> [!div class="nextstepaction"]
> [Zapojení síťové porty a napájení](fxt-network-power.md)