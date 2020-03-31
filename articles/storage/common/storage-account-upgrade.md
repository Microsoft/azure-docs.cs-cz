---
title: Upgrade na účet úložiště pro obecné účely verze 2
titleSuffix: Azure Storage
description: Upgradujte na účty úložiště pro obecné účely v2.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/25/2019
ms.author: tamram
ms.openlocfilehash: 9afbade408d6f95fcd3a61aa1ba65bc09c7a875b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067218"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Upgrade na účet úložiště pro obecné účely verze 2

Účty úložiště pro obecné účely v2 podporují nejnovější funkce Azure Storage a zahrnují všechny funkce účtů úložiště pro obecné účely v1 a Blob. Účty pro obecné účely v2 se doporučují pro většinu scénářů úložiště. Účty pro obecné účely v2 poskytují nejnižší ceny kapacity za gigabajt pro Azure Storage, stejně jako ceny transakcí konkurenceschopné v oboru. Účty pro obecné účely v2 podporují výchozí úrovně přístupu k účtu horké nebo studené a úrovně objektů blob mezi horkou, studenou nebo archivací.

Upgrade na účet úložiště pro obecné účely v2 z vašich účtů úložiště pro obecné účely v1 nebo Blob je jednoduchý. Můžete upgradovat pomocí portálu Azure, PowerShellu nebo Azure CLI. Neexistuje žádné výpadky nebo riziko ztráty dat spojené s upgradem na účet úložiště pro obecné účely v2. K upgradu účtu dochází prostřednictvím jednoduché operace Azure Resource Manager, která změní typ účtu.

