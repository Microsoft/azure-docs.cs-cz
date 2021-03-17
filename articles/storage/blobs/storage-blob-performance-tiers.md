---
title: Zablokovat úroveň výkonu úložiště objektů blob – Azure Storage
description: Tento článek popisuje rozdíl mezi úrovněmi výkonu úrovně Premium a standard pro úložiště objektů blob bloku Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: cf0b5d29e0dc375a07fe024ef0763c8200275055
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880704"
---
# <a name="performance-tiers-for-block-blob-storage"></a>Úrovně výkonu úložiště objektů blob bloku

Vzhledem k tomu, že podniky nasazují výkonně citlivé aplikace v cloudu, je důležité mít možnosti pro nákladově efektivní úložiště dat na různých úrovních výkonu.

Azure Block BLOB Storage nabízí dvě různé úrovně výkonu:

- **Premium**: optimalizováno pro vysoké sazby za transakce a latence úložiště s jednou číslicí
- **Standard**: optimalizováno pro vysokou kapacitu a vysokou propustnost

Následující požadavky platí pro různé úrovně výkonu:

| Plošný |Standardní výkon  |Výkon úrovně Premium  |
|---------|---------|---------|
|Dostupnost v oblastech     |   Všechny oblasti      | Ve [vybraných oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=storage)       |
|Podporované [typy účtů úložiště](../common/storage-account-overview.md#types-of-storage-accounts)     |     Obecné účely v2, BlobStorage, obecné účely v1    |    BlockBlobStorage     |
|Podporuje [objekty blob bloku s vysokou propustností](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) .     |    Ano, ve více než 4 MiB PutBlock nebo PutBlob velikosti     |    Ano, s více než 256 KiB PutBlock nebo velikosti PutBlob    |
|Redundance     |     Zobrazit [typy účtů úložiště](../common/storage-account-overview.md#types-of-storage-accounts)   |  V současné době podporuje jenom místně redundantní úložiště (LRS) a úložiště redudant (ZRS).<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1</sup> Zóna – redundantní úložiště (ZRS) je k dispozici ve vybraných oblastech pro účty úložiště blob bloku Premium.</div>

S ohledem na náklady poskytuje Premium Performance optimalizované ceny pro aplikace s vysokými nároky na transakce, které vám pomůžou [snížit celkové náklady na úložiště](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) pro tyto úlohy.

## <a name="premium-performance"></a>Výkon úrovně Premium

Úložiště objektů blob bloku úrovně Premium zpřístupňuje data prostřednictvím vysoce výkonného hardwaru. Data se ukládají na jednotky SSD (SSD), které jsou optimalizované pro nízkou latenci. SSD poskytují vyšší propustnost v porovnání s tradičními pevnými disky.

Úložiště výkonu Premium je ideální pro úlohy, které vyžadují rychlou a konzistentní dobu odezvy. Je nejvhodnější pro úlohy, které provádějí mnoho malých transakcí. Příklady úloh zahrnují:

- **Interaktivní úlohy**. Tyto úlohy vyžadují okamžité aktualizace a zpětnou vazbu od uživatelů, například elektronické obchodování a mapování aplikací. Například v aplikaci elektronického obchodování nejspíš méně často zobrazené položky neukládá do mezipaměti. Musí se ale okamžitě zobrazit zákazníkovi na vyžádání.

- **Analýzy**. Ve scénáři IoT může být do cloudu každou sekundu vložená spousta menších operací zápisu. Velké objemy dat může být v, agregované pro účely analýzy a následně odstraněny téměř okamžitě. Možnosti zajištění vysokého příjmu pro úložiště objektů blob bloku úrovně Premium je pro tento typ úlohy efektivní.

- **Umělá logika/strojové učení (AI/ml)**. AI/ML se zabývá spotřebou a zpracováním různých datových typů, jako jsou vizuály, řeč a text. Tento vysoce výkonný výpočetní typ úloh se zabývá velkými objemy dat, která pro analýzu dat vyžaduje rychlou odezvu a efektivní dobu přijímání.

- **Transformace dat**. Procesy, které vyžadují neustálou úpravu, úpravu a převod dat, vyžadují okamžité aktualizace. V případě přesné reprezentace dat musí příjemci těchto dat vidět, že se tyto změny projeví okamžitě.

## <a name="standard-performance"></a>Standardní výkon

Standardní výkon podporuje různé [úrovně přístupu](storage-blob-storage-tiers.md) , aby bylo možné data ukládat nejefektivnějším způsobem. Je optimalizovaná pro vysokou kapacitu a vysokou propustnost u velkých datových sad.

- **Datové sady pro zálohování a zotavení po havárii**. Standardní úložiště výkonu nabízí nákladově efektivní úrovně, které představují dokonalý případ použití pro krátkodobé i dlouhodobé datové sady pro zotavení po havárii, sekundární zálohování a archivaci dat dodržování předpisů.

- **Mediální obsah**. K obrázkům a videím se často přistupuje často při prvním vytvoření a uložení, ale tento typ obsahu se používá méně často, jako je starší. Standardní úložiště výkonu nabízí vhodné úrovně pro potřeby mediálního obsahu. 

- **Hromadné zpracování dat**. Tyto typy úloh jsou vhodné pro úložiště úrovně Standard, protože vyžadují nákladově efektivní úložiště s vysokou propustností, místo aby se shodovala s nízkou latencí. Velké a nezpracované datové sady jsou připravené ke zpracování a nakonec se migrují na úrovně chladicích souborů.

## <a name="migrate-from-standard-to-premium"></a>Migrace z úrovně Standard na Premium

Stávající standardní účet úložiště výkonu nemůžete převést na účet úložiště objektů blob bloku s výkonem Premium. K migraci na účet úložiště s výkonem Premium je potřeba vytvořit účet BlockBlobStorage a migrovat data na nový účet. Další informace najdete v tématu [Vytvoření účtu BlockBlobStorage](storage-blob-create-account-block-blob.md).

K kopírování objektů BLOB mezi účty úložiště můžete použít nejnovější verzi nástroje příkazového řádku [AzCopy](../common/storage-use-azcopy-v10.md#transfer-data) . Pro přesun a transformaci dat jsou k dispozici také jiné nástroje, například Azure Data Factory.

## <a name="blob-lifecycle-management"></a>Správa životního cyklu objektů BLOB

Správa životního cyklu úložiště objektů BLOB nabízí bohatou zásadu založenou na pravidlech:

- **Premium**: vypršení platnosti dat na konci svého životního cyklu.
- **Standard**: převeďte data na nejlepší úroveň přístupu a data vypršení platnosti na konci svého životního cyklu.

Další informace najdete v tématu [Správa životního cyklu služby Azure Blob Storage](storage-lifecycle-management-concepts.md).

Nemůžete přesouvat data uložená v účtu úložiště blob bloku úrovně Premium mezi horkou, studenou a archivní úrovní. Objekty blob můžete ale kopírovat z účtu bloku BLOB Storage do úrovně Hot Access v *jiném* účtu. Pokud chcete kopírovat data do jiného účtu, použijte [blok Put z URL](/rest/api/storageservices/put-block-from-url) API nebo [AzCopy v10 za účelem](../common/storage-use-azcopy-v10.md). **Blok Put z** rozhraní API URL synchronně kopíruje data na serveru. Volání se dokončí až po přesunu všech dat z původního umístění serveru do cílového umístění.

## <a name="next-steps"></a>Další kroky

Vyhodnoťte horkou, studenou a archivní v účtech GPv2 a BLOB Storage.

- [Přečtěte si informace o prodehydratacích dat objektů BLOB z archivní úrovně.](storage-blob-rehydration.md)
- [Zapnutí metrik Azure Storage a vyhodnocení používání aktuálních účtů úložiště](./monitor-blob-storage.md)
- [Ověření cen horké, studené a archivní úrovně v účtech Blob Storage a GPv2 v jednotlivých oblastech](https://azure.microsoft.com/pricing/details/storage/)
- [Posouzení cen přenosu dat](https://azure.microsoft.com/pricing/details/data-transfers/)
