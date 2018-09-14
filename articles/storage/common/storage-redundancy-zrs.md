---
title: Vytvářet vysoce dostupné aplikace služby Azure Storage na zónově redundantní úložiště (ZRS) | Dokumentace Microsoftu
description: Zónově redundantní úložiště (ZRS) poskytuje jednoduchý způsob, jak vytvářet vysoce dostupné aplikace. ZRS chrání před selháním hardwaru v datovém centru a před katastrofami některé oblasti.
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 03/20/2018
ms.author: jeking
ms.component: common
ms.openlocfilehash: dfd5058b17a211adf97eb8729158b2b65d7b6975
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45579509"
---
# <a name="zone-redundant-storage-zrs-highly-available-azure-storage-applications"></a>Zónově redundantní úložiště (ZRS): vysoce dostupné aplikace služby Azure Storage
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>Podpora je poskytována a dostupnosti v jednotlivých oblastech
ZRS v současné době podporuje standard [pro obecné účely v2 (GPv2)](storage-account-options.md#general-purpose-v2-accounts) typy účtů. Zónově redundantní úložiště je k dispozici pro objekty BLOB bloku, objekty BLOB stránky bez disku, soubory, tabulky a fronty. Kromě toho všechny vaše [Storage Analytics](storage-analytics.md) protokoly a [Storage Metrics](storage-enable-and-view-metrics.md)

Zónově redundantní úložiště je obecně dostupná v těchto oblastech:

- USA – východ
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

ZRS nemůže chránit vaše data před regionální po havárii, ve kterém jsou několika zónami trvale vliv. Místo toho ZRS nabízí odolnost proti chybám pro vaše data v případě, že bude dočasně není k dispozici. Pro ochranu před katastrofami regionální společnost Microsoft doporučuje používat geograficky redundantní úložiště (GRS). Další informace o GRS najdete v tématu [geograficky redundantní úložiště (GRS): replikace mezi zónami pro službu Azure Storage](storage-redundancy-grs.md).

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
> Společnost Microsoft se přestat používat a přenést do 31. března 2021 účty ZRS Classic. Další podrobnosti vám poskytneme ZRS Classic zákazníků před vyřazení. 
>
> Když se stane ZRS [obecně k dispozici](#support-coverage-and-regional-availability) v oblasti, zákazníci už budou moci vytvořit účty ZRS Classic na portálu v dané oblasti. Použití Microsoft PowerShell a rozhraní příkazového řádku Azure k vytvoření účty ZRS Classic je podporována, dokud ZRS Classic je zastaralý.

ZRS Classic asynchronně replikuje data napříč datovými centry v jedné až dvou oblastech. Replika nemusí být k dispozici, pokud společnost Microsoft nezahájí převzetí služeb při selhání a přechod na sekundární data. ZRS Classic je k dispozici pouze pro **objekty BLOB bloku** v [pro obecné účely V1 (GPv1)](storage-account-options.md#general-purpose-v1-accounts) účty úložiště. Účet ZRS Classic nelze převést na LRS nebo GRS ani naopak a nepodporuje možnosti metrik ani protokolování.

Účty ZRS Classic nelze převést na nebo z LRS, GRS nebo RA-GRS. Účty ZRS Classic také nepodporují protokolování nebo metrik.

Jak ručně provést migraci dat účtu ZRS do nebo z účet LRS, ZRS Classic, GRS nebo RA-GRS, použijte AzCopy, Průzkumníka služby Azure Storage, Azure Powershellu nebo rozhraní příkazového řádku Azure. Můžete také vytvořit svoje vlastní řešení migrace s jedním z klientských knihoven pro úložiště Azure.

## <a name="see-also"></a>Další informace najdete v tématech
- [Účet replikace Azure Storage](storage-redundancy.md)
- [Místně redundantní úložiště (LRS): redundanci dat s nízkými náklady pro službu Azure Storage](storage-redundancy-lrs.md)
- [Geograficky redundantní úložiště (GRS): replikace mezi zónami pro službu Azure Storage](storage-redundancy-grs.md)