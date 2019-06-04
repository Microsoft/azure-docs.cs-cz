---
title: Kurz k instalaci – vybalení, racku kabel fyzické zařízení Azure Data Box Edge | Dokumentace Microsoftu
description: Druhé části kurzu o instalaci Azure Data Box Edge zahrnuje jak Vybalte, namontujte do racku a fyzickým zařízením.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 05/31/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 0a9939155d92897019dc1ad5651d249cda11b993
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476943"
---
# <a name="tutorial-install-azure-data-box-edge"></a>Kurz: Nainstalovat Azure Data Box Edge

Tento kurz popisuje, jak nainstalovat fyzické zařízení Data Box Edge. Postup instalace zahrnuje při rozbalování, montáže do racku a kabeláž zařízení. 

Instalace může trvat přibližně dvě hodiny pro dokončení.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Rozbalení zařízení
> * Namontovat zařízení
> * Zapojení kabeláže zařízení

## <a name="prerequisites"></a>Požadavky

Předpoklady pro instalaci fyzické zařízení následujícím způsobem:

### <a name="for-the-data-box-edge-resource"></a>Prostředek Data Box Edge

Než začnete, ujistěte se, že:

* Jste dokončili všechny kroky v [Příprava na nasazení Azure Data Box Edge](data-box-edge-deploy-prep.md).
    * Vytvořili jste prostředek Data Box Edge do zařízení nasadit.
    * Vygenerovali jste aktivační klíč pro aktivaci zařízení s použitím prostředku Data Box Edge.

 
### <a name="for-the-data-box-edge-physical-device"></a>Fyzické zařízení Data Box Edge

Před nasazením zařízení:

- Ujistěte se, že zařízení bezpečně postavená na bez stromové struktury, stabilní a úrovni pracovní plocha.
- Ověřte, že lokalita, kterou chcete nastavit, disponuje jedním z následujícího:
    - Standardní napájení z nezávislých zdroje

        -OR-
    - Jednotka distribuci napájení (PDU) rack s nepřerušitelný zdroj napájení (UPS)
    - K dispozici 1U slot na stojan, na který chcete připojit zařízení

### <a name="for-the-network-in-the-datacenter"></a>Síť v datovém centru

Než začnete:

