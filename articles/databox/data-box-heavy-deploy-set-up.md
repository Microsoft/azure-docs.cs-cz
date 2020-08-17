---
title: Kurz nastavení Azure Data Boxu Heavy | Microsoft Docs
description: V tomto kurzu se naučíte, jak zapojit kabeláž Azure Data Boxu Heavy, připojit Azure Data Box Heavy a zapnout Azure Data Box Heavy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: c38ff7e642017afe5f220ae26d3a04c2c0b706ee
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2020
ms.locfileid: "87920938"
---
::: zone target = "docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box-heavy"></a>Kurz: Zapojení kabeláže a připojení Azure Data Boxu Heavy

::: zone-end

::: zone target = "chromeless"

## <a name="get-started-with-azure-data-box-heavy"></a>Začínáme s Azure Data Boxem Heavy

::: zone-end

::: zone target = "docs"

Tento kurz popisuje, jak zapojit kabeláž, připojit a zapnout Azure Data Box Heavy.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zapojení kabeláže Data Boxu Heavy
> * Připojení k Data Boxu Heavy

::: zone-end

::: zone target = "chromeless"

Tato příručka obsahuje pokyny ke kontrole požadavků, zapojení kabeláže a připojení zařízení, kopírování dat, jejich nahrání do Azure a následnému ověření nahraných dat.

::: zone-end

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste [Kurz: Objednání Azure Data Boxu Heavy](data-box-heavy-deploy-ordered.md).
2. Obdrželi jste Data Box Heavy a stav objednávky na portálu je **Doručeno**.
3. Prostudovali jste si [bezpečnostní pokyny k Data Boxu Heavy](data-box-safety.md).
4. V datacentru máte přístup k rovné ploše v blízkosti dostupného síťového připojení, kam je možné umístit takto velké zařízení. Toto zařízení není možné namontovat do racku.
5. Obdrželi jste čtyři uzemněné napájecí kabely pro použití s úložným zařízením.
6. Máte k síti datacentra připojený hostitelský počítač. Váš Data Box Heavy zkopíruje data z tohoto počítače. Na vašem hostitelském počítači musí běžet [podporovaný operační systém](data-box-heavy-system-requirements.md).
7. Vaše datacentrum má přístup k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. 
8. Musíte mít přenosný počítač s kabelem RJ-45, abyste se mohli připojit k místnímu uživatelskému rozhraní a nakonfigurovat zařízení. Pomocí přenosného počítače postupně nakonfigurujete jednotlivé uzly zařízení.
9. Pro každý uzel zařízení potřebujete jeden kabel s rychlostí 40 GB/s nebo jeden kabel s rychlostí 10 GB/s.
    - Zvolte kabely, které jsou kompatibilní se síťovým rozhraním [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html).
    - V případě kabelu s rychlostí 40 GB/s musí na konci kabelu na straně zařízení být QSFP+.
    - V případě kabelu s rychlostí 10 GB/s potřebujete kabel SFP+, který se na jednom konci připojí k přepínači s rychlostí 10 GB/s, s adaptérem QSFP+ na SFP+ (neboli adaptérem QSA) na druhém konci, který se připojí k zařízení.

::: zone target = "docs"

## <a name="cable-your-device-for-power"></a>Zapojení kabeláže zařízení k napájení

Při zapojování kabeláže zařízení postupujte následovně.

1. Zkontrolujte zařízení, zda nenese stopy porušení nebo není zjevně poškozeno. Pokud je zařízení porušené nebo vážně poškozené, nepokračujte. Okamžitě se [obraťte na podporu Microsoftu](data-box-disk-contact-microsoft-support.md), aby vám pomohla určit, jestli je zařízení v dobrém funkčním stavu nebo zda je potřeba poslat náhradní.
2. Přesuňte zařízení na místo instalace.

    ![Místo instalace zařízení Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-install-site.png)

3. Zablokujte zadní kolečka zařízení, jak je znázorněno níže.

    ![Zablokovaná kolečka zařízení Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-casters-locked.png)

