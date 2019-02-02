---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/30/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 311ec7bacdfe58982ee670277000d81756f92005
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55648599"
---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Nejčastější dotazy týkající se disky virtuálního počítače Azure IaaS a spravovaných a nespravovaných prémiové disky

Tento článek obsahuje odpovědi na některé nejčastější dotazy ohledně Azure Managed Disks a Azure Premium SSD disky.

## <a name="managed-disks"></a>Managed Disks

**Co je Azure Managed Disks?**

Spravované disky je funkce, která zjednodušují správu disků virtuálních počítačů Azure IaaS pomocí zpracování správu účtu úložiště za vás. Další informace najdete v tématu [Přehled služby Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

**Pokud vytvořím standardní spravovaný disk z existujícího VHD, který je 80 GB, kolik, která stojí mě?**

Další dostupné standardní disk velikost, která je disku S10 je považován za spravovaných disků úrovně standard, vytvořit z virtuálního pevného disku 80 GB. Platíte podle disku S10 ceny. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage).

**Existují žádné transakční náklady na spravované disky úrovně standard?**

Ano. Za každou transakci. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage).

**U spravovaných disků úrovně standard bude se mi účtovat skutečné množství dat na disku nebo zřízené kapacity disku?**

Platíte na základě zřízené kapacity disku. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage).

**Jak se ceny spravované disky úrovně premium liší od nespravovaných disků?**

Ceny za spravované disky úrovně premium je stejný jako disky úrovně premium.

**Můžete změnit typ účtu úložiště (Standard nebo Premium) pro moje spravované disky?**

Ano. Typ účtu úložiště spravovaných disků můžete změnit pomocí webu Azure portal, Powershellu nebo rozhraní příkazového řádku Azure.

**Můžete použít souboru VHD v účtu úložiště Azure k vytvoření spravovaného disku pomocí jiného předplatného?**

Ano.

**Můžete použít souboru VHD v účtu úložiště Azure k vytvoření spravovaného disku v jiné oblasti?**

Ne.

**Existují nějaká omezení škálování pro zákazníky, které používají spravované disky?**

Omezení spojená s účty úložiště se eliminují spravované disky. Maximální limit je však 50 000 spravované disky, jednotlivé oblasti a jednotlivá typ disku pro odběr.

**Může trvat přírůstkový snímek spravovaného disku?**

Ne. Aktuální snímků je úplná kopie spravovaného disku.

**Virtuální počítače ve skupině dostupnosti se může skládat z kombinaci spravovaných a nespravovaných disků?**

Ne. Virtuální počítače ve skupině dostupnosti musí používat všechny spravované disky nebo nespravované disky. Když vytvoříte skupinu dostupnosti, můžete typu disky, které chcete použít.

**Managed Disks je výchozí možnost na webu Azure Portal?**

Ano.

**Můžete vytvořit prázdnou spravovaného disku?**

Ano. Můžete vytvořit prázdný disk. Spravovaný disk můžete vytvořit nezávisle na virtuální počítač, například bez připojení k virtuálnímu počítači.

**Co je počet domén selhání podporované pro skupinu dostupnosti nastavení, která používá spravované disky?**

V závislosti na oblasti, ve kterém se nachází skupinu dostupnosti, který používá spravované disky je počet domén selhání podporované 2 nebo 3.

**Jak se účet úložiště úrovně standard pro nastavení diagnostiky?**

Nastavit účet privátního úložiště pro diagnostiku virtuálních počítačů.

**Jaký druh podporu řízení přístupu na základě Role je k dispozici pro Managed Disks?**

Spravované disky podporuje tři klíčové výchozí role:

* Vlastník: Můžou spravovat všechno včetně přístupu
* Přispěvatel: Můžou spravovat všechno kromě přístupu
* Čtenář: Všechno, co můžete zobrazit, ale nemůže provádět změny

**Existuje způsob, můžu zkopírovat nebo exportovat spravovaného disku do účtu privátního úložiště?**

Můžete vygenerovat jen pro čtení sdíleného přístupového podpisu (SAS) identifikátor URI pro spravovaný disk a pomocí zkopírovat obsah do privátního úložiště účtu nebo místní úložiště. Můžete použít identifikátor URI SAS pomocí webu Azure portal, Azure Powershellu, rozhraní příkazového řádku Azure nebo [AzCopy](../articles/storage/common/storage-use-azcopy.md)

**Můžete vytvořit kopii Moje spravovaného disku?**

Zákazníkům můžete pořídit snímek jejich spravované disky a pak pomocí tohoto snímku rychle vytvořit jiného spravovaného disku.

**Jsou nespravované disky stále podporovány?**

