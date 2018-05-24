---
title: Možnosti účtu Azure Storage | Microsoft Docs
description: Vysvětlení možností použití služby Azure Storage.
services: storage
author: hux
manager: jwillis
ms.service: storage
ms.workload: storage
ms.topic: get-started-article
ms.date: 05/02/2018
ms.author: hux
ms.openlocfilehash: 69da15b98e6c519a3a8352cc7ca7212286cb4e52
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="azure-storage-account-options"></a>Možnosti účtu Azure Storage

## <a name="overview"></a>Přehled
Azure Storage poskytuje tři různé možnosti účtu s různými cenami a podporovanými funkcemi. Než vytvoříte účet úložiště, zvažte následující rozdíly a určete možnost, která je pro vaše aplikace nejvhodnější. Tyto tři různé možnosti účtu úložiště jsou následující:

* Účty **pro obecné účely verze 2 (GPv2)** 
* Účty **pro obecné účely verze 1 (GPv1)**
* Účty **úložiště Blob**

Jednotlivé typy účtů jsou podrobněji popsané v následujících částech:

## <a name="storage-account-options"></a>Možnosti účtu úložiště

### <a name="general-purpose-v2"></a>Účty pro obecné účely verze 2

Účty pro obecné účely verze 2 (GPv2) jsou účty úložiště, které podporují všechny nejnovější funkce pro objekty blob, soubory, fronty a tabulky. Účty GPv2 podporují všechna rozhraní API a funkce podporované v účtech úložiště GPv1 a Blob. Podporují také stejné funkce odolnosti, dostupnosti, škálovatelnosti a výkonu jako tyto typy účtů. Ceny za účty GPv2 byly navržené pro zajištění nejnižších cen za gigabajt a konkurenceschopných cen za transakce.

Účet GPv1 můžete upgradovat na účet GPv2 pomocí portálu Azure Portal, PowerShellu nebo Azure CLI. 

Pro objekty blob bloku v účtu úložiště GPv2 si můžete vybrat mezi horkou a studenou úrovní úložiště na úrovni účtu, nebo mezi horkou, studenou a archivní úrovní na úrovni objektu blob, a to v závislosti na vzorech přístupu. Pro zajištění optimalizace nákladů ukládejte často, občas a zřídka používaná data v horké, studené a archivní úrovni úložiště (v uvedeném pořadí). 

Účty GPv2 zveřejňují atribut **Access Tier** na úrovni účtu. Tento atribut specifikuje výchozí úroveň účtu úložiště jako **horkou** nebo **studenou**. Výchozí úroveň účtu úložiště se použije pro všechny objekty blob, které nemají nastavenou explicitní úroveň na úrovni objektů blob. Pokud začnete k datům přistupovat jinak často, můžete mezi úrovněmi úložiště kdykoliv přepnout. **Archivní úroveň** je možné použít pouze na úrovni objektu blob.

