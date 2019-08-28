---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 69c63d4eb2e0bfd04bb232cb0cf39965a5b77193
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70104234"
---
## <a name="benefits-of-managed-disks"></a>Výhody spravovaných disků

Podívejme se na některé výhody, které získáte pomocí spravovaných disků.

### <a name="highly-durable-and-available"></a>Vysoká odolnost a dostupnost

Spravované disky jsou navržené pro 99,999% dostupnost. Služba Managed disks dosahuje tím, že poskytuje tři repliky vašich dat, což umožňuje vysokou odolnost. Pokud se u jedné nebo dokonce u dvou replik objeví problémy, zbývající repliky pomohou zajistit trvalost vašich dat a vysokou odolnost proti chybám. Tato architektura pomáhá Azure konzistentně zajistit odolnost na podnikové úrovni pro disky infrastruktury jako služby (IaaS), a to s špičkovou neplatnou roční mírou selhání v oboru.

### <a name="simple-and-scalable-vm-deployment"></a>Jednoduché a škálovatelné nasazení virtuálních počítačů

Pomocí spravovaných disků můžete vytvořit až 50 000 **disků** virtuálního počítače typu v rámci předplatného na oblast, což vám umožní vytvořit v jednom předplatném tisíce **virtuálních počítačů** . Tato funkce také dále zvyšuje škálovatelnost [virtuálních počítačů](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) s měřítkem, protože vám umožní vytvořit v sadě škálování virtuálního počítače až 1 000 virtuálních počítačů pomocí Image Marketplace.

### <a name="integration-with-availability-sets"></a>Integrace se skupinami dostupnosti

Spravované disky jsou integrované se skupinami dostupnosti, aby se zajistilo, že disky [virtuálních počítačů ve skupině dostupnosti](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) jsou dostatečně izolované, aby se předešlo jednomu bodu selhání. Disky se automaticky umístí do různých jednotek škálování úložiště (razítka). Selže-li razítko z důvodu selhání hardwaru nebo softwaru, selžou pouze instance virtuálních počítačů s disky v těchto razítkech. Řekněme například, že máte aplikaci spuštěnou na pět virtuálních počítačů a že jsou virtuální počítače ve skupině dostupnosti. Disky pro tyto virtuální počítače se neuloží do stejného razítka, takže pokud se jedno razítko rozroste, ostatní instance aplikace se budou dál spouštět.

### <a name="integration-with-availability-zones"></a>Integrace s Zóny dostupnosti

Managed disks podporuje [zóny dostupnosti](../articles/availability-zones/az-overview.md), což je nabídka s vysokou dostupností, která chrání vaše aplikace před selháními datových center. Zóny dostupnosti jsou jedinečná fyzická umístění v rámci oblasti Azure. Každá zóna se skládá z jednoho nebo více datových Center vybavených nezávislým napájením, chlazením a sítí. Aby se zajistila odolnost, existuje minimálně tři samostatné zóny ve všech povolených oblastech. Díky Zóny dostupnosti Azure nabízí nejlepší smlouvu SLA 99,99% provozu virtuálního počítače.

### <a name="azure-backup-support"></a>Podpora Azure Backup

K ochraně před místními haváriemi se [Azure Backup](../articles/backup/backup-overview.md) dá použít k vytvoření úlohy zálohování se zálohováním na základě času a zálohování. To vám umožní provádět snadné obnovení virtuálních počítačů. V současné době Azure Backup podporuje velikosti disků až na čtyři disky tebibyte (TiB).  Azure Backup podporuje zálohování a obnovení spravovaných disků. [Přečtěte si další informace](../articles/backup/backup-support-matrix-iaas.md) o podpoře zálohování virtuálních počítačů Azure.

### <a name="granular-access-control"></a>Podrobné řízení přístupu

[Řízení přístupu na základě role (RBAC) v Azure](../articles/role-based-access-control/overview.md) můžete použít k přiřazení konkrétních oprávnění ke spravovanému disku jednomu nebo více uživatelům. Managed disks zveřejňuje nejrůznější operace, včetně čtení, zápisu (vytvoření a aktualizace), odstranění a načtení identifikátoru [URI sdíleného přístupového podpisu (SAS)](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) pro disk. Můžete udělit přístup pouze k operacím, které osoba potřebuje k provedení svých úloh. Například pokud nechcete, aby osoba kopírovala spravovaný disk do účtu úložiště, můžete se rozhodnout neudělit přístup k exportní akci pro tento spravovaný disk. Podobně pokud nechcete, aby uživatel k kopírování spravovaného disku používal identifikátor URI SAS, můžete se rozhodnout, že toto oprávnění neudělíte spravovanému disku.

## <a name="encryption"></a>Šifrování

