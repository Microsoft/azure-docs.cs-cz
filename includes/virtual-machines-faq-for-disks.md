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
ms.openlocfilehash: 53bbee6dd75e045c2a7e95c88a0138c9859d12db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80374052"
---
Tento článek odpovídá na některé nejčastější dotazy týkající se spravovaných disků Azure a disků Azure Premium SSD.

## <a name="managed-disks"></a>Spravované disky

**Co jsou spravované disky Azure?**

Spravované disky je funkce, která zjednodušuje správu disků pro virtuální počítače Azure IaaS tím, že zpracovává správu účtů úložiště za vás. Další informace naleznete v [přehledu spravovaných disků](../articles/virtual-machines/windows/managed-disks-overview.md).

**Pokud vytvořím standardní spravovaný disk z existujícího virtuálního pevného disku, který má 80 GB, kolik mě to bude stát?**

Standardní spravovaný disk vytvořený z 80GB virtuálního pevného disku je považován za další dostupnou standardní velikost disku, což je disk S10. Poplatky vám budou účtovány podle cen disků S10. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage).

**Existují nějaké transakční náklady na standardní spravované disky?**

Ano. Za každou transakci se vám budou účtovat poplatky. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage).

**U standardního spravovaného disku se mi bude účtovat skutečná velikost dat na disku nebo zřízená kapacita disku?**

Poplatky se vám účtují na základě zřízené kapacity disku. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage).

**Jak se ceny prémiových spravovaných disků liší od nespravovaných disků?**

Ceny prémiových spravovaných disků jsou stejné jako ceny nespravovaných disků premium.

**Můžu změnit typ účtu úložiště (standardní nebo prémiový) spravovaných disků?**

Ano. Typ účtu úložiště spravovaných disků můžete změnit pomocí portálu Azure, PowerShellu nebo nastavení příkazového příkazu k nastavení účtu Azure.

**Můžu použít soubor Virtuálního pevného disku v účtu úložiště Azure k vytvoření spravovaného disku s jiným předplatným?**

Ano.

**Můžu použít soubor VHD v účtu úložiště Azure k vytvoření spravovaného disku v jiné oblasti?**

Ne.

**Existují nějaká omezení měřítka pro zákazníky, kteří používají spravované disky?**

Spravované disky eliminují limity spojené s účty úložiště. Maximální limit je však 50 000 spravovaných disků na oblast a na typ disku pro předplatné.

**Můžou virtuální počítače v sadě dostupnosti obsahovat kombinaci spravovaných a nespravovaných disků?**

Ne. Virtuální počítače v sadě dostupnosti musí používat všechny spravované disky nebo všechny nespravované disky. Při vytváření sady dostupnosti můžete zvolit typ disků, které chcete použít.

**Jsou spravované disky na webu Azure Portal výchozí možností?**

Ano.

**Mohu vytvořit prázdný spravovaný disk?**

Ano. Můžete vytvořit prázdný disk. Spravovaný disk lze vytvořit nezávisle na virtuálním počítače, například bez připojení k virtuálnímu počítače.

**Jaký je počet podporovaných domén selhání pro sadu dostupnosti, která používá spravované disky?**

V závislosti na oblasti, kde je umístěna sada dostupnosti, která používá spravované disky, je počet podporovaných domén selhání 2 nebo 3.

**Jak je nastaven standardní účet úložiště pro diagnostiku?**

Pro diagnostiku virtuálních zařízení nastavíte účet privátního úložiště.

**Jaký druh podpory řízení přístupu na základě rolí je k dispozici pro spravované disky?**

Spravované disky podporují tři klíčové výchozí role:

* Vlastník: Může spravovat vše, včetně přístupu
* Přispěvatel: Může spravovat vše kromě přístupu
* Čtenář: Může zobrazit všechno, ale nemůže provádět změny

**Existuje způsob, jak zkopírovat nebo exportovat spravovaný disk do účtu soukromého úložiště?**

