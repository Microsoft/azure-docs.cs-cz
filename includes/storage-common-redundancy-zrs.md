---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 86c301748b58e7642df9a738c70b4fe70be3310b
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39400004"
---
Zónově redundantní úložiště (ZRS) vaše data synchronně replikuje mezi tři úložnými clustery v jedné oblasti. Každý cluster úložiště je fyzicky oddělená od ostatních a je umístěn ve své vlastní zóně dostupnosti (AZ). Každá zóna dostupnosti a cluster ZRS obsahuje, je autonomní pomocí samostatných nástrojů a možností sítě.

Ukládání dat v účtu ZRS zajistí, že budete mít přístup a spravovat vaše data v případě, že zónu přestane být k dispozici. ZRS poskytuje vynikající výkon a nízkou latencí. ZRS poskytuje stejné [cíle škálovatelnosti](../articles/storage/common/storage-scalability-targets.md) jako [místně redundantní úložiště (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

Zvažte použití ZRS pro scénáře, které vyžadují silnou konzistenci, silné odolnost a vysokou dostupnost i v případě výpadku nebo přírodní katastrofě vykreslí oblastmi datového centra není k dispozici. ZRS poskytuje pro objekty úložiště alespoň 99,9999999999 % (12 9) průběhu daného roku.

Další informace o zónách dostupnosti najdete v tématu [Přehled zón dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="support-coverage-and-regional-availability"></a>Podpora je poskytována a dostupnosti v jednotlivých oblastech
ZRS v současné době podporuje standard [pro obecné účely v2 (GPv2)](../articles/storage/common/storage-account-options.md#general-purpose-v2-accounts) typy účtů. Zónově redundantní úložiště je k dispozici pro objekty BLOB bloku, objekty BLOB stránky bez disku, soubory, tabulky a fronty. Kromě toho všechny vaše [Storage Analytics](../articles/storage/common/storage-analytics.md) protokoly a [Storage Metrics](../articles/storage/common/storage-enable-and-view-metrics.md)

Zónově redundantní úložiště je obecně dostupná v těchto oblastech:

- USA – východ 2
- USA – západ 2
- USA – střed
- Severní Evropa
- Západní Evropa
- Francie – střed
- Jihovýchodní Asie

Microsoft nadále povolit ZRS v dalších oblastech Azure. Zkontrolujte [aktualizace služby Azure](https://azure.microsoft.com/updates/) stránky pravidelně informace o nové oblasti.

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>Co se stane, když se stane nedostupným zóny?

Vaše data zůstanou odolné pro případ nedostupnosti zóny. Společnost Microsoft doporučuje nadále postupy pro zpracování, jako je například implementace zásad opakování s exponenciální regresní přechodných chyb. Když zóna je k dispozici, Azure se zavazuje, že síťové aktualizace, jako je například DNS repointing. Tyto aktualizace může ovlivnit vaši aplikaci při přístupu k datům předtím, než byl dokončen.

ZRS nemůže chránit vaše data před regionální po havárii, ve kterém jsou několika zónami trvale vliv. Místo toho ZRS nabízí odolnost proti chybám pro vaše data v případě, že bude dočasně není k dispozici. Pro ochranu před katastrofami regionální společnost Microsoft doporučuje používat geograficky redundantní úložiště (GRS). Další informace o GRS najdete v tématu [geograficky redundantní úložiště (GRS): replikace mezi zónami pro službu Azure Storage](../articles/storage/common/storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>Převod na replikací zónově redundantního úložiště
V současné době můžete na webu Azure portal nebo rozhraní API poskytovatele prostředků úložiště chcete-li změnit typ redundance vašeho účtu, za předpokladu, migrujete do nebo z LRS, GRS a RA-GRS. S ZRS ale migrace není jako jednoznačné protože zahrnuje přesun fyzických dat z jednoho úložiště razítka do více razítek v rámci oblasti. 

Máte dvě primární možnosti pro migraci do nebo z ZRS. Můžete ručně zkopírovat nebo přesunout data do nového účtu ZRS z vašeho stávajícího účtu. Můžete také požádat o migraci za provozu. Společnost Microsoft důrazně doporučuje provést ruční migrace, protože neexistuje žádná záruka, kdy bude dokončena migrace za provozu. Ruční migraci trasa poskytuje větší flexibilitu než migrace za provozu nepodporuje a máte pod kontrolou načasování migrace.

Pokud chcete provést ruční migrace, máte různé možnosti:
- Použijte stávající nástroje, jako je AzCopy, úložiště, sady SDK, spolehlivé nástroje třetích stran atd.
- Pokud jste se seznámili s Hadoop nebo HDInsight, můžete připojit i zdroj a cíl (ZRS) účet ke svému clusteru a pomocí něčeho jako DistCp do masivně paralelní zpracování proces kopírování dat
- Vytváření vlastních nástrojů využívat jeden charakter úložiště sady SDK

Pokud ale ruční migraci způsobí výpadek aplikace a nemůžete chránit před, který dalšího, Microsoft nabízí možnost migrace za provozu. Migrace za provozu je místní migrace, která umožňuje pokračovat v používání existující účet úložiště, zatímco vaše data jsou migrována mezi zdrojovým a cílovým razítka úložiště. Během migrace bude stále mít stejnou úroveň odolnosti a smlouva SLA o dostupnosti obvyklým způsobem.

S určitými omezeními ale pocházet migrace za provozu. Jsou uvedeny níže.

- Když Microsoft bude zabývat vaše žádost o migraci za provozu okamžitě, neexistuje žádná záruka, kdy se migrace dokončí. Pokud potřebujete, aby vaše data, aby se v ZRS do určité doby, byste měli provést ruční migraci. Obecně platí, čím více dat mají ve vašem účtu tím déle bude trvat migrovat data. 
- Migrace za provozu mohou provést pouze z účtu služby pomocí replikace sazbou za LRS nebo GRS. Pokud váš účet používá RA-GRS, je potřeba nejdřív migrujte na jednu z těchto typů replikace, než budete pokračovat. Tento zprostředkující krok zajistí, že sekundární jen pro čtení koncový bod poskytující RA-GRS se odebere před migrací.
- Váš účet musí obsahovat data.
- Jsou podporovány pouze uvnitř oblasti migrace. Pokud chcete migrovat data do účtu ZRS nachází v jiné než účet zdrojové oblasti, je nutné provést ruční migraci.
- Jsou podporovány pouze typy účtů úložiště úrovně standard. Nemůžete migrovat z účtu služby premium storage.

Požadavky, projděte si portál podpory Azure migrace za provozu. Z portálu vyberte účet úložiště, který chcete převést na ZRS.
1. Klikněte na tlačítko **novou žádost o podporu**
2. Ověření základní informace. Klikněte na **Další**. 
3. Na **problém** části 
    - Ponechte závažnost jako-je.
    - Typ problému = **migrace dat**
    - Kategorie = **migrovat ZRS v rámci oblasti**
    - Název = **migrace účtu ZRS** (nebo něco popisné)
    - Podrobnosti = chci migrovat do ZRS z [LRS, GRS] v oblasti ___. 
4. Klikněte na **Další**.
5. Ověřte správnost kontaktní informace v okně kontaktní informace.
6. Klikněte na tlačítko **odeslat**.

Pracovník podpory pak bude vás budeme kontaktovat. Tato osoba bude k dispozici, kterou budete potřebovat pomoc. 

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZRS Classic: Starší verze požadované za účelem zajištění redundance objekty BLOB bloku
> [!NOTE]
> Účty ZRS Classic jsou plánovány pro vyřazení a vyžaduje migraci na 31. března 2021. Microsoft vám pošle další podrobnosti ZRS Classic zákazníkům před vyřazení. Microsoft má v plánu poskytovat procesu automatizovaný přenos z ZRS Classic ZRS v budoucnu.

>[!NOTE]
> Jakmile zónově redundantní úložiště je [obecně k dispozici](#support-coverage-and-regional-availability) v oblasti, se již budete moci vytvořit účet ZRS Classic na portálu v dané stejné oblasti. Však můžete stále vytvořit jeden prostřednictvím jiným způsobem, jako je Microsoft PowerShell a rozhraní příkazového řádku Azure, to znamená, dokud ZRS Classic je zastaralý.

ZRS Classic asynchronně replikuje data napříč datovými centry v jedné až dvou oblastech. Replika nemusí být k dispozici, pokud společnost Microsoft nezahájí převzetí služeb při selhání a přechod na sekundární data. ZRS Classic je k dispozici pouze pro **objekty BLOB bloku** v [pro obecné účely V1 (GPv1)](../articles/storage/common/storage-account-options.md#general-purpose-v1-accounts) účty úložiště. Účet ZRS Classic nelze převést na LRS nebo GRS ani naopak a nepodporuje možnosti metrik ani protokolování.

Účty ZRS Classic nelze převést na nebo z LRS, GRS nebo RA-GRS. Účty ZRS Classic také nepodporují protokolování nebo metrik.

Jak ručně provést migraci dat účtu ZRS do nebo z účet LRS, ZRS Classic, GRS nebo RA-GRS, použijte AzCopy, Průzkumníka služby Azure Storage, Azure Powershellu nebo rozhraní příkazového řádku Azure. Můžete také vytvořit svoje vlastní řešení migrace s jedním z klientských knihoven pro úložiště Azure.
