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
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437772"
---
# <a name="what-is-azure-data-box-heavy"></a>Co je Azure Data Box Heavy?

Azure Data Box Heavy umožňuje posílat stovky terabajtů dat do Azure rychlým, levným a spolehlivým způsobem. Data se přenášejí do Azure tak, že vám pošlete zařízení Data Box Heavy s úložnou kapacitou 1-PB, které vyplníte svými daty a odešlete zpět společnosti Microsoft. Zařízení má robustní pouzdro, které chrání a zabezpečuje vaše data během přepravy.

Jakmile je zařízení přijato ve vašem datovém centru, nastavte ho pomocí místního webového uživatelského rozhraní. Zkopírujte data ze serverů do zařízení a pošlete ho zpět do Azure. V datovém centru Azure se vaše data nahrají do vašich účtů Azure Storage. Celý proces od konce na webu Azure Portal můžete sledovat.


> [!IMPORTANT]
> - Pokud chcete požádat o zařízení, zaregistrujte se na [webu Azure Portal](https://portal.azure.com).


## <a name="use-cases"></a>Případy použití

Data Box Heavy se nejlépe hodí pro velikosti dat ve stovkách terabajtů, kde připojení k síti nestačí k nahrání dat do Azure. Data můžete přesunout jednorázově, přesunovat je pravidelně nebo provést počáteční hromadný přesun a posléze data přenášet pravidelně. Zde je několik různých scénářů, ve kterých je vhodné k přenosu dat použít Data Box Heavy.

 - **Jednorázová migrace** – při tomto typu migrace se do Azure přesune velký objem místních dat.
     - Přesuňte knihovnu médií z offline pásek do Azure a vytvořte online knihovnu médií.
     - Migrujte farmu virtuálních počítačových společností, SQL server a aplikace do Azure.
     - Přesuňte historická data do Azure pro hloubkovou analýzu a sestavu pomocí HDInsight.

 - **Počáteční hromadný přesun** – při tomto typu migrace provedete počáteční hromadný přesun pomocí služby Data Box Heavy (kompletní) a přírůstkové přenosy potom provádíte po síti.
     - Například Data Box Heavy a partner řešení zálohování se používají k přesunutí počáteční velké historické zálohy do Azure. Poté se už přírůstková data přenáší prostřednictvím sítě do Azure Storage.

 - **Pravidelné nahrávání** – provádí se v případech, kdy pravidelně dochází ke generování velkých objemů dat, která je nutná přesunout do Azure. Příkladem může být výzkum energie, kdy se u vrtných plošin a soustavy větrných generátorů generuje videoobsah.

## <a name="benefits"></a>Výhody

Data Box Heavy je navržený tak, aby přesouvá obrovské množství dat do Azure s malým nebo žádným dopadem na vaši síť. Toto řešení má následující výhody:

- **Rychlost** - Data Box Heavy používá vysoce výkonná síťová rozhraní 40 Gb/s.

- **Zabezpečení** – Data Box Heavy má integrovanou bezpečnostní ochranu pro zařízení, data a službu.
    - Zařízení má robustní obal, který zabezpečují šroubky odolné vůči neoprávněné manipulaci a nálepky odhalující manipulaci.
    - Data na zařízení nepřetržitě chrání 256bitové šifrování AES.
    - Zařízení je možné odemknout pouze heslem, které najdete na webu Azure Portal.
    - Služba samotná je chráněna funkcemi zabezpečení Azure.
    - Po nahrání dat do Azure se disky na zařízení vymažou v souladu se standardy 800-88r1 národního institutu pro standardy a technologie (NIST).


## <a name="features-and-specifications"></a>Funkce a specifikace

Zařízení Data Box Heavy má v této verzi následující funkce.

| Specifikace                                          | Popis              |
|---------------------------------------------------------|--------------------------|
| Hmotnost                                                  | ~ 500 liber. <br>Zařízení na zajišťovacích kolech pro přepravu|
| Dimenze                                              | Šířka: 26 palců Výška: 28 palců Délka: 48 palců |
| Místo v racku                                              | Nelze montovat do racku|
| Potřebné kabely                                         | 4 uzemněné napájecí kabely 120 V / 10 A (NEMA 5-15) v ceně <br> Zařízení podporuje výkon až 240 V a má napájecí nádoby C-13 <br> Používejte síťové kabely kompatibilní s [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html)  |
| Napájení                                                    | 4 vestavěné napájecí zdroje (PSU) sdílené mezi oběma uzly zařízení <br> Typický příkon 1 200 wattů|
| Kapacita úložiště                                        | ~ 1-PB raw, 70 disků po 14 TB <br> Využitelná kapacita 770 TB|
| Počet uzlů                                          | 2 nezávislé uzly na zařízení (500 TB každý) |
| Síťová rozhraní na uzel                             | 4 síťová rozhraní na uzel <br><br> MGMT, DATA3 <ul><li> 2 rozhraní X 1 GbE </li><li> MGMT je pro správu a počáteční nastavení, nikoli uživatelsky konfigurovatelné </li><li> DATA3 je uživatelsky konfigurovatelný a dynamický hostitelský konfigurační protokol (DHCP) ve výchozím nastavení</li></ul>DATA1, DATA2 <ul><li>2 rozhraní X 40 GbE </li><li> Uživatelsky konfigurovatelné pro DHCP (výchozí) nebo statické</li></ul>|


## <a name="components"></a>Komponenty

Součástí služby Data Box Heavy jsou následující komponenty:

* **Zařízení Data Box Heavy** – fyzické zařízení s robustním obalem, které udrží data v bezpečí. Toto zařízení má využitelnou úložnou kapacitu 770 TB.
    
* **Služba Data Box** – rozšíření webu Azure Portal, které vám umožní spravovat zařízení Data Box Heavy z webového rozhraní, ke kterému můžete získat přístup z různých geografických umístění. Ke správě zařízení Data Box Heavy použijte službu Data Box. Mezi úlohy služby patří postup vytvoření a správy objednávek, zobrazení a správa výstrah a správa sdílených složek.  

* **Místní webové uživatelské rozhraní** – uživatelské rozhraní na webovém základu, které se používá ke konfiguraci zařízení, aby se mohlo připojit k místní síti a potom pomocí služby Data Box zaregistrovat zařízení. Místní webové uživatelské rozhraní můžete také použít k vypnutí nebo restartování zařízení, zobrazení protokolů kopírování a kontaktování podpory Microsoftu, aby vytvořila žádost o služby.


## <a name="the-workflow"></a>Pracovní postup

Typický postup zahrnuje tyto kroky:

1. **Objednávka** – na portálu Azure Portal vytvoříte objednávku, zadáte doručovací údaje a cílový účet úložiště Azure pro svoje data. Pokud je zařízení k dispozici, připraví se v Azure a odešle se s identifikačním číslem pro sledování zásilky.

2. **Přijetí** – jakmile zařízení obdržíte, připojte ho pomocí přiložených kabelů k síti a napájení. Zařízení zapněte a připojte se k němu. Nakonfigurujte síť zařízení a připojte sdílené složky hostitelského počítače, ze kterého chcete kopírovat data.

3. **Kopírování dat** – zkopírujte data do sdílených složek služby Data Box Heavy.

4. **Vrácení** – zařízení připravte, vypněte a odešlete zpět do datacentra Azure.

5. **Nahrání** – data ze zařízení se automaticky zkopírují do Azure. Disky zařízení se bezpečně vymažou podle pokynů organizace NIST (National Institute of Standards and Technology).

Během tohoto procesu budete e-mailem upozorněni na všechny změny stavu.

## <a name="region-availability"></a>Dostupnost v oblastech

Data Box Heavy může přenášet data na základě oblasti, ve které je služba nasazena, země nebo oblasti, do které je zařízení dodáváno, a cílového účtu úložiště Azure, kde přenášíte data.

- **Dostupnost služby** – u této vydané verze je služba Data Box Heavy dostupná v následujících oblastech:
    - Všechny oblasti veřejného cloudu ve Spojených státech amerických – USA – středozápad, USA – západ 2, USA – západ, USA – středojih, USA – střed, USA – středosever, USA – východ a USA – východ 2
    - Evropská unie: západní a severní Evropa
    - Spojené království: Velká Británie – jih a Velká Británie – západ
    - Francie: Francie – střed a Francie – jih

- **Cílové účty úložiště** – účty úložiště, do kterých se ukládají data, jsou dostupné ve všech oblastech Azure, ve kterých je tato služba k dispozici.

Nejaktuálnější informace o dostupnosti oblasti pro data box heavy najdete v [části Produkty Azure podle oblastí](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

## <a name="sign-up"></a>Registrace

Při registraci do data boxu Heavy postupujte takto:

1. [Přihlaste se k portálu Azure](https://portal.azure.com).
2. Chcete-li vytvořit nový zdroj, klepněte na tlačítko **+ Vytvořit zdroj.** Vyhledejte **Azure Data Box**. Službu **Azure Data Box** vyberte.
3. Klikněte na **Vytvořit**.
4. Vyberte předplatné, které chcete použít pro data box heavy. Vyberte oblast, ve které chcete prostředek Data Box Heavy nasadit. U možnosti **Data Box Heavy** klikněte na **Zaregistrovat se**.
5. Odpovězte na otázky týkající se země nebo oblasti rezidence dat, časového rámce, cílové služby Azure pro přenos dat, šířky pásma sítě a frekvence přenosu dat. Projděte si ochranu osobních údajů a podmínky a zaškrtněte políčko, že Microsoft může použít vaši e-mailovou adresu, aby vás kontaktoval.

Jakmile se zaregistrujete, můžete si objednat datovou schránku Heavy.

    
