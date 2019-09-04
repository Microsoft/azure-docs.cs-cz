---
title: Kurz nastavení Azure Data Box Heavy | Microsoft Docs
description: Informace o tom, jak kabelovat a připojit Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: f5473ae7bd9ac1615d59d18c996e01902e1adbd6
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241419"
---
::: zone target = "docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box-heavy"></a>Kurz: Připojte se k vašemu Azure Data Box Heavy kabelem a připojte se.

::: zone-end

::: zone target = "chromeless"

## <a name="get-started-with-azure-data-box-heavy"></a>Začínáme s Azure Data Box Heavy

::: zone-end

::: zone target = "docs"

V tomto kurzu se dozvíte, jak můžete Azure Data Box Heavy kabelovat, připojit a zapnout.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Data Box Heavy kabelem
> * Připojení k Data Box Heavy

::: zone-end

::: zone target = "chromeless"

Tato příručka poskytuje pokyny pro kontrolu požadavků, kabelů a připojení zařízení, kopírování dat, nahrání do Azure a následné kontrole nahraných dat.

::: zone-end

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili [jste kurz: Pořadí Azure Data Box Heavy](data-box-heavy-deploy-ordered.md).
2. Dostali jste Data Box Heavy se **doručí**stav objednávky na portálu.
3. Prošli jste si [pokyny pro zabezpečení data box Heavy](data-box-safety.md).
4. Je nutné mít přístup k plochému webu v datacentru s blízkým připojením k dostupnému síťovému připojení, které může zařízení s těmito nároky přizpůsobit. Toto zařízení se nedá připojit k stojanu.
5. Obdrželi jste čtyři uzemněné napájecí šňůry, které se používají s vaším úložným zařízením.
6. Máte k síti datacentra připojený hostitelský počítač. Vaše Data Box Heavy zkopíruje data z tohoto počítače. Na hostitelském počítači musí běžet [podporovaný operační systém](data-box-heavy-system-requirements.md).
7. Vaše datacentrum má přístup k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. 
8. Pokud se chcete připojit k místnímu uživatelskému rozhraní a nakonfigurovat zařízení, musíte mít přenosný počítač s kabelem RJ-45. Pomocí přenosného počítače nakonfigurujete každý uzel zařízení jednou.
9. K jednomu uzlu zařízení potřebujete kabel 1 40-GB nebo kabel s rychlostí 10 GB/s.
    - Vyberte kabely, které jsou kompatibilní se síťovým rozhraním [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) .
    - Pro kabel 40 GB/s musí být zařízení zakončené kabelem QSFP +.
    - U kabelu s rychlostí 10 GB/s je potřeba kabel SFP +, který se na jednom konci připojí k přepínači s rychlostí 10 GB/s a s QSFP + až SFP + Adapter (nebo adaptér QUALIFIED Security ASSESSOR) pro konec, který se připojí k zařízení.

::: zone target = "docs"

## <a name="cable-your-device-for-power"></a>Připojte zařízení k elektrickému napájení

Pokud chcete zařízení kabelovat, proveďte následující kroky.

1. Zkontrolujte zařízení, zda nenese stopy porušení nebo není zjevně poškozeno. Pokud je zařízení porušené nebo vážně poškozené, nepokračujte. [Kontaktujte podpora Microsoftu](data-box-disk-contact-microsoft-support.md) hned, abyste pomohli posoudit, jestli je zařízení v dobrém pracovním řádu, a jestli k tomu potřebujete vyměnit.
2. Přesuňte zařízení na instalační Web.

    ![Lokalita instalace Data Box Heavy zařízení](media/data-box-heavy-deploy-set-up/data-box-heavy-install-site.png)

3. Odblokujte zadní přetypování na zařízení, jak je znázorněno níže.

    ![Data Box Heavy přetypování zařízení uzamčeno](media/data-box-heavy-deploy-set-up/data-box-heavy-casters-locked.png)

