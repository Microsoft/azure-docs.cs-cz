---
title: Kurz instalace – rozbalení, Rack, kabelové Azure Stack Edge pro fyzické zařízení | Microsoft Docs
description: Druhý kurz o instalaci Azure Stack Edge pro zahrnuje postup rozbalení, stojanu a kabelu fyzického zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/17/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Azure Stack Edge Pro in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 9aa02521d91d41380b1bdac3efe50ab3d196a856
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90894205"
---
# <a name="tutorial-install-azure-stack-edge-pro"></a>Kurz: instalace Azure Stack Edge pro

V tomto kurzu se dozvíte, jak nainstalovat fyzické zařízení Azure Stack Edge pro. Postup instalace zahrnuje rozbalení, připojení do racku a zapojení do kabeláže zařízení. 

Dokončení instalace může trvat přibližně dvě hodiny.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Rozbalení zařízení
> * Stojan připojit zařízení
> * Zapojení kabeláže zařízení

## <a name="prerequisites"></a>Požadavky

Požadavky pro instalaci fyzického zařízení jsou následující:

### <a name="for-the-azure-stack-edge-resource"></a>Pro prostředek Azure Stack Edge

Než začnete, ujistěte se, že:

* Dokončili jste všechny kroky v části [Příprava na nasazení Azure Stack Edge pro](azure-stack-edge-deploy-prep.md).
    * Vytvořili jste prostředek Azure Stack Edge pro nasazení zařízení.
    * Vygenerovali jste aktivační klíč pro aktivaci zařízení pomocí Azure Stackho hraničního prostředku.

 
### <a name="for-the-azure-stack-edge-pro-physical-device"></a>Pro fyzické zařízení Azure Stack Edge pro

Před nasazením zařízení:

- Ujistěte se, že zařízení funguje bezpečně na plochém, stabilním a úrovni pracovního prostoru.
- Ověřte, že lokalita, kterou chcete nastavit, disponuje jedním z následujícího:
    - Standardní AC výkon z nezávislého zdroje

        - nebo -
    - Jednotka PDU napájení stojanu s nenepřerušitelným zdrojem napájení (UPS)
    - Dostupná patice 1U na stojanu, do kterého chcete zařízení připojit

### <a name="for-the-network-in-the-datacenter"></a>Síť v datovém centru

Než začnete:

