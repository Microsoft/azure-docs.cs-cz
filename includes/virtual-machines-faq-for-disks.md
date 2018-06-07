---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: rogara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: bf0853b137e65ddd6ad40483c50fc8debb62f920
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34826544"
---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Nejčastější dotazy týkající se disky virtuálního počítače Azure IaaS a spravovanými a nespravovanými prémiové disky

Tento článek obsahuje odpovědi na některé nejčastější dotazy týkající se Azure spravované disky a Azure Premium SSD disky.

## <a name="managed-disks"></a>Managed Disks

**Co je Azure spravované disky?**

Spravované disků je funkce, která zjednodušuje Správa disku pro virtuální počítače Azure IaaS tím, že zpracování Správa účtů úložiště pro vás. Další informace najdete v tématu [spravované disky přehled](../articles/virtual-machines/windows/managed-disks-overview.md).

**Když vytvořím standardní se spravovaným diskem z existujícího VHD, který je 80 GB, jaké budou který náklady mi?**

Standardní se spravovaným diskem vytvořen z disku VHD 80 GB je považována za další velikost standardní disku, která je k dispozici S10 disk. Že se vám účtovat podle S10 disku ceny. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage).

**Existují žádné transakce náklady na standardní spravované disky?**

Ano. Vám účtovat každou transakci. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage).

**Pro standardní se spravovaným diskem I odečte skutečná velikost dat na disku nebo zřízená kapacita disku?**

Že se vám účtovat podle zřízená kapacita disku. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage).

**Jak je ceny disků premium spravované liší od nespravované disky?**

Ceny spravované prémiové disky je stejný jako nespravované prémiové disky.

**Můžete změnit typ účtu úložiště (Standard nebo Premium) Moje spravovaných disků?**

Ano. Typ účtu úložiště spravovaného disky můžete změnit pomocí portálu Azure, PowerShell nebo rozhraní příkazového řádku Azure.

**Existuje způsob, že umožní kopírování a exportovat spravovaných disků na účet privátní úložiště?**

Ano. Vaše spravované disky můžete exportovat pomocí portálu Azure, PowerShell nebo rozhraní příkazového řádku Azure.

**Můžete použít soubor virtuálního pevného disku v účtu úložiště Azure k vytvoření spravovaného disk pomocí jiné předplatné?**

Ne.

**Můžete použít soubor virtuálního pevného disku v účtu úložiště Azure k vytvoření spravovaného disku v jiné oblasti?**

Ne.

**Existují nějaká omezení škálování pro zákazníky, kteří používají spravované disky?**

Spravované disky eliminuje omezení spojená s účty úložiště. Maximální limit a také výchozí limit je však 10 000 spravovaných disků na každou oblast a na typu disku pro předplatné.

**Může trvat přírůstkový snímek spravovaného disku?**

Ne. Aktuální vytváření snímků provede úplnou kopii se spravovaným diskem.

**Virtuální počítače v nastavení dostupnosti se může skládat z kombinace spravovanými a nespravovanými disky?**

Ne. Virtuální počítače v nastavení dostupnosti musí používat disky všechny spravované nebo nespravované všechny disky. Když vytvoříte skupinu dostupnosti, můžete typu disky, které chcete použít.

**Spravované disků je výchozí možnost na portálu Azure?**

Ano. 

**Můžete vytvořit prázdný disk spravované?**

Ano. Můžete vytvořit prázdný disk. Spravovaný disk můžete vytvořit nezávisle na virtuální počítač, například bez připojení k virtuálnímu počítači.

**Co je počet domén selhání podporované pro dostupnosti nastavit používající spravované disky?**

V závislosti na oblasti, kde se nachází skupiny dostupnosti, který používá disky spravovat je počet domén selhání podporované 2 nebo 3.

**Jak je účet standardního úložiště pro diagnostiku nastavení?**

Nastavit účet privátní úložiště pro diagnostiku virtuálního počítače.

**Jaký druh řízení přístupu na základě Role podpora je k dispozici pro spravované disky?**

Spravované disky podporuje tři klíčové výchozích rolí:

* Vlastník: Můžou spravovat všechno včetně přístupu
* Přispěvatel: Můžou spravovat všechno kromě přístupu
* Čtečka: Zobrazit vše, co, ale nelze provádět změny

