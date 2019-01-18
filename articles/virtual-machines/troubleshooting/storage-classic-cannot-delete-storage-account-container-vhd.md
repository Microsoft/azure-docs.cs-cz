---
title: Řešení chyb při odstranění účtů úložiště Azure classic, kontejnerů nebo virtuálních pevných disků | Dokumentace Microsoftu
description: Řešení potíží při odstraňování prostředků úložiště s připojenými virtuálními pevnými disky.
services: storage
author: AngshumanNayakMSFT
tags: top-support-issue,azure-service-management
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/11/2019
ms.author: annayak
ms.openlocfilehash: 72493c6bba556314c3652be5251463d1d1e005bd
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54383441"
---
# <a name="troubleshoot-classic-storage-resource-deletion-errors"></a>Řešení chyb při odstraňování prostředku klasického úložiště.
Tento článek obsahuje pokyny k odstraňování problémů při jedné z následujících chyb při odstraňování účtu úložiště Azure classic, kontejneru nebo souboru objektu blob stránky *.vhd. 


Tento článek se týká pouze problémy s klasické prostředky úložiště. Když uživatel odstraní klasický virtuální počítač pomocí webu Azure portal, Powershellu nebo rozhraní příkazového řádku a potom disky nejsou automaticky odstraněny. Uživatel získá možnost odstranění prostředku "Disk". V případě, že není vybraná možnost, nebudou moct prostředku "Disk" odstranění účtu úložiště, kontejner a skutečné *.vhd stránkovacího souboru objektu blob.

Další informace o službě Azure disks najdete [tady](../../virtual-machines/windows/about-disks-and-vhds.md). Azure zabraňuje odstranění disku, který je připojen k virtuálnímu počítači zabránit v poškození. Zabrání také odstranění kontejnerů a účty úložiště, které mají objekt blob stránky, který je připojen k virtuálnímu počítači. 

## <a name="what-is-a-disk"></a>Co je "Disk"?
Prostředek "Disk" se používá k připojení *.vhd stránkovacího souboru objektu blob k virtuálnímu počítači jako disku s operačním systémem nebo datový disk. Zapůjčení přidržte *.vhd soubor disk s operačním systémem nebo datový prostředek disku, až do odstranění, bude pokračovat. Prostředek úložiště v cestě je uvedeno výše image nelze odstranit, pokud prostředek "Disk" odkazuje na ni.

![Otevřít snímek obrazovky portálu s podoknem "Vlastnosti" disk (klasický)](./media/storage-classic-cannot-delete-storage-account-container-vhd/Disk_Lease_Illustration.jpg) 


## <a name="steps-while-deleting-a-classic-virtual-machine"></a>Postup při odstraňování klasický virtuální počítač 
1. Odstraňte klasický virtuální počítač.
2. Pokud je zaškrtnuto políčko "Disky", **zapůjčení disku** (viz obrázek výše) spojená s objekty blob stránky *.vhd nefunguje. Soubor objektu blob *.vhd Skutečná stránka bude stále existovat v účtu úložiště.
![Otevřít snímek obrazovky portálu s podoknem chyba "Odstranit" virtuální počítač (klasický)](./media/storage-classic-cannot-delete-storage-account-container-vhd/steps_while_deleting_classic_vm.jpg) 

3. Po přerušení zapůjčení disky je možné odstranit objekty BLOB stránky, samotného. Účet úložiště nebo kontejneru můžete odstraňovat, jakmile se odstraní všechny "Disk" prostředky v nich k dispozici.

