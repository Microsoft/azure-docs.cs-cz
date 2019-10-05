---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/13/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 155ca71ae30559cc79e090a8a7bbc12c896b637f
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973003"
---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Nejčastější dotazy k diskům virtuálních počítačů Azure IaaS a spravovaným a nespravovaným prémiovým diskům

Tento článek obsahuje odpovědi na některé nejčastější dotazy týkající se Azure Managed Disks a Azure SSD úrovně Premium disků.

## <a name="managed-disks"></a>Spravované disky

**Co je Azure Managed Disks?**

Managed Disks je funkce, která zjednodušuje správu disků pro virtuální počítače Azure s IaaS tím, že zpracovává správu účtu úložiště za vás. Další informace najdete v [přehledu Managed disks](../articles/virtual-machines/windows/managed-disks-overview.md).

**Když vytvořím Standard Managed disk z existujícího virtuálního pevného disku, který je 80 GB, bude se vám to stát?**

Standardní spravovaný disk vytvořený z disku VHD 80 GB se považuje za další dostupnou velikost standardního disku, což je S10 disk. Účtuje se vám podle ceny za S10 disk. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage).

**Jsou nějaké transakční náklady na standard Managed disks?**

Ano. Účtují se vám poplatky za jednotlivé transakce. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage).

**U standardního spravovaného disku se mi bude účtovat skutečná velikost dat na disku nebo zajištěná kapacita disku?**

Účtují se vám poplatky podle zřízené kapacity disku. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage).

**Jak se ceny za spravované disky úrovně Premium liší od nespravovaných disků?**

Ceny za spravované disky úrovně Premium jsou stejné jako nespravované prémiové disky.

**Můžu změnit typ účtu úložiště (Standard nebo Premium) mých spravovaných disků?**

Ano. Typ účtu úložiště vašich spravovaných disků můžete změnit pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure.

**Můžu použít soubor VHD v účtu služby Azure Storage k vytvoření spravovaného disku s jiným předplatným?**

Ano.

**Můžu k vytvoření spravovaného disku v jiné oblasti použít soubor VHD v účtu služby Azure Storage?**

Ne.

**Existují nějaká omezení škálování pro zákazníky, kteří používají spravované disky?**

Managed Disks eliminuje limity spojené s účty úložiště. Maximální limit je ale 50 000 spravovaných disků na oblast a na typ disku pro předplatné.

**Můžu získat přírůstkový snímek spravovaného disku?**

Ne. Aktuální schopnost snímku vytvoří úplnou kopii spravovaného disku.

**Je možné virtuální počítače ve skupině dostupnosti sestávat z kombinace spravovaných a nespravovaných disků?**

Ne. Virtuální počítače ve skupině dostupnosti musí používat buď všechny spravované disky, nebo všechny nespravované disky. Když vytvoříte skupinu dostupnosti, můžete zvolit, který typ disků chcete použít.

**Je Managed Disks výchozí možnost v Azure Portal?**

Ano.

**Můžu vytvořit prázdný spravovaný disk?**

Ano. Můžete vytvořit prázdný disk. Spravovaný disk se dá vytvořit nezávisle na virtuálním počítači, například bez připojení k virtuálnímu počítači.

**Jaký je počet podporovaných domén selhání pro skupinu dostupnosti, která používá Managed Disks?**

V závislosti na oblasti, ve které se nachází skupina dostupnosti, která používá Managed Disks, je počet podporovaných domén selhání 2 nebo 3.

**Jak se má nastavit standardní účet úložiště pro diagnostiku?**

Nastavíte privátní účet úložiště pro diagnostiku virtuálních počítačů.

**Jaký druh podpory Access Control na základě rolí je k dispozici pro Managed Disks?**

Managed Disks podporuje tři výchozí role klíče:

* Vlastník: může spravovat všechno, včetně přístupu.
* Přispěvatel: může spravovat všechno kromě přístupu.
* Čtecí modul: může zobrazit vše, ale nemůže provádět změny.

