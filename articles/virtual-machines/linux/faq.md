---
title: Nejčastější dotazy pro virtuální počítače s Linuxem v Azure | Dokumentace Microsoftu
description: Poskytuje odpovědi na některé běžné dotazy týkající se virtuálních počítačů s Linuxem, které jsou vytvořené pomocí modelu Resource Manageru.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-management
ms.assetid: 3648e09c-1115-4818-93c6-688d7a54a353
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: 0623a7aff15184822ee8abde0b3c751f8a105b5b
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65463585"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Časté otázky o virtuálních počítačích s Linuxem
Tento článek se zabývá některými běžnými dotazy o virtuálních počítačích s Linuxem, které jsou vytvořené v Azure s využitím modelu nasazení Resource Manager. Windows verzi tohoto tématu naleznete v části [nejčastější dotazy o Windows Virtual Machines](../windows/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Co můžu spouštět na virtuálním počítači Azure?
Všichni předplatitelé můžou na virtuálním počítači Azure spouštět serverový software. Další informace najdete v tématu [Linux v distribucích Azure-Endorsed](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Kolik úložiště můžu využít s virtuálním počítačem?
Každý datový disk může být až na 4 TB (4 095 GB). Počet datových disků, které můžete využít, závisí na velikosti virtuálního počítače. Podrobnosti najdete v článku [Velikosti služeb Virtual Machines](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Služba Azure Managed Disks se doporučené nabídky diskových úložišť pro použití s Azure Virtual Machines k trvalému ukládání dat. Pro každý virtuální počítač můžete použít několik Spravovaných disků. Spravované disky nabídka dva typy odolných úložišť: Premium a Standard Managed Disks. Informace o cenách najdete v tématu [spravované disky ceny](https://azure.microsoft.com/pricing/details/managed-disks).

Účty úložiště Azure můžete také poskytují úložiště pro disk s operačním systémem a všechny datové disky. Každý disk je soubor .vhd uložený jako objekt blob stránky. Podrobnosti o cenách najdete v tématu [Podrobnosti o cenách úložiště](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Jak můžete přistupovat k virtuálnímu počítači?
Vytvořit vzdálené připojení k přihlášení k virtuálnímu počítači pomocí Secure Shell (SSH). Přečtěte si pokyny o tom, jak připojit [z Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [z Linuxu a Macu](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). SSH ve výchozím nastavení umožňuje maximálně 10 souběžných připojení. Toto číslo můžete navýšit upravením konfiguračního souboru.

Pokud máte problémy, projděte si [Poradce při potížích s Secure Shell (SSH) připojení](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Můžete použít dočasný disk (/ dev/sdb1) k ukládání dat?
Nepoužívejte k ukládání dat dočasný disk (/ dev/sdb1). Není pouze jako dočasné úložiště. Je tady riziko ztráty dat, které nelze obnovit.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Můžete kopírovat nebo naklonovat existující virtuální počítač Azure?
Ano. Pokyny najdete v tématu [vytvoření kopie virtuálního počítače s Linuxem v modelu nasazení Resource Manager](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Proč mi nezobrazují Kanada – střed a Kanada – východ oblastí prostřednictvím Azure Resource Manageru?
Tyto dvě nové oblasti, Kanada – střed a Kanada – východ nejsou registrovány automaticky k vytvoření virtuálního počítače pro existující předplatná Azure. Tuto registrace se provádí automaticky při nasazení virtuálního počítače na webu Azure portal do jakékoli jiné oblasti pomocí Azure Resource Manageru. Po nasazení virtuálního počítače do jiné oblasti Azure, třeba tyto nové oblasti k dispozici pro následující virtuální počítače.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Mohu přidat síťové rozhraní k virtuálnímu počítači po jeho vytvoření?
Ano, to je nyní možné. Virtuální počítač nejprve musí být zastaven uvolněno. Potom můžete přidat nebo odebrat síťovou kartu (pokud to není poslední síťového rozhraní na virtuálním počítači). 

## <a name="are-there-any-computer-name-requirements"></a>Existují nějaké požadavky na název počítače?
Ano. Název počítače může být maximálně 64 znaků. Zobrazit [konvence pravidla a omezení pojmenování](/azure/architecture/best-practices/naming-conventions) pro více informací o pojmenování prostředků.

## <a name="are-there-any-resource-group-name-requirements"></a>Existují prostředek požadavky na název skupiny?
Ano. Název skupiny prostředků může být maximálně 90 znaků. Zobrazit [konvence pravidla a omezení pojmenování](/azure/architecture/best-practices/naming-conventions) Další informace o skupinách prostředků.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Jaké jsou požadavky na uživatelské jméno při vytváření virtuálního počítače?

Uživatelská jména by měl být dlouhý 1 až 32 znaků.

Nejsou povoleny následující uživatelská jména:

| | | | |
|-----------------|-----------|--------------------|----------|
| `administrator` | `admin`   | `user`             | `user1`  |
| `test`          | `user2`   | `test1`            | `user3`  |
| `admin1`        | `1`       | `123`              | `a`      |
| `actuser`       | `adm`     | `admin2`           | `aspnet` |
| `backup`        | `console` | `david`            | `guest`  |
| `john`          | `owner`   | `root`             | `server` |
| `sql`           | `support` | `support_388945a0` | `sys`    |
| `test2`         | `test3`   | `user4`            | `user5`  |

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Jaké jsou požadavky na heslo při vytváření virtuálního počítače?

Existují různé heslo požadavky na délku, v závislosti na tom nástroje, které používáte:
 - Portál - 12 až 72 znaků
 - PowerShell – 8 až 123 znaků
 - Rozhraní příkazového řádku – mezi 12 až 123
 

Hesla také musí splňovat 3 ze 4 bezpečnostních požadavků následující:

* Mít nižší znaků
* Horní znaky
* Máte číslice
* Speciální znak (regulární výraz odpovídat [\W_])

Nejsou povoleny následující hesla:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Pa$$word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Heslo!</td>
        <td style="text-align:center">Heslo1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">ILOVEYOU!</td>
    </tr>
</table>
