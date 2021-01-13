---
title: Redundance dat
titleSuffix: Azure Storage
description: Pochopení redundance dat v Azure Storage. Data v účtu Microsoft Azure Storage se replikují pro zajištění odolnosti a vysoké dostupnosti.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/13/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 3c0b466a7db688ed3e24441f652f6a1ef1a88ee1
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/13/2021
ms.locfileid: "98180077"
---
# <a name="azure-storage-redundancy"></a>Redundance Azure Storage

Azure Storage vždycky ukládá více kopií vašich dat, takže je chráněná před plánovanými a neplánovanými událostmi, včetně přechodných selhání hardwaru, sítě nebo výpadků napájení a obrovských přirozených havárií. Redundance zajišťuje, že váš účet úložiště splňuje požadavky na dostupnost a odolnost i v případě selhání.

Při rozhodování, která možnost redundance je pro váš scénář nejvhodnější, zvažte kompromisy mezi nižšími náklady a vyšší dostupností. Mezi faktory, které vám pomůžou určit, kterou možnost redundance byste měli zvolit, patří:  

- Způsob replikace dat v primární oblasti
- Bez ohledu na to, jestli se vaše data replikují do druhé oblasti, která je geograficky vzdálená v primární oblasti, aby se chránila před místními katastrofami
- Bez ohledu na to, jestli vaše aplikace vyžaduje přístup pro čtení replikovaných dat v sekundární oblasti, pokud z nějakého důvodu nebude dostupná primární oblast

## <a name="redundancy-in-the-primary-region"></a>Redundance v primární oblasti

Data v účtu Azure Storage jsou vždy replikována třikrát v primární oblasti. Azure Storage nabízí dvě možnosti, jak se mohou vaše data replikovat v primární oblasti:

- **Místně redundantní úložiště (LRS)** kopíruje data synchronně třikrát v jednom fyzickém umístění v primární oblasti. LRS je nejlevnější možnost replikace, ale nedoporučuje se pro aplikace vyžadující vysokou dostupnost.
- **Zóna – redundantní úložiště (ZRS)** kopíruje data synchronně v rámci tří zón dostupnosti Azure v primární oblasti. Pro aplikace vyžadující vysokou dostupnost společnost Microsoft doporučuje používat ZRS v primární oblasti a také replikovat do sekundární oblasti.

### <a name="locally-redundant-storage"></a>Místně redundantní úložiště

Místně redundantní úložiště (LRS) replikuje vaše data třikrát v jednom fyzickém umístění v primární oblasti. LRS poskytuje v průběhu daného roku alespoň 99,999999999% (11 devíti) odolnosti objektů.

