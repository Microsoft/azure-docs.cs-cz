---
title: Vytvoření testu přes Internet Analyzer pomocí portálu | Microsoft Docs
description: V tomto článku se dozvíte, jak vytvořit první test přes Internet Analyzer.
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 64bbed9b558d4c20889b28a5247e2113d20daa77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "73501768"
---
# <a name="create-an-internet-analyzer-test-using-portal-preview"></a>Vytvoření testu přes Internet Analyzer pomocí portálu (Preview)

Existují dva způsoby, jak vytvořit prostředek nástroje Internet Analyzer pomocí Azure Portal nebo pomocí rozhraní příkazového [řádku](internet-analyzer-cli.md). Tato část vám pomůže vytvořit nový prostředek Azure Internet Analyzer pomocí našeho prostředí portálu.

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Než začnete

Verze Public Preview je k dispozici pro použití globálně; úložiště dat je ale ve verzi Preview omezené na *USA – západ 2* .

## <a name="basics"></a>Základy

1. Získat přístup k aplikaci Internet Analyzer Preview pomocí **návody se účastní verze Preview?** pokyny najdete v tématu [Nejčastější dotazy k Azure Internet Analyzer](internet-analyzer-faq.md).
2. Z domovské stránky v [Azure Portal](https://preview.portal.azure.com)klikněte na **+ vytvořit prostředek**. Nástroj Internet Analyzer je aktuálně k dispozici pouze ve verzi Preview Azure Portal.
3. Na **nové** stránce vyhledejte v poli *Hledat v Marketplace* text "Internet Analyzer".
4. Klikněte na tlačítko **Internet Analyzer (Preview)**. Ujistěte se, že je Vydavatel *Microsoft* a kategorie jsou *sítě*.
5. Na stránce **Internet Analyzer (Preview)** klikněte na **vytvořit** a otevřete stránku **vytvořit analyzátor sítě Internet** .
6. Pro prostředek nástroje Internet Analyzer zadejte následující nastavení konfigurace:

    * **Předplatné:** Předplatné Azure pro hostování nového prostředku Internet Analyzer. **_Použijte stejné ID předplatného, které se používá k vyžádání přístupu k verzi Preview._**
    * **Skupina prostředků:** Skupina prostředků Azure, ve které se vytvoří nový prostředek Internet Analyzer. Pokud nemáte existující skupinu prostředků, můžete vytvořit novou.
    * **Název:** Název nového profilu prostředku nástroje Internet Analyzer.
    * **Oblast:** Veřejná oblast Azure, ve které se prostředek vytvoří. Během období Preview je k dispozici pouze *USA – západ 2* .

7. Po dokončení zadávání nastavení profilu klikněte na tlačítko **zkontrolovat + vytvořit**.

## <a name="configuration"></a>Konfigurace

Splnění základních kroků je předpokladem pro konfiguraci testu a vložení klienta jazyka JavaScript. Po vytvoření profilu můžete nakonfigurovat svůj první test tak, že přejdete na **nastavení > konfigurace** .

1. Zadejte název testu do pole **název testu** .
2. Do pole **Popis** přidejte popis testu.
3. Klikněte na **Konfigurovat koncový bod** – na pravé straně se zobrazí karta. Vyberte typ koncového bodu, pro který chcete konfigurovat – jednu oblast Azure, několik oblastí Azure nebo vlastní koncový bod.

    >
    ***Předem nakonfigurované koncové body: kombinace jedné a více oblastí Azure***
    * Vyberte oblast nebo sadu oblastí z [předkonfigurovaného seznamu koncových bodů Azure](internet-analyzer-faq.md).
    * V dalším kroku vyberte typ architektury aplikace nebo doručování obsahu, který chcete vyhodnotit.
        * Jedna oblast Azure: akcelerace webu ([přední vrátka Azure](https://azure.microsoft.com/services/frontdoor/)), ukládání statických obsahu do mezipaměti ([Azure CDN pro Microsoft](https://azure.microsoft.com/services/cdn/)) nebo žádné.
        * Více oblastí Azure: akcelerace webu ([přední vrátka Azure](https://azure.microsoft.com/services/frontdoor/)), řízení DNS ([Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/))  

    ***Vlastní koncové body***
    * Postupujte podle pokynů na stránce [Vytvoření vlastního koncového bodu](internet-analyzer-custom-endpoint.md) .
    * Vložte umístění adresy URL protokolu HTTPS na obrázek s příponou One-pixel na portálu.
    >

4. Kliknutím na **Přidat** přidejte koncový bod do testu.
5. Opakujte kroky 1-4 ke konfiguraci druhého koncového bodu. Koncový bod B je vždy měřen jako relativní ke koncovému bodu A – při konfiguraci koncových bodů zvažte, který koncový bod by měl být testovacím ovládacím prvkem.
6. Kliknutím na tlačítko **Uložit** uložte test. Po uložení testu již nelze upravovat koncové body určitého testu.
7. Vyberte testy, které chcete spustit, a klikněte na **Spustit test**. Tím se změní **_stav_*testů na _*_spuštěno_**. Testy můžete spustit kdykoli, ale klient JavaScript musí být vložen pro test a zahájit shromažďování měření.
8. V jakémkoli okamžiku přidejte další testy. Počítejte s tím, že jedinečný klient jazyka JavaScript nebude vygenerován, dokud nebude vytvořen jeden test.

## <a name="embed-client"></a>Vložit klienta

Chcete-li zahájit libovolný test, musí být klient jazyka JavaScript vložen do vaší webové aplikace. Po nakonfigurování alespoň jednoho testu klikněte na tlačítko **zkontrolovat + vytvořit**, přejděte na **Nastavení > konfigurace** a zkopírujte klienta JavaScriptu. Konkrétní pokyny najdete na stránce pro [vložení klienta Internet Analyzer](internet-analyzer-embed-client.md) .  

## <a name="next-steps"></a>Další kroky

* Přečtěte si [Nejčastější dotazy k Internet Analyzer](internet-analyzer-faq.md)
* Přečtěte si další informace o vložení [klienta Internet Analyzer](internet-analyzer-embed-client.md) a vytvoření [vlastního koncového bodu](internet-analyzer-custom-endpoint.md).
