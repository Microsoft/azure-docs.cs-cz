---
title: SQL Server na virtuálních počítačích Azure Windows nejčastější dotazy | Microsoft Docs
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
ms.date: 03/20/2018
ms.author: v-shysun
ms.openlocfilehash: e0254cd16c27597c3d52aed19b4c4ece49bac765
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-azure-virtual-machines"></a>Nejčastější dotazy pro SQL Server běžící na virtuálních počítačích Windows Azure

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Tento článek obsahuje odpovědi na některé nejčastější dotazy o spuštění [systému SQL Server na virtuálních počítačích Windows Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> Tento článek se zaměřuje na problémy, které jsou specifické pro SQL Server na virtuálních počítačích systému Windows. Pokud používáte systém SQL Server na virtuální počítače s Linuxem, najdete v článku [Linux – nejčastější dotazy](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Bitové kopie

1. **Jaké Image Galerie virtuálních počítačů systému SQL Server jsou k dispozici?**

   Azure udržuje bitové kopie virtuálních počítačů pro všechny podporované hlavní verze systému SQL Server ve všech edicích Windows a Linux. Další podrobnosti najdete v tématu úplný seznam [Image virtuálních počítačů Windows](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) a [bitové kopie virtuálního počítače s Linuxem](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create).

1. **Jsou aktualizovány stávající Image Galerie virtuálních počítačů systému SQL Server?**

   Každé dva měsíce, bitové kopie systému SQL Server v galerii virtuálních počítačů jsou aktualizované o nejnovější Windows a Linux aktualizace. Pro bitové kopie systému Windows to zahrnuje všechny aktualizace, které jsou označené jako důležité ve službě Windows Update, včetně důležitých aktualizací zabezpečení systému SQL Server a aktualizace service Pack. Pro Linux Image to zahrnuje nejnovější aktualizace systému. Kumulativní aktualizace systému SQL Server jsou zpracovány jinak pro Linux a Windows. Pro systémy Linux kumulativní aktualizace systému SQL Server také zahrnuty v aktualizaci. Ale v tuto chvíli se neaktualizují virtuálních počítačů Windows s kumulativní aktualizací systému SQL Server nebo Windows Server.

1. **Můžete získat z galerie odebrat bitové kopie virtuálních počítačů systému SQL Server?**

   Ano. Azure udržuje jenom jednu image na hlavní verze a edice. Po vydání nové aktualizace service pack systému SQL Server, Azure přidá novou bitovou kopii do Galerie této aktualizace service Pack. Bitová kopie systému SQL Server pro předchozí aktualizaci service pack je okamžitě odstraněna z portálu Azure. Je však stále k dispozici pro zřizování z prostředí PowerShell pro další tři měsíce. Po dobu tří měsíců předchozí obrázek service pack již není k dispozici. Tato zásada odebrání by platí také v případě změní Nepodporovaná verze systému SQL Server, pokud ukončení životního cyklu.

1. **Můžete vytvořit image virtuálního pevného disku z virtuálního počítače s SQL Server?**

   Ano, ale existuje několik aspekty jsou. Pokud nasadíte tento virtuální pevný disk na nový virtuální počítač v Azure, můžete udělat ge není v části Konfigurace serveru SQL Server na portálu. Pak musí spravovat možnosti konfigurace SQL serveru pomocí prostředí PowerShell. Navíc vám bude účtována pro rychlostí původně podle vaší image virtuálního počítače SQL. To platí i v případě systému SQL Server odeberete z disku VHD před nasazením. 

1. **Je možné nastavit konfigurace není vidět v galerii virtuálních počítačů (pro příklad Windows 2008 R2 a SQL Server 2012)?**

   Ne. Galerie Image virtuálních počítačů, které zahrnují systému SQL Server musíte vybrat jednu z zadané bitové kopie.

## <a name="creation"></a>Vytvoření

1. **Jak vytvořit virtuální počítač Azure se systémem SQL Server?**

   Nejsnazším řešením je vytvoření virtuálního počítače, který zahrnuje SQL Server. Kurz týkající se registrace do služby Azure a vytvoření virtuálního počítače s SQL z portálu, najdete v části [zřízení virtuálního počítače s SQL serverem na portálu Azure](virtual-machines-windows-portal-sql-server-provision.md). Můžete vybrat bitovou kopii virtuálního počítače, který používá na licencování SQL serveru platím za sekundu, nebo můžete použít image, která umožňuje přineste si vlastní licenci na SQL Server. Máte také možnost ručně instalace systému SQL Server na virtuálním počítači pomocí volně licencovanou verzi (vývojáře nebo Express) nebo opětovným použitím licenci na místě. Pokud jste přineste si vlastní licenci, musíte mít [mobilitou licencí v rámci programu Software Assurance na Azure](https://azure.microsoft.com/pricing/license-mobility/). Další informace najdete v tématu [Doprovodné materiály k cenám pro virtuální počítače Azure s SQL Serverem](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Jak můžete migrovat místní databáze systému SQL Server do cloudu?**

   Nejprve vytvořte virtuální počítač Azure s instancí serveru SQL. Poté migrujte místní databáze do této instance. Strategie migrace dat, najdete v části [migrovat databázi systému SQL Server do systému SQL Server ve virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Správa licencí

1. **Jak můžete instalovat Moje licencovanou kopii systému SQL Server na virtuální počítač Azure?**

   Chcete-li to provést dvěma způsoby. Můžete zřídit mezi [bitové kopie virtuálních počítačů, které podporuje licence](virtual-machines-windows-sql-server-iaas-overview.md#BYOL). Další možností je ke zkopírování instalačního média systému SQL Server do virtuálního počítače Windows serveru a SQL Server nainstalovat na virtuální počítač. Pro licencování z důvodů, musíte mít [mobilitou licencí v rámci programu Software Assurance na Azure](https://azure.microsoft.com/pricing/license-mobility/). Další informace najdete v tématu [Doprovodné materiály k cenám pro virtuální počítače Azure s SQL Serverem](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Můžete změnit virtuálního počítače použít vlastní licenci na SQL Server, pokud byl vytvořen z jedné z bitových kopií průběžnými platbami Galerie?**

   Ne. Nelze přepnout z platím za sekundu licencí k používání vlastní licence. Vytvořit nový virtuální počítač Azure pomocí jedné z [BYOL image](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), a potom migrovat své databáze na nový server pomocí standardní [techniky migrace dat](virtual-machines-windows-migrate-sql.md).

1. **Musím platit na licencí systému SQL Server na virtuální počítač Azure, pokud je pouze používá pro pohotovostní režim nebo převzetí služeb při selhání?**

   Pokud máte Software Assurance a použijete mobilita licencí, jak je popsáno v [virtuální počítač nejčastější dotazy ohledně licencování](http://azure.microsoft.com/pricing/licensing-faq/) pak nemusí platit pro licenční Server SQL účastní jako pasivní sekundární replika v nasazení s vysokou DOSTUPNOSTÍ. Jinak je potřeba se k jeho licence.


## <a name="administration"></a>Správa

1. **Můžete instalaci druhé instance systému SQL Server na stejného virtuálního počítače? Můžete změnit nainstalované součásti výchozí instance?**

   Ano. Instalační médium systému SQL Server nachází ve složce na **C** jednotky. Spustit **Setup.exe** z tohoto umístění pro přidání nové instance systému SQL Server nebo změna jiné instalace funkce serveru SQL Server na počítači. Všimněte si, že některé funkce, například automatizovaného zálohování, automatizovaných oprav a Azure Key Vault integraci, fungovat jenom na výchozí instanci.

1. **Můžete odinstalovat výchozí instance systému SQL Server?**

   Ano, ale existuje jsou některé aspekty. Jak jsme uvedli v předchozím odpověď, funkce, které spoléhají na [rozšíření agenta systému SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) pracovat pouze u výchozí instance. Pokud odinstalujete výchozí instanci, podívejte se i nadále rozšíření a může způsobit chyby v protokolu událostí. Tyto chyby jsou z následujících dvou zdrojů: **správu přihlašovacích údajů systému Microsoft SQL Server** a **IaaS agenta systému Microsoft SQL Server**. Jedním z chyby může být podobné následujícímu:

      Při navazování připojení k systému SQL Server došlo k chybě související se sítí nebo konkrétní instancí. Server nebyl nalezen nebo není přístupný.

   Pokud se rozhodnete odinstalovat výchozí instanci, odinstalovat také [rozšíření agenta systému SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) také.

1. **Lze odebrat systému SQL Server úplně z virtuálního počítače s SQL?**

   Ano, ale bude dál účtovat pro virtuální počítač SQL, jak je popsáno v [ceny pokyny pro virtuální počítače Azure SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md). Pokud již nepotřebujete systému SQL Server, můžete nasadit nového virtuálního počítače a migraci dat a aplikací do nového virtuálního počítače. Potom můžete odebrat virtuální počítač systému SQL Server.
   
## <a name="updating-and-patching"></a>Aktualizace a opravy chyb

1. **Jak upgradovat na novou verzi nebo edici systému SQL Server ve virtuálním počítači Azure?**

   V současné době není k dispozici žádné místní upgrade pro SQL Server běžící ve virtuálním počítači Azure. Vytvořit nový virtuální počítač Azure s požadovanou verzi nebo edici systému SQL Server, a potom migrovat své databáze na nový server pomocí standardní [techniky migrace dat](virtual-machines-windows-migrate-sql.md).

1. **Jak jsou aktualizace a aktualizace service Pack použity na virtuální počítač SQL Server?**

   Virtuální počítače udělení ovládat hostitelský počítač, včetně, kdy a jak můžete použít aktualizace. Pro operační systém, můžete provést ručně aktualizace systému windows, nebo můžete povolit plánování služba s názvem [automatizovaných oprav](virtual-machines-windows-sql-automated-patching.md). Automatizované opravy nainstalují jakékoli aktualizace, které jsou označené jako důležité, včetně aktualizací SQL Serveru v této kategorii. Ostatní volitelné aktualizace SQL Server se musí instalovat ručně.

## <a name="general"></a>Obecné

1. **SQL Server převzetí služeb při selhání clusteru instance (FCI) podporuje na virtuálních počítačích Azure?**

   Ano. Můžete [vytvoření clusteru s podporou převzetí služeb při selhání systému Windows v systému Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) a použít prostory úložiště – přímé (S2D) pro úložiště clusteru. Alternativně můžete řešení třetí strany, clustering nebo úložiště, jak je popsáno v [vysoké dostupnosti a zotavení po havárii pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

1. **Jaký je rozdíl mezi virtuálním počítačům systému SQL a služba SQL Database?**

   Koncepčně systémem SQL Server na virtuální počítač Azure, není, se liší od systémem SQL Server ve vzdálené datovém centru. Naproti tomu [SQL Database](../../../sql-database/sql-database-technical-overview.md) nabízí databáze jako služba. S databází SQL nemáte přístup k počítačům, které hostují vaše databáze. Úplné porovnání najdete v tématu [volba cloudového řešení SQL serveru: Azure SQL (PaaS) Database nebo SQL Server na virtuálních počítačích Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Instalace nástrojů SQL Data na virtuálním počítači Azure**

    Stáhněte a nainstalujte nástroje SQL Data z [Microsoft SQL Server Data Tools – Business Intelligence pro Visual Studio 2013](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

## <a name="resources"></a>Zdroje a prostředky

**Virtuální počítače Windows**:

* [Přehled systému SQL Server na virtuální počítač Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Zřízení virtuálního počítače systému SQL Server Windows](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrace databáze do systému SQL Server ve virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md)
* [Vysoká dostupnost a zotavení po havárii pro SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Osvědčené postupy z hlediska výkonu pro SQL Server na Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Modely aplikací a vývojové strategie pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Virtuální počítače s Linuxem**:

* [Přehled systému SQL Server na virtuální počítač s Linuxem](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Zřízení virtuálního počítače s Linuxem SQL serveru](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Nejčastější dotazy (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Dokumentace k SQL Serveru na Linuxu](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