4. Vyhledejte knoflíky, kterými se odemknou přední a zadní dvířka zařízení. Odemkněte přední dvířka a posuňte je tak, aby byla zarovnaná s boční stranou zařízení. To samé udělejte také se zadními dvířky.
    Když je zařízení v provozu, obě tato dvířka musí zůstat otevřená, aby optimálně proudil vzduch z přední do zadní části zařízení.

    ![Otevřená dvířka Data Boxu Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-doors-open.png)

5. V zásobníku na zadní straně zařízení by měly být čtyři napájecí kabely. Vyjměte všechny kabely ze zásobníku a dejte je stranou.

    ![Napájecí kabely Data Boxu Heavy v zásobníku](media/data-box-heavy-deploy-set-up/data-box-heavy-power-cords-tray.png)

6. Dalším krokem je identifikovat různé porty na zadní straně zařízení. Existují dva uzly zařízení: **NODE1** a **NODE2**. Oba uzly mají čtyři síťová rozhraní: **MGMT**, **DATA1**, **DATA2** a **DATA3**. Rozhraní **MGMT** slouží ke konfiguraci správy při počáteční konfiguraci zařízení. Rozhraní **DATA1**-**DATA3** jsou datové porty. Porty **MGMT** a **DATA3** mají rychlost 1 GB/s, zatímco porty **DATA1** a **DATA2** můžou fungovat jako porty s rychlostí 40 GB/s nebo 10 GB/s. V dolní části těchto dvou uzlů zařízení se nacházejí čtyři napájecí zdroje sdílené oběma uzly zařízení. Při pohledu na zařízení jsou **napájecí zdroje** označené jako **PSU1**, **PSU2**, **PSU3** a **PSU4** (zleva doprava).

    ![Porty Data Boxu Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-ports.png)

7. Připojte všechny čtyři napájecí kabely k napájecím zdrojům zařízení. Rozsvítí se zelené indikátory LED a zablikají.
8. Pomocí tlačítek napájení na přední straně zapněte uzly zařízení. Nechte tlačítko napájení několik sekund stisknuté, dokud se nerozsvítí modrá světla. Všechny zelené indikátory LED pro napájecí zdroje na zadní straně zařízení by už měly přestat blikat. Přední ovládací panel zařízení také obsahuje indikátory LED signalizující selhání. Když se rozsvítí indikátor LED signalizující selhání, značí to selhání napájecího zdroje nebo větráku nebo problém s diskovými jednotkami.  

    ![Přední ovládací panel Data Boxu Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-front-ops-panel.png)

## <a name="cable-first-node-for-network"></a>Připojení prvního uzlu k síti

Pomocí následujícího postupu připojte jeden z uzlů zařízení k síti.

1. Pomocí síťového kabelu CAT 6 RJ-45 (kabel na obrázku vpravo nahoře, připojený k zásuvce označené MGMT) připojte hostitelský počítač k portu pro správu s rychlostí 1 GB/s.
2. Pomocí kabelu QSFP+ (optického nebo měděného) připojte alespoň jedno síťové rozhraní s rychlostí 40 Gb/s (tomu se dává přednost před rozhraním s rychlostí 1 Gb/s) pro data. Pokud používáte přepínač s rychlostí 10 GB/s, použijte k připojení síťového rozhraní s rychlostí 40 GB/s pro data kabel SFP+ s adaptérem QSFP+ na SFP+ (adaptér QSA).

    ![Zapojená kabeláž portů Data Boxu Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-ports-cabled.png)

    > [!IMPORTANT]
    > Porty DATA1 a DATA2 jsou prohozené a neodpovídají údajům zobrazeným v místním webovém uživatelském rozhraní.
    > Kabelový adaptér s rychlostí 40 GB/s se připojí, jakmile ho zapojíte následujícím způsobem.

    ![Kabelový adaptér s rychlostí 40 GB/s pro Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-cable-adaptor.png)

