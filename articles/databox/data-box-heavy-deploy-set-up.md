---
title: Kurz Azure Data Box náročné nastavit | Dokumentace Microsoftu
description: Další informace o zapojení a připojte vaše Azure Data Box náročné
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 05/24/2019
ms.author: alkohli
ms.openlocfilehash: f2f6b544b56977b3f1bfb6a4fb46a9f1e3bcc294
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427937"
---
# <a name="tutorial-cable-and-connect-to-your-azure-data-box-heavy-preview"></a>Kurz: Zapojení a připojte se k vaší Azure Data Box náročné (preview)


Tento kurz popisuje, jak pro zapojení, připojení a zapnout váš Azure Data Box náročné.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Kabel těžkých pole vaše Data
> * Připojte se k vaší těžkých pole Data

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Dokončili jste [kurzu: Pořadí Azure Data Box těžké](data-box-heavy-deploy-ordered.md).
2. Jste přijali vaše náročné pole Data a stav objednávky na portálu je **dodáno**.
3. Můžete si [Data Box silná bezpečnostní pokyny](data-box-safety.md).
4. Musíte mít přístup k lokalitě bez stromové struktury v datovém centru pomocí blízkosti dostupné síťové připojení, který zvládne zařízení s nároky na tomto. Toto zařízení nejde namontováno v racku.
5. Obdrželi jste čtyři základě konkrétních napájecích kabelů používat s vaším zařízením úložiště.
6. Máte k síti datacentra připojený hostitelský počítač. Vaše Data Box náročné zkopíruje data z tohoto počítače. Musíte spustit hostitelský počítač [podporovaný operační systém](data-box-heavy-system-requirements.md).
7. Vaše datacentrum má přístup k vysokorychlostní síti. Důrazně doporučujeme, abyste měli připojení minimálně 10 GbE. 
8. Musíte mít přenosném počítači pomocí kabelu RJ-45 kategorie pro připojení k místní uživatelského rozhraní a konfigurace zařízení. Pomocí přenosný počítač nakonfigurovat každý uzel zařízení jen jednou.
9. Budete potřebovat jeden kabel 40 GB/s nebo kabel 10 GB/s na jeden uzel zařízení.
    - Zvolte kabely, které jsou kompatibilní s [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) síťové rozhraní.
    - Pro kabel 40 GB/s musí být QSFP + zařízení konec kabelu.
    - Kabel 10 GB/s je nutné kabel SFP +, která zpřístupní přepínač 10 GB/s na jednom konci a QSFP + end adaptéru SFP + (nebo adaptér podmínky), který se připojuje k zařízení.

## <a name="cable-your-device-for-power"></a>Zapojení kabeláže zařízení pro výkon

Proveďte následující kroky a zapojení kabeláže zařízení.

1. Zkontrolujte zařízení, zda nenese stopy porušení nebo není zjevně poškozeno. Pokud je zařízení porušené nebo vážně poškozené, nepokračujte. [Obraťte se na Microsoft Support](data-box-disk-contact-microsoft-support.md) okamžitě, abyste si mohli vyhodnotit, jestli je zařízení v dobrém stavu a pokud potřebují k dodání můžou nahradit aktuální soubor.
2. Přesune zařízení do instalace lokality.

    ![Data Box náročné zařízení instalace lokality](media/data-box-heavy-deploy-set-up/data-box-heavy-install-site.png)

3. Zamkněte zadní koleček na zařízení, jak je znázorněno níže.

    ![Data Box náročné koleček zařízení uzamčeno](media/data-box-heavy-deploy-set-up/data-box-heavy-casters-locked.png)

4. Vyhledejte knoflíky, které odhalují přední a zadní dveře zařízení. Odemknutí a přesuňte branou zarovnán na straně zařízení. Tento postup opakujte s zadní vrátka.
    Obě dveře musí zůstat otevřít, když je zařízení provozní umožňující optimální air zepředu dozadu toku prostřednictvím zařízení.

    ![Data Box náročné dveře open](media/data-box-heavy-deploy-set-up/data-box-heavy-doors-open.png)

5. Na hlavním panelu zádi zařízení by měl mít čtyři napájecích kabelů. Odeberte všechny kabely ze zásobníku a umístit je jste si poznamenali.

    ![Data Box náročné napájecích kabelů na hlavním panelu](media/data-box-heavy-deploy-set-up/data-box-heavy-power-cords-tray.png)

6. Dalším krokem je identifikace různých portů zádi zařízení. Existují dva uzly zařízení **NODE1** a **NODE2**. Každý uzel má čtyři síťových rozhraní, **MGMT**, **DATA1**, **DATA2**, **DATA3**. **MGMT** slouží ke konfiguraci správy během počáteční konfigurace zařízení. **Data1**-**DATA3** jsou data porty. **MGMT** a **DATA3** porty jsou 1 GB/s, zatímco **DATA1**, **DATA2** může fungovat jako porty 40 GB/s nebo porty 10 GB/s. V dolní části zařízení dva uzly jsou čtyři jednotky dodavatelského power (PSUs), které jsou sdíleny napříč uzly dvě zařízení. Jak jste toto zařízení pro rozpoznávání tváře **PSUs** jsou **PSU1**, **PSU2**, **PSU3**, a **PSU4** zleva doprava.

    ![Data Box náročné porty](media/data-box-heavy-deploy-set-up/data-box-heavy-ports.png)

