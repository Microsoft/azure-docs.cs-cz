---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/11/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 10afad7f782d1a98dfde5f7d708477375af54597
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56330962"
---
## <a name="benefits-of-managed-disks"></a>Výhody spravovaných disků

Probereme si některé z výhod, které získáte s použitím spravovaných disků.

### <a name="highly-durable-and-available"></a>Vysoká odolnost a dostupnost

Spravované disky jsou navržené pro 99,999 % dostupnost. Spravované disky dosáhnout tím, že poskytuje pomocí tří replik vašich dat umožňující vysoké odolnosti. Pokud se u jedné nebo dokonce u dvou replik objeví problémy, zbývající repliky pomohou zajistit trvalost vašich dat a vysokou odolnost proti chybám. Tato architektura pomohla Azure soustavně zajišťovat podnikové úrovně odolnosti pro infrastrukturu jako službu (IaaS) disky se špičkovou NULOVOU % selhání s hodinovou sazbu.

### <a name="simple-and-scalable-vm-deployment"></a>Snadné a škálovatelné nasazení virtuálních počítačů

Použití spravovaných disků, můžete vytvořit až 50 000 virtuálních počítačů **disky** typu v předplatné a oblast, abyste mohli vytvořit tisíce **virtuálních počítačů** v rámci jednoho předplatného. Tato funkce také dále zvyšuje škálovatelnost [škálovací sady virtuálních počítačů](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) , neboť umožňuje vytvořit až 1 000 virtuálních počítačů ve škálovací sadu virtuálních počítačů pomocí imagí Marketplace.

### <a name="integration-with-availability-sets"></a>Integrace se skupinami dostupnosti

Spravované disky jsou součástí skupiny dostupnosti a zkontrolujte, že disky [virtuálních počítačů ve skupině dostupnosti](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) jsou dostatečně izolované od sebe, aby jediný bod selhání. Disky se automaticky umísťují do odlišných uložišť jednotek škálování (razítka). Pokud razítkem nezdaří z důvodu selhání hardwaru nebo softwaru, selžou pouze instance Virtuálních počítačů na těchto jednotkách. Například Řekněme, že máte aplikaci spuštěné na pět virtuálních počítačů a virtuální počítače jsou ve skupině dostupnosti. Disky pro tyto virtuální počítače nebudou všechny ukládat ve stejném razítka, tak pokud jedno razítko ocitne mimo provoz ostatních instancí aplikace i nadále spouštět.

### <a name="azure-backup-support"></a>Podpora Azure Backup

