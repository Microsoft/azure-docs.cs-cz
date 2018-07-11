---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 336e6e163178cd6d244460dbf9bee2a5bc9d714e
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935742"
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

* Vlastník: Může spravovat všechno včetně přístupu
* Přispěvatel: Může spravovat všechno kromě přístupu
* Čtenář: Může vše zobrazit, ale nemůže provádět změny

**Existuje způsob, můžu zkopírovat nebo exportovat spravovaného disku do účtu privátního úložiště?**

Můžete vygenerovat jen pro čtení sdíleného přístupového podpisu (SAS) identifikátor URI pro spravovaný disk a pomocí zkopírovat obsah do privátního úložiště účtu nebo místní úložiště. Můžete použít identifikátor URI SAS pomocí webu Azure portal, Azure Powershellu, rozhraní příkazového řádku Azure nebo [AzCopy](../articles/storage/common/storage-use-azcopy.md)

**Můžete vytvořit kopii Moje spravovaného disku?**

Zákazníkům můžete pořídit snímek jejich spravované disky a pak pomocí tohoto snímku rychle vytvořit jiného spravovaného disku.

**Jsou nespravované disky stále podporovány?**

Ano, spravované a nespravované disky se podporují. Doporučujeme použít spravované disky pro nové úlohy a aktuálních úloh migrace na spravované disky.


**Je-li vytvořit 128 GB disk a potom zvětšit velikost až 130 GB, bude mi účtovat další velikost disku (256 GB)?**

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

**Můžu na jednom virtuálním počítači vyhledejte společně spravované a nespravované disky?**

Ne.

## <a name="standard-ssd-disks-preview"></a>Standardní disky SSD (Preview)

**Co jsou disky SSD na úrovni Standard Azure?**
Standardní disky SSD jsou standardní disky zálohovanými SSD média, optimalizované jako nákladově efektivní úložiště pro úlohy, které je třeba zajistit konzistentní výkon na nižších úrovních vstupně-výstupních operací. Ve verzi preview jsou k dispozici v omezeném počtu oblastí, s omezenou správu (k dispozici prostřednictvím šablon Resource Manageru).

<a id="standard-ssds-azure-regions"></a>**Co jsou oblasti, které aktuálně podporuje pro disky SSD na úrovni Standard (Preview)?**
* Severní Evropa
* Francie – střed
* Východní USA 2
* Střed USA
* Střední Kanada
* Východní Asie
* Jižní Korea – jih
* Austrálie – východ

**Jak vytvořím disků SSD na úrovni Standard?**
V současné době můžete vytvořit pomocí šablon Azure Resource Manageru disků SSD na úrovni Standard. V následující tabulce jsou parametry potřebné v šabloně Resource Manageru k vytvoření standardní disky SSD:

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
Ano, je to možné. Odkazovat na [převést Azure managed disks storage úroveň ze standard na úrovni premium a naopak](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/convert-disk-storage) obecné pokyny pro převod na Managed Disks. A je možné, použijte tuto hodnotu Aktualizovat typ disku na SSD na úrovni Standard.
-AccountType StandardSSD_LRS

**Můžete použít standardní disky SSD jako nespravované disky?**
Ne, standardní disky SSD disky jsou dostupné jenom jako spravované disky.

**Podporují standardní disky SSD "jednu instanci SLA k virtuálním počítačům"?**
Ne, standardní disky SSD nemají jednu instanci SLA k virtuálním počítačům. Použití disků Premium SSD pro jednu instanci SLA k virtuálním počítačům.

## <a name="migrate-to-managed-disks"></a>Migrace na spravované disky 

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

## <a name="premium-disks-managed-and-unmanaged"></a>Disky Premium: spravovaných a nespravovaných

**Pokud virtuální počítač používá velikost series, podporující Premium SSD disků, jako je například DSv2, můžu připojit na úrovni premium i standard datové disky?** 

Ano.

**Můžete připojit na úrovni premium i standard datových disků pro velikost řady, která nepodporuje Premium SSD disky, jako je například D, řada Dv2, F nebo G series?**

Ne. Pouze standardní datové disky můžete připojit k virtuálním počítačům, které nepoužívají velikost series, podporující Premium SSD disky.

**Pokud vytvořím datový disk premium z existujícího VHD, který byl 80 GB, kolik, která stojí?**

Vytvořit z virtuálního pevného disku 80 GB datový disk úrovně premium je považován za velikost disku k dispozici další úrovně premium, která je disku P10. Už se vám účtovat podle cen disku P10.

