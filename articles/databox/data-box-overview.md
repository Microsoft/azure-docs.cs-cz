---
title: Microsoft Azure Data Box – přehled | Microsoft Docs v datech
description: Tento článek popisuje službu Azure Data Box, což je cloudové řešení, které umožňuje přenášet obrovské objemy dat do Azure.
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: 13c07da7393c70bb1fc61930dd1179994c68ed75
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389803"
---
# <a name="what-is-azure-data-box"></a>Co je Azure Data Box?

Cloudové řešení Microsoft Azure Data Box umožňuje posílat terabajty dat do a z Azure rychlým, levným a spolehlivým způsobem. Zabezpečený a rychlý přenos dat se zajišťuje zasláním speciálního zařízení úložiště Data Box. Každé zařízení úložiště má maximální využitelnou kapacitu úložiště 80 TB a do datového centra ho transportuje místní přepravce. Zařízení má robustní obal, který data během převozu chrání a zabezpečí.

Zařízení Data Box můžete objednat prostřednictvím Azure Portal pro import nebo export dat z Azure. Jakmile vám zařízení přijde, můžete ho rychle nastavit. Použijete k tomu místní webové uživatelské rozhraní. V závislosti na tom, jestli budete importovat nebo exportovat data, zkopírujte data ze serverů do zařízení nebo ze zařízení na vaše servery a odešlete zařízení zpátky do Azure. Pokud importujete data do Azure, v datacentru Azure se vaše data automaticky nahrají ze zařízení do Azure. Celý proces se od začátku do konce sleduje ve službě Data Box na webu Azure Portal.

## <a name="use-cases"></a>Případy použití

Data Box je ideální k přenosu dat o objemu větším než 40 TB v případech s omezeným nebo žádným síťovým připojením. Data můžete přesunout jednorázově, přesunovat je pravidelně nebo provést počáteční hromadný přesun a posléze data přenášet pravidelně. 

Tady je několik scénářů, ve kterých můžete Data Box použít k importu dat do Azure.

 - **Jednorázová migrace** – při tomto typu migrace se do Azure přesune velký objem místních dat. 
     - Přesun knihovny médií z offline pásek do Azure za účelem vytvoření online knihovny médií
     - Migrace farmy virtuálních počítačů, SQL serveru a aplikací do Azure
     - Přesun historických dat do Azure za účelem hloubkové analýzy a vytváření sestav pomocí HDInsight

 - **Počáteční hromadný přesun** – při tomto typu migrace provedete počáteční hromadný přesun pomocí služby Data Box (kompletní) a přírůstkové přenosy potom provádíte po síti. 
     - Například partneři řešení zálohování, jako je Commvault a Data Box, se používají k přesunu počátečních velkých historických záloh do Azure. Poté se už přírůstková data přenáší prostřednictvím sítě do Azure Storage.

- **Pravidelné nahrávání** – provádí se v případech, kdy pravidelně dochází ke generování velkých objemů dat, která je nutná přesunout do Azure. Příkladem může být výzkum energie, kdy se u vrtných plošin a soustavy větrných generátorů generuje videoobsah. 

Tady jsou různé scénáře, ve kterých můžete Data Box použít k exportu dat z Azure.

- **Zotavení po havárii** – při obnovení kopie dat z Azure do místní sítě. V typickém scénáři zotavení po havárii se do Data Box exportují velké množství dat Azure. Společnost Microsoft pak doplní tyto Data Box a data se v místním prostředí obnoví v krátkém čase.

- **Požadavky na zabezpečení** – Pokud potřebujete být schopni exportovat data z Azure z důvodu požadavků na státní správu nebo zabezpečení. Například Azure Storage je k dispozici v tajných a hlavních tajných cloudech a pomocí Data Box můžete exportovat data mimo Azure. 

- **Migrace zpátky do místního prostředí nebo jiného poskytovatele cloudové služby** – Pokud chcete přesunout všechna data zpátky do místního prostředí nebo do jiného poskytovatele cloudové služby, exportujte data prostřednictvím data box k migraci úloh.


## <a name="benefits"></a>Výhody

Data Box slouží k přesunu velkých objemů dat do Azure bez výraznějšího dopadu na síť. Toto řešení má následující výhody:

- **Speed** -data box využívá síťová rozhraní s rychlostí 1 GB/s nebo 10 GB pro přesun až 80 TB dat do a z Azure.

