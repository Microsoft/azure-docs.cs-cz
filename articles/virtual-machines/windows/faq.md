---
title: Nejčastější dotazy k virtuálním počítačům s Windows v Azure
description: Obsahuje odpovědi na některé běžné otázky týkající se virtuálních počítačů windows vytvořených pomocí modelu Správce prostředků.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-management
ms.assetid: 757da816-a050-4889-a010-6f75d7978eb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: e3d2673ba52ba0cca36e2a999558313b64716ade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299610"
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Nejčastější dotazy týkající se virtuálních počítačů s Windows
Tento článek řeší některé běžné otázky týkající se virtuálních počítačů Windows vytvořených v Azure pomocí modelu nasazení Správce prostředků. Linuxovou verzi tohoto tématu najdete [v tématu Často kladené otázky týkající se virtuálních počítačů s Linuxem](../linux/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="what-can-i-run-on-an-azure-vm"></a>Co můžu spouštět na virtuálním počítači Azure?
Všichni předplatitelé můžou na virtuálním počítači Azure spouštět serverový software. Informace o zásadách podpory pro spouštění serverového softwaru Microsoft v Azure najdete v tématu [podpora softwaru serveru Microsoft pro virtuální počítače Azure](https://support.microsoft.com/kb/2721672).

Některé verze Windows 7, Windows 8.1 a Windows 10 jsou k dispozici předplatitelům zaměstnaneckých výhod MSDN Azure a předplatitelům s průběžnými platbami s průběžnými platbami msdn a předplatitelům s průběžnými platbami pro vývoj a testování úloh. Podrobnosti, včetně pokynů a omezení, najdete v tématu [Image klienta Windows pro předplatitele MSDN](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Kolik úložiště můžu využít s virtuálním počítačem?
Každý datový disk může být až 32 767 GiB. Počet datových disků, které můžete využít, závisí na velikosti virtuálního počítače. Podrobnosti najdete v článku [Velikosti služeb Virtual Machines](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Spravované disky Azure jsou doporučené nabídky diskového úložiště pro použití s virtuálními počítači Azure pro trvalé ukládání dat. S každým virtuálním počítačem můžete použít více spravovaných disků. Spravované disky nabízejí dva typy odolných možností úložiště: prémiové a standardní spravované disky. Informace o cenách naleznete v [tématu Ceny spravovaných disků](https://azure.microsoft.com/pricing/details/managed-disks).

Účty úložiště Azure můžete také poskytnout úložiště pro disk operačního systému a všechny datové disky. Každý disk je soubor .vhd uložený jako objekt blob stránky. Podrobnosti o cenách najdete v tématu [Podrobnosti o cenách úložiště](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Jak se dostanu k virtuálnímu počítači?
Navázat vzdálené připojení pomocí připojení ke vzdálené ploše (RDP) pro virtuální počítač se systémem Windows. Pokyny najdete v tématu [Jak se připojit a přihlásit k virtuálnímu počítači Azure se systémem Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Jsou podporována maximálně dvě souběžná připojení, pokud není server nakonfigurován jako hostitel relací služby Vzdálená plocha.  

Pokud máte problémy se vzdálenou plochou, [přečtěte si článek Poradce při potížích s připojením ke vzdálené ploše k virtuálnímu počítači Azure založenému na Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Pokud jste obeznámeni s Technologiemi Hyper-V, možná hledáte nástroj podobný službě VMConnect. Azure nenabízí podobný nástroj, protože konzolový přístup k virtuálnímu počítači není podporovaný.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Mohu k ukládání dat použít dočasný disk (disk D: ve výchozím nastavení)?
Nepoužívejte dočasný disk k ukládání dat. Je to pouze dočasné úložiště, takže byste riskovali ztrátu dat, která nelze obnovit. Ke ztrátě dat může dojít, když se virtuální počítač přesune na jiného hostitele. Mezi důvody možného přesunu virtuálního počítače patří změna velikosti virtuálního počítače, aktualizace hostitele nebo selhání hardwaru.

Pokud máte aplikaci, která potřebuje použít písmeno jednotky D:, můžete znovu přiřadit písmena jednotek tak, aby dočasný disk používal něco jiného než D:. Pokyny najdete v článku [Změna písmena jednotky na dočasném disku ve Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Jak můžu změnit písmeno jednotky dočasného disku?
Písmeno jednotky můžete změnit přesunutím stránkovacího souboru a opětovným přiřazením písmen jednotek, ale musíte se ujistit, že kroky provedete v určitém pořadí. Pokyny najdete v článku [Změna písmena jednotky na dočasném disku ve Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Můžu do skupiny dostupnosti přidat existující virtuální hod?
Ne. Pokud chcete, aby váš virtuální počítač byl součástí skupiny dostupnosti, musíte vytvořit virtuální počítač v rámci sady. V současné době neexistuje způsob, jak přidat virtuální ho k sadě dostupnosti po jeho vytvoření.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Můžu do Azure nahrát virtuální počítač?
Ano. Pokyny najdete [v tématu migrace místních virtuálních počítačů do Azure](on-prem-to-azure.md).

## <a name="can-i-resize-the-os-disk"></a>Mohu změnit velikost disku operačního systému?
Ano. Pokyny najdete [v tématu Jak rozbalit jednotku operačního serveru virtuálního počítače ve skupině prostředků Azure](expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Můžu zkopírovat nebo naklonovat existující virtuální počítač Azure?
Ano. Pomocí spravovaných bitových kopií můžete vytvořit bitovou kopii virtuálního počítače a pak ji použít k vytvoření více nových virtuálních počítačů. Pokyny najdete [v tématu Vytvoření vlastní image virtuálního virtuálního montova](tutorial-custom-images.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Proč nevidím oblasti Kanada – střed a Kanada – východ prostřednictvím Správce prostředků Azure?

Dvě nové oblasti Kanada – střed a Kanada – východ se automaticky nezaevidují pro vytváření virtuálních strojů pro stávající předplatná Azure. Tato registrace se provádí automaticky, když se virtuální počítač nasadí přes portál Azure do jakékoli jiné oblasti pomocí Správce prostředků Azure. Po nasazení virtuálního počítače do jakékoli jiné oblasti Azure by nové oblasti měly být dostupné pro další virtuální počítače.

## <a name="does-azure-support-linux-vms"></a>Podporuje Azure virtuální počítače s Linuxem?
Ano. Pokud chcete rychle vytvořit virtuální počítač s Linuxem, který chcete vyzkoušet, přečtěte si hlavní informace [o vytvoření virtuálního počítače s Linuxem v Azure pomocí portálu](../linux/quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Můžu do virtuálního virtuálního sekana po vytvoření přidat nic?
Ano, to je nyní možné. Virtuální město nejprve musí být zastaveno přidělené. Pak můžete přidat nebo odebrat nic (pokud se nejedná o poslední nic na virtuálním počítači). 

## <a name="are-there-any-computer-name-requirements"></a>Existují nějaké požadavky na název počítače?
Ano. Název počítače může být maximálně 15 znaků na délku. Další informace o pojmenování prostředků najdete v [tématu Pravidla a omezení konvencí pojmenování.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)

## <a name="are-there-any-resource-group-name-requirements"></a>Existují nějaké požadavky na název skupiny prostředků?
Ano. Název skupiny prostředků může mít maximálně 90 znaků. Další informace o skupinách prostředků naleznete [v tématu Pravidla a omezení konvencí pojmenování.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming)

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Jaké jsou požadavky na uživatelské jméno při vytváření virtuálního virtuálního soudu?

Uživatelská jména mohou mít délku maximálně 20 znaků a nemohou končit tečkou ("."). 

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


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Jaké jsou požadavky na heslo při vytváření virtuálního počítače?

V závislosti na nástroji, který používáte, jsou různé požadavky na délku hesla:
 - Portál - mezi 12 - 72 znaky
 - PowerShell - mezi 8 - 123 znaků
 - CLI - mezi 12 - 123

* Musí obsahovat malá písmena.
* Musí obsahovat velká písmena.
* Musí obsahovat číslici.
* Musí obsahovat speciální znak (odpovídající regulárnímu výrazu [\W_]).

Následující hesla nejsou povolena:

<table>
    <tr>
        <td>abc@123</td>
        <td>Miluji tě!</td>
        <td>P@$$w0.</td>
        <td>P@ssw0rd</td>
        <td>P@ssword123</td>
    </tr>
    <tr>
        <td>Pa$$word</td>
        <td>pass@word1</td>
        <td>Heslo!</td>
        <td>Heslo1</td>
        <td>Heslo22</td>
    </tr>
</table>