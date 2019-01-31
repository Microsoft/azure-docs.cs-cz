---
title: Vytvářet vysoce dostupné aplikace služby Azure Storage na zónově redundantní úložiště (ZRS) | Dokumentace Microsoftu
description: Zónově redundantní úložiště (ZRS) poskytuje jednoduchý způsob, jak vytvářet vysoce dostupné aplikace. ZRS chrání před selháním hardwaru v datovém centru a před katastrofami některé oblasti.
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 10/24/2018
ms.author: jeking
ms.subservice: common
ms.openlocfilehash: 62c6e24776c1d9a4e6a82eea557244bb390ebd00
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55471483"
---
# <a name="zone-redundant-storage-zrs-highly-available-azure-storage-applications"></a>Zónově redundantní úložiště (ZRS): Vysoce dostupné aplikace služby Azure Storage
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>Podpora je poskytována a dostupnosti v jednotlivých oblastech
ZRS aktuálně podporuje typy účtů standard pro obecné účely v2. Další informace o typech účtů úložiště najdete v tématu [přehled účtu Azure storage](storage-account-overview.md).

Zónově redundantní úložiště je k dispozici pro objekty BLOB bloku, objekty BLOB stránky bez disku, soubory, tabulky a fronty.

Zónově redundantní úložiště je obecně dostupná v těchto oblastech:

- Jihovýchodní Asie
- Evropa – západ
- Evropa – sever
- Francie – střed
- Japonsko – východ
- USA – východ
- USA – východ 2
- USA – západ 2
- USA – střed