**Existuje způsob, jak mohu zkopírovat nebo exportovat spravovaný disk do účtu privátního úložiště?**

Pro spravovaný disk můžete vygenerovat identifikátor URI sdíleného přístupového podpisu (SAS) jen pro čtení a použít ho ke zkopírování obsahu do soukromého účtu úložiště nebo místního úložiště. Identifikátor URI SAS můžete použít pomocí Azure Portal, Azure PowerShell, rozhraní příkazového řádku Azure nebo [AzCopy](../articles/storage/common/storage-use-azcopy.md)

**Můžu vytvořit kopii spravovaného disku?**

Zákazníci si můžou pořídit snímek svých spravovaných disků a potom použít snímek k vytvoření dalšího spravovaného disku.

**Jsou pořád podporované nespravované disky?**

Ano, podporují se nespravované i spravované disky. Pro nové úlohy doporučujeme používat spravované disky a migrovat aktuální úlohy na spravované disky.

**Můžu na stejném virtuálním počítači společně najít nespravované a spravované disky?**

Ne.

**Když vytvořím disk 128 GB a pak se velikost zvýší na 130 gibibajtech (GiB), bude se mi účtovat další velikost disku (256 GiB)?**

Ano.

**Můžu vytvářet místně redundantní úložiště, geograficky redundantní úložiště a disky spravované úložištěm redundantní zóny?**

Služba Azure Managed Disks aktuálně podporuje jenom místně redundantní disky spravované úložiště.

**Můžu své spravované disky zmenšit nebo klidnějších?**

Ne. Tato funkce se momentálně nepodporuje.

**Můžu na svém disku přerušit zapůjčení?**

Ne. Tato operace se momentálně nepodporuje, protože je k dispozici zapůjčení, aby se zabránilo nechtěnému odstranění při použití disku.

**Můžu změnit vlastnost název počítače, když se pro zřízení virtuálního počítače použije specializovaná (nevytvořená pomocí nástroje pro přípravu systému nebo zobecněný) disk s operačním systémem?**

Ne. Nemůžete aktualizovat vlastnost název počítače. Nový virtuální počítač dědí z nadřazeného virtuálního počítače, který se použil k vytvoření disku operačního systému. 

