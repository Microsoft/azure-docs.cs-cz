---
title: Přehled Azure Disk Storage
description: Přehled služby Azure Managed disks, které při používání virtuálních počítačů zpracovávají účty úložiště.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: contperfq1
ms.openlocfilehash: 773c5f95cdbec6961b063720106794e6ec00451d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89299928"
---
# <a name="introduction-to-azure-managed-disks"></a>Úvod ke spravovaným diskům Azure

Azure Managed disks jsou svazky úložiště na úrovni bloku, které spravuje Azure a používají se s Azure Virtual Machines. Managed disks je jako fyzický disk na místním serveru, ale virtualizuje se. U spravovaných disků stačí, když zadáte velikost disku, typ disku a zřídíte disk. Po zřízení disku Azure zpracuje zbytek.

Dostupné typy disků jsou disky Ultra, Premium SSD (Solid-State Drive), Standard SSD a standardní jednotky pevného disku (HDD). Informace o jednotlivých typech jednotlivých disků najdete v tématu [Výběr typu disku pro virtuální počítače s IaaS](disks-types.md).

## <a name="benefits-of-managed-disks"></a>Výhody spravovaných disků

Podívejme se na některé výhody, které získáte pomocí spravovaných disků.

### <a name="highly-durable-and-available"></a>Vysoce odolné a dostupné

Spravované disky jsou navržené pro 99,999% dostupnost. Služba Managed disks dosahuje tím, že poskytuje tři repliky vašich dat, což umožňuje vysokou odolnost. Pokud u jedné nebo i dvou replik dochází k problémům, zbývající repliky pomůžou zajistit trvalost vašich dat a vysokou odolnost proti chybám. Tato architektura pomáhá Azure konzistentně zajistit odolnost na podnikové úrovni pro disky infrastruktury jako služby (IaaS), a to s špičkovou neplatnou roční mírou selhání v oboru.

### <a name="simple-and-scalable-vm-deployment"></a>Jednoduché a škálovatelné nasazení virtuálních počítačů

Pomocí spravovaných disků můžete vytvořit až 50 000 **disků** virtuálního počítače typu v rámci předplatného na oblast, což vám umožní vytvořit v jednom předplatném tisíce **virtuálních počítačů** . Tato funkce také dále zvyšuje škálovatelnost [virtuálních počítačů s měřítkem](../virtual-machine-scale-sets/overview.md) , protože vám umožní vytvořit v sadě škálování virtuálního počítače až 1 000 virtuálních počítačů pomocí Image Marketplace.

### <a name="integration-with-availability-sets"></a>Integrace se skupinami dostupnosti

Spravované disky jsou integrované se skupinami dostupnosti, aby se zajistilo, že disky [virtuálních počítačů ve skupině dostupnosti](windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) jsou dostatečně izolované, aby se předešlo jednomu bodu selhání. Disky se automaticky umístí do různých jednotek škálování úložiště (razítka). Selže-li razítko z důvodu selhání hardwaru nebo softwaru, selžou pouze instance virtuálních počítačů s disky v těchto razítkech. Řekněme například, že máte aplikaci spuštěnou na pět virtuálních počítačů a že jsou virtuální počítače ve skupině dostupnosti. Disky pro tyto virtuální počítače se neuloží do stejného razítka, takže pokud se jedno razítko rozroste, ostatní instance aplikace se budou dál spouštět.

### <a name="integration-with-availability-zones"></a>Integrace s Zóny dostupnosti

Managed disks podporuje [zóny dostupnosti](../availability-zones/az-overview.md), což je nabídka s vysokou dostupností, která chrání vaše aplikace před selháními datových center. Zóny dostupnosti jsou jedinečná fyzická umístění uvnitř oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Kvůli odolnosti ve všech aktivovaných oblastech existují minimálně tři samostatné zóny. Díky Zóny dostupnosti Azure nabízí nejlepší smlouvu SLA 99,99% provozu virtuálního počítače.

### <a name="azure-backup-support"></a>Podpora Azure Backup

K ochraně před místními haváriemi se [Azure Backup](../backup/backup-overview.md) dá použít k vytvoření úlohy zálohování se zálohováním na základě času a zálohování. To vám umožní provést obnovení virtuálního počítače nebo spravovaného disku. V současné době Azure Backup podporuje velikosti disků až po 32 tebibyte (TiB) disků. [Přečtěte si další informace](../backup/backup-support-matrix-iaas.md) o podpoře zálohování virtuálních počítačů Azure.

### <a name="granular-access-control"></a>Podrobné řízení přístupu

[Řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md) můžete použít k přiřazení konkrétních oprávnění ke spravovanému disku jednomu nebo více uživatelům. Managed disks zveřejňuje nejrůznější operace, včetně čtení, zápisu (vytvoření a aktualizace), odstranění a načtení [identifikátoru URI sdíleného přístupového podpisu (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md) pro disk. Můžete udělit přístup pouze k operacím, které osoba potřebuje k provedení svých úloh. Například pokud nechcete, aby osoba kopírovala spravovaný disk do účtu úložiště, můžete se rozhodnout neudělit přístup k exportní akci pro tento spravovaný disk. Podobně pokud nechcete, aby uživatel k kopírování spravovaného disku používal identifikátor URI SAS, můžete se rozhodnout, že toto oprávnění neudělíte spravovanému disku.

### <a name="upload-your-vhd"></a>Nahrání virtuálního pevného disku

Přímé nahrávání usnadňuje přenos VHD na spravovaný disk Azure. Dříve museli byste postupovat podle dalšího Zahrnutého procesu, který zahrnuje přípravu vašich dat v účtu úložiště. Nyní je k dispozici méně kroků. Nahrávání na místní virtuální počítače do Azure je snazší, nahrajte na velké spravované disky a proces zálohování a obnovení se zjednodušuje. Také snižuje náklady tím, že vám umožní nahrávat data na spravované disky přímo bez jejich připojení k virtuálním počítačům. K nahrání virtuálních pevných disků do velikosti 32 TiB můžete použít přímé nahrávání.

Informace o tom, jak přenést virtuální pevný disk do Azure, najdete v článcích [CLI](linux/disks-upload-vhd-to-managed-disk-cli.md) nebo [PowerShell](windows/disks-upload-vhd-to-managed-disk-powershell.md) .

## <a name="security"></a>Zabezpečení

### <a name="private-links"></a>Privátní odkazy

Podpora privátních odkazů pro služby Managed disks je aktuálně ve verzi Preview a je možné ji použít k internímu importu nebo exportu spravovaného disku do vaší sítě. Privátní odkazy umožňují vygenerovat identifikátor URI sdíleného přístupového podpisu (SAS) pro nepřipojené spravované disky a snímky, které můžete použít k exportu dat do jiných oblastí pro regionální rozšiřování, zotavení po havárii a analýzu forenzní. Identifikátor URI SAS můžete použít také k přímému nahrání virtuálního pevného disku na prázdný disk z místního prostředí. Teď můžete využít [privátní odkazy](../private-link/private-link-overview.md) k omezení exportu a importu spravovaných disků tak, aby se mohly vyskytovat jenom v rámci vaší virtuální sítě Azure. Soukromé odkazy vám umožní zajistit, aby data byla přenášena pouze v rámci zabezpečené páteřní sítě Microsoftu.

Informace o tom, jak povolit privátní odkazy pro import nebo Export spravovaného disku, najdete v článcích o [CLI](linux/disks-export-import-private-links-cli.md) nebo na [portálu](disks-enable-private-links-for-import-export-portal.md) .

### <a name="encryption"></a>Šifrování

Managed disks nabízí dva různé druhy šifrování. První je šifrování na straně serveru (SSE), které provádí služba úložiště. Druhá je Azure Disk Encryption (ADE), kterou můžete povolit na discích s operačním systémem a datových discích pro vaše virtuální počítače.

#### <a name="server-side-encryption"></a>Šifrování na straně serveru

Šifrování na straně serveru poskytuje šifrování v klidovém prostředí a chrání vaše data, aby splňovala závazky zabezpečení vaší organizace a dodržování předpisů. Šifrování na straně serveru je ve výchozím nastavení povolené pro všechny spravované disky, snímky a image ve všech oblastech, kde jsou dostupné spravované disky. (Dočasné disky na druhé straně nejsou šifrovány šifrováním na straně serveru, pokud nepovolíte šifrování na hostiteli; viz [role disku: dočasné disky](#temporary-disk)).

Můžete buď dovolit, aby Azure spravoval vaše klíče za vás, jedná se o klíče spravované platformou, nebo můžete klíče spravovat sami, jedná se o klíče spravované zákazníkem. Podrobnosti najdete v článku [o šifrování na straně serveru Azure Disk Storage](windows/disk-encryption.md) .


#### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Azure Disk Encryption umožňuje šifrovat operační systém a datové disky používané virtuálním počítačem s IaaS. Toto šifrování zahrnuje spravované disky. V systému Windows se jednotky šifrují pomocí standardní technologie šifrování BitLockeru v oboru. Pro Linux jsou disky šifrované pomocí technologie DM-Crypt. Proces šifrování je integrovaný s Azure Key Vault, abyste mohli řídit a spravovat klíče pro šifrování disků. Další informace najdete v tématu [Azure Disk Encryption pro virtuální](linux/disk-encryption-overview.md) počítače se [systémem Linux nebo Azure Disk Encryption pro virtuální počítače s Windows](windows/disk-encryption-overview.md).

## <a name="disk-roles"></a>Role disků

V Azure existují tři hlavní diskové role: datový disk, disk s operačním systémem a dočasný disk. Tyto role se mapují na disky, které jsou připojené k vašemu virtuálnímu počítači.

![Diskové role v akci](media/virtual-machines-managed-disks-overview/disk-types.png)

### <a name="data-disk"></a>Datový disk

Datový disk je spravovaný disk, který je připojený k virtuálnímu počítači pro ukládání dat aplikací, nebo jiná data, která potřebujete zachovat. Datové disky se registrují jako jednotky SCSI a jsou označené písmenem, které zvolíte. Každý datový disk má maximální kapacitu 32 767 gibibajtech (GiB). Velikost virtuálního počítače určuje, kolik datových disků se k němu můžete připojit, a typ úložiště, které můžete použít k hostování disků.

### <a name="os-disk"></a>Disk OS

Každý virtuální počítač má jeden připojený disk s operačním systémem. Tento disk s operačním systémem má předem instalovaný operační systém, který byl vybrán při vytvoření virtuálního počítače. Tento disk obsahuje spouštěcí svazek.

Tento disk má maximální kapacitu 4 095 GiB.

### <a name="temporary-disk"></a>Dočasný disk

Většina virtuálních počítačů obsahuje dočasný disk, což není spravovaný disk. Dočasný disk poskytuje krátkodobé úložiště pro aplikace a procesy a je určen pouze k ukládání dat, jako jsou například stránky nebo soubory odkládacích souborů. Data na dočasném disku mohou být ztracena během [události údržby](windows/manage-availability.md?toc=/azure/virtual-machines/windows/toc.json#understand-vm-reboots---maintenance-vs-downtime) nebo při [opětovném nasazení virtuálního počítače](troubleshooting/redeploy-to-new-node-windows.md?toc=/azure/virtual-machines/windows/toc.json). Během úspěšného standardního restartování virtuálního počítače se uchovávají data na dočasném disku. Další informace o virtuálních počítačích bez dočasných disků najdete v tématu [velikosti virtuálních počítačů Azure bez místního dočasného disku](azure-vms-no-temp-disk.md).

Na virtuálních počítačích se systémem Azure Linux je dočasný disk typicky/dev/sdb a na virtuálních počítačích s Windows dočasný disk je ve výchozím nastavení D: Pokud na hostiteli nepovolíte šifrování, dočasný disk není zašifrovaný šifrováním na straně serveru.

## <a name="managed-disk-snapshots"></a>Snímky spravovaných disků

Snímek spravovaného disku je nekonzistentní úplná kopie spravovaného disku, který je ve výchozím nastavení uložený jako standardní spravovaný disk. Pomocí snímků můžete zálohovat své spravované disky kdykoli v čase. Tyto snímky existují nezávisle na zdrojovém disku a lze je použít k vytvoření nových spravovaných disků. 

Snímky se účtují na základě použité velikosti. Pokud třeba vytvoříte snímek spravovaného disku s zřízenou kapacitou 64 GiB a skutečnou velikostí dat 10 GiB, bude se tento snímek fakturovat jenom za využitou velikost dat 10 GiB. Velikost snímků můžete zobrazit tak, že si prohlížíte [sestavu využití Azure](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). Pokud je například použitá velikost snímku 10 GiB, zobrazí se **denní** sestava o využití 10 GIB/(31 dní) = 0,3226 jako spotřebované množství.

Další informace o tom, jak vytvořit snímky pro služby Managed disks, najdete v následujících zdrojích informací:

- [Vytvoření snímku spravovaného disku ve Windows](windows/snapshot-copy-managed-disk.md)
- [Vytvoření snímku spravovaného disku v systému Linux](linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>Images

Spravované disky také podporují vytváření spravované vlastní image. Můžete vytvořit image z vlastního virtuálního pevného disku v účtu úložiště nebo přímo z zobecněného (nástroje Sysprep) virtuálního počítače. Tento proces zachycuje jeden obrázek. Tato image obsahuje všechny spravované disky přidružené k virtuálnímu počítači, včetně operačního systému i datových disků. Tato spravovaná vlastní image umožňuje vytvářet stovky virtuálních počítačů s využitím vlastní image bez nutnosti kopírovat ani spravovat žádné účty úložiště.

Informace o vytváření imagí najdete v následujících článcích:

- [Postup zachycení spravované image zobecněného virtuálního počítače v Azure](windows/capture-image-resource.md)
- [Jak generalizovat a zachytit linuxový virtuální počítač s využitím Azure CLI](linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>Obrázky oproti snímkům

Je důležité pochopit rozdíl mezi obrázky a snímky. Se službou Managed disks můžete pořídit image zobecněného virtuálního počítače, který byl uvolněn. Tato image zahrnuje všechny disky připojené k virtuálnímu počítači. Pomocí této image můžete vytvořit virtuální počítač, který zahrnuje všechny disky.

Snímek je kopie disku v okamžiku pořízení snímku. Vztahuje se pouze na jeden disk. Pokud máte virtuální počítač, který má jeden disk (disk s operačním systémem), můžete pořídit snímek nebo jeho image a vytvořit virtuální počítač ze snímku nebo Image.

Snímek neobsahuje žádné povědomí o disku s výjimkou toho, který obsahuje. Díky tomu bude problematické používat ve scénářích, které vyžadují koordinaci více disků, jako je například proložení. Snímky by se musely vzájemně koordinovat a to se v tuto chvíli nepodporuje.

## <a name="disk-allocation-and-performance"></a>Přidělení a výkon disku

Následující diagram znázorňuje přidělení šířky pásma a IOPS pro disky v reálném čase pomocí systému zřizování na třech úrovních:

![Systém zřizování tří úrovní, který ukazuje přidělení šířky pásma a IOPS](media/virtual-machines-managed-disks-overview/real-time-disk-allocation.png)

Při zřizování první úrovně se nastaví vstupně-výstupní operace na disk a přiřazení šířky pásma.  Na druhé úrovni hostitel COMPUTE serveru implementuje zřizování SSD, uplatní ho jenom na data uložená v SSD serveru, které zahrnuje disky s mezipamětí (pro čtení a zápis) a také místní a dočasné disky. Nakonec se zřizování sítě virtuálních počítačů provádí na třetí úrovni pro jakékoli vstupně-výstupní operace, které výpočetní hostitel odesílá Azure Storage back-endu. V tomto schématu závisí výkon virtuálního počítače na nejrůznějších faktorech, od toho, jak virtuální počítač používá místní jednotku SSD, na počet připojených disků a také na výkon a typ mezipaměti pro disky, které jsou připojené.

Jako příklad těchto omezení bráníte tomu, aby se VIRTUÁLNÍm počítačům s Standard_DS1v1 5 000 IOPS, která je v mezipaměti, nebo ne, a to z důvodu omezení na úrovni jednotky SSD a sítě:

![Standard_DS1v1 příklad přidělení](media/virtual-machines-managed-disks-overview/example-vm-allocation.png)

Azure používá pro přenos na disk prioritní síťový kanál, který získá prioritu před jinou nízkou prioritou síťového provozu. To pomáhá diskům udržovat očekávaný výkon v případě kolizí sítě. Podobně Azure Storage zpracovává spory prostředků a další problémy na pozadí s automatickým vyrovnáváním zatížení. Azure Storage přiděluje požadované prostředky při vytváření disku a používá proaktivní a reaktivní vyvážení prostředků pro zpracování úrovně provozu. Tím se zajistí, že disky budou tolerovat očekávané cíle IOPS a propustnosti. Pomocí metrik na úrovni virtuálního počítače a disku můžete sledovat výstrahy týkající se výkonu a nastavení podle potřeby.

Projděte si náš [Návrh pro vysoký výkon](premium-storage-performance.md) a Naučte se osvědčené postupy pro optimalizaci konfigurací virtuálních počítačů a disků, abyste mohli dosáhnout požadovaného výkonu.

## <a name="next-steps"></a>Další kroky

Pokud se chcete podívat na další podrobnosti o spravovaných discích, podívejte se na: [lepší odolnost virtuálních počítačů Azure pomocí Managed disks](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency).

Přečtěte si další informace o jednotlivých typech disků, které nabízí Azure, který typ je vhodný pro vaše potřeby, a seznamte se s cíli jejich výkonu v našem článku o typech disků.

> [!div class="nextstepaction"]
> [Výběr typu disku pro virtuální počítače IaaS](disks-types.md)
