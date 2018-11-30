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
ms.openlocfilehash: edfd2e9e03aefa4833c8472a43d4857f08b95780
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495476"
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

1. **Můžete vytvořit image virtuálního pevného disku z virtuálního počítače s SQL serverem?**

   Ano, ale zde je několik důležitých informací. Je-li nasadit tento virtuální pevný disk do nového virtuálního počítače v Azure, provedete není ge část konfigurace systému SQL Server na portálu. Pak musíte spravovat možnosti konfigurace serveru SQL Server pomocí Powershellu. Navíc vám bude účtovat sazbou původně podle vaší image virtuálního počítače SQL. To platí i v případě systému SQL Server odeberete z virtuálního pevného disku před nasazením. 

1. **Je možné nastavit konfigurace není zobrazené v galerii virtuálních počítačů (pro příklad Windows 2008 R2 a SQL Server 2012)?**

   Ne. Pro Image Galerie virtuálních počítačů, které patří SQL Server musíte vybrat jednu z zadané bitové kopie.

## <a name="creation"></a>Vytvoření

1. **Jak vytvořit virtuální počítač Azure s SQL serverem**

   Nejjednodušším řešením je vytvoření virtuálního počítače, který obsahuje SQL Server. Kurz týkající se registrací do Azure a vytvoření virtuálního počítače s SQL z portálu, najdete v tématu [zřízení virtuálního počítače s SQL serverem na webu Azure Portal](virtual-machines-windows-portal-sql-server-provision.md). Můžete vybrat image virtuálního počítače, který používá licencování SQL serveru s platbami za sekundu, nebo můžete použít bitovou kopii, která umožňuje používat vlastní licenci systému SQL Server. Máte také možnost ručně nainstalujte SQL Server na virtuálním počítači s jedním volně licencovanou verzi (Developer a Express) nebo opětovným použitím licenci místní. Pokud je používání vlastní licence, musíte mít [mobilitu licencí v rámci programu Software Assurance na Azure](https://azure.microsoft.com/pricing/license-mobility/). Další informace najdete v tématu [Doprovodné materiály k cenám pro virtuální počítače Azure s SQL Serverem](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Jak můžu migrovat místní databázi systému SQL Server do cloudu?**

   Vytvoření virtuálního počítače Azure s instancí SQL serveru. Potom migrace místních databází do této instance. Strategie migrace dat, naleznete v tématu [migrace databáze SQL serveru na SQL Server na Virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licencování

1. **Jak mohu nainstalovat licencovanou kopii systému SQL Server na Virtuálním počítači Azure?**

   Existují dva způsoby, jak to provést. Můžete zřídit jednu z [imagí virtuálních počítačů, které podporuje licence](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), což se označuje také jako přineste si – vlastní licence (BYOL). Další možností je zkopírovat instalačního média systému SQL Server do virtuálního počítače s Windows serverem a potom nainstalovat SQL Server na virtuálním počítači. Ale pokud ručně nainstalujte SQL Server, neexistuje žádná integrace portálu a není podporováno rozšíření agenta SQL Server IaaS, takže funkce, jako jsou automatické opravy chyb a automatické zálohování nebude fungovat v tomto scénáři. Z tohoto důvodu doporučujeme používat jednu z Galerie imagí BYOL. Pokud chcete použít BYOL nebo média systému SQL Server na Virtuálním počítači Azure, musíte mít [mobilitu licencí v rámci programu Software Assurance na Azure](https://azure.microsoft.com/pricing/license-mobility/). Další informace najdete v tématu [Doprovodné materiály k cenám pro virtuální počítače Azure s SQL Serverem](virtual-machines-windows-sql-server-pricing-guidance.md).


1. **Musím zaplatit licence SQL serveru na Virtuálním počítači Azure, pokud se slouží pouze pro pohotovostní režim/převzetí služeb při selhání?**

   Pokud máte Software Assurance a využít mobilitu licencí, jak je popsáno v virtuálního počítače nejčastější dotazy ohledně licencování,] (https://azure.microsoft.com/pricing/licensing-faq/) potom není potřeba platit licenční Server SQL účasti jako pasivní sekundární replika v nasazení vysokou DOSTUPNOSTÍ. V opačném případě budete muset zaplatit jeho licence.

1. **Můžete změnit virtuálního počítače používat vlastní licenci na SQL Server, pokud byl vytvořen z některou k imagí s průběžnými platbami Galerie?**

   Ano. Můžete snadno přesouvat přesunout mezi těmito dvěma licenční modely, bez ohledu na obrázku, který byl původně nasazené. Další informace najdete v tématu [jak změnit licenční model virtuálního počítače SQL](virtual-machines-windows-sql-ahb.md).

1. **Použít Image BYOL nebo RP virtuálního počítače SQL k vytvoření nového virtuálního počítače SQL?**

   Přineste si – používání vlastní licence (BYOL) image jsou dostupné jenom pro zákazníky se smlouvou EA. Další zákazníky programu Software Assurance používali poskytovatele prostředků virtuálního počítače s SQL k vytvoření virtuálního počítače SQL s [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/licensing-faq/). 

1. **Bude vyžadovat přepínání licenční modely žádné výpadky pro SQL Server?**

   Ne. [Změna licenční model](virtual-machines-windows-sql-ahb.md) nevyžaduje žádné výpadky pro SQL Server, jak tato změna je hned platná a nevyžaduje restartování virtuálního počítače. 

1. **Program Azure Hybrid Benefit aktivovat předplatných CSP?**

   Ano. [Změna licenční model](virtual-machines-windows-sql-ahb.md) je dostupná pro předplatná CSP. 

1. **Dodatečné poplatky přinese registrace virtuálního počítače s poskytovatelem prostředků pro nový virtuální počítač SQL?**

   Ne. Poskytovatel prostředků virtuálního počítače SQL právě umožňuje další možnosti správy pro SQL Server na virtuálním počítači Azure s žádné další poplatky. 

1. **Je poskytovatel prostředků virtuálního počítače s SQL k dispozici pro všechny zákazníky?**
 
   Ano. Všichni zákazníci budou moct zaregistrovat u nového poskytovatele prostředků virtuálního počítače s SQL. Ale můžete jenom zákazníci s programem Software Assurance Benefit aktivovat [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) (byol) na virtuální počítač s SQL serverem. 

1. **Co se stane _* Microsoft.SqlVirtualMachine_* prostředku, pokud je přesunut nebo vyřadit prostředku virtuálního počítače?** 

   Pokud je prostředků Microsoft.Compute/VirtualMachine vyřadit nebo přesunout, pak je přidružený prostředek Microsoft.SqlVirtualMachine upozornění, že má asynchronní replikace operaci.

1. **Co se stane k virtuálnímu počítači, pokud _* vyřadit Microsoft.SqlVirtualMachine_* prostředků?**

   Prostředek Microsoft.Compute/VirtualMachine nemá žádný vliv při přetažení Microsoft.SqlVirtualMachine prostředků. Licencování změní se však výchozí zpět na původní zdroj obrázku. 

1. **Je možné zaregistrovat svým nasazené virtuální počítače SQL serveru s poskytovatelem prostředků pro virtuální počítač SQL?**

   Ano. Pokud jste nasadili SQL Server z vlastní média, můžete zaregistrovat virtuálního počítače s SQL s poskytovatelem prostředků zobrazíte možnosti správy výhody poskytované modulem rozšíření SQL IaaS. Můžete ale nejde převést na průběžné platby místním nasazený virtuální počítač SQL. 

## <a name="administration"></a>Správa

1. **Můžete nainstalovat druhou instanci systému SQL Server na stejném virtuálním počítači? Můžete změnit nainstalované součásti výchozí instance?**

   Ano. Instalační médium systému SQL Server nachází ve složce na **C** jednotky. Spustit **Setup.exe** z tohoto umístění pro přidání nové instance systému SQL Server nebo na změnu jiné nainstalované funkce SQL serveru na počítači. Všimněte si, že některé funkce, jako je například automatizovaného zálohování, automatické opravy a integrace Azure Key Vaultu, fungovat pouze pro výchozí instanci.

1. **Můžete odinstalovat výchozí instanci systému SQL Server?**

   Ano, ale zde jsou některé důležité informace. Jak je uvedeno v předchozí odpověď, funkce, které využívají [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) pracovat pouze na výchozí instanci. Pokud odinstalujete výchozí instance, podívejte se i nadále rozšíření a může způsobit chyby v protokolu událostí. Tyto chyby jsou z těchto dvou zdrojů: **Správa přihlašovacích údajů systému Microsoft SQL Server** a **agenta systému Microsoft SQL Server IaaS**. Některé z chyb může být podobné následujícímu:

      Při navazování připojení k serveru SQL Server došlo k chybě související se sítí nebo s instancí. Server nebyl nalezen nebo nebyl přístupný.

   Pokud se rozhodnete odinstalovat výchozí instance, odinstalovat také [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) také.

1. **Můžu odebrat systému SQL Server zcela z virtuálního počítače s SQL?**

   Ano, ale bude nadále účtovat virtuálního počítače s SQL, jak je popsáno v [doprovodné materiály k pro virtuální počítače Azure s SQL serverem cenám](virtual-machines-windows-sql-server-pricing-guidance.md). Pokud už nepotřebujete systému SQL Server, můžete nasadit nový virtuální počítač a migraci dat a aplikací do nového virtuálního počítače. Pak můžete odebrat virtuální počítač s SQL serverem.
   
## <a name="updating-and-patching"></a>Aktualizace a opravy chyb

1. **Jak upgradovat na novou verzi nebo edici SQL serveru na Virtuálním počítači Azure?**

   V současné době neexistuje žádný místní upgrade SQL serveru běžícího na Virtuálním počítači Azure. Vytvořit nový virtuální počítač Azure s požadovanou verzí/edicí systému SQL Server, a potom migrovat databáze do nového serveru pomocí standardní [technik migrace dat](virtual-machines-windows-migrate-sql.md).

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
