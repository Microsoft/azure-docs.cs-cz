---
title: SQL Server na virtuálních počítačích Linux Azure – nejčastější dotazy | Microsoft Docs
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se systémem SQL Server na virtuálních počítačích Azure Linux.
services: virtual-machines-linux
documentationcenter: ''
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: jroth
ms.openlocfilehash: 56a0629249cdb5f0f098d2b7b6d36b3fbb215009
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30190859"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Nejčastější dotazy pro SQL Server na virtuálních počítačích Linux Azure

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

Tento článek obsahuje odpovědi na některé nejčastější dotazy o spuštění [systému SQL Server na virtuálních počítačích Linux Azure](sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Tento článek se zaměřuje na problémy, které jsou specifické pro SQL Server na virtuální počítače s Linuxem. Pokud používáte systém SQL Server na virtuálních počítačích Windows, najdete v článku [Windows nejčastější dotazy k](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Bitové kopie

1. **Jaké Image Galerie virtuálních počítačů systému SQL Server jsou k dispozici?**

   Azure udržuje bitové kopie virtuálních počítačů pro všechny podporované hlavní verze systému SQL Server ve všech edicích pro Linux a Windows. Další podrobnosti najdete v tématu úplný seznam [bitové kopie virtuálního počítače s Linuxem](sql-server-linux-virtual-machines-overview.md#create) a [Image virtuálních počítačů Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

1. **Jsou aktualizovány stávající Image Galerie virtuálních počítačů systému SQL Server?**

   Každé dva měsíce, bitové kopie systému SQL Server v galerii virtuálních počítačů jsou aktualizované o nejnovější Linux a aktualizace systému Windows. Pro Linux Image to zahrnuje nejnovější aktualizace systému. Pro bitové kopie systému Windows to zahrnuje všechny aktualizace, které jsou označené jako důležité ve službě Windows Update, včetně důležitých aktualizací zabezpečení systému SQL Server a aktualizace service Pack. Kumulativní aktualizace systému SQL Server jsou zpracovány jinak pro Linux a Windows. Pro systémy Linux kumulativní aktualizace systému SQL Server také zahrnuty v aktualizaci. Ale v tuto chvíli se neaktualizují virtuálních počítačů Windows s kumulativní aktualizací systému SQL Server nebo Windows Server.

1. **Co související, jestli jsou také nainstalované balíčky systému SQL Server?**

   Balíčky systému SQL Server, které jsou nainstalované ve výchozím nastavení u virtuálních počítačů Linux SQL serveru najdete v sekci [nainstalované balíky](sql-server-linux-virtual-machines-overview.md#packages).

1. **Můžete získat z galerie odebrat bitové kopie virtuálních počítačů systému SQL Server?**

   Ano. Azure udržuje jenom jednu image na hlavní verze a edice. Po vydání nové aktualizace service pack systému SQL Server, Azure přidá novou bitovou kopii do Galerie této aktualizace service Pack. Bitová kopie systému SQL Server pro předchozí aktualizaci service pack je okamžitě odstraněna z portálu Azure. Je však stále k dispozici pro zřizování z prostředí PowerShell pro další tři měsíce. Po dobu tří měsíců předchozí obrázek service pack již není k dispozici. Tato zásada odebrání by platí také v případě změní Nepodporovaná verze systému SQL Server, pokud ukončení životního cyklu.

## <a name="creation"></a>Vytvoření

1. **Vytvoření virtuálního počítače Linux Azure s SQL serverem**

   Nejsnazším řešením je vytvoření virtuální počítač s Linuxem, který zahrnuje SQL Server. Kurz týkající se registrace do služby Azure a vytvoření virtuálního počítače s SQL z portálu, najdete v části [zřízení virtuálního počítače s Linuxem SQL Server na webu Azure portal](provision-sql-server-linux-virtual-machine.md). Máte také možnost ručně instalace systému SQL Server na virtuálním počítači pomocí volně licencovanou verzi (vývojáře nebo Express) nebo opětovným použitím licenci na místě. Pokud jste přineste si vlastní licenci, musíte mít [mobilitou licencí v rámci programu Software Assurance na Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **Proč nelze zřídit systémem RHEL nebo virtuální počítač SLES SQL Server má předplatné Azure, který má limitu útraty a automaticky?**

   RHEL a SLES virtuální počítače vyžadují předplatné se žádné limitu útraty a způsob ověřené platby (obvykle platební karty) přidružené k předplatnému. Pokud zřizujete systémem RHEL nebo SLES virtuálních počítačů bez odebrání limitu útraty, předplatné bude získat zakázané a zastaveny všechny virtuální počítače nebo služby. Pokud narazíte na tomto stavu, chcete-li znovu povolit předplatné [odeberte limit útraty](https://account.windowsazure.com/subscriptions). Váš zbývající kredit se obnoví na aktuální fakturační cyklus, ale příplatek bitové kopie virtuálního počítače SLES nebo RHEL přejde proti platební karty, pokud zvolíte možnost znovu spustit a spusťte jej.

## <a name="licensing"></a>Správa licencí

1. **Jak můžete instalovat Moje licencovanou kopii systému SQL Server na virtuální počítač Azure?**

   Nejprve vytvořte Linux jen operačního systému virtuálního počítače. Spusťte [kroky instalace systému SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) pro distribuční Linux. Pokud instalujete jednu z volně licencovanou edicí systému SQL Server, musí mít licenci systému SQL Server a [mobilitou licencí v rámci programu Software Assurance na Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Existují bitové kopie virtuálních počítačů Linux Bring-Your-vlastní-licenci (BYOL) pro systém SQL Server?**

   V současné době neexistují žádné bitové kopie virtuálních počítačů BYOL Linux pro SQL Server. Však můžete ručně nainstalovat SQL Server na virtuálním počítači pouze Linux popsané v předchozí dotazy.

1. **Můžete změnit virtuálního počítače použít vlastní licenci na SQL Server, pokud byl vytvořen z jedné z bitových kopií průběžnými platbami Galerie?**

   Ne. Nelze přepnout z platím za sekundu licencí k používání vlastní licence. Musíte vytvořit nový virtuální počítač s Linuxem, instalaci systému SQL Server a přenést data. Přejděte k předchozí otázce další podrobnosti o přinesou vlastní licenci.

## <a name="administration"></a>Správa

1. **Můžete spravovat virtuální počítače s SQL serverem Linux s SQL Server Management Studio (SSMS)?**

   Ano, ale aplikace SSMS je aktuálně nástroj pouze pro systém Windows. Je nutné připojit vzdáleně z počítače s Windows pro použití aplikace SSMS s virtuální počítače s Linuxem SQL Server. Místně v systému Linux nové [mssql-conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf) nástroj může provádět mnoho úloh správy. Zobrazte náhled nástroj pro správu databáze a platformy, najdete v části [SQL Server Operations Studio (preview)](https://docs.microsoft.com/sql/sql-operations-studio/what-is).

1. **Lze odebrat systému SQL Server úplně z virtuálního počítače s SQL?**

   Ano, ale bude dál účtovat pro virtuální počítač SQL, jak je popsáno v [ceny pokyny pro virtuální počítače Azure SQL Server](../../windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json). Pokud již nepotřebujete systému SQL Server, můžete nasadit nového virtuálního počítače a migraci dat a aplikací do nového virtuálního počítače. Potom můžete odebrat virtuální počítač systému SQL Server.

## <a name="updating-and-patching"></a>Aktualizace a opravy chyb

1. **Jak upgradovat na novou verzi nebo edici systému SQL Server ve virtuálním počítači Azure?**

   V současné době není k dispozici žádné místní upgrade pro SQL Server běžící ve virtuálním počítači Azure. Vytvořit nový virtuální počítač Azure s požadovanou verzi nebo edici systému SQL Server, a potom migrovat své databáze na nový server pomocí [standardní data migrace techniky](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview).

## <a name="general"></a>Obecné

1. **Podporuje řešení vysoké dostupnosti SQL serveru na virtuálních počítačích Azure?**

   V tuto chvíli to není možné. Skupiny dostupnosti Always On i Clustering převzetí služeb při selhání vyžadují clustering řešení v systému Linux, jako je například kardiostimulátor. Podporované distribuce systému Linux pro SQL Server nepodporují jejich doplňky vysoké dostupnosti v cloudu.

## <a name="resources"></a>Zdroje a prostředky

**Virtuální počítače s Linuxem**:

* [Přehled systému SQL Server na virtuální počítač s Linuxem](sql-server-linux-virtual-machines-overview.md)
* [Zřízení virtuálního počítače s Linuxem SQL serveru](provision-sql-server-linux-virtual-machine.md)
* [SQL Server na dokumentaci k systému Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Virtuální počítače Windows**:

* [Přehled systému SQL Server v systému Windows virtuálního počítače](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Zřízení virtuálního počítače systému SQL Server Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Nejčastější dotazy (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)