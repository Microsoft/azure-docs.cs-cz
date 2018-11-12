---
title: Microsoft Azure Data Box Heavy – přehled | Microsoft Docs
description: Tento článek popisuje službu Azure Data Box Heavy, což je hybridní řešení, které umožňuje přenášet obrovské objemy dat do Azure.
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: ''
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 0a5b7f93f9ac6cc5b1076881727a42fd5b95ff4b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235152"
---
# <a name="what-is-azure-data-box-heavy-preview"></a>Co je Azure Data Box Heavy? (Preview)

Hybridní řešení Microsoft Azure Data Box umožňuje do Azure odeslat rychle, levně a bezpečně stovky terabajtů dat. Rychlý a zabezpečený přenos dat se zajišťuje zasláním chráněného úložiště o velikosti 1 PB prostřednictvím přepravní společnosti. Zařízení má robustní obal, který data během převozu chrání a zabezpečuje.

Azure Data Box Heavy je aktuálně ve verzi Preview, ale prostřednictvím registrace na webu Azure Portal si můžete o zařízení zažádat. Po přijetí zařízení vaším datacentrem ho můžete nastavit pomocí místního webového uživatelského rozhraní. Zkopírujte data ze serverů do zařízení a zašlete ho zpět do Azure. V datacentru Azure se vaše data nahrají ze zařízení do Azure automaticky. Celý proces se na webu Azure Portal sleduje od začátku do konce službou Data Box.