LRS je možnost redundance nejnižší ceny a nabízí minimální odolnost v porovnání s jinými možnostmi. LRS chrání vaše data proti racku serveru a selhání jednotky. Pokud ale v datovém centru dojde k havárii, jako je třeba požár nebo zahlcení, může dojít ke ztrátě nebo obnovení všech replik účtu úložiště pomocí LRS. Pro zmírnění tohoto rizika Společnost Microsoft doporučuje používat úložiště ZRS ( [Zone-redundantní](#zone-redundant-storage) úložiště), [geograficky redundantní úložiště](#geo-redundant-storage) (GRS) nebo [geograficky redundantní úložiště](#geo-zone-redundant-storage) (GZRS).

Požadavek na zápis do účtu úložiště, který používá LRS, proběhne synchronně. Operace zápisu se úspěšně vrátí až po zápisu dat do všech tří replik.

LRS je dobrou volbou pro následující scénáře:

- Pokud vaše aplikace ukládá data, která je možné snadno rekonstruovat, pokud dojde ke ztrátě dat, můžete se rozhodnout pro LRS.
- Pokud je vaše aplikace omezená na replikaci dat v rámci země nebo oblasti z důvodu požadavků na zásady správného řízení dat, můžete se rozhodnout pro LRS. V některých případech se spárované oblasti, ve kterých se data geograficky replikují, můžou nacházet v jiné zemi nebo oblasti. Další informace o spárovaných oblastech najdete v tématu [oblasti Azure](https://azure.microsoft.com/regions/).

### <a name="zone-redundant-storage"></a>Zónově redundantní úložiště

Zóna – redundantní úložiště (ZRS) replikuje data Azure Storage synchronně v rámci tří zón dostupnosti Azure v primární oblasti. Každá zóna dostupnosti je samostatné fyzické umístění s nezávislým napájením, chlazením a sítí. ZRS nabízí odolnost pro Azure Storage datových objektů nejméně 99,9999999999% (12 9) v průběhu daného roku.

Pomocí ZRS jsou vaše data stále přístupná pro operace čtení i zápisu i v případě, že zóna nebude k dispozici. Pokud se zóna bude nedostupná, Azure si neuvolní síťové aktualizace, jako je třeba přesměrování DNS. Tyto aktualizace mohou ovlivnit vaši aplikaci, pokud přistupujete k datům před dokončením aktualizací. Při navrhování aplikací pro ZRS se řiďte postupy pro zpracování přechodných chyb, včetně implementace zásad opakování pomocí exponenciálního zálohování.

Požadavek na zápis do účtu úložiště, který používá ZRS, proběhne synchronně. Operace zápisu se úspěšně vrátí až po zápisu dat do všech replik ve třech zónách dostupnosti.

Microsoft doporučuje používat ZRS v primární oblasti pro scénáře, které vyžadují konzistenci, odolnost a vysokou dostupnost. Doporučujeme také používat ZRS, pokud chcete omezit aplikaci na replikaci dat pouze v zemi nebo oblasti z důvodu požadavků na zásady správného řízení dat.

ZRS poskytuje vynikající výkon, nízkou latenci a odolnost pro vaše data, pokud je dočasně nedostupná. ZRS sám o sobě však nemusí chránit vaše data před regionální havárií, při které je trvale ovlivněno více zón. V případě ochrany před místními katastrofami Microsoft doporučuje použití [geograficky redundantního úložiště](#geo-zone-redundant-storage) (GZRS), které používá ZRS v primární oblasti a také geograficky replikuje vaše data do sekundární oblasti.

Následující tabulka uvádí, které typy účtů úložiště podporují ZRS, ve kterých oblastech:

| Typ účtu úložiště | Podporované oblasti | Podporované služby |
|--|--|--|
| Obecné účely v2<sup>1</sup> | Jihovýchodní Asie<br /> Austrálie – východ<br /> Evropa – sever<br />  Evropa – západ<br /> Francie – střed<br /> Japan East<br /> Jižní Afrika – sever<br /> Spojené království – jih<br /> USA – střed<br /> USA – východ<br /> USA – východ 2<br /> USA – západ 2 | Objekty blob bloku<br /> Objekty blob stránky<sup>2</sup><br /> Sdílené složky (Standard)<br /> Tabulky<br /> Fronty<br /> |
| BlockBlobStorage<sup>1</sup> | Jihovýchodní Asie<br /> Austrálie – východ<br /> Evropa – sever<br /> Evropa – západ<br /> Japan East<br /> USA – východ <br /> USA – východ 2 <br /> USA – západ 2| Jenom objekty blob bloku úrovně Premium |
| Úložiště | Jihovýchodní Asie<br /> Austrálie – východ<br /> Evropa – sever<br /> Evropa – západ<br /> Japan East<br /> USA – východ <br /> USA – východ 2 <br /> USA – západ 2 | Jenom soubory úrovně Premium – jenom sdílené složky |

<sup>1</sup> úroveň archivu se v současnosti nepodporuje u účtů ZRS.<br />
<sup>2</sup> účty úložiště, které obsahují Azure Managed disks pro virtuální počítače, vždycky používají LRS. Nespravované disky Azure by měly také používat LRS. Je možné vytvořit účet úložiště pro nespravované disky Azure, které používají GRS, ale nedoporučuje se v důsledku potenciálních problémů s konzistencí přes asynchronní geografickou replikaci. Ani spravované ani nespravované disky nepodporují ZRS nebo GZRS. Další informace o službě Managed disks najdete v tématu [ceny za službu Azure Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).

Informace o tom, které oblasti podporují ZRS, najdete v článku **Podpora služeb podle oblasti**  v tématu [co je zóny dostupnosti Azure?](../../availability-zones/az-overview.md).

## <a name="redundancy-in-a-secondary-region"></a>Redundance v sekundární oblasti

U aplikací, které vyžadují vysokou dostupnost, se můžete rozhodnout dál kopírovat data v účtu úložiště do sekundární oblasti, která je od primární oblasti od sebe stovky kilometrů. Pokud je váš účet úložiště zkopírovaný do sekundární oblasti, budou vaše data odolná i v případě kompletního výpadku nebo havárie, ve kterém se primární oblast nedá obnovit.

Při vytváření účtu úložiště vyberete primární oblast pro daný účet. Spárovaná sekundární oblast je určena v závislosti na primární oblasti a nelze ji změnit. Další informace o oblastech podporovaných v Azure najdete v tématu [oblasti Azure](https://azure.microsoft.com/global-infrastructure/regions/).

Azure Storage nabízí dvě možnosti pro kopírování dat do sekundární oblasti:

- **Geograficky redundantní úložiště (GRS)** kopíruje data synchronně třikrát v rámci jednoho fyzického umístění v primární oblasti s využitím LRS. Pak data kopíruje asynchronně do jednoho fyzického umístění v sekundární oblasti.
- **Geograficky redundantní úložiště (GZRS)** kopíruje data synchronně v rámci tří zón dostupnosti Azure v primární oblasti pomocí ZRS. Pak data kopíruje asynchronně do jednoho fyzického umístění v sekundární oblasti.

Hlavním rozdílem mezi GRS a GZRS je způsob, jakým se data replikují v primární oblasti. V rámci sekundární oblasti jsou data vždy synchronně replikována třikrát pomocí LRS. LRS v sekundární oblasti chrání vaše data proti selhání hardwaru.

V GRS nebo GZRS nejsou data v sekundární oblasti k dispozici pro přístup pro čtení nebo zápis, pokud nedojde k převzetí služeb při selhání sekundární oblastí. Pro přístup pro čtení do sekundární oblasti nakonfigurujte účet úložiště tak, aby používal geograficky redundantní úložiště s přístupem pro čtení (RA-GRS) nebo geograficky redundantní úložiště s přístupem pro čtení (RA-GZRS). Další informace najdete v tématu [přístup pro čtení k datům v sekundární oblasti](#read-access-to-data-in-the-secondary-region).

Pokud primární oblast nebude k dispozici, můžete zvolit převzetí služeb při selhání sekundární oblastí. Po dokončení převzetí služeb při selhání se sekundární oblast přestanou primární oblastí a můžete znovu číst a zapisovat data. Další informace o zotavení po havárii a o tom, jak převzít služby při selhání do sekundární oblasti, najdete v tématu [převzetí služeb při selhání při zotavení po havárii a účtu úložiště](storage-disaster-recovery-guidance.md).

> [!IMPORTANT]
> Vzhledem k tomu, že data jsou replikována do sekundární oblasti asynchronně, selhání ovlivňující primární oblast může způsobit ztrátu dat, pokud nelze obnovit primární oblast. Interval mezi nejnovějšími zápisy do primární oblasti a posledním zápisem do sekundární oblasti se označuje jako cíl bodu obnovení (RPO). RPO označuje bod v čase, do kterého lze obnovit data. Azure Storage obvykle má RPO méně než 15 minut, ale v současné době není k dispozici žádná smlouva SLA, jak dlouho trvá replikace dat do sekundární oblasti.

### <a name="geo-redundant-storage"></a>Geograficky redundantní úložiště

Geograficky redundantní úložiště (GRS) kopíruje data synchronně třikrát v rámci jednoho fyzického umístění v primární oblasti s využitím LRS. Pak data kopíruje asynchronně do jednoho fyzického umístění v sekundární oblasti, která je od primární oblasti vzdálena stovky mil. GRS nabízí odolnost pro Azure Storage datových objektů nejméně 99.99999999999999% (16 9) v průběhu daného roku.

Operace zápisu se nejdřív potvrdí do primárního umístění a replikuje se pomocí LRS. Aktualizace se pak asynchronně replikuje do sekundární oblasti. Když jsou data zapsána do sekundárního umístění, je také replikována v tomto umístění pomocí LRS.

### <a name="geo-zone-redundant-storage"></a>Geograficky zónově redundantní úložiště

Geograficky redundantní úložiště (GZRS) kombinuje vysokou dostupnost poskytovanou redundancí napříč zónami dostupnosti s ochranou z regionálních výpadků poskytovaných geografickou replikací. Data v účtu úložiště GZRS se zkopírují do tří [zón dostupnosti Azure](../../availability-zones/az-overview.md) v primární oblasti a také se replikují do sekundární geografické oblasti pro ochranu z regionálních havárií. Microsoft doporučuje používat GZRS pro aplikace, které vyžadují maximální konzistenci, odolnost a dostupnost, vynikající výkon a odolnost proti zotavení po havárii.

S účtem úložiště GZRS můžete dál číst a zapisovat data, pokud se zóna dostupnosti stane nedostupnou nebo nejde obnovit. Kromě toho jsou vaše data také odolná v případě kompletního oblasti výpadku nebo havárie, ve které není primární oblast obnovitelné. GZRS je navržený tak, aby poskytoval alespoň 99.99999999999999% (16 9) odolnosti objektů v průběhu daného roku.

GZRS a RA-GZRS podporují jenom účty úložiště pro obecné účely verze 2. Další informace o typech účtů úložiště najdete v tématu [Přehled účtu Azure Storage](storage-account-overview.md). GZRS a RA-GZRS podporují objekty blob bloku, objekty blob stránky (s výjimkou disků VHD), soubory, tabulky a fronty.

GZRS a RA-GZRS jsou podporovány v následujících oblastech:

- Jihovýchodní Asie
- Evropa – sever
- Evropa – západ
- Japan East
- Spojené království – jih
- USA – střed
- USA – východ
- USA – východ 2
- USA – západ 2

Informace o cenách najdete v podrobnostech o cenách [objektů BLOB](https://azure.microsoft.com/pricing/details/storage/blobs), [souborů](https://azure.microsoft.com/pricing/details/storage/files/), [front](https://azure.microsoft.com/pricing/details/storage/queues/)a [tabulek](https://azure.microsoft.com/pricing/details/storage/tables/).

## <a name="read-access-to-data-in-the-secondary-region"></a>Přístup pro čtení k datům v sekundární oblasti

Geograficky redundantní úložiště (s GRS nebo GZRS) replikuje vaše data do jiného fyzického umístění v sekundární oblasti, aby se chránila před oblastními výpadky. Tato data jsou však k dispozici pro čtení pouze v případě, že zákazník nebo společnost Microsoft zahájí převzetí služeb při selhání z primární do sekundární oblasti. Když povolíte přístup pro čtení do sekundární oblasti, data je možné kdykoli číst, a to i v případě, že primární oblast nebude k dispozici. Pro přístup pro čtení do sekundární oblasti povolte geograficky redundantní úložiště s přístupem pro čtení (RA-GRS) nebo geograficky redundantní úložiště s přístupem pro čtení (RA-GZRS).

> [!NOTE]
> Soubory Azure nepodporují geograficky redundantní úložiště s přístupem pro čtení (RA-GRS) a geograficky redundantní úložiště s přístupem pro čtení (RA-GZRS).

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Návrh aplikací pro přístup pro čtení sekundárního

Pokud je váš účet úložiště nakonfigurovaný pro přístup pro čtení do sekundární oblasti, můžete navrhovat aplikace pro bezproblémové přesunutí na čtení dat ze sekundární oblasti, pokud z nějakého důvodu dojde k nedostupnosti primární oblasti. 

Sekundární oblast je k dispozici pro přístup pro čtení po povolení RA-GRS nebo RA-GZRS, abyste mohli otestovat svou aplikaci předem, abyste se ujistili, že se v případě výpadku budou správně číst ze sekundárního zařízení. Další informace o tom, jak navrhovat aplikace pro zajištění vysoké dostupnosti, najdete v tématu [použití geografické redundance k návrhu vysoce dostupných aplikací](geo-redundant-design.md).

Když je povolený přístup pro čtení k sekundárnímu, vaše aplikace se dá číst ze sekundárního koncového bodu i z primárního koncového bodu. Sekundární koncový bod připojí příponu *– sekundární* k názvu účtu. Pokud je například primárním koncovým bodem pro úložiště objektů BLOB `myaccount.blob.core.windows.net` , pak je sekundární koncový bod `myaccount-secondary.blob.core.windows.net` . Přístupové klíče účtu pro váš účet úložiště jsou u primárních i sekundárních koncových bodů stejné.

### <a name="check-the-last-sync-time-property"></a>Kontrola vlastnosti Čas poslední synchronizace

Vzhledem k tomu, že data jsou replikována do sekundární oblasti asynchronně, Sekundární oblast je často za primární oblastí. Pokud dojde k selhání v primární oblasti, je pravděpodobně možné, že všechna zápisy do primární databáze nebudou dosud replikována do sekundárního umístění.

Chcete-li zjistit, které operace zápisu byly replikovány do sekundární oblasti, může vaše aplikace kontrolovat vlastnost **času poslední synchronizace** pro váš účet úložiště. Všechny operace zápisu zapsané do primární oblasti před poslední dobou synchronizace byly úspěšně replikovány do sekundární oblasti, což znamená, že je možné je načíst ze sekundárního umístění. Všechny operace zápisu zapsané do primární oblasti po poslední době synchronizace mohou nebo nemusí být replikovány do sekundární oblasti, což znamená, že nemusí být k dispozici pro operace čtení.

Můžete zadat dotaz na hodnotu vlastnosti **čas poslední synchronizace** pomocí Azure PowerShell, Azure CLI nebo některé z klientských knihoven Azure Storage. Vlastnost **čas poslední synchronizace** je hodnota data a času GMT. Další informace najdete v tématu [Zkontrolujte vlastnost čas poslední synchronizace pro účet úložiště](last-sync-time-get.md).

## <a name="summary-of-redundancy-options"></a>Souhrn možností redundance

Tabulky v následujících částech shrnují možnosti redundance, které jsou k dispozici pro Azure Storage

### <a name="durability-and-availability-parameters"></a>Parametry odolnosti a dostupnosti

Následující tabulka popisuje klíčové parametry pro každou možnost redundance:

| Parametr | LRS | ZRS | GRS/RA – GRS | GZRS/RA – GZRS |
|:-|:-|:-|:-|:-|
| Procentuální hodnota odolnosti objektů v průběhu daného roku | alespoň 99,999999999% (11 9 's) | minimálně 99,9999999999% (12 9 's) | minimálně 99.99999999999999% (16 9) | minimálně 99.99999999999999% (16 9) |
| Dostupnost pro žádosti o čtení | Minimálně 99,9% (99% pro studenou úroveň přístupu) | Minimálně 99,9% (99% pro studenou úroveň přístupu) | Minimálně 99,9% (99% pro studenou úroveň přístupu) pro GRS<br /><br />Minimálně 99,99% (99,9% pro studenou úroveň přístupu) pro RA-GRS | Minimálně 99,9% (99% pro studenou úroveň přístupu) pro GZRS<br /><br />Minimálně 99,99% (99,9% pro studenou úroveň přístupu) pro RA-GZRS |
| Dostupnost pro požadavky na zápis | Minimálně 99,9% (99% pro studenou úroveň přístupu) | Minimálně 99,9% (99% pro studenou úroveň přístupu) | Minimálně 99,9% (99% pro studenou úroveň přístupu) | Minimálně 99,9% (99% pro studenou úroveň přístupu) |
| Počet kopií dat udržovaných na samostatných uzlech | Tři kopie v jedné oblasti | Tři kopie v různých zónách dostupnosti v rámci jedné oblasti | Šest kopií celkem, včetně tří v primární oblasti a tři v sekundární oblasti | Šest kopií celkem, včetně tří v samostatných zónách dostupnosti v primární oblasti a tří místně redundantních kopií v sekundární oblasti |

### <a name="durability-and-availability-by-outage-scenario"></a>Scénář odolnosti a dostupnosti při výpadku

Následující tabulka uvádí, zda jsou vaše data v daném scénáři odolná a dostupná v závislosti na tom, jaký typ redundance je platný pro váš účet úložiště:

| Scénář výpadku | LRS | ZRS | GRS/RA – GRS | GZRS/RA – GZRS |
|:-|:-|:-|:-|:-|
| Uzel v datovém centru nebude dostupný. | Yes | Yes | Yes | Yes |
| Nebudete mít k dispozici celé datové centrum (oblast nebo mimo oblast). | No | Yes | Ano<sup>1</sup> | Yes |
| V primární oblasti dojde k výpadku v rámci oblasti. | No | No | Ano<sup>1</sup> | Ano<sup>1</sup> |
| Přístup pro čtení do sekundární oblasti je k dispozici, pokud primární oblast nebude k dispozici. | No | No | Ano (s RA-GRS) | Ano (s RA-GZRS) |

<sup>1</sup> převzetí služeb při selhání účtu se vyžaduje k obnovení dostupnosti pro zápis, pokud primární oblast nebude k dispozici. Další informace najdete v tématu [převzetí služeb při selhání při zotavení po havárii a účtu úložiště](storage-disaster-recovery-guidance.md).

### <a name="supported-storage-account-types"></a>Podporované typy účtů úložiště

Následující tabulka uvádí, které možnosti redundance jsou podporovány jednotlivými typy účtů úložiště. Informace o typech účtů úložiště najdete v tématu [Přehled účtu úložiště](storage-account-overview.md).

| LRS | ZRS | GRS/RA – GRS | GZRS/RA – GZRS |
|:-|:-|:-|:-|
| Účty pro obecné účely verze 2<br /> Účty pro obecné účely verze 1<br /> Úložiště objektů blob bloku<br /> Blob Storage<br /> File Storage | Účty pro obecné účely verze 2<br /> Úložiště objektů blob bloku<br /> File Storage | Účty pro obecné účely verze 2<br /> Účty pro obecné účely verze 1<br /> Blob Storage | Účty pro obecné účely verze 2 |

Všechna data pro všechny účty úložiště se zkopírují podle možnosti redundance pro účet úložiště. Zkopírují se objekty, mezi které patří objekty blob bloku, doplňovací objekty blob, objekty blob stránky, fronty, tabulky a soubory. Data na všech úrovních, včetně archivní úrovně, se zkopírují. Další informace o úrovních objektů BLOB najdete v tématu [Azure Blob Storage: horká, studená a archivní úroveň přístupu](../blobs/storage-blob-storage-tiers.md).

Informace o cenách pro jednotlivé možnosti redundance najdete v tématu [Azure Storage ceny](https://azure.microsoft.com/pricing/details/storage/).

> [!NOTE]
> Azure Premium Disk Storage v současné době podporuje jenom místně redundantní úložiště (LRS). Účty úložiště blob bloku v některých oblastech podporují místně redundantní úložiště (LRS) a zónu redundantního úložiště (ZRS).

## <a name="data-integrity"></a>Integrita dat

Azure Storage pravidelně ověřuje integritu dat uložených pomocí redundantních kontrol redundance (CRCs). Pokud je zjištěno poškození dat, je opraveno pomocí redundantních dat. Azure Storage taky vypočítává kontrolní součty pro veškerý síťový provoz, aby se zjistilo poškození datových paketů při ukládání nebo načítání dat.

## <a name="see-also"></a>Viz také:

- [Podívejte se na vlastnost čas poslední synchronizace pro účet úložiště.](last-sync-time-get.md)
- [Změna možnosti redundance pro účet úložiště](redundancy-migration.md)
- [Použití geografické redundance k návrhu vysoce dostupných aplikací](geo-redundant-design.md)
- [Zotavení po havárii a převzetí služeb při selhání účtu úložiště](storage-disaster-recovery-guidance.md)