- Projděte si požadavky na síť pro nasazení Azure Stack Edge pro a konfigurujte síť datacenter podle požadavků. Další informace najdete v tématu [požadavky na síť Azure Stack Edge pro](azure-stack-edge-system-requirements.md#networking-port-requirements).

- Ujistěte se, že minimální šířka pásma internetu je 20 MB/s pro optimální fungování zařízení.


## <a name="unpack-the-device"></a>Rozbalení zařízení

Toto zařízení se dodává v jedné krabici. Zařízení rozbalíte pomocí následujících kroků. 

1. Umístěte krabici na rovný povrch.
2. Zkontrolujte krabici a výplňový materiál, jestli nejsou pomačkané, pořezané nebo poškozené vodou nebo jestli nenesou stopy jiného zjevného poškození. Pokud je box nebo balení vážně poškozené, neotevírejte ho. Obraťte se na podporu Microsoftu, která vám pomůže určit, jestli je zařízení v dobrém funkčním stavu.
3. Rozbalte krabici. Po rozbalení krabice se ujistěte, že obsahuje následující:
    - Jedna skříň zařízení Azure Stack Edge pro
    - Dva napájecí kabely
    - Jedno sestavení pro železniční sadu
    - Bezpečnostní brožura, informace o životním prostředí a regulativní informace

Pokud jste neobdrželi všechny uvedené položky, obraťte se na podporu Azure Stack Edge pro. Dalším krokem je připojení zařízení k stojanu.


## <a name="rack-the-device"></a>Usazení zařízení do racku

Zařízení musí být nainstalované na standardním racku na 19 palců. Pomocí následujícího postupu Zapojte zařízení do racku na standardní skříň na 19 palců.

> [!IMPORTANT]
> Zařízení Azure Stack Edge pro musí být připojená do racku pro správnou operaci.


### <a name="prerequisites"></a>Požadavky

- Než začnete, přečtěte si pokyny pro bezpečnost v brožuře o zabezpečení, ochraně životního prostředí a regulativní informace. Tato brožura byla dodávána se zařízením.
- Zahajte instalaci kolejnic do vyhrazeného místa, které je nejblíže k dolnímu okraji skříňky racku.
- Pro konfiguraci nástroje pro připojení k nástroji:
    -  Je potřeba dodat osm šrouby: #10-32, #12-24, #M5 nebo #M6. Průměr horních šroubů musí být menší než 10 mm (0,4).
    -  Potřebujete Screwdriver s plochou čárkou.

### <a name="identify-the-rail-kit-contents"></a>Identifikace obsahu v sadě kolejnice

Vyhledejte komponenty pro instalaci sestavení se sadou pro železnici:
1. Dvě A7 Dell ReadyRails II – klouzavé železniční sestavení
2. Dva popruhy zavěšení a smyček

    ![Identifikace obsahu v sadě kolejnice](./media/azure-stack-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>Instalace a odebrání nástrojů – bez zábradlí (racky na druhou nebo kulaté otvory)

> [!TIP]
> Tato možnost je menší než nástroj, protože nevyžaduje nástroje pro instalaci a odebrání kolejnic do nezávitového čtverce nebo na kulaté díry v rackech.

1. Umístěte vlevo a vpravo zakončení kolejnice s označením **dopředu** a orientovat každou koncovou stranu na vzdálenosti v otvorech na přední straně svislé příruby stojanu.
2. Zarovnejte jednotlivé konce dolů a horních děr z požadovaných prostorů U.
3. Zapojte se do konce kolejnice až do úplného umístění na vertikálním přírubě regálu a západky se klikne na místo. Zopakováním těchto kroků můžete umístit přední stranu na vertikální přírubu.
4. Pokud chcete tyto kolejnice odebrat, Stáhněte tlačítko pro vydání západek na střední bod koncového bodu a odinstalujte jednotlivé kolejnice.

    ![Instalace a odebrání nástrojů – bez kolejnicí](./media/azure-stack-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>Instalace a odebrání nástrojů na železnici (racky s vlákny)

> [!TIP]
> Tato možnost je vydaná, protože vyžaduje nástroj (_Screwdriver s plochou_ obdobu) pro instalaci a odebrání kolejnic do závitových otvorů v rackech.

1. Odeberte PIN kódy z front-a zadních závorek pomocí Screwdriver s plochou čárkou.
2. Umožňuje načíst a otočit podsestavení západky kolejnice a odebrat je z montážní hranaté závorky.
3. Připojte levé a pravé montážní kolejnice k přednímu vertikálnímu přírubě na racku pomocí dvou párů šroubů.
4. Posunutí vlevo a vpravo od zadních závorek směrem k zadním svislým přírubám a jejich připojení pomocí dvou párů šroubů.

    ![Instalace a odebrání nástrojů na železnici](./media/azure-stack-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>Instalace systému do racku

1. Vydejte vnitřní snímek ze stojanu, dokud se nezamkne.
2. Vyhledejte zadní kolejnici Standoff na všech stranách systému a snižte jejich množství na zadních \ sloty v sestaveních snímků. Otáčejte v systému směrem dolů, dokud se všechny standoffs kolejnice nesedí v podpaticích J.
3. Přehrajte systém do režimu až do chvíle, kdy kliknete na místo.
4. V obou kolejnicích stiskněte tlačítka pro zámek posunutí a posunutí systému do stojanu.

    ![Instalace systému do stojanu](./media/azure-stack-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>Odebrat systém ze stojanu

1. Vyhledejte páčky zámku na stranách vnitřních kolejnic.
2. Odemkněte jednotlivé páky tím, že je překročíte k její pozici vydání.
3. Potáhne strany systému pevně a předají ho do té doby, než se standoffs kolejnice na přední straně J slotů. Zvedněte systém směrem od stojanu a umístěte ho na plochu úrovně.

    ![Odebrat systém ze stojanu](./media/azure-stack-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>Zapojení a vydání západky Slam

> [!NOTE]
> U systémů, které nejsou vybavené západkou Slam, zabezpečte systém pomocí šroubů, jak je popsáno v kroku 3 tohoto postupu.

1. Lícem dozadu, najděte západku Slam na obou stranách systému.
2. Zámky se automaticky účtují, protože systém je vložen do racku a uvolní se na západce.
3. Chcete-li zabezpečit systém pro expedici v racku nebo pro jiné nestabilní prostředí, vyhledejte šroub na pevném straně pod každým západkou a sePhillipste každý šroub pomocí #2ho screwdriveru.

    ![Zapojit a uvolnit západku Slam](./media/azure-stack-edge-deploy-install/engaging-releasing-slam-latch.png)


## <a name="cable-the-device"></a>Zapojení kabeláže zařízení

Směrování kabelů a potom kabel zařízení. Následující postupy vysvětlují, jak zařízení Azure Stack Edge pro pro napájení a síť kabelem.

Než začnete kabelovat vaše zařízení, budete potřebovat následující:

- Vaše fyzické zařízení Azure Stack Edge pro, nebalené a připojené k racku.
- Dva napájecí kabely
- Alespoň jeden síťový kabel 1 GbE RJ-45 pro připojení k rozhraní pro správu. Na zařízení jsou dvě síťová rozhraní 1 GbE – jedno pro správu a druhé pro data.
- Jeden měděný kabel 25 GbE SFP+ pro každé datové síťové rozhraní, které chcete konfigurovat. Aspoň jedno rozhraní datové sítě z portu 2, PORT 3, port 4, PORT 5 nebo PORT 6 musí být připojené k Internetu (s připojením k Azure).  
- Přístup ke dvěma jednotkám distribuce napájení (doporučeno).

> [!NOTE]
> - Pokud se připojujete pouze k jednomu síťovému rozhraní, doporučujeme, abyste pro posílání dat do Azure používali síťové rozhraní 25 nebo 10 GbE, jako je PORT 3, PORT 4, PORT 5 nebo PORT 6. 
> - Pro zajištění nejlepšího výkonu a zpracování velkých objemů dat zvažte připojení všech datových portů.
> - Zařízení Azure Stack Edge pro by mělo být připojené k síti datacenter, aby mohl ingestovat data ze serverů zdrojů dat.

Na zařízení Azure Stack Edge pro:

- Přední panel má diskové jednotky a tlačítko napájení.

    - Před zařízením je 10 diskových slotů.
    - Slot 0 má jednotku SATA 240 GB, která se používá jako disk operačního systému. Slot 1 je prázdný a sloty 2 až 9 jsou NVMe SSD, které se používají jako datové disky.
- Zadní rovina zahrnuje nadbytečné jednotky napájení (PSUs).
- Zadní rovina má šest síťových rozhraní:

    - Rozhraní 2 1 až GB/s.
    - Rozhraní 4 25-GB, která můžou sloužit taky jako rozhraní s rychlostí 10 GB/s.
    - Řadič pro správu základní desky (BMC).

- Zadní rovina má dvě síťové karty odpovídající 6 portům:

    - FastLinQ 41264 QLogic
    - FastLinQ 41262 QLogic

Úplný seznam podporovaných kabelů, přepínačů a vysílačů u těchto síťových karet najdete v části [Cavium FastlinQ 41000 – matice interoperability](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
 
Proveďte následující kroky, které zařízení zapojte do sítě pro napájení a síť.

1. Identifikujte různé porty v zadní rovině zařízení.

    ![Zadní rovina kabelového zařízení](./media/azure-stack-edge-deploy-install/backplane-cabled.png)

2. Vyhledejte diskové sloty a tlačítko napájení na přední straně zařízení.

    ![Přední rovina zařízení](./media/azure-stack-edge-deploy-install/device-front-plane-labeled-1.png)

3. Připojte napájecí kabely k oběma jednotkám PSU ve skříni. Pokud chcete zajistit vysokou dostupnost, nainstalujte a připojte obě jednotky k různým zdrojům napájení.
4. Připojte napájecí kabely k rozvodným energetickým jednotkám racku. Ujistěte se, že obě jednotky PSU používají samostatné zdroje napájení.
5. Kliknutím na tlačítko napájení zapněte zařízení.
6. Připojte PORT 1 síťového rozhraní 1-GbE k počítači, který se používá ke konfiguraci fyzického zařízení. PORT 1 představuje vyhrazené rozhraní pro správu.
7. Připojte jeden nebo několik portů PORT 2, PORT 3, PORT 4, PORT 5 nebo PORT 6 k internetu nebo síti datacentra.

    - Pokud připojujete PORT 2, použijte síťový kabel RJ-45.
    - Pro síťová rozhraní 10/25 až GbE použijte kabely SFP + Copper.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o Azure Stack tématech pro Edge pro, jako jsou:

> [!div class="checklist"]
> * Rozbalení zařízení
> * Usazení zařízení do racku
> * Zapojení kabeláže zařízení

V dalším kurzu se dozvíte, jak připojit, nastavit a aktivovat zařízení.

> [!div class="nextstepaction"]
> [Připojení a nastavení Azure Stack Edge pro](./azure-stack-edge-deploy-connect-setup-activate.md)
