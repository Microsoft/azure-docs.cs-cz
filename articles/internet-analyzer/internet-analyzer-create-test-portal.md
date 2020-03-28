---
title: Vytvoření testu internetového analyzátoru pomocí portálu | Dokumenty společnosti Microsoft
description: V tomto článku se dozvíte, jak vytvořit první test nástroje Internet Analyzer.
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 64bbed9b558d4c20889b28a5247e2113d20daa77
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73501768"
---
# <a name="create-an-internet-analyzer-test-using-portal-preview"></a>Vytvoření testu internetového analyzátoru pomocí portálu (preview)

Existují dva způsoby, jak vytvořit prostředek Nástroje pro analýzu Internetu – pomocí portálu Azure nebo [pomocí příkazového příkazového příkazu](internet-analyzer-cli.md). Tato část vám pomůže vytvořit nový prostředek Azure Internet Analyzer pomocí našeho prostředí portálu.

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Než začnete

Verze Public Preview je k dispozici pro použití globálně. úložiště dat je však omezena na *US West 2* během náhledu.

## <a name="basics"></a>Základy

1. Získejte přístup k náhledu nástroje Internet Analyzer podle pokynů **How do I participate in preview?** z [nejčastějších dotazů nástroje Azure Internet Analyzer](internet-analyzer-faq.md).
2. Na domovské stránce na [webu Azure Portal](https://preview.portal.azure.com)klikněte na + Vytvořit **prostředek**. Internet Analyzer je momentálně dostupný jenom ve verzi Preview na webu Azure Portal.
3. Na **nové** stránce vyhledejte v poli *Hledat marketplace* možnost "Internetový analyzátor".
4. Klepněte na **položku Internetový analyzátor (náhled).** Zkontrolujte, zda je vydavatelem *společnost Microsoft* a zda je kategorie *Síťová .*
5. Na stránce **Internet Analyzer (preview)** klikněte na **Vytvořit** a otevřete **stránku Vytvořit internetový analyzátor.**
6. Zadejte následující nastavení konfigurace prostředku nástroje Internet Analyzer:

    * **Předplatné:** Předplatné Azure pro hostování nového prostředku Nástroje pro analýzu Internetu. ***Použijte stejné ID předplatného, které se používá k vyžádání přístupu k náhledu.***
    * **Skupina zdrojů:** Skupina prostředků Azure, ve které se vytvoří nový prostředek Nástroje pro analýzu internetu. Pokud nemáte existující skupinu prostředků, můžete vytvořit novou.
    * **Název:** Název nového profilu prostředku nástroje Internet Analyzer.
    * **Region:** Veřejná oblast Azure, ve které se prostředek vytvoří. Během náhledu je k dispozici pouze *US West 2.*

7. Po dokončení zadávání nastavení profilu klepněte na **tlačítko Zkontrolovat + vytvořit**.

## <a name="configuration"></a>Konfigurace

Dokončení základních kroků je předpokladem konfigurace testu a vložení klienta JavaScriptu. Po vytvoření profilu přejděte na **nastavení > konfigurace** a nakonfigurujte první test.

1. Do pole **Název testu** zadejte název testu.
2. Do pole **Popis** přidejte popis testu.
3. Klikněte na **Konfigurovat koncový bod** – karta se zobrazí z pravé strany. Vyberte typ koncového bodu, který chcete nakonfigurovat – jednu oblast Azure, více oblastí Azure nebo vlastní koncový bod.

    >
    ***Předkonfigurované koncové body: jedna a více kombinací oblastí Azure***
    * Vyberte oblast nebo sadu oblastí z [předem nakonfigurovaného seznamu koncových bodů Azure](internet-analyzer-faq.md).
    * Dále vyberte typ aplikace nebo architektury doručování obsahu, kterou chcete vyhodnotit.
        * Oblast single Azure: Akcelerace webu[(Azure Front Door),](https://azure.microsoft.com/services/frontdoor/)ukládání statického obsahu do mezipaměti ([Azure CDN pro Microsoft](https://azure.microsoft.com/services/cdn/)) nebo žádná.
        * Více oblastí Azure: Akcelerace webu[(Azure Front Door),](https://azure.microsoft.com/services/frontdoor/)řízení DNS[(Azure Traffic Manager)](https://azure.microsoft.com/services/traffic-manager/)  

    ***Vlastní koncové body***
    * Postupujte podle pokynů na stránce [Vytvořit vlastní koncový bod.](internet-analyzer-custom-endpoint.md)
    * Vložte umístění adresy URL HTTPS bitové kopie s jedním obrazovým obrazem na portálu.
    >

4. Kliknutím na **Přidat** přidejte koncový bod do testu.
5. Opakujte kroky 1-4 pro konfiguraci druhého koncového bodu. Koncový bod B se vždy měří vzhledem ke koncovému bodu A – při konfiguraci koncových bodů zvažte, který koncový bod by měl být vaším testovacím ovládacím prvkem.
6. Kliknutím na tlačítko **Uložit** test uložte. Po uložení testu již nelze upravovat koncové body konkrétního testu.
7. Vyberte testy, které chcete spustit, a klepněte na tlačítko **Spustit test**. Tím se změní ***stav*** test (testů) na ***Spuštěno***. Testy můžete spustit kdykoli, ale klient JavaScriptu musí být vložen, aby mohl test začít shromažďovat měření.
8. Přidejte další testy v libovolném bodě. Všimněte si, že jedinečný klient JavaScript nebude generován, dokud nebude vytvořen jeden test.

## <a name="embed-client"></a>Vložit klienta

Chcete-li zahájit jakýkoli test, musí být klient JavaScript vložen do webové aplikace. Po konfiguraci alespoň jednoho testu klepněte na **tlačítko Revize + vytvořit**, přejděte do nastavení > **konfigurace**a zkopírujte klienta JavaScriptu. Konkrétní pokyny naleznete na stránce [Klient embed internet analyzer.](internet-analyzer-embed-client.md)  

## <a name="next-steps"></a>Další kroky

* Přečtěte si [nejčastější dotazy k analyzátoru Internetu](internet-analyzer-faq.md)
* Přečtěte si další informace o vložení [klienta Nástroje pro analýzu Internetu](internet-analyzer-embed-client.md) a vytvoření [vlastního koncového bodu](internet-analyzer-custom-endpoint.md).
