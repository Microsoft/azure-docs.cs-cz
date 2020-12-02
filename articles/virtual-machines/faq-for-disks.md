---
title: Nejčastější dotazy k diskům
description: Nejčastější dotazy k diskům virtuálních počítačů Azure IaaS Linux a diskům úrovně Premium (spravované a nespravované)
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 06/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: dcfef81f2d7f3413489490d97c143fdec7e11bed
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499318"
---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Nejčastější dotazy týkající se disků virtuálních počítačů Azure IaaS a spravovaných a nespravovaných disků Premium

Tento článek obsahuje odpovědi na některé nejčastější dotazy týkající se Azure Managed Disks a Azure SSD úrovně Premium disků.

## <a name="managed-disks"></a>Spravované disky

**Co je Azure Managed Disks?**

Managed Disks je funkce, která zjednodušuje správu disků pro virtuální počítače Azure s IaaS tím, že zpracovává správu účtu úložiště za vás. Další informace najdete v [přehledu Managed disks](managed-disks-overview.md).

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

**Jaký druh podpory řízení přístupu na základě role Azure je k dispozici pro Managed Disks?**

Managed Disks podporuje tři výchozí role klíče:

* Vlastník: může spravovat všechno, včetně přístupu.
* Přispěvatel: může spravovat všechno kromě přístupu.
* Čtecí modul: může zobrazit vše, ale nemůže provádět změny.

**Existuje způsob, jak mohu zkopírovat nebo exportovat spravovaný disk do účtu privátního úložiště?**

Pro spravovaný disk můžete vygenerovat identifikátor URI sdíleného přístupového podpisu (SAS) jen pro čtení a použít ho ke zkopírování obsahu do soukromého účtu úložiště nebo místního úložiště. Identifikátor URI SAS můžete použít pomocí Azure Portal, Azure PowerShell, rozhraní příkazového řádku Azure nebo [AzCopy](../storage/common/storage-use-azcopy-v10.md)

**Můžu vytvořit kopii spravovaného disku?**

Zákazníci si můžou pořídit snímek svých spravovaných disků a potom použít snímek k vytvoření dalšího spravovaného disku.

**Jsou pořád podporované nespravované disky?**

Ano, podporují se nespravované i spravované disky. Pro nové úlohy doporučujeme používat spravované disky a migrovat aktuální úlohy na spravované disky.