>[!NOTE] 
>Pokud uživatel odstraní virtuální počítač, ale ne virtuální pevný disk, poplatky za úložiště se dál nabíhat stránkovacího souboru *.vhd objektů blob. Budou se účtovat poplatky za byla v souladu s typ účtu úložiště, zkontrolujte [stránce s cenami](https://azure.microsoft.com/en-us/pricing/details/storage/) další podrobnosti. Pokud uživatel už si klade za cíl použít virtuální pevné disky, odstraňte it nebo je na budoucím poplatkům vyhnout. 

## <a name="unable-to-delete-storage-account"></a>Nepovedlo se odstranit účet úložiště 

Když se uživatel pokusí odstranit klasický účet úložiště, který už je nepotřebujete, může se zobrazit následující chování uživatele.

#### <a name="azure-portal"></a>portál Azure 
Uživatel přejde do klasického úložiště účtu na [webu Azure portal](https://portal.azure.com) a klikne na tlačítko **odstranit**, uživateli se zobrazí následující zpráva: 

S disky "připojené" k virtuálnímu počítači

![Otevřít snímek obrazovky portálu s podoknem chyba "Odstranit" virtuální počítač (klasický)](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_attached_portal.jpg) 


S disky "Ne" virtuální počítač

![Otevřít snímek obrazovky portálu s podoknem bez chyb "Odstranit" virtuální počítač (klasický)](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_unattached_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Uživatel se pokusí odstranit účet úložiště, který je již nejsou déle používány, prostřednictvím rutin prostředí PowerShell classic. Uživateli se zobrazí následující zpráva:

><span style="color:cyan">**Remove-AzureStorageAccount -StorageAccountName myclassicaccount**</span>

><span style="color:red">Remove-AzureStorageAccount : Chybného požadavku: Myclassicaccount účet úložiště obsahuje některé aktivní Image a/nebo disky, například  
myclassicaccount. Zkontrolujte tyto Image a/nebo disky se odeberou před odstraněním tohoto účtu úložiště.</span>

## <a name="unable-to-delete-storage-container"></a>Nepovedlo se odstranit kontejner úložiště

Když se uživatel pokusí odstranit kontejner objektů blob klasického úložiště, který už je nepotřebujete, může se zobrazit následující chování uživatele.

#### <a name="azure-portal"></a>portál Azure 
Azure portal nepovolí uživateli odstranit kontejner, pokud existuje zapůjčení "disků:" odkazuje na soubor *.vhd stránky objektů blob v kontejneru. Je záměrné, abyste zabránili nechtěnému odstranění souboru virtuální pevné disky s disky zapůjčení na ně. 

![Otevřít snímek obrazovky s podoknem "seznam" kontejneru úložiště na portálu](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_container_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Pokud uživatel zvolí možnost odstranit pomocí prostředí PowerShell, bude výsledkem následující chyba. 

><span style="color:cyan">**Remove-AzureStorageContainer -Context $context -Name vhds**</span>

><span style="color:red">Remove-AzureStorageContainer : Vzdálený server vrátil chybu: (412) je aktuálně v kontejneru zapůjčení a žádné ID zapůjčení byla zadaná v žádosti... Kód stavu HTTP: 412 – HTTP chybová zpráva: V kontejneru je aktuálně zapůjčení a v požadavku bylo zadáno žádné ID zapůjčení.</span>

## <a name="unable-to-delete-a-vhd"></a>Nepovedlo se odstranit virtuální pevný disk 

Po odstranění virtuálního počítače Azure, uživatel pokusí odstranit soubor virtuálního pevného disku (objekt blob stránky) a zobrazí se následující zpráva:

#### <a name="azure-portal"></a>portál Azure 
Na portálu může být dvě možnosti v závislosti na seznam vybraných pro odstranění objektů BLOB.

1. Pokud jsou vybrán "Zapůjčený" objekty BLOB, pouze na tlačítko Odstranit nezobrazí.
![Otevřít snímek obrazovky s podoknem kontejneru objektů blob "Přehled" na portálu](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_portal.jpg)


2. Pokud vyberete kombinaci "Zapůjčený" a "K dispozici" objekty BLOB, na tlačítko "Odstranit" se zobrazí. Ale operace "Odstranit" za objekty BLOB stránky, které mají zapůjčení disku. v nich. 
![Snímek obrazovky portálu s otevřeným podoknem kontejneru objektů blob v "seznamu"](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_1.jpg)
![snímek obrazovky portálu s vybraném objektu blob "odstranit" Otevřít podokno](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_2.jpg)

#### <a name="azure-powershell"></a>Azure PowerShell 
Pokud uživatel zvolí možnost odstranit pomocí prostředí PowerShell, bude výsledkem následující chyba. 

><span style="color:cyan">**Remove-AzureStorageBlob -Context $context -Container vhds -Blob "classicvm-os-8698.vhd"**</span>

><span style="color:red">Remove-AzureStorageBlob : Vzdálený server vrátil chybu: (412) je aktuálně zapůjčení u objektu blob a žádné ID zapůjčení byla zadaná v žádosti... Kód stavu HTTP: 412 – HTTP chybová zpráva: V požadavku bylo zadáno žádné ID zapůjčení u objektu blob je aktuálně zapůjčení.</span>


## <a name="resolution-steps"></a>Postup řešení

### <a name="to-remove-classic-disks"></a>Chcete-li odebrat klasické disky
Na portálu Azure portal postupujte podle těchto kroků:
1.  Přejděte na [Azure Portal](https://portal.azure.com).
2.  Přejděte Disks(classic). 
3.  Klikněte na kartu Disky. ![Otevřít snímek obrazovky s podoknem kontejneru objektů blob "Přehled" na portálu](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_disks_tab.jpg)
 
4.  Vyberte datový disk a potom klikněte na Odstranit disk.
 ![Otevřít snímek obrazovky s podoknem kontejneru objektů blob "Přehled" na portálu](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_delete_disk.jpg)
 
5.  Opakujte operaci, která původně selhala.
6.  Účet úložiště nebo kontejneru nelze odstranit, dokud obsahuje jeden Disk.

### <a name="to-remove-classic-images"></a>Chcete-li odebrat klasické Image   
Na portálu Azure portal postupujte podle těchto kroků:
1.  Přejděte na [Azure Portal](https://portal.azure.com).
2.  Přejděte do bitové kopie operačního systému (klasická).
3.  Odstraňte image.
4.  Opakujte operaci, která původně selhala.
5.  Účet úložiště nebo kontejneru nelze odstranit, dokud obsahuje jedinou bitovou kopii.