**Kde najdu ukázkové Azure Resource Manager šablony pro vytváření virtuálních počítačů se spravovanými disky?**
* [Seznam šablon pomocí Managed Disks](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

**Při vytváření disku z objektu BLOB se u tohoto zdrojového objektu BLOB nějaký nepřetržitě existující relace používá?**

Ne, při vytvoření nového disku je v tuto chvíli úplná samostatná kopie tohoto objektu BLOB a mezi nimi není žádné spojení. Pokud budete chtít, můžete zdrojový objekt BLOB po vytvoření odstranit, aniž by to mělo vliv na nově vytvořený disk jakýmkoli způsobem.

**Můžu po vytvoření přejmenovat spravovaný nebo nespravovaný disk?**

U spravovaných disků je nemůžete přejmenovat. Nespravovaný disk ale můžete přejmenovat, pokud není aktuálně připojený k virtuálnímu pevnému disku VHD nebo virtuálnímu počítači.

**Můžu na disku Azure použít dělení na oddíly GPT?**

Dělení GPT se dá použít jenom na datových discích, nikoli na discích s operačním systémem. Disky s operačním systémem musí používat styl oddílu MBR.

**Jaké typy disků podporují snímky?**

Snímky podpory SSD úrovně Premium, Standard SSD a standardní HDD. U těchto tří typů disků se snímky podporují pro všechny velikosti disků (včetně disků až do 32 TiB velikosti). Disky Ultra nepodporují snímky.

## <a name="ultra-disks"></a>Disky Ultra

**Jaké oblasti v současné době podporují Ultra disks?**
- Východ USA 2
- Jihovýchodní Asie
- Severní Evropa

**Jaké řady virtuálních počítačů v současné době podporují Ultra disks?**
- ESv3
- DSv3

**Jak mám nastavit propustnost Ultra disk na?**
Pokud si nejste jistí, co nastavit propustnost disku, doporučujeme začít tím, že zadáte velikost vstupně-výstupní operace 16 KiB a upravíte výkon při monitorování aplikace. Vzorec je: propustnost v MB/s = # IOPS * 16/1000.

**Nakonfigurovali jsem disk na 40000 IOPS, ale zobrazuje se pouze 12800 IOPS, proč mi nevidím výkon disku?**
Kromě omezení disku existuje omezení vstupně-výstupní operace, která je uložena na úrovni virtuálního počítače. Ujistěte se prosím, že velikost virtuálního počítače, kterou používáte, může podporovat úrovně, které jsou nakonfigurované na vašich discích. Podrobnosti týkající se omezení v/v, které ukládá váš virtuální počítač, najdete v tématu [velikosti pro virtuální počítače s Windows v Azure](../articles/virtual-machines/windows/sizes.md).

**Můžu používat úrovně ukládání do mezipaměti s Ultra diskem?**
Ne, Ultra disky nepodporují různé metody ukládání do mezipaměti, které jsou podporované na jiných typech disků. Nastavte ukládání disku do mezipaměti na žádné.

**Můžu k existujícímu virtuálnímu počítači připojit disk Ultra?**
Je možné, že váš virtuální počítač musí být v oblasti a páru zón dostupnosti, které podporují disky Ultra. Podrobnosti najdete v tématu [Začínáme s disky Ultra](../articles/virtual-machines/windows/disks-enable-ultra-ssd.md) .

**Můžu jako disk s operačním systémem pro svůj virtuální počítač použít disk Ultra?**
Ne, Ultra disky se podporují jenom jako datové disky a podporují se jenom jako 4K nativní disky.

**Můžu převést existující disk na disk Ultra?**
Ne, můžete ale migrovat data z existujícího disku na disk Ultra. Pokud chcete migrovat existující disk na disk Ultra, připojte oba disky ke stejnému virtuálnímu počítači a zkopírujte data disku z jednoho disku na druhý nebo využijte řešení třetích stran pro migraci dat.

**Můžu vytvářet snímky pro disky Ultra?**
Ne, snímky ještě nejsou k dispozici.

**Je Azure Backup k dispozici pro disky Ultra?**
Ne, podpora Azure Backup ještě není k dispozici.

**Můžu k virtuálnímu počítači se systémem v sadě dostupnosti připojit disk Ultra?**
Ne, tato není ještě podporovaná.

**Můžu povolit Azure Site Recovery pro virtuální počítače s využitím disků Ultra?**
Ne, Azure Site Recovery ještě není pro disky Ultra podporována.

## <a name="uploading-to-a-managed-disk"></a>Nahrávání na spravovaný disk

**Můžu nahrávat data na existující spravovaný disk?**

Ne, nahrávání se dá použít jenom při vytváření nového prázdného disku se stavem **ReadyToUpload** .

**Návody nahrát na spravovaný disk?**

Vytvořte spravovaný disk s vlastností [CreateOption](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#diskcreateoption) [creationData](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#creationdata) nastavenou na Odeslat a potom do něj můžete nahrát data.

**Můžu k virtuálnímu počítači připojit disk, když je ve stavu nahrávání?**

Ne.

**Můžu pořídit snímek spravovaných disku ve stavu nahrávání?**

Ne.

## <a name="standard-ssd-disks"></a>SSD úrovně Standard disky

**Co jsou disky Azure SSD úrovně Standard?**
SSD úrovně Standard disky jsou standardní disky řízené médii s pevným stavem, optimalizované jako nákladově efektivní úložiště pro úlohy, které vyžadují konzistentní výkon na nižší úrovni IOPS.

<a id="standard-ssds-azure-regions"></a>**Jaké jsou aktuálně podporované oblasti pro SSD úrovně Standard disky?**
Všechny oblasti Azure teď podporují SSD úrovně Standard disky.

**Je Azure Backup k dispozici při použití standardního SSD?**
Ano, Azure Backup je teď k dispozici.

**Návody vytvořit SSD úrovně Standard disky?**
SSD úrovně Standard disky můžete vytvořit pomocí šablon Azure Resource Manager, sady SDK, PowerShellu nebo rozhraní příkazového řádku. Níže jsou uvedeny parametry, které jsou potřeba v šabloně Správce prostředků k vytvoření SSD úrovně Standard disků:

* *apiVersion* pro Microsoft. COMPUTE musí být nastavená na `2018-04-01` (nebo novější).
* Zadejte *managedDisk. storageAccountType* jako `StandardSSD_LRS`.

Následující příklad ukazuje oddíl *Properties. storageProfile. osDisk* pro virtuální počítač, který používá SSD úrovně Standard disky:

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

Kompletní příklad vytvoření SSD úrovně Standard disku pomocí šablony najdete v tématu [Vytvoření virtuálního počítače z image Windows s datovými disky SSD úrovně Standard](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

**Můžu převést existující disky na SSD úrovně Standard?**
Ano, je to možné. Přečtěte si téma [Převod úložiště Azure Managed disks z úrovně Standard na Premium a naopak](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) pro obecné pokyny k převodu Managed disks. Pomocí následující hodnoty můžete aktualizovat typ disku na SSD úrovně Standard.
– AccountType StandardSSD_LRS

**Jaká je výhoda použití SSD úrovně Standard disků místo HDD?**
SSD úrovně Standard disky dodávají lepší latenci, konzistenci, dostupnost a spolehlivost v porovnání s disky HDD. Úlohy aplikací běží na SSD úrovně Standard mnohem plynule. Upozorňujeme, že SSD úrovně Premium disky jsou doporučeným řešením pro většinu produkčních úloh náročných na vstupně-výstupní operace.

**Můžu používat standardní SSD jako nespravované disky?**
Ne, standardní disky SSD jsou k dispozici pouze jako Managed Disks.

**Podporuje SSD úrovně Standard disky smlouvu SLA pro virtuální počítače s jednou instancí?**
Ne, standardní SSD nemají smlouvu SLA pro virtuální počítače s jednou instancí. Pro smlouvu SLA pro virtuální počítače s jednou instancí použijte SSD úrovně Premium disky.

## <a name="migrate-to-managed-disks"></a>Migrace na Spravované disky

**Existuje dopad migrace na Managed Disks výkon?**

Migrace zahrnuje přesun disku z jednoho umístění úložiště do druhého. Tato aplikace je organizována prostřednictvím kopírování dat na pozadí, což může trvat několik hodin, obvykle méně než 24 hodin v závislosti na množství dat na discích. Během této doby může aplikace docházet k vyšší latenci při čtení, protože některé čtení se může přesměrovat na původní umístění a dokončení může trvat déle. Během této doby není nijak ovlivněna latence zápisu.  

**Jaké změny se vyžadují v existující konfiguraci služby Azure Backup před migrací do Managed Disks?**

Nejsou vyžadovány žádné změny.

**Budou moje zálohy virtuálních počítačů vytvořené prostřednictvím služby Azure Backup před tím, než migrace zůstane fungovat?**

Ano, zálohování funguje bez problémů.

**Jaké změny se vyžadují v dříve existující konfiguraci šifrování disků Azure před migrací na Managed Disks?**

Nejsou vyžadovány žádné změny.

**Je automatizovaná migrace stávající sady škálování virtuálních počítačů z nespravovaných disků na Managed Disks podporovaná?**

Ne. Novou sadu škálování můžete vytvořit pomocí Managed Disks pomocí image ze své staré sady škálování s nespravovanými disky.

**Můžu před migrací na Managed Disks vytvořit spravovaný disk z snímku objektu blob stránky?**

Ne. Snímek objektu blob stránky můžete exportovat jako objekt blob stránky a pak vytvořit spravovaný disk z exportovaného objektu blob stránky.

**Můžu převzít služby při selhání místních počítačů chráněných Azure Site Recovery k virtuálnímu počítači s Managed Disks?**

Ano, můžete se rozhodnout pro převzetí služeb při selhání virtuálního počítače pomocí Managed Disks.

**Je nějaký dopad migrace na virtuální počítače Azure chráněné Azure Site Recovery přes Azure do Azure – replikace?**

Ne. K dispozici je Azure Site Recovery ochrany Azure do Azure pro virtuální počítače s Managed Disks.

**Můžu migrovat virtuální počítače s nespravovanými disky, které se nacházejí v účtech úložiště, které jsou nebo byly předtím zašifrované na spravované disky?**

Ano

## <a name="managed-disks-and-storage-service-encryption"></a>Managed Disks a Šifrování služby Storage

**Je šifrování služby Azure Storage ve výchozím nastavení povolené při vytváření spravovaného disku?**

Ano.

**Je spouštěcí svazek ve výchozím nastavení zašifrovaný na spravovaném disku?**

Ano. Ve výchozím nastavení jsou všechny spravované disky zašifrované, včetně disku s operačním systémem.

**Kdo spravuje šifrovací klíče?**

Microsoft spravuje šifrovací klíče.

**Můžu zakázat Šifrování služby Storage pro moje spravované disky?**

Ne.

**Je Šifrování služby Storage k dispozici pouze v konkrétních oblastech?**

Ne. Je k dispozici ve všech oblastech, kde jsou Managed Disks k dispozici. Managed Disks je k dispozici ve všech veřejných oblastech a Německu. Je dostupná i v Číně, ale jenom pro spravované klíče Microsoftu, ne pro spravované klíče zákazníka.

**Jak zjistím, jestli je můj spravovaný disk zašifrovaný?**

Čas vytvoření spravovaného disku můžete zjistit z Azure Portal, Azure CLI a PowerShellu. Pokud je čas po 9. června 2017, je disk zašifrovaný.

**Jak můžu šifrovat stávající disky vytvořené před 10. června 2017?**

Od 10. června 2017 se nová data zapsaná na existující spravované disky automaticky šifrují. Také plánujeme zašifrovat stávající data a na pozadí proběhne asynchronní šifrování. Pokud teď musíte šifrovat existující data, vytvořte kopii disku. Nové disky budou zašifrovány.

* [Kopírování spravovaných disků pomocí Azure CLI](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Kopírování spravovaných disků pomocí prostředí PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**Jsou spravované snímky a obrázky zašifrované?**

Ano. Všechny spravované snímky a image vytvořené po 9. června 2017 se automaticky šifrují. 

**Můžu převést virtuální počítače s nespravovanými disky, které se nacházejí v účtech úložiště, které jsou nebo byly předtím zašifrované na spravované disky?**

Ano

**Bude exportovaný virtuální pevný disk ze spravovaného disku nebo snímku taky zašifrovaný?**

Ne. Pokud ale exportujete VHD na zašifrovaný účet úložiště ze zašifrovaného spravovaného disku nebo snímku, bude zašifrovaný. 

## <a name="premium-disks-managed-and-unmanaged"></a>Disky úrovně Premium: spravované a nespravované

**Pokud virtuální počítač používá řadu velikostí, která podporuje SSD úrovně Premium disky, jako je DSv2, můžu připojit datové disky Premium i Standard?** 

Ano.

**Můžu připojit datové disky Premium i Standard k řadě velikostí, která nepodporuje SSD úrovně Premium disky, jako je D, Dv2, G nebo F Series?**

Ne. K virtuálním počítačům, které nepoužívají řadu velikostí, která podporuje SSD úrovně Premium disky, můžete připojit pouze standardní datové disky.

**Když vytvořím datový disk Premium z existujícího virtuálního pevného disku, který byl 80 GB, kolik to bude mít za cenu?**

Datový disk Premium vytvořený z disku VHD 80 GB se považuje za další dostupnou velikost disku Premium, což je P10 disk. Účtuje se vám podle ceny za P10 disk.

**Používají se pro SSD úrovně Premium disky náklady na transakci?**

Pro každou velikost disku se účtují pevné náklady, které se zřídí s konkrétními limity pro IOPS a propustnost. Ostatní náklady jsou odchozí šířka pásma a kapacita snímku (Pokud je k dispozici). Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage).

**Jaká jsou omezení pro IOPS a propustnost, kterou můžu získat z mezipaměti disku?**

Kombinovaná omezení pro mezipaměť a místní SSD pro řady DS jsou 4 000 vstupně-výstupních operací za jádro a 33 MiB za sekundu na jádro. Řada GS nabízí 5 000 IOPS za jádro a 50 MiB za sekundu na jádro.

**Podporuje Managed Disks virtuální počítač místní SSD?**

Místní jednotka SSD je dočasné úložiště, které je součástí Managed Disks virtuálního počítače. Pro toto dočasné úložiště se neúčtují žádné další poplatky. K ukládání dat aplikací doporučujeme nepoužívat tento místní jednotku SSD, protože není trvalá v úložišti objektů BLOB v Azure.

**Máte nějaké dopady na použití OŘEZÁVÁNÍ na prémiových discích?**

K použití OŘEZÁVÁNÍ na discích Azure na discích úrovně Premium nebo Standard neexistuje žádný nevýhodou.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Nové velikosti disků: spravované a nespravované

**Jaká je největší velikost spravovaného disku podporovaná pro operační systém a datové disky?**

Typ oddílu, který Azure podporuje pro disk s operačním systémem, je hlavním spouštěcím záznamem (MBR). Formát MBR podporuje velikost disku až 2 TiB. Největší velikost, kterou Azure podporuje pro disk s operačním systémem, je 2 TiB. Azure podporuje až 32 TiB pro spravované datové disky v globálním cloudu Azure, 4 TiB ve službě Azure svrchované cloudy.

**Jaká je největší velikost nespravovaného disku podporovaná pro operační systém a datové disky?**

Typ oddílu, který Azure podporuje pro disk s operačním systémem, je hlavním spouštěcím záznamem (MBR). Formát MBR podporuje velikost disku až 2 TiB. Největší velikost, kterou Azure podporuje pro nespravovaný disk s operačním systémem, je 2 TiB. Azure podporuje až 4 TiB pro nespravované disky dat.

**Jaká je největší velikost objektu blob stránky, která je podporovaná?**

Největší velikost objektu blob stránky, kterou Azure podporuje, je 8 TiB (8 191 GiB). Maximální velikost objektu blob stránky při připojení k virtuálnímu počítači jako disky s daty nebo operačním systémem je 4 TiB (4 095 GiB).

**Potřebuji pro vytváření, připojování, změny velikosti a odesílání disků větších než 1 TiB použít novou verzi nástrojů Azure?**

Nemusíte upgradovat stávající nástroje Azure pro vytváření, připojování a změnu velikosti disků větších než 1 TiB. Pokud chcete nahrát soubor VHD z místního prostředí přímo do Azure jako objekt blob stránky nebo nespravovaný disk, budete muset použít nejnovější sady nástrojů uvedené níže. Podporujeme pouze nahrávání VHD až do 8 TiB.

|Nástroje Azure      | Podporované verze                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Číslo verze 4.1.0: verze z června 2017 nebo novější|
|Azure CLI v1     | Číslo verze 0.10.13: může 2017 verze nebo novější.|
|Azure CLI v2     | Číslo verze 2.0.12: červenec 2017 nebo novější verze|
|AzCopy           | Číslo verze 6.1.0: verze z června 2017 nebo novější|

**Jsou podporovány velikosti P4 a P6 disků pro nespravované disky nebo objekty blob stránky?**

Velikosti disků P4 (32 GiB) a P6 (64 GiB) nejsou podporovány jako výchozí vrstvy disků pro nespravované disky a objekty blob stránky. Musíte explicitně [nastavit úroveň objektu BLOB](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) na P4 a P6 tak, aby byl váš disk mapovaný na tyto vrstvy. Pokud nasadíte nespravovaný disk nebo objekt blob stránky s velikostí disku nebo délkou obsahu menší než 32 GiB nebo mezi 32 GiB a 64 GiB bez nastavování úrovně objektu blob, budete pokračovat na P10 s 500 IOPS a 100 MiB/s a namapovanou cenovou úrovní.

**Pokud byl před povolením malého disku vytvořen ještě méně než 64 GiB, než je malý disk povolený (asi 15. června 2017), jak se účtuje?**

Stávající disky s malým množstvím prémií menší než 64 GiB se budou účtovat podle cenové úrovně P10.

**Jak můžu přepnout diskovou vrstvu malých prémiových disků menších než 64 GiB z P10 na P4 nebo P6?**

Můžete pořídit snímky malých disků a pak vytvořit disk pro automatické přepínání cenové úrovně na P4 nebo P6 na základě zřízené velikosti.

**Můžete změnit velikost stávajícího Managed Disks z velikosti menší než 4 tebibytes (TiB) na nové nově zavedené velikosti disků až do 32 TiB?**

Ano.

**Jaké jsou největší velikosti disků podporované službou Azure Backup a Azure Site Recovery?**

Největší velikost disku podporovaná službou Azure Backup a službou Azure Site Recovery je 4 TiB. Podpora větších disků až do 32 TiB není ještě dostupná.

**Jaké jsou doporučené velikosti virtuálních počítačů pro větší velikosti disků (> 4 TiB), SSD úrovně Standard a HDD úrovně Standard disků pro dosažení optimalizovaného diskového IOPS a šířky pásma?**

K dosažení propustnosti disku SSD úrovně Standard a HDD úrovně Standard velkých velikostí disků (> 4 TiB) překračuje 500 IOPS a 60 MiB/s, doporučujeme nasadit nový virtuální počítač z jedné z následujících velikostí virtuálních počítačů pro optimalizaci výkonu: B-Series, DSv2-Series, Dsv3-Series, ESv3-Series. , Řady FS-Series, Fsv2-Series, řady M-Series, GS-Series, NCv2-Series, NCv3-Series nebo ls-series. Připojení velkých disků ke stávajícím virtuálním počítačům nebo virtuálním počítačům, které nepoužívají výše uvedené doporučené velikosti, se může vyskytnout i u nižšího výkonu.

**Jak můžu upgradovat svoje disky (> 4 TiB), které se nasadily během verze Preview větších velikostí disků, abyste dosáhli vyššího počtu IOPS & šířku pásma na GA?**

Můžete buď zastavit a spustit virtuální počítač, ke kterému je disk připojený, nebo odpojit a znovu připojit disk. Pro zvýšení úrovně Premium SSD i Standard SSD na GA se zvýšily cíle výkonu větších velikostí disků.

**Jaké oblasti jsou velikosti spravovaných disků 8 TiB, 16 TiB a 32 TiB v systému?**

Diskové SKU 8 TiB, 16 TiB a 32 TiB jsou podporované ve všech oblastech v části globální Azure, Microsoft Azure Government a Azure Čína 21Vianet.

**Podporujeme povolování ukládání hostitelů do mezipaměti u všech velikostí disků?**

Podporujeme ukládání do mezipaměti pro jen pro čtení a čtení a zápis na discích, které jsou menší než 4 TiB. Pro velikosti disků větší než 4 TiB nepodporujeme možnost nastavení ukládání do mezipaměti kromě možnosti žádné. Doporučujeme používat ukládání do mezipaměti pro menší velikosti disků, kde můžete očekávat lepší zvýšení výkonu s daty uloženými v mezipaměti do virtuálního počítače.

## <a name="what-if-my-question-isnt-answered-here"></a>Co když tady není odpověď na moji otázku?

Pokud tady uvedený dotaz není, dejte nám prosím jistotu a my vám pomůžeme najít odpověď. Na konci tohoto článku můžete v komentářích odeslat otázku. Pokud se chcete zapojit do Azure Storage týmu a dalších členů komunity o tomto článku, použijte [Fórum MSDN Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).

Pokud chcete požádat o funkce, odešlete své žádosti a nápady na [fórum Azure Storage Feedback](https://feedback.azure.com/forums/217298-storage).