- **Zabezpečení** – Data Box má integrovanou ochranu zařízení, dat a samotné služby.
  - Zařízení má robustní obal, který zabezpečují šroubky odolné vůči neoprávněné manipulaci a nálepky odhalující manipulaci. 
  - Data na zařízení nepřetržitě chrání 256bitové šifrování AES.
  - Zařízení je možné odemknout pouze heslem, které najdete na webu Azure Portal.
  - Služba samotná je chráněna funkcemi zabezpečení Azure.
  - Po nahrání dat z vašeho pořadí importu do Azure se disky v zařízení vymažou vyčistit v souladu se standardy NIST 800-88r1. V případě objednávky exportu se disky vymažou, jakmile zařízení dosáhne datacentra Azure.
    
    Další informace najdete v článku o [zabezpečení a ochraně dat ve službě Azure Data Box](data-box-security.md).

## <a name="features-and-specifications"></a>Funkce a specifikace

Zařízení Data Box má v této verzi následující funkce.

| Specifikace                                          | Description              |
|---------------------------------------------------------|--------------------------|
| Hmotnost                                                  | < 23 kg                |
| Dimenze                                              | Zařízení – šířka: 309,0 mm, výška: 430,4 mm, hloubka: 502.0 mm |            
| Místo v racku                                              | 7U při umístění do racku na boku (montáž do racku není možná)|
| Potřebné kabely                                         | 1 napájecí kabel (je součástí balení) <br> 2 kabely RJ45 <br> 2 měděné kabely SFP+ Twinax|
| Kapacita úložiště                                        | 100-TB zařízení po ochraně RAID 5 má 80 TB nebo použitelnou kapacitu|
| Hodnocení výkonu                                            | Jednotka zdroje napájení je ohodnocena za 700 W. <br> Jednotka obvykle vykresluje 375 W.|
| Síťová rozhraní                                      | 2 X 1-GbE rozhraní – Správa, DATA 3. <br> MGMT – pro správu, nemůžou konfigurovat uživatelé, používá se pro počáteční instalaci <br> DATA3 – pro data, můžou konfigurovat uživatelé, výchozí je dynamické nastavení <br> MGMT a DATA 3 fungují také jako 10 GbE <br> rozhraní 2 X 10 GbE – DATA 1, DATA 2 <br> Oboje slouží pro data, dají se konfigurovat jako dynamická (výchozí) nebo statická |
| Přenos dat                                      | Podporuje se import i export.  |
| Média pro přenos dat                                     | RJ45, měděný kabel SFP+ 10 GbE Ethernet  |
| Zabezpečení                                                | Robustní obal zařízení se šroubky odolnými vůči neoprávněné manipulaci <br> Nálepky odhalující manipulaci na spodní straně zařízení|
| Rychlost přenosu dat                                      | Až 80 TB za den přes síťové rozhraní 10 GbE        |
| Správa                                              | Místní webové uživatelské rozhraní – jednorázové počáteční nastavení a konfigurace <br> Azure Portal – každodenní správa zařízení        |

## <a name="data-box-components"></a>Součásti Data Boxu

Data Box má následující součásti:

* **Data box zařízení** – fyzické zařízení, které poskytuje primární úložiště, spravuje komunikaci s cloudovým úložištěm a pomáhá zajistit zabezpečení a důvěrnost všech dat uložených v zařízení. Použitelná kapacita zařízení Data Box je 80 TB. 

    ![Přední a zadní plocha Data Boxu](media/data-box-overview/data-box-combined.png)

    
* **Služba Data Box** – rozšíření webu Azure Portal, které vám umožní spravovat zařízení Data Box z webového rozhraní, ke kterému můžete získat přístup z různých geografických umístění. Pomocí služby Data Box můžete zařízení Data Box spravovat každodenně. Mezi úlohy služby patří postup vytvoření a správy objednávek, zobrazení a správa výstrah a správa sdílených složek.  

    ![Služba Data Box na webu Azure Portal](media/data-box-overview/data-box-service.png)

    Další informace najdete v tématu [Použití služby Data Box ke správě vašeho zařízení Data Box](data-box-portal-ui-admin.md).

