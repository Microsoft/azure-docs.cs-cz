---
title: SQL Server na Azure pro Linux Virtual Machines – nejčastější dotazy | Dokumentace Microsoftu
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se systémem SQL Server na virtuálních počítačích Azure s Linuxem.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: fb1cafcf9405576749ea91aeea033c6ee783a026
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330428"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Nejčastější dotazy k SQL serveru v Azure virtuální počítače s Linuxem

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

Tento článek obsahuje odpovědi na některé nejběžnější otázky o spuštění [systému SQL Server v Azure virtuální počítače s Linuxem](sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Tento článek se zaměřuje na problémy, které jsou specifické pro systém SQL Server na virtuálních počítačích s Linuxem. Pokud používáte systém SQL Server na virtuálních počítačích s Windows, přečtěte si článek [nejčastější dotazy k Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Bitové kopie

1. **Jaké Image z Galerie virtuálních počítačů SQL serveru jsou k dispozici?**

   Azure udržuje imagí virtuálních počítačů pro všechny podporované hlavní verze systému SQL Server na všechny edice pro Linux a Windows. Další podrobnosti najdete v tématu úplný seznam [Linuxové Image virtuálních počítačů](sql-server-linux-virtual-machines-overview.md#create) a [Image virtuálních počítačů s Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

1. **Aktualizují se existující Image z Galerie virtuálních počítačů SQL Server?**

   Každé dva měsíce, imagí SQL serveru v galerii virtuálních počítačů se aktualizují pomocí nejnovějších Linuxu a aktualizace Windows. Pro Image Linuxu se to zahrnuje nejnovější aktualizace systému. Pro Image Windows to zahrnuje všechny aktualizace, které jsou označené jako důležité ve službě Windows Update, včetně důležitých aktualizací zabezpečení systému SQL Server a aktualizace service Pack. Kumulativní aktualizace SQL serveru jsou zpracovány jinak pro systémy Linux a Windows. Pro Linux kumulativní aktualizace SQL serveru zahrnuté taky aktualizace. Ale v tuto chvíli nejsou aktualizovány virtuální počítače s Windows pomocí SQL Server nebo Windows Server kumulativní aktualizace.

1. **Co týkající se, že jsou také nainstalované balíčky SQL Server?**

   Balíčky SQL Server, které jsou nainstalované ve výchozím nastavení SQL serveru s linuxem najdete v tématu [nainstalované balíčky](sql-server-linux-virtual-machines-overview.md#packages).

1. **Můžete získat Image virtuálního počítače SQL serveru odebrána z Galerie?**

   Ano. Azure udržuje pouze jednu image pro každou hlavní verze a edice. Po vydání nové aktualizace service pack SQL serveru, Azure přidá novou bitovou kopii do Galerie pro s aktualizací service pack. Image SQL serveru pro předchozí aktualizaci service pack se okamžitě odebere z portálu Azure portal. Je však stále k dispozici pro zřizování z prostředí PowerShell pro následující tři měsíce. Po třech měsících na předchozím obrázku service pack už nejsou k dispozici. Tyto zásady odebrání by platí také v případě dosáhne konci svého životního cyklu se stane Nepodporovaná verze systému SQL Server.

## <a name="creation"></a>Vytvoření

1. **Jak vytvořit virtuální počítač Azure pro Linux s využitím SQL serveru?**

   Nejjednodušším řešením je vytvoření virtuálního počítače s Linuxem, který obsahuje SQL Server. Kurz týkající se registrací do Azure a vytvoření virtuálního počítače s SQL z portálu, najdete v tématu [zřízení virtuálního počítače s Linuxem a SQL serverem na webu Azure Portal](provision-sql-server-linux-virtual-machine.md). Máte také možnost ručně nainstalujte SQL Server na virtuálním počítači s jedním volně licencovanou verzi (Developer a Express) nebo opětovným použitím licenci místní. Pokud je používání vlastní licence, musíte mít [mobilitu licencí v rámci programu Software Assurance na Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **Proč nejde zřídit s RHEL nebo SLES virtuálního počítače s SQL serverem s předplatným Azure, které má nastavený limit útraty?**

   Službu virtual machines RHEL a SLES vyžaduje předplatné bez omezení výdajů a ověřený způsob platby metody (obvykle platební karty) přidružené k předplatnému. Pokud zřizujete s RHEL nebo SLES virtuálního počítače bez odebrání limitu útraty, vaše předplatné bude zakázán a zastavit všechny virtuální počítače nebo služby. Pokud narazíte na tento stav, pokud chcete předplatné znovu aktivovat [odebrat limit útraty](https://account.windowsazure.com/subscriptions). Zbývající kredit se obnoví do aktuálního fakturačního cyklu, ale příplatek image RHEL nebo SLES virtuálního počítače přejde z vaší platební karty, pokud se rozhodnete znovu spustí a dál používat, musíte.

## <a name="licensing"></a>Licencování

1. **Jak mohu nainstalovat licencovanou kopii systému SQL Server na Virtuálním počítači Azure?**

   Nejprve vytvořte pouze pro operační systém virtuálního počítače s Linuxem. Spusťte [pokynů k instalaci systému SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) pro vaši Linuxovou distribuci. Pokud instalujete některou z volně licencovaný edicí systému SQL Server, musíte mít také licenci systému SQL Server a [mobilitu licencí v rámci programu Software Assurance na Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Existují Bring Your-používání vlastní licence (BYOL) Linuxové Image virtuálních počítačů pro SQL Server?**

   V tuto chvíli nejsou k dispozici žádné BYOL Linuxové Image virtuálních počítačů pro SQL Server. Však můžete ručně nainstalovat SQL Server na virtuálním počítači pouze pro Linux jak je popsáno v předchozí otázky.

1. **Můžete změnit virtuálního počítače používat vlastní licenci na SQL Server, pokud byl vytvořen z některou k imagí s průběžnými platbami Galerie?**

   Ne. Není možné přejít z licencí platit za sekundu na používání vlastní licence. Musíte vytvořit nový virtuální počítač s Linuxem, instalaci systému SQL Server a migrovat data. Viz předchozí otázce další podrobnosti o používáním vlastní licence.

## <a name="administration"></a>Správa

1. **Můžete spravovat virtuální počítač Linux SQL Server s SQL Server Management Studio (SSMS)?**

   Ano, ale aplikace SSMS je aktuálně nástroj jen pro Windows. Musíte se připojit vzdáleně z počítače s Windows pomocí aplikace SSMS virtuální počítače s Linuxem SQL serveru. Místně v Linuxu nové [mssql conf.](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf) nástroje můžete provádět mnoho úloh správy. Nástroj pro správu databáze pro více platforem, naleznete v tématu [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is).

1. **Můžu odebrat systému SQL Server zcela z virtuálního počítače s SQL?**

   Ano, ale bude nadále účtovat virtuálního počítače s SQL, jak je popsáno v [doprovodné materiály k pro virtuální počítače Azure s SQL serverem cenám](../../windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json). Pokud už nepotřebujete systému SQL Server, můžete nasadit nový virtuální počítač a migraci dat a aplikací do nového virtuálního počítače. Pak můžete odebrat virtuální počítač s SQL serverem.

## <a name="updating-and-patching"></a>Aktualizace a opravy chyb

1. **Jak upgradovat na novou verzi nebo edici SQL serveru na Virtuálním počítači Azure?**

   V současné době neexistuje žádný místní upgrade SQL serveru běžícího na Virtuálním počítači Azure. Vytvořit nový virtuální počítač Azure s požadovanou verzí/edicí systému SQL Server, a potom migrovat databáze do nového serveru pomocí [standardních technik migrace dat](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview).

## <a name="general"></a>Obecné

1. **Podporuje řešení vysoké dostupnosti pro SQL Server na virtuálních počítačích Azure?**

   V tuto chvíli to není možné. Skupiny dostupnosti Always On i Clustering převzetí služeb při selhání vyžadují clustering řešení v systému Linux, jako je například Pacemaker. Podporované distribuce systému Linux pro SQL Server nepodporují jejich doplňky vysoké dostupnosti v cloudu.

## <a name="resources"></a>Zdroje a prostředky

**Virtuální počítače s Linuxem**:

* [Přehled SQL serveru na virtuálním počítači s Linuxem](sql-server-linux-virtual-machines-overview.md)
* [Zřízení virtuálního počítače s SQL Server Linux](provision-sql-server-linux-virtual-machine.md)
* [Dokumentace k SQL Serveru na Linuxu](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Virtuální počítače s Windows**:

* [Přehled SQL serveru na virtuálním počítači Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Zřídit Windows SQL Server VM](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Nejčastější dotazy (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)