K ochraně před regionální katastrofami [Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md) slouží k vytváření úlohy zálohování s časovou synchronizací zálohy a zásady uchovávání záloh. To umožňuje snadné obnovení virtuálního počítače budete provádět. Azure Backup nyní podporuje disky o velikosti až čtyři disky tebibyte (TiB). Další informace najdete v tématu [pomocí Azure Backup pro virtuální počítače se spravovanými disky](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

### <a name="granular-access-control"></a>Podrobné řízení přístupu

Můžete použít [řízení přístupu Azure na základě rolí (RBAC)](../articles/role-based-access-control/overview.md) k přiřazení konkrétních oprávnění pro spravovaného disku do jednoho nebo více uživatelů. Spravované disky odhalují nejrůznějších operací, včetně čtení, zápis (vytvoření/aktualizace), odstranit a načtením [sdílený přístupový podpis (SAS) URI](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) disku. Můžete udělit přístup jenom k operacím uživatel potřebuje pro výkon své práce. Například pokud nechcete, aby uživatel ke zkopírování spravovaného disku do účtu úložiště, můžete rozhodnete udělit přístup k exportu akce pro tento spravovaný disk. Podobně pokud nechcete, aby osoba, která použít identifikátor URI SAS ke zkopírování spravovaného disku, můžete neudělujte oprávnění, které chcete spravovaného disku.

## <a name="disk-roles"></a>Role disku

### <a name="data-disks"></a>Datové disky

Datový disk je spravovaný disk, který je připojen k virtuálnímu počítači k ukládání dat aplikací nebo data, která je potřeba nechat. Datové disky jsou registrovány jako disky SCSI a jsou označeny písmeno, kterou zvolíte. Každý datový disk má maximální kapacitu 4 095 gibibajtech (GiB). Velikost virtuálního počítače určuje, kolik datových disků můžete připojit a typ úložiště můžete použít k hostování disky.

### <a name="os-disks"></a>Disky s operačním systémem

Každý virtuální počítač má jeden disk připojený operačního systému. Tento disk s operačním systémem má předem nainstalovaný operační systém, který byl vybrán při vytvoření virtuálního počítače.

Tento disk má maximální kapacitu 2 048 GB.

### <a name="temporary-disk"></a>Dočasný disk

Každý virtuální počítač obsahuje dočasný disk, který není spravovaný disk. Dočasný disk obsahuje krátkodobé úložiště pro aplikace a procesy a je určené k ukládání pouze data, jako jsou stránkovací nebo odkládací soubory. Data na dočasném disku mohou být ztracena během [události údržby](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) nebo při opětovném nasazování virtuálního počítače. Během standardní restartování virtuálního počítače byste neměli zachovat data na dočasné jednotce. Existují však případy, kde nemusí uchovávat data, jako je například přesun do nového hostitele. Proto se všechna data na dočasné jednotky by neměl být data, která jsou zásadní pro systém.

## <a name="managed-disk-snapshots"></a>Snímky spravovaného disku

Snímek spravovaného disku je jen pro čtení úplná kopie spravovaného disku uložená jako standardní spravovaný disk ve výchozím nastavení. Snímky můžete zálohovat spravovaných disků v libovolném bodě v čase. Tyto snímky existují nezávisle na zdrojový disk a slouží k vytvoření nové spravované disky. Účtují se podle použité velikosti. Například pokud vytvoříte snímku spravovaného disku s zřízená kapacita 64 GiB a skutečné používaných dat velikosti 10 GB, tento snímek se mu fakturuje jenom používaných dat velikosti 10 GB.  

Další informace o tom, jak vytvářet snímky se spravovanými disky, najdete v následujících zdrojích:

* [Vytvoření kopie VHD uložené jako spravovaný disk pomocí snímků ve Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Vytvoření kopie VHD uložené jako spravovaný disk pomocí snímků v Linuxu](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>Image

Spravované disky podporují také vytváření spravovanou vlastní image. Image můžete vytvořit z vašeho vlastního virtuálního pevného disku v účtu úložiště nebo přímo zobecněný (připravené nástrojem Sysprep) virtuální počítač. Tento proces zachytí jedné image. Tento obrázek obsahuje všechny spravované disky přidružené k virtuálnímu počítači, včetně disků operačního systému i data. Tuto spravovanou vlastní image umožňuje vytváření stovek virtuálních počítačů pomocí vlastní image bez nutnosti kopírovat nebo spravovat všechny účty úložiště.

Informace o vytváření bitové kopie naleznete v následujících článcích:

* [Jak zachytit spravované image zobecněného virtuálního počítače v Azure](../articles/virtual-machines/windows/capture-image-resource.md)
* [Generalizace a zachytit virtuální počítač s Linuxem pomocí Azure CLI](../articles/virtual-machines/linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>Imagí a snímky

Je důležité porozumět rozdílu mezi imagí a snímků. Díky službě managed disks můžete využít image generalizovaného virtuálního počítače, které bylo zrušeno. Tato image obsahuje všechny disky připojené k virtuálnímu počítači. Vytvoření virtuálního počítače můžete použít tuto bitovou kopii a obsahuje všechny disky.

Snímek je kopie disku v bodě v čase pořízení snímku. Platí pouze pro jeden disk. Pokud máte virtuální počítač, který má jeden disk (disk s operačním systémem), může trvat snímku nebo obrázek se a vytvoření virtuálního počítače ze snímku nebo image.

Snímek nemá povědomí o žádný disk s výjimkou toho, které obsahuje. Díky tomu problematické pro použití ve scénářích, které vyžadují koordinaci více disků, jako je například prokládání. Snímky by musí mít možnost ke koordinaci mezi sebou a tím se aktuálně nepodporuje.

## <a name="next-steps"></a>Další postup

Další informace o typech jednotlivých disků, které Azure nabízí a jaký typ je vhodný pro vaše potřeby v náš článek věnovaný tomu typů disků nevidí.