Microsoft nadále povolit ZRS v dalších oblastech Azure. Zkontrolujte [aktualizace služby Azure](https://azure.microsoft.com/updates/) stránky pravidelně informace o nové oblasti.

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>Co se stane, když se stane nedostupným zóny?
Vaše data jsou stále přístupné pro čtení i zápisu operace i pokud zónu přestane být k dispozici. Společnost Microsoft doporučuje nadále postupy pro zpracování přechodných chyb. Tyto postupy zahrnují implementovat zásady opakování s exponenciální regrese.

Když zóna je k dispozici, Azure se zavazuje, že síťové aktualizace, jako je například DNS repointing. Tyto aktualizace může ovlivnit vaši aplikaci při přístupu k datům před dokončení aktualizace.

ZRS nemůže chránit vaše data před regionální po havárii, ve kterém jsou několika zónami trvale vliv. Místo toho ZRS nabízí odolnost proti chybám pro vaše data v případě, že bude dočasně není k dispozici. Pro ochranu před katastrofami regionální společnost Microsoft doporučuje používat geograficky redundantní úložiště (GRS). Další informace o GRS najdete v tématu [geograficky redundantní úložiště (GRS): Replikace mezi zónami pro službu Azure Storage](storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>Převod na replikací zónově redundantního úložiště
Migrace do nebo z LRS, GRS a RA-GRS je jednoduché. Pomocí webu Azure portal nebo rozhraní API poskytovatele prostředků úložiště můžete změnit typ redundance vašeho účtu. Azure bude odpovídajícím způsobem replikovat data. 

Migrace dat do nebo z ZRS vyžaduje jinou strategii. ZRS migrace zahrnuje fyzické přesun dat z razítka s jediným úložištěm do několika razítek v rámci oblasti.

Existují dvě primární možnosti pro migraci do nebo z ZRS: 

- Ručně zkopírovat nebo přesunout data do nového účtu ZRS z existující účet.
- Požádat o migraci za provozu.

Společnost Microsoft důrazně doporučuje provést ruční migraci. Ruční migraci přináší více flexibility než migrace za provozu. Ruční migraci máte pod kontrolou načasování.

Pokud chcete provést ruční migrace, máte možnosti:
- Použijte stávající nástroje, jako je AzCopy, jednu z knihoven klienta služby Azure Storage a spolehlivé nástroje třetích stran.
- Pokud jste obeznámeni s Hadoop nebo HDInsight, připojte zdrojový i cílový (ZRS) účet ke svému clusteru. Potom paralelizovat proces kopírování dat pomocí některého nástroje, například DistCp.
- Vytváření vlastních nástrojů pomocí jedné z klientských knihoven pro úložiště Azure.

Ruční migraci může způsobit výpadky aplikací. Pokud vaše aplikace vyžaduje vysokou dostupnost, Microsoft taky nabízí možnost migrace za provozu. Migrace za provozu je místní migrace. 

Během migrace za provozu můžete použít svůj účet úložiště při migraci dat mezi zdrojovou a cílovou razítka úložiště. Během procesu migrace dosahovat stejné úrovně smlouva SLA o dostupnosti a odolnosti obvyklým způsobem.

Mějte na paměti následující omezení migrace za provozu:

- Když Microsoft zpracuje vaše žádost o migraci za provozu okamžitě, neexistuje žádná záruka, kdy bude dokončena migrace za provozu. Pokud potřebujete data migrovat do ZRS do určitého data a pak společnost Microsoft doporučuje místo toho proveďte ruční migraci. Obecně platí, čím více dat mají ve vašem účtu, tím déle trvá migrovat data. 
- Migrace za provozu je podporována pouze pro účty úložiště LRS nebo GRS replikaci. Pokud váš účet používá RA-GRS, budete muset nejprve změňte typ replikace váš účet LRS nebo GRS než budete pokračovat. Tento krok zprostředkující odebere sekundárního koncového bodu jen pro čtení, poskytuje RA-GRS před migrací.
- Váš účet musí obsahovat data.
- Můžete migrovat pouze data v rámci stejné oblasti. Pokud chcete migrovat data do účtu ZRS nachází v jiné než účet zdrojové oblasti, je nutné provést ruční migraci.
- Pouze typy účtů úložiště úrovně standard podporují migraci za provozu. Účty služby Premium storage je potřeba migrovat ručně.

Můžete požádat o migraci za provozu prostřednictvím [portál podpory Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). Z portálu vyberte účet úložiště, který chcete převést na ZRS.
1. Vyberte **novou žádost o podporu**
2. Dokončení **Základy** podle informací o vašem účtu. V **služby** vyberte **správu účtu úložiště** a prostředků, kterou chcete převést na ZRS. 
3. Vyberte **Další**. 
4. Zadejte následující hodnoty **problém** části: 
    - **Závažnost**: Ponechte výchozí hodnotu jako-je.
    - **Typ problému**: Vyberte **migrace dat**.
    - **Kategorie**: Vyberte **migrovat v rámci oblasti ZRS**.
    - **Název**: Zadejte popisný název, například **migrace účtu ZRS**.
    - **Podrobnosti o**: Zadejte další podrobnosti **podrobnosti** pole, například chci migrovat do ZRS z [LRS, GRS] v \_ \_ oblasti. 
5. Vyberte **Další**.
6. Ověřte správnost kontaktní informace na **kontaktní informace** okno.
7. Vyberte **Vytvořit**.

Pracovník podpory bude vás kontaktovat a poskytnout pomoc, které potřebujete. 

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZRS Classic: Starší verze možnost redundance objekty BLOB bloku
> [!NOTE]
> Společnost Microsoft se přestat používat a přenést do 31. března 2021 účty ZRS Classic. Další podrobnosti vám poskytneme ZRS Classic zákazníků před vyřazení. 
>
> Jakmile bude ZRS [obecně k dispozici](#support-coverage-and-regional-availability) v oblasti a zákazníci nebudou moci vytvořit účty ZRS Classic na portálu v dané oblasti. Použití Microsoft PowerShell a rozhraní příkazového řádku Azure k vytvoření účty ZRS Classic je možné, dokud ZRS Classic je zastaralý.

ZRS Classic asynchronně replikuje data napříč datovými centry v jedné až dvou oblastech. Replikovaná data nemusí být k dispozici, pokud Microsoft nezahájí převzetí služeb při selhání do sekundární. Účet ZRS Classic nelze převést na nebo z LRS, GRS nebo RA-GRS. Účty ZRS Classic také nepodporují protokolování nebo metrik.

ZRS Classic je k dispozici pouze pro **objekty BLOB bloku** v pro obecné účely V1 (GPv1) účty úložiště. Další informace o účtech úložiště najdete v [přehledu účtu Azure Storage](storage-account-overview.md).

Jak ručně provést migraci dat účtu ZRS do nebo z účet LRS, ZRS Classic, GRS nebo RA-GRS, použijte jednu z následujících nástrojů: AzCopy, Průzkumníka služby Azure Storage, Azure Powershellu nebo Azure CLI. Můžete také vytvořit svoje vlastní řešení migrace s jedním z klientských knihoven pro úložiště Azure.

## <a name="see-also"></a>Další informace najdete v tématech
- [Účet replikace Azure Storage](storage-redundancy.md)
- [Místně redundantní úložiště (LRS): Redundanci dat s nízkými náklady pro službu Azure Storage](storage-redundancy-lrs.md)
- [Geograficky redundantní úložiště (GRS): Replikace mezi zónami pro službu Azure Storage](storage-redundancy-grs.md)
