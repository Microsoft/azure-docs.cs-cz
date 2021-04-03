---
title: Microsoft Azure Data Box Heavy – přehled | Microsoft Docs
description: Tento článek popisuje službu Azure Data Box Heavy, což je hybridní řešení, které umožňuje přenášet obrovské objemy dat do Azure.
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: overview
ms.date: 08/28/2019
ms.author: alkohli
ms.openlocfilehash: 2f1f01a8cfa25c222848e7cc5c86dc3532eba348
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "80437772"
---
# <a name="what-is-azure-data-box-heavy"></a>Co je Azure Data Box Heavy?

Azure Data Box Heavy vám umožní rychle a bezplatným a spolehlivým způsobem posílat stovky terabajtů dat do Azure. Data se přenesou do Azure tím, že vám Data Box Heavy zařízení s kapacitou úložiště 1 – PB, kterou vyplníte daty a pošlete zpátky do Microsoftu. Zařízení má robustní velká a malá písmena, která chrání a zabezpečují vaše data během přenosu.

Po přijetí zařízení v datovém centru ho nastavte pomocí místního webového uživatelského rozhraní. Zkopírujte data ze serverů do zařízení a pošlete ho zpět do Azure. V datovém centru Azure se vaše data odešlou do vašich Azure Storagech účtů. Celý proces můžete sledovat v Azure Portal.


