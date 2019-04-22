---
title: SQL Server na virtuálních počítačích Windows v Azure – nejčastější dotazy | Dokumentace Microsoftu
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se systémem SQL Server na virtuálních počítačích Azure.
services: virtual-machines-windows
documentationcenter: ''
author: v-shysun
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: v-shysun
ms.openlocfilehash: 5299437dea18510fa5f85ee27240c8afc434d125
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680800"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Nejčastější dotazy ke službě SQL Server běžící na virtuálních počítačích Windows v Azure

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Tento článek obsahuje odpovědi na některé nejběžnější otázky o spuštění [SQL serveru na Windows Virtual Machines v Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> Tento článek se zaměřuje na problémy, které jsou specifické pro SQL Server na virtuálních počítačích s Windows. Pokud používáte systém SQL Server na virtuálních počítačích s Linuxem, najdete v článku [Linuxu – nejčastější dotazy](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Bitové kopie

1. **Jaké Image z Galerie virtuálních počítačů SQL serveru jsou k dispozici?**

   Azure udržuje imagí virtuálních počítačů pro všechny podporované hlavní verze systému SQL Server ve všech edicích Windows a Linux. Další informace najdete v tématu úplný seznam [Image virtuálních počítačů s Windows](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) a [Linuxové Image virtuálních počítačů](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create).

1. **Aktualizují se existující Image z Galerie virtuálních počítačů SQL Server?**

   Každé dva měsíce, imagí SQL serveru v galerii virtuálních počítačů se aktualizují pomocí nejnovějších Windows a Linuxem aktualizuje. Pro Image Windows to zahrnuje všechny aktualizace, které jsou označené jako důležité ve službě Windows Update, včetně důležitých aktualizací zabezpečení systému SQL Server a aktualizace service Pack. Pro Image Linuxu se to zahrnuje nejnovější aktualizace systému. Kumulativní aktualizace SQL serveru jsou zpracovány jinak pro systémy Linux a Windows. Pro Linux kumulativní aktualizace SQL serveru zahrnuté taky aktualizace. Ale v tuto chvíli nejsou aktualizovány virtuální počítače s Windows pomocí SQL Server nebo Windows Server kumulativní aktualizace.

1. **Můžete získat Image virtuálního počítače SQL serveru odebrána z Galerie?**

   Ano. Azure udržuje pouze jednu image pro každou hlavní verze a edice. Po vydání nové aktualizace service pack SQL serveru, Azure přidá novou bitovou kopii do Galerie pro s aktualizací service pack. Image SQL serveru pro předchozí aktualizaci service pack se okamžitě odebere z portálu Azure portal. Je však stále k dispozici pro zřizování z prostředí PowerShell pro následující tři měsíce. Po třech měsících na předchozím obrázku service pack už nejsou k dispozici. Tyto zásady odebrání by platí také v případě dosáhne konci svého životního cyklu se stane Nepodporovaná verze systému SQL Server.


1. **Je možné nasadit starších image SQL serveru, který se nezobrazuje na portálu Azure portal?**

   Ano, s použitím prostředí PowerShell. Další informace o nasazování virtuálních počítačů SQL Server pomocí Powershellu najdete v tématu [jak zřídit virtuální počítače systému SQL Server v prostředí Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

1. **Můžete vytvořit image virtuálního pevného disku z virtuálního počítače s SQL serverem?**

   Ano, ale zde je několik důležitých informací. Je-li nasadit tento virtuální pevný disk do nového virtuálního počítače v Azure, se nezobrazí část konfigurace systému SQL Server na portálu. Pak musíte spravovat možnosti konfigurace serveru SQL Server pomocí Powershellu. Navíc vám bude účtovat sazbou původně podle vaší image virtuálního počítače SQL. To platí i v případě systému SQL Server odeberete z virtuálního pevného disku před nasazením. 

1. **Je možné nastavit konfigurace není zobrazené v galerii virtuálních počítačů (pro příklad Windows 2008 R2 a SQL Server 2012)?**

   Ne. Image Galerie virtuálních počítačů, které patří SQL Server, musíte vybrat některou ze zadané imagí na webu Azure portal nebo prostřednictvím [Powershellu](virtual-machines-windows-ps-sql-create.md). 


## <a name="creation"></a>Vytvoření

1. **Jak vytvořit virtuální počítač Azure s SQL serverem**

   Nejjednodušším řešením je vytvoření virtuálního počítače, který obsahuje SQL Server. Kurz týkající se registrací do Azure a vytvoření virtuálního počítače s SQL z portálu, najdete v tématu [zřízení virtuálního počítače s SQL serverem na webu Azure Portal](virtual-machines-windows-portal-sql-server-provision.md). Můžete vybrat image virtuálního počítače, který používá licencování SQL serveru s platbami za sekundu, nebo můžete použít bitovou kopii, která umožňuje používat vlastní licenci systému SQL Server. Máte také možnost ručně nainstalujte SQL Server na virtuálním počítači s jedním volně licencovanou verzi (Developer a Express) nebo opětovným použitím licenci místní. Pokud je používání vlastní licence, musíte mít [mobilitu licencí v rámci programu Software Assurance na Azure](https://azure.microsoft.com/pricing/license-mobility/). Další informace najdete v tématu [Doprovodné materiály k cenám pro virtuální počítače Azure s SQL Serverem](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Jak můžu migrovat místní databázi systému SQL Server do cloudu?**

   Vytvoření virtuálního počítače Azure s instancí SQL serveru. Potom migrace místních databází do této instance. Strategie migrace dat, naleznete v tématu [migrace databáze SQL serveru na SQL Server na Virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licencování

1. **Jak můžu na virtuální počítač Azure nainstalovat licencovanou kopii SQL Serveru?**

   Můžete to provést dvěma způsoby. Můžete zřídit některou z [imagí virtuálního počítače s podporou licencí](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), což se také označuje jako model používání vlastní licence (BYOL). Další možností je zkopírovat instalační médium SQL Serveru na virtuální počítač s Windows Serverem a pak na tomto virtuálním počítači nainstalovat SQL Server. Pokud však nainstalujete SQL Server ručně, připravíte se o integraci portálu a podporu rozšíření agenta SQL Server IaaS, takže funkce jako automatizované zálohování a automatizované opravy v tomto scénáři nebudou fungovat. Z tohoto důvodu doporučujeme použít některou z imagí BYOL z galerie. Pokud chcete použít BYOL nebo média systému SQL Server na Virtuálním počítači Azure, musíte mít [mobilitu licencí v rámci programu Software Assurance na Azure](https://azure.microsoft.com/pricing/license-mobility/). Další informace najdete v tématu [Doprovodné materiály k cenám pro virtuální počítače Azure s SQL Serverem](virtual-machines-windows-sql-server-pricing-guidance.md).


1. **Musím platit za licenci SQL Serveru na virtuálním počítači Azure, pokud se používá pouze jako pohotovostní nebo pro převzetí služeb při selhání?**

   Pokud máte Software Assurance a využít mobilitu licencí, jak je popsáno v [virtuálního počítače nejčastější dotazy ohledně licencování](https://azure.microsoft.com/pricing/licensing-faq/), pak nemusíte zaplatit licenční Server SQL účasti jako pasivní sekundární replika v nasazení vysokou DOSTUPNOSTÍ. Jinak za jeho licenci platit musíte.

1. **Můžu změnit virtuální počítač tak, aby používal vlastní licenci SQL Serveru, pokud byl vytvořený z některé z imagí z galerie s průběžnými platbami?**

   Ano. Můžete snadno přesouvat přesunout mezi těmito dvěma licenční modely, pokud jste původně spustili pomocí image galerie s průběžnými platbami. Pokud jste však původně začali s imagí BYOL, nemůžete licenci přepnout na průběžné platby. Další informace najdete v tématu [jak chcete-li změnit licenční model virtuálního počítače s SQL serverem](virtual-machines-windows-sql-ahb.md).

   > [!Note]
   > V současné době je k dispozici jenom pro zákazníky veřejného cloudu Azure.

1. **Mám k vytvoření nového virtuálního počítače SQL použít image BYOL nebo poskytovatele prostředků virtuálního počítače SQL?**

   Image BYOL (Používání vlastní licence) jsou k dispozici pouze pro zákazníky se smlouvou Enterprise. Další zákazníky programu Software Assurance používali poskytovatele prostředků virtuálního počítače s SQL k vytvoření virtuálního počítače SQL s [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/licensing-faq/). 

1. **Způsobí přepnutí modelů licencování výpadek SQL Serveru?**

   Ne. [Změna licenční model](virtual-machines-windows-sql-ahb.md) nevyžaduje žádné výpadky pro SQL Server, jak tato změna je hned platná a nevyžaduje restartování virtuálního počítače. Nicméně, k registraci virtuálního počítače s SQL serverem s poskytovatelem prostředků pro virtuální počítač s SQL [rozšíření SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md) je předpokladem a instalace rozšíření SQL IaaS restartuje službu systému SQL Server. Proto pokud je potřeba nainstalovat rozšíření SQL IaaS, mělo by se to provést během údržby. 

1. **Program Azure Hybrid Benefit aktivovat předplatných CSP?**

   Ano, je dostupná pro předplatná CSP zvýhodněné hybridní využití Azure. Zákazníci CSP by měly nejprve nasadit bitovou kopii s průběžnými platbami a potom [změnit licenční model](virtual-machines-windows-sql-ahb.md) k používání – vlastní licence.  

1. **Jsou s registrací virtuálního počítače u nového poskytovatele prostředků virtuálního počítače SQL spojené další náklady?**

   Ne. Poskytovatel prostředků virtuálního počítače SQL pouze poskytuje další možnosti správy SQL Serveru na virtuálním počítači Azure bez dalších poplatků. 

1. **Je poskytovatel prostředků virtuálního počítače SQL dostupný pro všechny zákazníky?**
 
   Ano. Všichni zákazníci se můžou zaregistrovat u nového poskytovatele prostředků virtuálního počítače SQL. Ale můžete jenom zákazníci s programem Software Assurance Benefit aktivovat [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) (byol) na virtuální počítač s SQL serverem. 

1. **Co se stane _Microsoft.SqlVirtualMachine_ prostředku, pokud je přesunut nebo vyřadit prostředku virtuálního počítače?** 

   Pokud je prostředků Microsoft.Compute/VirtualMachine vyřadit nebo přesunout, pak je přidružený prostředek Microsoft.SqlVirtualMachine upozornění, že má asynchronní replikace operaci.

1. **Co se stane k virtuálnímu počítači, pokud _Microsoft.SqlVirtualMachine_ vyřadit prostředků?**

    Prostředek Microsoft.Compute/VirtualMachine nemá žádný vliv při přetažení Microsoft.SqlVirtualMachine prostředků. Licencování změní se však výchozí zpět na původní zdroj obrázku. 

1. **Je možné u poskytovatele prostředků virtuálního počítače SQL zaregistrovat samostatně nasazené virtuální počítače s SQL Serverem?**

    Ano. Pokud nasazení SQL serveru z vlastní média a instalaci rozšíření SQL IaaS, virtuální počítač s SQL serverem můžete zaregistrovat u poskytovatele prostředků zobrazíte možnosti správy výhody poskytované modulem rozšíření SQL IaaS. Samostatně nasazený virtuální počítač SQL však nemůžete převést na průběžné platby.

## <a name="administration"></a>Správa

1. **Můžu na stejný virtuální počítač nainstalovat druhou instanci SQL Serveru? Můžu změnit nainstalované funkce výchozí instance?**

   Ano. Instalační médium systému SQL Server nachází ve složce na **C** jednotky. Spustit **Setup.exe** z tohoto umístění pro přidání nové instance systému SQL Server nebo na změnu jiné nainstalované funkce SQL serveru na počítači. Všimněte si, že některé funkce, jako je například automatizovaného zálohování, automatické opravy a integrace Azure Key Vaultu, pracovat pouze s výchozí instancí, nebo pojmenovanou instanci, která byla správně nakonfigurována (viz otázku 3). 

1. **Můžu odinstalovat výchozí instanci SQL Serveru?**

   Ano, měli byste však vzít v úvahu několik skutečností. Jak je uvedeno v předchozí odpověď, jsou funkce, které využívají [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).  Pokud odinstalujete bez odebrání rozšíření IaaS také výchozí instance, podívejte se i nadále rozšíření a může způsobit chyby v protokolu událostí. Tyto chyby pocházejí z následujících dvou zdrojů: **Správa přihlašovacích údajů služby Microsoft SQL Server** a **Agent serveru Microsoft SQL Server IaaS**. Následuje příklad jedné z chyb:

      Při navazování připojení k SQL Serveru došlo k chybě související se sítí nebo konkrétní instancí. Server se nenašel nebo nebyl dostupný.

   Pokud se rozhodnete odinstalovat výchozí instance, odinstalovat také [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) také.

1. **Můžete použít pojmenovanou instanci systému SQL Server s příponou IaaS**?
   
   Ano, pokud pojmenovaná instance je jedinou instanci systému SQL Server, a pokud byl původní výchozí instanci [odinstalováno](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md#installation). Pokud není žádná výchozí instance a existuje víc instancí s názvem na jeden virtuální počítač SQL serveru, rozšíření IaaS se nepodaří nainstalovat. 

1. **Můžu z virtuálního počítače SQL zcela odebrat SQL Server?**

   Ano, ale bude nadále účtovat virtuálního počítače s SQL, jak je popsáno v [doprovodné materiály k pro virtuální počítače Azure s SQL serverem cenám](virtual-machines-windows-sql-server-pricing-guidance.md). Pokud už SQL Server nepotřebujete, můžete nasadit nový virtuální počítač a migrovat na něj data a aplikace. Pak můžete odebrat virtuální počítač s SQL Serverem.
   
## <a name="updating-and-patching"></a>Aktualizace a opravy chyb

1. **Jak změnit na novou verzi nebo edici systému SQL Server na Virtuálním počítači Azure?**

   Zákazníci s programem Software Assurance budou moct místní upgrady z jejich SQL serveru běžícího na Virtuálním počítači Azure pomocí instalačního média na portálu Volume Licensing. Ale v současné době neexistuje žádný způsob, jak změnit edici instance systému SQL Server. Vytvořit nový virtuální počítač Azure s požadovanou edici systému SQL Server, a potom migrovat databáze do nového serveru pomocí standardní [technik migrace dat](virtual-machines-windows-migrate-sql.md).

1. **Jak se aktualizace a aktualizace service Pack používají na virtuální počítač s SQL serverem?**

   Virtuální počítače umožňují kontrolu hostitelského počítače včetně doby a způsobu použití aktualizací. Pro operační systém, které můžete aktualizace oken aplikovat ručně nebo můžete povolit službu plánování nazvanou [automatizované opravy](virtual-machines-windows-sql-automated-patching.md). Automatizované opravy nainstalují jakékoli aktualizace, které jsou označené jako důležité, včetně aktualizací SQL Serveru v této kategorii. Ostatní volitelné aktualizace SQL Server se musí instalovat ručně.

## <a name="general"></a>Obecné

1. **Podporují se SQL Server Failover Cluster instance (FCI) na virtuálních počítačích Azure?**

   Ano. Je možné [vytvořit Cluster převzetí služeb při selhání Windows ve Windows serveru 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) a používat úložiště prostorů s přímým přístupem (S2D) pro úložiště clusteru. Alternativně můžete použít clustering nebo úložiště řešení třetích stran jak je popsáno v [vysokou dostupnost a zotavení po havárii pro SQL Server ve službě Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > V tuto chvíli [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) není podporována pro FCI Server SQL v Azure. Doporučujeme odinstalovat rozšíření z virtuálních počítačů, které jsou součástí FCI. Toto rozšíření podporuje funkce, jako je automatické zálohování a opravy a některé funkce portálu pro SQL. Tato funkce nebude fungovat pro virtuální počítače s SQL po daný agent nebude odinstalován.

1. **Jaký je rozdíl mezi virtuálními počítači SQL a službu SQL Database?**

   Koncepčně systémem SQL Server na virtuálním počítači Azure, ne, který se liší od systémem SQL Server ve vzdáleném datovém centru. Naproti tomu [SQL Database](../../../sql-database/sql-database-technical-overview.md) nabízí database-as-a-service. SQL Database nemáte přístup k počítačům, které jsou hostiteli databází. Úplné porovnání najdete v tématu [volba cloudového řešení systému SQL Server: Azure SQL (PaaS) Database nebo SQL Server na virtuálních počítačích Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Jak nainstalovat nástroje SQL Data na mém virtuálním počítači Azure?**

    Stáhněte a nainstalujte nástroje SQL Data z [Microsoft SQL Server Data Tools – Business Intelligence pro Visual Studio 2013](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

1. **Se distribuované transakce MSDTC nepodporuje na virtuální počítače s SQL serverem?**
   
    Ano. Místní služby DTC je podporována pro SQL Server 2016 SP2 a vyšší. Aplikace musí provést však test při použití skupin dostupnosti AlwaysOn, jako transakce vydávaných za pochodu při selhání se nezdaří a je nutné zopakovat. Clusterové služby DTC je k dispozici od verze Windows serveru 2019. 

## <a name="resources"></a>Zdroje a prostředky

**Virtuální počítače s Windows**:

* [Přehled SQL serveru na virtuálním počítači Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Zřídit Windows SQL Server VM](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrace databáze systému SQL Server na Virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md)
* [Vysoká dostupnost a zotavení po havárii pro SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Osvědčené postupy z hlediska výkonu pro SQL Server na Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Modely aplikací a vývojové strategie pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Virtuální počítače s Linuxem**:

* [Přehled SQL serveru na virtuálním počítači s Linuxem](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Zřízení virtuálního počítače s SQL Server Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Nejčastější dotazy (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Dokumentace k SQL Serveru na Linuxu](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