**Existuje způsob, že umožní kopírování a exportovat spravovaných disků na účet privátní úložiště?**

Můžete získat jen pro čtení sdíleného přístupového podpisu identifikátor URI pro spravované disk a použít ho zkopírovat obsah do privátní úložiště účet nebo místní úložiště.

**Můžete vytvořit kopii Moje spravovaného disku?**

Zákazníci můžete pořízení snímku jejich spravované disky a potom pomocí snímku vytvoření spravovaných disků na jiný.

**Jsou nespravované disky stále podporovány?**

Ano, jsou podporovány nespravované a spravované disky. Doporučujeme používat spravované disky pro nové úlohy a migraci vašich aktuální zatížení na spravované disky.


**Je-li vytvořit 128 GB disk a poté zvýšit velikost 130 GB, bude I vám účtována další velikost disku (512 GB)?**

Ano.

**Můžete vytvořit místně redundantní úložiště, geograficky redundantní úložiště, a zónově redundantní úložiště spravované disky?**

Disky systému Azure spravované aktuálně podporuje pouze místně redundantního úložiště spravované disky.

**Můžete zmenšit nebo downsize Moje spravované disky?**

Ne. Tato funkce není aktuálně podporován. 

**Můžete rozdělit zapůjčení na můj disku?**

Ne. To není aktuálně podporováno jako zapůjčení je k dispozici, aby se zabránilo nechtěnému odstranění při použití disku.

**Můžete změnit vlastnost název počítače při specializované (ne vytvořili pomocí nástroje pro přípravu systému nebo zobecněn) disk operačního systému slouží ke zřízení virtuálního počítače?**

Ne. Nelze aktualizovat vlastnosti název počítače. Nový virtuální počítač se dědí z nadřazený virtuální počítač, který byl použit k vytvoření disku operačního systému. 

