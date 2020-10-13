---
title: Řešení chyb při odstraňování účtů úložiště Azure Classic, kontejnerů nebo virtuálních pevných disků | Microsoft Docs
description: Jak řešit problémy při odstraňování prostředků úložiště obsahujících připojené virtuální pevné disky.
services: storage
author: AngshumanNayakMSFT
tags: top-support-issue,azure-service-management
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/11/2019
ms.author: annayak
ms.openlocfilehash: c74f2ef9eed25719e722970671406c850b6a59b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361853"
---
# <a name="troubleshoot-classic-storage-resource-deletion-errors"></a>Řešení chyb při odstraňování klasických prostředků úložiště
V tomto článku najdete pokyny k odstraňování potíží, když se při pokusu o odstranění účtu úložiště Azure Classic, kontejneru nebo souboru BLOB stránky virtuálního pevného disku zobrazí některá z následujících chyb. 


Tento článek se zabývá jenom problémy s klasickými prostředky úložiště. Pokud uživatel odstraní klasický virtuální počítač pomocí Azure Portal PowerShellu nebo rozhraní příkazového řádku, disky se automaticky neodstraní. Uživatel získá možnost odstranit prostředek "disk". V případě, že není vybraná možnost, prostředek "disk" zabrání v odstraňování účtu úložiště, kontejneru a skutečného souboru objektů blob stránky *. VHD.

Další informace o discích Azure najdete [tady](../../virtual-machines/managed-disks-overview.md). Azure zabraňuje odstranění disku připojeného k virtuálnímu počítači, aby nedocházelo k poškození. Zabrání taky odstranění kontejnerů a účtů úložiště, které mají objekt blob stránky, který je připojený k virtuálnímu počítači. 

## <a name="what-is-a-disk"></a>Co je to "disk"?
Prostředek "disk" se používá k připojení souboru objektu blob stránky *. VHD k virtuálnímu počítači, jako je disk s operačním systémem nebo datový disk. Disk s operačním systémem nebo prostředek datového disku, dokud se neodstraní, bude i nadále uchovávat zapůjčení pro soubor *. VHD. Všechny prostředky úložiště v cestě zobrazené na obrázku níže se nedají odstranit, pokud na ni odkazuje prostředek "disk".

![Snímek obrazovky portálu s otevřeným panelem vlastností disk (Classic)](./media/storage-classic-cannot-delete-storage-account-container-vhd/Disk_Lease_Illustration.jpg) 


## <a name="steps-while-deleting-a-classic-virtual-machine"></a>Kroky při odstraňování klasického virtuálního počítače 

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


1. Odstraňte klasický virtuální počítač.
2. Pokud je zaškrtnuto políčko "disky", **zapůjčení disku** (zobrazené v obrázku výše) přidružené k objektu blob stránky *. VHD je přerušeno. Skutečný objekt blob stránky *. VHD v účtu úložiště stále existuje.
![Snímek obrazovky se zobrazí dialogové okno pro potvrzení odstranění virtuálního počítače.](./media/storage-classic-cannot-delete-storage-account-container-vhd/steps_while_deleting_classic_vm.jpg) 

3. Po přerušení zapůjčení disků je možné objekty blob stránky odstranit. Účet úložiště nebo kontejner je možné odstranit, jakmile se odstraní všechny prostředky "disk", které jsou v nich přítomné.

>[!NOTE] 
>Pokud uživatel odstraní virtuální počítač, ale ne virtuální pevný disk, poplatky za úložiště se budou dál rozlišit na soubor blob stránky *. VHD. Poplatky budou v souladu s typem účtu úložiště. Další podrobnosti najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/storage/) . Pokud uživatel už nemá v úmyslu používat virtuální pevné disky, odstraňte ho nebo ho, aby se předešlo budoucím poplatkům. 

## <a name="unable-to-delete-storage-account"></a>Nepovedlo se odstranit účet úložiště. 

Když se uživatel pokusí odstranit klasický účet úložiště, který už není potřebný, může se uživateli zobrazit následující chování.

