---
title: Nejčastější dotazy týkající se virtuálních počítačů s Windows v Azure
description: Obsahuje odpovědi na některé běžné dotazy týkající se virtuálních počítačů s Windows vytvořených pomocí modelu Správce prostředků.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: 0de25b29dc1e930956c01f342ca2614d1a9082ca
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557501"
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Nejčastější dotazy týkající se Windows Virtual Machines
Tento článek popisuje některé běžné dotazy k virtuálním počítačům s Windows vytvořeným v Azure pomocí modelu nasazení Správce prostředků. Verzi tohoto tématu pro Linux najdete v tématu [Nejčastější dotazy o Linux Virtual Machines](../linux/faq.md).

## <a name="what-can-i-run-on-an-azure-vm"></a>Co můžu spouštět na virtuálním počítači Azure?
Všichni předplatitelé můžou na virtuálním počítači Azure spouštět serverový software. Informace o zásadách podpory pro používání serverového softwaru Microsoftu v Azure najdete v tématu [podpora serverového softwaru Microsoftu pro Azure Virtual Machines](https://support.microsoft.com/kb/2721672).

Některé verze Windows 7, Windows 8.1 a Windows 10 jsou dostupné pro předplatitele zvýhodněné služby MSDN Azure a předplatitele MSDN pro vývoj a testování s průběžnými platbami podle aktuálního využití pro úlohy vývoje a testování. Podrobnosti, včetně pokynů a omezení, najdete v tématu [Image klienta Windows pro předplatitele MSDN](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Kolik úložiště můžu využít s virtuálním počítačem?
Každý datový disk může mít až 32 767 GiB. Počet datových disků, které můžete využít, závisí na velikosti virtuálního počítače. Podrobnosti najdete v článku [Velikosti služeb Virtual Machines](../sizes.md).

Azure Managed Disks jsou doporučené nabídky diskového úložiště pro použití s Azure Virtual Machines k trvalému ukládání dat. Pro každý virtuální počítač můžete použít více Managed Disks. Managed Disks nabízí dva typy trvalých možností úložiště: Premium a Standard Managed Disks. Informace o cenách najdete v tématu [Managed disks ceny](https://azure.microsoft.com/pricing/details/managed-disks).

Účty Azure Storage taky můžou poskytovat úložiště pro disk operačního systému a všechny datové disky. Každý disk je soubor .vhd uložený jako objekt blob stránky. Podrobnosti o cenách najdete v tématu [Podrobnosti o cenách úložiště](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Jak můžu získat přístup k virtuálnímu počítači?
Navažte vzdálené připojení pomocí Připojení ke vzdálené ploše (RDP) pro virtuální počítač s Windows. Pokyny najdete v tématu [jak se připojit a přihlásit se k virtuálnímu počítači Azure s Windows](connect-logon.md). Podporuje se maximálně dvě souběžná připojení, pokud server není nakonfigurovaný jako hostitel relace vzdálené plochy.  

Pokud máte problémy se vzdálenou plochou, přečtěte si téma [řešení potíží s připojením ke vzdálené ploše na virtuálním počítači Azure s Windows](../troubleshooting/troubleshoot-rdp-connection.md?toc=/azure/virtual-machines/windows/toc.json). 

Pokud jste obeznámeni s technologií Hyper-V, možná hledáte nástroj podobný nástroji VMConnect. Azure nenabízí podobný nástroj, protože přístup konzoly k virtuálnímu počítači není podporovaný.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Můžu pro ukládání dat použít dočasný disk (ve výchozím nastavení jednotku D:)?
Nepoužívejte dočasný disk k ukládání dat. Je to jenom dočasné úložiště, takže byste měli riskovat ztráty dat, která se nedají obnovit. Pokud se virtuální počítač přesune na jiného hostitele, může dojít ke ztrátě dat. Mezi důvody možného přesunu virtuálního počítače patří změna velikosti virtuálního počítače, aktualizace hostitele nebo selhání hardwaru.

Pokud máte aplikaci, která potřebuje použít písmeno jednotky D:, můžete znovu přiřadit písmena jednotek, aby dočasný disk používal jinou hodnotu než D:.. Pokyny najdete v článku [Změna písmena jednotky na dočasném disku ve Windows](change-drive-letter.md).


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Jak můžu změnit písmeno jednotky dočasného disku?
Můžete změnit písmeno jednotky přesunutím stránkovacího souboru a opětovným přiřazením písmen jednotek, ale musíte se ujistit, že provedete kroky v určitém pořadí. Pokyny najdete v článku [Změna písmena jednotky na dočasném disku ve Windows](change-drive-letter.md).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Můžu přidat existující virtuální počítač do skupiny dostupnosti?
No. Pokud chcete, aby váš virtuální počítač byl součástí skupiny dostupnosti, musíte vytvořit virtuální počítač v rámci sady. V tuto chvíli není možné přidat virtuální počítač do skupiny dostupnosti po jeho vytvoření.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Můžu nahrát virtuální počítač do Azure?
Ano. Pokyny najdete v tématu [migrace místních virtuálních počítačů do Azure](on-prem-to-azure.md).

## <a name="can-i-resize-the-os-disk"></a>Můžu změnit velikost disku s operačním systémem?
Ano. Pokyny najdete v tématu [postup rozšíření jednotky operačního systému virtuálního počítače ve skupině prostředků Azure](expand-os-disk.md).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Můžu zkopírovat nebo naklonovat stávající virtuální počítač Azure?
Ano. Pomocí spravovaných imagí můžete vytvořit image virtuálního počítače a pak image použít k sestavení několika nových virtuálních počítačů. Pokyny najdete v tématu [Vytvoření vlastní image virtuálního počítače](tutorial-custom-images.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Proč se mi nezobrazuje oblast Kanada – střed a Kanada – východní Azure Resource Manager?

Pro vytváření virtuálních počítačů v existujících předplatných Azure se neregistrují automaticky dvě nové oblasti Kanady – střed a Kanada – východ. Tato registrace se provádí automaticky, když se virtuální počítač nasadí prostřednictvím Azure Portal do jakékoli jiné oblasti pomocí Azure Resource Manager. Po nasazení virtuálního počítače do jakékoli jiné oblasti Azure by měly být nové oblasti dostupné pro další virtuální počítače.

## <a name="does-azure-support-linux-vms"></a>Podporuje Azure virtuální počítače se systémem Linux?
Ano. Postup pro rychlé vytvoření virtuálního počítače se systémem Linux pro vyzkoušení najdete v tématu [Vytvoření virtuálního počítače se systémem Linux v Azure pomocí portálu](../linux/quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Můžu na virtuální počítač přidat síťové rozhraní po jeho vytvoření?
Ano, teď je to možné. Nejdřív je potřeba zastavit navráceného virtuálního počítače. Pak můžete přidat nebo odebrat síťovou kartu (Pokud se nejedná o poslední síťovou kartu na VIRTUÁLNÍm počítači). 

## <a name="are-there-any-computer-name-requirements"></a>Existují nějaké požadavky na název počítače?
Ano. Název počítače může mít délku maximálně 15 znaků. Další informace o pojmenování prostředků najdete v tématu [pravidla a omezení konvence pojmenování](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging) .

## <a name="are-there-any-resource-group-name-requirements"></a>Existují nějaké požadavky na název skupiny prostředků?
Ano. Název skupiny prostředků může mít délku maximálně 90 znaků. Další informace o skupinách prostředků najdete v tématu [pravidla a omezení konvence pojmenování](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming) .

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Jaké jsou požadavky na uživatelské jméno při vytváření virtuálního počítače?

Uživatelské jméno může mít délku maximálně 20 znaků a nesmí končit tečkou ("."). 

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

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Jaké jsou požadavky na heslo při vytváření virtuálního počítače?

Existují různé požadavky na délku hesla, a to v závislosti na použitém nástroji:
 - Portál – mezi 12-72 znaky
 - PowerShell – mezi 8-123 znaky
 - CLI – mezi 12-123

* Musí obsahovat malá písmena.
* Musí obsahovat velká písmena.
* Musí obsahovat číslici.
* Musí obsahovat speciální znak (odpovídající regulárnímu výrazu [\W_]).

Následující hesla nejsou povolena:

<table>
    <tr>
        <td>abc@123</td>
        <td>iloveyou!</td>
        <td>P@ $ $w 0rd</td>
        <td>P@ssw0rd</td>
        <td>P@ssword123</td>
    </tr>
    <tr>
        <td>PA $ $word</td>
        <td>pass@word1</td>
        <td>Zadáno!</td>
        <td>Heslo1</td>
        <td>Password22</td>
    </tr>
</table>