* **Místní webové uživatelské rozhraní** – uživatelské rozhraní na webovém základu, které se používá ke konfiguraci zařízení, aby se mohlo připojit k místní síti a potom pomocí služby Data Box zaregistrovat zařízení. Místní webové uživatelské rozhraní můžete také použít k vypnutí nebo restartování zařízení Data Box, zobrazení protokolů kopírování a kontaktování podpory Microsoftu, aby vytvořila žádost o služby.

    ![Místní webové uživatelské rozhraní Data Boxu](media/data-box-overview/data-box-local-web-ui.png)

    Informace o používání uživatelského rozhraní založeného na webu najdete v tématu [Použití uživatelského rozhraní založeného na webu ke správě vašeho zařízení Data Box](data-box-portal-ui-admin.md).

## <a name="the-workflow"></a>Pracovní postup

Typický tok importu zahrnuje následující kroky:

1. **Objednávka** – na portálu Azure Portal vytvoříte objednávku, zadáte doručovací údaje a cílový účet úložiště Azure pro svoje data. Pokud je zařízení k dispozici, připraví se v Azure a odešle se s identifikačním číslem pro sledování zásilky.

2. **Přijetí** – jakmile zařízení obdržíte, připojte ho pomocí přiložených kabelů k síti a napájení. Zařízení zapněte a připojte se k němu. Nakonfigurujte síť zařízení a připojte sdílené složky hostitelského počítače, ze kterého chcete kopírovat data.

3. **Kopírování dat** – zkopírujte data do sdílených složek služby Data Box.

4. **Vrácení** – zařízení připravte, vypněte a odešlete zpět do datacentra Azure.

5. **Nahrání** – data ze zařízení se automaticky zkopírují do Azure. Disky zařízení se bezpečně vymažou podle pokynů organizace NIST (National Institute of Standards and Technology).

Během tohoto procesu budete e-mailem dostávat oznámení o všech změnách stavu. Další informace o podrobném postupu najdete v článku o [nasazení služby Data Box na webu Azure Portal](data-box-deploy-ordered.md).


Typický tok exportu zahrnuje následující kroky:

1. **Objednávka** – vytvořte v Azure Portal objednávku exportu, zadejte informace o expedici a zdrojový účet úložiště Azure pro vaše data. Pokud je zařízení k dispozici, Azure připraví zařízení. Data se zkopírují z účtu Azure Storage do Data Box. Po dokončení kopírování dat Microsoft dodá zařízení s ID sledování dodávky.

2. **Přijetí** – jakmile zařízení obdržíte, připojte ho pomocí přiložených kabelů k síti a napájení. Zařízení zapněte a připojte se k němu. Na hostitelském počítači, do kterého chcete kopírovat data, nakonfigurujte síť zařízení a sdílené složky připojení.

3. **Kopírování dat** – kopírování dat z data box sdílených složek na místní datové servery.

4. **Vrácení** – zařízení připravte, vypněte a odešlete zpět do datacentra Azure.

5. **Vymazání dat** – disky zařízení se bezpečně vymažou podle pokynů National Institute of Standards and Technology (NIST).

Během procesu exportu budete upozorněni e-mailem na všechny změny stavu. Další informace o podrobném postupu najdete v článku o [nasazení služby Data Box na webu Azure Portal](data-box-deploy-export-ordered.md).

## <a name="region-availability"></a>Dostupnost v oblastech

Data Box může přenášet data v závislosti na oblasti, ve které je služba nasazená, zemi nebo oblasti, do které zařízení dodáváte, a cílový účet úložiště Azure, do kterého data přenášíte.

### <a name="for-import"></a>Pro import

- **Dostupnost služby** – při použití data box k importu nebo exportu objednávek můžete získat informace o dostupnosti oblastí, přejít na [produkty Azure dostupné v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

    Pro příkazy import je možné Data Box nasadit také v cloudu Azure Government. Další informace najdete v tématu [co je Azure Government?](../azure-government/documentation-government-welcome.md). 

- **Cílové účty úložiště** – účty úložiště, které ukládají data, jsou dostupné ve všech oblastech Azure, kde je služba dostupná.


## <a name="next-steps"></a>Další kroky

- Přečtěte si [systémové požadavky služby Data Box](data-box-system-requirements.md).
- Seznamte se s [omezeními služby Data Box](data-box-limits.md).
- Rychle nasaďte [Azure Data Box](data-box-quickstart-portal.md) na webu Azure Portal.
