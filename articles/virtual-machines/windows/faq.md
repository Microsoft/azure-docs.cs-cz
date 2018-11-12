---
title: Nejčastější dotazy týkající se virtuálních počítačů s Windows v Azure | Dokumentace Microsoftu
description: Poskytuje odpovědi na některé běžné otázky o virtuálních počítačích s Windows vytvořeného pomocí modelu Resource Manageru.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-management
ms.assetid: 757da816-a050-4889-a010-6f75d7978eb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: cynthn
ms.openlocfilehash: 65d4326763ef9754159e94c9426f3aee69f80ffd
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51253359"
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Časté otázky o Windows Virtual Machines
Tento článek se zabývá některými běžnými dotazy o virtuálních počítačích s Windows, které jsou vytvořené v Azure s využitím modelu nasazení Resource Manager. Linux verzi tohoto tématu naleznete v části [nejčastější dotazy o virtuálních počítačích s Linuxem](../linux/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Co můžu spouštět na virtuálním počítači Azure?
Všichni předplatitelé můžou na virtuálním počítači Azure spouštět serverový software. Informace o zásadách podpory pro provozování softwaru Microsoftu serveru v Azure najdete v tématu [podpora serverového softwaru Microsoftu pro Azure Virtual Machines](https://support.microsoft.com/kb/2721672)

Některé verze Windows 7, Windows 8.1 a Windows 10 jsou dostupné pro předplatitele MSDN Azure benefit a předplatitele MSDN pro účely vývoje a testování s průběžnými platbami, pro vývojová a testovací úlohy. Podrobnosti, včetně pokynů a omezení, najdete v tématu [Image klienta Windows pro předplatitele MSDN](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Kolik úložiště můžu využít s virtuálním počítačem?
Každý datový disk může být až na 4 TB (4 095 GB). Počet datových disků, které můžete využít, závisí na velikosti virtuálního počítače. Podrobnosti najdete v článku [Velikosti služeb Virtual Machines](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Služba Azure Managed Disks se doporučené nabídky diskových úložišť pro použití s Azure Virtual Machines k trvalému ukládání dat. Pro každý virtuální počítač můžete použít několik služeb Managed Disks. Služba Managed Disks nabízí dva typy odolných úložišť: Premium a Standard. Informace o cenách najdete v tématu [spravované disky ceny](https://azure.microsoft.com/pricing/details/managed-disks).

Účty úložiště Azure můžete také poskytují úložiště pro disk s operačním systémem a všechny datové disky. Každý disk je soubor .vhd uložený jako objekt blob stránky. Podrobnosti o cenách najdete v tématu [Podrobnosti o cenách úložiště](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Jak můžete přistupovat k virtuálnímu počítači?
Vytvořit vzdálené připojení pomocí připojení vzdálené plochy (RDP) pro virtuální počítač s Windows. Pokyny najdete v tématu [jak se připojit a přihlaste se na virtuálním počítači Azure s Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Podporují se maximálně dvě souběžná připojení, pokud je server nakonfigurovaný jako hostitel relace vzdálené plochy.  

Pokud máte potíže s pomocí vzdálené plochy, přečtěte si téma [připojení řešení ke vzdálené ploše na základě Windows Azure virtuální počítač](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Pokud už znáte technologii Hyper-V, možná hledáte podobný nástroj jako VMConnect. Azure žádný podobný nástroj nenabízí, protože přístup k virtuálnímu počítači prostřednictvím konzoly nepodporuje.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Můžete použít dočasný disk (jednotku D: ve výchozím nastavení) k ukládání dat?
Nepoužívejte k ukládání dat dočasném disku. Je pouze dočasné úložiště, takže by mohl riskovat ztrátu dat, která není možné obnovit. Při přesunu virtuálního počítače na jiného hostitele, může dojít ke ztrátě dat. Mezi důvody možného přesunu virtuálního počítače patří změna velikosti virtuálního počítače, aktualizace hostitele nebo selhání hardwaru.

Pokud máte aplikaci, kterou je potřeba použít písmeno jednotky D:, můžete změnit tak, aby dočasný disk používá jinou hodnotu než D: přiřazení písmena jednotek. Pokyny najdete v článku [Změna písmena jednotky na dočasném disku ve Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Jak můžu změnit písmeno jednotky dočasného disku?
Písmeno jednotky můžete změnit přesunutím stránkovacího souboru a novým přiřazením písmen jednotek, ale musíte zajistit, aby že provedením kroků v určitém pořadí. Pokyny najdete v článku [Změna písmena jednotky na dočasném disku ve Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Můžete přidat existující virtuální počítač do skupiny dostupnosti?
Ne. Pokud chcete virtuální počítač jako součást skupiny dostupnosti, musíte vytvořit virtuální počítač v rámci sady. Aktuálně není k dispozici způsob, jak přidat do skupiny dostupnosti po vytvoření virtuálního počítače.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Můžete nahrát virtuálního počítače do Azure?
Ano. Pokyny najdete v tématu [migrace místních virtuálních počítačů do Azure](on-prem-to-azure.md).

## <a name="can-i-resize-the-os-disk"></a>Můžete změnit velikost disku operačního systému?
Ano. Pokyny najdete v tématu [popisem postupu rozšíření jednotky operačního systému virtuálního počítače ve skupině prostředků Azure](expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Můžete kopírovat nebo naklonovat existující virtuální počítač Azure?
Ano. Spravované Image můžete vytvořit image virtuálního počítače a pak použít bitovou kopii k sestavení několika nových virtuálních počítačů. Pokyny najdete v tématu [vytvořit vlastní image virtuálního počítače](tutorial-custom-images.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Proč mi nezobrazují Kanada – střed a Kanada – východ oblastí prostřednictvím Azure Resource Manageru?

Tyto dvě nové oblasti, Kanada – střed a Kanada – východ nejsou registrovány automaticky k vytvoření virtuálního počítače pro existující předplatná Azure. Tuto registrace se provádí automaticky při nasazení virtuálního počítače na webu Azure portal do jakékoli jiné oblasti pomocí Azure Resource Manageru. Po nasazení virtuálního počítače do jiné oblasti Azure, třeba tyto nové oblasti k dispozici pro následující virtuální počítače.

## <a name="does-azure-support-linux-vms"></a>Azure podporuje virtuální počítače s Linuxem?
Ano. Rychlé vytvoření virtuálního počítače s Linuxem pro vyzkoušení najdete v tématu [vytvoření virtuálního počítače s Linuxem v Azure pomocí portálu](../linux/quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Mohu přidat síťové rozhraní k virtuálnímu počítači po jeho vytvoření?
Ano, to je nyní možné. Virtuální počítač nejprve musí být zastaven uvolněno. Potom můžete přidat nebo odebrat síťovou kartu (pokud to není poslední síťového rozhraní na virtuálním počítači). 

## <a name="are-there-any-computer-name-requirements"></a>Existují nějaké požadavky na název počítače?
Ano. Název počítače může být maximálně 15 znaků. Zobrazit [konvence pravidla a omezení pojmenování](/azure/architecture/best-practices/naming-conventions#compute) pro více informací o pojmenování prostředků.

## <a name="are-there-any-resource-group-name-requirements"></a>Existují prostředek požadavky na název skupiny?
Ano. Název skupiny prostředků může být maximálně 90 znaků. Zobrazit [konvence pravidla a omezení pojmenování](/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions) Další informace o skupinách prostředků.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Jaké jsou požadavky na uživatelské jméno při vytváření virtuálního počítače?

Uživatelská jména, může být maximálně 20 znaků a nesmí končit tečkou ("."). 


Nejsou povoleny následující uživatelská jména:
<table>
    <tr>
        <td style="text-align:center">1</td><td style="text-align:center">123</td><td style="text-align:center">a</td><td style="text-align:center">actuser</td>
    </tr>
    <tr>
        <td style="text-align:center">ADM –</td><td style="text-align:center">Správce</td><td style="text-align:center">admin1</td><td style="text-align:center">admin2</td>
    </tr>   <tr>
        <td style="text-align:center">Správce</td><td style="text-align:center">ASPNET</td><td style="text-align:center">zálohování</td><td style="text-align:center">konzola</td>
    </tr>
    <tr>
        <td style="text-align:center">David </td><td style="text-align:center">hosta</td><td style="text-align:center">Jan</td><td style="text-align:center">vlastník</td>
    </tr>
    <tr>
        <td style="text-align:center">Kořenové</td><td style="text-align:center">server</td><td style="text-align:center">SQL</td><td style="text-align:center">podpora</td>
    </tr>
    <tr>
        <td style="text-align:center">support_388945a0</td><td style="text-align:center">Sys</td><td style="text-align:center">test</td><td style="text-align:center">test1</td>
    </tr>
    <tr>
        <td style="text-align:center">test2</td><td style="text-align:center">Test3</td><td style="text-align:center">uživatel</td><td style="text-align:center">uživatele User1</td>
    </tr>
    <tr>
        <td style="text-align:center">uživatel2</td><td style="text-align:center">UŽIVATEL3</td><td style="text-align:center">Uživatel4</td><td style="text-align:center">user5</td>
    </tr>
</table>

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Jaké jsou požadavky na heslo při vytváření virtuálního počítače?
Hesla musí mít délku 12 až 123 znaků a musí splňovat 3 ze 4 bezpečnostních požadavků následující:

* Mít nižší znaků
* Horní znaky
* Máte číslice
* Speciální znak (regulární výraz odpovídat [\W_])

Nejsou povoleny následující hesla:

<table>
    <tr>
        <td>abc@123</td>
        <td>ILOVEYOU!</td>
        <td>P@$$w0rd</td>
        <td>P@ssw0rd</td>
        <td>P@ssword123</td>
    </tr>
    <tr>
        <td>Pa$ $word</td>
        <td>pass@word1</td>
        <td>Heslo!</td>
        <td>Heslo1</td>
        <td>Password22</td>
    </tr>
</table>
