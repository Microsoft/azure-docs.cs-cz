---
title: Data redundancy
titleSuffix: Azure Storage
description: Data v účtu úložiště Microsoft Azure jsou replikována z důvodu odolnosti a vysoké dostupnosti. Konfigurace redundance zahrnují místně redundantní úložiště (LRS), zónově redundantní úložiště (ZRS), geograficky redundantní úložiště (GRS), geograficky redundantní úložiště pro čtení (RA-GRS), geograficky zónově redundantní úložiště (GZRS) (preview) a geograficky redundantní úložiště pro čtení (RA-GZRS) (preview).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 78f7c935e64276e7f4862dad966b99bff6bd246d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481943"
---
# <a name="azure-storage-redundancy"></a>Redundance azure úložiště

Azure Storage vždy ukládá více kopií vašich dat tak, aby byla chráněna před plánovanými a neplánovanými událostmi, včetně přechodných selhání hardwaru, výpadků sítě nebo napájení a rozsáhlých přírodních katastrof. Redundance zajišťuje, že váš účet úložiště splňuje [smlouvu o úrovni služeb (SLA) pro Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/) i tváří v tvář selhání.

Při rozhodování, která možnost redundance je pro váš scénář nejvhodnější, zvažte kompromisy mezi nižšími náklady a vyšší dostupností a trvanlivostí. Mezi faktory, které pomáhají určit, kterou možnost redundance byste měli zvolit, patří:  

- Způsob replikace dat v primární oblasti
- Zda jsou data replikována do druhého umístění geograficky vzdáleného primární oblasti, za účelem ochrany před regionálními katastrofami
- Zda vaše aplikace vyžaduje přístup pro čtení k replikovaných dat v sekundární oblasti, pokud primární oblast přestane být z jakéhokoli důvodu k dispozici

## <a name="redundancy-in-the-primary-region"></a>Redundance v primární oblasti

Data v účtu Azure Storage se v primární oblasti vždy replikují třikrát. Azure Storage nabízí dvě možnosti replikace dat v primární oblasti:

- **Místně redundantní úložiště (LRS)** zkopíruje data synchronně třikrát v rámci jednoho fyzického umístění v primární oblasti. LRS je nejlevnější možnost replikace, ale nedoporučuje se pro aplikace vyžadující vysokou dostupnost.
- **Zónově redundantní úložiště (ZRS)** synchronně kopíruje vaše data ve třech zónách dostupnosti Azure v primární oblasti. Pro aplikace vyžadující vysokou dostupnost společnost Microsoft doporučuje používat ZRS v primární oblasti a také replikovat do sekundární oblasti.

### <a name="locally-redundant-storage"></a>Místně redundantní úložiště

Místně redundantní úložiště (LRS) replikuje data třikrát v rámci jednoho fyzického umístění v primární oblasti. LRS poskytuje alespoň 99.999999999% (11 devítky) trvanlivost objektů v daném roce.