> [!NOTE]
> Se změnou úrovně úložiště můžou být spojeny další poplatky. Další informace najdete v části [Ceny a fakturace](#pricing-and-billing).
>
> Microsoft doporučuje ve většině scénářů používat místo účtů úložiště Blob účty úložiště pro obecné účely verze 2.

### <a name="upgrade-a-storage-account-to-gpv2"></a>Upgrade účtu úložiště na účet GPv2

Uživatelé můžou účet GPv1 kdykoli upgradovat na účet GPv2 pomocí PowerShellu nebo Azure CLI. Tato změna je nevratná a žádné jiné změny nejsou povoleny.

#### <a name="upgrade-with-powershell"></a>Upgrade pomocí PowerShellu

Pokud chcete upgradovat účet GPv1 na účet GPv2 pomocí PowerShellu, nejprve aktualizujte PowerShell, aby používal nejnovější verzi modulu **AzureRm.Storage**. Informace o instalaci PowerShellu najdete v tématu [Instalace a konfigurace Azure PowerShellu](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Pak účet upgradujte zavoláním následujícího příkazu, ve kterém nahradíte název vaší skupiny prostředků a účtu úložiště:

```powershell
Set-AzureRmStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

#### <a name="upgrade-with-azure-cli"></a>Upgrade pomocí Azure CLI

Pokud chcete upgradovat účet GPv1 na účet GPv2 pomocí Azure CLI, nejprve nainstalujte nejnovější verzi Azure CLI. Informace o instalaci rozhraní příkazového řádku najdete v tématu [Instalace Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Pak účet upgradujte zavoláním následujícího příkazu, ve kterém nahradíte název vaší skupiny prostředků a účtu úložiště:

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
```` 

### <a name="general-purpose-v1"></a>Účty pro obecné účely verze 1

Účty pro obecné účely verze 1 (GPv1) poskytují přístup ke všem službám Azure Storage, ale nemusí zahrnovat nejnovější funkce nebo nejnižší ceny za gigabajt. Účty GPv1 například nepodporují studené úložiště a úložiště archivu. Ceny za transakce jsou u účtů GPv1 nižší, takže z tohoto typu účtu můžou mít užitek úlohy s vysokou četností změn dat nebo vysokou frekvencí čtení.

Účty úložiště pro obecné účely verze 1 (GPv1) jsou nejstarším typem účtů úložiště a jediným druhem, který je možné použít v modelu nasazení Classic. 

### <a name="blob-storage-accounts"></a>Účty úložiště Blob

Účty úložiště Blob podporují stejné funkce objektů blob bloku jako účty GPv2, ale jsou omezené pouze na objekty blob bloku. Ceny jsou velmi podobné cenám za účty pro obecné účely verze 2. Zákazníci by si měli prostudovat rozdíly v cenách účtů úložiště Blob a GPv2 a zvážit upgrade na účet GPv2. Tento upgrade není možné vrátit.

> [!NOTE]
> Účty úložiště Blob podporují pouze objekty blob bloku a doplňovací objekty blob, nepodporují objekty blob stránky.

## <a name="recommendations"></a>Doporučení

Další informace o účtech úložiště najdete v tématu [Účty Azure Storage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Pro aplikace, které potřebují pouze úložiště objektů blob bloku nebo doplňovacích objektů blob, doporučujeme použít účty úložiště GPv2 a využít tak výhody specializovaného cenového modelu vrstveného úložiště. V určitých scénářích však můžete chtít použít účty GPv1, například:

* Potřebujete stále používat model nasazení Classic. Účty úložiště Blob jsou dostupné jen přes model nasazení Azure Resource Manager.

* Používáte velké objemy transakcí nebo šířky pásma geografické replikace, z čehož obojí stojí více v účtech úložiště GPv2 a Blob než v účtech GPv1, a nemáte dostatek úložiště, abyste mohli využívat výhody nižší ceny za GB úložiště.

* Používáte verzi rozhraní [Storage Services REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx), která je starší než 14.2.2014, nebo klientskou knihovnu verze nižší než 4.x a nemůžete svoji aplikaci upgradovat.

> [!NOTE]
> Účty Blob Storage jsou aktuálně podporované ve všech oblastech Azure.

## <a name="pricing-and-billing"></a>Ceny a fakturace
Všechny účty úložiště vycházejí z cenového modelu úložiště objektů blob založeného na úrovních jednotlivých objektů blob. Při použití účtu úložiště je potřeba vzít v úvahu tyto fakturační podmínky:

* **Cena za uložení**: Vedle uloženého množství dat se cena za uložení odvíjí také od úrovně úložiště. Pokud je úroveň chladnější, cena za gigabajt se snižuje.

* **Cena za přístup k datům:** Pokud je úroveň chladnější, cena za přístup k datům se zvyšuje. Přístup k datům ve studené úrovni úložiště a v úrovni úložiště Archive je zpoplatněný podle sazby za GB přečtených dat.

* **Cena za transakce:** Pro všechny úrovně se účtuje poplatek za transakce, který se pro chladnější úrovně zvyšuje.

* **Cena za přenosy dat geografické replikace:** Tento poplatek se vztahuje jen na účty s nastavenou geografickou replikací, jako třeba GRS a RA-GRS. Přenos dat geografické replikace je zpoplatněný podle sazby za GB.

* **Cena za odchozí přenosy dat**: Odchozí přenosy dat (dat přenesených směrem z oblasti Azure) jsou zpoplatněné podle využití šířky pásma sazbou za GB, stejně jako je tomu u účtů úložiště pro obecné účely.

* **Změna vrstvy úložiště:** Změna vrstvy úložiště účtu ze studené na horkou je zpoplatněna částkou, jejíž výše odpovídá přečtení všech dat v aktuálním účtu úložiště. Naproti tomu změna vrstvy úložiště účtu z horké na studenou je zpoplatněna částkou, která odpovídá zápisu všech dat do studené vrstvy (pouze účty GPv2).

> [!NOTE]
> Další informace o cenovém modelu pro účty úložiště Blob najdete na stránce [Ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/). Další informace o poplatcích za odchozí přenosy dat najdete na stránce [Podrobné informace o cenách přenosů dat](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="quickstart-scenarios"></a>Scénáře Rychlý start

V tomto oddílu jsou předvedené následující scénáře s využitím webu Azure Portal:

* [Vytvoření účtu úložiště GPv2](#create-a-gpv2-storage-account-using-the-azure-portal)
* [Převod účtu úložiště GPv1 nebo Blob na účet úložiště GPv2](#convert-a-gpv1-or-blob-storage-account-to-a-gpv2-storage-account-using-the-azure-portal)
* [Nastavení účtu v účtu úložiště GPv2](#change-the-storage-tier-of-a-gpv2-storage-account-using-the-azure-portal)
* [Nastavení úrovně objektů blob ve službě Blob Storage nebo v účtu úložiště GPv2](#change-the-storage-tier-of-a-blob-using-the-azure-portal)

V následujících příkladech nejde nastavit úroveň přístupu Na Archive, protože toto nastavení se vztahuje na celý účet úložiště. Nastavení Archiv je možné použít jenom pro konkrétní objekty blob.

### <a name="create-a-gpv2-storage-account-using-the-azure-portal"></a>Vytvoření účtu úložiště GPv2 pomocí webu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. V nabídce centra vyberte **Vytvořit prostředek** > **Data a úložiště** > **Účet úložiště**.

3. Zadejte název účtu úložiště.

    Název musí být globálně jedinečný, protože je součástí adresy URL pro přístup k objektům v účtu úložiště.  

4. Jako model nasazení vyberte **Resource Manager**.

    Vrstvené úložiště lze použít jen s účty úložiště Resource Manageru, proto u nových prostředků doporučujeme model Resource Manager. Další informace najdete v tématu [Přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md).  

5. V rozevíracím seznamu **Druh účtu** vyberte **Obecné účely verze 2**.

    Když vyberete možnost GPv2, úroveň výkonu se nastaví na Standard. V úrovni výkonu Premium nejsou úrovně úložiště dostupné.

6. Vyberte pro účet úložiště možnost replikace: **LRS**, **ZRS**, **GRS** nebo **RA-GRS**. Výchozí hodnota je **RA-GRS**.

    LRS = místně redundantní úložiště; ZRS = zónově redundantní úložiště; GRS = geograficky redundantní úložiště (dvě oblasti); RA-GRS je geograficky redundantní úložiště s oprávněním ke čtení (dvě oblasti s oprávněním ke čtení v té druhé).

    Další informace o možnostech replikace pro Azure Storage najdete v tématu [Replikace Azure Storage](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

7. Vyberte si úroveň úložiště, která vám vyhovuje: **Úroveň přístupu** nastavte na **Studená** nebo **Horká**. Výchozí hodnota je **Hot**.

8. Vyberte předplatné, ve kterém chcete vytvořit nový účet úložiště.

9. Zadejte novou skupinu prostředků nebo vyberte existující skupinu prostředků. Další informace o skupinách prostředků najdete v článku [Přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md).

10. K účtu úložiště přiřaďte oblast.

11. Vytvořte účet úložiště kliknutím na **Vytvořit**.

### <a name="convert-a-gpv1-or-blob-storage-account-to-a-gpv2-storage-account-using-the-azure-portal"></a>Převod účtu GPv1 nebo Blob Storage na účet úložiště GPv2 pomocí portálu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Přejděte do svého účtu úložiště tak, že vyberete **Všechny prostředky** a pak vyberete svůj účet úložiště.

3. V části Nastavení klikněte na **Konfigurace**.

4. V části **Druh účtu** klikněte na **Upgradovat**.

5. V části **Potvrdit upgrade** zadejte název svého účtu. 

5. Klikněte na Upgradovat v dolní části okna.

### <a name="change-the-storage-tier-of-a-gpv2-storage-account-using-the-azure-portal"></a>Změna úrovně úložiště u účtu úložiště GPv2 pomocí webu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Přejděte do svého účtu úložiště tak, že vyberete **Všechny prostředky** a pak vyberete svůj účet úložiště.

3. V okně Nastavení klikněte na **Konfigurace**, odkud můžete zobrazit nebo změnit konfiguraci účtu.

4. Vyberte si úroveň úložiště, která vám vyhovuje: **Úroveň přístupu** nastavte na **Studená** nebo **Horká**.

5. V horní části okna klikněte na možnost Uložit.

### <a name="change-the-storage-tier-of-a-blob-using-the-azure-portal"></a>Změna vrstvy úložiště u objektu blob s využitím webu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Pokud chcete přejít na objekt blob ve vašem účtu úložiště, vyberte **Všechny prostředky**, vyberte svůj účet úložiště a pak vyberte objekt blob.

3. V okně vlastností objektu blob klikněte v rozevírací nabídce **Úroveň přístupu** a vyberte úroveň úložiště (**horká**, **studená** nebo **Archive**).

5. V horní části okna klikněte na možnost Uložit.

> [!NOTE]
> Se změnou úrovně úložiště můžou být spojeny další poplatky. Další informace najdete v části [Ceny a fakturace](#pricing-and-billing).


## <a name="evaluating-and-migrating-to-gpv2-storage-accounts"></a>Vyhodnocení a migrace na účty úložiště GPv2
V této části chceme uživatelům pomoci s hladkým přechodem na účty úložiště GPv2 (z účtů GPv1). Jako uživatel stojíte před jednou z těchto dvou možností:

* Máte účet úložiště GPv1 a chcete vyhodnotit přechod na účtu úložiště GPv2 s vhodnou úrovní úložiště.
* Rozhodli jste se používat účet úložiště GPv2, nebo už ho dokonce máte, a chcete vyhodnotit, jestli máte použít horkou nebo studenou úroveň úložiště.

V obou případech je hlavní prioritou odhad nákladů na ukládání a přístup k datům uloženým v účtu úložiště GPv2 a jejich porovnání s aktuálními náklady.

## <a name="evaluating-gpv2-storage-account-tiers"></a>Vyhodnocení úrovní účtu úložiště GPv2

Abyste stanovili přibližnou cenu za ukládání a přístup k datům uloženým v účtu úložiště GPv2, musíte vyhodnotit, jak v současné době k datům přistupujete, nebo odhadnout, jak k nim přistupovat budete. Celkově vzato potřebujete vědět:

* Spotřebu úložiště – Kolik dat ukládáte a jak se toto množství měsíc od měsíce mění?

* Vzorec přistupování k úložišti – Kolik dat se na účtu čte a zapisuje (včetně nových dat)? Ke kolika transakcím dochází při přístupu k datům a o jaké transakce se jedná?

## <a name="monitoring-existing-storage-accounts"></a>Monitorování existujících účtů úložiště

K monitorování existujících účtů úložiště a sesbírání dat můžete využít službu Azure Storage Analytics, která provádí protokolování a poskytuje data metriky pro účet úložiště. Analýza úložiště může pro účty úložiště typu GPv1, GPv2 a Blob ukládat metriky, včetně souhrnné statistiky transakcí a dat o kapacitě požadavků na službu úložiště. Tato data se ukládají do známých tabulek na tom samém účtu úložiště.

Další informace najdete na stránkách věnovaných [metrikám Analýzy úložiště](https://msdn.microsoft.com/library/azure/hh343258.aspx) a [tabulkovému schématu metrik Analýzy úložiště](https://msdn.microsoft.com/library/azure/hh343264.aspx).

> [!NOTE]
> Účty úložiště Blob zpřístupňují koncový bod služby Table service pouze pro účely ukládání a zpřístupnění dat metrik pro tento účet. 

Pokud chcete monitorovat využití úložiště pro účet úložiště Blob, je potřeba povolit metriky kapacity.
Když tuto funkci zapnete, data o kapacitě služby Blob service pro daný účet úložiště se budou denně zaznamenávat jako zápisy do tabulky *$MetricsCapacityBlob* v rámci stejného účtu úložiště.

Aby bylo možné pro účet úložiště Blob monitorovat vzory přístupu k datům, je potřeba povolit hodinovou metriku transakcí z rozhraní API. Když povolíte hodinovou metriku transakcí, data o transakcích rozhraní API se budou každou hodinu shromažďovat a zaznamenávat jako zápisy do tabulky *$MetricsHourPrimaryTransactionsBlob* v rámci stejného účtu úložiště. Při použití účtů úložiště RA-GRS zaznamenává tabulka *$MetricsHourSecondaryTransactionsBlob* transakce do sekundárního koncového bodu.

> [!NOTE]
> Pokud máte účet úložiště pro obecné účely, ve kterém jsou uložené objekty blob stránky a disky virtuálních počítačů, případně fronty, soubory, nebo tabulky, vedle dat objektů blob bloku a doplňovacích objektů blob, odhad tímto postupem provést nepůjde. Data o kapacitě nerozlišují objekty blob bloku od ostatních typů a proto pro ostatní typy dat neposkytují data o kapacitě. Pokud používáte tyto typy, můžete se podívat na množství na nejnovějším vyúčtování.

Pokud chcete dobře odhadnout spotřebu dat a přístup k nim, doporučujeme pro měření dat vybrat takovou dobu uchování, která vystihuje pravidelné používání, a potom údaje extrapolovat. Můžete například měřená data uchovávat po sedm dní, sesbírat jednou za týden a analyzovat je na konci měsíce. Nebo změřte a nasbírejte data za posledních 30 dní a na konci 30denního období je analyzujte.

Podrobnosti o povolení, shromažďování a zobrazování dat metrik najdete v tématu [Povolení metrik Azure Storage a zobrazení dat metrik](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Uložení, zobrazování a stahování analyzovaných data se účtuje stejně jako běžná uživatelská data.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Odhadnutí nákladů s pomocí naměřených údajů o využití

#### <a name="storage-costs"></a>Cena za uložení

Poslední položka v tabulce kapacitní metriky *$MetricsCapacityBlob* s klíčem řádku *data* zobrazuje kapacitu úložiště spotřebovanou uživatelskými daty. Poslední položka v tabulce kapacitní metriky *$MetricsCapacityBlob* s klíčem řádku *analytics* zobrazuje kapacitu úložiště spotřebovanou protokoly analýzy.

Celková kapacita spotřebovaná uživatelskými daty a protokoly analýzy (pokud jsou povoleny) se potom dají použít k odhadu nákladů za uložení dat v účtu úložiště. Stejnou metodu je možné použít také pro odhad nákladů na úložiště v účtech úložiště GPv1.

#### <a name="transaction-costs"></a>Cena za transakce

Součet *TotalBillableRequests* všech položek rozhraní API v tabulce metrik transakcí udává celkový počet transakcí daného rozhraní API. *Například* celkový počet transakcí *GetBlob* v daném časovém období se dá vypočítat jako celkový součet fakturovatelných požadavků všech položek s klíčem řádku *user;GetBlob*.

Pokud chcete pro účet Blob Storage odhadnout náklady za transakce, je potřeba rozdělit transakce do tří skupin, protože se cenově liší.

* Transakce zápisu jako *PutBlob*, *PutBlock*, *PutBlockList*, *AppendBlock*, *ListBlobs*, *ListContainers*, *CreateContainer*, *SnapshotBlob* a *CopyBlob*.
* Transakce odstranění jako *DeleteBlob* a *DeleteContainer*.
* Veškeré ostatní transakce.

Pokud chcete odhadnout náklady na transakce pro účet úložiště GPv1, je potřeba započítat všechny transakce bez ohledu na operaci nebo rozhraní API.

### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Přístup k datům a cena za přenos dat – geografická replikace

Analýza úložiště sice k účtu úložiště nevypíše množství přečtených a zapsaných dat, toto množství lze ale zhruba odhadnout z tabulky metriky transakcí. Součet *TotalBillableRequests* všech položek rozhraní API v tabulce metrik transakcí udává celkové množství příchozích dat k tomuto rozhraní API v bajtech. Podobně součet *TotalEgress* udává celkové množství odchozích dat v bajtech.

Pokud chcete pro účet Blob Storage odhadnout náklady za přístup k datům, je potřeba transakce rozdělit do dvou skupin.

* Množství dat načtených z účtu úložiště lze odhadnout ze součtu *TotalEgress* především u operací *GetBlob* a *CopyBlob*.

* Množství dat zapsaných do účtu úložiště lze odhadnout ze součtu *TotalIngress* především u operací *PutBlob*, *PutBlock*, *CopyBlob* a *AppendBlock*.

Také cena za přenos geograficky replikovaných dat účtů Blob Storage se v případě účtu úložiště typu GRS nebo RA-GRS dá vypočítat pomocí toho, že odhadnete množství zapsaných dat.

> [!NOTE]
> Podrobnější příklad výpočtu ceny za využívání horké či studené vrstvy úložiště najdete v odpovědi na často kladenou otázku *Co jsou horká a studená vrstva přístupu a jak určit, která z nich se má použít?* na stránce [Ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="migrating-existing-data"></a>Migrace existujících dat

Účet GPv1 je možné snadno upgradovat na účet GPv2, a to bez výpadků, změn rozhraní API a nutnosti migrovat data. Z tohoto důvodu se doporučuje účty GPv1 migrovat na účty GPv2, a ne na účty úložiště Blob.

Pokud však potřebujete migrovat na účet úložiště Blob, můžete postupovat podle následujících pokynů.

Účet úložiště Blob se specializuje pouze na ukládání objektů blob bloku a doplňovacích objektů blob. Existující účty úložiště pro obecné účely, ve kterých můžete ukládat tabulky, fronty, soubory a disky i objekty blob, se nedají převést na účty Blob Storage. Pokud chcete využívat vrstvy úložiště, budete muset vytvořit nové účty Blob Storage a existující data migrovat do těchto nových účtů.

Následující metody můžete použít pro migraci existujících dat do účtů úložiště Blob z místních úložných zařízení, z cloudového úložiště jiných poskytovatelů nebo z existujícího účtu úložiště Azure pro obecné účely:

### <a name="azcopy"></a>AzCopy

AzCopy je nástroj Windows, který se spouští z příkazového řádku a který je určený pro vysoce výkonné kopírování dat do Azure Storage a z Azure Storage. Pomocí nástroje AzCopy můžete kopírovat data do svého účtu úložiště Blob z existujících účtů úložiště pro obecné účely nebo ukládat data z lokálních úložných zařízení do svého účtu úložiště Blob.

Další informace najdete v tématu [Přenos dat pomocí nástroje příkazového řádku AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Knihovna pro přesun dat

Knihovna pro přesun dat v Azure Storage pro .NET je založená na základní platformě pro přesun dat, kterou využívá nástroj AzCopy. Knihovna je určená pro vysoce výkonné, spolehlivé a snadné operace přenosu dat, podobně jako AzCopy. Pomocí této knihovny můžete využívat výhody funkcí AzCopy ve svojí aplikaci nativně, bez nutnosti spouštět a sledovat externí instance nástroje AzCopy.

Další informace najdete v tématu [Knihovna pro přesun dat v Azure Storage pro .Net](https://github.com/Azure/azure-storage-net-data-movement).

### <a name="rest-api-or-client-library"></a>Rozhraní REST API nebo klientská knihovna

Pomocí některé z knihoven klienta Azure nebo rozhraní REST API služeb úložiště Azure můžete vytvořit vlastní aplikaci pro migraci dat do účtu úložiště Blob. Azure Storage poskytuje množství knihoven klienta pro různé jazyky a platformy, jako například .NET, Java, C++, Node.JS, PHP, Ruby nebo Python. Knihovny klienta nabízí pokročilé možnosti a funkce, jako je například logika opakovaných pokusů, protokolování a paralelní ukládání. Můžete také psát aplikace přímo na rozhraní REST API, které může zavolat jakýkoli jazyk schopný vytvářet požadavky přes HTTP/HTTPS.

Další informace najdete v tématu [Začínáme se službou Azure Blob Storage](../blobs/storage-dotnet-how-to-use-blobs.md).

> [!IMPORTANT]
> Objekty blob šifrované na straně klienta ukládají metadata šifrování s objektem blob. Pokud kopírujete objekt blob šifrovaný na straně klienta, ujistěte se, že operace kopírování zachová metadata objektu blob, zejména metadata šifrování. Pokud objekt blob zkopírujete bez metadat šifrování, obsah objektu blob bude nenávratně ztracený. Další informace o metadatech šifrování najdete v tématu [Azure Storage a šifrování na straně klienta](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="faq"></a>Nejčastější dotazy

**Jsou existující účty úložiště stále dostupné?**

Ano, existující účty úložiště (GPv1) jsou stále dostupné a jejich funkce ani cena se nemění. U účtů GPv1 není možné vybrat úroveň úložiště a tato možnost nebude ani v budoucnosti.

**Proč a kdy bych měl/a začít používat účty úložiště GPv2?**

Účty úložiště GPv2 jsou specializované na poskytování nejnižších cen za GB úložiště při současném poskytování konkurenceschopných cen za transakce a přístup k datům. Výhledově se účty úložiště GPv2 doporučují pro ukládání objektů blob, protože na základě tohoto typu účtu se budou zavádět funkce jako upozornění na změny. Kdy budete chtít upgradovat ale záleží na vás a vašich obchodních potřebách. Před upgradem se například můžete rozhodnout optimalizovat vzorce transakcí.

Downgrady z účtů GPv2 se nepodporují, proto před upgradováním svých účtů na účty GPv2 zvažte všechny cenové dopady.

**Můžu svůj existující účet úložiště upgradovat na účet úložiště GPv2?**

Ano. Účty úložiště GPv1 nebo Blob je možné snadno upgradovat na účty GPv2 pomocí portálu, PowerShellu nebo rozhraní příkazového řádku. 

Downgrady z účtů GPv2 se nepodporují, proto před upgradováním svých účtů na účty GPv2 zvažte všechny cenové dopady.

**Můžu na jednom účtu ukládat objekty do obou úrovní úložiště?**

Ano. Atribut **Access Tier** nastavený na úrovni účtu představuje výchozí úroveň, která se použije pro všechny objekty v příslušném účtu, které nemají explicitně nastavenou úroveň. Ovládání datových vrstev na úrovni objektů blob však umožňuje nastavit úroveň přístupu na úrovni objektu bez ohledu na to, jaké je nastavení úrovně přístupu v příslušném účtu. V rámci stejného účtu mohou existovat objekty blob ve všech třech vrstvách úložiště (horká, studená nebo archivní).

**Můžu u svého účtu úložiště GPv2 změnit úroveň úložiště?**

Ano, úroveň úložiště účtu můžete změnit nastavením atributu **Access Tier** v účtu úložiště. Změna úrovně úložiště účtu se projeví u všech objektů uložených v tomto účtu, které nemají explicitně nastavenou úroveň. Při změně úrovně úložiště z horké na studenou vznikají náklady na operace zápisu (za 10 000) (pouze účty úložiště GPv2), zatímco změna úrovně úložiště ze studené na horkou s sebou nese náklady na operace čtení (za 10 000) a načítání dat (za GB) pro načtení všech dat v příslušném účtu.

**Jak často můžu účtu úložiště Blob změnit úroveň úložiště?**

Možnost měnit vrstvu úložiště se nijak neomezuje, ale nezapomeňte, že změna vrstvy úložiště ze studené na horkou s sebou může nést značné náklady. Nedoporučujeme měnit vrstvu úložiště často.

**Budou se objekty blob ve studené vrstvě úložiště chovat jinak než objekty blob v horké vrstvě úložiště?**

Objekty blob v horké úrovni úložiště účtů GPv2 a Blob mají stejnou latenci jako objekty blob v účtech úložiště GPv1. Objekty blob ve studené úrovni úložiště mají podobnou latenci (v řádu milisekund) jako objekty blob v horké úrovni. Objekty blob v úrovni úložiště Archive mají latenci několik hodin.

Objekty blob ve studené vrstvě úložiště budou mít trochu nižší úroveň dostupnosti služeb (SLA) než objekty blob uložené v horké vrstvě úložiště. Další informace najdete ve [smlouvě SLA pro vaše úložiště](https://azure.microsoft.com/support/legal/sla/storage).

**Můžu do účtů úložiště Blob ukládat objekty blob stránky a disky virtuálních počítačů?**

Ne. Účty úložiště Blob podporují pouze objekty blob bloku a doplňovací objekty blob, nepodporují objekty blob stránky. Disky virtuálních počítačů Azure se opírají o objekty blob stránky, proto se účty úložiště Blob nedají použít k uložení disků virtuálních počítačů. Zálohy disků virtuálních počítačů se ale dají do účtu úložiště Blob uložit jako objekty blob bloku. Toto je jeden z důvodů pro zvážení použití účtů úložiště GPv2 místo účtů Blob.

**Musím měnit své existující aplikace, aby používaly účty úložiště GPv2?**

Účty úložiště GPv2 jsou z hlediska rozhraní API 100% konzistentní s účty úložiště GPv1 a Blob. Pokud aplikace používá objekty blob bloku nebo objekty blob připojení a vy používáte verzi rozhraní [služby úložiště REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) z 14. 2. 2014 nebo novější, měla by aplikace fungovat. Pokud používáte starší verzi protokolu, budete muset aplikaci aktualizovat, aby používala novou verzi, a mohla tak bez problémů pracovat s oběma typy účtů úložiště. Celkově vždy doporučujeme používat nejnovější verzi bez ohledu na to, který typ účtu úložiště používáte.

Ceny za transakce a šířku pásma jsou u účtů GPv2 obecně vyšší než u účtů GPv1. Proto možná budete muset před upgradem optimalizovat vzory transakcí, aby se vaše celková fakturovaná částka nezvýšila.

**Mění se něco v uživatelském rozhraní?**

Účty úložiště GPv2 jsou velmi podobné účtům úložiště GPv1 a podporují všechny klíčové funkce služby Azure Storage, včetně vysoké odolnosti a dostupnosti, škálovatelnosti, výkonnosti a zabezpečení. Kromě funkcí a omezení charakteristických pro účty a úrovně úložiště Blob, jak jsme popsali výše, zůstává při upgradu na účet úložiště GPv2 nebo Blob všechno ostatní při starém.

## <a name="next-steps"></a>Další kroky

### <a name="evaluate-blob-storage-accounts"></a>Posouzení účtů úložiště Blob

[Ověření dostupnosti účtů úložiště Blob v jednotlivých oblastech](https://azure.microsoft.com/regions/#services)

[Zapnutí metrik Azure Storage a vyhodnocení používání aktuálních účtů úložiště](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Posouzení cen úložiště Blob v jednotlivých oblastech](https://azure.microsoft.com/pricing/details/storage/)

[Posouzení cen přenosu dat](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-gpv2-storage-accounts"></a>Začátek používání účtů úložiště GPv2

[Začínáme s úložištěm Azure Blob](../blobs/storage-dotnet-how-to-use-blobs.md)

[Přesunutí dat z Azure Storage a do Azure Storage](../common/storage-moving-data.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Přenos dat pomocí nástroje příkazového řádku AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Procházení a prozkoumání účtů úložiště](http://storageexplorer.com/)
