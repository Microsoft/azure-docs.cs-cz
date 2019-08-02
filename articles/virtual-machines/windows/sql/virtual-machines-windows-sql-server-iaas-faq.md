---
title: SQL Server Windows Virtual Machines v Azure – Nejčastější dotazy | Microsoft Docs
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se spuštění SQL Server na virtuálních počítačích Azure.
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
ms.author: mathoma
ms.openlocfilehash: 7f6ec1ee65727fb8c3c7d98f696c288e95ec880a
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876195"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Nejčastější dotazy týkající se SQL Server běžících na virtuálních počítačích s Windows v Azure

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Tento článek obsahuje odpovědi na některé nejčastější dotazy týkající se spuštění [SQL Server na Windows Virtual Machines v Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> Tento článek se zaměřuje na problémy, které jsou specifické pro SQL Server virtuálních počítačů s Windows. Pokud používáte SQL Server on Linux virtuálních počítačů, přečtěte si téma [Nejčastější dotazy pro Linux](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a>Fotografií

1. **Jaké jsou k dispozici Image Galerie virtuálních počítačů SQL Server?** 

   Azure udržuje image virtuálních počítačů pro všechny podporované hlavní verze SQL Server ve všech edicích pro Windows i Linux. Další informace najdete v tématu úplný seznam [imagí virtuálních počítačů s Windows](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) a [imagí virtuálních počítačů](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create)se systémem Linux.

1. **Jsou existující obrázky galerie virtuálních počítačů aktualizované SQL Server?**

   Každé dva měsíce SQL Server Image v galerii virtuálních počítačů se aktualizují s nejnovějšími aktualizacemi pro Windows a Linux. Pro image Windows To zahrnuje aktualizace, které jsou v web Windows Update důležité, včetně důležitých SQL Server aktualizací zabezpečení a aktualizací Service Pack. Pro Image Linux to zahrnuje nejnovější aktualizace systému. SQL Server kumulativní aktualizace se pro systémy Linux a Windows liší. V případě systému SQL Server Linux jsou do aktualizace zahrnuty také kumulativní aktualizace. Ale v tuto chvíli nejsou virtuální počítače s Windows aktualizované s SQL Server nebo kumulativními aktualizacemi Windows serveru.

1. **SQL Server můžou se z Galerie odebrat image virtuálních počítačů?**

   Ano. Azure udržuje jenom jednu Image na hlavní verzi a edici. Například když je vydána nová aktualizace Service Pack SQL Server, Azure přidá novou bitovou kopii do galerie pro danou aktualizaci Service Pack. Obrázek SQL Server pro předchozí aktualizaci Service Pack je okamžitě odebrán z Azure Portal. Je ale stále k dispozici pro zřizování z PowerShellu po dobu příštích tří měsíců. Po třech měsících již není k dispozici předchozí obrázek aktualizace Service Pack. Tato zásada odebrání by se taky použila v případě, že se SQL Serverá verze Nepodporovaná, když dosáhne konce svého životního cyklu.


1. **Je možné nasadit starší obrázek SQL Server, který není viditelný v Azure Portal?**

   Ano, pomocí prostředí PowerShell. Další informace o nasazení SQL Server virtuálních počítačů pomocí prostředí PowerShell najdete v tématu [jak zřídit SQL Server virtuálních počítačů pomocí Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

1. **Můžu vytvořit image VHD z SQL Server virtuálního počítače?**

   Ano, ale existuje několik důležitých informací. Pokud tento virtuální pevný disk nasadíte do nového virtuálního počítače v Azure, nezískáte na portálu konfigurační oddíl SQL Server. Pak musíte spravovat možnosti konfigurace SQL Server prostřednictvím PowerShellu. Také se vám bude účtovat sazba za službu SQL VM, na kterou se image původně vycházela. To platí i v případě, že před nasazením odeberete SQL Server z virtuálního pevného disku. 

1. **Je možné nastavit konfigurace, které nejsou v galerii virtuálních počítačů zobrazené (například Windows 2008 R2 + SQL Server 2012)?**

   Ne. Pro image z Galerie virtuálních počítačů, které zahrnují SQL Server, je nutné vybrat jednu z poskytnutých imagí buď pomocí Azure Portal nebo [](virtual-machines-windows-ps-sql-create.md)pomocí PowerShellu. 


## <a name="creation"></a>Vytvořena

1. **Návody vytvořit virtuální počítač Azure s SQL Server?**

   Nejjednodušším řešením je vytvořit virtuální počítač, který obsahuje SQL Server. Kurz týkající se registrace do Azure a vytvoření virtuálního počítače s SQL z portálu najdete v tématu [zřízení virtuálního počítače s SQL Server v Azure Portal](virtual-machines-windows-portal-sql-server-provision.md). Můžete vybrat bitovou kopii virtuálního počítače, která používá licencování SQL Server s platbou za sekundu, nebo můžete použít image, která vám umožní využít vlastní SQL Server licenci. Máte také možnost ručně nainstalovat SQL Server na virtuálním počítači s jednou z licencovaných edicí (Developer nebo Express) nebo pomocí místní licence. Pokud přenesete vlastní licenci, musíte mít [License mobility prostřednictvím Software Assurance v Azure](https://azure.microsoft.com/pricing/license-mobility/). Další informace najdete v tématu [Doprovodné materiály k cenám pro virtuální počítače Azure s SQL Serverem](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Jak můžu migrovat místní databázi SQL Server do cloudu?**

   Nejdřív vytvořte virtuální počítač Azure s instancí SQL Server. Pak migrujte své místní databáze do této instance. Informace o strategiích migrace dat najdete v tématu [migrace databáze SQL Server pro SQL Server na virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licencování

1. **Jak můžu na virtuální počítač Azure nainstalovat licencovanou kopii SQL Serveru?**

   Můžete to provést dvěma způsoby. Můžete zřídit některou z [imagí virtuálního počítače s podporou licencí](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), což se také označuje jako model používání vlastní licence (BYOL). Další možností je zkopírovat instalační médium SQL Serveru na virtuální počítač s Windows Serverem a pak na tomto virtuálním počítači nainstalovat SQL Server. Pokud však nainstalujete SQL Server ručně, připravíte se o integraci portálu a podporu rozšíření agenta SQL Server IaaS, takže funkce jako automatizované zálohování a automatizované opravy v tomto scénáři nebudou fungovat. Z tohoto důvodu doporučujeme použít některou z imagí BYOL z galerie. Pokud chcete používat BYOL nebo vlastní SQL Server média na virtuálním počítači Azure, musíte mít [License mobility prostřednictvím Software Assurance v Azure](https://azure.microsoft.com/pricing/license-mobility/). Další informace najdete v tématu [Doprovodné materiály k cenám pro virtuální počítače Azure s SQL Serverem](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Musím platit za licenci SQL Serveru na virtuálním počítači Azure, pokud se používá pouze jako pohotovostní nebo pro převzetí služeb při selhání?**

   Pokud máte program Software Assurance a používáte License Mobility, jak je popsáno v tématu [licencování virtuálních počítačů – Nejčastější dotazy](https://azure.microsoft.com/pricing/licensing-faq/), nemusíte platit licence jedna SQL Server účast jako pasivní sekundární repliky v nasazení ha. Jinak za jeho licenci platit musíte.

1. **Můžu změnit virtuální počítač tak, aby používal vlastní licenci SQL Serveru, pokud byl vytvořený z některé z imagí z galerie s průběžnými platbami?**

   Ano. Pokud jste původně začali s imagí s průběžnými platbami, můžete se snadno přesouvat mezi těmito dvěma licenčními modely. Pokud jste však původně začali s imagí BYOL, nemůžete licenci přepnout na průběžné platby. Další informace najdete v tématu [Změna licenčního modelu pro SQL Server virtuální počítač](virtual-machines-windows-sql-ahb.md).

   > [!Note]
   > V současné době je toto zařízení k dispozici pouze pro zákazníky s veřejným cloudem.

1. **Mám k vytvoření nového virtuálního počítače SQL použít image BYOL nebo poskytovatele prostředků virtuálního počítače SQL?**

   Image BYOL (Používání vlastní licence) jsou k dispozici pouze pro zákazníky se smlouvou Enterprise. Jiní zákazníci, kteří mají Software Assurance, by měli použít poskytovatele prostředků SQL VM k vytvoření virtuálního počítače s SQL pomocí [zvýhodněné hybridní využití Azure (AHB)](https://azure.microsoft.com/pricing/licensing-faq/). 

1. **Způsobí přepnutí modelů licencování výpadek SQL Serveru?**

   Ne. [Změna licenčního modelu](virtual-machines-windows-sql-ahb.md) nevyžaduje žádné výpadky SQL Server, protože změna je okamžitě platná a nevyžaduje restartování virtuálního počítače. Pokud ale chcete zaregistrovat SQL Server virtuální počítač s poskytovatelem prostředků virtuálního počítače SQL, [IaaS rozšíření SQL](virtual-machines-windows-sql-server-agent-extension.md) je předpokladem a instalace rozšíření SQL IaaS restartuje službu SQL Server. Proto pokud je potřeba nainstalovat rozšíření SQL IaaS, mělo by se to provést během údržby. 

1. **Můžou předplatná CSP aktivovat Zvýhodněné hybridní využití Azure?**

   Ano, Zvýhodněné hybridní využití Azure je k dispozici pro odběry CSP. Zákazníci CSP by si měli nejdřív nasadit image s průběžnými platbami a pak [změnit licenční model](virtual-machines-windows-sql-ahb.md) na vlastní licenci.  

1. **Jsou s registrací virtuálního počítače u nového poskytovatele prostředků virtuálního počítače SQL spojené další náklady?**

   Ne. Poskytovatel prostředků virtuálního počítače SQL pouze poskytuje další možnosti správy SQL Serveru na virtuálním počítači Azure bez dalších poplatků. 

1. **Je poskytovatel prostředků virtuálního počítače SQL dostupný pro všechny zákazníky?**
 
   Ano. Všichni zákazníci se můžou zaregistrovat u nového poskytovatele prostředků virtuálního počítače SQL. [Zvýhodněné hybridní využití Azure (](https://azure.microsoft.com/pricing/hybrid-benefit/) nebo BYOL) na virtuálním počítači s SQL Server ale můžou aktivovat jenom zákazníci s výhodou Software Assurance. 

1. **Co se stane s prostředkem _Microsoft. SqlVirtualMachine_ , pokud je prostředek virtuálního počítače přesunutý nebo vyřazený?** 

   Po vyřazení nebo přesunutí prostředku Microsoft. COMPUTE/VirtualMachine je přidružený prostředek Microsoft. SqlVirtualMachine upozorněn na asynchronní replikaci operace.

1. **Co se stane s virtuálním počítačem, pokud je prostředek _Microsoft. SqlVirtualMachine_ vyřazený?**

    Prostředek Microsoft. COMPUTE/VirtualMachine nemá vliv na vyřazení prostředku Microsoft. SqlVirtualMachine. Změny licencí se ale nastaví jako výchozí zpátky na původní zdroj bitové kopie. 

1. **Je možné u poskytovatele prostředků virtuálního počítače SQL zaregistrovat samostatně nasazené virtuální počítače s SQL Serverem?**

    Ano. Pokud jste nasadili SQL Server z vlastního média a nainstalovali jste rozšíření SQL IaaS, můžete zaregistrovat SQL Server virtuální počítač s poskytovatelem prostředků, abyste získali výhody správy poskytované rozšířením SQL IaaS. Samostatně nasazený virtuální počítač SQL však nemůžete převést na průběžné platby.

## <a name="administration"></a>Správa

1. **Můžu na stejný virtuální počítač nainstalovat druhou instanci SQL Serveru? Můžu změnit nainstalované funkce výchozí instance?**

   Ano. Instalační médium SQL Server se nachází ve složce na jednotce **C** . Spuštěním souboru **Setup. exe** z tohoto umístění přidejte nové instance SQL Server nebo změňte jiné nainstalované funkce SQL Server v počítači. Všimněte si, že některé funkce, například automatizované zálohování, automatizované opravy a Integrace Azure Key Vault, pracují jenom s výchozí instancí nebo s pojmenovanou instancí nakonfigurovanou správně (viz otázka 3). 

1. **Můžu odinstalovat výchozí instanci SQL Serveru?**

   Ano, měli byste však vzít v úvahu několik skutečností. Jak je uvedeno v předchozí odpovědi, jsou k dispozici funkce, které spoléhají na [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).  Pokud odinstalujete výchozí instanci bez odebrání rozšíření IaaS, rozšíření bude i nadále hledat a může generovat chyby protokolu událostí. Tyto chyby pocházejí z následujících dvou zdrojů: **Microsoft SQL Server Správce přihlašovacích údajů** a **agentů Microsoft SQL Server IaaS**. Následuje příklad jedné z chyb:

      Při navazování připojení k SQL Serveru došlo k chybě související se sítí nebo konkrétní instancí. Server se nenašel nebo nebyl dostupný.

   Pokud se rozhodnete odinstalovat výchozí instanci, odinstalujte taky [SQL Server rozšíření agenta IaaS](virtual-machines-windows-sql-server-agent-extension.md) .

1. Můžu **použít pojmenovanou instanci SQL Server s rozšířením IaaS**?
   
   Ano, pokud je pojmenovaná instance jedinou instancí na SQL Server a v případě, že původní výchozí instance byla [správně](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md#installation)odinstalována. Pokud není k dispozici žádná výchozí instance a na jednom virtuálním počítači SQL Server existuje více pojmenovaných instancí, rozšíření IaaS se nepodaří nainstalovat. 

1. **Můžu z virtuálního počítače SQL zcela odebrat SQL Server?**

   Ano, ale budete se vám nadále účtovat za váš virtuální počítač SQL, jak je popsáno v tématu [doprovodné materiály k SQL Server virtuálních počítačů Azure](virtual-machines-windows-sql-server-pricing-guidance.md). Pokud už SQL Server nepotřebujete, můžete nasadit nový virtuální počítač a migrovat na něj data a aplikace. Pak můžete odebrat virtuální počítač s SQL Serverem.
   
## <a name="updating-and-patching"></a>Aktualizace a opravy

1. **Návody změnit na jinou verzi nebo edici SQL Server na virtuálním počítači Azure?**

   Zákazníci mohou změnit verzi nebo edici SQL Server pomocí instalačního média, které obsahuje požadovanou verzi nebo edici SQL Server. Po změně edice použijte Azure Portal k úpravě vlastnosti edice virtuálního počítače tak, aby přesně odrážela fakturaci pro virtuální počítač. Další informace najdete v tématu [Změna edice SQL Server virtuálního počítače](virtual-machines-windows-sql-change-edition.md). 


1. **Jak se v SQL Serverm virtuálním počítači používají aktualizace a aktualizace Service Pack?**

   Virtuální počítače umožňují kontrolu hostitelského počítače včetně doby a způsobu použití aktualizací. Pro operační systém můžete aktualizace systému Windows nainstalovat ručně nebo můžete povolit Plánovací službu s názvem [automatizované opravy](virtual-machines-windows-sql-automated-patching.md). Automatizované opravy nainstalují jakékoli aktualizace, které jsou označené jako důležité, včetně aktualizací SQL Serveru v této kategorii. Ostatní volitelné aktualizace SQL Server se musí instalovat ručně.

## <a name="general"></a>Obecné

1. **Jsou SQL Server na virtuálních počítačích Azure podporované instance clusterů s podporou převzetí služeb při selhání (FCI)?**

   Ano. [Cluster s podporou převzetí služeb při selhání systému Windows můžete vytvořit v systému Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) a použít prostory úložiště S přímým přístupem (S2D) pro úložiště clusteru. Alternativně můžete použít řešení clusteringu nebo úložišť třetích stran, jak je popsáno v tématu [Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > V tuto chvíli se [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) nepodporuje pro SQL Server FCI v Azure. Doporučujeme odinstalovat rozšíření z virtuálních počítačů, které jsou součástí FCI. Toto rozšíření podporuje funkce, jako jsou například automatizované zálohování a opravy a některé funkce portálu pro SQL. Po odinstalaci agenta nebudou tyto funkce fungovat pro virtuální počítače s SQL.

1. **Jaký je rozdíl mezi virtuálními počítači SQL a službou SQL Database?**

   V koncepčním provozu SQL Server na virtuálním počítači Azure se neliší od spuštění SQL Server ve vzdáleném datacentru. Na rozdíl od [SQL Database](../../../sql-database/sql-database-technical-overview.md) nabízí databázi jako službu. Pomocí SQL Database nemáte přístup k počítačům, které hostují vaše databáze. Úplné porovnání najdete v tématu [volba cloudové SQL Server možnosti: Databáze Azure SQL (PaaS) nebo SQL Server na virtuálních počítačích Azure (IaaS](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)).

1. **Návody nainstalovat SQL Data Tools na mém virtuálním počítači Azure?**

    Stáhněte si a nainstalujte nástroje SQL Data Tools z [Microsoft SQL Server Data Tools – Business Intelligence for Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313).

1. **Podporují se na virtuálních počítačích s SQL Server distribuované transakce s MSDTC?**
   
    Ano. Místní služba DTC je podporovaná pro SQL Server 2016 SP2 a vyšší. Avšak aplikace musí být testovány při použití skupin dostupnosti Always On, protože transakce probíhající během převzetí služeb při selhání se nezdaří a musí se opakovat. Služba DTC (CLUSTERED DTC) je dostupná od Windows serveru 2019. 

## <a name="resources"></a>Zdroje a prostředky

**Virtuální počítače s Windows**:

* [Přehled SQL Server na virtuálním počítači s Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Zřízení virtuálního počítače s SQL Server Windows](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrace databáze na SQL Server na virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md)
* [Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md)
* [Osvědčené postupy z hlediska výkonu pro SQL Server na Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Modely aplikací a vývojové strategie pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Virtuální počítače se systémem Linux**:

* [Přehled SQL Server na virtuálním počítači se systémem Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Zřízení virtuálního počítače s SQL Server Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Nejčastější dotazy (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Dokumentace k SQL Serveru na Linuxu](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