Můžete vygenerovat identifikátor URI pro sdílený přístupový podpis jen pro čtení (SAS) pro spravovaný disk a použít ho ke kopírování obsahu do účtu soukromého úložiště nebo místního úložiště. Identifikátor URI služby SAS můžete použít pomocí portálu Azure Portal, Azure PowerShellu, azure CLI nebo [AzCopy.](../articles/storage/common/storage-use-azcopy.md)

**Mohu vytvořit kopii spravovaného disku?**

Zákazníci mohou pořídit snímek svých spravovaných disků a potom pomocí snímku vytvořit jiný spravovaný disk.

**Jsou nespravované disky stále podporovány?**

Ano, jsou podporovány nespravované i spravované disky. Doporučujeme používat spravované disky pro nové úlohy a migrovat aktuální úlohy na spravované disky.

**Můžu na stejném virtuálním počítači spolu najít nespravované a spravované disky?**

Ne.

**Pokud vytvořím disk 128 GB a pak zvětším velikost na 130 gibibyte (GiB), bude mi účtována další velikost disku (256 GiB)?**

Ano.

**Můžu vytvořit místně redundantní disky úložiště, geograficky redundantní úložiště a zónově redundantní disky?**

Spravované disky Azure aktuálně podporují jenom místně redundantní disky spravované úložištěm.

**Mohu spravované disky zmenšit nebo zmenšit?**

Ne. Tato funkce není aktuálně podporována.

**Mohu přerušit pronájem na disku?**

Ne. To není podporováno v současné době jako zapůjčení je k dispozici, aby se zabránilo náhodnému odstranění při použití disku.

**Lze změnit vlastnost názvu počítače, když se k zřízení virtuálního počítače používá specializovaný (nevytvořený pomocí nástroje Příprava systému nebo generalizovaného) disku operačního systému?**

Ne. Vlastnost název počítače nelze aktualizovat. Nový virtuální modul dědí z nadřazeného virtuálního počítače, který byl použit k vytvoření disku operačního systému. 