- Zkontrolujte požadavky na síť pro nasazení hrany pole Data a nakonfigurujte síť datacenter konkrétní požadavky. Další informace najdete v tématu popisujícím [požadavky na síť pro Data Box Edge](data-box-edge-system-requirements.md#networking-port-requirements).

- Ujistěte se, že minimální šířky pásma Internetu je 20 MB/s pro optimální fungování zařízení.


## <a name="unpack-the-device"></a>Rozbalení zařízení

Toto zařízení se dodává v jedné krabici. Zařízení rozbalíte pomocí následujících kroků. 

1. Umístěte krabici na rovný povrch.
2. Zkontrolujte krabici a výplňový materiál, jestli nejsou pomačkané, pořezané nebo poškozené vodou nebo jestli nenesou stopy jiného zjevného poškození. Pokud pole nebo balení je vážně poškozen, neotevírejte ho. Obraťte se na podporu Microsoftu, která vám pomůže určit, jestli je zařízení v dobrém funkčním stavu.
3. Rozbalte krabici. Po rozbalení krabice se ujistěte, že obsahuje následující:
    - Jeden jeden skříň Data pole hraničního zařízení
    - Dva napájecí kabely
    - Sestavení sady jednoho lišty
    - Brožura bezpečnost, životní prostředí a regulační informace

Pokud jste nedostali všechny položky, které jsou tady uvedené, obraťte se na podporu Data Box Edge. Dalším krokem je stojan připojení vašich zařízení.


## <a name="rack-the-device"></a>Usazení zařízení do racku

Zařízení musí být nainstalován na standardní 19 palec stojanu. Pomocí následujícího postupu do racku připojení zařízení na standardní 19 palec stojanu.

> [!IMPORTANT]
> Aby zařízení Data Box Edge správně fungovala, musí být usazená do racku.


### <a name="prerequisites"></a>Požadavky

- Než začnete, přečtěte si pokyny bezpečnost v brožuru bezpečnost, životní prostředí a regulační informace. Tato publikace byl dodán se zařízením.
- Zahájení instalace rails v přiděleného prostoru, který je nejblíž k dolnímu okraji skříň stojanu.
- Pro konfiguraci připojení tooled lišty:
    -  Budete muset zadat osm šrouby: #10 až 32, #12 až 24, #M5 nebo #M6. Hlavní průměr šrouby musí být menší než 10 mm (0.4").
    -  Je třeba šroubovák šikmý plochý.

### <a name="identify-the-rail-kit-contents"></a>Identifikaci obsahu kit lišty

Vyhledejte součásti pro instalaci sestavení lišty kit:
1. Dva A7 Dell ReadyRails II klouzavé sestavení lišty
2. Dva popruhů hook a smyčky

    ![Určit obsah kit lišty](./media/data-box-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>Instalace a odebrání nástrojů bez rails (čtvereček hole nebo stojany round hole)

> [!TIP]
> Tato možnost je bez nástrojů, protože nevyžaduje nástroje instalace a odebrání rails do hranatých nezřetězený nebo zaokrouhlit otvory v stojanů.

1. Umístit části koncové levé a pravé lišty označené jako **přední** směřující dovnitř a zorientovat každého jednotlivého end pro mezery na straně front-přírub svislé stojanu.
2. Zarovnejte každého jednotlivého koncové mezery dolní a horní požadované mezery U.
3. Zapojení back-endu lišty dokud plně licencovaná místa na svislé rack ohraničení a zámek klikne na místě. Opakujte tyto kroky pro nastavení pozice a pracovní stanice front-endu část na svislé rack ohraničení.
4. Odeberte rails, o přijetí změn tlačítko vydání západku na střední část end a vyjměte každý lišty.

    ![Instalace a odebrání nástroje bez rails](./media/data-box-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>Instalace a odebrání tooled rails (stojany hole seznam s vlákny)

> [!TIP]
> Tato možnost je tooled, protože vyžaduje, aby nástroj (_šroubovák šikmý paušální_) instalovat a odebírat rails do vláken round otvorů v stojanů.

1. Odeberte tyto PIN kódy z přední a zadní připojení pomocí šroubovák šikmý paušální hranaté závorky.
2. O přijetí změn a otočení podsestav západku lišty odebrat z připojení uvedených v závorkách.
3. Vlevo a vpravo připojení rails k front-svislé rack přírub pomocí dvě dvojice šroubů připojte.
4. Snímek zpět levé a pravé závorky dál proti přírub zadní svislé stojan a připojte je pomocí dvě dvojice šroubů.

    ![Instalace a odebrání tooled rails](./media/data-box-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>Instalace systému do racku

1. O přijetí změn rails vnitřní snímek mimo racku, dokud se Uzamknout na místě.
2. Vyhledejte standoff zadní lišty na každé straně systému a snížit do zadní J sloty na sestaveních snímku. Otočte systému směrem dolů, dokud všechny standoffs lišty sedí ve J slotů.
3. Systém dovnitř push, dokud páky zámek, klikněte na tlačítko na místě.
4. Stisknutím tlačítka snímku verze zámku on rails nebo snímek systému do racku.

    ![Instalace systému do racku](./media/data-box-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>Odebrání racku systému

1. Vyhledejte páky zámek na stranách vnitřní rails.
2. Každá úroveň odemknete otočením až po jeho uvolnění pozice.
3. Pevně pochopit její podstatu stranách systému a vložit jej vpřed, dokud standoffs lišty do přední části J slotů. Zvedněte systému nahoru a od racku a umístěte ho na úrovni povrchu.

    ![Odebrání racku systému](./media/data-box-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>Zapojení a uvolnit zámek slam

> [!NOTE]
> Pro systémy není vybaven slam zámky zabezpečení systému pomocí šrouby, jak je popsáno v kroku 3 tohoto postupu.

1. Směřující popředí, vyhledejte slam zámek na obou stranách systému.
2. Zámků zapojení automaticky jako systém odesílají do racku a vydávají roztažením na zámků.
3. K zabezpečení systému pro dodávku do racku a pro další nestabilní prostředí, vyhledejte šroubovacím pevné připojení v rámci každé západku a posílit každý šroubovacím s #2 Phillips šroubovák.

    ![Zapojení a vydání slam západky](./media/data-box-edge-deploy-install/engaging-releasing-slam-latch.png)

### <a name="route-the-cables"></a>Směrovat kabely

> [!NOTE]
>  Pokud není pořadí volitelné Cable Management Arm (CMA), použít dva zavěšení a opakovat popruhů k dispozici v sadě lišty směrovat kabely zádi vašeho systému.

1. Vnější závorky CMA vyhledejte na vnitřní stranách obou přírub stojanu.
2. Vytvoření balíčku kabely jemně, přijímání změn je vymazat systému konektorů pro levé a pravé straně.
3. Vlákno hook a smyčka popruhů prostřednictvím slotů na vnější závorky CMA na každé straně systém a zabezpečení sady kabel.


    ![Směrovat kabely](./media/data-box-edge-deploy-install/routing-cables.png)

## <a name="cable-the-device"></a>Zapojení kabeláže zařízení

Následující postupy popisují, jak zapojení kabeláže zařízení Data Box Edge pro napájení a sítě.

Než začnete kabeláže zařízení, budete potřebovat následující:

- Fyzické zařízení Data Box Edge vybaleno a racku.
- Dva napájecí kabely
- Alespoň jeden síťový kabel 1 GbE RJ-45 pro připojení k rozhraní pro správu. Na zařízení jsou dvě síťová rozhraní 1 GbE – jedno pro správu a druhé pro data.
- Jeden měděný kabel 25 GbE SFP+ pro každé datové síťové rozhraní, které chcete konfigurovat. Nejméně jeden datový síťové rozhraní z PORT 2, PORT 3, PORT 4, PORT 5 nebo 6 portu musí být připojený k Internetu (s připojením k Azure).  
- Přístup ke dvěma jednotek pro distribuci napájení (doporučeno).

> [!NOTE]
> - Pokud se chcete připojit pouze jedno síťové rozhraní data, doporučujeme použít 25/10 GbE síťové rozhraní, jako je PORT 3, PORT 4, PORT 5 nebo 6 portu pro odesílání dat do Azure. 
> - Pro zajištění nejlepšího výkonu a zpracování velkých objemů dat zvažte připojení všech datových portů.
> - Data Box hraniční zařízení musí být připojené k síti datového centra, tak, aby se schopností ingestovat data ze zdrojových serverů se data.

V zařízení Data Box Edge:

- Přední panel má diskové jednotky a tlačítko napájení.

    - Existují 10 sloty disku ve vašem zařízení.
    - Slot 0 má jednotku SATA 240 GB použít jako disk s operačním systémem. Pozice 1 je prázdný a sloty 2 až 9 jsou použitou jako datové disky NVMe SSD.
- Zpět roviny zahrnuje redundantní napájení dodavatelského jednotek (PSUs).
- Zpět roviny má šest síťových rozhraní:

    - Dvě 1 GB/s rozhraní.
    - Čtyři 25 GB/s rozhraní, které může sloužit také jako 10 GB/s rozhraní.
    - Řadič správy základní desky (BMC).

- Zpět roviny má dvě síťové karty odpovídající 6 portů:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

Úplný seznam podporovaných kabely, přepínače a vysílače pro tyto síťové karty, přejděte na [Cavium FastlinQ 41000 řady Interoperability matice](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
 
Proveďte následující kroky a zapojení kabeláže zařízení pro napájení a sítě.

1. Identifikujte různé porty v rovině back vašeho zařízení.

    ![Zpět roviny poskytuje zařízení](./media/data-box-edge-deploy-install/backplane-cabled.png)

2. Vyhledejte sloty disk a na začátku zařízení tlačítko napájení.

    ![Přední roviny tohoto zařízení](./media/data-box-edge-deploy-install/device-front-plane-labeled-1.png)

3. Připojte napájecí kabely k oběma jednotkám PSU ve skříni. Pokud chcete zajistit vysokou dostupnost, nainstalujte a připojte obě jednotky k různým zdrojům napájení.
4. Připojte napájecí kabely k rozvodným energetickým jednotkám racku. Ujistěte se, že obě jednotky PSU používají samostatné zdroje napájení.
5. K zapnutí nastavení v zařízení klepněte na tlačítko napájení.
6. Rozhraní sítě 1 GbE PORT 1 připojení k počítači, který se používá ke konfiguraci fyzického zařízení. PORT 1 představuje vyhrazené rozhraní pro správu.
7. Připojte jeden nebo několik portů PORT 2, PORT 3, PORT 4, PORT 5 nebo PORT 6 k internetu nebo síti datacentra.

    - Pokud připojujete PORT 2, použijte síťový kabel RJ-45.
    - Pro rozhraní sítí 10 / 2510gbe použijte kabely SFP + mědi.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o Data Box Edge témata naučili jste se například:

> [!div class="checklist"]
> * Rozbalení zařízení
> * Usazení zařízení do racku
> * Zapojení kabeláže zařízení

V dalším kurzu se dozvíte, jak připojit, nastavit a aktivovat zařízení.

> [!div class="nextstepaction"]
> [Připojení a nastavení dat pole Edge](./data-box-edge-deploy-connect-setup-activate.md)
