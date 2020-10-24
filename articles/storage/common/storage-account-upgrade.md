---
title: Upgrade na účet úložiště pro obecné účely verze 2
titleSuffix: Azure Storage
description: Upgradujte účty úložiště pro obecné účely v2 pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure. Zadejte úroveň přístupu pro data objektu BLOB.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/25/2019
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 34b486dc4ccfb116b49cf57217c5b7e5aedbd992
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488839"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Upgrade na účet úložiště pro obecné účely verze 2

Účty úložiště pro obecné účely v2 podporují nejnovější funkce Azure Storage a zahrnují všechny funkce účtů pro obecné účely V1 a BLOB Storage. Pro většinu scénářů úložišť se doporučuje používat účty pro obecné účely v2. Účty pro obecné účely v2 poskytují nejnižší cenu za GB pro Azure Storage a také ceny za transakce z odvětví konkurenčních produktů. Účty pro obecné účely v2 podporují úrovně přístupu na úrovni Standard, horké nebo studené a převrstvení objektů BLOB mezi horkou, studenou nebo archivní.

Upgrade na účet úložiště pro obecné účely v2 z účtů pro obecné účely v1 nebo BLOB Storage je jednoduchý. Můžete upgradovat pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure CLI. Neexistuje žádné výpadky nebo riziko ztráty dat související s upgradem na účet úložiště pro obecné účely v2. K upgradu účtu dochází prostřednictvím jednoduché operace Azure Resource Manager, která mění typ účtu.

> [!IMPORTANT]
> Upgrade účtu úložiště pro obecné účely v1 nebo blob na obecné účely je trvalý a nedá se vrátit zpátky.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Přejděte na svůj účet úložiště.
3. V části **Nastavení** klikněte na **Konfigurace**.
4. V části **Druh účtu** klikněte na **Upgradovat**.
5. V části **Potvrdit upgrade** zadejte název svého účtu.
6. V dolní části okna klikněte na **upgradovat** .

    ![Upgradovat druh účtu](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pokud chcete upgradovat účet pro obecné účely V1 na účet pro obecné účely v2 pomocí PowerShellu, nejdřív aktualizujte PowerShell tak, aby používal nejnovější verzi modulu **AZ. Storage** . Informace o instalaci PowerShellu najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-Az-ps).

Potom zavolejte následující příkaz pro upgrade účtu, kde nahradíte název skupiny prostředků, název účtu úložiště a požadovanou úroveň přístupu k účtu.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2 -AccessTier <Hot/Cool>
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete upgradovat účet pro obecné účely V1 na účet pro obecné účely v2 pomocí Azure CLI, nejdřív nainstalujte nejnovější verzi rozhraní příkazového řádku Azure CLI. Informace o instalaci rozhraní příkazového řádku najdete v tématu [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli).

Potom zavolejte následující příkaz pro upgrade účtu, kde nahradíte název skupiny prostředků, název účtu úložiště a požadovanou úroveň přístupu k účtu.

```azurecli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2 --access-tier=<Hot/Cool>
```

---

## <a name="specify-an-access-tier-for-blob-data"></a>Zadejte úroveň přístupu pro data objektů BLOB.

Účty pro obecné účely v2 podporují všechny služby úložiště Azure a datové objekty, ale úrovně přístupu jsou dostupné jenom pro objekty blob bloku v úložišti objektů BLOB. Když provedete upgrade na účet úložiště pro obecné účely v2, můžete zadat výchozí úroveň přístupu k účtu horké nebo studené, která indikuje výchozí úroveň vašich dat objektů blob, jako by nebyl zadán individuální parametr úrovně přístupu objektu BLOB.

Úrovně přístupu k objektu BLOB umožňují zvolit nejúčinnější úložiště na základě předpokládaných způsobů použití. Objekty blob bloku se můžou ukládat na horkou, studenou nebo archivní úroveň. Další informace o úrovních přístupu najdete v tématu [Azure Blob Storage: horká, studená a archivní úroveň úložiště](../blobs/storage-blob-storage-tiers.md).

Ve výchozím nastavení se na úrovni Hot Accessu vytvoří nový účet úložiště a účet úložiště pro obecné účely V1 se dá upgradovat na jednu nebo studenou úroveň účtu. Pokud při upgradu není zadaná úroveň přístupu k účtu, ve výchozím nastavení se upgraduje na Hot. Pokud zkoumáte úroveň přístupu, která se má použít pro upgrade, vezměte v úvahu svůj scénář využití svých dat. Existují dva typické scénáře uživatelů pro migraci na účet pro obecné účely v2:

* Máte existující účet úložiště pro obecné účely V1 a chcete vyhodnotit upgrade na účet úložiště pro obecné účely v2 se správnou úrovní přístupu úložiště pro data objektů BLOB.
* Rozhodli jste se použít účet úložiště pro obecné účely verze 2 nebo ho už mít a chcete vyhodnotit, jestli byste měli pro data BLOB použít horkou nebo studenou úroveň přístupu.

