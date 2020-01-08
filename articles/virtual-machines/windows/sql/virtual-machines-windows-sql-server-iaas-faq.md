---
title: SQL Server Windows Virtual Machines v Azure – Nejčastější dotazy | Microsoft Docs
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se spuštění SQL Server na virtuálních počítačích Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: 4919c8f303488b583ea4d10dca87dd29bfb52e99
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75374076"
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

1. **Můžu vytvořit zobecněnou image Azure SQL Server Marketplace na mém virtuálním počítači s SQL Server a použít ji k nasazení virtuálních počítačů?**

   Ano, ale abyste mohli spravovat SQL Server virtuální počítač na portálu a využívat funkce, jako jsou automatické opravy a automatické zálohování, musíte [každý SQL Server virtuální počítač zaregistrovat pomocí poskytovatele prostředků SQL Server virtuálního počítače](virtual-machines-windows-sql-register-with-resource-provider.md) . Při registraci u poskytovatele prostředků budete taky muset zadat typ licence pro každý virtuální počítač SQL Server. 

1. **Můžu pomocí vlastního virtuálního pevného disku nasadit SQL Server virtuální počítač?**

   Ano, ale abyste mohli spravovat SQL Server virtuální počítač na portálu a využívat funkce, jako jsou automatické opravy a automatické zálohování, musíte [každý SQL Server virtuální počítač zaregistrovat pomocí poskytovatele prostředků SQL Server virtuálního počítače](virtual-machines-windows-sql-register-with-resource-provider.md) .

1. **Je možné nastavit konfigurace, které nejsou v galerii virtuálních počítačů zobrazené (například Windows 2008 R2 + SQL Server 2012)?**

   Ne. Pro image z Galerie virtuálních počítačů, které zahrnují SQL Server, je nutné vybrat jednu z poskytnutých imagí buď pomocí Azure Portal nebo pomocí [PowerShellu](virtual-machines-windows-ps-sql-create.md). Máte ale možnost nasadit virtuální počítač s Windows a SQL Server k němu nainstalovat sami. Pak je nutné [zaregistrovat svůj SQL Server virtuální počítač s poskytovatelem prostředků SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) pro správu SQL Server virtuálního počítače na portálu a využívat funkce, jako jsou automatické opravy a automatické zálohování. 


## <a name="creation"></a>Vytvoření

