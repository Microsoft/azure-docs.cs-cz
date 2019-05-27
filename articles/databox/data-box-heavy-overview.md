---
title: Microsoft Azure Data Box Heavy – přehled | Microsoft Docs
description: Tento článek popisuje službu Azure Data Box Heavy, což je hybridní řešení, které umožňuje přenášet obrovské objemy dat do Azure.
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 05/20/2019
ms.author: alkohli
ms.openlocfilehash: 0f71d9b4400041db50cb3e24940e922acde55edc
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991688"
---
# <a name="what-is-azure-data-box-heavy-preview"></a>Co je Azure Data Box Heavy? (Preview)

Azure Data Box náročné umožňuje odeslat stovky terabajtů dat do Azure v rámci rychlého, cenově dostupné a spolehlivě. Data se přenesou do Azure přenosem dat pole náročné zařízení s kapacitou 1 PB úložiště, který vyplnit s vašimi daty a zasílají zpět společnosti Microsoft. Zařízení má robustní malých a velkých písmen k ochraně a zabezpečení vašich dat během přenosu.

Data Box velké je aktuálně ve verzi preview. Zaregistrujte se na vyžádání pro zařízení pomocí webu Azure portal. Po přijetí zařízení je ve vašem datovém centru, nastavte pomocí místního webového uživatelského rozhraní. Zkopírujte data ze serverů do zařízení a pak ho pošlete zpět do Azure. V datovém centru Azure vaše data nahrávají do vašich účtech úložiště Azure. Můžete sledovat celý proces začátku do konce na webu Azure Portal.