**Můžu na stejném virtuálním počítači vyhledat nespravované a spravované disky?**

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
* [Seznam šablon pomocí Managed Disks](https://github.com/Azure/azure-quickstart-templates/)
* https://github.com/chagarw/MDPP

**Při vytváření disku z objektu BLOB se u tohoto zdrojového objektu BLOB nějaký nepřetržitě existující relace používá?**

Ne, při vytvoření nového disku je v tuto chvíli úplná samostatná kopie tohoto objektu BLOB a mezi nimi není žádné spojení. Pokud budete chtít, můžete zdrojový objekt BLOB po vytvoření odstranit, aniž by to mělo vliv na nově vytvořený disk jakýmkoli způsobem.

**Můžu po vytvoření přejmenovat spravovaný nebo nespravovaný disk?**

U spravovaných disků je nemůžete přejmenovat. Nespravovaný disk ale můžete přejmenovat, pokud není aktuálně připojený k virtuálnímu pevnému disku VHD nebo virtuálnímu počítači.

**Můžu na disku Azure použít dělení na oddíly GPT?**

Bitové kopie 1. generace můžou na datových discích používat jenom oddíly GPT, ne disky s operačním systémem. Disky s operačním systémem musí používat styl oddílu MBR.

[Image 2. generace](./generation-2.md) můžou na disku s operačním systémem i na datových discích používat dělení GPT.

**Jaké typy disků podporují snímky?**

Snímky podpory SSD úrovně Premium, Standard SSD a standardní HDD. U těchto tří typů disků se snímky podporují pro všechny velikosti disků (včetně disků až do 32 TiB velikosti). Disky Ultra nepodporují snímky.

**Co jsou rezervace disků Azure?**
Rezervace disku je možnost předem si koupit jeden rok diskového úložiště a snížit tak celkové náklady. Podrobnosti o rezervacích disků Azure najdete v našem článku na předmětu: [Vysvětlení způsobu použití zlevněné slevy na disk Azure](../cost-management-billing/reservations/understand-disk-reservations.md).

**Jaké možnosti nabízí rezervace disku Azure?**    
Rezervace disku Azure nabízí možnost koupit SSD úrovně Premium v zadaných SKU z P30 (1 TiB) až P80 (32 TiB) po dobu jednoho roku. Minimální množství disků potřebných k zakoupení rezervovaného disku není nijak omezené. Kromě toho se můžete rozhodnout, že platíte s jednou, předem platbou nebo měsíčními platbami. Pro SSD úrovně Premium Managed Disks se neúčtují žádné další transakční náklady.    

Rezervace se provádějí ve formě disků, mimo kapacitu. Jinými slovy, když rezervujete disk P80 (32 TiB), získáte jeden P80 disk, takže nemůžete tuto specifickou rezervaci rozdělit do dvou menších disků P70 (16 TiB). Můžete samozřejmě vyhradit tolik disků, kolik jich budete chtít, a to včetně dvou samostatných disků P70 (16 TiB).

**Jak se používá rezervace disku Azure?**    
Rezervace disků sleduje model podobný vyhrazeným instancím virtuálních počítačů. Rozdíl mezi tím, že se rezervace disku nedá použít u různých SKU, zatímco instance virtuálního počítače může. Další informace o instancích virtuálních počítačů najdete v tématu [úspory nákladů pomocí Azure Reserved VM Instances](./prepay-reserved-vm-instances.md) .     

**Můžu použít svoje úložiště dat zakoupené prostřednictvím rezervovaného disku Azure v několika oblastech?**    
Rezervace disků Azure se kupuje pro konkrétní oblast a SKU (jako je P30 v Východní USA 2), a proto se nedá použít mimo tyto konstrukce. U všech potřeb diskových úložišť, které jsou v jiných oblastech nebo SKU, si vždycky můžete koupit další rezervované disky Azure.    

**Co se stane, když vyprší platnost rezervace na discích Azure?**    
Před vypršením platnosti a znovu po datu vypršení platnosti obdržíte e-mailová oznámení 30 dnů. Po vypršení platnosti rezervace budou nasazené disky i nadále spuštěné a budou se fakturovat s nejnovějšími [tarify](https://azure.microsoft.com/pricing/details/managed-disks/)průběžných plateb.

**Podporuje SSD úrovně Standard disky smlouvu SLA pro virtuální počítače s jednou instancí?**

Ano, všechny typy disků podporují smlouvu SLA pro virtuální počítače s jednou instancí.

### <a name="azure-shared-disks"></a>Sdílené disky Azure

**Je funkce sdílené disky podporovaná pro nespravované disky nebo objekty blob stránky?**

Ne, podporuje se jenom pro disky Ultra a Premium SSD.

**Které oblasti podporují sdílené disky?**

Regionální informace najdete v našem [koncepčním článku](disks-shared.md).

**Můžou se sdílené disky používat jako disk s operačním systémem?**

Ne, sdílené disky se podporují jenom pro datové disky.

**Jaké velikosti disků podporují sdílené disky?**

Podporované velikosti najdete v našem [koncepčním článku](disks-shared.md).

**Když mám existující disk, můžu na něj Povolit sdílené disky?**

Sdílené disky můžou povolit všechny spravované disky vytvořené pomocí rozhraní API verze 2019-07-01 nebo vyšší. K tomu je potřeba odpojit disk od všech virtuálních počítačů, ke kterým je připojený. Pak na disku upravte vlastnost **maxShares** .

**Pokud už nechci používat disk ve sdíleném režimu, jak ho můžu zakázat?**

Odpojte disk ze všech virtuálních počítačů, ke kterým je připojen. Pak na disku upravte vlastnost maxShare na hodnotu 1.

**Můžete změnit velikost sdíleného disku?**

Ano.

**Můžu povolit akcelerátor zápisu na disku, na kterém jsou taky povolené sdílené disky?**

Ne.

**Můžu povolit ukládání hostitelů do mezipaměti pro disk, na kterém je povolený sdílený disk?**

Jediná podporovaná možnost ukládání hostitelů do mezipaměti je **none**.

## <a name="ultra-disks"></a>Disky Ultra

**Jak mám nastavit propustnost Ultra disk na?**
Pokud si nejste jistí, co nastavit propustnost disku, doporučujeme začít tím, že zadáte velikost vstupně-výstupní operace 16 KiB a upravíte výkon při monitorování aplikace. Vzorec je: propustnost v MB/s = # IOPS * 16/1000.

**Nakonfigurovali jsem disk na 40000 IOPS, ale zobrazuje se pouze 12800 IOPS, proč mi nevidím výkon disku?**
Kromě omezení disku existuje omezení vstupně-výstupní operace, která je uložena na úrovni virtuálního počítače. Ujistěte se, že velikost virtuálního počítače, kterou používáte, může podporovat úrovně, které jsou nakonfigurované na vašich discích. Podrobnosti týkající se omezení v/v, které ukládá váš virtuální počítač, najdete v tématu [velikosti pro virtuální počítače v Azure](sizes.md).

**Můžu používat úrovně ukládání do mezipaměti s Ultra diskem?**
Ne, Ultra disky nepodporují různé metody ukládání do mezipaměti, které jsou podporované na jiných typech disků. Nastavte ukládání disku do mezipaměti na **žádné**.

**Můžu k existujícímu virtuálnímu počítači připojit disk Ultra?**
Je možné, že váš virtuální počítač musí být v oblasti a páru zón dostupnosti, které podporují disky Ultra. Podrobnosti najdete v tématu [Začínáme s disky Ultra](disks-enable-ultra-ssd.md) .

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

Vytvořte spravovaný disk s vlastností [CreateOption](/rest/api/compute/disks/createorupdate#diskcreateoption) [creationData](/rest/api/compute/disks/createorupdate#creationdata) nastavenou na Odeslat a potom do něj můžete nahrát data.

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

**Jaká je výhoda použití SSD úrovně Standard disků místo HDD?**
SSD úrovně Standard disky dodávají lepší latenci, konzistenci, dostupnost a spolehlivost v porovnání s disky HDD. Úlohy aplikací běží na SSD úrovně Standard mnohem plynule. Upozorňujeme, že SSD úrovně Premium disky jsou doporučeným řešením pro většinu produkčních úloh náročných na vstupně-výstupní operace.

**Můžu používat standardní SSD jako nespravované disky?**
Ne, standardní disky SSD jsou k dispozici pouze jako Managed Disks.

## <a name="migrate-to-managed-disks"></a>Migrace na spravované disky

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

Yes

## <a name="managed-disks-and-storage-service-encryption"></a>Managed Disks a Šifrování služby Storage

**Je šifrování na straně serveru ve výchozím nastavení povolené při vytváření spravovaného disku?**

Ano. Managed Disks se šifrují pomocí šifrování na straně serveru s použitím klíčů spravovaných platformou. 

**Je spouštěcí svazek ve výchozím nastavení zašifrovaný na spravovaném disku?**

Ano. Ve výchozím nastavení jsou všechny spravované disky zašifrované, včetně disku s operačním systémem.

**Kdo spravuje šifrovací klíče?**

Spravované klíče platformy jsou spravované Microsoftem. Můžete také použít a spravovat vlastní klíče uložené v Azure Key Vault. 

**Můžu zakázat šifrování na straně serveru pro moje spravované disky?**

Ne.

**Je šifrování na straně serveru dostupné jenom v konkrétních oblastech?**

Ne. Šifrování na straně serveru s klíči spravovanými platformou a zákazníkem jsou k dispozici ve všech oblastech, kde je Managed Disks k dispozici. 

**Podporuje Azure Site Recovery šifrování na straně serveru s klíčem spravovaným zákazníkem pro místní prostředí do Azure a pro scénáře zotavení po havárii z Azure do Azure?**

Ano. 

**Je možné zálohovat Managed Disks šifrované pomocí šifrování na straně serveru s klíčem spravovaným zákazníkem pomocí služby Azure Backup?**

Ano.

**Jsou spravované snímky a obrázky zašifrované?**

Ano. Všechny spravované snímky a obrázky se zašifrují automaticky. 

**Můžu převést virtuální počítače s nespravovanými disky, které se nacházejí v účtech úložiště, které jsou nebo byly předtím zašifrované na spravované disky?**

Yes

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

**Které oblasti podporují možnosti shlukování pro platnou velikost disku SSD úrovně Premium?**

Funkce shlukování se v současné době podporují ve všech oblastech veřejného cloudu Azure. Podpora pro cloudy v svrchovaném období už brzy probíhá. 

**Jaké oblasti jsou 4/8/16 GiB spravované velikosti disků (P1/P2/P3, E1/E2/E3), které jsou podporovány v?**

Tyto nové velikosti disků jsou aktuálně podporované ve všech oblastech ve veřejném cloudu Azure. Podpora pro cloudy v svrchovaném období už brzy probíhá. 

**Jsou velikosti disků P1/P2/P3 podporované pro nespravované disky nebo objekty blob stránky?**

Ne, podporuje se jenom na discích se systémem Premium SSD. 

**Podporují se velikosti disků E1/E2/E3 pro nespravované disky nebo objekty blob stránky?**

Ne, standardní disky SSD na libovolné velikosti se nedají použít u nespravovaných disků nebo objektů blob stránky.

**Jaká je největší velikost spravovaného disku podporovaná pro operační systém a datové disky na virtuálních počítačích s Gen1?**

Typ oddílu, který Azure podporuje pro disky s operačním systémem Gen1, je hlavním spouštěcím záznamem (MBR). I když disky s operačním systémem Gen1 podporují jenom MBR, datové disky podporují GPT. I když můžete přidělit až 4 disky s operačním systémem TiB, může typ oddílu MBR použít pro operační systém až 2 TiB tohoto místa na disku. Azure podporuje až 32 TiB pro spravované datové disky.

**Jaká je největší velikost spravovaného disku podporovaná pro operační systém a datové disky na virtuálních počítačích s Gen2?**

Typ oddílu, který Azure podporuje pro disky s operačním systémem Gen2, je tabulka oddílů GUID (GPT). Virtuální počítače s Gen2 podporují až 4 disky s operačním systémem TiB. Azure podporuje až 32 TiB pro spravované datové disky.


**Jaká je největší velikost nespravovaného disku podporovaná pro operační systém a datové disky?**

Typ oddílu, který Azure podporuje pro disk s operačním systémem pomocí nespravovaných disků, je hlavní spouštěcí záznam (MBR).  I když můžete přidělit až 4 disky s operačním systémem TiB, může typ oddílu MBR použít pro operační systém až 2 TiB tohoto místa na disku. Azure podporuje až 4 TiB pro nespravované datové disky.


**Jaká je největší velikost objektu blob stránky, která je podporovaná?**

Největší velikost objektu blob stránky, kterou Azure podporuje, je 8 TiB (8 191 GiB). Maximální velikost objektu blob stránky při připojení k virtuálnímu počítači jako disky s daty nebo operačním systémem je 4 TiB (4 095 GiB).

**Potřebuji pro vytváření, připojování, změny velikosti a odesílání disků větších než 1 TiB použít novou verzi nástrojů Azure?**

Nemusíte upgradovat stávající nástroje Azure pro vytváření, připojování a změnu velikosti disků větších než 1 TiB. Pokud chcete nahrát soubor VHD z místního prostředí přímo do Azure jako objekt blob stránky nebo nespravovaný disk, budete muset použít nejnovější sady nástrojů uvedené níže. Podporujeme pouze nahrávání VHD až do 8 TiB.

|Nástroje Azure      | Podporované verze                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Číslo verze 4.1.0: verze z června 2017 nebo novější|
|Azure CLI v1     | Číslo verze 0.10.13: může 2017 verze nebo novější.|
|Azure CLI v2     | Číslo verze 2.0.12: červenec 2017 nebo novější verze|
|AzCopy              | Číslo verze 6.1.0: verze z června 2017 nebo novější|

**Jsou podporovány velikosti P4 a P6 disků pro nespravované disky nebo objekty blob stránky?**

Velikosti disků P4 (32 GiB) a P6 (64 GiB) nejsou podporovány jako výchozí vrstvy disků pro nespravované disky a objekty blob stránky. Musíte explicitně [nastavit úroveň objektu BLOB](/rest/api/storageservices/set-blob-tier) na P4 a P6 tak, aby byl váš disk mapovaný na tyto vrstvy. Pokud nasadíte nespravovaný disk nebo objekt blob stránky s velikostí disku nebo délkou obsahu menší než 32 GiB nebo mezi 32 GiB a 64 GiB bez nastavování úrovně objektu blob, budete pokračovat na P10 s 500 IOPS a 100 MiB/s a namapovanou cenovou úrovní.

**Pokud byl před povolením malého disku vytvořen ještě méně než 64 GiB, než je malý disk povolený (asi 15. června 2017), jak se účtuje?**

Stávající disky s malým množstvím prémií menší než 64 GiB se budou účtovat podle cenové úrovně P10.

**Jak můžu přepnout diskovou vrstvu malých prémiových disků menších než 64 GiB z P10 na P4 nebo P6?**

Můžete pořídit snímky malých disků a pak vytvořit disk pro automatické přepínání cenové úrovně na P4 nebo P6 na základě zřízené velikosti.

**Můžete změnit velikost stávajícího Managed Disks z velikosti menší než 4 tebibytes (TiB) na nové nově zavedené velikosti disků až do 32 TiB?**

Ano.

**Jaké jsou největší velikosti disků podporované službou Azure Backup a Azure Site Recovery?**

Největší velikost disku podporovaná Azure Backup je 32 TiB (4 TiB pro šifrované disky). Největší velikost disku podporovaná Azure Site Recovery je 8 TiB. Podpora větších disků až do 32 TiB není v Azure Site Recovery ještě dostupná.

**Jaké jsou doporučené velikosti virtuálních počítačů pro větší velikosti disků (>4 TiB), SSD úrovně Standard a HDD úrovně Standard disků pro dosažení optimalizovaného diskového IOPS a šířky pásma?**

Chcete-li dosáhnout propustnosti disku SSD úrovně Standard a HDD úrovně Standard velkých velikostí disků (>4 TiB) až 500 vstupně-výstupních operací a 60 MiB/s, doporučujeme nasadit nový virtuální počítač z jedné z následujících velikostí virtuálních počítačů pro optimalizaci výkonu: B-Series, DSv2-Series, Dsv3-Series, ESv3-Series, FS-Series, Fsv2-Series, řady M-Series, GS-Series, NCv2-Series, NCv3-Series nebo virtuálních počítačů ls-series. Připojení velkých disků ke stávajícím virtuálním počítačům nebo virtuálním počítačům, které nepoužívají výše uvedené doporučené velikosti, se může vyskytnout i u nižšího výkonu.

**Jak můžu upgradovat svoje disky (>4 TiB), které se nasadily během verze Preview větších velikostí disků, abyste dosáhli vyššího počtu IOPS & šířku pásma na GA?**

Můžete buď zastavit a spustit virtuální počítač, ke kterému je disk připojený, nebo odpojit a znovu připojit disk. Pro zvýšení úrovně Premium SSD i Standard SSD na GA se zvýšily cíle výkonu větších velikostí disků.

**Jaké oblasti jsou velikosti spravovaných disků 8 TiB, 16 TiB a 32 TiB v systému?**

Diskové SKU 8 TiB, 16 TiB a 32 TiB jsou podporované ve všech oblastech v části globální Azure, Microsoft Azure Government a Azure Čína 21Vianet.

**Podporujeme povolování ukládání hostitelů do mezipaměti u všech velikostí disků?**

Mezipaměť hostitele (**jen** pro **čtení a zápis**) je podporovaná na discích, které jsou menší než 4 TIB. To znamená, že každý disk, který je zřízený až 4095 GiB, může využít výhod ukládání do mezipaměti hostitele. Mezipaměť hostitele není podporována pro velikosti disků větší nebo rovnou 4096 GiB. Například P50 Premium disk zřízený v 4095 GiB může využít výhod ukládání do mezipaměti hostitele a P50 disk zřízený v 4096 GiB nemůže využít ukládání do mezipaměti hostitele. Doporučujeme používat ukládání do mezipaměti pro menší velikosti disků, kde můžete očekávat lepší zvýšení výkonu s daty uloženými v mezipaměti do virtuálního počítače.

## <a name="private-links-for-securely-exporting-and-importing-managed-disks"></a>Soukromé odkazy pro bezpečné export a import Managed Disks

**Jaká je výhoda použití privátních odkazů pro export a import Managed Disks?**

Pomocí privátních odkazů můžete omezit export a import Managed Disks jenom z vaší virtuální sítě Azure. 

**Jak se dá zajistit, aby se disk mohl exportovat nebo importovat jenom pomocí privátních odkazů?**

Vlastnost je nutné nastavit `DiskAccessId` na instanci objektu pro přístup k disku a také nastavit vlastnost NetworkAccessPolicy na hodnotu `AllowPrivate` .

**Můžu propojit více virtuálních sítí se stejným objektem pro přístup k disku?**

Ne. V současné době můžete objekt pro přístup k disku propojit jenom s jednou virtuální sítí.

**Můžu propojit virtuální síť s objektem pro přístup k disku v jiném předplatném?**

Ne. V současné době můžete propojit objekt pro přístup k disku k virtuální síti ve stejném předplatném.

**Můžu propojit virtuální síť s objektem pro přístup k disku v jiném předplatném?**

Ne. V současné době můžete propojit objekt pro přístup k disku k virtuální síti ve stejném předplatném.

**Kolik exportů a importů používajících stejný objekt pro přístup k disku může probíhat ve stejnou dobu?**

5

**Můžu pomocí identifikátoru URI SAS disku nebo snímku stáhnout základní virtuální pevný disk virtuálního počítače ve stejné podsíti jako podsíť privátního koncového bodu přidruženého k disku?**

Ano.

**Můžu pomocí identifikátoru URI SAS disku nebo snímku stáhnout základní virtuální pevný disk virtuálního počítače, který není ve stejné podsíti jako podsíť privátního koncového bodu, který není přidružený k disku?**

Ne.

## <a name="what-if-my-question-isnt-answered-here"></a>Co když tady není odpověď na moji otázku?

Pokud tady uvedený dotaz není, dejte nám prosím jistotu a my vám pomůžeme najít odpověď. Na konci tohoto článku můžete v komentářích odeslat otázku. Pokud se chcete zapojit do Azure Storage týmu a dalších členů komunity o tomto článku, použijte [Azure Storage Microsoft Q&na stránce s otázkou](/answers/products/azure?product=storage).

Pokud chcete požádat o funkce, odešlete své žádosti a nápady na [fórum Azure Storage Feedback](https://feedback.azure.com/forums/217298-storage).