4. Vyhledejte ovladače, které odemknou přední a zadní dveře zařízení. Odemkněte a přesuňte přední dveře, dokud se neuvolní na straně zařízení. Tento postup opakujte i s back-dvířky.
    Dveře musí zůstat otevřené, když je zařízení v provozu, aby umožňovalo optimální proud vpřed na pozadí přes zařízení.

    ![Otevřené dveře Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-doors-open.png)

5. Zásobník na zadní straně zařízení by měl mít čtyři napájecí kabely. Odeberte ze zásobníku všechny kabely a umístěte je z libovolného místa.

    ![Data Box Heavy napájecí šňůry v zásobníku](media/data-box-heavy-deploy-set-up/data-box-heavy-power-cords-tray.png)

6. Dalším krokem je identifikace různých portů na zadní straně zařízení. Existují dva uzly zařízení, **Uzel1** a **Uzel2**. Každý uzel má čtyři síťová rozhraní, **správu**, **Data1**, **Data2**, **DATA3**. Ke konfiguraci správy při počáteční konfiguraci **zařízení se používá Správa.** Data1-**DATA3** jsou datové porty. Porty pro **správu** a **DATA3** mají 1 GB/s, zatímco **Data1**a **Data2** můžou pracovat jako porty 40 GB/s nebo porty s rychlostí 10 GB/s. V dolní části obou uzlů zařízení jsou čtyři jednotky napájení (PSUs), které jsou sdíleny mezi dvěma uzly zařízení. Při obtváření tohoto zařízení jsou **PSUsy** **PSU1**, **PSU2**, **PSU3**a **PSU4** zleva doprava.

    ![Porty Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-ports.png)

7. Připojte všechny čtyři napájecí kabely k napájení zařízení. Zelené diody LED zapínají a blikají.
8. Pomocí tlačítek napájení v přední rovině zapněte uzly zařízení. Nechte tlačítko napájení stisknuté po dobu několika sekund, dokud modré indikátory nepřijde na. Všechny zelené indikátory LED pro napájení v rámci zařízení by teď měly být plné. Front-end zařízení obsahuje také indikátory LED chyb. Je-li indikátor LED chyby osvětlen, znamená to vadné PSU nebo ventilátor nebo problém s diskovou jednotkou.  

    ![Data Box Heavy panel front-OPS](media/data-box-heavy-deploy-set-up/data-box-heavy-front-ops-panel.png)

## <a name="cable-first-node-for-network"></a>Zapojte se do prvního uzlu sítě.

V jednom z uzlů zařízení proveďte následující kroky, aby se síťový kabel.

1. Použijte síťový kabel RJ-45 s CAT 6 (modrý kabel na obrázku) a připojte tak hostitelský počítač k portu pro správu s rychlostí 1 GB/s.
2. Použijte kabel QSFP + (Fiber nebo měď) pro připojení k datům minimálně 1 40 GB/s (upřednostňovaných přes 1 GB/s) síťového rozhraní. Pokud používáte přepínač s rychlostí 10 GB/s, použijte k připojení síťového rozhraní 40 GB/s (Qualified Security assessor Adapter) pro data síťový adaptér GB/s () kabel SFP +.

    ![Porty Data Box Heavy kabelové](media/data-box-heavy-deploy-set-up/data-box-heavy-ports-cabled.png)

    > [!IMPORTANT]
    > DATA 1 a DATA2 jsou přepnuta a neodpovídají tomu, co se zobrazuje v místním webovém uživatelském rozhraní.
    > Kabel 40 GB/s se připojí, když jste vložili způsob, jak je znázorněno níže.

    ![Adaptér kabelu Data Box Heavy 40-GB/s](media/data-box-heavy-deploy-set-up/data-box-heavy-cable-adaptor.png)

## <a name="configure-first-node"></a>Konfigurovat první uzel