Managed disks nabízí dva různé druhy šifrování. První je Šifrování služby Storage (SSE), kterou provádí služba úložiště. Druhá je Azure Disk Encryption, kterou můžete povolit na discích s operačním systémem a datových discích pro vaše virtuální počítače.

### <a name="storage-service-encryption-sse"></a>Šifrování služby Storage (SSE)

[Šifrování služby Azure Storage](../articles/storage/common/storage-service-encryption.md) poskytuje šifrování v klidovém prostředí a chrání vaše data, aby splňovala závazky zabezpečení vaší organizace a dodržování předpisů. SSE je ve výchozím nastavení povolená pro všechny spravované disky, snímky a image ve všech oblastech, kde jsou dostupné spravované disky. Další podrobnosti najdete na [stránce s nejčastějšími dotazy Managed disks](../articles/virtual-machines/windows/faq-for-disks.md#managed-disks-and-storage-service-encryption) .

### <a name="azure-disk-encryption-ade"></a>Azure Disk Encryption (ADE)

Azure Disk Encryption umožňuje šifrovat operační systém a datové disky používané virtuálním počítačem s IaaS. Toto šifrování zahrnuje spravované disky. V systému Windows se jednotky šifrují pomocí standardní technologie šifrování BitLockeru v oboru. Pro Linux jsou disky šifrované pomocí technologie DM-crypt. Proces šifrování je integrovaný s Azure Key Vault, abyste mohli řídit a spravovat klíče pro šifrování disků. Další informace najdete v tématu [Azure Disk Encryption pro virtuální počítače s IaaS](../articles/security/azure-security-disk-encryption-overview.md).

## <a name="disk-roles"></a>Diskové role

V Azure existují tři hlavní diskové role: datový disk, disk s operačním systémem a dočasný disk. Tyto role se mapují na disky, které jsou připojené k vašemu virtuálnímu počítači.

![Diskové role v akci](media/virtual-machines-managed-disks-overview/disk-types.png)

### <a name="data-disk"></a>Datový disk

Datový disk je spravovaný disk, který je připojený k virtuálnímu počítači pro ukládání dat aplikací, nebo jiná data, která potřebujete zachovat. Datové disky se registrují jako jednotky SCSI a jsou označené písmenem, které zvolíte. Každý datový disk má maximální kapacitu 32 767 gibibajtech (GiB). Velikost virtuálního počítače určuje, kolik datových disků se k němu můžete připojit, a typ úložiště, které můžete použít k hostování disků.

### <a name="os-disk"></a>Disk s operačním systémem

Každý virtuální počítač má jeden připojený disk s operačním systémem. Tento disk s operačním systémem má předem instalovaný operační systém, který byl vybrán při vytvoření virtuálního počítače.

Tento disk má maximální kapacitu 2 048 GiB.

### <a name="temporary-disk"></a>Dočasný disk

Každý virtuální počítač obsahuje dočasný disk, což není spravovaný disk. Dočasný disk poskytuje krátkodobé úložiště pro aplikace a procesy a je určen pouze k ukládání dat, jako jsou například stránky nebo odkládací soubory. Data na dočasném disku se můžou během události [údržby](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) nebo při [opětovném nasazení virtuálního počítače](../articles/virtual-machines/troubleshooting/redeploy-to-new-node-windows.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)ztratit. Na virtuálních počítačích se systémem Azure Linux je dočasný disk ve výchozím nastavení/dev/sdb a na virtuálních počítačích s Windows je ve výchozím nastavení dočasný disk D:. Během úspěšného standardního restartování virtuálního počítače se uchovávají data na dočasném disku.

## <a name="managed-disk-snapshots"></a>Snímky spravovaných disků

Snímek spravovaného disku je nekonzistentní úplná kopie spravovaného disku, který je ve výchozím nastavení uložený jako standardní spravovaný disk. Pomocí snímků můžete zálohovat své spravované disky kdykoli v čase. Tyto snímky existují nezávisle na zdrojovém disku a lze je použít k vytvoření nových spravovaných disků. 

Snímky se účtují na základě použité velikosti. Pokud třeba vytvoříte snímek spravovaného disku s zřízenou kapacitou 64 GiB a skutečnou velikostí dat 10 GiB, bude se tento snímek fakturovat jenom za využitou velikost dat 10 GiB. Velikost snímků můžete zobrazit tak, že si prohlížíte [sestavu využití Azure](https://docs.microsoft.com/en-us/azure/billing/billing-understand-your-bill). Pokud je například velikost použité velikosti snímku 10 GiB, zobrazí se v sestavě využití 10 GiB/(31 dnů × 24 hodin) = 0,013441 GiB jako spotřebované množství.

Další informace o tom, jak vytvářet snímky se službou Managed disks, najdete v následujících zdrojích informací:

* [Vytvoření kopie VHD uložené jako spravovaný disk pomocí snímků ve Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Vytvoření kopie VHD uložené jako spravovaný disk pomocí snímků v Linuxu](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>Obrázky

Spravované disky také podporují vytváření spravované vlastní image. Můžete vytvořit image z vlastního virtuálního pevného disku v účtu úložiště nebo přímo z zobecněného (nástroje Sysprep) virtuálního počítače. Tento proces zachycuje jeden obrázek. Tato image obsahuje všechny spravované disky přidružené k virtuálnímu počítači, včetně operačního systému i datových disků. Tato spravovaná vlastní image umožňuje vytvářet stovky virtuálních počítačů s využitím vlastní image bez nutnosti kopírovat ani spravovat žádné účty úložiště.

Informace o vytváření imagí najdete v následujících článcích:

* [Postup zachycení spravované image zobecněného virtuálního počítače v Azure](../articles/virtual-machines/windows/capture-image-resource.md)
* [Jak generalizovat a zachytit linuxový virtuální počítač s využitím Azure CLI](../articles/virtual-machines/linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>Obrázky oproti snímkům

Je důležité pochopit rozdíl mezi obrázky a snímky. Se službou Managed disks můžete pořídit image zobecněného virtuálního počítače, který byl uvolněn. Tato image zahrnuje všechny disky připojené k virtuálnímu počítači. Pomocí této image můžete vytvořit virtuální počítač, který zahrnuje všechny disky.

Snímek je kopie disku v okamžiku pořízení snímku. Vztahuje se pouze na jeden disk. Pokud máte virtuální počítač, který má jeden disk (disk s operačním systémem), můžete pořídit snímek nebo jeho image a vytvořit virtuální počítač ze snímku nebo Image.

Snímek neobsahuje žádné povědomí o disku s výjimkou toho, který obsahuje. Díky tomu bude problematické používat ve scénářích, které vyžadují koordinaci více disků, jako je například proložení. Snímky by se musely vzájemně koordinovat a to se v tuto chvíli nepodporuje.

## <a name="disk-allocation-and-performance"></a>Přidělení a výkon disku

Následující diagram znázorňuje přidělení šířky pásma a IOPS pro disky v reálném čase pomocí systému zřizování na třech úrovních:

![Systém zřizování tří úrovní, který ukazuje přidělení šířky pásma a IOPS](media/virtual-machines-managed-disks-overview/real-time-disk-allocation.png)

Při zřizování první úrovně se nastaví vstupně-výstupní operace na disk a přiřazení šířky pásma.  Na druhé úrovni hostitel COMPUTE serveru implementuje zřizování SSD, uplatní ho jenom na data uložená v SSD serveru, které zahrnuje disky s mezipamětí (pro čtení a zápis) a také místní a dočasné disky. Nakonec se zřizování sítě virtuálních počítačů provádí na třetí úrovni pro jakékoli vstupně-výstupní operace, které výpočetní hostitel odesílá Azure Storage back-endu. V tomto schématu závisí výkon virtuálního počítače na nejrůznějších faktorech, od toho, jak virtuální počítač používá místní jednotku SSD, na počet připojených disků a také na výkon a typ mezipaměti pro disky, které jsou připojené.

Jako příklad těchto omezení bráníte virtuálnímu počítači s Standard_DS1v1 v tom, aby bylo možné dosáhnout 5 000 IOPS disku P30, ať už je v mezipaměti, nebo ne, z důvodu omezení na úrovni SSD a sítě:

![Standard_DS1v1 – příklad přidělení](media/virtual-machines-managed-disks-overview/example-vm-allocation.png)

Azure používá pro přenos na disk prioritní síťový kanál, který získá prioritu před jinou nízkou prioritou síťového provozu. To pomáhá diskům udržovat očekávaný výkon v případě kolizí sítě. Podobně Azure Storage zpracovává spory prostředků a další problémy na pozadí s automatickým vyrovnáváním zatížení. Azure Storage přiděluje požadované prostředky při vytváření disku a používá proaktivní a reaktivní vyvážení prostředků pro zpracování úrovně provozu. Tím se zajistí, že disky budou tolerovat očekávané cíle IOPS a propustnosti. Pomocí metrik na úrovni virtuálního počítače a disku můžete sledovat výstrahy týkající se výkonu a nastavení podle potřeby.

Projděte si náš [Návrh pro vysoký výkon](../articles/virtual-machines/windows/premium-storage-performance.md) a Naučte se osvědčené postupy pro optimalizaci konfigurací virtuálních počítačů a disků, abyste mohli dosáhnout požadovaného výkonu.

## <a name="next-steps"></a>Další postup

Přečtěte si další informace o jednotlivých typech disků, které nabízí Azure, který typ je vhodný pro vaše potřeby, a seznamte se s cíli jejich výkonu v našem článku o typech disků.