Ano, spravované a nespravované disky se podporují. Doporučujeme použít spravované disky pro nové úlohy a aktuálních úloh migrace na spravované disky.

**Můžu na jednom virtuálním počítači vyhledejte společně spravované a nespravované disky?**

Ne.

**Je-li vytvořit 128 GB disk a potom dojít ke zvětšení na 130 GB, bude mi účtovat další velikost disku (256 GB)?**

Ano.

**Můžete vytvořit místně redundantní úložiště, geograficky redundantní úložiště, a zónově redundantní úložiště spravované disky?**

Služba Azure Managed Disks v současné době podporuje pouze místně redundantní úložiště spravované disky.

**Můžete zmenšit nebo downsize Moje spravované disky?**

Ne. Tato funkce není aktuálně podporována. 

**Můžete přerušit zapůjčení na disku?**

Ne. Aktuálně to není podporováno je k dispozici, aby se zabránilo nechtěnému odstranění, když na disku se používá k zapůjčení adresy.

**Můžete změnit vlastnost název počítače při specializovaný (vytvořené pomocí nástroje pro přípravu systému ani zobecněn) disk s operačním systémem se používá ke zřízení virtuálního počítače?**

Ne. Nelze aktualizovat vlastnost název počítače. Nový virtuální počítač se dědí z nadřazeného objektu VM, která byla použita k vytvoření disku s operačním systémem. 