> [!IMPORTANT]
> - Data Box Heavy je ve verzi Preview. Před nasazením tohoto řešení si přečtěte [podmínky užívání pro předběžné verze platformy Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> - Pokud chcete o zařízení požádat, přihlaste se k webu [Azure Portal Preview](https://aka.ms/azuredatabox).
> - Po dobu platnosti verze Preview je možné zařízení poslat zákazníkům služby Data Box Heavy v USA a Evropské unii. Další informace najdete v části [Regionální dostupnost](#region-availability).

## <a name="use-cases"></a>Případy použití

Data Box náročné je nejvhodnější pro velikosti dat v stovky terabajtů, kde není dostatečná k odesílání dat do Azure připojení k síti. Data můžete přesunout jednorázově, přesunovat je pravidelně nebo provést počáteční hromadný přesun a posléze data přenášet pravidelně. Zde je několik různých scénářů, ve kterých je vhodné k přenosu dat použít Data Box Heavy.

 - **Jednorázová migrace** – při tomto typu migrace se do Azure přesune velký objem místních dat.
     - Přesuňte knihovnu médií z offline pásky do Azure za účelem vytvoření média online knihovny.
     - Migrace farmy virtuálních počítačů, SQL server a aplikací do Azure.
     - Přesuňte historická data do Azure pro hloubkovou analýzu a sestavy pomocí HDInsight.

 - **Počáteční hromadný přesun** – při tomto typu migrace provedete počáteční hromadný přesun pomocí služby Data Box Heavy (kompletní) a přírůstkové přenosy potom provádíte po síti.
     - Data Box zátěži a partnerských řešení zálohování se používají k prvotní zálohování velkých Historický přesunout do Azure. Poté se už přírůstková data přenáší prostřednictvím sítě do Azure Storage.

 - **Pravidelné nahrávání** – provádí se v případech, kdy pravidelně dochází ke generování velkých objemů dat, která je nutná přesunout do Azure. Příkladem může být výzkum energie, kdy se u vrtných plošin a soustavy větrných generátorů generuje videoobsah.

## <a name="benefits"></a>Výhody

Data Box náročné je navržená pro přesun velkých objemů dat do Azure s minimem žádný vliv ve vaší síti. Toto řešení má následující výhody:

- **Rychlost** -Data Box náročné využívá vysoce výkonné 40 GB/s – síťová rozhraní.

- **Zabezpečení** -Data Box náročné má integrované bezpečnostní ochrany pro zařízení, data a služby.
    - Zařízení má robustní obal, který zabezpečují šroubky odolné vůči neoprávněné manipulaci a nálepky odhalující manipulaci.
    - Data na zařízení jsou neustále chráněna pomocí 256bitového šifrování AES.
    - Zařízení je možné odemknout pouze heslem, které najdete na webu Azure Portal.
    - Služba samotná je chráněna funkcemi zabezpečení Azure.
    - Po nahrání dat do Azure disky v zařízení vymažou vyčištění, v souladu se standardy 800 88r1 National Institute of Standards and Technology (NIST).


## <a name="features-and-specifications"></a>Funkce a specifikace

Data Box náročné zařízení má následující funkce v této verzi.

| Specifikace                                          | Popis              |
|---------------------------------------------------------|--------------------------|
| Váha                                                  | ~ 500 lbs.                |
| Dimenze                                              | Width: 26 palců Height: 28 palců délka: 48 palců |
| Místo v racku                                              | Nemůže být skříňová|
| Potřebné kabely                                         | 4 X založený 120 V/10 A napájecích kabelů (NEMA 5 – 15) zahrnuté <br> Podporuje až 240 V power a má C-13 power zásobníků <br> Použití síťové kabely, které jsou kompatibilní s [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html)  |
|Napájení                                                    | 4 integrované power dodavatelského jednotky (PSUs) sdíleny napříč uzly zařízení|
| Kapacita úložiště                                        | ~ 1 PB nezpracovaná, 70 disků 14 TB <br> 770 TB využitelné kapacity|
|Počet uzlů                                          | 2 uzly nezávislé na zařízení (500 TB) |
| Síťová rozhraní za uzel                             | 4 síťových rozhraní na jeden uzel <br> MGMT DATA3 <ul><li> 2 × 1 GbE rozhraní </li><li> MGMT je pro správu, není konfigurovatelná uživatelem, použít pro počáteční instalaci </li><li> DATA3 je uživatelem konfigurovatelné data rozhraní, DHCP Dynamic Host Configuration Protocol () ve výchozím nastavení</li><li>1 GbE síťová rozhraní můžete také nakonfigurovat jako rozhraní 10 GbE</li></ul>Data1 DATA2 datová rozhraní <ul><li>2 x 40 10GbE rozhraní </li><li> DHCP pomocí výchozího nebo statické, uživatelská rozhraní konfigurovatelná data</li>|


## <a name="components"></a>Komponenty

Součástí služby Data Box Heavy jsou následující komponenty:

* **Zařízení Data Box Heavy** – fyzické zařízení s robustním obalem, které udrží data v bezpečí. Toto zařízení má kapacitu úložiště použitelné 770 TB.
    
* **Služba Data Box** – rozšíření webu Azure Portal, které vám umožní spravovat zařízení Data Box Heavy z webového rozhraní, ke kterému můžete získat přístup z různých geografických umístění. Pomocí služby Data Box ke správě zařízení Data Box náročné. Mezi úlohy služby patří postup vytvoření a správy objednávek, zobrazení a správa výstrah a správa sdílených složek.  

* **Místní webové uživatelské rozhraní** – uživatelské rozhraní na webovém základu, které se používá ke konfiguraci zařízení, aby se mohlo připojit k místní síti a potom pomocí služby Data Box zaregistrovat zařízení. Místní webové uživatelské rozhraní můžete také použít k vypnutí nebo restartování zařízení, zobrazení protokolů kopírování a kontaktování podpory Microsoftu, aby vytvořila žádost o služby.


## <a name="the-workflow"></a>Pracovní postup

Typický postup zahrnuje tyto kroky:

1. **Objednávka** – na portálu Azure Portal vytvoříte objednávku, zadáte doručovací údaje a cílový účet úložiště Azure pro svoje data. Pokud je zařízení k dispozici, připraví se v Azure a odešle se s identifikačním číslem pro sledování zásilky.

2. **Přijetí** – jakmile zařízení obdržíte, připojte ho pomocí přiložených kabelů k síti a napájení. Zařízení zapněte a připojte se k němu. Nakonfigurujte síť zařízení a připojte sdílené složky hostitelského počítače, ze kterého chcete data kopírovat.

3. **Kopírování dat** – zkopírujte data do sdílených složek služby Data Box Heavy.

4. **Vrácení** – zařízení připravte, vypněte a odešlete zpět do datacentra Azure.

5. **Nahrání** – data ze zařízení se automaticky zkopírují do Azure. Disky zařízení se bezpečně vymažou podle pokynů organizace NIST (National Institute of Standards and Technology).

Během tohoto procesu se upozornění prostřednictvím e-mailu na všechny změny stavu.

## <a name="region-availability"></a>Dostupnost v oblastech

Data Box náročné se můžou přenášet data podle oblasti, ve kterém je služba nasazená, země/oblast, ke kterému zařízení se posílá a cílovým účtem úložiště Azure, ve kterém můžete přenést data.

- **Dostupnost služby** – u této vydané verze je služba Data Box Heavy dostupná v následujících oblastech:
    - Všechny oblasti veřejného cloudu ve Spojených státech amerických – USA – středozápad, Západní USA 2, Západní USA, USA (střed) – jih, USA – střed, USA (střed) – sever, USA – východ a USA – východ 2
    - Evropská unie – západní a severní Evropa
    - Spojené království: Velká Británie – jih a Velká Británie – západ
    - Francie: Francie – střed a Francie – jih

- **Cílové účty úložiště** – účty úložiště, do kterých se ukládají data, jsou dostupné ve všech oblastech Azure, ve kterých je tato služba k dispozici.

Nejnovější informace o dostupnosti oblast pro náročné pole dat, přejděte na [produkty Azure podle oblastí](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

## <a name="sign-up"></a>Registrace

Služba Data Box Heavy je ve verzi Preview a musíte se k ní zaregistrovat. Proveďte následující kroky se zaregistrovat do služby Data Box náročné:

1. Přihlaste se k webu Azure Portal na adrese: https://aka.ms/azuredatabox.
2. Klikněte na tlačítko **+ vytvořit prostředek** k vytvoření nového prostředku. Vyhledejte **Azure Data Box**. Službu **Azure Data Box** vyberte.

    <!--![The Data Box Heavy sign up 1]()-->

3. Klikněte na možnost **Vytvořit**.

    <!--![The Data Box Heavy sign up 2]()-->

4. Vyberte předplatné, které chcete použít pro Data Box Heavy ve verzi Preview. Vyberte oblast, ve které chcete prostředek Data Box Heavy nasadit. U možnosti **Data Box Heavy** klikněte na **Zaregistrovat se**.

   <!--![The Data Box Heavy sign up 3]()-->

5. Odpovězte na otázky týkající se dat frekvence přenosu pobytu zemi/oblast, časový rámec, cílové služby Azure pro přenos dat, šířky pásma sítě a data. Projděte si ochranu osobních údajů a podmínky a zaškrtněte políčko, že Microsoft může použít vaši e-mailovou adresu, aby vás kontaktoval.

    <!--![The Data Box Heavy sign up 4]()-->

Jakmile už zaregistrovali a povolit pro verzi preview, můžete si objednat velkým pole Data.

    