**Kde najdu ukázkové šablony Azure Resource Manageru pro vytváření virtuálních počítačů se spravovanými disky?**
* [Seznam šablon pomocí spravovaných disků](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

**Při vytváření disku z objektu blob existuje nějaký průběžně existující vztah s tímto zdrojovým objektem blob?**

Ne, při vytvoření nového disku je úplná samostatná kopie tohoto objektu blob v té době a neexistuje žádné spojení mezi těmito dvěma. Pokud chcete, po vytvoření disku může být zdrojový objekt blob odstraněn, aniž by to jakkoli ovlivnilo nově vytvořený disk.

**Mohu po vytvoření přejmenovat spravovaný nebo nespravovaný disk?**

U spravovaných disků je nelze přejmenovat. Můžete však přejmenovat nespravovaný disk tak dlouho, dokud není aktuálně připojen k virtuálnímu pevnému disku nebo virtuálnímu počítače.

**Můžu použít oddíly GPT na disku Azure?**

Bitové kopie generace 1 lze použít pouze gpt dělení na datových discích, nikoli na discích operačního systému. Disky operačního systému musí používat styl oddílu MBR.

[Generace 2 bitové kopie](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2) můžete použít GPT dělení na disku operačního systému, stejně jako datové disky.

**Jaké typy disků podporují snímky?**

Premium SSD, standardní SSD a standardní HDD podporují snímky. Pro tyto tři typy disků jsou podporovány snímky pro všechny velikosti disků (včetně disků o velikosti až 32 TiB). Ultra disky nepodporují snímky.

**Co jsou rezervace disků Azure?**
Rezervace disku je možnost zakoupit jeden rok diskového úložiště předem, což snižuje celkové náklady. Podrobnosti o rezervacích disků Azure najdete v článku na toto téma: [Zjistěte, jak se vaše sleva na rezervaci vztahuje na Azure Disk](../articles/cost-management-billing/reservations/understand-disk-reservations.md).

**Jaké možnosti nabízí rezervace disku Azure?**    
Rezervace disku Azure poskytuje možnost zakoupit premium SSD v zadané ssd od P30 (1 TiB) až P80 (32 TiB) po dobu jednoho roku. Neexistuje žádné omezení na minimální množství disků potřebných k zakoupení rezervace disku. Kromě toho můžete platit jednorázovou platbou předem nebo měsíční platbou. Pro disky spravované disky SSD s platební stanicí Premium nejsou použity žádné další transakční náklady.    

Rezervace jsou prováděny ve formě disků, nikoli kapacity. Jinými slovy, když si rezervujete disk P80 (32 TiB), získáte jeden disk P80, nelze rozdělit tuto konkrétní rezervaci na dva menší disky P70 (16 TiB). Můžete si samozřejmě vyhradit tolik nebo tolik disků, kolik chcete, včetně dvou samostatných disků P70 (16 TiB).

**Jak se používá rezervace disku Azure?**    
Rezervace disků se řídí modelem podobným vyhrazeným instancí virtuálního počítače (VM). Rozdíl je, že rezervace disku nelze použít na různé sku, zatímco instance virtuálního počítače může. Další informace o instancích virtuálních počítačů najdete v [tématu Ukládání nákladů s instancemi vyhrazených virtuálních počítačích Azure.](../articles/virtual-machines/linux/prepay-reserved-vm-instances.md)     

**Můžu použít svoje úložiště dat zakoupené prostřednictvím rezervace disků Azure ve více oblastech?**    
Rezervace disků Azure jsou zakoupeny pro konkrétní oblast a skladovou položku (například P30 ve východním USA 2), a proto nelze použít mimo tyto konstrukce. Vždy můžete zakoupit další rezervaci disků Azure pro potřeby úložiště disku v jiných oblastech nebo skladových objektech.    

**Co se stane, když vyprší platnost rezervace disků Azure?**    
Budete dostávat e-mailová oznámení 30 dní před vypršením platnosti a znovu v den vypršení platnosti. Po vypršení platnosti rezervace budou nasazené disky nadále spuštěny a budou se účtovat s nejnovějšími [sazbami průběžných plateb](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="azure-shared-disks"></a>Sdílené disky Azure

**Je funkce sdílených disků podporována pro nespravované disky nebo objekty BLOB stránky?**

Ne, je podporována pouze pro disky spravované s ssd s prémií.

**Které oblasti podporují sdílené disky?**

V současné době pouze západní střední USA.

**Lze sdílené disky používat jako disk operačního systému?**

Ne, sdílené disky jsou podporovány pouze pro datové disky.

**Jaké velikosti disků podporují sdílené disky?**

Pouze prémiové ssd disky, které podporují sdílené disky P15 nebo vyšší.

**Pokud mám existující prémiový SSD, můžu na něm povolit sdílené disky?**

Sdílené disky mohou povolit všechny spravované disky vytvořené pomocí rozhraní API verze 2019-07-01 nebo vyšší. Chcete-li to provést, je třeba odpojit disk ze všech virtuálních počítačů, které je připojen. Dále upravte `maxShares` vlastnost na disku.

**Pokud již nechci používat disk ve sdíleném režimu, jak jej zakázat?**

Odpojte disk ze všech virtuálních počítačů, ke kterým je připojen. Potom upravte vlastnost maxShare na disku na 1.

**Můžete změnit velikost sdíleného disku?**

Ano.

**Mohu povolit akcelerátor zápisu na disku, na který jsou povoleny také sdílené disky?**

Ne.

**Lze povolit ukládání hostitelů do mezipaměti pro disk, na který je povolen sdílený disk?**

Jedinou podporovanou možností ukládání hostitelů do mezipaměti je Možnost Žádný.

## <a name="ultra-disks"></a>Disky Ultra

**Na co mám nastavit propustnost ultra disku?**
Pokud si nejste jisti, na co nastavit propustnost disku, doporučujeme začít tím, že předpokládá velikost IO 16 KiB a upravit výkon odtud při sledování aplikace. Vzorec je: Propustnost v MB/s = # VOPS * 16 / 1000.

**Nakonfiguroval jsem disk na 40000 IOPS, ale já jsem jen vidět 12800 IOPS, proč nejsem vidět výkon disku?**
Kromě omezení disku je omezení vstupně-souhlasím, který získá uložené na úrovni virtuálního počítače. Ujistěte se, že velikost virtuálního počítače, kterou používáte, podporuje úrovně, které jsou nakonfigurované na vašich discích. Podrobnosti o limitech vi objektů, které vaše virtuální počítač i), najdete v [tématu Velikosti pro virtuální počítače s Windows v Azure](../articles/virtual-machines/windows/sizes.md).

**Mohu použít úrovně ukládání do mezipaměti s ultra diskem?**
Ne, ultra disky nepodporují různé metody ukládání do mezipaměti, které jsou podporovány v jiných typech disků. Nastavte ukládání disku do mezipaměti na žádné.

**Můžu k stávajícímu virtuálnímu počítači připojit ultra disk?**
Možná, že váš virtuální počítač musí být v oblasti a dostupnost i zóny dvojice, která podporuje Ultra disky. Podrobnosti [najdete v tématu Začínáme s ultra disky.](../articles/virtual-machines/windows/disks-enable-ultra-ssd.md)

**Můžu použít ultra disk jako disk operačního systému pro svůj virtuální počítač?**
Ne, ultra disky jsou podporovány pouze jako datové disky a jsou podporovány pouze jako nativní disky 4K.

**Mohu převést existující disk na ultra disk?**
Ne, ale data můžete migrovat z existujícího disku na ultra disk. Chcete-li migrovat existující disk na ultra disk, připojte oba disky ke stejnému virtuálnímu počítači a zkopírujte data disku z jednoho disku na druhý nebo využijte řešení třetí strany pro migraci dat.

**Mohu vytvářet snímky pro ultra disky?**
Ne, snímky ještě nejsou k dispozici.

**Je azure backup k dispozici pro ultra disky?**
Ne, podpora Azure Backup ještě není dostupná.

**Můžu k virtuálnímu počítači spuštěného v sadě dostupnosti připojit ultra disk?**
Ne, zatím to není podporováno.

**Můžu povolit Azure Site Recovery pro virtuální počítače pomocí ultra disků?**
Ne, Azure Site Recovery ještě není podporována pro ultra disky.

## <a name="uploading-to-a-managed-disk"></a>Nahrávání na spravovaný disk

**Můžu nahrát data na existující spravovaný disk?**

Ne, nahrávání lze použít pouze při vytváření nového prázdného disku se stavem **ReadyToUpload.**

**Jak se nahrají na spravovaný disk?**

Vytvořte spravovaný disk s vlastností [createOption](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#diskcreateoption) [creationData nastavenou](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#creationdata) na "Upload", pak do něj můžete nahrát data.

**Můžu k virtuálnímu virtuálnímu počítače připojit disk, když je ve stavu nahrávání?**

Ne.

**Mohu pořizovat snímek zmanivek disku ve stavu nahrávání?**

Ne.

## <a name="standard-ssd-disks"></a>Standardní Disky SSD

**Co jsou disky Azure Standard SSD?**
Standardní disky SSD jsou standardní disky podporované ssd médii, optimalizované jako nákladově efektivní úložiště pro úlohy, které vyžadují konzistentní výkon na nižších úrovních VOPS.

<a id="standard-ssds-azure-regions"></a>**Jaké oblasti jsou aktuálně podporovány pro disky Se standardním SSD diskem?**
Všechny oblasti Azure teď podporují disky Se standardem SSD.

**Je azure backup k dispozici při použití standardních ssd distek?**
Ano, Azure Backup je teď k dispozici.

**Jak vytvořím standardní Disky SSD?**
Standardní disky SSD můžete vytvářet pomocí šablon Azure Resource Manager, Sady SDK, PowerShellu nebo CLI. Níže jsou uvedeny parametry potřebné v šabloně Správce prostředků k vytvoření standardních disků SSD:

* *apiVersion* pro Microsoft.Compute musí `2018-04-01` být nastavena jako (nebo novější)
* Zadejte *managedDisk.storageAccountType* jako`StandardSSD_LRS`

Následující příklad ukazuje oddíl *properties.storageProfile.osDisk* pro virtuální počítače, který používá standardní disky SSD:

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

Úplný příklad šablony, jak vytvořit standardní disk SSD se šablonou, najdete v [tématu Vytvoření virtuálního počítače z bitové kopie systému Windows se standardními datovými disky SSD](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

**Mohu převést své stávající disky na standardní SSD?**
Ano, můžete. Informace o [převodu úložiště spravovaných disků Azure ze standardního na prémiový a naopak,](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) kde najdete obecné pokyny pro převod spravovaných disků. A použijte následující hodnotu k aktualizaci typu disku na standardní SSD.
-AccountType StandardSSD_LRS

**Jaká je výhoda použití standardních Disků SSD namísto pevného disku?**
Standardní disky SSD poskytují lepší latenci, konzistenci, dostupnost a spolehlivost ve srovnání s disky HDD. Úlohy aplikací běží mnohem plynuleji na standardnísssd kvůli tomu. Upozorňujeme, že disky Premium SSD jsou doporučeným řešením pro většinu produkčních úloh náročných na vi.

**Mohu použít standardní disky SSD jako nespravované disky?**
Ne, disky Se standardními disky SSD jsou k dispozici pouze jako spravované disky.

**Podporují standardní disky SSD "SLA virtuálního počítače s jednou instancí"?**
Ne, standardní ssd disy nemají sla s jediným instancem v sla. Použijte disky Premium SSD pro sla virtuálního počítače s jednou instancí.

## <a name="migrate-to-managed-disks"></a>Migrace na spravované disky

**Má migrace nějaký vliv na výkon spravovaných disků?**

Migrace zahrnuje přesun disku z jednoho umístění úložiště do jiného. To je řízeno pomocí kopie dat na pozadí, což může trvat několik hodin, obvykle méně než 24 hodin v závislosti na množství dat na discích. Během této doby může aplikace zaznamenat vyšší než obvyklou latenci čtení, protože některé čtení může být přesměrováno do původního umístění a může trvat déle. Neexistuje žádný vliv na latenci zápisu během tohoto období.  

**Jaké změny jsou vyžadovány v již existující konfiguraci služby Azure Backup před nebo po migraci na spravované disky?**

Nejsou nutné žádné změny.

**Budou moje zálohy virtuálních počítačových služeb vytvořené prostřednictvím služby Azure Backup, než migrace bude fungovat?**

Ano, zálohy fungují bez problémů.

**Jaké změny jsou vyžadovány v existující konfiguraci šifrování disků Azure před nebo po migraci na spravované disky?**

Nejsou nutné žádné změny.

**Je podporována automatická migrace existující škálovací sady virtuálních počítačů z nespravovaných disků na spravované disky?**

Ne. Pomocí bitové kopie ze staré škálovací sady s nespravovanými disky můžete vytvořit novou škálovací sadu se spravovanými disky.

**Je možné vytvořit spravovaný disk ze snímku objektu blob stránky pořízeném před migrací na spravované disky?**

Ne. Snímek objektu blob stránky můžete exportovat jako objekt blob stránky a potom vytvořit spravovaný disk z objektu blob exportované stránky.

**Můžu si povolit služby při selhání místní počítače chráněné azure site recovery na virtuální počítač se spravovanými disky?**

Ano, můžete zvolit převzetí služeb při selhání na virtuální ms se spravovanými disky.

**Existuje nějaký dopad migrace na virtuální počítače Azure chráněné Azure Site Recovery prostřednictvím replikace Azure do Azure?**

Ne. Azure Site Recovery Azure to Azure protection for VM with Managed Disks je dostupná.

**Můžu migrovat virtuální počítače s nespravovanými disky, které jsou umístěné na účtech úložiště, které jsou nebo byly dříve zašifrovány na spravované disky?**

Ano

## <a name="managed-disks-and-storage-service-encryption"></a>Šifrování spravovaných disků a služby úložiště

**Je šifrování služby Azure Storage Service ve výchozím nastavení povoleno při vytváření spravovaného disku?**

Ano.

**Je spouštěcí svazek ve výchozím nastavení šifrován na spravovaném disku?**

Ano. Ve výchozím nastavení jsou všechny spravované disky šifrovány, včetně disku operačního systému.

**Kdo spravuje šifrovací klíče?**

Společnost Microsoft spravuje šifrovací klíče.

**Můžu zakázat šifrování služby Storage Service pro spravované disky?**

Ne.

**Je šifrování služby úložiště k dispozici pouze v určitých oblastech?**

Ne. Je k dispozici ve všech oblastech, kde jsou k dispozici spravované disky. Spravované disky jsou k dispozici ve všech veřejných oblastech a v Německu. Je také k dispozici v Číně, ale pouze pro spravované klíče společnosti Microsoft, nikoli pro klíče spravované zákazníkem.

**Jak zjistím, zda je můj spravovaný disk zašifrován?**

Můžete zjistit, kdy byl spravovaný disk vytvořen z webu Azure Portal, Azure CLI a PowerShellu. Pokud je čas po červnu 9, 2017, pak je váš disk zašifrován.

**Jak lze zašifrovat své stávající disky, které byly vytvořeny před 10.**

června 2017 se automaticky zašifrují nová data zapsaná na existující spravované disky. Plánujeme také šifrovat existující data a šifrování proběhne asynchronně na pozadí. Pokud je nyní nutné zašifrovat existující data, vytvořte kopii disku. Nové disky budou zašifrovány.

* [Kopírování spravovaných disků pomocí příkazového příkazu k příkazu Azure](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Kopírování spravovaných disků pomocí Prostředí PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**Jsou spravované snímky a obrázky šifrovány?**

Ano. Všechny spravované snímky a obrázky vytvořené po 9. 

**Je možné převést virtuální počítače s nespravovanými disky, které jsou umístěny na účtech úložiště, které jsou nebo byly dříve zašifrovány na spravované disky?**

Ano

**Bude exportovaný virtuální pevný disk ze spravovaného disku nebo snímek také zašifrován?**

Ne. Pokud však exportujete virtuální pevný disk do účtu šifrovaného úložiště ze šifrovaného spravovaného disku nebo snímku, je zašifrovaný. 

## <a name="premium-disks-managed-and-unmanaged"></a>Disky Premium: Spravované a nespravované

**Pokud virtuální počítače používá velikost řady, která podporuje disky Premium SSD, jako je například DSv2, můžete připojit premium i standardní datové disky?** 

Ano.

**Můžu připojit prémiové i standardní datové disky k řadě velikostí, které nepodporují disky Premium SSD, například řady D, Dv2, G nebo F?**

Ne. K virtuálním počítačům, které nepoužívají řadu velikostí, která podporuje disky Premium SSD, můžete připojit pouze standardní datové disky.

**Pokud vytvořím prémiový datový disk z existujícího virtuálního pevného disku, který byl 80 GB, kolik to bude stát?**

Prémiový datový disk vytvořený z 80GB virtuálního pevného disku je považován za další dostupný disk premium, což je disk P10. Poplatky vám budou účtovány podle cen disků P10.

**Existují transakční náklady na použití disků Premium SSD?**

Je pevné náklady pro každou velikost disku, který je dodáván zřízené se specifickými omezeními vipoje a propustnost. Ostatní náklady jsou odchozí šířku pásma a kapacita snímku, pokud je to možné. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/storage).

**Jaké jsou limity pro viops a propustnost, které mohu získat z mezipaměti disku?**

Kombinované limity pro cache a místní SSD pro řadu DS jsou 4 000 VOPS na jádro a 33 MiB za sekundu na jádro. Řada GS nabízí 5 000 IOPS na jádro a 50 MiB za sekundu na jádro.

**Je místní SSD podporován pro virtuální virtuální počítače se spravovaným diskem?**

Místní SSD je dočasné úložiště, které je součástí virtuálního počítače spravované disky. Za dočasné úložiště se neplatí žádné další náklady. Doporučujeme nepoužívat tento místní SSD k ukládání dat aplikace, protože není trvalé v úložišti objektů blob Azure.

**Existují nějaké důsledky pro použití TRIM na prémiové disky?**

Použití trimu na discích Azure na prémiových nebo standardních discích nemá žádnou nevýhodu.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Nové velikosti disků: Spravované a nespravované

**Které oblasti podporují možnost roztržení pro příslušnou velikost disku SSD s prémií?**

Funkce roztržení je aktuálně podporována v Azure – –střed USA.

**V jakých oblastech jsou podporovány 4/8/16 GiB Managed Disk velikosti (P1/P2/P3, E1/E2/E3) v roce?**

Tyto nové velikosti disků jsou aktuálně podporované v Azure – –střed USA.

**Jsou velikosti disků P1/P2/P3 podporované pro nespravované disky nebo objekty BLOB stránky?**

Ne, je podporována pouze na disky spravované s ssd s prémií. 

**Jsou velikosti disků E1/E2/E3 podporované pro nespravované disky nebo objekty BLOB stránky?**

Ne, standardní disky spravované s ssd ssd libovolné velikosti nelze použít s nespravovanými disky nebo objekty BLOB stránky.

**Jaká je největší podporovaná velikost spravovaného disku pro operační systém a datové disky?**

Typ oddílu, který Azure podporuje pro disk operačního systému, je hlavní spouštěcí záznam (MBR). Formát MBR podporuje velikost disku až 2 TiB. Největší velikost, kterou Azure podporuje pro disk operačního systému, je 2 TiB. Azure podporuje až 32 TiB pro spravované datové disky.

**Jaká je největší velikost nespravovaného disku podporovaná pro operační systém a datové disky?**

Typ oddílu, který Azure podporuje pro disk operačního systému, je hlavní spouštěcí záznam (MBR). Formát MBR podporuje velikost disku až 2 TiB. Největší velikost, kterou Azure podporuje pro nespravovaný disk operačního systému, je 2 TiB. Azure podporuje až 4 TiB pro nespravované disky dat.

**Jaká je největší velikost objektu blob stránky, která je podporovaná?**

Největší velikost objektu blob stránky, kterou Azure podporuje, je 8 TiB (8 191 GiB). Maximální velikost objektu blob stránky při připojení k virtuálnímu počítače jako disky dat nebo operačního systému je 4 TiB (4 095 GiB).

**Musím k vytváření, připojování, změně velikosti a nahrávání disků větších než 1 TiB používat novou verzi nástrojů Azure?**

Nemusíte upgradovat stávající nástroje Azure k vytvoření, připojení nebo změně velikosti disků větších než 1 TiB. Chcete-li nahrát soubor VHD z místního přímo do Azure jako objekt blob stránky nebo nespravovaný disk, musíte použít nejnovější sady nástrojů uvedené níže. Podporujeme pouze VHD uploady až 8 TiB.

|Nástroje Azure      | Podporované verze                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Číslo verze 4.1.0: vydání v červnu 2017 nebo novější|
|Azure CLI v1     | Číslo verze 0.10.13: Verze z května 2017 nebo novější|
|Azure CLI v2     | Číslo verze 2.0.12: vydání z července 2017 nebo novější|
|AzCopy              | Číslo verze 6.1.0: vydání v červnu 2017 nebo novější|

**Jsou velikosti disků P4 a P6 podporovány pro nespravované disky nebo objekty BLOB stránky?**

Velikosti disků P4 (32 GiB) a P6 (64 GiB) nejsou podporovány jako výchozí úrovně disků pro nespravované disky a objekty BLOB stránky. Musíte explicitně [nastavit úroveň objektu blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) na P4 a P6, aby byl disk mapován na tyto úrovně. Pokud nasadíte nespravovaný disk nebo objekt blob stránky s velikostí disku nebo délkou obsahu menší než 32 GiB nebo mezi 32 GiB až 64 GiB bez nastavení úrovně blob, budete pokračovat v přistát na P10 s 500 IOPS a 100 MiB/s a mapovanou cenovou vrstvou.

**Pokud byl můj stávající prémiový spravovaný disk menší než 64 GiB vytvořen před povolením malého disku (kolem 15. června 2017), jak se účtuje?**

Stávající malé prémiové disky menší než 64 GiB se nadále účtují podle cenové úrovně P10.

**Jak mohu přepnout diskovou vrstvu malých prémiových disků menší než 64 GiB z P10 na P4 nebo P6?**

Můžete pořídit snímek malých disků a potom vytvořit disk pro automatické přepnutí cenové úrovně na P4 nebo P6 na základě zřízené velikosti.

**Můžete změnit velikost existujících spravovaných disků z velikostí méně než 4 tebibytes (TiB) na nové nově zavedené velikosti disků až 32 TiB?**

Ano.

**Jaké jsou největší velikosti disků podporované službou Azure Backup a Azure Site Recovery?**

Největší velikost disku podporovaná službou Azure Backup je 32 TiB (4 TiB pro šifrované disky). Největší velikost disku podporovaná azure site recovery je 8 TiB. Podpora pro větší disky až do 32 TiB ještě není k dispozici v Azure Site Recovery.

**Jaké jsou doporučené velikosti virtuálních počítačů pro větší velikosti disků (>4 TiB) pro standardní disky SSD a Standardní pevný disk pro dosažení optimalizovaných diskových VOPS a šířky pásma?**

Pro dosažení propustnosti disku u velkých disků standardních SSD a standardních pevných disků (>4 TiB) nad 500 IOPS a 60 MiB/s, Doporučujeme nasadit nový virtuální počítač z jedné z následujících velikostí virtuálních zařízení pro optimalizaci výkonu: řady B, řady DSv2, Řady DSv3, Řady ESv3, Řady FS, Řady FS2, Řady FS2, Řady M, Řady GS, Řady NCv2, Řady NCv3 nebo Virtuální počítače řady LS. Připojení velkých disků k existujícím virtuálním počítačům nebo virtuálním počítačům, které nepoužívají výše uvedené doporučené velikosti, může dojít k nižšímu výkonu.

**Jak mohu upgradovat své disky (>4 TiB), které byly nasazeny během větší houfnib preview, aby se získaly vyšší IOPS & šířku pásma na GA?**

Můžete buď zastavit a spustit virtuální počítač, ke kterému je disk připojen, nebo odpojit a znovu připojit disk. Výkonnostní cíle větších disků byly zvýšeny jak pro prémiové SSD, tak pro standardní SSD disky v GA.

**V jakých oblastech jsou podporovány spravované disky 8 TiB, 16 TiB a 32 TiB?**

Disky 8 TiB, 16 TiB a 32 TiB disků jsou podporované ve všech oblastech v rámci globálního Azure, Microsoft Azure Government a Azure China 21Vianet.

**Podporujeme povolení ukládání hostitelů do mezipaměti na všech velikostech disků?**

Podporujeme ukládání hostitelů do mezipaměti readonly a čtení a zápis na diskech velikosti menší než 4 TiB. U velikostí disků většínež 4 TiB nepodporujeme nastavení ukládání do mezipaměti kromě žádné. Doporučujeme využití ukládání do mezipaměti pro menší velikosti disků, kde můžete očekávat, že budete sledovat lepší zvýšení výkonu s daty uloženými v mezipaměti do virtuálního počítače.

## <a name="what-if-my-question-isnt-answered-here"></a>Co když moje otázka není zodpovězena tady?

Pokud zde vaše otázka není uvedena, dejte nám vědět a my vám pomůžeme najít odpověď. Můžete psát otázku na konci tohoto článku v komentářích. Pokud chcete ohledně tohoto článku spolupracovat s týmem Azure Storage a dalšími členy komunity, použijte [fórum úložiště](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)MSDN Azure .

Chcete-li požádat o funkce, odešlete své požadavky a nápady do [fóra pro zpětnou vazbu služby Azure Storage](https://feedback.azure.com/forums/217298-storage).
