---
title: Návod k instalaci – vybalte, rack, kabel azure data box edge fyzické zařízení | Dokumenty společnosti Microsoft
description: Druhý kurz o instalaci Azure Data Box Edge zahrnuje, jak rozbalit, rack a kabel fyzické zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/17/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: fe74db34e62a80935954c6cfc2e591d49a84b0b7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76263944"
---
# <a name="tutorial-install-azure-data-box-edge"></a>Kurz: Instalace Azure Data Box Edge

Tento kurz popisuje, jak nainstalovat fyzické zařízení Data Box Edge. Postup instalace zahrnuje vybalení, montáž do racku a kabeláž zařízení. 

Instalace může trvat přibližně dvě hodiny.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Rozbalení zařízení
> * Rack namontujte zařízení
> * Zapojení kabeláže zařízení

## <a name="prerequisites"></a>Požadavky

Předpoklady pro instalaci fyzického zařízení následujícím způsobem:

### <a name="for-the-data-box-edge-resource"></a>Prostředek Data Box Edge

Než začnete, ujistěte se, že:

* Dokončili jste všechny kroky v [části Příprava na nasazení Azure Data Box Edge](data-box-edge-deploy-prep.md).
    * Vytvořili jste prostředek Data Box Edge pro nasazení zařízení.
    * Vygenerovali jste aktivační klíč pro aktivaci zařízení s použitím prostředku Data Box Edge.

 
### <a name="for-the-data-box-edge-physical-device"></a>Fyzické zařízení Data Box Edge

Před nasazením zařízení:

- Ujistěte se, že zařízení bezpečně spočívá na rovném, stabilním a rovném pracovním povrchu.
- Ověřte, že lokalita, kterou chcete nastavit, disponuje jedním z následujícího:
    - Standardní střídavý proud z nezávislého zdroje

        - nebo -
    - Rozváděcí jednotka napájení do racku (PDU) s nepřerušitelným zdrojem napájení (UPS)
    - K dispozici slot 1U na stojanu, na který chcete zařízení namontovat

### <a name="for-the-network-in-the-datacenter"></a>Síť v datovém centru

Než začnete:

- Zkontrolujte síťové požadavky pro nasazení Data Box Edge a nakonfigurujte síť datového centra podle požadavků. Další informace najdete v tématu popisujícím [požadavky na síť pro Data Box Edge](data-box-edge-system-requirements.md#networking-port-requirements).

- Ujistěte se, že minimální šířka pásma Internetu je 20 Mb/s pro optimální fungování zařízení.


## <a name="unpack-the-device"></a>Rozbalení zařízení

Toto zařízení se dodává v jedné krabici. Zařízení rozbalíte pomocí následujících kroků. 

1. Umístěte krabici na rovný povrch.
2. Zkontrolujte krabici a výplňový materiál, jestli nejsou pomačkané, pořezané nebo poškozené vodou nebo jestli nenesou stopy jiného zjevného poškození. Pokud je krabice nebo obal vážně poškozen, neotvírejte ji. Obraťte se na podporu Microsoftu, která vám pomůže určit, jestli je zařízení v dobrém funkčním stavu.
3. Rozbalte krabici. Po rozbalení krabice se ujistěte, že obsahuje následující:
    - Jedno jedno skříňové zařízení Data Box Edge
    - Dva napájecí kabely
    - Sestava jedné sady kolejnic
    - Brožura o bezpečnosti, životním prostředí a regulačních informacích

Pokud jste neobdrželi všechny zde uvedené položky, obraťte se na podporu Data Box Edge. Dalším krokem je montáž zařízení do racku.


## <a name="rack-the-device"></a>Usazení zařízení do racku

Zařízení musí být instalováno na standardním 19palcovém stojanu. Následující postup použijte pro montáž zařízení do racku na standardní 19palcový stojan.

> [!IMPORTANT]
> Aby zařízení Data Box Edge správně fungovala, musí být usazená do racku.


### <a name="prerequisites"></a>Požadavky

- Než začnete, přečtěte si bezpečnostní pokyny v brožované příručce Pro bezpečnost, životní prostředí a regulační informace. Tato brožura byla dodána se zařízením.
- Začněte instalovat kolejnice v přiděleném prostoru, který je nejblíže ke spodní části skříně racku.
- Pro montážní konfiguraci obrojecí lišty:
    -  Musíte dodat osm šroubů: #10-32, #12-24, #M5 nebo #M6. Průměr hlavy šroubů musí být menší než 10 mm (0,4").
    -  Potřebuješ šroubovák s plochým hrotem.

### <a name="identify-the-rail-kit-contents"></a>Identifikujte obsah železniční sady

Vyhledejte součásti pro instalaci sestavy železniční sady:
1. Dvě posuvné kolejnice A7 Dell ReadyRails II
2. Dva popruhy na háček a smyčku

    ![Identifikace obsahu železniční sady](./media/data-box-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>Instalace a odstranění lišt bez nástrojů (čtvercové otvory nebo kulaté otvory)

> [!TIP]
> Tato možnost je bez použití nástrojů, protože nevyžaduje nástroje pro instalaci a demontáž kolejnic do čtvercových nebo kulatých otvorů v regálech, které nejsou závitem.

1. Umístěte koncové kusy levé a pravé kolejnice označené **směrem dopředu** směrem dovnitř a nasměrujte každý koncový kus k usazení do otvorů na přední straně svislých přírub racku.
2. Zarovnejte každý koncový kus ve spodních a horních otvorech požadovaných prostorů U.
3. Zapněte zadní konec kolejnice tak, aby se plně nenasadila na svislou přírubu stojanu a západka nezapadla na místo. Opakujte tyto kroky tak, aby byl přední díl na svislé přírubě racku usadit a usadit.
4. Chcete-li odstranit kolejnice, vytáhněte tlačítko pro uvolnění západky na středovém bodě koncového dílu a sesaďte každou kolejnici.

    ![Instalace a odebrání lišt bez nástrojů](./media/data-box-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>Instalace a odstranění obrobek (závitové otvory)

> [!TIP]
> Tato volba je opojná, protože vyžaduje nástroj _(plochý šroubovák)_ pro instalaci a vyjmutí kolejnic do závitových kulatých otvorů v regálech.

1. Odstraňte kolíky z předních a zadních montážních držáků pomocí šroubováku s plochým hrotem.
2. Zatáhněte a otočte podsestavami západky kolejnice, abyste je odstranili z montážních držáků.
3. Připojte levé a pravé montážní lišty k předním svislým přírubám racku pomocí dvou párů šroubů.
4. Posuňte levou a pravou zadní konzolu dopředu proti zadním svislým přírubám racku a připevněte je pomocí dvou párů šroubů.

    ![Instalace a odebrání nástrojových lišt](./media/data-box-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>Instalace systému do stojanu

1. Vytáhněte vnitřní posuvné lišty ze stojanu, dokud se nezablokují na místo.
2. Umístěte patovou situaci zadní kolejnice na obou stranách systému a spusťte je do zadních omeků J na skluzavce. Otočte systém směrem dolů, dokud nebudou všechny patové situace na kolejnici usazeny v drážkách J.
3. Zatlačte systém dovnitř, dokud zacvaknou páčky zámku na místo.
4. Stiskněte tlačítka zámku pro uvolnění posuvu na obou lištách a zasuňte systém do stojanu.

    ![Instalace systému do stojanu](./media/data-box-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>Vyjměte systém ze stojanu

1. Umístěte zajišťovací páčky po stranách vnitřních kolejnic.
2. Odemkněte každou páčku otočením do polohy uvolnění.
3. Pevně uchopte strany systému a zatahěte jej dopředu, dokud nebudou patové situace kolejnice v přední části otečků J. Zvedněte systém nahoru a pryč od stojanu a umístěte jej na rovný povrch.

    ![Vyjměte systém ze stojanu](./media/data-box-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>Zapněte a uvolněte západku slam

> [!NOTE]
> U systémů, které nejsou vybaveny slam zámky, zajistěte systém pomocí šroubů, jak je popsáno v kroku 3 tohoto postupu.

1. Směrem dopředu vyhledejte západku slamu na obou stranách systému.
2. Západky se automaticky zapínají, když je systém zatlačen do stojanu a uvolňuje se vytažením zámků.
3. Chcete-li zajistit systém pro přepravu v racku nebo v jiných nestabilních prostředích, vyhledejte šroub s tvrdou montáží pod každou západku a utáhněte každý šroub #2 křížovým šroubovákem.

    ![Zapněte a uvolněte slam západku](./media/data-box-edge-deploy-install/engaging-releasing-slam-latch.png)


## <a name="cable-the-device"></a>Zapojení kabeláže zařízení

Kabely veďte a poté zakabelejte zařízení kabelem. Následující postupy vysvětlují, jak vytvořit kabel zařízení Data Box Edge pro napájení a síť.

Než začnete zařízení kabeláž, budete potřebovat následující:

- Vaše fyzické zařízení Data Box Edge, nezabalené a namontované na racku.
- Dva napájecí kabely
- Alespoň jeden síťový kabel 1 GbE RJ-45 pro připojení k rozhraní pro správu. Na zařízení jsou dvě síťová rozhraní 1 GbE – jedno pro správu a druhé pro data.
- Jeden měděný kabel 25 GbE SFP+ pro každé datové síťové rozhraní, které chcete konfigurovat. K Internetu musí být připojeno alespoň jedno rozhraní datové sítě z portů 2, PORT 3, PORT 4, PORT 5 nebo PORT 6 (s připojením k Azure).  
- Přístup ke dvěma napájecím distribučním jednotkám (doporučeno).

> [!NOTE]
> - Pokud připojujete pouze jedno rozhraní datové sítě, doporučujeme k odesílání dat do Azure použít síťové rozhraní s kapacitou 25 gbE, například PORT 3, PORT 4, PORT 5 nebo PORT 6. 
> - Pro zajištění nejlepšího výkonu a zpracování velkých objemů dat zvažte připojení všech datových portů.
> - Zařízení Data Box Edge by mělo být připojeno k síti datového centra, aby mohlo ingestovat data ze serverů zdrojů dat.

Na zařízení Data Box Edge:

- Přední panel má diskové jednotky a tlačítko napájení.

    - V přední části zařízení je 10 diskových slotů.
    - Slot 0 má 240GB SATA disk používaný jako disk operačního systému. Slot 1 je prázdný a sloty 2 až 9 jsou NVMe SSD používané jako datové disky.
- Zadní rovina obsahuje redundantní napájecí zdroje (PSU).
- Zadní rovina má šest síťových rozhraní:

    - Dvě rozhraní 1 Gb/s.
    - Čtyři rozhraní 25 Gb/s, která mohou sloužit také jako rozhraní 10 Gb/s.
    - Řadič správy základní desky (BMC).

- Zadní rovina má dvě síťové karty odpovídající 6 portům:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

Úplný seznam podporovaných kabelů, přepínačů a vysílačů pro tyto síťové karty naleznete v části [Cavium FastlinQ 41000 Series Interoperability Matrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
 
Postupujte podle následujících kroků k připojení zařízení k napájení a síti.

1. Identifikujte různé porty v zadní rovině zařízení.

    ![Zadní rovina kabelového zařízení](./media/data-box-edge-deploy-install/backplane-cabled.png)

2. Vyhledejte diskové sloty a tlačítko napájení na přední straně zařízení.

    ![Přední rovina zařízení](./media/data-box-edge-deploy-install/device-front-plane-labeled-1.png)

3. Připojte napájecí kabely k oběma jednotkám PSU ve skříni. Pokud chcete zajistit vysokou dostupnost, nainstalujte a připojte obě jednotky k různým zdrojům napájení.
4. Připojte napájecí kabely k rozvodným energetickým jednotkám racku. Ujistěte se, že obě jednotky PSU používají samostatné zdroje napájení.
5. Stisknutím tlačítka napájení přístroj zapnete.
6. Připojte síťové rozhraní port 1 GbE port 1 k počítači, který slouží ke konfiguraci fyzického zařízení. PORT 1 představuje vyhrazené rozhraní pro správu.
7. Připojte jeden nebo několik portů PORT 2, PORT 3, PORT 4, PORT 5 nebo PORT 6 k internetu nebo síti datacentra.

    - Pokud připojujete PORT 2, použijte síťový kabel RJ-45.
    - Pro síťová rozhraní 10/25 GbE použijte měděné kabely SFP+.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech Data Box Edge, jako je například:

> [!div class="checklist"]
> * Rozbalení zařízení
> * Usazení zařízení do racku
> * Zapojení kabeláže zařízení

V dalším kurzu se dozvíte, jak připojit, nastavit a aktivovat zařízení.

> [!div class="nextstepaction"]
> [Připojení a nastavení okraje datové schránky](./data-box-edge-deploy-connect-setup-activate.md)