> [!IMPORTANT]
> - Data Box Heavy je ve verzi Preview. Před nasazením tohoto řešení si přečtěte [podmínky užívání pro předběžné verze platformy Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
> - Pokud chcete o zařízení požádat, přihlaste se k webu [Azure Portal Preview](https://aka.ms/).
> - Po dobu platnosti verze Preview je možné zařízení poslat zákazníkům služby Data Box Heavy v USA a Evropské unii. Další informace najdete v části [Regionální dostupnost](#region-availability).

## <a name="use-cases"></a>Případy použití

Data Box Heavy je ideální k přenosu dat o objemu větším než 500 TB v případech s omezeným nebo žádným síťovým připojením. Data můžete přesunout jednorázově, přesunovat je pravidelně nebo provést počáteční hromadný přesun a posléze data přenášet pravidelně. Zde je několik různých scénářů, ve kterých je vhodné k přenosu dat použít Data Box Heavy.

 - **Jednorázová migrace** – při tomto typu migrace se do Azure přesune velký objem místních dat. 
     - Přesun knihovny médií z offline pásek do Azure za účelem vytvoření online knihovny médií
     - Migrace farmy virtuálních počítačů, SQL serveru a aplikací do Azure
     - Přesun historických dat do Azure za účelem hloubkové analýzy a vytváření sestav pomocí HDInsight

 - **Počáteční hromadný přesun** – při tomto typu migrace provedete počáteční hromadný přesun pomocí služby Data Box Heavy (kompletní) a přírůstkové přenosy potom provádíte po síti. 
     - Například partneři řešení zálohování, jako je Commvault a Data Box Heavy, se používají k přesunu počátečních velkých historických záloh do Azure. Poté se už přírůstková data přenáší prostřednictvím sítě do Azure Storage.

 - **Pravidelné nahrávání** – provádí se v případech, kdy pravidelně dochází ke generování velkých objemů dat, která je nutná přesunout do Azure. Příkladem může být výzkum energie, kdy se u vrtných plošin a soustavy větrných generátorů generuje videoobsah.      

## <a name="benefits"></a>Výhody

Data Box Heavy slouží k přesunu ohromných objemů dat do Azure s minimálním nebo žádným dopadem na síť. Toto řešení má následující výhody:

- **Rychlost** – Data Box Heavy používá vysoce výkonná síťová rozhraní s rychlostí 40 GB/s.

- **Zabezpečení** – Data Box Heavy má integrovanou ochranu zařízení, dat a samotné služby.
    - Zařízení má robustní obal, který zabezpečují šroubky odolné vůči neoprávněné manipulaci a nálepky odhalující manipulaci. 
    - Data na zařízení jsou neustále chráněna pomocí 256bitového šifrování AES.
    - Zařízení je možné odemknout pouze heslem, které najdete na webu Azure Portal.
    - Služba samotná je chráněna funkcemi zabezpečení Azure.
    - Po nahrání dat do Azure se disky na zařízení zcela vymažou podle standardů NIST 800-88r1.


<!--## Features and specifications

The Data Box Heavy device has the following features in this release.

| Specifications                                          | Description              |
|---------------------------------------------------------|--------------------------|
| Weight                                                  | < 50 lbs.                |
| Dimensions                                              | Device - Width: 309.0 mm Height: 430.4 mm Depth: 502.0 mm |            
| Rack space                                              | 7 U when placed in the rack on its side (cannot be rack-mounted)|
| Cables required                                         | 1 X power cable (included) <br> 2 RJ45 cables <br> 2 X SFP+ Twinax copper cables|
| Storage capacity                                        | 100 TB <br> 80 TB usable capacity after RAID 5 protection|
| Network interfaces                                      | 2 X 1 GbE interface - MGMT, DATA 3. <br> MGMT - for management, not user configurable, used for initial setup <br> DATA3 - for data, user configurable, and is dynamic by default <br> MGMT and DATA 3 can also work as 10 GbE <br> 2 X 10 GbE interface - DATA 1, DATA 2 <br> Both are for data, can be configured as dynamic (default) or static |
| Data transfer media                                     | RJ45, SFP+ copper 10 GbE Ethernet  |
| Security                                                | Rugged device casing with tamper-proof custom screws <br> Tamper-evident stickers placed at the bottom of the device|
| Data transfer rate                                      | Up to 80 TB in a day over 10 GbE network interface        |
| Management                                              | Local web UI - one-time initial setup and configuration <br> Azure portal - day-to-day device management        |-->

## <a name="components"></a>Komponenty

Součástí služby Data Box Heavy jsou následující komponenty:

* **Zařízení Data Box Heavy** – fyzické zařízení s robustním obalem, které udrží data v bezpečí. Použitelná kapacita tohoto zařízení je 800 TB. 

    
* **Služba Data Box** – rozšíření webu Azure Portal, které vám umožní spravovat zařízení Data Box Heavy z webového rozhraní, ke kterému můžete získat přístup z různých geografických umístění. Pomocí služby Data Box můžete zařízení Data Box Heavy spravovat každodenně. Mezi úlohy služby patří postup vytvoření a správy objednávek, zobrazení a správa výstrah a správa sdílených složek.  

* **Místní webové uživatelské rozhraní** – uživatelské rozhraní na webovém základu, které se používá ke konfiguraci zařízení, aby se mohlo připojit k místní síti a potom pomocí služby Data Box zaregistrovat zařízení. Místní webové uživatelské rozhraní můžete také použít k vypnutí nebo restartování zařízení, zobrazení protokolů kopírování a kontaktování podpory Microsoftu, aby vytvořila žádost o služby.


## <a name="the-workflow"></a>Pracovní postup

Typický postup zahrnuje tyto kroky:

1. **Objednávka** – na portálu Azure Portal vytvoříte objednávku, zadáte doručovací údaje a cílový účet úložiště Azure pro svoje data. Pokud je zařízení k dispozici, připraví se v Azure a odešle se s identifikačním číslem pro sledování zásilky.

2. **Přijetí** – jakmile zařízení obdržíte, připojte ho pomocí přiložených kabelů k síti a napájení. Zařízení zapněte a připojte se k němu. Nakonfigurujte síť zařízení a připojte sdílené složky hostitelského počítače, ze kterého chcete data kopírovat.

3. **Kopírování dat** – zkopírujte data do sdílených složek služby Data Box Heavy.

4. **Vrácení** – zařízení připravte, vypněte a odešlete zpět do datacentra Azure.

5. **Nahrání** – data ze zařízení se automaticky zkopírují do Azure. Disky zařízení se bezpečně vymažou podle pokynů organizace NIST (National Institute of Standards and Technology).

Během tohoto procesu budete e-mailem dostávat oznámení o všech změnách stavu. 

## <a name="region-availability"></a>Dostupnost v oblastech

Data Box Heavy může přenést data na základě oblasti, ve které se služba nasazuje, země do které se zařízení odesílá a cíle účtu Azure Storage, do kterého data přenášíte. 

- **Dostupnost služby** – u této vydané verze je služba Data Box Heavy dostupná v následujících oblastech:
    - Všechny oblasti veřejného cloudu ve Spojených státech amerických – USA – středozápad, USA – západ 2, USA – západ, USA (střed) – jih, USA – střed, USA (střed) – sever, USA – východ a USA – východ 2
    - Evropská unie – západní a severní Evropa
    - Spojené království: Velká Británie – jih a Velká Británie – západ
    - Francie: Francie – střed a Francie – jih

- **Cílové účty úložiště** – účty úložiště, do kterých se ukládají data, jsou dostupné ve všech oblastech Azure, ve kterých je tato služba k dispozici. 

## <a name="sign-up"></a>Registrace

Služba Data Box Heavy je ve verzi Preview a musíte se k ní zaregistrovat. Data Box Heavy si zaregistrujete následujícím způsobem:

1. Přihlaste se k webu Azure Portal na adrese: https://aka.ms/azuredatabox.
2. Kliknutím na **+** vytvořte nový prostředek. Vyhledejte **Azure Data Box**. Službu **Azure Data Box** vyberte.

    <!--![The Data Box Heavy sign up 1]()-->

3. Klikněte na možnost **Vytvořit**.

    <!--![The Data Box Heavy sign up 2]()-->

4. Vyberte předplatné, které chcete použít pro Data Box Heavy ve verzi Preview. Vyberte oblast, ve které chcete prostředek Data Box Heavy nasadit. U možnosti **Data Box Heavy** klikněte na **Zaregistrovat se**.

   <!--![The Data Box Heavy sign up 3]()-->

5. Odpovězte na otázky týkající země trvalého umístění dat, časového rámce, cílové služby Azure pro přenos dat, šířky pásma sítě a frekvence přenosu dat. Projděte si ochranu osobních údajů a podmínky a zaškrtněte políčko, že Microsoft může použít vaši e-mailovou adresu, aby vás kontaktoval.

    <!--![The Data Box Heavy sign up 4]()-->

Po registraci a povolení verze Preview si můžete službu Data Box Heavy objednat.




