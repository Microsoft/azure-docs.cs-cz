---
title: Nejčastější dotazy k virtuálním počítačům se systémem Linux v Azure
description: Poskytuje odpovědi na některé běžné dotazy týkající se virtuálních počítačů se systémem Linux vytvořených pomocí modelu Správce prostředků.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: 84cb9860f247610621bff130aff155abf222f3c4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91274890"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Nejčastější dotazy týkající se Linux Virtual Machines
Tento článek popisuje některé běžné otázky týkající se virtuálních počítačů s Linux vytvořenými v Azure pomocí modelu nasazení Správce prostředků. Verzi tohoto tématu pro Windows najdete v části [Nejčastější dotazy týkající se Windows Virtual Machines](../windows/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Co můžu spouštět na virtuálním počítači Azure?
Všichni předplatitelé můžou na virtuálním počítači Azure spouštět serverový software. Další informace najdete v tématu věnovaném [distribucím v systému Linux v Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Kolik úložiště můžu využít s virtuálním počítačem?
Každý datový disk může mít až 32 767 GiB. Počet datových disků, které můžete využít, závisí na velikosti virtuálního počítače. Podrobnosti najdete v článku [Velikosti služeb Virtual Machines](../sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Azure Managed Disks jsou doporučené nabídky diskového úložiště pro použití s Azure Virtual Machines k trvalému ukládání dat. Pro každý virtuální počítač můžete použít více Managed Disks. Managed Disks nabízí dva typy trvalých možností úložiště: Premium a Standard Managed Disks. Informace o cenách najdete v tématu [Managed disks ceny](https://azure.microsoft.com/pricing/details/managed-disks).

Účty Azure Storage taky můžou poskytovat úložiště pro disk operačního systému a všechny datové disky. Každý disk je soubor .vhd uložený jako objekt blob stránky. Podrobnosti o cenách najdete v tématu [Podrobnosti o cenách úložiště](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Jak můžu získat přístup k virtuálnímu počítači?
Navažte vzdálené připojení pro přihlášení k virtuálnímu počítači pomocí Secure Shell (SSH). Přečtěte si pokyny k připojení [ze systému Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [Linux a Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). SSH ve výchozím nastavení umožňuje maximálně 10 souběžných připojení. Toto číslo můžete navýšit upravením konfiguračního souboru.

Pokud máte problémy, podívejte se na [řešení potíží s připojením Secure Shell (SSH)](../troubleshooting/troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json).

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Můžu k ukládání dat použít dočasný disk (adresář/dev/sdb1)?
Nepoužívejte dočasný disk (adresář/dev/sdb1) k ukládání dat. Pouze pro dočasné úložiště. Nehrozí riziko ztráty dat, která se nedají obnovit.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Můžu zkopírovat nebo naklonovat stávající virtuální počítač Azure?
Ano. Pokyny najdete v tématu [Postup vytvoření kopie virtuálního počítače se systémem Linux v modelu nasazení Správce prostředků](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Proč se mi nezobrazuje oblast Kanada – střed a Kanada – východní Azure Resource Manager?
Pro vytváření virtuálních počítačů v existujících předplatných Azure se neregistrují automaticky dvě nové oblasti Kanady – střed a Kanada – východ. Tato registrace se provádí automaticky, když se virtuální počítač nasadí prostřednictvím Azure Portal do jakékoli jiné oblasti pomocí Azure Resource Manager. Po nasazení virtuálního počítače do jakékoli jiné oblasti Azure by měly být nové oblasti dostupné pro další virtuální počítače.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Můžu na virtuální počítač přidat síťové rozhraní po jeho vytvoření?
Ano, teď je to možné. Nejdřív je potřeba zastavit navráceného virtuálního počítače. Pak můžete přidat nebo odebrat síťovou kartu (Pokud se nejedná o poslední síťovou kartu na VIRTUÁLNÍm počítači). 

## <a name="are-there-any-computer-name-requirements"></a>Existují nějaké požadavky na název počítače?
Ano. Název počítače může mít délku maximálně 64 znaků. Další informace o pojmenování prostředků najdete v tématu [pravidla a omezení konvence pojmenování](/azure/architecture/best-practices/resource-naming) .

## <a name="are-there-any-resource-group-name-requirements"></a>Existují nějaké požadavky na název skupiny prostředků?
Ano. Název skupiny prostředků může mít délku maximálně 90 znaků. Další informace o skupinách prostředků najdete v tématu [pravidla a omezení konvence pojmenování](/azure/architecture/best-practices/resource-naming) .

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Jaké jsou požadavky na uživatelské jméno při vytváření virtuálního počítače?

Uživatelská jména by měla mít délku 1-32 znaků.

Následující uživatelská jména nejsou povolena:

- `1`
- `123`
- `a`
- `actuser`
- `adm`
- `admin`
- `admin1`
- `admin2`
- `administrator`
- `aspnet`
- `backup`
- `console`
- `david`
- `guest`
- `john`
- `owner`
- `root`
- `server`
- `sql`
- `support_388945a0`
- `support`
- `sys`
- `test`
- `test1`
- `test2`
- `test3`
- `user`
- `user1`
- `user2`
- `user3`
- `user4`
- `user5`
- `video`


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Jaké jsou požadavky na heslo při vytváření virtuálního počítače?

Existují různé požadavky na délku hesla, a to v závislosti na použitém nástroji:
 - Portál – mezi 12-72 znaky
 - PowerShell – mezi 8-123 znaky
 - CLI – mezi 12-123 znaky
 - Šablony Azure Resource Manager (ARM) – 12-72 znaků a řídicích znaků nejsou povolené.
 

Hesla musí taky splňovat 3 z následujících 4 požadavků na složitost:

* Musí obsahovat malá písmena.
* Musí obsahovat velká písmena.
* Musí obsahovat číslici.
* Musí obsahovat speciální znak (odpovídající regulárnímu výrazu [\W_]).

Následující hesla nejsou povolena:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@ $ $w 0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">PA $ $word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Zadáno!</td>
        <td style="text-align:center">Heslo1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">iloveyou!</td>
    </tr>
</table>
