---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 72b6c1f86b18df172994827ec78eb109fe82454e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75913786"
---
## <a name="benefits-of-managed-disks"></a>Výhody spravovaných disků

Pojďme si projít některé z výhod, které získáte pomocí spravovaných disků.

### <a name="highly-durable-and-available"></a>Vysoce odolná a dostupná

Spravované disky jsou navrženy pro 99,999% dostupnost. Spravované disky toho dosahují tím, že vám poskytnou tři repliky dat, což umožňuje vysokou odolnost. Pokud se problémy se dvěma replikami vyskytnou, zbývající repliky pomáhají zajistit trvalost dat a vysokou toleranci proti chybám. Tato architektura pomohla Azure konzistentně poskytovat odolnost na podnikové úrovni pro disky infrastruktury jako služby (IaaS) s špičkovou roční poruchovcí ZERO%.

### <a name="simple-and-scalable-vm-deployment"></a>Jednoduché a škálovatelné nasazení virtuálních počítače

Pomocí spravovaných disků můžete vytvořit až 50 000 **virtuálních disků** typu v rámci předplatného podle oblasti, což vám umožní vytvořit tisíce **virtuálních počítačů** v jednom předplatném. Tato funkce také dále zvyšuje škálovatelnost [škálovacích sad virtuálních strojů](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) tím, že umožňuje vytvářet až 1 000 virtuálních počítačů ve škálovací sadě virtuálních strojů pomocí image Marketplace.

### <a name="integration-with-availability-sets"></a>Integrace se sadami dostupnosti

Spravované disky jsou integrovány s sadami dostupnosti, aby bylo zajištěno, že disky [virtuálních počítačů v sadě dostupnosti](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) jsou od sebe dostatečně izolované, aby se zabránilo jedinému bodu selhání. Disky jsou automaticky umístěny v různých jednotkách úložného měřítka (razítka). Pokud razítko selže z důvodu selhání hardwaru nebo softwaru, nezdaří se pouze instance virtuálního počítače s disky na těchto razítkech. Řekněme například, že máte aplikaci spuštěnou na pěti virtuálních počítačích a virtuální chody jsou v sadě dostupnosti. Disky pro tyto virtuální počítače nebudou všechny uloženy ve stejném razítku, takže pokud jedno razítko přestane fungovat, ostatní instance aplikace budou nadále spuštěny.

### <a name="integration-with-availability-zones"></a>Integrace se zónami dostupnosti

Spravované disky podporují [zóny dostupnosti](../articles/availability-zones/az-overview.md), což je nabídka s vysokou dostupností, která chrání vaše aplikace před selháním datového centra. Zóny dostupnosti jsou jedinečná fyzická umístění uvnitř oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Aby byla zajištěna odolnost proti chybám, jsou ve všech povolených oblastech minimálně tři samostatné zóny. Díky zónám dostupnosti nabízí Azure nejlepší 99,99% dostupnost služby SLA virtuálního počítače.

### <a name="azure-backup-support"></a>Podpora azure backupu

K ochraně před regionálními katastrofami lze [azure backup](../articles/backup/backup-overview.md) vytvořit úlohu zálohování s časovými zálohami a zásadami uchovávání záloh. To umožňuje provádět snadné obnovení virtuálních stránek podle vůle. V současné době Azure Backup podporuje velikosti disků až čtyři tebibyte (TiB) disky.  Azure Backup podporuje zálohování a obnovení spravovaných disků. [Přečtěte si další informace](../articles/backup/backup-support-matrix-iaas.md) o podpoře zálohování virtuálních počítačích Azure.

### <a name="granular-access-control"></a>Podrobné řízení přístupu

Pomocí [řízení přístupu azure založené na rolích (RBAC)](../articles/role-based-access-control/overview.md) můžete přiřadit konkrétní oprávnění pro spravovaný disk pro jednoho nebo více uživatelů. Spravované disky zveřejňují různé operace, včetně čtení, zápisu (vytvoření/aktualizace), odstranění a načtení [identifikátoru URI sdíleného přístupového podpisu (SAS)](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) pro disk. Přístup můžete udělit pouze operacím, které osoba potřebuje k provedení své práce. Pokud například nechcete, aby osoba kopírovala spravovaný disk do účtu úložiště, můžete se rozhodnout, že pro tento spravovaný disk neudělíte přístup k akci exportu. Podobně pokud nechcete, aby osoba používala identifikátor URI SAS ke kopírování spravovaného disku, můžete se rozhodnout, že toto oprávnění spravovanému disku neudělíte.

### <a name="upload-your-vhd"></a>Nahrajte svůj vhd

 Přímé nahrávání usnadňuje přenos virtuálního pevného disku na spravovaný disk Azure. Dříve jste museli sledovat složitější proces, který zahrnoval přípravu dat v účtu úložiště. Nyní je méně kroků. Je jednodušší nahrát na místnívirtuální počítače do Azure, nahrát na velké spravované disky a proces zálohování a obnovení je zjednodušený. Také snižuje náklady tím, že umožňuje nahrávat data na spravované disky přímo bez jejich připojení k virtuálním počítačům. Můžete použít přímé nahrávání nahrát vhds až 32 TiB ve velikosti.

 Informace o tom, jak přenést virtuální pevné disky do Azure, najdete v [článcích rozhraní příkazového příkazu](../articles/virtual-machines/linux/disks-upload-vhd-to-managed-disk-cli.md) nebo [prostředí PowerShell.](../articles/virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md)