> [!IMPORTANT]
> Upgrade účtu úložiště pro obecné účely v1 nebo úložiště objektů Blob na obecný účel v2 je trvalý a nelze jej vrátit zpět.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Přejděte na svůj účet úložiště.
3. V části **Nastavení** klepněte na **položku Konfigurace**.
4. V části **Druh účtu** klikněte na **Upgradovat**.
5. V části **Potvrdit upgrade** zadejte název svého účtu.
6. V dolní části okna klikněte na **Upgrade.**

    ![Druh upgradu účtu](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Chcete-li upgradovat účet pro obecné účely v1 na účet pro obecné účely v2 pomocí prostředí PowerShell, nejprve aktualizujte prostředí PowerShell tak, aby používalo nejnovější verzi modulu **Az.Storage.** Informace o instalaci PowerShellu najdete v tématu [Instalace a konfigurace Azure PowerShellu](https://docs.microsoft.com/powershell/azure/install-Az-ps).

Dále zavolejte následující příkaz k upgradu účtu a navrácením názvu skupiny prostředků, názvu účtu úložiště a požadované úrovně přístupu k účtu.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2 -AccessTier <Hot/Cool>
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete upgradovat účet pro obecné účely v1 na účet pro obecné účely v2 pomocí azure cli, nejprve nainstalujte nejnovější verzi Azure CLI. Informace o instalaci rozhraní příkazového řádku najdete v tématu [Instalace Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Dále zavolejte následující příkaz k upgradu účtu a navrácením názvu skupiny prostředků, názvu účtu úložiště a požadované úrovně přístupu k účtu.

```azurecli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2 --access-tier=<Hot/Cool>
```

---

## <a name="specify-an-access-tier-for-blob-data"></a>Určení přístupové vrstvy pro data objektů blob

Účty pro obecné účely v2 podporují všechny služby úložiště Azure a datové objekty, ale úrovně přístupu jsou dostupné jenom pro blokové objekty BLOB v rámci úložiště objektů blob. Při upgradu na účet úložiště pro obecné účely v2 můžete zadat výchozí úroveň přístupu k účtu horké nebo cool, což znamená, že výchozí úroveň dat objektu blob se nahraje, jako by nebyl zadán parametr úrovně přístupu k objektům blob.

Úrovně přístupu k objektům Blob umožňují zvolit nákladově nejefektivnější úložiště na základě vašich očekávaných vzorců využití. Objekty BLOB bloku lze uložit do horké, studené nebo archivní úrovně. Další informace o úrovních přístupu najdete v [tématu úložiště objektů blob Azure: horké, studené a archivní úrovně úložiště](../blobs/storage-blob-storage-tiers.md).

Ve výchozím nastavení se vytvoří nový účet úložiště v úrovni aktivního přístupu a účet úložiště pro obecné účely v1 lze upgradovat na úroveň horkého nebo chladného účtu. Pokud úroveň přístupu k účtu není zadána při upgradu, bude ve výchozím nastavení upgradována na horkou. Pokud zkoumáte, kterou úroveň přístupu použít pro váš upgrade, zvažte aktuální scénář využití dat. Existují dva typické uživatelské scénáře pro migraci na účet pro obecné účely v2:

* Máte existující účet úložiště pro obecné účely v1 a chcete vyhodnotit upgrade na účet úložiště pro obecné účely v2 se správnou vrstvou přístupu k úložišti pro data objektů blob.
* Rozhodli jste se použít účet úložiště pro obecné účely v2 nebo už ho máte a chcete vyhodnotit, jestli byste měli použít úroveň přístupu k horkému nebo chladnému úložišti pro data objektů blob.

V obou případech je první prioritou odhadnout náklady na ukládání, přístup a provoz na vašich datech uložených v účtu úložiště pro obecné účely v2 a porovnat je s aktuálními náklady.

## <a name="pricing-and-billing"></a>Ceny a fakturace

Upgrade účtu úložiště v1 na účet pro obecné účely v2 je zdarma. Můžete zadat požadovanou úroveň účtu během procesu upgradu. Pokud při upgradu není zadána úroveň účtu, bude výchozí `Hot`úroveň účtu upgradovaného účtu . Změna úrovně přístupu k úložišti po upgradu však může vést ke změnám na vaší vyúčtování, takže se doporučuje zadat novou úroveň účtu během upgradu.

Všechny účty úložiště vycházejí z cenového modelu úložiště objektů blob založeného na úrovních jednotlivých objektů blob. Při použití účtu úložiště je potřeba vzít v úvahu tyto fakturační podmínky:

* **Náklady na úložiště**: Kromě množství uložených dat se náklady na ukládání dat liší v závislosti na úrovni přístupu k úložišti. Pokud je úroveň chladnější, cena za gigabajt se snižuje.

* **Cena za přístup k datům:** Pokud je úroveň chladnější, cena za přístup k datům se zvyšuje. Pro data v úrovni přístupu k chladnému a archivnímu úložišti se účtuje poplatek za gigabajt za přístup k datům za čtení.

* **Cena za transakce:** Pro všechny úrovně se účtuje poplatek za transakce, který se pro chladnější úrovně zvyšuje.

* **Cena za přenosy dat geografické replikace:** Tento poplatek se vztahuje jen na účty s nastavenou geografickou replikací, jako třeba GRS a RA-GRS. Přenos dat geografické replikace je zpoplatněný podle sazby za GB.

* **Cena za odchozí přenosy dat**: Odchozí přenosy dat (dat přenesených směrem z oblasti Azure) jsou zpoplatněné podle využití šířky pásma sazbou za GB, stejně jako je tomu u účtů úložiště pro obecné účely.

* **Změna úrovně přístupu k úložišti**: Změna úrovně přístupu k úložišti účtu z chladné na horké bude účtována poplatek rovnající se čtení všech dat existujících v účtu úložiště. Změna úrovně přístupu k účtu z horké na studenou však účtuje poplatek rovnající se zápisu všech dat do chladné vrstvy (pouze účty GPv2).

> [!NOTE]
> Další informace o cenovém modelu pro účty úložišť najdete na stránce [Ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/). Další informace o poplatcích za odchozí přenosy dat najdete na stránce [Podrobné informace o cenách přenosů dat](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Odhad nákladů pro aktuální vzorce používání

Chcete-li odhadnout náklady na ukládání a přístup k datům objektů blob v účtu úložiště pro obecné účely v2 v určité vrstvě, vyhodnoťte stávající způsob využití nebo přibližte očekávaný vzor využití. Celkově vzato potřebujete vědět:

* Vaše využití úložiště objektů Blob v gigabajtech, včetně:
  * Kolik dat se v účtu úložiště ukládá?
  * Jak se mění objem dat měsíčně? Nahrazují nová data neustále stará data?

* Vzor primárního přístupu pro vaše data úložiště objektů Blob, včetně:
  * Kolik dat se čte a zapisuje do účtu úložiště?
  * Kolik operací čtení versus zápisu dochází na data v účtu úložiště?

Chcete-li rozhodnout o nejlepší úroveň přístupu pro vaše potřeby, může být užitečné určit kapacitu dat objektu blob a způsob, jakým se tato data používají. To lze provést tak, že se podíváte na metriky monitorování vašeho účtu.

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

Podrobnosti o povolení, shromažďování a zobrazování dat metrik najdete v [tématu Metriky analýzy úložiště](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

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
> Podrobnější příklad výpočtu nákladů na použití úrovně přístupu k horkému nebo chladnému úložišti naleznete v nejčastějších dotazech s názvem *"Co jsou horké a studené úrovně přístupu a jak mám určit, kterou z nich použít?"* na stránce [Ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Další kroky

* [Vytvoření účtu úložiště](storage-account-create.md)
