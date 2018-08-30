---
title: Microsoft Azure Data Box Disk – přehled | Microsoft Docs
description: Tento článek popisuje službu Azure Data Box Disk, což je cloudové řešení, které umožňuje přenášet velké objemy dat do Azure.
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/28/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand what Data Box Disk is and how it works so I can use it to import on-premises data into Azure.
ms.openlocfilehash: f2e1a59584507b52d0a834af1869e3e9f2db5f07
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127360"
---
# <a name="what-is-azure-data-box-disk-preview"></a>Co je Azure Data Box Disk? (Preview)

Řešení Microsoft Azure Data Box Disk umožňuje odesílat do Azure rychle, levně a bezpečně terabajty místních dat. Rychlejší bezpečný přenos dat je zajišťován pomocí 1 až 5 disků SSD (Solid-State Disk), které dostanete. Tyto 8TB šifrované disky jsou odeslány do vašeho datacentra prostřednictvím místní přepravní služby. 

Tyto disky můžete pomocí služby Data Box na portálu Azure Portal rychle nakonfigurovat, připojit a odemknout. Data zkopírujete na disky a ty pošlete zpět do Azure. V datacentru Azure se vaše data automaticky nahrají z disků do cloudu pomocí rychlého nahrávacího propojení privátní sítě.