**Existují náklady za transakce, pokud chcete použít disky Premium SSD?**

Není k dispozici pro jednotlivé velikosti disku, který se dodává na vstupně-výstupních operací a propustnosti zřízené pomocí omezení hradit fixní částku. Další náklady jsou odchozí šířka pásma a kapacity snímku, pokud je k dispozici. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage).

**Jaká jsou omezení pro počet IOPS a propustnost, které můžu získat z mezipaměti disku?**

Kombinované omezení pro mezipaměť a místní disk SSD pro DS-series jsou 4 000 IOPS na jádro a 33 MB za sekundu na jádro. Řada GS nabízí 5 000 IOPS na jádro a přes 50 MB za sekundu na jádro.

**Je podporován místní disk SSD pro virtuální počítač spravované disky?**

Místní disk SSD je dočasné úložiště, která je součástí virtuálního počítače spravované disky. Zde je bez dalších poplatků pro toto dočasné úložiště. Doporučujeme tento místní disk SSD nepoužívejte k ukládání dat aplikací, protože není trvale uložena v úložišti objektů Blob v Azure.

**Existují jakékoli důsledky pro použití operace TRIM na disky premium?**

Neexistuje žádné nevýhodou použití operace TRIM na Azure disks na premium buď nebo disky standard.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Nové velikosti disků: spravovaných a nespravovaných

**Co je největší velikost disku podporované pro operační systém a datové disky?**

Typ oddílu, které Azure podporuje pro disk s operačním systémem je hlavní spouštěcí záznam (MBR). Podporuje formát hlavního spouštěcího záznamu a disk velikost až do velikosti 2 TB. Největší velikost, které Azure podporuje pro disk s operačním systémem je 2 TB. Azure podporuje až na 4 TB pro datové disky. 

**Co je největší velikost objektu blob stránky, která je podporována?**

Největší velikost objektu blob stránky, které Azure podporuje je 8 TB (8191 GB). Maximální blogu velikost stránky při připojení k virtuálnímu počítači jako data nebo disky operačního systému je 4 TB (4 095 GB).

**Je nutné použít na novou verzi nástroje Azure a vytvořit, připojit, změna velikosti a nahrát disků větších než 1 TB?**

Nemusíte upgradovat svoje stávající nástroje Azure k vytvoření, připojení nebo změna velikosti disků větších než 1 TB. K odeslání souboru VHD z místního přímo do Azure jako objekt blob stránky nebo nespravovaný disk, budete muset použít nejnovější sady nástrojů:

|Nástroje Azure      | Podporované verze                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Číslo verze 4.1.0: verzi z června 2017 nebo novější|
|Azure CLI verze 1     | Číslo verze 0.10.13: uvolněte. května 2017 nebo novější|
|AzCopy           | Číslo verze 6.1.0: verzi z června 2017 nebo novější|

Podpora Azure CLI verze 2 a Průzkumníka služby Azure Storage je již brzy. 

**Podporují se velikosti disků P4 a P6 pro nespravované disky a objekty BLOB stránky?**

Ne. P4 (32 GB) a P6 (64 GB) velikosti disků jsou podporovány pouze pro spravované disky. Podpora pro objekty BLOB stránky a nespravované disky už brzy bude.

**Pokud Můj existující premium managed disku menší než 64 GB byl vytvořen před povolením malý disk (kolem 15. června 2017), jak se účtuje?**

Existující malé premium disky menší než 64 GB služby nadále fakturovat podle cenové úrovně P10. 

**Jak můžu přejít na úrovni disku malé prémiové disky menší než 64 GB z P10 P4 nebo P6?**

Můžete pořídit snímek malé disky a pak vytvořte na cenovou úroveň se automaticky přepnout na P4 nebo P6 závisí na velikosti zřízeného disku. 


## <a name="what-if-my-question-isnt-answered-here"></a>Co když můj dotaz zde nenalezl?

Pokud zde není uveden váš dotaz, dejte nám vědět a pomůžeme vám najít odpověď. Odeslat dotaz na konci tohoto článku v komentářích. Kontaktovat tým Azure Storage a další členové komunity o tomto článku, použití MSDN [fórum služby Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).

Funkce požadavků, odeslání žádosti a nápady, jak ho [fóru pro zpětnou vazbu služby Azure Storage](https://feedback.azure.com/forums/217298-storage).
