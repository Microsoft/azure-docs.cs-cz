---
title: Poradce při odstraňování účtů klasického úložiště Azure, kontejnerů nebo virtuálních discích | Dokumenty společnosti Microsoft
description: Jak řešit problémy při odstraňování prostředků úložiště obsahujících připojené virtuální počítače.
services: storage
author: AngshumanNayakMSFT
tags: top-support-issue,azure-service-management
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/11/2019
ms.author: annayak
ms.openlocfilehash: 95c85309058911d6767eb44efd7b37ddac7a9119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77915033"
---
# <a name="troubleshoot-classic-storage-resource-deletion-errors"></a>Poradce při potížích s klasickými chybami odstranění prostředků úložiště
Tento článek obsahuje pokyny pro řešení potíží, když dojde k jedné z následujících chyb při pokusu o odstranění účtu klasického úložiště Azure, kontejneru nebo souboru objektů blob stránky *.vhd. 


Tento článek se týká pouze problémů s klasickými prostředky úložiště. Pokud uživatel odstraní klasický virtuální počítač pomocí portálu Azure, PowerShellnebo CLI pak disky nejsou automaticky odstraněny. Uživatel získá možnost odstranit prostředek "Disk". V případě, že tato možnost není vybrána, prostředek "Disk" zabrání odstranění účtu úložiště, kontejneru a skutečného souboru objektů blob stránky *.vhd.

Další informace o discích Azure naleznete [zde](../../virtual-machines/windows/managed-disks-overview.md). Azure zabraňuje odstranění disku, který je připojen k virtuálnímu počítači, aby se zabránilo poškození. Také zabraňuje odstranění kontejnerů a účtů úložiště, které mají objekt blob stránky, který je připojený k virtuálnímu virtuálnímu počítačům. 

## <a name="what-is-a-disk"></a>Co je to "Disk"?
Prostředek "Disk" se používá k připojení souboru objektů blob stránky *.vhd do virtuálního počítače jako disk operačního systému nebo datový disk. Disk operačního systému nebo prostředek datového disku, dokud nebude odstraněn, bude nadále obsahovat zapůjčení souboru *.vhd. Žádný prostředek úložiště v cestě zobrazené na obrázku nelze odstranit, pokud na něj odkazuje prostředek "Disk".

![Snímek obrazovky portálu s otevřeným podoknem "Vlastnosti" disku (klasické)](./media/storage-classic-cannot-delete-storage-account-container-vhd/Disk_Lease_Illustration.jpg) 


## <a name="steps-while-deleting-a-classic-virtual-machine"></a>Kroky při odstranění klasického virtuálního počítače 

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


1. Odstraňte klasický virtuální počítač.
2. Pokud je zaškrtnuto políčko Disky, **zapůjčení disku** (znázorněno na obrázku výše) přidružené k objektu blob stránky *.vhd je přerušeno. Skutečný objekt blob stránky *.vhd soubor bude stále existovat v účtu úložiště.
![Snímek obrazovky portálu s otevřeným podoknem chyb virtuálního počítače (klasického) "Odstranit"](./media/storage-classic-cannot-delete-storage-account-container-vhd/steps_while_deleting_classic_vm.jpg) 

3. Jakmile je zapůjčení disk(s) přerušeno, lze odstranit samotné objekty blob stránky. Účet úložiště nebo kontejner lze odstranit po odstranění všech prostředků "Disk", které jsou v nich přítomny.