V obou případech má první priorita odhadnout náklady na ukládání, přístup a provoz na vašich datech uložených v účtu úložiště pro obecné účely v2 a porovnat je s vašimi aktuálními náklady.

## <a name="pricing-and-billing"></a>Ceny a fakturace

Upgrade účtu úložiště V1 na účet pro obecné účely v2 je zdarma. Během procesu upgradu můžete určit požadovanou úroveň účtu. Pokud při upgradu není zadána úroveň účtu, bude použita výchozí úroveň účtu upgradovaného účtu `Hot` . Změna úrovně přístupu úložiště ale může mít za následek změny ve vyúčtování, takže se při upgradu doporučuje zadat novou úroveň účtu.

Všechny účty úložiště vycházejí z cenového modelu úložiště objektů blob založeného na úrovních jednotlivých objektů blob. Při použití účtu úložiště je potřeba vzít v úvahu tyto fakturační podmínky:

* **Náklady na úložiště**: Kromě množství uložených dat se cena za ukládání dat liší v závislosti na úrovni přístupu úložiště. Pokud je úroveň chladnější, cena za gigabajt se snižuje.

* **Cena za přístup k datům:** Pokud je úroveň chladnější, cena za přístup k datům se zvyšuje. Pro data ve studené a archivní úrovni přístupového úložiště se účtují poplatky za přístup k datům za gigabajt.

* **Cena za transakce:** Pro všechny úrovně se účtuje poplatek za transakce, který se pro chladnější úrovně zvyšuje.

* **Cena za přenosy dat geografické replikace:** Tento poplatek se vztahuje jen na účty s nastavenou geografickou replikací, jako třeba GRS a RA-GRS. Přenos dat geografické replikace je zpoplatněný podle sazby za GB.

* **Cena za odchozí přenosy dat**: Odchozí přenosy dat (dat přenesených směrem z oblasti Azure) jsou zpoplatněné podle využití šířky pásma sazbou za GB, stejně jako je tomu u účtů úložiště pro obecné účely.

* **Změna úrovně přístupu úložiště**: Změna úrovně přístupu k úložišti účtů ze studené na horkou má poplatek, který se rovná čtení všech dat existujících v účtu úložiště. Změna úrovně přístupu k účtu z horké na studenou se ale účtuje za poplatek, který se rovná zápisu všech dat do studené úrovně (pouze účty GPv2).

