---
title: Nejčastější dotazy týkající se virtuálních počítačů s Linuxem v Azure
description: Poskytuje odpovědi na některé běžné otázky týkající se virtuálních počítačů Linux vytvořených pomocí modelu Resource Manager.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: 3c6a5e011a536cc9c34565d4f72a9bee6c6a5254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945169"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Často kladené otázky týkající se virtuálních počítačů s Linuxem
Tento článek řeší některé běžné otázky týkající se virtuálních počítačů Linux vytvořených v Azure pomocí modelu nasazení Správce prostředků. Verze tohoto tématu pro Windows najdete [v tématu Nejčastější dotazy týkající se virtuálních počítačů s Windows](../windows/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Co můžu spouštět na virtuálním počítači Azure?
Všichni předplatitelé můžou na virtuálním počítači Azure spouštět serverový software. Další informace najdete v [tématu Linux v azure-endorsed distributions](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Kolik úložiště můžu využít s virtuálním počítačem?
Každý datový disk může být až 32 767 GiB. Počet datových disků, které můžete využít, závisí na velikosti virtuálního počítače. Podrobnosti najdete v článku [Velikosti služeb Virtual Machines](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Spravované disky Azure jsou doporučené nabídky diskového úložiště pro použití s virtuálními počítači Azure pro trvalé ukládání dat. S každým virtuálním počítačem můžete použít více spravovaných disků. Spravované disky nabízejí dva typy odolných možností úložiště: prémiové a standardní spravované disky. Informace o cenách naleznete v [tématu Ceny spravovaných disků](https://azure.microsoft.com/pricing/details/managed-disks).

Účty úložiště Azure můžete také poskytnout úložiště pro disk operačního systému a všechny datové disky. Každý disk je soubor .vhd uložený jako objekt blob stránky. Podrobnosti o cenách najdete v tématu [Podrobnosti o cenách úložiště](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Jak se dostanu k virtuálnímu počítači?
Vytvořte vzdálené připojení pro přihlášení k virtuálnímu počítači pomocí zabezpečeného prostředí (SSH). Podívejte se na pokyny, jak se připojit [z Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [z Linuxu a Macu](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). SSH ve výchozím nastavení umožňuje maximálně 10 souběžných připojení. Toto číslo můžete navýšit upravením konfiguračního souboru.

Pokud máte potíže, podívejte se [na článek Poradce při potížích s připojením Zabezpečeného prostředí (SSH).](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Mohu použít dočasný disk (/dev/sdb1) k ukládání dat?
Nepoužívejte dočasný disk (/dev/sdb1) k ukládání dat. Je tam jen pro dočasné skladování. Riskujete ztrátu dat, která nelze obnovit.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Můžu zkopírovat nebo naklonovat existující virtuální počítač Azure?
Ano. Pokyny najdete v [tématu Jak vytvořit kopii virtuálního počítače s Linuxem v modelu nasazení Správce prostředků](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Proč nevidím oblasti Kanada – střed a Kanada – východ prostřednictvím Správce prostředků Azure?
Dvě nové oblasti Kanada – střed a Kanada – východ se automaticky nezaevidují pro vytváření virtuálních strojů pro stávající předplatná Azure. Tato registrace se provádí automaticky, když se virtuální počítač nasadí přes portál Azure do jakékoli jiné oblasti pomocí Správce prostředků Azure. Po nasazení virtuálního počítače do jakékoli jiné oblasti Azure by nové oblasti měly být dostupné pro další virtuální počítače.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Můžu do virtuálního virtuálního sekana po vytvoření přidat nic?
Ano, to je nyní možné. Virtuální město nejprve musí být zastaveno přidělené. Pak můžete přidat nebo odebrat nic (pokud se nejedná o poslední nic na virtuálním počítači). 

## <a name="are-there-any-computer-name-requirements"></a>Existují nějaké požadavky na název počítače?
Ano. Název počítače může být maximálně 64 znaků na délku. Další informace o pojmenování prostředků najdete v [tématu Pravidla a omezení konvencí pojmenování.](/azure/architecture/best-practices/resource-naming)

## <a name="are-there-any-resource-group-name-requirements"></a>Existují nějaké požadavky na název skupiny prostředků?
Ano. Název skupiny prostředků může mít maximálně 90 znaků. Další informace o skupinách prostředků naleznete [v tématu Pravidla a omezení konvencí pojmenování.](/azure/architecture/best-practices/resource-naming)

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Jaké jsou požadavky na uživatelské jméno při vytváření virtuálního virtuálního soudu?

Uživatelská jména by měla být 1 - 32 znaků na délku.

Následující uživatelská jména nejsou povolena:

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
| `video`         |

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Jaké jsou požadavky na heslo při vytváření virtuálního počítače?

V závislosti na nástroji, který používáte, jsou různé požadavky na délku hesla:
 - Portál - mezi 12 - 72 znaky
 - PowerShell - mezi 8 - 123 znaků
 - CLI - mezi 12 - 123
 

Hesla musí také splňovat 3 z následujících 4 požadavků na složitost:

* Musí obsahovat malá písmena.
* Musí obsahovat velká písmena.
* Musí obsahovat číslici.
* Musí obsahovat speciální znak (odpovídající regulárnímu výrazu [\W_]).

Následující hesla nejsou povolena:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0.</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Pa$$word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Heslo!</td>
        <td style="text-align:center">Heslo1</td>
        <td style="text-align:center">Heslo22</td>
        <td style="text-align:center">Miluji tě!</td>
    </tr>
</table>