>[!NOTE] 
>Pokud uživatel odstraní virtuální počítač, ale ne virtuální pevný disk, poplatky za úložiště budou nadále narůstat v souboru objektu blob stránky *.vhd. Poplatky budou v souladu s typem účtu úložiště, další podrobnosti najdete na [stránce s cenami.](https://azure.microsoft.com/pricing/details/storage/) Pokud uživatel již nemá v úmyslu používat virtuální pevné disky, odstraňte je/je, abyste se vyhnuli budoucím poplatkům. 

## <a name="unable-to-delete-storage-account"></a>Účet úložiště nelze odstranit. 

Když se uživatel pokusí odstranit klasický účet úložiště, který již není potřeba, může se uživateli zobrazit následující chování.

#### <a name="azure-portal"></a>portál Azure 
Uživatel přejde na klasický účet úložiště na [webu Azure Portal](https://portal.azure.com) a klikne na **Odstranit**, uživateli se zobrazí následující zpráva: 

S disky "připojenými" k virtuálnímu počítači

![Snímek obrazovky portálu s otevřeným podoknem chyb virtuálního počítače (klasického) "Odstranit"](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_attached_portal.jpg) 


S disky "nepřipojenými" k virtuálnímu počítači

![Snímek obrazovky portálu s otevřeným podoknem virtuálního počítače (klasického) "Odstranit" bez chyb](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_unattached_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Uživatel se pokusí odstranit účet úložiště, který se již nepoužívá, pomocí klasických rutin prostředí PowerShell. Uživateli se zobrazí následující zpráva:

> <span style="color:cyan">**Odebrat AzureStorageAccount -StorageAccountName myclassicaccount**</span>
> 
> <span style="color:red">Remove-AzureStorageAccount : BadRequest: Účet úložiště myclassicaccount má některé aktivní image (y) a/nebo disky, například.  
> myclassicaccount. Před odstraněním tohoto účtu úložiště zkontrolujte, zda budou tyto bitové kopie nebo disky odebrány.</span>

## <a name="unable-to-delete-storage-container"></a>Kontejner úložiště nelze odstranit.

Když se uživatel pokusí odstranit klasický kontejner objektů blob úložiště, který už není potřeba, může se uživateli zobrazit následující chování.

#### <a name="azure-portal"></a>portál Azure 
Portál Azure by uživateli nepovolil odstranění kontejneru, pokud existuje zapůjčení "Disk(s)" ukazující na soubor objektů blob stránky *.vhd v kontejneru. Je to záměrné, aby se zabránilo náhodnému odstranění vhd (y) soubor s disky pronájem na ně. 

![Snímek obrazovky portálu s otevřeným podoknem "seznam" kontejneru úložiště](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_container_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Pokud se uživatel rozhodne odstranit pomocí prostředí PowerShell, bude mít za následek následující chybu. 

> <span style="color:cyan">**Odebrat-AzureStorageContainer -Kontext $context -Name vhds**</span>
> 
> <span style="color:red">Remove-AzureStorageContainer : Vzdálený server vrátil chybu: (412) V současné době je zapůjčení kontejneru a v požadavku nebylo zadáno žádné ID zapůjčení.. Stavový kód HTTP: 412 - Chybová zpráva HTTP: V kontejneru je aktuálně zapůjčení a v požadavku nebylo zadáno žádné ID zapůjčení.</span>

## <a name="unable-to-delete-a-vhd"></a>Nelze odstranit virtuální pevný disk. 

Po odstranění virtuálního počítače Azure se uživatel pokusí odstranit soubor vhd (objekt blob stránky) a obdrží následující zprávu:

#### <a name="azure-portal"></a>portál Azure 
Na portálu mohou být dvě prostředí v závislosti na seznamu objektů BLOB vybraných k odstranění.

1. Pokud jsou vybrány pouze objekty BLOB "Pronajaté", tlačítko Odstranit se nezobrazí.
![Snímek obrazovky portálu s otevřeným podoknem "seznam" objektu blob kontejneru](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_portal.jpg)


2. Pokud je vybrána kombinace objektů BLOB "Pronajaté" a "K dispozici", zobrazí se tlačítko "Odstranit". Ale operace "Odstranit" zanechá za objekty BLOB stránky, které mají zapůjčení disku na ně. 
![Snímek obrazovky portálu s podoknem "seznam" kontejneru blob otevře](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_1.jpg)
![snímek obrazovky portálu s otevřeným vybraným podoknem odstranění objektu blob](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_2.jpg)

#### <a name="azure-powershell"></a>Azure PowerShell 
Pokud se uživatel rozhodne odstranit pomocí prostředí PowerShell, bude mít za následek následující chybu. 

> <span style="color:cyan">**Remove-AzureStorageBlob -Kontext $context -Kontejner vhds -Blob "classicvm-os-8698.vhd"**</span>
> 
> <span style="color:red">Remove-AzureStorageBlob : Vzdálený server vrátil chybu: (412) Aktuálně existuje zapůjčení objektu blob a v požadavku nebylo zadáno žádné ID zapůjčení.. Stavový kód HTTP: 412 - chybová zpráva HTTP: Aktuálně existuje zapůjčení objektu blob a v požadavku nebylo zadáno žádné ID zapůjčení.</span>


## <a name="resolution-steps"></a>Postup řešení

### <a name="to-remove-classic-disks"></a>Odebrání klasických disků
Postupujte podle následujících kroků na webu Azure Portal:
1.  Přejděte na [Azure Portal](https://portal.azure.com).
2.  Přejděte na disky (klasické). 
3.  Klikněte na kartu ![Disky. Snímek obrazovky portálu s otevřeným podoknem "seznam" objektu blob kontejneru.](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_disks_tab.jpg)
 
4.  Vyberte datový disk a potom klikněte na Odstranit disk.
 ![Snímek obrazovky portálu s otevřeným podoknem "seznam" objektu blob kontejneru](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_delete_disk.jpg)
 
5.  Opakujte operaci Delete, která se dříve nezdařila.
6.  Účet úložiště nebo kontejner nelze odstranit, pokud má jeden disk.

### <a name="to-remove-classic-images"></a>Odebrání klasických obrázků   
Postupujte podle následujících kroků na webu Azure Portal:
1.  Přejděte na [Azure Portal](https://portal.azure.com).
2.  Přejděte na obrázky operačního systému (klasické).
3.  Odstraňte obrázek.
4.  Opakujte operaci Delete, která se dříve nezdařila.
5.  Účet úložiště nebo kontejner nelze odstranit, pokud má jednu bitovou kopii.