#### <a name="azure-portal"></a>portál Azure 
Uživatel přejde na klasický účet úložiště v [Azure Portal](https://portal.azure.com) a klikne na **Odstranit**, uživateli se zobrazí následující zpráva: 

S disky připojenými k virtuálnímu počítači

![Snímek obrazovky se zobrazí zpráva s vysvětlením, proč nelze odstranit účet úložiště.](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_attached_portal.jpg) 


Disky s nepřipojeným diskem (s) k virtuálnímu počítači

![Snímek obrazovky portálu se spuštěným virtuálním počítačem (Classic), který není v otevřeném podokně chyby](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_unattached_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Uživatel se pokusí odstranit účet úložiště, který se už nepoužívá, a to pomocí klasických rutin PowerShellu. Uživateli se zobrazí následující zpráva:

> <span style="color:cyan">**Remove-AzureStorageAccount-StorageAccountName myclassicaccount**</span>
> 
> <span style="color:red">Remove-AzureStorageAccount: důvodu chybného požadavku: účet úložiště myclassicaccount obsahuje některé aktivní Image nebo disky, např.  
> myclassicaccount. Před odstraněním tohoto účtu úložiště zajistěte, aby byly tyto Image (y) a/nebo disky odebrané.</span>

## <a name="unable-to-delete-storage-container"></a>Nepovedlo se odstranit kontejner úložiště.

Když se uživatel pokusí odstranit klasický kontejner objektů BLOB úložiště, který už není potřeba, může se zobrazit následující chování.

#### <a name="azure-portal"></a>portál Azure 
Azure Portal by nemohlo uživateli odstranit kontejner, pokud v kontejneru existují "disky", které odkazují na soubor objektu blob stránky VHD. Je navržený tak, aby se zabránilo nechtěnému odstranění souboru VHD s zapůjčením na disk (y). 

![Snímek obrazovky portálu se spuštěným oknem Seznam kontejnerů úložiště](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_container_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Pokud se uživatel rozhodne odstranit pomocí PowerShellu, bude výsledkem následující chyba. 

> <span style="color:cyan">**Remove-AzureStorageContainer-Context $context-Name VHD**</span>
> 
> <span style="color:red">Remove-AzureStorageContainer: vzdálený server vrátil chybu: (412) pro kontejner je aktuálně zapůjčení a v žádosti nebylo zadané žádné ID zapůjčení. Stavový kód HTTP: 412-chybová zpráva HTTP: v tomto kontejneru je momentálně zapůjčení a v žádosti nebylo zadané žádné ID zapůjčení.</span>

## <a name="unable-to-delete-a-vhd"></a>Nepovedlo se odstranit VHD. 

Po odstranění virtuálního počítače Azure se uživatel pokusí odstranit soubor VHD (objekt blob stránky) a přijmout následující zprávu:

#### <a name="azure-portal"></a>portál Azure 
Na portálu můžou být v závislosti na seznamu objektů BLOB vybraných k odstranění dvě prostředí.

1. Pokud je vybráno pouze "pronajaté" objekty blob, tlačítko Odstranit se nezobrazí.
![Snímek obrazovky portálu s otevřeným oknem seznamu objektů BLOB kontejneru a vybranými pouze pronajatými objekty blob](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_portal.jpg)


2. Pokud je vybraná kombinace "pronajatých" a "dostupných" objektů blob, zobrazí se tlačítko "odstranit". Operace "Delete" ale ponechá za objekty blob stránky, na kterých se zapůjčení disku zachová. 
![Snímek obrazovky portálu s otevřeným oknem seznamu objektů BLOB kontejneru a vybranými možnostmi ](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_1.jpg)
 ![ zapůjčení a dostupných objektů BLOB Snímek obrazovky portálu s otevřeným otevřeným oknem objektů blob "odstranit"](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_2.jpg)

#### <a name="azure-powershell"></a>Azure PowerShell 
Pokud se uživatel rozhodne odstranit pomocí PowerShellu, bude výsledkem následující chyba. 

> <span style="color:cyan">**Remove-AzureStorageBlob-Context $context-Container VHD-blob "classicvm-OS-8698. VHD"**</span>
> 
> <span style="color:red">Remove-AzureStorageBlob: vzdálený server vrátil chybu: (412) momentálně existuje zapůjčení pro objekt BLOB a v žádosti nebylo zadané žádné ID zapůjčení. Stavový kód HTTP: 412-chybová zpráva HTTP: v tomto objektu BLOB je momentálně zapůjčení a v žádosti nebylo zadané žádné ID zapůjčení.</span>


## <a name="resolution-steps"></a>Postup řešení

### <a name="to-remove-classic-disks"></a>Postup odebrání klasických disků
Postupujte podle těchto kroků na Azure Portal:
1.  Přejděte na [Azure Portal](https://portal.azure.com).
2.  Přejděte na disky (klasické). 
3.  Klikněte na kartu disky. ![ Snímek obrazovky se zobrazí Azure Portal s vybranými disky (klasické) a názvem klasického disku a účtem úložiště.](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_disks_tab.jpg)
 
4.  Vyberte datový disk a potom klikněte na Odstranit disk.
 ![Snímek Azure Portal obrazovky se zvolenými vybranými disky (klasickými), s vybraným datovým diskem a možností odstranění.](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_delete_disk.jpg)
 
5.  Opakujte operaci odstranění, která se dřív nezdařila.
6.  Účet úložiště nebo kontejner se nedají odstranit, pokud má jeden disk.

### <a name="to-remove-classic-images"></a>Postup odebrání klasických imagí   
Postupujte podle těchto kroků na Azure Portal:
1.  Přejděte na [Azure Portal](https://portal.azure.com).
2.  Přejděte na image operačních systémů (klasické).
3.  Odstraňte obrázek.
4.  Opakujte operaci odstranění, která se dřív nezdařila.
5.  Účet úložiště nebo kontejner se nedají odstranit, pokud má jeden obrázek.