**Kde najdu ukázkové šablony Azure Resource Manageru k vytvoření virtuálních počítačů se spravovanými disky?**
* [Seznam šablon využití služby Managed Disks](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

**Při vytváření disku z objektu blob, je k dispozici žádné průběžně existující relaci se tento zdrojový objekt blob?**

Ne, při vytvoření nového disku je úplné samostatnou kopii tohoto objektu blob v daném čase a neexistuje žádné připojení mezi těmito dvěma. Pokud chcete po vytvoření disku, může zdrojový objekt blob odstranit bez ovlivnění nově vytvořený disku žádným způsobem.

**Můžete přejmenovat po jejím vytvoření spravovaných nebo nespravovaných disků?**

Za spravované disky nelze přejmenovat je. Může však přejmenovat nespravovaný disk, tak dlouho, dokud není aktuálně připojen k virtuální počítač nebo virtuální pevný disk.

**Můžete použít GPT dělení na Disk s Azure?**

Vytváření oddílů GPT lze použít pouze pro datové disky, nikoli disky s operačním systémem. Disky s operačním systémem, musíte použít styl oddílů MBR.

## <a name="standard-ssd-disks"></a>Standardní disky SSD

**Co jsou disky SSD na úrovni Standard Azure?**
Standardní disky SSD jsou standardní disky zálohovanými SSD média, optimalizované jako nákladově efektivní úložiště pro úlohy, které je třeba zajistit konzistentní výkon na nižších úrovních vstupně-výstupních operací.

<a id="standard-ssds-azure-regions"></a>**Co jsou oblasti, které aktuálně podporuje pro disky SSD na úrovni Standard?**
Všechny oblasti Azure teď podporují disků SSD na úrovni Standard.

**Azure Backup je k dispozici, při použití standardní disky SSD?**
Ano, je Azure Backup nyní k dispozici.

**Jak vytvořím disků SSD na úrovni Standard?**
Můžete vytvořit pomocí šablon Azure Resource Manageru, sady SDK, Powershellu nebo rozhraní příkazového řádku disků SSD na úrovni Standard. V následující tabulce jsou parametry potřebné v šabloně Resource Manageru k vytvoření standardní disky SSD:

* *verze rozhraní API* pro Microsoft.Compute musí být nastavena jako `2018-04-01` (nebo novější)
* Zadejte *managedDisk.storageAccountType* jako `StandardSSD_LRS`

Následující příklad ukazuje *properties.storageProfile.osDisk* oddílu pro virtuální počítač, který používá standardní disky SSD:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Úplnou šablonu příklad toho, jak vytvořit disk SSD na úrovni Standard se šablonou, naleznete v tématu [vytvoření virtuálního počítače z Image Windows pomocí standardních datových disků SSD](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

**Můžete převést své stávající disky na SSD na úrovni Standard?**
Ano, je to možné. Odkazovat na [převést Azure managed disks storage úroveň ze standard na úrovni premium a naopak](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) obecné pokyny pro převod na Managed Disks. A je možné, použijte tuto hodnotu Aktualizovat typ disku na SSD na úrovni Standard.
-AccountType StandardSSD_LRS

**Co je výhodou místo HDD disků SSD na úrovni Standard?**
Standardní disky SSD poskytovat lepší latencí, konzistencí, dostupností a spolehlivostí v porovnání s HDD disky. Aplikační úlohy spusťte na SSD na úrovni Standard proto mnohem více hladce. Mějte na paměti, disky Premium SSD skupiny představují doporučené řešení pro většinu produkčních úloh náročných na vstupně-výstupních operací. 

**Můžete použít standardní disky SSD jako nespravované disky?**
Ne, standardní disky SSD disky jsou dostupné jenom jako spravované disky.

**Podporují standardní disky SSD "jednu instanci SLA k virtuálním počítačům"?**
Ne, standardní disky SSD nemají jednu instanci SLA k virtuálním počítačům. Použití disků Premium SSD pro jednu instanci SLA k virtuálním počítačům.

## <a name="migrate-to-managed-disks"></a>Migrace na spravované disky

**Je k dispozici žádný dopad migrace na spravované disky výkon?**

Migrace zahrnuje přesun Disk z jednoho umístění úložiště do druhého. To je orchestrované přes pozadí kopii dat, která může trvat několik hodin, obvykle méně než 24 hodin v závislosti na množství dat na discích. Během této doby může aplikace docházet vyšší než obvykle latence čtení a některé čtení můžete získat přesměrováno do původního umístění, může trvat déle. Během tohoto období není žádný vliv na latence zápisu.  

**Jaké změny jsou nutné v již existující Azure Backup service před/po migraci konfigurace disků na Managed Disks?**

Nejsou potřeba žádné změny.

**Bude tak fungovat dál záloh virtuálních počítačů vytvořených prostřednictvím služby Azure Backup před migrací?**

Ano, zálohy fungují.

**Jaké změny jsou nutné v již existující šifrování disků Azure před/po migraci konfigurace disků na Managed Disks?**

Nejsou potřeba žádné změny.

**Je automatizovaný přenos z existující virtuální počítač škálovací sady z nespravovaných disků na Managed Disks, které jsou podporovány?**

Ne. Můžete vytvořit novou škálovací sadu s použitím image z původní škálovací sady s nespravovanými disky Managed Disks.

**Můžete vytvořit spravovaný Disk ze snímku objektu blob stránky provedena před migrací do Managed Disks?**

Ne. Můžete exportovat snímek objektu blob stránky jako objekt blob stránky a pak vytvořit spravovaný Disk z objektu blob exportované stránky.

**Můžete převzít své místní počítače chráněné službou Azure Site Recovery na virtuálním počítači se službou Managed Disks?**

Ano, můžete převzetí služeb při selhání virtuálního počítače se spravovanými disky.

**Je k dispozici žádný dopad migrace na virtuálních počítačích Azure, které jsou chráněné službou Azure Site Recovery prostřednictvím replikace z Azure do Azure?**

Ano. V současné době Azure do Azure ochranu Azure Site Recovery pro virtuální počítače se službou Managed Disks je k dispozici pouze jako služba ve verzi public preview.

**Můžete migrovat virtuální počítače s nespravovanými disky, které se nacházejí v účtech úložiště, které jsou nebo se dříve zašifrována na spravované disky?**

Ano

## <a name="managed-disks-and-storage-service-encryption"></a>Spravované disky a šifrování služby Storage

**Šifrování služby Azure Storage ve výchozím nastavení zapnutá při vytváření spravovaného disku?**

Ano.

**Kdo spravuje šifrovací klíče?**

Šifrovací klíče se spravuje Microsoft.

**Můžete zakázat šifrování služby Storage pro moje spravované disky?**

Ne.

**Je šifrování služby Storage k dispozici pouze v určitých oblastech?**

Ne. Je k dispozici ve všech oblastech, kde jsou k dispozici Managed Disks. Spravované disky jsou dostupné ve všech veřejných oblastech a Německo. Je také k dispozici v Číně, ale jenom pro Microsoft není spravované klíče, klíče spravované zákazníkem.

**Jak lze zjistit, zašifrován Moje spravovaného disku?**

Můžete zjistit čas vytvoření spravovaného disku z webu Azure portal, rozhraní příkazového řádku Azure a Powershellu. Pokud je doba od 9. června 2017, se šifrují na disku.

**Jak můžete šifrovat Moje stávající disky, které byly vytvořeny před 10. června 2017?**

K 10. červnu 2017 budou automaticky šifrována nově zapsaná data do existující spravované disky. Plánujeme také šifrování existujících dat a šifrování bude probíhat asynchronně na pozadí. Pokud je nutné šifrovat všechna existující data, vytvořte kopii disku. Nové disky, které budou zašifrovaná.

* [Kopírování spravovaných disků pomocí rozhraní příkazového řádku Azure](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Kopírování spravovaných disků pomocí prostředí PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**Jsou spravované snímky a Image šifrovaná?**

Ano. Všechny spravované snímky a Image vytvořené po 9. června 2017, se šifrují automaticky. 

**Můžete převést virtuální počítače s nespravovanými disky, které se nacházejí v účtech úložiště, které jsou nebo se dříve zašifrována na spravované disky?**

Ano

**Exportovaná virtuální pevný disk ze spravovaného disku nebo snímek také zašifruje?**

Ne. Pokud exportujete virtuální pevný disk do zašifrovaném účtu úložiště ze šifrované, ale spravovaného disku nebo snímek a pak je šifrovaný. 

## <a name="premium-disks-managed-and-unmanaged"></a>Prémiové disky: Spravované a nespravované

**Pokud virtuální počítač používá velikost series, podporující Premium SSD disků, jako je například DSv2, můžu připojit na úrovni premium i standard datové disky?** 

Ano.

**Můžete připojit na úrovni premium i standard datových disků pro velikost řady, která nepodporuje Premium SSD disky, jako je například D, řada Dv2, F nebo G series?**

Ne. Pouze standardní datové disky můžete připojit k virtuálním počítačům, které nepoužívají velikost series, podporující Premium SSD disky.

**Pokud vytvořím datový disk premium z existujícího VHD, který byl 80 GB, kolik, která stojí?**

Vytvořit z virtuálního pevného disku 80 GB datový disk úrovně premium je považován za velikost disku k dispozici další úrovně premium, která je disku P10. Už se vám účtovat podle cen disku P10.

**Existují náklady za transakce, pokud chcete použít disky Premium SSD?**

Není k dispozici pro jednotlivé velikosti disku, který se dodává na vstupně-výstupních operací a propustnosti zřízené pomocí omezení hradit fixní částku. Další náklady jsou odchozí šířka pásma a kapacity snímku, pokud je k dispozici. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage).

**Jaká jsou omezení pro počet IOPS a propustnost, které můžu získat z mezipaměti disku?**

Kombinované omezení pro mezipaměť a místní disk SSD pro DS-series jsou 4 000 IOPS na jádro a 33 MiB za sekundu na jádro. Řada GS nabízí 5 000 IOPS na jádro a 50 MiB za sekundu na jádro.

**Je podporován místní disk SSD pro virtuální počítač spravované disky?**

Místní disk SSD je dočasné úložiště, která je součástí virtuálního počítače spravované disky. Zde je bez dalších poplatků pro toto dočasné úložiště. Doporučujeme tento místní disk SSD nepoužívejte k ukládání dat aplikací, protože není trvale uložena v úložišti objektů Blob v Azure.

**Existují jakékoli důsledky pro použití operace TRIM na disky premium?**

Neexistuje žádné nevýhodou použití operace TRIM na Azure disks na premium buď nebo disky standard.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Nové velikosti disků: Spravované a nespravované

**Co je největší velikost disku spravované podporováno pro operační systém a datové disky?**

Typ oddílu, které Azure podporuje pro disk s operačním systémem je hlavní spouštěcí záznam (MBR). Formát hlavního spouštěcího záznamu podporuje velikost disku až 2 TB. Největší velikost, které Azure podporuje pro disk s operačním systémem je 2 TB. Azure podporuje až 32 TB pro spravované datové disky. Spravované disky o velikosti větší než 4 TB jsou ve verzi preview. Další informace o nich najdete v našich [blogový příspěvek](https://aka.ms/azure-large-disk-32TB-preview-blog).

**Co je největší velikost nespravovaného disku podporované pro operační systém a datové disky?**

Typ oddílu, které Azure podporuje pro disk s operačním systémem je hlavní spouštěcí záznam (MBR). Formát hlavního spouštěcího záznamu podporuje velikost disku až 2 TB. Největší velikost, které Azure podporuje pro nespravovaný disk s operačním systémem je 2 TB. Azure podporuje až 4 TB pro nespravované disky data.

**Co je největší velikost objektu blob stránky, která je podporována?**

Největší velikost objektu blob stránky, které Azure podporuje je 8 TB (8191 GiB). Velikost objektu blob maximální stránky při připojení k virtuálnímu počítači jako data nebo disky operačního systému je 4 TB (4095 GiB).

**Je nutné použít na novou verzi nástroje Azure a vytvořit, připojit, změna velikosti a nahrát disků větších než 1 TB?**

Nemusíte upgradovat svoje stávající nástroje Azure k vytvoření, připojení nebo změna velikosti disků větších než 1 TB. K odeslání souboru VHD z místního přímo do Azure jako objekt blob stránky nebo nespravovaný disk, budete muset použít nejnovější sady nástrojů uvedených níže. Podporujeme jenom virtuální pevný disk nahrávání až do 8 TB.

|Nástroje Azure      | Podporované verze                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Číslo verze 4.1.0: Verzi z června 2017 nebo novější|
|Azure CLI v1     | Číslo verze 0.10.13: Verze. května 2017 nebo novější|
|Azure CLI v2     | Číslo verze 2.0.12: Verzi z července 2017 nebo novější|
|AzCopy           | Číslo verze 6.1.0: Verzi z června 2017 nebo novější|

**Podporují se velikosti disků P4 a P6 pro nespravované disky a objekty BLOB stránky?**

P4 (32 GB) a P6 (64 GiB) velikosti disků nejsou podporovány jako výchozí vrstvy disku pro nespravované disky a objekty BLOB stránky. Je potřeba explicitně [nastavit úroveň objektu Blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) P4 a P6 mají disku namapované k těmto vrstvám. Pokud provádíte nasazení nespravovaného disku nebo stránek blob se velikost disku nebo délka obsahu menší než 32 GiB nebo mezi 32 GB na 64 GB bez nastavení na úrovni objektů Blob, budou přesměrováni na P10 s 500 IOPS a 100 MiB/s a mapované cenovou úroveň.

**Pokud Můj existující premium managed disku menší než 64 GiB byl vytvořen před povolením malý disk (kolem 15. června 2017), jak se účtuje?**

Existující malé premium disky menší než 64 GiB služby nadále fakturovat podle cenové úrovně P10.

**Jak můžu přejít na úrovni disku v rámci malé prémiové disky menší než 64 GiB z P10 P4 nebo P6?**

Můžete pořídit snímek malé disky a pak vytvořte na cenovou úroveň se automaticky přepnout na P4 nebo P6 závisí na velikosti zřízeného disku.

**Můžete je změnit velikost existující spravované disky z velikostí menší než 4 TB pro nové nově zavedená disky o velikosti až 32 TB?**

Nové velikosti spravovaného disku 8 TB, 16 TiB a 32 TB jsou aktuálně ve verzi Preview. Dosud nepodporujeme změnu velikosti existujícího velikosti disků na nové velikosti disků.

**Co je největší velikostí disků, podporované službou Azure Backup a Azure Site Recovery?**

Největší velikost disku podporované službou Azure Backup a Azure Site Recovery je 4 TB.

**Co jsou doporučené virtuální počítač o velikosti pro velké disky o velikosti (> 4TiB) za SEKUNDU a šířky pásma na disku SSD na úrovni Standard a standardní HDD disky k dosažení optimalizovaná?**

K dosažení propustnost disku SSD na úrovni Standard a standardní HDD velké disky o velikosti (> 4TB) nad rámec 60 MiB/s a 500 IOPS, by měl používat jednu z následujících velikostí virtuálních počítačů pro optimalizaci výkonu: B-series, DSv2-series, Dsv3-Series, ESv3-Series, Fsv2-series, Fs-series, M-series, GS-series, NCv2-series, řady NCv3-series, nebo virtuální počítače řady Ls-Series.

**Které oblasti jsou spravovaný disk větší než 4 TB podporováno v?**

V tuto chvíli ve verzi preview jsou podporovány velikosti spravovaného disku v západní USA – střed, Severní Evropa, západní Evropa, USA – západ 2, USA – východ 2, USA – východ.

**Podporujeme povolení ukládání do mezipaměti hostitele na novější velikosti disků?**

Disky o velikosti menší než 4TiB podporujeme hostitele ukládání do mezipaměti ReadOnly a čtení a zápisu. Pro disky o velikosti více než 4 TB nepodporujeme nastavení ukládání do mezipaměti možnost Žádný. Doporučujeme, abyste využití ukládání do mezipaměti pro menší velikost disku, kde můžete očekávat sledovat lepší zvýšení výkonu s daty v mezipaměti k virtuálnímu počítači.

## <a name="what-if-my-question-isnt-answered-here"></a>Co když můj dotaz zde nenalezl?

Pokud zde není uveden váš dotaz, dejte nám vědět a pomůžeme vám najít odpověď. Odeslat dotaz na konci tohoto článku v komentářích. Kontaktovat tým Azure Storage a další členové komunity o tomto článku, použití MSDN [fórum služby Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).

Funkce požadavků, odeslání žádosti a nápady, jak ho [fóru pro zpětnou vazbu služby Azure Storage](https://feedback.azure.com/forums/217298-storage).