LRS je možnost redundance s nejnižšími náklady a nabízí nejmenší trvanlivost ve srovnání s jinými možnostmi. LRS chrání vaše data před selháním serverového racku a jednotky. Pokud však dojde k havárii, jako je požár nebo zaplavení v datovém centru, všechny repliky účtu úložiště pomocí LRS může dojít ke ztrátě nebo neopravitelné. Pro zmírnění tohoto rizika společnost Microsoft doporučuje používat [zónově redundantní úložiště](#zone-redundant-storage) (ZRS), [geograficky redundantní úložiště](#geo-redundant-storage) (GRS) nebo [úložiště (preview) (GZRS) (geograficky redundantní).](#geo-zone-redundant-storage-preview)

Požadavek na zápis do účtu úložiště, který používá LRS se stane synchronně. Operace zápisu vrátí úspěšně pouze po zápisu dat do všech tří replik.

LRS je dobrou volbou pro následující scénáře:

- Pokud vaše aplikace ukládá data, která lze snadno rekonstruovat, pokud dojde ke ztrátě dat, můžete se rozhodnout pro LRS.
- Pokud je vaše aplikace omezena na replikaci dat pouze v rámci země nebo oblasti z důvodu požadavků na zásady správného řízení dat, můžete se rozhodnout pro LRS. V některých případech mohou být spárované oblasti, ve kterých jsou data geograficky replikována, v jiné zemi nebo oblasti. Další informace o spárovaných oblastech najdete v [tématu Oblasti Azure](https://azure.microsoft.com/regions/).

### <a name="zone-redundant-storage"></a>Zónově redundantní úložiště

Zónově redundantní úložiště (ZRS) replikuje data Azure Storage synchronně přes tři zóny dostupnosti Azure v primární oblasti. Každá zóna dostupnosti je samostatné fyzické umístění s nezávislým napájením, chlazením a sítí. ZRS nabízí odolnost pro datové objekty Azure Storage alespoň 99.9999999999 % (12 9) za daný rok.

S ZRS vaše data jsou stále přístupná pro operace čtení i zápisu i v případě, že zóna přestane být k dispozici. Pokud zóna přestane být dostupná, Azure provede aktualizace sítě, jako je například opětovné ukazování DNS. Tyto aktualizace mohou mít vliv na vaši aplikaci, pokud přistupujete k datům před dokončením aktualizací. Při navrhování aplikací pro ZRS postupujte podle postupů pro přechodné zpracování chyb, včetně implementace zásad opakování s exponenciálním back-off.

Požadavek na zápis do účtu úložiště, který používá ZRS se stane synchronně. Operace zápisu vrátí úspěšně pouze po zápisu dat do všech replik ve všech třech zónách dostupnosti.

Společnost Microsoft doporučuje používat ZRS v primární oblasti pro scénáře, které vyžadují konzistenci, odolnost a vysokou dostupnost. ZRS poskytuje vynikající výkon, nízkou latenci a odolnost proti chybám pro vaše data, pokud se stane dočasně nedostupný. ZRS však sama o sobě nemusí chránit vaše data před regionální katastrofou, kde je trvale ovlivněno více zón. Pro ochranu před regionálními katastrofami společnost Microsoft doporučuje používat úložiště GZRS [(geozónově redundantní),](#geo-zone-redundant-storage-preview) které používá ZRS v primární oblasti a také geograficky replikuje data do sekundární oblasti.

V následující tabulce jsou uvedeny typy účtů úložiště, které podporují ZRS v oblastech:

|    Typ účtu úložiště    |    Podporované oblasti    |    Podporované služby    |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
|    Pro všeobecné účely v2<sup>1</sup>    | Jihovýchodní Asie<br /> Austrálie – východ<br /> Evropa – sever<br />  Evropa – západ<br /> Francie – střed<br /> Japonsko – východ<br /> Jižní Afrika – sever<br /> Spojené království – jih<br /> USA – střed<br /> USA – východ<br /> USA – východ 2<br /> USA – západ 2    |    Objekty blob bloku<br /> Objekty BLOB stránky<sup>2</sup><br /> Sdílené složky (standardní)<br /> Tabulky<br /> Fronty<br /> |
|    Úložiště BlockBlobStorage<sup>1</sup>    | Evropa – západ<br /> USA – východ    |    Pouze objekty BLOB bloku    |
|    Úložiště souborů    | Evropa – západ<br /> USA – východ    |    Jenom soubory Azure    |

<sup>1</sup> Archivní vrstva není aktuálně podporována pro účty ZRS.<br />
<sup>2</sup> Účty úložiště, které obsahují spravované disky Azure pro virtuální počítače, vždy používají LRS. Nespravované disky Azure by měly také používat LRS. Je možné vytvořit účet úložiště pro nespravované disky Azure, který používá GRS, ale nedoporučuje se z důvodu potenciálních problémů s konzistencí přes asynchronní geografickou replikaci. Spravované ani nespravované disky nepodporují ZRS nebo GZRS. Další informace o spravovaných discích najdete v [tématu Ceny za spravované disky Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

Informace o tom, které oblasti podporují ZRS, najdete v **tématu Podpora služeb podle oblastí** v [tématu Co jsou zóny dostupnosti Azure?](../../availability-zones/az-overview.md).

## <a name="redundancy-in-a-secondary-region"></a>Redundance v sekundární oblasti

Pro aplikace vyžadující vysokou dostupnost můžete navíc zkopírovat data v účtu úložiště do sekundární oblasti, která je stovky kilometrů od primární oblasti. Pokud je váš účet úložiště zkopírován do sekundární oblasti, vaše data jsou trvalá i v případě úplného místního výpadku nebo havárie, ve které primární oblast není obnovitelná.

Při vytváření účtu úložiště vyberete primární oblast pro účet. Spárovaná sekundární oblast je určena na základě primární oblasti a nelze ji změnit. Další informace o oblastech podporovaných Azure najdete v [tématu oblasti Azure](https://azure.microsoft.com/global-infrastructure/regions/).

Azure Storage nabízí dvě možnosti kopírování dat do sekundární oblasti:

- **Geograficky redundantní úložiště (GRS)** zkopíruje data synchronně třikrát v rámci jednoho fyzického umístění v primární oblasti pomocí LRS. Potom zkopíruje data asynchronně do jednoho fyzického umístění v sekundární oblasti.
- **Geograficky redundantní úložiště (GZRS)** (preview) synchronně kopíruje vaše data ve třech zónách dostupnosti Azure v primární oblasti pomocí ZRS. Potom zkopíruje data asynchronně do jednoho fyzického umístění v sekundární oblasti.

Primární rozdíl mezi GRS a GZRS je způsob replikace dat v primární oblasti. V sekundárním umístění jsou data vždy replikována synchronně třikrát pomocí LRS.

S GRS nebo GZRS data v sekundárním umístění není k dispozici pro čtení nebo zápis přístup, pokud je převzetí služeb při selhání do sekundární oblasti. Pro přístup pro čtení do sekundárního umístění nakonfigurujte účet úložiště tak, aby používal geograficky redundantní úložiště pro čtení (RA-GRS) nebo geograficky redundantní úložiště pro přístup ke čtení (RA-GZRS). Další informace naleznete [v tématu Přístup pro čtení k datům v sekundární oblasti](#read-access-to-data-in-the-secondary-region).

Pokud primární oblast přestane být k dispozici, můžete zvolit převzetí služeb při selhání do sekundární oblasti (náhled). Po dokončení převzetí služeb při selhání se sekundární oblast stane primární oblastí a můžete znovu číst a zapisovat data. Další informace o zotavení po havárii a informace o převzetí služeb při selhání do sekundární oblasti naleznete v [tématu Zotavení po havárii a převzetí služeb při selhání účtu (preview)](storage-disaster-recovery-guidance.md).

> [!IMPORTANT]
> Vzhledem k tomu, že data jsou replikována do sekundární oblasti asynchronně, selhání, které ovlivňuje primární oblast může mít za následek ztrátu dat, pokud primární oblast nelze obnovit. Interval mezi nejnovější zápisy do primární oblasti a poslední zápis do sekundární oblasti se označuje jako cíl bodu obnovení (RPO). RPO označuje bod v čase, do kterého lze obnovit data. Azure Storage má obvykle RPO méně než 15 minut, i když je aktuálně žádná sla na, jak dlouho trvá replikovat data do sekundární oblasti.

### <a name="geo-redundant-storage"></a>Geograficky redundantní úložiště

Geograficky redundantní úložiště (GRS) zkopíruje data synchronně třikrát v rámci jednoho fyzického umístění v primární oblasti pomocí LRS. Potom zkopíruje data asynchronně do jednoho fyzického umístění v sekundární oblasti, která je stovky kilometrů od primární oblasti. GRS nabízí odolnost pro datové objekty Azure Storage alespoň 99.999999999999999 % (16 9) za daný rok.

Operace zápisu je nejprve potvrzena do primárního umístění a replikována pomocí LRS. Aktualizace je pak replikována asynchronně do sekundární oblasti. Když jsou data zapsána do sekundárního umístění, je také replikována v rámci tohoto umístění pomocí LRS.

### <a name="geo-zone-redundant-storage-preview"></a>Úložiště s redundantním zónou (náhled)

Geograficky redundantní úložiště (GZRS) (preview) kombinuje vysokou dostupnost poskytovanou redundancí napříč zónami dostupnosti s ochranou před regionálními výpadky poskytovanými geografickou replikací. Data v účtu úložiště GZRS se kopírují do tří [zón dostupnosti Azure](../../availability-zones/az-overview.md) v primární oblasti a replikují se také do sekundární geografické oblasti pro ochranu před regionálními katastrofami. Společnost Microsoft doporučuje používat GZRS pro aplikace, které vyžadují maximální konzistenci, odolnost a dostupnost, vynikající výkon a odolnost pro zotavení po havárii.

S účtem úložiště GZRS můžete pokračovat ve čtení a zápisu dat, pokud zóna dostupnosti přestane být dostupná nebo není obnovitelná. Kromě toho jsou vaše data také trvalá v případě úplného regionálního výpadku nebo havárie, ve které primární oblast není obnovitelná. GZRS je navržen tak, aby poskytoval alespoň 99.99999999999999% (16 9) trvanlivost objektů v daném roce.

Pouze účty úložiště v2 pro obecné účely podporují GZRS a RA-GZRS. Další informace o typech účtů úložiště najdete v tématu [Přehled účtu úložiště Azure](storage-account-overview.md). GZRS a RA-GZRS podporují objekty BLOB bloku, objekty BLOB stránky (s výjimkou disků VHD), soubory, tabulky a fronty.

GZRS a RA-GZRS jsou v současné době k dispozici pro náhled v následujících oblastech:

- Jihovýchodní Asie
- Evropa – sever
- Evropa – západ
- Japonsko – východ
- Spojené království – jih
- USA – východ
- USA – východ 2
- USA – střed
- USA – západ 2

Microsoft nadále povoluje GZRS a RA-GZRS v dalších oblastech Azure. Informace o podporovaných oblastech najdete pravidelně na stránce [Aktualizace služeb Azure.](https://azure.microsoft.com/updates/)

Informace o cenách ve verzi Preview najdete v části GZRS preview cen pro [objekty Blob](https://azure.microsoft.com/pricing/details/storage/blobs), [Soubory](https://azure.microsoft.com/pricing/details/storage/files/), [Fronty](https://azure.microsoft.com/pricing/details/storage/queues/)a [Tabulky](https://azure.microsoft.com/pricing/details/storage/tables/).

> [!IMPORTANT]
> Společnost Microsoft doporučuje nepoužívat funkce náhledu pro produkční úlohy.

## <a name="read-access-to-data-in-the-secondary-region"></a>Přístup pro čtení k datům v sekundární oblasti

Geograficky redundantní úložiště (s GRS nebo GZRS) replikuje vaše data do jiného fyzického umístění v sekundární oblasti, aby bylo chráněno před regionálními výpadky. Tato data jsou však k dispozici pouze pro čtení, pokud zákazník nebo společnost Microsoft zahájí převzetí služeb při selhání z primární do sekundární oblasti. Pokud povolíte přístup pro čtení do sekundární oblasti, vaše data budou k dispozici ke čtení, pokud primární oblast přestane být k dispozici. Pro přístup pro čtení do sekundární oblasti povolte geograficky redundantní úložiště pro čtení (RA-GRS) nebo geograficky redundantní úložiště pro čtení (RA-GZRS).

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Navrhněte své aplikace pro přístup pro čtení do sekundárního

Pokud je váš účet úložiště nakonfigurován pro přístup pro čtení do sekundární oblasti, můžete navrhnout aplikace tak, aby se bez problémů přesunuly na čtení dat ze sekundární oblasti, pokud primární oblast nebude z jakéhokoli důvodu k dispozici. Sekundární oblast je vždy k dispozici pro přístup pro čtení, takže můžete otestovat aplikaci a ujistěte se, že bude číst ze sekundární v případě výpadku. Další informace o návrhu aplikací pro vysokou dostupnost naleznete v [tématu Navrhování vysoce dostupných aplikací pomocí geograficky redundantního úložiště pro čtení](storage-designing-ha-apps-with-ragrs.md).

Pokud je povolen přístup pro čtení do sekundární, vaše data lze číst ze sekundárního koncového bodu, stejně jako z primárního koncového bodu pro váš účet úložiště. Sekundární koncový bod připojí příponu *–sekundární* k názvu účtu. Pokud je `myaccount.blob.core.windows.net`například primární koncový bod pro úložiště objektů Blob `myaccount-secondary.blob.core.windows.net`, je sekundární koncový bod . Přístupové klíče účtu pro váš účet úložiště jsou stejné pro primární i sekundární koncové body.

### <a name="check-the-last-sync-time-property"></a>Kontrola vlastnosti Čas poslední synchronizace

Vzhledem k tomu, že data jsou replikována do sekundární oblasti asynchronně, sekundární oblast je často za primární oblasti. Pokud dojde k selhání v primární oblasti, je pravděpodobné, že všechny zápisy do primární dosud nebyly replikovány do sekundární.

Chcete-li zjistit, které operace zápisu byly replikovány do sekundární oblasti, aplikace může zkontrolovat vlastnost **Poslední čas synchronizace** pro váš účet úložiště. Všechny operace zápisu zapsané do primární oblasti před časem poslední synchronizace byly úspěšně replikovány do sekundární oblasti, což znamená, že jsou k dispozici pro čtení ze sekundární. Všechny operace zápisu zapsané do primární oblasti po posledním čase synchronizace mohou nebo nemusí být replikovány do sekundární oblasti, což znamená, že nemusí být k dispozici pro operace čtení.

Můžete zadat dotaz na hodnotu vlastnosti **Poslední doba synchronizace** pomocí Azure PowerShell, Azure CLI nebo jedné z klientských knihoven Azure Storage. Vlastnost **Čas poslední synchronizace** je hodnota data a času GMT. Další informace naleznete [v tématu Kontrola vlastnosti Poslední doba synchronizace pro účet úložiště](last-sync-time-get.md).

## <a name="summary-of-redundancy-options"></a>Souhrn možností redundance

Následující tabulka ukazuje, jak trvalá a dostupná jsou vaše data v daném scénáři v závislosti na tom, jaký typ redundance platí pro váš účet úložiště:

| Scénář                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS (náhled)                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Uzel v datovém centru přestane být k dispozici                                                                 | Ano                             | Ano                              | Ano                                  | Ano                                  |
| Celé datové centrum (zonální nebo nezonální) se stane nedostupným                                           | Ne                              | Ano                              | Ano                                  | Ano                                  |
| Dojde k výpadku v celém regionu                                                                                     | Ne                              | Ne                               | Ano                                  | Ano                                  |
| Přístup pro čtení k datům v sekundární oblasti, pokud primární oblast přestane být k dispozici | Ne                              | Ne                               | Ano (s RA-GRS)                                   | Ano (s RA-GZRS)                                 |
| Procentuální životnost objektů za daný rok<sup>1</sup>                                          | alespoň 99,9999999999 % (11 9) | alespoň 99,999999999999 % (12 9) | alespoň 99.9999999999999999% (16 9 je) | alespoň 99.9999999999999999% (16 9 je) |
| Podporované typy účtů úložiště<sup>2</sup>                                                                   | GPv2, GPv1, BlockBlobStorage, BlobStorage, Úložiště souborů                | GPv2, BlockBlobStorage, Úložiště souborů                             | GPv2, GPv1, BlobStorage                     | GPv2                     |
| SLA dostupnost pro požadavky na čtení<sup>1</sup>  | Alespoň 99,9 % (99 % pro úroveň chladného přístupu) | Alespoň 99,9 % (99 % pro úroveň chladného přístupu) | Nejméně 99,9 % (99 % pro úroveň chladného přístupu) pro<br /><br />Alespoň 99,99 % (99,9 % pro úroveň studeného přístupu) pro RA-GRS | Nejméně 99,9 % (99 % pro úroveň chladného přístupu) pro GZRS<br /><br />Nejméně 99,99 % (99,9 % pro úroveň chladného přístupu) pro RA-GZRS |
| SLA dostupnost pro požadavky na zápis<sup>1</sup>  | Alespoň 99,9 % (99 % pro úroveň chladného přístupu) | Alespoň 99,9 % (99 % pro úroveň chladného přístupu) | Alespoň 99,9 % (99 % pro úroveň chladného přístupu) | Alespoň 99,9 % (99 % pro úroveň chladného přístupu) |

<sup>1</sup> Informace o zárukách azure storage pro odolnost a dostupnost najdete v tématu [Azure Storage SLA](https://azure.microsoft.com/support/legal/sla/storage/).

<sup>2</sup> Informace o typech účtů úložiště najdete v [tématu Přehled účtu úložiště](storage-account-overview.md).

Všechna data pro všechny typy účtů úložiště a [všechny úrovně (včetně archivu)](../blobs/storage-blob-storage-tiers.md) jsou zkopírovány podle možnosti redundance pro účet úložiště. Objekty včetně objektů blob bloku, objektů BLOB, objektů BLOB stránky, front, tabulek a souborů se zkopírují.

Informace o cenách pro každou možnost redundance najdete v [tématu Ceny azure storage](https://azure.microsoft.com/pricing/details/storage/).

> [!NOTE]
> Azure Premium Disk Storage aktuálně podporuje jenom místně redundantní úložiště (LRS). Účty úložiště objektů blob bloku podporují místně redundantní úložiště (LRS) a zónově redundantní úložiště (ZRS) v určitých oblastech.

## <a name="data-integrity"></a>Integrita dat

Azure Storage pravidelně ověřuje integritu dat uložených pomocí cyklických kontrol redundance (CRC). Pokud je zjištěno poškození dat, je opraveno pomocí redundantních dat. Azure Storage také vypočítá kontrolní součty na všech síťových přenosů ke zjištění poškození datových paketů při ukládání nebo načítání dat.

## <a name="see-also"></a>Viz také

- [Kontrola vlastnosti Poslední čas synchronizace pro účet úložiště](last-sync-time-get.md)
- [Změna možnosti redundance pro účet úložiště](redundancy-migration.md)
- [Navrhování vysoce dostupných aplikací pomocí úložiště RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
- [Zotavení po havárii a převzetí služeb při selhání účtu (náhled)](storage-disaster-recovery-guidance.md)