Proveďte následující kroky a nastavte zařízení pomocí místní konfigurace a Azure Portal.

1. Stáhněte si z portálu přihlašovací údaje zařízení. Přejděte na **Obecné > Podrobnosti zařízení**. Zkopírujte si **Heslo zařízení**. Tato hesla jsou vázaná na konkrétní pořadí na portálu. V případě odpovídajících dvou uzlů v Data Box Heavy se zobrazí dvě sériová čísla zařízení. Heslo správce zařízení pro oba uzly je stejné.

    ![Data Box Heavy přihlašovací údaje zařízení](media/data-box-heavy-deploy-set-up/data-box-heavy-device-credentials.png)

2. Připojte klientskou pracovní stanici k zařízení prostřednictvím síťového kabelu CAT6 RJ-45.
3. Nakonfigurujte adaptér sítě Ethernet na počítači, který používáte pro připojení k zařízení se statickou IP adresou `192.168.100.5` a podsítí. `255.255.255.0`

    ![Data Box Heavy se připojuje k místnímu webovému uživatelskému rozhraní](media/data-box-heavy-deploy-set-up/data-box-heavy-connect-local-web-ui.png)

4. Připojte se k místnímu webovému uživatelskému rozhraní zařízení na následující adrese `http://192.168.100.10`URL:. Klikněte na **Upřesnit** a potom klikněte na **Přejít k 192.168.100.10 (nezabezpečené)** .
5. Zobrazí se **přihlašovací** stránka místního webového uživatelského rozhraní.
    
    - Jedna z sériových čísel uzlu na této stránce se shoduje s uživatelským rozhraním portálu i s místním webovým uživatelským rozhraním. Poznamenejte si číslo uzlu pro mapování sériového čísla. Na portálu jsou dva uzly a dvě sériová čísla zařízení. Toto mapování vám pomůže pochopit, který uzel odpovídá sériovému číslu.
    - V tuto chvíli je zařízení uzamčené.
    - Zadejte heslo správce zařízení, které jste získali v předchozím kroku, a přihlaste se k zařízení. Klikněte na **Přihlásit se**.

    ![Přihlášení k Data Box Heavy místního webového uživatelského rozhraní](media/data-box-heavy-deploy-set-up/data-box-heavy-unlock-device.png)

5. Na řídicím panelu zkontrolujte, že jsou nakonfigurovaná síťová rozhraní. Uzel zařízení obsahuje čtyři síťová rozhraní, dvě 1 GB/s a 2 40 GB/s. Jedním z rozhraní s 1 GB/s je rozhraní pro správu, a proto není uživatelsky konfigurovatelné. Zbývající tři síťová rozhraní jsou vyhrazená pro data a můžou je nakonfigurovat uživatel.

- Pokud je ve vašem prostředí povolený protokol DHCP, síťová rozhraní se nakonfigurují automaticky.
- Pokud není protokol DHCP povolený, vyberte v případě potřeby možnost nastavit síťová rozhraní a přiřaďte statické IP adresy.

    ![Uzel Data Box Heavyho řídicího panelu 1](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-1.png)

## <a name="configure-second-node"></a>Konfigurovat druhý uzel

Proveďte kroky popsané v části [Konfigurace prvního uzlu](#configure-first-node) pro druhý uzel zařízení.

![Uzel Data Box Heavyho řídicího panelu 2](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-2.png)

Po dokončení nastavení zařízení se můžete připojit ke sdíleným složkám zařízení a zkopírovat do zařízení data z vašeho počítače.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o Azure Data Box Heavy témata, jako například:

> [!div class="checklist"]
> * Data Box Heavy kabelem
> * Připojení k Data Box Heavy

Přejděte k dalšímu kurzu, kde se dozvíte, jak kopírovat data v Data Box Heavy.

> [!div class="nextstepaction"]
> [Kopírování dat do Azure Data Boxu](./data-box-heavy-deploy-copy-data.md)

::: zone-end