1. **Návody vytvořit virtuální počítač Azure s SQL Server?**

   Nejjednodušším způsobem je vytvořit virtuální počítač, který obsahuje SQL Server. Kurz týkající se registrace do Azure a vytvoření virtuálního počítače s SQL Server z portálu najdete v tématu [zřízení virtuálního počítače s SQL Server v Azure Portal](virtual-machines-windows-portal-sql-server-provision.md). Můžete vybrat bitovou kopii virtuálního počítače, která používá licencování SQL Server s platbou za sekundu, nebo můžete použít image, která vám umožní využít vlastní SQL Server licenci. Máte také možnost ručně nainstalovat SQL Server na virtuálním počítači s jednou z licencovaných edicí (Developer nebo Express) nebo pomocí místní licence. Nezapomeňte [zaregistrovat svůj SQL Server virtuální počítač pomocí poskytovatele prostředků SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) pro správu SQL Server virtuálního počítače na portálu a využít funkce, jako jsou automatické opravy a automatické zálohování. Pokud přenesete vlastní licenci, musíte mít [License mobility prostřednictvím Software Assurance v Azure](https://azure.microsoft.com/pricing/license-mobility/). Další informace najdete v tématu [Doprovodné materiály k cenám pro virtuální počítače Azure s SQL Serverem](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Jak můžu migrovat místní databázi SQL Server do cloudu?**

   Nejdřív vytvořte virtuální počítač Azure s instancí SQL Server. Pak migrujte své místní databáze do této instance. Informace o strategiích migrace dat najdete v tématu [migrace databáze SQL Server pro SQL Server na virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Správa licencí

1. **Jak můžu na virtuální počítač Azure nainstalovat licencovanou kopii SQL Serveru?**

   Existují tři způsoby, jak to provést. Pokud jste zákazníkem se smlouvou Enterprise (EA), můžete zřídit jednu z [imagí virtuálních počítačů, které podporují licence](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), označované také jako vlastní licence (BYOL). Pokud máte [program Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default), můžete [zvýhodněné hybridní využití Azure](virtual-machines-windows-sql-ahb.md) povolit na stávající imagi s průběžnými platbami (PAYG). Případně můžete zkopírovat instalační médium SQL Server do virtuálního počítače s Windows serverem a pak na virtuální počítač nainstalovat SQL Server. Nezapomeňte zaregistrovat svůj SQL Server virtuální počítač u [poskytovatele prostředků](virtual-machines-windows-sql-register-with-resource-provider.md) pro funkce, jako je Správa portálu, automatizované zálohování a automatizované opravy. 

1. **Musím platit za licenci SQL Serveru na virtuálním počítači Azure, pokud se používá pouze jako pohotovostní nebo pro převzetí služeb při selhání?**

   Pokud chcete mít bezplatnou pasivní licenci pro sekundární skupinu dostupnosti nebo instanci clusteru s podporou převzetí služeb při selhání, musíte splnit všechna následující kritéria, jak je uvedeno v [licenčních podmínkách produktu](https://www.microsoft.com/licensing/product-licensing/products):

   1. Máte [mobilitu licencí](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) v rámci programu [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3). 
   1. Pasivní SQL Server instance neobsluhuje SQL Server data pro klienty nebo spouštění aktivních SQL Server úloh. Slouží pouze k synchronizaci s primárním serverem a v případě údržby pasivní databáze v pohotovostním úsporném režimu. Pokud obsluhuje data, jako jsou například sestavy klientů se spuštěnou službou Active SQL Server, nebo provádění jakékoli jiné práce, než jaká je zadána v rámci podmínek produktu, musí se jednat o placené licencované SQL Server instanci. U sekundární instance je povolena následující aktivita: kontroly konzistence databáze nebo CheckDB, úplné zálohování, zálohování transakčních protokolů a monitorování dat o využití prostředků. V případě krátké doby testování zotavení po havárii každé 90 dní můžete také spustit primární a odpovídající instanci zotavení po havárii. 
   1. Licence na službu Active SQL Server je pokrytá programem Software Assurance a umožňuje **jednu** pasivní sekundární SQL Server instanci, která má až stejnou velikost COMPUTE jako licencovaný aktivní server. 
   1. Sekundární SQL Server virtuální počítač využívá licenci [pro zotavení po havárii](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) v Azure Portal.

1. **Můžu změnit virtuální počítač tak, aby používal vlastní licenci SQL Serveru, pokud byl vytvořený z některé z imagí z galerie s průběžnými platbami?**

   Ano. Můžete snadno přepnout image galerie s průběžnými platbami (PAYG) a využít tak vlastní licenci (BYOL) tím, že povolíte [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/faq/).  Další informace najdete v tématu [Změna licenčního modelu pro SQL Server virtuální počítač](virtual-machines-windows-sql-ahb.md). V současné době je toto zařízení k dispozici pouze pro zákazníky s veřejným cloudem.

1. **Způsobí přepnutí modelů licencování výpadek SQL Serveru?**

   Ne. [Změna licenčního modelu](virtual-machines-windows-sql-ahb.md) nevyžaduje žádné výpadky SQL Server, protože změna je okamžitě platná a nevyžaduje restartování virtuálního počítače. Pokud ale chcete zaregistrovat SQL Server virtuální počítač pomocí poskytovatele prostředků SQL Server virtuálních počítačů, je potřeba [rozšíření SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md) a instalace rozšíření SQL IaaS v _plném_ režimu restartuje službu SQL Server. Pokud je třeba nainstalovat rozšíření SQL IaaS, buď ho nainstalujte do _zjednodušeného_ režimu pro omezené funkce, nebo ho během časového období údržby nainstalujte v _plném_ režimu. Rozšíření SQL IaaS nainstalované v _jednoduchém_ režimu můžete kdykoli upgradovat na _úplný_ režim, ale vyžaduje restart služby SQL Server. 

1. **Můžu použít Azure Portal ke správě více instancí na jednom virtuálním počítači?**

   Ne. Správa portálu je funkce poskytovaná poskytovatelem prostředků SQL Server virtuálních počítačů, která spoléhá na rozšíření agenta SQL Server IaaS. Stejně tak platí stejná omezení jako u poskytovatele prostředků jako u rozšíření. Portál může spravovat jenom jednu výchozí instanci nebo jednu pojmenovanou instanci, pokud byla správně nakonfigurovaná. Další informace o těchto omezeních najdete v tématu [SQL Server rozšíření agenta IaaS](virtual-machines-windows-sql-server-agent-extension.md). 

1. **Můžou předplatná CSP aktivovat Zvýhodněné hybridní využití Azure?**

   Ano, Zvýhodněné hybridní využití Azure je k dispozici pro odběry CSP. Zákazníci CSP by si měli nejdřív nasadit image s průběžnými platbami a pak [změnit licenční model](virtual-machines-windows-sql-ahb.md) na vlastní licenci.

1. **Zaregistrujeme svůj virtuální počítač pomocí nového poskytovatele prostředků SQL Server virtuálních počítačů, který přináší další náklady?**

   Ne. Poskytovatel prostředků virtuálního počítače s SQL Server jenom umožňuje další možnosti správy pro SQL Server na virtuálním počítači Azure bez dalších poplatků. 

1. **Je poskytovatel prostředků SQL Server virtuálních počítačů dostupný pro všechny zákazníky?**
 
   Ano, pokud byl virtuální počítač SQL Server nasazený ve veřejném cloudu pomocí modelu Správce prostředků, a ne klasického modelu. Všichni ostatní zákazníci se můžou zaregistrovat u nového poskytovatele prostředků virtuálního počítače SQL Server. Vlastní licenci ale můžou používat jenom zákazníci s výhodou [program Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) , a to aktivací [zvýhodněné hybridní využití Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) na SQL serverm virtuálním počítači. 

1. **Co se stane s prostředkem poskytovatele prostředků (_Microsoft. SqlVirtualMachine_), pokud je prostředek virtuálního počítače přesunutý nebo vyřazený?** 

   Po vyřazení nebo přesunutí prostředku Microsoft. COMPUTE/VirtualMachine je přidružený prostředek Microsoft. SqlVirtualMachine upozorněn na asynchronní replikaci operace.

1. **Co se stane s virtuálním počítačem, pokud je prostředek poskytovatele prostředků (_Microsoft. SqlVirtualMachine_) vyřazený?**

    Prostředek Microsoft. COMPUTE/VirtualMachine nemá vliv na vyřazení prostředku Microsoft. SqlVirtualMachine. Změny licencí se ale nastaví jako výchozí zpátky na původní zdroj bitové kopie. 

1. **Je možné zaregistrovat SQL Server virtuální počítače nasazené svým držitelem pomocí SQL Server poskytovatele prostředků virtuálního počítače?**

    Ano. Pokud jste nasadili SQL Server z vlastního média a nainstalovali jste rozšíření SQL IaaS, můžete zaregistrovat SQL Server virtuální počítač s poskytovatelem prostředků, abyste získali výhody správy poskytované rozšířením SQL IaaS. Nemůžete ale převést SQL Server virtuální počítač nasazený svým držitelem na průběžné platby.

1. **Je možné přepínat licenční model na SQL Server nasazeném virtuálním počítači pomocí klasického modelu?**

   Ne. Změna modelu licencování není na klasickém virtuálním počítači podporovaná. Můžete migrovat virtuální počítač do modelu Azure Resource Manager a zaregistrovat se pomocí poskytovatele prostředků SQL Server virtuálního počítače. Jakmile je virtuální počítač zaregistrovaný u poskytovatele prostředků SQL Server virtuálních počítačů, budou na virtuálním počítači k dispozici změny v modelu licencování. 
   


## <a name="administration"></a>Správa

1. **Můžu na stejný virtuální počítač nainstalovat druhou instanci SQL Server? Můžu změnit nainstalované funkce výchozí instance?**

   Ano. Instalační médium SQL Server se nachází ve složce na jednotce **C** . Spuštěním souboru **Setup. exe** z tohoto umístění přidejte nové instance SQL Server nebo změňte jiné nainstalované funkce SQL Server v počítači. Všimněte si, že některé funkce, například automatizované zálohování, automatizované opravy a Integrace Azure Key Vault, pracují jenom s výchozí instancí nebo s pojmenovanou instancí nakonfigurovanou správně (viz otázka 3). 

1. **Můžu odinstalovat výchozí instanci SQL Serveru?**

   Ano, měli byste však vzít v úvahu několik skutečností. V závislosti na modelu licence pro virtuální počítač se může dál vyskytnout SQL Server – fakturace přidružená k. Za druhé, jak je uvedeno v předchozí odpovědi, jsou k dispozici funkce, které spoléhají na [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). Pokud odinstalujete výchozí instanci bez odebrání rozšíření IaaS, rozšíření bude i nadále hledat výchozí instanci a může generovat chyby protokolu událostí. Tyto chyby jsou z následujících dvou zdrojů: **Microsoft SQL Server Správa přihlašovacích údajů** a **Agent Microsoft SQL Server IaaS**. Následuje příklad jedné z chyb:

      Při navazování připojení k SQL Serveru došlo k chybě související se sítí nebo konkrétní instancí. Server se nenašel nebo nebyl dostupný.

   Pokud se rozhodnete odinstalovat výchozí instanci, odinstalujte taky [SQL Server rozšíření agenta IaaS](virtual-machines-windows-sql-server-agent-extension.md) . 

1. Můžu **použít pojmenovanou instanci SQL Server s rozšířením IaaS**?
   
   Ano, pokud je pojmenovaná instance jedinou instancí na SQL Server a v případě, že původní výchozí instance byla [správně odinstalována](virtual-machines-windows-sql-server-agent-extension.md#install-on-a-vm-with-a-single-named-sql-server-instance). Pokud není k dispozici žádná výchozí instance a na jednom virtuálním počítači SQL Server existuje více pojmenovaných instancí, rozšíření agenta SQL Server IaaS se nepodaří nainstalovat. 

1. **Můžu SQL Server kompletně odebrat z SQL Server virtuálního počítače?**

   Ano, ale budete se nadále účtovat za váš SQL Server virtuální počítač, jak je popsáno v [doprovodnéch materiálech pro SQL Server virtuálních počítačů Azure](virtual-machines-windows-sql-server-pricing-guidance.md). Pokud už SQL Server nepotřebujete, můžete nasadit nový virtuální počítač a migrovat na něj data a aplikace. Pak můžete odebrat virtuální počítač s SQL Serverem.
   
## <a name="updating-and-patching"></a>Aktualizace a opravy

1. **Jak změním verzi nebo edici SQL Serveru na virtuálním počítači Azure?**

   Zákazníci můžou změnit verzi nebo edici SQL Serveru pomocí instalačního média, které obsahuje požadovanou verzi nebo edici SQL Serveru. Po změně edice pomocí webu Azure Portal upravte vlastnost edice virtuálního počítače, aby přesně odpovídala fakturaci za virtuální počítač. Další informace najdete v tématu [Změna edice SQL Server virtuálního počítače](virtual-machines-windows-sql-change-edition.md). Pro různé verze SQL Server neexistuje rozdíl mezi fakturací, takže když se změní verze SQL Server, není potřeba žádná další akce.

1. **Kde můžu získat instalační médium pro změnu edice nebo verze SQL Server?**

   Zákazníci, kteří mají [program Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) , mohou získat instalační média z [centra](https://www.microsoft.com/Licensing/servicecenter/default.aspx)multilicenčního programu. Zákazníci, kteří nemají Software Assurance, mohou použít instalační médium z webu Marketplace SQL Server image virtuálního počítače, která má požadovanou edici.
   
1. **Jak se v SQL Serverm virtuálním počítači používají aktualizace a aktualizace Service Pack?**

   Virtuální počítače umožňují kontrolu hostitelského počítače včetně doby a způsobu použití aktualizací. Pro operační systém můžete aktualizace systému Windows nainstalovat ručně nebo můžete povolit Plánovací službu s názvem [automatizované opravy](virtual-machines-windows-sql-automated-patching.md). Automatizované opravy nainstalují jakékoli aktualizace, které jsou označené jako důležité, včetně aktualizací SQL Serveru v této kategorii. Ostatní volitelné aktualizace SQL Server se musí instalovat ručně.

1. **Můžu upgradovat SQL Server 2008/2008 R2 po registraci pomocí poskytovatele prostředků SQL Server virtuálního počítače?**

   Ano. K upgradu verze a edice SQL Server můžete použít libovolné instalační médium a pak můžete upgradovat [IaaS režim rozšíření SQL](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes), a to z _žádného agenta_ na _úplný_. Díky tomu budete mít přístup ke všem výhodám rozšíření SQL IaaS, jako je Správa portálu, automatizované zálohování a automatizované opravy. 

1. **Jak mohu získat bezplatné rozšířené aktualizace zabezpečení pro můj konec podpory SQL Server 2008 a SQL Server 2008 R2?**

   [Bezplatné rozšířené aktualizace zabezpečení](virtual-machines-windows-sql-server-2008-eos-extend-support.md) můžete získat přesunutím SQL Server tak, jak se nachází na virtuálním počítači Azure SQL. Další informace najdete v tématu [konec možností podpory](/sql/sql-server/end-of-support/sql-server-end-of-life-overview). 
  
   

## <a name="general"></a>Obecné

1. **Jsou SQL Server na virtuálních počítačích Azure podporované instance clusterů s podporou převzetí služeb při selhání (FCI)?**

   Ano. [Cluster s podporou převzetí služeb při selhání systému Windows můžete vytvořit v systému Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) a použít prostory úložiště S přímým přístupem (S2D) pro úložiště clusteru. Alternativně můžete použít řešení clusteringu nebo úložišť třetích stran, jak je popsáno v tématu [Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > V tuto chvíli se _úplné_ [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) nepodporuje pro SQL Server FCI v Azure. Doporučujeme odinstalovat _úplné_ rozšíření z virtuálních počítačů, které jsou součástí FCI, a místo toho nainstalovat rozšíření v _jednoduchém_ režimu. Toto rozšíření podporuje funkce, jako je automatické zálohování a opravy a některé funkce portálu pro SQL Server. Po odinstalaci _úplného_ agenta nebudou tyto funkce fungovat u SQL serverch virtuálních počítačů.

1. **Jaký je rozdíl mezi SQL Servermi virtuálními počítači a službou SQL Database?**

   V koncepčním provozu SQL Server na virtuálním počítači Azure se neliší od spuštění SQL Server ve vzdáleném datacentru. Na rozdíl od [SQL Database](../../../sql-database/sql-database-technical-overview.md) nabízí databázi jako službu. Pomocí SQL Database nemáte přístup k počítačům, které hostují vaše databáze. Úplné porovnání najdete v tématu Volba [cloudového SQL Server možnosti: databáze Azure SQL (PaaS) nebo SQL Server na virtuálních počítačích Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Návody nainstalovat SQL Data Tools na mém virtuálním počítači Azure?**

    Stáhněte si a nainstalujte nástroje SQL Data Tools z [Microsoft SQL Server Data Tools – Business Intelligence for Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313).

1. **Podporují se na virtuálních počítačích s SQL Server distribuované transakce s MSDTC?**
   
    Ano. Místní služba DTC je podporovaná pro SQL Server 2016 SP2 a vyšší. Avšak aplikace musí být testovány při použití skupin dostupnosti Always On, protože transakce probíhající během převzetí služeb při selhání se nezdaří a musí se opakovat. Služba DTC (CLUSTERED DTC) je dostupná od Windows serveru 2019. 

## <a name="resources"></a>Materiály

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