**Kde najdu ukázkových šablon Azure Resource Manageru k vytvoření virtuálních počítačů s spravované disky?**
* [Seznam šablon pomocí spravovaných disků](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

## <a name="standard-ssd-disks-preview"></a>Standardní disky SSD (Preview)

**Co jsou disky SSD standardní Azure?**
Standardní disky SSD jsou standardní disky založenou na SSD média, optimalizované jako nákladově efektivní úložiště pro úlohy, které je třeba konzistentní výkon na nižších úrovních IOPS. Ve verzi preview jsou k dispozici pro omezený počet oblastí se omezené možnosti správy (k dispozici prostřednictvím šablon Resource Manager).

<a id="standard-ssds-azure-regions"></a>**Jaké jsou oblasti, které aktuálně podporuje standardní SSD disků (Preview)?**
* Severní Evropa

**Jak vytvořit standardní SSD disky?**
V současné době můžete vytvořit standardní SSD disky pomocí šablony Azure Resource Manager. V následující tabulce jsou potřebné v šabloně Resource Manager vytvořit standardní disky SSD parametry:

* *apiVersion* pro Microsoft.Compute musí být nastavené jako `2018-04-01` (nebo novější)
* Zadejte *managedDisk.storageAccountType* jako `StandardSSD_LRS`

Následující příklad ukazuje *properties.storageProfile.osDisk* části pro virtuální počítač, který používá standardní disky SSD:

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

Úplnou šablonu příklad toho, jak vytvořit standardní SSD disk s využitím šablony najdete v tématu [vytvořte virtuální počítač z bitové kopie systému Windows s standardní datových disků SSD](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

**Můžete použít standardní SSD jako nespravované disky?**
Ne, standardní SSD disky jsou dostupné jen jako spravované disky.

## <a name="migrate-to-managed-disks"></a>Migrace na spravované disky 

**Jaké změny jsou potřeba v existující Azure Backup service před nebo za migraci konfigurace na spravovaných disky?**

Nejsou vyžadovány žádné změny. 

**Bude Moje zálohování virtuálních počítačů vytvořené prostřednictvím služby Azure Backup před migrací pokračovat v práci?**

Ano, zálohování funguje bez problémů.

**Jaké změny jsou potřeba v existující šifrování disků Azure konfigurace před nebo po migraci na spravované disky?**

Nejsou vyžadovány žádné změny. 

**Je automatické migrace existující virtuální počítač měřítka sad z nespravovaných disky na disky spravované podporovány?**

Ne. Můžete vytvořit nové škálování nastavit s disky spravované pomocí bitové kopie z vašeho původního sad s nespravované disky škálování. 

**Můžete vytvořit na Disk spravovaný ze snímku objekt blob stránky prováděné před migrací na spravované disky?**

Ne. Můžete exportovat snímek objekt blob stránky jako objekt blob stránky a pak vytvořit spravované Disk z objektu blob exportovaný stránky. 

**Můžete převzít Moje místní počítače chráněné službou Azure Site Recovery pro virtuální počítač s spravované disky?**

Ano, můžete k převzetí služeb při selhání pro virtuální počítač s spravované disky.

**Je k dispozici žádný vliv migrace na virtuálních počítačích Azure, které jsou chráněné službou Azure Site Recovery prostřednictvím replikace Azure do Azure?**

Ano. V současné době Azure do Azure ochrany Azure Site Recovery pro virtuální počítače s spravované disky je k dispozici pouze jako služby ve verzi public preview.

**Můžete migrovat virtuální počítače s nespravované disky, které se nacházejí na účtech úložiště, které jsou nebo byly dříve šifrovaná na spravované disky?**

Ano

## <a name="managed-disks-and-storage-service-encryption"></a>Spravované disky a šifrování služby úložiště 

**Šifrování služby úložiště Azure ve výchozím nastavení zapnutá po vytvoření se spravovaným diskem?**

Ano.

**Kdo spravuje šifrovacích klíčů?**

Microsoft spravuje šifrovací klíče.

**Můžete zakázat šifrování služby úložiště pro moje spravované disky?**

Ne.

**Je šifrování služby úložiště k dispozici pouze v určitých oblastí?**

Ne. Je k dispozici ve všech oblastech, kde spravované disky jsou dostupné. Spravované disků je k dispozici ve všech veřejných oblastí a Německu.

**Jak můžete zjistit, pokud je zašifrovaná Moje spravovaných disků?**

Můžete získat čas vytvoření spravovaného disku z portálu Azure, rozhraní příkazového řádku Azure a prostředí PowerShell. Pokud je doba po 9. června 2017, se šifrují na disku. 

**Jak můžete šifrovat Můj stávající disky, které byly vytvořeny před 10 června 2017?**

Od verze 10 června 2017 je nová data stávající spravovaný disky šifrují automaticky. Můžeme také plánování šifrování stávající data a šifrování asynchronně proběhne na pozadí. Pokud je nutné šifrovat všechna existující data, vytvoří se kopie disku. Bude se šifrovat nové disky.

* [Zkopírovat disky spravované pomocí rozhraní příkazového řádku Azure](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Zkopírujte spravované disky pomocí prostředí PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**Jsou spravované snímky a bitové kopie zašifrovaná?**

Ano. Všechny spravované snímky a bitové kopie vytvořené po 9. června 2017, se šifrují automaticky. 

**Můžete převést virtuální počítače s nespravované disky, které se nacházejí na účtech úložiště, které jsou nebo byly dříve šifrovaná na spravované disky?**

Ano

**Budou exportovaného virtuálního pevného disku z spravovaného disku nebo snímek také zašifrovaná?**

Ne. Pokud exportujete virtuální pevný disk k účtu úložiště šifrované z šifrované, ale spravované disku nebo snímek a potom je zašifrovaná. 

## <a name="premium-disks-managed-and-unmanaged"></a>Pro prémiové disky: spravovaných a nespravovaných

**Pokud virtuální počítač používá velikost série, která podporuje Premium SSD disky, například DSv2, můžu připojit premium a standard datové disky?** 

Ano.

**Můžete připojit premium a standard datových disků pro velikost série, která nepodporuje Premium SSD disky, například D Dv2, G nebo F řady?**

Ne. Pouze standardní datových disků můžete připojit k virtuálním počítačům, které nepoužívají velikost série, která podporuje Premium SSD disky.

**Když vytvořím premium datový disk z existujícího VHD, který byl 80 GB, kolik bude která stojí?**

Datový disk premium vytvořen z disku VHD 80 GB je považována za velikost disku k dispozici další premium, což je P10 disku. Že se vám účtovat podle P10 disku ceny.

**Existují transakce náklady na Premium SSD disky používat?**

Není opravené náklady pro každou velikost disku, která pochází zřízené pomocí omezení na IOPS a propustnosti. Další náklady jsou šířky odchozího pásma a kapacity snímku, pokud je k dispozici. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage).

**Jaké jsou limity pro IOPS a propustnosti, kterou můžete dostat z mezipaměti disku?**

Kombinované omezení pro mezipaměť a místní SSD pro řady DS jsou 4 000 IOPS na základní a 33 MB za sekundu za jádra. GS řady nabízí 5 000 IOPS na základní a 50 MB za sekundu za jádra.

**Je podporovaný na místní SSD pro virtuální počítač spravovaný disky?**

Na místní SSD je dočasné úložiště, která je součástí spravované virtuální disky. Existuje nejsou zpoplatněné pro toto dočasný úložiště. Doporučujeme vám, nepoužívejte tento místní SSD ukládat data aplikací, protože není trvalý v Azure Blob storage.

**Existují jakékoli následky pro použití operace TRIM na prémiové disky?**

Neexistuje žádné nevýhodou použití operace TRIM na disky Azure na buď premium nebo standardní disky.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Nové velikosti disků: spravovaných a nespravovaných

**Co je podporováno pro operační systém a datové disky největší velikost disku?**

Typ oddílu, který podporuje Azure pro disk operačního systému je hlavní spouštěcí záznam (MBR). Podporuje formátu MBR disk velikost až 2 TB. Největší velikost, kterou podporuje Azure pro disk operačního systému je 2 TB. Azure podporuje až 4 TB pro datové disky. 

**Co je největší velikost objektu blob stránky, která je podporována?**

Největší velikost objektu blob stránky, který podporuje Azure je 8 TB (8 191 GB). Velikost maxmium stránky blogu při připojené k virtuálnímu počítači jako data nebo disky operačního systému je 4 TB (4095 GB).

**Je potřeba použít novou verzi nástroje Azure vytvořit, připojení, přizpůsobit a odeslat disky, které jsou větší než 1 TB?**

Není nutné upgradovat existující nástroje Azure k vytvoření, připojit nebo změna velikosti disků, které jsou větší než 1 TB. K odeslání souboru virtuálního pevného disku z místního přímo do Azure jako objekt blob stránky nebo nespravované disku, budete muset použít nejnovější sady nástrojů:

|Nástroje Azure      | Podporované verze                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Číslo verze 4.1.0: červen 2017 verzi nebo novější|
|Rozhraní příkazového řádku Azure v1     | Číslo verze 0.10.13: pravděpodobně 2017 verzi nebo novější|
|AzCopy           | Číslo verze 6.1.0: červen 2017 verzi nebo novější|

Podpora rozhraní příkazového řádku Azure v2 a Azure Storage Explorer tu bude brzo dostupná. 

**Jsou podporovány P4 a P6 velikosti disků pro nespravovaná disky nebo objekty BLOB stránky?**

Ne. P4 (32 GB) a P6 velikosti disků (64 GB) jsou podporovány pouze u spravovaných disky. Podpora pro nespravovaná disky a objekty BLOB stránky bude brzo.

**Pokud mé existující premium spravované disku menší než 64 GB byl vytvořen před povolením malý disk (přibližně 15. června 2017), jak se jeho fakturuje?**

Stávající malých premium disky menší než 64 GB dál účtovat podle cenové úrovně P10. 

**Jak můžete přepnout na úrovni disku malé prémiové disky menší než 64 GB z P10 P4 nebo P6?**

Můžete pořízení snímku menší disky a potom vytvořit disk automaticky přepínat cenové úrovně P4 nebo P6 podle zřízené velikost. 


## <a name="what-if-my-question-isnt-answered-here"></a>Co když není zde odpovědi můj dotaz?

Pokud váš dotaz není zde uvedeno, dejte nám vědět, a pomůžeme vám najít odpověď. V komentářích můžete odeslat dotaz na konci tohoto článku. Chcete-li se spojte s týmu Azure Storage a ostatními členy komunity o tomto článku, použijte webu MSDN [fórum pro Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).

Chcete-li požádat o funkce, odeslání požadavků a nápady, jak [fóru pro zpětnou vazbu Azure Storage](https://feedback.azure.com/forums/217298-storage).