> [!IMPORTANT]
> - Data Box Disk je ve verzi Preview. Před nasazením tohoto řešení si přečtěte [podmínky užívání pro předběžné verze platformy Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
> - V této službě se musíte zaregistrovat. Pokud se chcete zaregistrovat, přejděte na [portál Preview](http://aka.ms/azuredataboxfromdiskdocs).
> - Po dobu platnosti verze Preview je možné disky poslat zákazníkům služby Data Box Disk v USA a Evropské unii. Další informace najdete v části [Regionální dostupnost](#region-availability).

## <a name="use-cases"></a>Případy použití

Prostřednictvím služby Data Box Disk můžete přenést terabajty dat i v případě, že máte omezené připojení k síti. Data můžete přesunout jednorázově, přesunovat je pravidelně nebo provést počáteční hromadný přesun a posléze data přenášet pravidelně. 

- **Jednorázová migrace** – při tomto typu migrace se do Azure přesune velký objem místních dat. Příkladem může být přesun dat z offline pásek za účelem archivace dat ve studeném úložišti Azure.
- **Přírůstkový přenos** – při tomto typu migrace provedete počáteční hromadný přesun pomocí služby Data Box Disk (kompletní) a potom provádíte přírůstkové přenosy přes síť. Pro přesun záložních kopií do Azure se například využívají Commvault a Data Box Disk. Po této migraci následuje kopírování přírůstkových dat přes síť do úložiště Azure. 
- **Pravidelné nahrávání** – provádí se v případech, kdy pravidelně dochází ke generování velkých objemů dat, která je nutná přesunout do Azure. Příkladem může být výzkum energie, kdy se u vrtných plošin a soustavy větrných generátorů generuje videoobsah.

## <a name="the-workflow"></a>Pracovní postup

Typický postup zahrnuje tyto kroky:

1. **Objednávka** – na portálu Azure Portal vytvoříte objednávku, zadáte doručovací údaje a cílový účet úložiště Azure pro svoje data. Pokud jsou disky k dispozici, Azure zašifruje, připraví a odešle disky s identifikačním číslem pro sledování zásilky.

2. **Příjem** – po doručení disky vybalíte a připojíte k počítači s daty, která chcete zkopírovat. Odemknete disky.
    
3. **Kopírování dat** – příslušná data přetažením zkopírujete na disky.

4. **Vrácení** – připravíte a odešlete disky zpět do datacentra Azure.

5. **Nahrání** – data se automaticky zkopírují z disků do Azure. Disky jsou bezpečně vymazány podle pokynů organizace NIST (National Institute of Standards and Technology).

Během tohoto procesu budete e-mailem dostávat oznámení o všech změnách stavu. Další informace o podrobném postupu najdete v článku o [nasazení služby Data Box Disk na portálu Azure Portal](data-box-disk-quickstart-portal.md).


## <a name="benefits"></a>Výhody

Data Box Disk slouží k přesunu velkých objemů dat do Azure bez sebemenšího dopadu na síť. Toto řešení má následující výhody:

- **Rychlost** – služba Data Box Disk používá připojení USB 3.0 a je tak schopná do Azure přesunout až 35 TB dat za dobu kratší než týden.   

- **Snadné použití** – služba Data Box je snadno použitelné řešení.

    - Disky využívají připojení USB a jejich nastavení nezabere téměř žádný čas.
    - Disky jsou malé, a proto se s nimi snadno manipuluje.
    - Disky nemají žádné požadavky na externí napájení.
    - Disky je možné používat se serverem datacentra, desktopem i laptopem.
    - Řešení poskytuje sledování od začátku až do konce prostřednictvím portálu Azure Portal.    

- **Zabezpečení** – Data Box Disk má integrovanou ochranu disků, dat a samotné služby. 
    - Disky jsou odolné vůči neoprávněné manipulaci a podporují možnosti zabezpečené aktualizace. 
    - Data na discích jsou neustále chráněna pomocí 128bitového šifrování AES. 
    - Disky je možné odemknout pouze klíčem, který najdete na portálu Azure Portal. 
    - Služba samotná je chráněna funkcemi zabezpečení Azure. 
    - Po nahrání dat do Azure se disky zcela vymažou podle standardů NIST 800-88r1.  
    
Další informace najdete v článku, který se věnuje [zabezpečení a ochraně dat ve službě Data Box Disk](data-box-disk-security.md).


## <a name="features-and-specifications"></a>Funkce a specifikace


| Specifikace                                          | Popis              |
|---------------------------------------------------------|--------------------------|
| Hmotnost                                                  | < 1 kg na krabici. V krabici může být až 5 disků.                |
| Dimenze                                              | Disk – 2,5“ SSD |            
| Kabely                                                  | 1 kabel USB 3.1 pro každý disk|
| Kapacita služby Storage na jednu objednávku                              | 40 TB (využitelnost cca 35 TB)|
| Kapacita úložiště disku                                   | 8 TB (využitelnost cca 7 TB)|
| Datové rozhraní                                          | USB   |
| Zabezpečení                                                | Předběžné šifrování pomocí nástroje BitLocker a zabezpečené aktualizace <br> Disky chráněné klíčem <br> Data šifrovaná za všech okolností  |
| Rychlost přenosu dat                                      | Až 430 MB/s v závislosti na velikosti souboru      |
|Správa                                               | portál Azure |


## <a name="region-availability"></a>Dostupnost v oblastech

Po dobu platnosti verze Preview může služba Data Box Disk přenášet data do následujících oblastí Azure:


|Oblast Azure  |Oblast Azure  |
|---------|---------|
|Západní střed USA     |USA – východ        |        
|Západní USA 2     |USA – východ 2          |     
|USA – západ     | Západní Evropa        |      
|Střed USA – jih   |Severní Evropa     |         
|USA – střed     |Kanada – východ|
|Střed USA – sever  |Kanada – střed    |




## <a name="pricing"></a>Ceny

Ve verzi Preview je služba Data Box Disk dostupná zdarma. To se změní, jakmile bude služba Data Box Disk dostupná obecně.

## <a name="next-steps"></a>Další kroky

- Projděte si [požadavky služby Data Box Disk](data-box-disk-system-requirements.md).
- Seznamte se s [omezeními služby Data Box Disk](data-box-disk-limits.md).
- Rychlé nasazení řešení [Azure Data Box Disk](data-box-disk-quickstart-portal.md) na webu Azure Portal