> [!IMPORTANT]
> - Pokud chcete požádat o zařízení, zaregistrujte se do [Azure Portal](https://portal.azure.com).


## <a name="use-cases"></a>Případy použití

Data Box Heavy se nejlépe hodí pro velikosti dat ve stovkách terabajtů, kde připojení k síti není dostatečné pro nahrávání dat do Azure. Data můžete přesunout jednorázově, přesunovat je pravidelně nebo provést počáteční hromadný přesun a posléze data přenášet pravidelně. Zde je několik různých scénářů, ve kterých je vhodné k přenosu dat použít Data Box Heavy.

 - **Jednorázová migrace** – při tomto typu migrace se do Azure přesune velký objem místních dat.
     - Přesuňte knihovnu médií z offline pásek do Azure a vytvořte online knihovnu médií.
     - Migrujte farmu virtuálních počítačů, SQL Server a aplikace do Azure.
     - Přesuňte historická data do Azure pro podrobnou analýzu a sestavu pomocí HDInsight.

 - **Počáteční hromadný přesun** – při tomto typu migrace provedete počáteční hromadný přesun pomocí služby Data Box Heavy (kompletní) a přírůstkové přenosy potom provádíte po síti.
     - Například Data Box Heavy a partnerská řešení pro zálohování slouží k přesunu prvotní rozsáhlé historické zálohy do Azure. Poté se už přírůstková data přenáší prostřednictvím sítě do Azure Storage.

 - **Pravidelné nahrávání** – provádí se v případech, kdy pravidelně dochází ke generování velkých objemů dat, která je nutná přesunout do Azure. Příkladem může být výzkum energie, kdy se u vrtných plošin a soustavy větrných generátorů generuje videoobsah.

## <a name="benefits"></a>Výhody

Data Box Heavy je navržená tak, aby se do Azure přesunula obrovský objem dat bez jakéhokoli dopadu na vaši síť. Toto řešení má následující výhody:

- **Speed** data box Heavy využívá vysoce výkonná síťová rozhraní 40 GB/s.

- Data Box Heavy **zabezpečení** má vestavěnou ochranu zabezpečení pro zařízení, data a službu.
    - Zařízení má robustní obal, který zabezpečují šroubky odolné vůči neoprávněné manipulaci a nálepky odhalující manipulaci.
    - Data na zařízení nepřetržitě chrání 256bitové šifrování AES.
    - Zařízení je možné odemknout pouze heslem, které najdete na webu Azure Portal.
    - Služba samotná je chráněna funkcemi zabezpečení Azure.
    - Po nahrání dat do Azure se disky v zařízení vymažou vyčistit, v souladu se standardy Standard a NIST (National Institute of Standards and Technology) 800-88r1.


## <a name="features-and-specifications"></a>Funkce a specifikace

V této verzi Data Box Heavy zařízení obsahuje následující funkce.

| Specifikace                                          | Popis              |
|---------------------------------------------------------|--------------------------|
| Hmotnost                                                  | ~ 500 kg. <br>Zařízení pro uzamykání kol pro přenos|
| Dimenze                                              | Šířka: 26 palců (výška: 28 palců): 48 palců |
| Místo v racku                                              | Nejde připojit k racku.|
| Potřebné kabely                                         | 4 mleté 120 V/10 napájecí šňůra (NEMA 5-15) je součástí <br> Zařízení podporuje až 240 V napájení a má zásobníky napájení v jazyce C-13. <br> Používání síťových kabelů kompatibilních s [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html)  |
| Napájení                                                    | 4 předdefinované jednotky napájení (PSUs) sdílené v obou uzlech zařízení <br> vykreslování typických mocnin 1 200 w|
| Kapacita úložiště                                        | ~ 1 – PB RAW, 70 disků o 14 TB <br> 770 až TB použitelné kapacity|
| Počet uzlů                                          | 2 nezávislé uzly na zařízení (každý 500 TB) |
| Síťová rozhraní na uzel                             | 4 síťová rozhraní na uzel <br><br> SPRÁVA, DATA3 <ul><li> rozhraní 2 X 1 – GbE </li><li> Správa je určena pro správu a počáteční nastavení, není uživatelsky konfigurovatelné. </li><li> DATA3 je ve výchozím nastavení uživatelsky konfigurovatelné a protokol DHCP (Dynamic Host Configuration Protocol).</li></ul>Datová rozhraní DATA1, DATA2 <ul><li>rozhraní 2 X 40-GbE </li><li> Uživatelsky konfigurovatelné pro DHCP (výchozí) nebo static</li></ul>|


## <a name="components"></a>Komponenty

Součástí služby Data Box Heavy jsou následující komponenty:

* **Zařízení Data Box Heavy** – fyzické zařízení s robustním obalem, které udrží data v bezpečí. Toto zařízení má použitelnou kapacitu úložiště 770 TB.
    
* **Služba Data Box** – rozšíření webu Azure Portal, které vám umožní spravovat zařízení Data Box Heavy z webového rozhraní, ke kterému můžete získat přístup z různých geografických umístění. Ke správě zařízení Data Box Heavy použijte službu Data Box. Mezi úlohy služby patří postup vytvoření a správy objednávek, zobrazení a správa výstrah a správa sdílených složek.  

* **Místní webové uživatelské rozhraní** – uživatelské rozhraní na webovém základu, které se používá ke konfiguraci zařízení, aby se mohlo připojit k místní síti a potom pomocí služby Data Box zaregistrovat zařízení. Místní webové uživatelské rozhraní můžete také použít k vypnutí nebo restartování zařízení, zobrazení protokolů kopírování a kontaktování podpory Microsoftu, aby vytvořila žádost o služby.


## <a name="the-workflow"></a>Pracovní postup

Typický postup zahrnuje tyto kroky:

1. **Objednávka** – na portálu Azure Portal vytvoříte objednávku, zadáte doručovací údaje a cílový účet úložiště Azure pro svoje data. Pokud je zařízení k dispozici, připraví se v Azure a odešle se s identifikačním číslem pro sledování zásilky.

2. **Přijetí** – jakmile zařízení obdržíte, připojte ho pomocí přiložených kabelů k síti a napájení. Zařízení zapněte a připojte se k němu. Nakonfigurujte síť zařízení a připojte sdílené složky hostitelského počítače, ze kterého chcete kopírovat data.

3. **Kopírování dat** – zkopírujte data do sdílených složek služby Data Box Heavy.

4. **Vrácení** – zařízení připravte, vypněte a odešlete zpět do datacentra Azure.

5. **Nahrání** – data ze zařízení se automaticky zkopírují do Azure. Disky zařízení se bezpečně vymažou podle pokynů organizace NIST (National Institute of Standards and Technology).

V průběhu tohoto procesu budete upozorněni e-mailem na všechny změny stavu.

## <a name="region-availability"></a>Dostupnost v oblastech

Data Box Heavy může přenášet data na základě oblasti, ve které je služba nasazená, země/oblasti, do které se zařízení dodává, a cílového účtu Azure Storage, kam data přenášíte.

- **Dostupnost služby** – u této vydané verze je služba Data Box Heavy dostupná v následujících oblastech:
    - Všechny oblasti veřejného cloudu ve Spojených státech amerických – USA – středozápad, USA – západ 2, USA – západ, USA – středojih, USA – střed, USA – středosever, USA – východ a USA – východ 2
    - Evropská unie: západní a severní Evropa
    - Spojené království: Velká Británie – jih a Velká Británie – západ
    - Francie: Francie – střed a Francie – jih

- **Cílové účty úložiště** – účty úložiště, do kterých se ukládají data, jsou dostupné ve všech oblastech Azure, ve kterých je tato služba k dispozici.

Nejaktuálnější informace o dostupnosti oblastí pro Data Box Heavy najdete v části [produkty Azure podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

## <a name="sign-up"></a>Registrace

Pokud se chcete zaregistrovat Data Box Heavy, proveďte následující kroky:

1. [Přihlaste se k Azure Portal](https://portal.azure.com).
2. Kliknutím na **+ vytvořit prostředek** vytvořte nový prostředek. Vyhledejte **Azure Data Box**. Službu **Azure Data Box** vyberte.
3. Klikněte na **Vytvořit**.
4. Vyberte předplatné, které chcete použít pro Data Box Heavy. Vyberte oblast, ve které chcete prostředek Data Box Heavy nasadit. U možnosti **Data Box Heavy** klikněte na **Zaregistrovat se**.
5. Odpovězte na otázky týkající se země/oblasti pobytu dat, časového rámce, cílové služby Azure pro přenos dat, šířku pásma sítě a četnosti přenosu dat. Projděte si ochranu osobních údajů a podmínky a zaškrtněte políčko, že Microsoft může použít vaši e-mailovou adresu, aby vás kontaktoval.

Po registraci můžete objednat Data Box Heavy.

    
