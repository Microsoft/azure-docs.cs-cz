---
title: Možnosti přenosu dat Azure pro velké datové sady s nízkou nebo žádnou šířkou pásma sítě | Microsoft Docs
description: Naučte se, jak zvolit řešení Azure pro přenos dat, pokud jste ve svém prostředí omezili šířku pásma sítě a plánujete přenos velkých datových sad.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 9b83ba106b35a0a3abd035e85f60c4c39bbadd3b
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704636"
---
# <a name="data-transfer-for-large-datasets-with-low-or-no-network-bandwidth"></a>Přenos dat rozsáhlých datových sad s malou nebo žádnou šířkou pásma sítě
 
Tento článek obsahuje přehled řešení přenosu dat, pokud jste ve svém prostředí omezili šířku pásma sítě a plánujete přenos velkých datových sad. Tento článek také popisuje doporučené možnosti přenosu dat a příslušnou klíčovou matrici pro tento scénář.

Přehled všech dostupných možností přenosu dat získáte, když přejdete na [vybrat řešení Azure Data Transfer](storage-choose-data-transfer-solution.md).

## <a name="offline-transfer-or-network-transfer"></a>Offline přenos nebo přenos v síti

Velké datové sady znamenají, že máte k dispozici několik služby TBs až po několik služby PBs. Omezíte šířku pásma sítě, síť je pomalá nebo nespolehlivá. Navíc:

- Náklady na přenos v síti od poskytovatelů internetových služeb (ISP) jsou omezené.
- Zásady zabezpečení nebo organizace neumožňují odchozí připojení při obchodování s citlivými daty.

Ve všech výše uvedených instancích použijte fyzické zařízení k jednorázovému přenosu dat hromadných přenosů dat. Vyberte si z Data Box Disk, Data Box, Data Box Heavy zařízení, která jsou poskytována společností Microsoft, nebo je importujte a exportujte pomocí vlastních disků.

Chcete-li ověřit, zda je fyzickým zařízením správná možnost, použijte následující tabulku. Zobrazuje projektový čas pro přenos síťových dat pro různé dostupné šířky pásma (předpokládá 90% využití). Pokud je přenos v síti příliš pomalý, měli byste použít fyzické zařízení.  

![Přenos v síti nebo offline přenos](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

## <a name="recommended-options"></a>Doporučené možnosti

Dostupné možnosti v tomto scénáři jsou zařízení pro Azure Data Box offline přenos nebo Azure import/export.

- **Azure Data box rodina pro offline přenosy** – pomocí zařízení ze zařízení data box dodaných společností Microsoft můžete přesunout velké objemy dat do Azure, pokud jste omezeni časem, dostupností sítě nebo náklady. Ke kopírování místních dat můžete použít nástroje, jako je například Robocopy. V závislosti na velikosti dat, která chcete přenést, si můžete vybrat Data Box Disk, Data Box nebo Data Box Heavy.
- **Import/export do Azure** – pomocí služby importu a exportu v Azure můžete prostřednictvím přenosu vlastních diskových jednotek bezpečně importovat velké objemy dat do služby Azure Blob Storage a souborů Azure. Pomocí této služby je také možné přenést data ze služby Azure Blob Storage na diskové jednotky a odeslat je do místních lokalit.

## <a name="comparison-of-key-capabilities"></a>Porovnání klíčových funkcí

Následující tabulka shrnuje rozdíly v klíčových funkcích.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy              |    Import/export                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    **Velikost dat**                    |    Až 35 TBs                 |    Až 80 TBs na zařízení                       |    Až 800 TB na zařízení               |    Proměnná                            |
|    **Datový typ**                    |    Objekty blob Azure                  |    Objekty blob Azure<br>Azure Files                    |    Objekty blob Azure<br>Azure Files            |    Objekty blob Azure<br>Azure Files          |
|    **Formulářový faktor**                  |    5 SSD na objednávku             |    1 X 50-kg zařízení velikosti stolního počítače na jednu objednávku    |    1 X ~ 500-kg velké zařízení na jednu objednávku    |    Až 10 HDD/SSD na objednávku        |
|    **Čas prvotního nastavení**           |    Nízká <br>(15 minut)            |    Nízká až střední <br> (<30 minut)               |    Pokročilé<br>(1-2 hodin)               |    Střední až obtížné<br>variabilní |
|    **Odesílání dat do Azure**           |    Ano                          |    Ano                                           |    Ano                                   |    Ano                                 |
|    **Export dat z Azure**       |    No                           |    No                                            |    No                                    |    Ano                                 |
|    **Šifrování**                   |    AES 128-bit                  |    AES 256-bit                                   |    AES 256-bit                           |    AES 128-bit                         |
|    **Hardware**                     |     Dodán Microsoft          |    Dodán Microsoft                            |    Dodán Microsoft                    |    Dodán zákazník                   |
|    **Síťové rozhraní**            |    USB 3.1/SATA                 |    RJ 45, SFP +                                   |    RJ45, QSFP +                           |    SATA II/SATA III                    |
|    **Integrace partnerských řešení**          |    Některé                         |    [Vysoká](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                          |    [Vysoká](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                  |    Některé                                |
|    **Expedice**                     |    Spravované Microsoftem            |    Spravované Microsoftem                             |    Spravované Microsoftem                     |    Spravované zákazníkem                    |
| **Použít při přesunu dat**     |V rámci hranice obchodu|V rámci hranice obchodu|V rámci hranice obchodu|Mezi geografickými hranicemi, např. US až EU|
|    **Ceny**                      |    [Ceny](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Ceny](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Ceny](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Ceny](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


## <a name="next-steps"></a>Další kroky

- Informace o tom, jak

    - [Přenos dat pomocí data box disk](../../databox/data-box-disk-quickstart-portal.md).
    - [Přenos dat pomocí data box](../../databox/data-box-quickstart-portal.md).
    - [Přeneste data pomocí importu/exportu](../../import-export/storage-import-export-data-to-blobs.md).