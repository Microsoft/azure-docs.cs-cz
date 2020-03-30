---
title: SQL Server na virtuálních počítačích s Windows v nejčastějších dotazech k Azure | Dokumenty společnosti Microsoft
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se spuštění SQL Serveru na virtuálních počítačích Azure.
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
ms.openlocfilehash: 3b73c329c3db54ba78db15ced8e919af4d4a45d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249734"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Nejčastější dotazy týkající se SQL Serveru na virtuálních počítačích s Windows v Azure

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Tento článek obsahuje odpovědi na některé z nejčastějších otázek týkajících se spuštění [SQL Serveru na virtuálních počítačích Windows v Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> Tento článek se zaměřuje na problémy specifické pro SQL Server na virtuálních počítačích se systémem Windows. Pokud používáte SQL Server na virtuálních počítačích s Linuxem, přečtěte si nejčastější dotazy k [Linuxu](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a>Obrázky

1. **Jaké image galerie virtuálních počítačů serveru SQL Server jsou k dispozici?** 

   Azure udržuje image virtuálních strojů pro všechny podporované hlavní verze SQL Serveru ve všech edicích pro Windows i Linux. Další informace najdete v kompletním seznamu [bitových kopií virtuálních počítače s Windows](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) a [bitových kopií virtuálních aplikací v Linuxu](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create).

1. **Jsou aktualizovány existující bitové kopie galerie virtuálních počítačů serveru SQL Server?**

   Každé dva měsíce jsou bitové kopie serveru SQL Server v galerii virtuálních počítačů aktualizovány nejnovějšími aktualizacemi systému Windows a Linux. Pro bitové kopie systému Windows to zahrnuje všechny aktualizace, které jsou ve službě Windows Update označeny jako důležité, včetně důležitých aktualizací zabezpečení serveru SQL Server a aktualizací Service Pack. Pro linuxové obrázky to zahrnuje nejnovější aktualizace systému. Kumulativní aktualizace SQL Serveru jsou zpracovávány odlišně pro Linux a Windows. Pro Linux, SQL Server kumulativní aktualizace jsou také zahrnuty v aktualizaci. V současné době však virtuální servery Windows nejsou aktualizovány kumulativními aktualizacemi serveru SQL Server nebo Windows Server.

1. **Mohou být image virtuálních počítačů serveru SQL Server odebrány z galerie?**

   Ano. Azure udržuje jenom jednu bitovou kopii na hlavní verzi a edici. Například při vydání nové aktualizace SERVICE Pack serveru SQL Server azure přidá novou bitovou kopii do galerie pro tuto aktualizaci Service Pack. Bitová kopie serveru SQL Server pro předchozí aktualizaci Service Pack se okamžitě odebere z portálu Azure. Je však stále k dispozici pro zřizování z prostředí PowerShell pro další tři měsíce. Po třech měsících již není k dispozici předchozí bitová kopie aktualizace Service Pack. Tato zásada odebrání by také použít, pokud sql server verze stane nepodporovaný, když dosáhne konce svého životního cyklu.


1. **Je možné nasadit starší bitovou kopii SQL Serveru, která není viditelná na webu Azure?**

   Ano, pomocí prostředí PowerShell. Další informace o nasazení virtuálních počítačů SQL Serveru pomocí PowerShellu najdete v tématu [Jak zřídit virtuální počítače SQL Serveru pomocí Azure PowerShellu](virtual-machines-windows-ps-sql-create.md).

1. **Můžu vytvořit zobecněnou image Azure SQL Server Marketplace mého virtuálního počítače SQL Serveru a použít ji k nasazení virtuálních počítačů?**

   Ano, ale pak musíte [zaregistrovat každý virtuální počítač SQL Server u poskytovatele prostředků virtuálního počítače SQL Server,](virtual-machines-windows-sql-register-with-resource-provider.md) abyste mohli spravovat virtuální počítač SQL Server na portálu a využívat funkce, jako jsou automatické opravy a automatické zálohování. Při registraci u poskytovatele prostředků budete také muset zadat typ licence pro každý virtuální modul SQL Server. 

1. **Můžu k nasazení virtuálního virtuálního modulu SQL Serveru použít vlastní virtuální pevný disk?**

   Ano, ale pak musíte [zaregistrovat každý virtuální počítač SQL Server u poskytovatele prostředků virtuálního počítače SQL Server,](virtual-machines-windows-sql-register-with-resource-provider.md) abyste mohli spravovat virtuální počítač SQL Server na portálu a využívat funkce, jako jsou automatické opravy a automatické zálohování.

1. **Je možné nastavit konfigurace, které nejsou zobrazeny v galerii virtuálních počítačů (například Windows 2008 R2 + SQL Server 2012)?**

   Ne. Pro image galerie virtuálních počítačů, které obsahují SQL Server, musíte vybrat jednu z dodaných bitových kopií buď prostřednictvím portálu Azure nebo přes [PowerShell](virtual-machines-windows-ps-sql-create.md). Máte však možnost nasadit virtuální ho virtuálního modulu systému Windows a vlastní instalaci SQL Serveru. Potom je nutné [zaregistrovat virtuální počítač SQL Server u poskytovatele prostředků virtuálního počítače SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) pro správu virtuálního počítače SQL Server na portálu, stejně jako využívat funkce, jako je automatické opravy a automatické zálohování. 


## <a name="creation"></a>Vytvoření

1. **Jak vytvořím virtuální počítač Azure s SQL Serverem?**

   Nejjednodušší metodou je vytvořit virtuální počítač, který obsahuje SQL Server. Kurz týkající se registrace pro Azure a vytvoření virtuálního počítače SQL Serveru z portálu najdete [v tématu Zřízení virtuálního počítače SQL Serveru na webu Azure Portal](virtual-machines-windows-portal-sql-server-provision.md). Můžete vybrat bitovou kopii virtuálního počítače, která používá licencování serveru SQL Server s platbou za sekundu, nebo můžete použít bitovou kopii, která umožňuje přenést vlastní licenci serveru SQL Server. Máte také možnost ručně nainstalovat SQL Server na virtuální počítač s volně licencovanou edicí (Developer nebo Express) nebo opětovným použitím místní licence. Ujistěte se, že [zaregistrovat sql server virtuální počítač s poskytovatelem prostředků virtuálního počítače SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) pro správu virtuálního počítače SQL Server na portálu, stejně jako využívat funkce, jako je automatické opravy a automatické zálohování. Pokud si přivedete vlastní licenci, musíte mít [mobilitu licencí prostřednictvím programu Software Assurance v Azure](https://azure.microsoft.com/pricing/license-mobility/). Další informace najdete v tématu [Doprovodné materiály k cenám pro virtuální počítače Azure s SQL Serverem](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Jak můžu migrovat svou místní databázi SQL Serveru do cloudu?**

   Nejprve vytvořte virtuální počítač Azure s instancí SERVERU SQL Server. Pak migrujte místní databáze do této instance. Strategie migrace dat najdete v [tématu Migrace databáze SQL Serveru na SQL Server ve virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Licencování

1. **Jak můžu na virtuální počítač Azure nainstalovat licencovanou kopii SQL Serveru?**

   Existují tři způsoby, jak to udělat. Pokud jste zákazníkem podnikové smlouvy (EA), můžete zřídit jednu z [iobrazek virtuálních strojů, která podporuje licence](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), která se také označuje jako bring-your-own-license (BYOL). Pokud máte [záruku softwaru](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default), můžete povolit [hybridní výhodu Azure](virtual-machines-windows-sql-ahb.md) na existující bitové kopii pay-as-you-go (PAYG). Nebo můžete zkopírovat instalační médium serveru SQL Server do virtuálního počítači systému Windows Server a potom nainstalovat SQL Server na virtuální počítač. Ujistěte se, že zaregistrovat sql server virtuální počítač u [poskytovatele prostředků](virtual-machines-windows-sql-register-with-resource-provider.md) pro funkce, jako je správa portálu, automatické zálohování a automatické opravy. 

1. **Můžu změnit virtuální počítač tak, aby používal vlastní licenci SQL Serveru, pokud byl vytvořený z některé z imagí z galerie s průběžnými platbami?**

   Ano. Můžete snadno přepnout bitovou kopii galerie s průběžnými platbami (PAYG) tak, aby přinesla vlastní licenci (BYOL) povolením [hybridní výhody Azure](https://azure.microsoft.com/pricing/hybrid-benefit/faq/).  Další informace naleznete [v tématu Jak změnit model licencování pro virtuální hod sql server](virtual-machines-windows-sql-ahb.md). V současné době je toto zařízení k dispozici pouze pro zákazníky veřejného cloudu.

1. **Způsobí přepnutí modelů licencování výpadek SQL Serveru?**

   Ne. [Změna modelu licencování](virtual-machines-windows-sql-ahb.md) nevyžaduje žádné prostoje pro SQL Server jako změna je účinná okamžitě a nevyžaduje restartování virtuálního počítače. Chcete-li však zaregistrovat virtuální počítač SQL Server u poskytovatele prostředků virtuálního počítače SQL Server, je [předpokladem rozšíření SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md) a instalace rozšíření SQL IaaS v _plném_ režimu restartuje službu SQL Server. Jako takové pokud sql IaaS rozšíření je třeba nainstalovat, nainstalujte jej v _zjednodušeném_ režimu pro omezené funkce nebo jej nainstalovat v _plném_ režimu během údržby okna. Rozšíření SQL IaaS nainstalované v _zjednodušeném_ režimu lze kdykoli upgradovat na _plný_ režim, ale vyžaduje restartování služby SQL Server. 
   
1. **Je možné přepnout licenční model na virtuálním počítači SQL Server nasazenýpomocí klasického modelu?**

   Ne. Změna modelu licencování není podporována na klasickém virtuálním počítači. Virtuální počítač můžete migrovat do modelu Azure Resource Manager a zaregistrovat se u poskytovatele prostředků virtuálního počítače SQL Server. Jakmile je virtuální počítač registrovaný u poskytovatele prostředků virtuálního zařízení SQL Server, změny licenčního modelu budou k dispozici na virtuálním počítači.

1. **Můžu použít portál Azure ke správě více instancí na stejném virtuálním počítači?**

   Ne. Správa portálu je funkce poskytovaná poskytovatelem prostředků virtuálního počítači SQL Server, který se spoléhá na rozšíření agenta SQL Server IaaS Agent. Jako takové platí stejná omezení pro poskytovatele prostředků jako rozšíření. Portál může spravovat pouze jednu výchozí instanci nebo jednu pojmenovanou instanci, pokud byla správně nakonfigurována. Další informace o těchto omezeních naleznete v tématu [rozšíření agenta SERVERU SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). 

1. **Můžou předplatná CSP aktivovat hybridní výhodu Azure?**

   Ano, hybridní výhoda Azure je dostupná pro předplatná CSP. Zákazníci CSP by měli nejprve nasadit bitovou kopii s průběžným platbou a potom [změnit licenční model](virtual-machines-windows-sql-ahb.md) tak, aby přinesl vlastní licenci.
   
 
1. **Musím platit za licenci SQL Serveru na virtuálním počítači Azure, pokud se používá pouze jako pohotovostní nebo pro převzetí služeb při selhání?**

   Chcete-li mít bezplatnou pasivní licenci pro skupinu pohotovostní sekundární dostupnosti nebo clusterovinou s podporou převzetí služeb při selhání, musíte splnit všechna následující kritéria, jak je uvedeno v [licenčních podmínkách produktu](https://www.microsoft.com/licensing/product-licensing/products):

   1. Mobilitu [licencí](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) máte prostřednictvím [programu Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3). 
   1. Pasivní instance serveru SQL Server neobsluhuje data serveru SQL Server klientům ani nespouštějí aktivní úlohy serveru SQL Server. Používá se pouze k synchronizaci s primárním serverem a jinak udržovat pasivní databázi v teplém pohotovostním stavu. Pokud se poskytuje data, jako jsou sestavy klientům s provozem aktivní úlohy SQL Server nebo provádění jakékoli jiné práce než co je uvedeno v podmínkách produktu, musí být placené licencované SQL Server instance. Následující aktivita je povolena v sekundární instanci: kontroly konzistence databáze nebo CheckDB, úplné zálohy, zálohy transakčních protokolů a data o využití prostředků monitorování. Můžete také spustit primární a odpovídající instance zotavení po havárii současně pro krátké období testování zotavení po havárii každých 90 dní. 
   1. Aktivní licence SQL Serveru je pokryta programem Software Assurance a umožňuje **jednu** pasivní sekundární instanci serveru SQL Server, pouze se stejným množstvím výpočetních prostředků jako licencovaný aktivní server. 
   1. Sekundární virtuální počítač SQL Server využívá licenci [zotavení po havárii](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) na webu Azure Portal.
   
1. **Co je považováno za pasivní instanci?**

   Pasivní instance serveru SQL Server neobsluhuje data serveru SQL Server klientům ani nespouštějí aktivní úlohy serveru SQL Server. Používá se pouze k synchronizaci s primárním serverem a jinak udržovat pasivní databázi v teplém pohotovostním stavu. Pokud se poskytuje data, jako jsou sestavy klientům s provozem aktivní úlohy SQL Server nebo provádění jakékoli jiné práce než co je uvedeno v podmínkách produktu, musí být placené licencované SQL Server instance. Následující aktivita je povolena v sekundární instanci: kontroly konzistence databáze nebo CheckDB, úplné zálohy, zálohy transakčních protokolů a data o využití prostředků monitorování. Můžete také spustit primární a odpovídající instance zotavení po havárii současně pro krátké období testování zotavení po havárii každých 90 dní.
   

1. **Jaké scénáře mohou využít výhodu zotavení z distasteru(DR)?**

   [Průvodce licencováním](https://aka.ms/sql2019licenseguide) obsahuje scénáře, ve kterých lze využít výhodu zotavení po havárii. Další informace naleznete v podmínkách produktu a obraťte se na licenční kontakty nebo správce účtu.

1. **Která předplatná podporují výhodu zotavení po havárii?**

   Komplexní programy, které nabízejí rovnocenná práva předplatného programu Software Assurance jako pevnou výhodu, podporují výhodu zotavení po havárii. To zahrnuje. ale není omezena na otevřené hodnoty (OV), předplatné s otevřenou hodnotou (OVS), smlouvu Enterprise Agreement (EA), smlouvu o předplatném enterprise (EAS) a zápis serveru a cloudu (SCE). Další informace naleznete v [podmínkách produktu](https://www.microsoft.com/licensing/product-licensing/products) a obraťte se na licenční kontakty nebo na manažera společnosti Acocunt. 

   
 ## <a name="resource-provider"></a>Poskytovatel prostředků

1. **Bude registrace mého virtuálního virtuálního montovana u nového poskytovatele prostředků virtuálního připojení SQL Server přinést další náklady?**

   Ne. Poskytovatel prostředků virtuálního počítače SQL Server pouze umožňuje další možnosti správy pro SQL Server na virtuálním počítači Azure bez dalších poplatků. 

1. **Je poskytovatel prostředků virtuálního připojení sql serveru k dispozici pro všechny zákazníky?**
 
   Ano, pokud byl virtuální počítač SQL Server nasazen ve veřejném cloudu pomocí modelu Resource Manager, a nikoli klasického modelu. Všichni ostatní zákazníci se mohou zaregistrovat u nového poskytovatele prostředků virtuálního připojení SQL Server. Aktivací [hybridní výhody Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) na virtuálním počítači SQL Server však můžou používat vlastní licenci pouze zákazníci s výhodou [Software Assurance.](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) 

1. **Co se stane s prostředku poskytovatele prostředků (_Microsoft.SqlVirtualMachine_) prostředek, pokud prostředek virtuálního počítače je přesunuta nebo vynechána?** 

   Při vynechání nebo přesunutí prostředku Microsoft.Compute/VirtualMachine je přidružený prostředek Microsoft.SqlVirtualMachine upozorněn na asynchronní replikaci operace.

1. **Co se stane s virtuálním počítačem, pokud je poskytovatel prostředků (_Microsoft.SqlVirtualMachine_) vynechán?**

    Prostředek Microsoft.Compute/VirtualMachine není ovlivněn, když je prostředek Microsoft.SqlVirtualMachine vynechán. Změny licencí se však budou ve výchozím nastavení vrátit zpět k původnímu zdroji bitové kopie. 

1. **Je možné zaregistrovat vlastní nasazené virtuální servery SQL Server u poskytovatele prostředků virtuálního připojení k serveru SQL Server?**

    Ano. Pokud jste nasadili SQL Server z vlastního média a nainstalovali rozšíření SQL IaaS, můžete zaregistrovat virtuální počítač SQL Server u poskytovatele prostředků a získat výhody správy poskytované rozšířením SQL IaaS. Však nelze převést vlastní nasazený virtuální modul SQL Server na průběžný platby.


   


## <a name="administration"></a>Správa

1. **Můžu nainstalovat druhou instanci SQL Serveru na stejný virtuální počítač? Mohu změnit nainstalované funkce výchozí instance?**

   Ano. Instalační médium serveru SQL Server je umístěno ve složce na jednotce **C.** Spusťte **program Setup.exe** z tohoto umístění a přidejte nové instance serveru SQL Server nebo změňte další nainstalované funkce serveru SQL Server v počítači. Všimněte si, že některé funkce, jako je například automatické zálohování, automatické opravy a integrace azure trezoru klíčů, fungují pouze proti výchozí instanci nebo pojmenované instanci, která byla správně nakonfigurovaná (viz otázka 3). 

1. **Můžu odinstalovat výchozí instanci SQL Serveru?**

   Ano, měli byste však vzít v úvahu několik skutečností. Za prvé, sql server související fakturace může nadále dochází v závislosti na modelu licence pro virtuální počítač. Za druhé, jak je uvedeno v předchozí odpovědi, existují funkce, které spoléhají na [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md). Pokud odinstalujete výchozí instanci bez odebrání rozšíření IaaS také, rozšíření pokračuje v hledání výchozí instance a může generovat chyby protokolu událostí. Tyto chyby pocházejí z následujících dvou zdrojů: **Microsoft SQL Server Credential Management** a Microsoft SQL Server **IaaS Agent**. Následuje příklad jedné z chyb:

      Při navazování připojení k SQL Serveru došlo k chybě související se sítí nebo konkrétní instancí. Server se nenašel nebo nebyl dostupný.

   Pokud se rozhodnete odinstalovat výchozí instanci, odinstalujte také [rozšíření agenta SQL Server IaaS.](virtual-machines-windows-sql-server-agent-extension.md) 

1. **Mohu použít pojmenovanou instanci serveru SQL Server s rozšířením IaaS?**
   
   Ano, pokud je pojmenovaná instance jedinou instancí na serveru SQL Server a původní výchozí instance byla [správně odinstalována](virtual-machines-windows-sql-server-agent-extension.md#install-on-a-vm-with-a-single-named-sql-server-instance). Pokud neexistuje žádná výchozí instance a existuje více pojmenovaných instancí na jednom virtuálním počítači SQL Server, rozšíření agenta SQL Server IaaS se nezdaří nainstalovat. 

1. **Můžete úplně odebrat SQL Server z virtuálního serveru SQL Server?**

   Ano, ale budete se nadále účtovat za váš virtuální počítač SQL Server, jak je popsáno v [pokynech pro ceny pro virtuální počítače SQL Server Azure](virtual-machines-windows-sql-server-pricing-guidance.md). Pokud už SQL Server nepotřebujete, můžete nasadit nový virtuální počítač a migrovat na něj data a aplikace. Pak můžete odebrat virtuální počítač s SQL Serverem.
   
## <a name="updating-and-patching"></a>Aktualizace a opravy

1. **Jak změním verzi nebo edici SQL Serveru na virtuálním počítači Azure?**

   Zákazníci můžou změnit verzi nebo edici SQL Serveru pomocí instalačního média, které obsahuje požadovanou verzi nebo edici SQL Serveru. Po změně edice pomocí webu Azure Portal upravte vlastnost edice virtuálního počítače, aby přesně odpovídala fakturaci za virtuální počítač. Další informace naleznete v [tématu změna edice virtuálního serveru SQL Server](virtual-machines-windows-sql-change-edition.md). Neexistuje žádný rozdíl v účtování pro různé verze SQL Server, takže po změně verze SQL Server, není potřeba žádná další akce.

1. **Kde lze získat instalační médium pro změnu edice nebo verze serveru SQL Server?**

   Zákazníci, kteří mají [záruku softwaru,](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) mohou získat instalační médium v [Centru multilicenčního programu](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Zákazníci, kteří nemají záruku softwaru, můžou používat instalační médium z bitové kopie virtuálního počítače Marketplace SQL Server, která má požadovanou edici.
   
1. **Jak se aktualizace a aktualizace Service Pack používají na virtuálním počítači serveru SQL Server?**

   Virtuální počítače umožňují kontrolu hostitelského počítače včetně doby a způsobu použití aktualizací. V operačním systému můžete ručně použít aktualizace systému Windows nebo můžete povolit plánovací službu nazvanou [Automatické opravy](virtual-machines-windows-sql-automated-patching.md). Automatizované opravy nainstalují jakékoli aktualizace, které jsou označené jako důležité, včetně aktualizací SQL Serveru v této kategorii. Ostatní volitelné aktualizace SQL Server se musí instalovat ručně.

1. **Je možné upgradovat instanci SQL Server 2008 / 2008 R2 po registraci u poskytovatele prostředků virtuálního serveru SQL Server?**

   Ano. K upgradu verze a edice serveru SQL Server můžete použít libovolná instalační média a potom můžete upgradovat [režim rozšíření SQL IaaS](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)) z _žádného agenta_ na _úplný_. Získáte tak přístup ke všem výhodám rozšíření SQL IaaS, jako je správa portálu, automatické zálohování a automatické opravy. 

1. **Jak lze získat bezplatné rozšířené aktualizace zabezpečení pro mé instance SQL Server 2008 a SQL Server 2008 R2?**

   Můžete získat [bezplatné rozšířené aktualizace zabezpečení](virtual-machines-windows-sql-server-2008-eos-extend-support.md) přesunutím SQL Server as-je do virtuálního počítače Azure SQL. Další informace naleznete v [tématu konec možnosti podpory](/sql/sql-server/end-of-support/sql-server-end-of-life-overview). 
  
   

## <a name="general"></a>Obecné

1. **Jsou instance clusteru SQL Server s podporou clusteru (FCI) na virtuálních počítačích Azure?**

   Ano. Instanci clusteru s podporou převzetí služeb při selhání můžete nainstalovat buď pomocí [sdílených složek pro premium (PFS),](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) nebo [mezery úložiště přímé (S2D)](virtual-machines-windows-portal-sql-create-failover-cluster.md) pro podsystém úložiště. Sdílené složky Premium poskytují kapacity viposlužby a propustnosti, které budou splňovat potřeby mnoha úloh. Pro úlohy náročné na vi zvažte použití úložných prostorů přímo na základě manged premium nebo ultra-disků. Případně můžete použít řešení clusteringu nebo úložiště třetích stran, jak je popsáno v [článku Vysoká dostupnost a zotavení po havárii pro SQL Server ve virtuálních počítačích Azure](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > V současné době _úplné_ [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) není podporováno pro SQL Server FCI v Azure. Doporučujeme odinstalovat _úplné_ rozšíření z virtuálních společností, které se účastní FCI a nainstalovat rozšíření v _zjednodušeném_ režimu místo. Toto rozšíření podporuje funkce, jako je například automatické zálohování a opravy a některé funkce portálu pro SQL Server. Tyto funkce nebudou fungovat pro virtuální server SQL Server po odinstalaci _úplného_ agenta.

1. **Jaký je rozdíl mezi virtuálními servery SQL Server a službou SQL Database?**

   Koncepčně spuštění SQL Serveru na virtuálním počítači Azure se neliší od spuštění SQL Serveru ve vzdáleném datovém centru. Naproti tomu [SQL Database](../../../sql-database/sql-database-technical-overview.md) nabízí databázi jako službu. S SQL Database nemáte přístup k počítačům, které hostují databáze. Úplné porovnání najdete [v tématu Volba možnosti cloudového SQL Serveru: Azure SQL (PaaS) Database nebo SQL Server na virtuálních počítačích Azure (IaaS).](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

1. **Jak na svůj virtuální počítač Azure nainstaluju nástroje SQL Data?**

    Stáhněte a nainstalujte nástroje SQL Data z [Microsoft SQL Server Data Tools – Business Intelligence for Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313).

1. **Jsou distribuované transakce s koordinátorem MSDTC podporovány na virtuálních počítačích SQL Server?**
   
    Ano. Místní dtc je podporován pro SQL Server 2016 SP2 a vyšší. Aplikace však musí být testovány při využití vždy na skupiny dostupnosti, jako transakce za letu během převzetí služeb při selhání se nezdaří a musí být opakován. Clusterovaný dtc je k dispozici od Windows Server 2019. 

## <a name="resources"></a>Prostředky

**Virtuální aplikace Windows**:

* [Přehled serveru SQL Server na virtuálním počítači se systémem Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Zřízení virtuálního virtuálního modulu SQL Server pro Windows](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrace databáze na SQL Server na virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md)
* [Vysoká dostupnost a zotavení po havárii pro SQL Server ve virtuálních počítačích Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Osvědčené postupy z hlediska výkonu pro SQL Server na Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Modely aplikací a vývojové strategie pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Virtuální počítače S Linuxem**:

* [Přehled SQL Serveru na virtuálním počítači s Linuxem](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Zřízení virtuálního počítače SQL Server Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Nejčastější dotazy (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Sql Server v dokumentaci k Linuxu](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