## <a name="configure-first-node"></a>Konfigurace prvního uzlu

Pomocí následujícího postupu nastavte své zařízení s využitím místní konfigurace a webu Azure Portal.

1. Stáhněte si z portálu přihlašovací údaje zařízení. Přejděte na **Obecné > Podrobnosti zařízení**. Zkopírujte si **Heslo zařízení**. Tato hesla se na portálu váží ke konkrétní objednávce. Zobrazí se dvě sériová čísla zařízení, která odpovídají dvěma uzlům v Data Boxu Heavy. Heslo správce zařízení pro oba uzly je stejné.

    ![Přihlašovací údaje zařízení Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-device-credentials.png)

2. Pomocí síťového kabelu CAT 6 RJ-45 připojte klientskou stanici k zařízení.
3. Na adaptéru sítě Ethernet na počítači, pomocí kterého se připojujete k zařízení, nakonfigurujte statickou IP adresu `192.168.100.5` a podsíť `255.255.255.0`.

    ![Připojení Data Boxu Heavy k místnímu webovému uživatelskému rozhraní](media/data-box-heavy-deploy-set-up/data-box-heavy-connect-local-web-ui.png)

4. Připojte se k místnímu webovému uživatelskému rozhraní zařízení na následující adrese URL: `http://192.168.100.10`. Klikněte na **Upřesnit** a pak na **Pokračovat na adresu 192.168.100.10 (nebezpečné)** .
5. Zobrazí se **přihlašovací** stránka místního webového uživatelského rozhraní.
    
    - Jedno ze sériových čísel uzlů na této stránce je stejné v uživatelském rozhraní portálu i v místním webovém uživatelském rozhraní. Poznamenejte si mapování čísla uzlu na sériové číslo. Na portálu jsou dva uzly a dvě sériová čísla zařízení. Toto mapování vám pomůže určit, který uzel odpovídá kterému sériovému číslu.
    - V tuto chvíli je zařízení uzamčené.
    - Zadejte heslo správce zařízení, které jste získali v předchozím kroku, a přihlaste se k zařízení. Klikněte na **Přihlásit se**.

    ![Přihlášení k místnímu webovému uživatelskému rozhraní Data Boxu Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-unlock-device.png)

5. Na řídicím panelu se ujistěte, že jsou nakonfigurovaná síťová rozhraní. Na uzlu zařízení jsou čtyři síťová rozhraní: dvě s rychlostí 1 GB/s a dvě s rychlostí 40 GB/s. Jedno z rozhraní s rychlostí 1 GB/s je rozhraní pro správu a proto ho uživatel nemůže konfigurovat. Zbývající tři síťová rozhraní jsou vyhrazená pro data a uživatel je může konfigurovat.

- Pokud je ve vašem prostředí povolený protokol DHCP, síťová rozhraní se nakonfigurují automaticky.
- Pokud protokol DHCP není povolený, přejděte na Nastavení síťových rozhraní a v případě potřeby přiřaďte statické IP adresy.

    ![Řídicí panel Data Boxu Heavy pro uzel 1](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-1.png)

## <a name="configure-second-node"></a>Konfigurace druhého uzlu

Proveďte kroky podrobně popsané v části [Konfigurace prvního uzlu](#configure-first-node) u druhého uzlu zařízení.

![Řídicí panel Data Boxu Heavy pro uzel 2](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-2.png)

Po dokončení nastavení zařízení se můžete připojit ke sdíleným složkám zařízení a zkopírovat do zařízení data z vašeho počítače.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených s Azure Data Boxem Heavy, mezi která patří:

> [!div class="checklist"]
> * Zapojení kabeláže Data Boxu Heavy
> * Připojení k Data Boxu Heavy

V dalším kurzu se dozvíte, jak do Data Boxu Heavy zkopírovat data.

> [!div class="nextstepaction"]
> [Kopírování dat do Azure Data Boxu](./data-box-heavy-deploy-copy-data.md)

::: zone-end