> [!NOTE]
> Další informace o cenovém modelu pro účty úložišť najdete na stránce [Ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/). Další informace o poplatcích za odchozí přenosy dat najdete na stránce [Podrobné informace o cenách přenosů dat](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Odhad nákladů na vaše aktuální vzory využití

Chcete-li odhadnout náklady na ukládání a přístup k datům objektů BLOB v účtu úložiště pro obecné účely V2 v konkrétní úrovni, vyhodnoťte stávající vzor použití nebo zvažte očekávaný vzor využití. Celkově vzato potřebujete vědět:

* Spotřeba úložiště objektů BLOB (v gigabajtech), včetně:
  * Kolik dat se v účtu úložiště ukládá?
  * Jak se mění objem dat měsíčně? Nahrazují nová data neustále stará data?

* Základní vzor přístupu pro data služby Blob Storage, včetně:
  * Kolik dat se čte a zapisuje do účtu úložiště?
  * Kolik operací čtení a operací zápisu dochází k datům v účtu úložiště?

Abyste se rozhodli, jak úroveň přístupu vyhovuje vašim potřebám, může být užitečné určit kapacitu vašich dat objektů BLOB a způsob, jakým se data používají. To se dá nejlépe udělat tím, že si prohlížíte metriky monitorování pro váš účet.

### <a name="monitoring-existing-storage-accounts"></a>Monitorování existujících účtů úložiště

K monitorování existujících účtů úložiště a sesbírání dat můžete využít službu Azure Storage Analytics, která provádí protokolování a poskytuje data metriky pro účet úložiště. Analýza úložiště může pro účty úložiště typu GPv1, GPv2 a Blob ukládat metriky, včetně souhrnné statistiky transakcí a dat o kapacitě požadavků na službu úložiště. Tato data se ukládají do známých tabulek na tom samém účtu úložiště.

Další informace najdete na stránkách věnovaných [metrikám Analýzy úložiště](https://msdn.microsoft.com/library/azure/hh343258.aspx) a [tabulkovému schématu metrik Analýzy úložiště](https://msdn.microsoft.com/library/azure/hh343264.aspx).

> [!NOTE]
> Účty úložiště Blob zpřístupňují koncový bod služby Table service pouze pro účely ukládání a zpřístupnění dat metrik pro tento účet.

Pokud chcete monitorovat využití úložiště pro účet úložiště Blob, je potřeba povolit metriky kapacity.
Když tuto funkci zapnete, data o kapacitě služby Blob service pro daný účet úložiště se budou denně zaznamenávat jako zápisy do tabulky *$MetricsCapacityBlob* v rámci stejného účtu úložiště.

Aby bylo možné pro účet úložiště Blob monitorovat vzory přístupu k datům, je potřeba povolit hodinovou metriku transakcí z rozhraní API. Když povolíte hodinovou metriku transakcí, data o transakcích rozhraní API se budou každou hodinu shromažďovat a zaznamenávat jako zápisy do tabulky *$MetricsHourPrimaryTransactionsBlob* v rámci stejného účtu úložiště. Při použití účtů úložiště RA-GRS zaznamenává tabulka *$MetricsHourSecondaryTransactionsBlob* transakce do sekundárního koncového bodu.

> [!NOTE]
> Pokud máte účet úložiště pro obecné účely, ve kterém jsou uložené objekty blob stránky a disky virtuálních počítačů, případně fronty, soubory, nebo tabulky, vedle dat objektů blob bloku a doplňovacích objektů blob, odhad tímto postupem provést nepůjde. Data o kapacitě nerozlišují objekty blob bloku od ostatních typů a neposkytují data o kapacitě pro ostatní typy dat. Pokud používáte tyto typy, můžete se podívat na množství na nejnovějším vyúčtování.

Pokud chcete dobře odhadnout spotřebu dat a přístup k nim, doporučujeme pro měření dat vybrat takovou dobu uchování, která vystihuje pravidelné používání, a potom údaje extrapolovat. Můžete například měřená data uchovávat po sedm dní, sesbírat jednou za týden a analyzovat je na konci měsíce. Nebo změřte a nasbírejte data za posledních 30 dní a na konci 30denního období je analyzujte.

Podrobnosti o povolení, shromažďování a zobrazování dat metrik najdete v tématu [metriky služby Storage Analytics](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Uložení, zobrazování a stahování analyzovaných data se účtuje stejně jako běžná uživatelská data.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Odhadnutí nákladů s pomocí naměřených údajů o využití

#### <a name="capacity-costs"></a>Náklady na kapacitu

Poslední položka v tabulce kapacitní metriky *$MetricsCapacityBlob* s klíčem řádku *data* zobrazuje kapacitu úložiště spotřebovanou uživatelskými daty. Poslední položka v tabulce kapacitní metriky *$MetricsCapacityBlob* s klíčem řádku *analytics* zobrazuje kapacitu úložiště spotřebovanou protokoly analýzy.

Celková kapacita spotřebovaná uživatelskými daty a protokoly analýzy (pokud jsou povoleny) se potom dají použít k odhadu nákladů za uložení dat v účtu úložiště. Stejnou metodu je možné použít také pro odhad nákladů na úložiště v účtech úložiště GPv1.

#### <a name="transaction-costs"></a>Cena za transakce

Součet *TotalBillableRequests* všech položek rozhraní API v tabulce metrik transakcí udává celkový počet transakcí daného rozhraní API. *Například* celkový počet transakcí *GetBlob* v daném časovém období se dá vypočítat jako celkový součet fakturovatelných požadavků všech položek s klíčem řádku *user;GetBlob*.

Pokud chcete pro účet Blob Storage odhadnout náklady za transakce, je potřeba rozdělit transakce do tří skupin, protože se cenově liší.

* Transakce zápisu jako *PutBlob*, *PutBlock*, *PutBlockList*, *AppendBlock*, *ListBlobs*, *ListContainers*, *CreateContainer*, *SnapshotBlob* a *CopyBlob*.
* Transakce odstranění jako *DeleteBlob* a *DeleteContainer*.
* Veškeré ostatní transakce.

Pokud chcete odhadnout náklady na transakce pro účet úložiště GPv1, je potřeba započítat všechny transakce bez ohledu na operaci nebo rozhraní API.

#### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Přístup k datům a cena za přenos dat – geografická replikace

Analýza úložiště sice k účtu úložiště nevypíše množství přečtených a zapsaných dat, toto množství lze ale zhruba odhadnout z tabulky metriky transakcí. Součet *TotalBillableRequests* všech položek rozhraní API v tabulce metrik transakcí udává celkové množství příchozích dat k tomuto rozhraní API v bajtech. Podobně součet *TotalEgress* udává celkové množství odchozích dat v bajtech.

Pokud chcete pro účet Blob Storage odhadnout náklady za přístup k datům, je potřeba transakce rozdělit do dvou skupin.

* Množství dat načtených z účtu úložiště lze odhadnout ze součtu *TotalEgress* především u operací *GetBlob* a *CopyBlob*.

* Množství dat zapsaných do účtu úložiště lze odhadnout ze součtu *TotalIngress* především u operací *PutBlob*, *PutBlock*, *CopyBlob* a *AppendBlock*.

Také cena za přenos geograficky replikovaných dat účtů Blob Storage se v případě účtu úložiště typu GRS nebo RA-GRS dá vypočítat pomocí toho, že odhadnete množství zapsaných dat.

> [!NOTE]
> Podrobnější příklad výpočtu nákladů na používání horké nebo studené úrovně přístupu najdete v nejčastějších dotazech s podrobnostmi o tom, *co jsou horká a studená úroveň přístupu a jak určit, která z nich se má použít?* na stránce [Ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Další kroky

* [Vytvoření účtu úložiště](storage-account-create.md)