## <a name="encryption"></a>Šifrování

Spravované disky nabízejí dva různé druhy šifrování. První je šifrování na straně serveru (SSE), které provádí služba úložiště. Druhý je Azure Disk Encryption (ADE), které můžete povolit na operačních discích a datových discích pro vaše virtuální počítače.

### <a name="server-side-encryption"></a>Šifrování na straně serveru

[Šifrování na straně serveru Azure](../articles/virtual-machines/windows/disk-encryption.md) poskytuje šifrování v klidovém stavu a chrání vaše data, aby splňovala závazky organizace v oblasti zabezpečení a dodržování předpisů. Šifrování na straně serveru je ve výchozím nastavení povoleno pro všechny spravované disky, snímky a bitové kopie ve všech oblastech, kde jsou k dispozici spravované disky. Můžete buď povolit Azure spravovat vaše klíče za vás, to jsou klíče spravované platformou, nebo můžete spravovat klíče sami, to jsou klíče spravované zákazníkem. Další podrobnosti najdete na stránce Nejčastější dotazy [ke spravovaným diskům.](../articles/virtual-machines/windows/faq-for-disks.md#managed-disks-and-storage-service-encryption)

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Azure Disk Encryption umožňuje šifrovat operační systém a datové disky používané virtuálním počítačem IaaS. Toto šifrování zahrnuje spravované disky. V systému Windows jsou jednotky šifrovány pomocí standardní technologie šifrování nástroje BitLocker. Pro Linux jsou disky šifrovány pomocí technologie DM-Crypt. Proces šifrování je integrovaný s Azure Key Vault, abyste mohli řídit a spravovat klíče pro šifrování disků. Další informace najdete [v tématu Azure Disk Encryption for IaaS VMs](../articles/security/azure-security-disk-encryption-overview.md).

## <a name="disk-roles"></a>Role disků

V Azure existují tři hlavní role disku: datový disk, disk operačního systému a dočasný disk. Tyto role mapovat na disky, které jsou připojeny k virtuálnímu počítači.

![Role disku v akci](media/virtual-machines-managed-disks-overview/disk-types.png)

### <a name="data-disk"></a>Datový disk

Datový disk je spravovaný disk, který je připojený k virtuálnímu počítači k ukládání dat aplikací nebo jiných dat, která je třeba zachovat. Datové disky jsou registrovány jako jednotky SCSI a jsou označeny písmenem, které zvolíte. Každý datový disk má maximální kapacitu 32 767 gibibajtů (GiB). Velikost virtuálního počítače určuje, kolik datových disků k němu můžete připojit a typ úložiště, které můžete použít k hostování disků.

### <a name="os-disk"></a>Disk OS

Každý virtuální počítač má jeden připojený disk operačního systému. Tento disk operačního systému má předinstalovaný operační systém, který byl vybrán při vytvoření virtuálního počítače. Tento disk obsahuje spouštěcí svazek.

Tento disk má maximální kapacitu 2 048 GB.

### <a name="temporary-disk"></a>Dočasný disk

Každý virtuální modul obsahuje dočasný disk, který není spravovaný disk. Dočasný disk poskytuje krátkodobé úložiště pro aplikace a procesy a je určen pouze k ukládání dat, jako je stránka nebo odkládací soubory. Data na dočasném disku mohou být ztracena během události [události údržby](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) nebo při [opětovném nasazení virtuálního počítače](../articles/virtual-machines/troubleshooting/redeploy-to-new-node-windows.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json). Na virtuálních počítačích Azure Linux je dočasný disk ve výchozím nastavení /dev/sdb a na virtuálních počítačích windows je dočasný disk D: ve výchozím nastavení. Během úspěšného standardního restartování virtuálního počítače budou data na dočasném disku zachována.

## <a name="managed-disk-snapshots"></a>Snímky spravovaného disku

Snímek spravovaného disku je úplná kopie spravovaného disku, která je ve výchozím nastavení uložena jako standardní spravovaný disk, která je konzistentní se selháním. Pomocí snímků můžete spravované disky zálohovat kdykoli v čase. Tyto snímky existují nezávisle na zdrojovém disku a lze je použít k vytvoření nových spravovaných disků. 

Snímky se účtují na základě použité velikosti. Například pokud vytvoříte snímek spravovaného disku s zřízenou kapacitou 64 GiB a skutečně použitá velikost dat 10 GiB, tento snímek se účtuje pouze pro použitou velikost dat 10 GiB. Použitou velikost snímků najdete v [sestavě využití Azure](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). Například pokud je použitá velikost dat snímku 10 GiB, denní **sestava** využití zobrazí 10 GiB/(31 dní) = 0,3226 jako spotřebované množství.

Další informace o vytváření snímků pro spravované disky najdete v následujících zdrojích informací:

* [Vytvoření snímku spravovaného disku ve Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Vytvoření snímku spravovaného disku v Linuxu](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>Obrázky

Spravované disky také podporují vytváření spravované vlastní bitové kopie. Můžete vytvořit image z vlastního virtuálního pevného disku v účtu úložiště nebo přímo z generalizovaného (sysprepped) virtuálního počítače. Tento proces zachytí jeden obrázek. Tato bitová kopie obsahuje všechny spravované disky přidružené k virtuálnímu počítače, včetně operačního systému i datových disků. Tato spravovaná vlastní image umožňuje vytvářet stovky virtuálních počítačů pomocí vlastní image bez nutnosti kopírovat nebo spravovat účty úložiště.

Informace o vytváření obrázků naleznete v následujících článcích:

* [Jak zachytit spravovanou bitovou kopii generalizovaného virtuálního počítače v Azure](../articles/virtual-machines/windows/capture-image-resource.md)
* [Jak generalizovat a zachytit linuxový virtuální počítač s využitím Azure CLI](../articles/virtual-machines/linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>Obrázky versus snímky

Je důležité pochopit rozdíl mezi obrázky a snímky. Se spravovanými disky můžete pořídit bitovou kopii generalizovaného virtuálního počítače, který byl navrácen. Tato bitová kopie obsahuje všechny disky připojené k virtuálnímu počítače. Tuto bitovou kopii můžete použít k vytvoření virtuálního počítače a obsahuje všechny disky.

Snímek je kopie disku v okamžiku pořízení snímku. Platí pouze pro jeden disk. Pokud máte virtuální počítače, který má jeden disk (disk operačního systému), můžete pořídit snímek nebo jeho bitovou kopii a vytvořit virtuální ho z snímku nebo image.

Snímek nemá povědomí o žádný disk kromě toho, který obsahuje. To ztěžuje použití ve scénářích, které vyžadují koordinaci více disků, jako je například prokládání. Snímky by musely být schopny vzájemně koordinovat a to není aktuálně podporováno.

## <a name="disk-allocation-and-performance"></a>Přidělení a výkon disku

Následující diagram znázorňuje rozdělení šířky pásma a vstupně-operace v reálném čase pro disky pomocí tříúrovňového zřizovacího systému:

![Zřizovací systém tří úrovní zobrazující šířku pásma a přidělení VOPS](media/virtual-machines-managed-disks-overview/real-time-disk-allocation.png)

Zřizování první úrovně nastavuje vstupně-disk VOPS a přiřazení šířky pásma.  Na druhé úrovni hostitel výpočetního serveru implementuje zřizování SSD a aplikuje ho pouze na data uložená na ssd serveru, která zahrnují disky s ukládáním do mezipaměti (ReadWrite a ReadOnly) a také místní a dočasné disky. Nakonec zřizování sítě virtuálních počítačových sítí probíhá na třetí úrovni pro všechny vstupně-va, které hostitel výpočetních prostředků odešle do back-endu služby Azure Storage. S tímto schématem závisí výkon virtuálního počítače na různých faktorech, od způsobu, jakým virtuální počítač používá místní ssd, až po počet připojených disků a také na typu výkonu a ukládání disků, které připojil.

Jako příklad těchto omezení je Standard_DS1v1 virtuálního počítače zabráněno v dosažení potenciálu 5 000 VOPS disku P30, ať už je uložen do mezipaměti nebo ne, z důvodu omezení na úrovni SSD a sítě:

![Standard_DS1v1 příklad přidělení](media/virtual-machines-managed-disks-overview/example-vm-allocation.png)

Azure používá prioritní síťový kanál pro provoz na disku, který získává přednost před další nízkou prioritou síťového provozu. To pomáhá diskům udržovat očekávaný výkon v případě konfliktů v síti. Podobně Azure Storage zpracovává konflikty prostředků a další problémy na pozadí s automatickým vyrovnáváním zatížení. Azure Storage přiděluje požadované prostředky při vytváření disku a použije proaktivní a reaktivní vyvažování prostředků pro zpracování úrovně provozu. To dále zajišťuje, že disky mohou udržovat jejich očekávané cíle vipos a propustnost. Metriky na úrovni virtuálních počítačů a Disk můžete podle potřeby sledovat výstrahy výkonu a nastavení.

Podívejte se na náš [návrh pro vysoce výkonný](../articles/virtual-machines/windows/premium-storage-performance.md) článek, kde se dozvíte osvědčené postupy pro optimalizaci konfigurací virtuálního počítače a disku, abyste mohli dosáhnout požadovaného výkonu

## <a name="next-steps"></a>Další kroky

Pokud chcete, aby se video na spravovaných discích podrobněji objevilo, podívejte se [na: Lepší odolnost virtuálního počítače Azure se spravovanými disky](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency).

Další informace o jednotlivých typech disků, které Azure nabízí, které typy jsou vhodné pro vaše potřeby, a informace o jejich výkonnostních cílech najdete v našem článku o typech disků.
