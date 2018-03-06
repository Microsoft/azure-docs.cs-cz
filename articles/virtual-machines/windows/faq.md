---
title: "Nejčastější dotazy o virtuálních počítačích Windows v Azure | Microsoft Docs"
description: "Poskytuje odpovědi na některé časté otázky týkající se virtuální počítače s Windows, které jsou vytvořené pomocí modelu Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-management
ms.assetid: 757da816-a050-4889-a010-6f75d7978eb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: cynthn
ms.openlocfilehash: 9aa316e59fb78373cc044f8e18f5d24ea9c5258f
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Časté otázky o virtuálních počítačích s Windows
Tento článek se zaměřuje na některé běžné dotazy týkající se virtuální počítače s Windows, které jsou vytvořené v Azure pomocí modelu nasazení Resource Manager. Linux verzi tohoto tématu naleznete v části [často kladené otázky o virtuálních počítačích s Linuxem](../linux/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Co můžu spouštět na virtuálním počítači Azure?
Všichni předplatitelé můžou na virtuálním počítači Azure spouštět serverový software. Informace o zásadách podpory pro spuštěné serverového softwaru společnosti Microsoft v Azure najdete v tématu [podporu softwaru server společnosti Microsoft pro virtuální počítače Azure](https://support.microsoft.com/kb/2721672)

Některé verze systému Windows 7, Windows 8.1 a Windows 10 jsou k dispozici výhody Odběratelé MSDN Azure a odběratelé MSDN vývoj a testování průběžné platby pro vývoj a testování úlohy. Podrobnosti, včetně pokynů a omezení, najdete v tématu [Image klienta Windows pro předplatitele MSDN](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Kolik úložiště můžu využít s virtuálním počítačem?
Každý datový disk může být až 4 TB (4095 GB). Počet datových disků, které můžete využít, závisí na velikosti virtuálního počítače. Podrobnosti najdete v článku [Velikosti služeb Virtual Machines](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Azure spravované disky jsou doporučené disk úložiště nabídky pro použití s virtuálními počítači Azure pro trvalé úložiště dat. Pro každý virtuální počítač můžete použít několik služeb Managed Disks. Služba Managed Disks nabízí dva typy odolných úložišť: Premium a Standard. Informace o cenách najdete v části [spravované ceny disků](https://azure.microsoft.com/pricing/details/managed-disks).

Účty úložiště Azure můžete také poskytují úložiště pro disk operačního systému a všechny datové disky. Každý disk je soubor .vhd uložený jako objekt blob stránky. Podrobnosti o cenách najdete v tématu [Podrobnosti o cenách úložiště](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Jak lze získat přístup k virtuální počítač?
Vytvořte vzdálené připojení pomocí připojení vzdálené plochy (RDP) pro virtuální počítač s Windows. Pokyny najdete v tématu [jak se připojit a přihlásit se na virtuálním počítači Azure s Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Maximální počet souběžných připojení dvě jsou podporovány, pokud je server nakonfigurovaný jako hostitel relace vzdálené plochy.  

Pokud máte problémy s vzdálené plochy, přečtěte si téma [řešení potíží s vzdálené plochy připojení k systému Windows Azure virtuální počítač](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Pokud už znáte technologii Hyper-V, možná hledáte podobný nástroj jako VMConnect. Azure žádný podobný nástroj nenabízí, protože přístup k virtuálnímu počítači prostřednictvím konzoly nepodporuje.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Můžete použít dočasné disk (disk D: ve výchozím nastavení) k uložení dat?
Nepoužívejte dočasné disku k uložení dat. Je pouze dočasné úložiště, takže by riziko ztráty dat, které nelze obnovit. Může dojít ke ztrátě dat. Pokud se virtuální počítač přesune na jiného hostitele. Mezi důvody možného přesunu virtuálního počítače patří změna velikosti virtuálního počítače, aktualizace hostitele nebo selhání hardwaru.

Pokud máte aplikaci, která je potřeba použít písmeno jednotky D:, můžete opětovně přiřadit písmena jednotek, aby dočasné disk používá něco jiného než D:. Pokyny najdete v článku [Změna písmena jednotky na dočasném disku ve Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Jak můžu změnit písmeno jednotky dočasného disku?
Můžete změnit písmeno jednotky přesunutím stránkovací soubor a nové přiřazení písmena jednotek, ale budete muset udělat, je nutné že provést kroky v určitém pořadí. Pokyny najdete v článku [Změna písmena jednotky na dočasném disku ve Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Můžete přidat existující virtuální počítač na sadu dostupnosti
Ne. Pokud chcete virtuální počítač jako součást skupiny dostupnosti, budete muset vytvořit virtuální počítač v rámci sady. Momentálně není k dispozici způsob, jak přidat virtuální počítač na dostupnosti nastavit po jeho vytvoření.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Můžete nahrát virtuálního počítače do Azure?
Ano. Pokyny najdete v tématu [migrace místní virtuální počítače Azure](on-prem-to-azure.md).

## <a name="can-i-resize-the-os-disk"></a>Můžete změnit velikost disku operačního systému?
Ano. Pokyny najdete v tématu [způsob, jak rozbalit jednotku operačního systému virtuálního počítače ve skupině prostředků Azure](expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Můžete kopírovat nebo klonovat existující virtuální počítač Azure?
Ano. Použití spravovaných obrázků, můžete vytvořit bitovou kopii virtuálního počítače a pak použít bitovou kopii k vytvoření více nové virtuální počítače. Pokyny najdete v tématu [vytvořit vlastní image virtuálního počítače](tutorial-custom-images.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Proč nejsou zobrazeny Kanada centrální a Východní Kanada oblastí prostřednictvím Správce Azure Resource Manager?

Dvě nové oblasti Kanada centrální a Východní Kanada nejsou automaticky registrované pro vytvoření virtuálního počítače pro existující předplatných Azure. Tato registrace se provádí automaticky při nasazení virtuálního počítače prostřednictvím portálu Azure do žádné jiné oblasti pomocí Azure Resource Manager. Po nasazení virtuálního počítače v jiné oblasti Azure nové oblasti musí být k dispozici pro následující virtuální počítače.

## <a name="does-azure-support-linux-vms"></a>Podporuje Azure virtuální počítače s Linuxem?
Ano. Pokud chcete rychle vytvořit virtuální počítač s Linuxem můžete vyzkoušet na, najdete v části [vytvoření virtuálního počítače s Linuxem v Azure pomocí portálu](../linux/quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Mohu přidat síťový adaptér k virtuálnímu počítači po jeho vytvoření?
Ano, to je nyní možné. Virtuální počítač nejdřív je potřeba zastavit deallocated. Potom můžete přidat nebo odebrat síťový adaptér (Pokud je poslední síťový adaptér ve virtuálním počítači). 

## <a name="are-there-any-computer-name-requirements"></a>Existují jakékoli požadavky na název počítače?
Ano. Název počítače nesmí být delší než 15 znaků. V tématu [pojmenování konvence pravidla a omezení](/architecture/best-practices/naming-conventions#naming-rules-and-restrictions?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Další informace ohledně pojmenování vašich prostředků.

## <a name="are-there-any-resource-group-name-requirements"></a>Dochází k jakémukoli prostředku, požadavky na název skupiny?
Ano. Název skupiny prostředků může být maximálně 90 znaků. V tématu [pojmenování konvence pravidla a omezení](/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions) Další informace o skupinách prostředků.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Jaké jsou požadavky na uživatelské jméno při vytváření virtuálního počítače?

Uživatelská jména, může být maximálně 20 znaků a nesmí končit tečkou ("."). 


Následující uživatelská jména nejsou povoleny:
<table>
    <tr>
        <td style="text-align:center">Správce </td><td style="text-align:center"> správce </td><td style="text-align:center"> uživatel </td><td style="text-align:center"> Uživatel1</td>
    </tr>
    <tr>
        <td style="text-align:center">testování </td><td style="text-align:center"> uživatel2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> UŽIVATEL3</td>
    </tr>    <tr>
        <td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> a</td>
    </tr>
    <tr>
        <td style="text-align:center">actuser  </td><td style="text-align:center"> adm </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> aspnet</td>
    </tr>
    <tr>
        <td style="text-align:center">zálohování </td><td style="text-align:center"> Konzola </td><td style="text-align:center"> david </td><td style="text-align:center"> hosta</td>
    </tr>
    <tr>
        <td style="text-align:center">Jan </td><td style="text-align:center"> vlastník </td><td style="text-align:center"> kořenové </td><td style="text-align:center"> server</td>
    </tr>
    <tr>
        <td style="text-align:center">SQL </td><td style="text-align:center"> podpora </td><td style="text-align:center"> support_388945a0 </td><td style="text-align:center"> Sys</td>
    </tr>
    <tr>
        <td style="text-align:center">test2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> Uživatel4 </td><td style="text-align:center"> user5</td>
    </tr>
</table>

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Jaké jsou požadavky na heslo, když vytvoření virtuálního počítače?
Hesla musí být 12 123 znaků a musí splňovat 3 z následujících 4 složitost:

* Mít nižší znaků
* Horní znaky
* Mít číslice.
* Mít speciální znak (regulární výraz odpovídat [\W_])

Nejsou povoleny následující hesla:

<table>
    <tr>
        <td>abc@123 </td>
        <td>P@$$w0rd </td>
        <td>P@ssw0rd </td>
        <td>P@ssword123 </td>
        <td>Pa$ $ aplikace word </td>
    </tr>
    <tr>
        <td>pass@word1 </td>
        <td>Heslo! </td>
        <td>Heslo1 </td>
        <td>Password22 </td>
        <td>ILOVEYOU! </td>
    </tr>
</table>