7. Všechny čtyři napájecích kabelů připojení k zařízení napájecí zdroje. Zelený LED zapněte a blink.
8. Pomocí tlačítka napájení v rovině front-zapnutí zařízení uzly. Zachovat na tlačítku napájení na několik sekund, dokud nebude modré světla stisknuté. Všechny zelené LED pro dodávku napájení zadní zařízení by měl být nyní solid. Přední panel provozní zařízení také obsahuje LED selhání. Při selhání, který svítí LED, znamená to vadným PSU nebo ventilátoru nebo problém s diskové jednotky.  

    ![Pole náročné přední ops panel dat](media/data-box-heavy-deploy-set-up/data-box-heavy-front-ops-panel.png)

## <a name="cable-first-node-for-network"></a>První uzel kabelové sítě

Na jednom z uzlů zařízení proveďte následující kroky pro kabelové sítě.

1. Připojte hostitelský počítač k portu pro správu 1 GB/s pomocí síťového kabelu RJ-45 kategorie CAT 6 (modré kabel na obrázku).
2. Připojte alespoň jedno síťové rozhraní 40 GB/s (vhodnější než 1 GB/s) pro data pomocí kabelu Twinax QSFP + měděného (černé kabely na obrázku). Pokud pomocí přepínače 10 GB/s, pomocí kabelu Twinax SFP + měděného a QSFP + SFP + adaptér (adaptér podmínky) pro připojení 40 GB/s síťové rozhraní pro data.

    ![Data Box náročné porty zapojené](media/data-box-heavy-deploy-set-up/data-box-heavy-ports-cabled.png)

    > [!IMPORTANT]
    > DATA 1 a DATA2 jsou přepnuté a neshodují, co se zobrazí v místním webovém uživatelském rozhraní.
    > Adaptér kabel 40 GB/s se připojí při vložení způsob, jak je znázorněno níže.

    ![Data adaptér kabel pole náročné 40 GB/s](media/data-box-heavy-deploy-set-up/data-box-heavy-cable-adaptor.png)

## <a name="configure-first-node"></a>Konfigurace první uzel

Proveďte následující kroky k nastavení zařízení pomocí místní konfiguraci a webu Azure portal.

1. Stáhněte si z portálu přihlašovací údaje zařízení. Přejděte na **Obecné > Podrobnosti zařízení**. Zkopírujte si **Heslo zařízení**. Tato hesla jsou vázané na určitém pořadí na portálu. Odpovídající dvěma uzly náročné pole dat, se zobrazí dvě zařízení sériová čísla. Heslo správce zařízení pro oba uzly je stejný.

    ![Data Box náročné přihlašovací údaje zařízení](media/data-box-heavy-deploy-set-up/data-box-heavy-device-credentials.png)

2. Připojení k zařízení pomocí síťového kabelu RJ-45 kategorie CAT6 pracovní stanice klienta.
3. Nakonfigurujte adaptér sítě Ethernet na počítači, které používáte pro připojení k zařízení se statickou IP adresou z `192.168.100.5` a podsíť `255.255.255.0`.

    ![Data Box náročné připojí k místního webového uživatelského rozhraní](media/data-box-heavy-deploy-set-up/data-box-heavy-connect-local-web-ui.png)

4. Připojení k místní webové uživatelské rozhraní zařízení na následující adrese URL: `http://192.168.100.10`. Klikněte na tlačítko **Upřesnit** a potom klikněte na tlačítko **pokračujte 192.168.100.10 (unsafe)** .
5. Zobrazí se **přihlašovací** stránka místního webového uživatelského rozhraní.
    
    - Jeden uzel sériová čísla na této stránce odpovídá přes uživatelské rozhraní portálu a místního webového uživatelského rozhraní. Poznamenejte si číslo uzlu pro mapování sériové číslo. Existují dva uzly a dvěma sériová čísla zařízení na portálu. Toto mapování vám pomůže pochopit který uzel odpovídá která sériové číslo.
    - V tuto chvíli je zařízení uzamčené.
    - Zadejte heslo správce zařízení, který jste získali v předchozím kroku, a přihlaste se do zařízení. Klikněte na **Přihlásit se**.

    ![Přihlaste se k datové pole v případě velkého místního webového uživatelského rozhraní](media/data-box-heavy-deploy-set-up/data-box-heavy-unlock-device.png)

5. Na řídicím panelu Ujistěte se, že jsou nakonfigurovaná síťová rozhraní. Existují čtyři síťová rozhraní v uzlu zařízení, dvě 1 GB/s a dvě 40 GB/s. Jednou z 1 GB/s rozhraní je rozhraní pro správu a proto není konfigurovatelná uživatelem. Zbývající tři síťová rozhraní jsou vyhrazené pro data a dá se uživatel.

- Pokud je ve vašem prostředí povolený protokol DHCP, síťová rozhraní se nakonfigurují automaticky.
- Pokud není povolený DHCP, přejděte k nastavení síťových rozhraní a přiřazení statických IP adres v případě potřeby.

    ![Data Box náročné řídicí panel uzlu 1](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-1.png)

## <a name="configure-second-node"></a>Nakonfigurujte druhý uzel

Proveďte kroky popsané v [konfigurace první uzel](#configure-first-node) pro druhý uzel zařízení.

![Data Box náročné řídicí panel uzlu 2](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-2.png)

Po dokončení nastavení zařízení se můžete připojit ke sdíleným složkám zařízení a zkopírovat do zařízení data z vašeho počítače.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o Azure Data Box náročné témata, jako:

> [!div class="checklist"]
> * Kabel těžkých pole vaše Data
> * Připojte se k vaší těžkých pole Data

Přejděte k dalšímu kurzu, přečtěte si, jak kopírovat data na vaše Data Box náročné.

> [!div class="nextstepaction"]
> [Kopírování dat do Azure Data Boxu](./data-box-heavy-deploy-copy-data.md)
