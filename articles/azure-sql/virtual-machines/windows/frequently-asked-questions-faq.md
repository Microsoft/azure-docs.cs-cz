---
title: SQL Server Windows Virtual Machines v Azure – Nejčastější dotazy | Microsoft Docs
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se spuštění SQL Server na virtuálních počítačích Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: 91f93faded7c18a1bc24f17053231f9011080c57
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036233"
---
# <a name="frequently-asked-questions-for-sql-server-on-azure-vms"></a>Nejčastější dotazy k SQL Server na virtuálních počítačích Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](frequently-asked-questions-faq.md)
> * [Linux](../linux/frequently-asked-questions-faq.md)

Tento článek obsahuje odpovědi na některé nejčastější dotazy týkající se spuštění [SQL Server na Windows Azure Virtual Machines (VM)](https://azure.microsoft.com/services/virtual-machines/sql-server/).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a> Fotografií

1. **Jaké jsou k dispozici Image Galerie virtuálních počítačů SQL Server?** 

   Azure udržuje image virtuálních počítačů pro všechny podporované hlavní verze SQL Server ve všech edicích pro Windows i Linux. Další informace najdete v tématu úplný seznam [imagí virtuálních počítačů s Windows](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) a [imagí virtuálních počítačů](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md#create)se systémem Linux.

1. **Jsou existující obrázky galerie virtuálních počítačů aktualizované SQL Server?**

   Každé dva měsíce SQL Server Image v galerii virtuálních počítačů se aktualizují s nejnovějšími aktualizacemi pro Windows a Linux. Pro image Windows To zahrnuje aktualizace, které jsou v web Windows Update důležité, včetně důležitých SQL Server aktualizací zabezpečení a aktualizací Service Pack. Pro Image Linux to zahrnuje nejnovější aktualizace systému. SQL Server kumulativní aktualizace se pro systémy Linux a Windows liší. V případě systému SQL Server Linux jsou do aktualizace zahrnuty také kumulativní aktualizace. Ale v tuto chvíli nejsou virtuální počítače s Windows aktualizované s SQL Server nebo kumulativními aktualizacemi Windows serveru.

1. **SQL Server můžou se z Galerie odebrat image virtuálních počítačů?**

   Ano. Azure udržuje jenom jednu Image na hlavní verzi a edici. Například když je vydána nová aktualizace Service Pack SQL Server, Azure přidá novou bitovou kopii do galerie pro danou aktualizaci Service Pack. Obrázek SQL Server pro předchozí aktualizaci Service Pack je okamžitě odebrán z Azure Portal. Je ale stále k dispozici pro zřizování z PowerShellu po dobu příštích tří měsíců. Po třech měsících již není k dispozici předchozí obrázek aktualizace Service Pack. Tato zásada odebrání by se taky použila v případě, že se SQL Serverá verze Nepodporovaná, když dosáhne konce svého životního cyklu.


1. **Je možné nasadit starší obrázek SQL Server, který není viditelný v Azure Portal?**

   Ano, pomocí prostředí PowerShell. Další informace o nasazení SQL Server virtuálních počítačů pomocí prostředí PowerShell najdete v tématu [jak zřídit SQL Server virtuálních počítačů pomocí Azure PowerShell](create-sql-vm-powershell.md).
   
1. **Je možné vytvořit zobecněnou Azure Marketplace SQL Server bitovou kopii mého SQL Server virtuálního počítače a použít ji k nasazení virtuálních počítačů?**

   Ano, ale musíte [každý SQL Server virtuální počítač zaregistrovat pomocí rozšíření agenta SQL IaaS](sql-agent-extension-manually-register-single-vm.md) a spravovat tak SQL Server virtuální počítač na portálu a využívat funkce, jako jsou automatické opravy a automatické zálohování. Při registraci s příponou budete taky muset zadat typ licence pro každý virtuální počítač SQL Server.

1. **Návody zobecnit SQL Server na virtuálním počítači Azure a použít ho k nasazení nových virtuálních počítačů?**

   Můžete nasadit virtuální počítač s Windows serverem (bez nainstalovaného SQL Server) a pomocí procesu [SQL sysprepu](/sql/database-engine/install-windows/install-sql-server-using-sysprep) zobecnit SQL Server na virtuálním počítači Azure (Windows) s SQL Server instalačním médiem. Zákazníci, kteří mají [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot%3aprimaryr3), můžou instalační médium získat z centra [Volume Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Zákazníci, kteří nemají Software Assurance, mohou použít instalační médium z Azure Marketplace SQL Server image virtuálního počítače, která má požadovanou edici.

   Případně můžete použít jednu z SQL Server imagí z Azure Marketplace k generalizaci SQL Server na virtuálním počítači Azure. Všimněte si, že před vytvořením vlastní image musíte ve zdrojové imagi odstranit následující klíč registru. V takovém případě může dojít k tomu, že SQL Server bloating nastavení spouštěcí složky nebo rozšíření SQL IaaS ve stavu selhání.

   Cesta ke klíči registru:  
   `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\SysPrepExternal\Specialize`

   > [!NOTE]
   > SQL Server na virtuálních počítačích Azure, včetně těch, které se nasazují z vlastních zobecněných imagí, by mělo být [zaregistrované s rozšířením agenta SQL IaaS](./sql-agent-extension-manually-register-single-vm.md?tabs=azure-cli%252cbash) pro splnění požadavků na dodržování předpisů a používání volitelných funkcí, jako jsou automatické opravy a automatické zálohování. Rozšíření také umožňuje [zadat typ licence](./licensing-model-azure-hybrid-benefit-ahb-change.md?tabs=azure-portal) pro každý virtuální počítač SQL Server.

1. **Můžu pomocí vlastního virtuálního pevného disku nasadit SQL Server virtuální počítač?**

   Ano, ale musíte [každý SQL Server virtuální počítač zaregistrovat pomocí rozšíření agenta SQL IaaS](sql-agent-extension-manually-register-single-vm.md) a spravovat tak SQL Server virtuální počítač na portálu a využívat funkce, jako jsou automatické opravy a automatické zálohování.

1. **Je možné nastavit konfigurace, které nejsou v galerii virtuálních počítačů zobrazené (například Windows 2008 R2 + SQL Server 2012)?**

   No. Pro image z Galerie virtuálních počítačů, které zahrnují SQL Server, je nutné vybrat jednu z poskytnutých imagí buď pomocí Azure Portal nebo pomocí [PowerShellu](create-sql-vm-powershell.md). Máte ale možnost nasadit virtuální počítač s Windows a SQL Server k němu nainstalovat sami. Je pak nutné [zaregistrovat svůj SQL Server virtuální počítač s rozšířením agenta SQL IaaS](sql-agent-extension-manually-register-single-vm.md) a spravovat tak SQL Server virtuální počítač v Azure Portal a využívat funkce, jako jsou automatické opravy a automatické zálohování. 


## <a name="creation"></a>Vytvoření

1. **Návody vytvořit virtuální počítač Azure s SQL Server?**

   Nejjednodušším způsobem je vytvořit virtuální počítač, který obsahuje SQL Server. Kurz týkající se registrace do Azure a vytvoření virtuálního počítače s SQL Server z portálu najdete v tématu [zřízení virtuálního počítače s SQL Server v Azure Portal](create-sql-vm-portal.md). Můžete vybrat bitovou kopii virtuálního počítače, která používá licencování SQL Server s platbou za sekundu, nebo můžete použít image, která vám umožní využít vlastní SQL Server licenci. Máte také možnost ručně nainstalovat SQL Server na virtuálním počítači s jednou z licencovaných edicí (Developer nebo Express) nebo pomocí místní licence. Nezapomeňte [zaregistrovat svůj SQL Server virtuální počítač s rozšířením agenta SQL IaaS](sql-agent-extension-manually-register-single-vm.md) , abyste mohli spravovat SQL Server virtuální počítač na portálu, a využívat funkce, jako jsou automatické opravy a automatické zálohování. Pokud přenesete vlastní licenci, musíte mít [License mobility prostřednictvím Software Assurance v Azure](https://azure.microsoft.com/pricing/license-mobility/). Další informace najdete v tématu [Doprovodné materiály k cenám pro virtuální počítače Azure s SQL Serverem](pricing-guidance.md).

1. **Jak můžu migrovat místní databázi SQL Server do cloudu?**

   Nejdřív vytvořte virtuální počítač Azure s instancí SQL Server. Pak migrujte své místní databáze do této instance. Informace o strategiích migrace dat najdete v tématu [migrace databáze SQL Server pro SQL Server na virtuálním počítači Azure](migrate-to-vm-from-sql-server.md).

## <a name="licensing"></a>Licencování

1. **Jak můžu na virtuální počítač Azure nainstalovat licencovanou kopii SQL Serveru?**

   Můžete to provést třemi způsoby. Pokud jste zákazníkem smlouva Enterprise (EA), můžete zřídit jednu z [imagí virtuálních počítačů, které podporují licence](sql-server-on-azure-vm-iaas-what-is-overview.md#BYOL), označované také jako vlastní licence (BYOL). Pokud máte [program Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default), můžete [zvýhodněné hybridní využití Azure](licensing-model-azure-hybrid-benefit-ahb-change.md) povolit na stávající imagi s průběžnými platbami (PAYG). Případně můžete zkopírovat instalační médium SQL Serveru na virtuální počítač s Windows Serverem a pak na tomto virtuálním počítači nainstalovat SQL Server. Nezapomeňte zaregistrovat SQL Server virtuální počítač s [rozšířením](sql-agent-extension-manually-register-single-vm.md) pro funkce, jako je Správa portálu, automatizované zálohování a automatizované opravy. 


1. **Potřebuje zákazník SQL Server licence pro klientský přístup (CAL), aby se připojil k SQL Server imagi s průběžnými platbami, která běží na Azure Virtual Machines?**

   No. Zákazníci potřebují licence CAL, pokud používají vlastní licenci a přesunují svůj virtuální počítač s SQL Server SA/CAL na virtuální počítače Azure. 

1. **Můžu změnit virtuální počítač tak, aby používal vlastní licenci SQL Serveru, pokud byl vytvořený z některé z imagí z galerie s průběžnými platbami?**

   Ano. Můžete snadno přepnout image galerie s průběžnými platbami (PAYG) a využít tak vlastní licenci (BYOL) tím, že povolíte [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/faq/).  Další informace najdete v tématu [Změna licenčního modelu pro SQL Server virtuální počítač](licensing-model-azure-hybrid-benefit-ahb-change.md). V současné době je tato služba dostupná jenom pro zákazníky s veřejnými a Azure Governmentými cloudy.


1. **Způsobí přepnutí modelů licencování výpadek SQL Serveru?**

   Ne. [Změna licenčního modelu](licensing-model-azure-hybrid-benefit-ahb-change.md) nevyžaduje žádné výpadky SQL Server, protože změna je okamžitě platná a nevyžaduje restartování virtuálního počítače. Pokud ale chcete zaregistrovat SQL Server virtuální počítač s rozšířením agenta SQL IaaS, je potřeba [rozšíření SQL IaaS](sql-server-iaas-agent-extension-automate-management.md) a instalace rozšíření SQL IaaS v _plném_ režimu restartuje službu SQL Server. Pokud je třeba nainstalovat rozšíření SQL IaaS, buď ho nainstalujte do _zjednodušeného_ režimu pro omezené funkce, nebo ho během časového období údržby nainstalujte v _plném_ režimu. Rozšíření SQL IaaS nainstalované v _jednoduchém_ režimu můžete kdykoli upgradovat na _úplný_ režim, ale vyžaduje restart služby SQL Server. 
   
1. **Je možné přepínat modely licencování na SQL Server nasazeném virtuálním počítači pomocí klasického modelu?**

   No. Změna modelů licencování není na klasickém virtuálním počítači podporována. Svůj virtuální počítač můžete migrovat do modelu Azure Resource Manager a zaregistrovat se pomocí rozšíření agenta SQL IaaS. Jakmile je virtuální počítač zaregistrován pomocí rozšíření agenta SQL IaaS, budou na virtuálním počítači k dispozici změny modelu licencování.

1. **Můžu pomocí webu Azure Portal spravovat více instancí na jednom virtuálním počítači?**

   Ne. Správa portálu je funkce poskytovaná rozšířením agenta SQL IaaS, která spoléhá na rozšíření agenta SQL Server IaaS. Stejně tak se stejná omezení vztahují na rozšíření jako na rozšíření. Na portálu je možné spravovat pouze jednu výchozí instanci nebo jednu pojmenovanou instanci (pokud je správně nakonfigurovaná). Další informace o těchto omezeních najdete v tématu [SQL Server rozšíření agenta IaaS](sql-server-iaas-agent-extension-automate-management.md). 

1. **Je možné aktivovat Zvýhodněné hybridní využití Azure pro předplatná CSP?**

   Ano, Zvýhodněné hybridní využití Azure je k dispozici pro předplatná CSP. Zákazníci CSP by si měli nejdřív nasadit image s průběžnými platbami a pak [změnit licenční model](licensing-model-azure-hybrid-benefit-ahb-change.md) na vlastní licenci.
   
 
1. **Musím platit za licenci SQL Serveru na virtuálním počítači Azure, pokud se používá pouze jako pohotovostní nebo pro převzetí služeb při selhání?**

   Pokud chcete mít bezplatnou pasivní licenci pro sekundární skupinu dostupnosti nebo instanci clusteru s podporou převzetí služeb při selhání, musíte splnit všechna následující kritéria, jak je uvedeno v [licenčních podmínkách produktu](https://www.microsoft.com/licensing/product-licensing/products):

   1. Máte [mobilitu licencí](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) v rámci programu [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3). 
   1. Pasivní instance SQL Serveru nesmí poskytovat data SQL Serveru klientům ani spouštět aktivní úlohy SQL Serveru. Musí sloužit pouze k synchronizaci s primárním serverem a jinak udržovat pasivní databázi v záložním pohotovostním režimu. Pokud obsluhuje data, jako jsou například sestavy klientů se spuštěnou službou Active SQL Server, nebo provádění jakékoli jiné práce, než jaká je zadána v rámci podmínek produktu, musí se jednat o placené licencované SQL Server instanci. V sekundární instanci jsou povolené následující aktivity: kontroly konzistence databází nebo používání příkazu CHECKDB, úplné zálohování, zálohování transakčních protokolů a monitorování dat o využití prostředků. Každých 90 dnů také můžete krátkodobě spustit primární instanci i odpovídající instanci pro zotavení po havárii současně pro účely testování zotavení po havárii. 
   1. Licence na službu Active SQL Server je pokrytá programem Software Assurance a umožňuje **jednu** pasivní sekundární SQL Server instanci, která má až stejnou velikost COMPUTE jako licencovaný aktivní server. 
   1. Sekundární SQL Server virtuální počítač využívá licenci [pro zotavení po havárii](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) v Azure Portal.
   
1. **Co se považuje za pasivní instanci?**

   Pasivní instance SQL Serveru nesmí poskytovat data SQL Serveru klientům ani spouštět aktivní úlohy SQL Serveru. Musí sloužit pouze k synchronizaci s primárním serverem a jinak udržovat pasivní databázi v záložním pohotovostním režimu. Pokud poskytuje data (například sestavy) klientům s aktivními úlohami SQL Serveru nebo provádí jinou práci, než je uvedeno v podmínkách produktu, musí se jednat o placenou a licencovanou instanci SQL Serveru. V sekundární instanci jsou povolené následující aktivity: kontroly konzistence databází nebo používání příkazu CHECKDB, úplné zálohování, zálohování transakčních protokolů a monitorování dat o využití prostředků. Každých 90 dnů také můžete krátkodobě spustit primární instanci i odpovídající instanci pro zotavení po havárii současně pro účely testování zotavení po havárii.
   

1. **V jakých scénářích je možné využít výhodu zotavení po havárii (DR)?**

   [Průvodce licencováním](https://aka.ms/sql2019licenseguide) poskytuje scénáře, ve kterých je možné využít výhod zotavení po havárii. Další informace najdete v podmínkách produktu, případně vám je poskytne váš kontakt pro licencování nebo správce účtu.

1. **Která předplatná podporují výhodu zotavení po havárii (DR)?**

   Výhodu DR podporují komplexní programy, které jako pevně danou výhodu nabízejí stejná práva předplatného jako Software Assurance. Patří mezi ně není to ale omezené na, Open Value (OV), Open Value Subscription (OVS), smlouva Enterprise (EA), smlouva Enterprise předplatné (EAS) a zápis na server a Cloud (SCE). Další informace najdete v tématu [podmínky produktu](https://www.microsoft.com/licensing/product-licensing/products) a kontaktování licenčních kontaktů nebo účtu správce účtů. 

   
 ## <a name="extension"></a>Linka

1. **Zaregistruje se můj virtuální počítač s novým rozšířením agenta SQL IaaS a přináší další náklady?**

   No. Rozšíření agenta SQL IaaS umožňuje pouze další možnosti správy pro SQL Server na virtuálním počítači Azure bez dalších poplatků. 

1. **Je k dispozici rozšíření agenta SQL IaaS pro všechny zákazníky?**
 
   Ano, pokud byl virtuální počítač SQL Server nasazený ve veřejném cloudu pomocí modelu Správce prostředků, a ne klasického modelu. Všichni ostatní zákazníci se můžou zaregistrovat s novým rozšířením agenta SQL IaaS. Vlastní licenci ale můžou používat jenom zákazníci s výhodou [program Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) , a to aktivací [zvýhodněné hybridní využití Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) na SQL serverm virtuálním počítači. 

1. **Co se stane s prostředkem rozšíření (_Microsoft. SqlVirtualMachine_), pokud je prostředek virtuálního počítače přesunutý nebo vyřazený?** 

   Po vyřazení nebo přesunutí prostředku Microsoft. COMPUTE/VirtualMachine je přidružený prostředek Microsoft. SqlVirtualMachine upozorněn na asynchronní replikaci operace.

1. **Co se stane s virtuálním počítačem, pokud je prostředek rozšíření (_Microsoft. SqlVirtualMachine_) vyřazený?**

    Prostředek Microsoft. COMPUTE/VirtualMachine nemá vliv na vyřazení prostředku Microsoft. SqlVirtualMachine. Změny licencí se ale nastaví jako výchozí zpátky na původní zdroj bitové kopie. 

1. **Je možné zaregistrovat SQL Server virtuální počítače nasazené svým držitelem pomocí rozšíření agenta SQL IaaS?**

    Ano. Pokud jste nasadili SQL Server z vlastního média a nainstalovali jste rozšíření SQL IaaS, můžete zaregistrovat SQL Server virtuální počítač s rozšířením a získat výhody správy, které poskytuje rozšíření SQL IaaS.    


## <a name="administration"></a>Správa

1. **Můžu na stejný virtuální počítač nainstalovat druhou instanci SQL Server? Můžu změnit nainstalované funkce výchozí instance?**

   Ano. Instalační médium SQL Server se nachází ve složce na jednotce **C** . Pokud chcete přidat nové instance SQL Server nebo změnit jiné nainstalované funkce SQL Server na počítači, spusťte z tohoto umístění **Setup.exe** . Všimněte si, že některé funkce, například automatizované zálohování, automatizované opravy a Integrace Azure Key Vault, pracují jenom s výchozí instancí nebo s pojmenovanou instancí nakonfigurovanou správně (viz otázka 3). Zákazníci, kteří používají [Software Assurance prostřednictvím zvýhodněné hybridní využití Azure](licensing-model-azure-hybrid-benefit-ahb-change.md) nebo licenční model s průběžnými **platbami** , můžou do virtuálního počítače nainstalovat víc instancí SQL Server, aniž by se musely účtovat další náklady na licencování. Další instance SQL Server můžou zadržet systémové prostředky, pokud nejsou správně nakonfigurované. 

1. **Jaký je maximální počet instancí virtuálního počítače?**
   SQL Server 2012 až SQL Server 2019 může podporovat [instance 50](/sql/sql-server/editions-and-components-of-sql-server-version-15#RDBMSSP) na samostatném serveru. Toto je stejný limit bez ohledu na místní Azure. Další informace o tom, jak lépe připravit prostředí, najdete v tématu [osvědčené postupy](performance-guidelines-best-practices.md#multiple-instances) . 

1. **Můžu odinstalovat výchozí instanci SQL Serveru?**

   Ano, měli byste však vzít v úvahu několik skutečností. V závislosti na modelu licence pro virtuální počítač se může dál vyskytnout SQL Server – fakturace přidružená k. Za druhé, jak je uvedeno v předchozí odpovědi, jsou k dispozici funkce, které spoléhají na [rozšíření agenta SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md). Pokud odinstalujete výchozí instanci bez odebrání rozšíření IaaS, rozšíření bude i nadále hledat výchozí instanci a může generovat chyby protokolu událostí. Tyto chyby jsou z následujících dvou zdrojů: **Microsoft SQL Server Správa přihlašovacích údajů** a **Agent Microsoft SQL Server IaaS**. Následuje příklad jedné z chyb:

      Při navazování připojení k SQL Serveru došlo k chybě související se sítí nebo konkrétní instancí. Server se nenašel nebo nebyl dostupný.

   Pokud se rozhodnete odinstalovat výchozí instanci, odinstalujte taky [SQL Server rozšíření agenta IaaS](sql-server-iaas-agent-extension-automate-management.md) . 

1. **Můžu použít pojmenovanou instanci SQL Server s rozšířením IaaS?**
   
   Ano, pokud je pojmenovaná instance jedinou instancí na SQL Server a v případě, že původní výchozí instance byla [správně odinstalována](sql-server-iaas-agent-extension-automate-management.md#named-instance-support). Pokud není k dispozici žádná výchozí instance a na jednom virtuálním počítači SQL Server existuje více pojmenovaných instancí, rozšíření agenta SQL Server IaaS se nepodaří nainstalovat.  

1. **Můžu SQL Server a přidruženou licenci odebrat z SQL Server virtuálního počítače?**

   Ano, ale budete muset provést další kroky, abyste se vyhnuli účtování SQL Server instance, jak je popsáno v [doprovodnéch materiálech k ceníkům](pricing-guidance.md). Pokud chcete úplně odebrat instanci SQL Server, můžete migrovat na jiný virtuální počítač Azure bez SQL Server předinstalovaného na VIRTUÁLNÍm počítači a odstranit aktuální SQL Server virtuální počítač. Pokud chcete virtuální počítač zachovat, ale zastavit SQL Server fakturace, postupujte podle následujících kroků: 

   1. V případě potřeby zálohujte všechna vaše data, včetně systémových databází. 
   1. Odinstalujte SQL Server úplně, včetně rozšíření SQL IaaS (Pokud je k dispozici).
   1. Nainstalujte bezplatnou [verzi SQL Express](https://www.microsoft.com/sql-server/sql-server-downloads).
   1. Zaregistrujte se pomocí rozšíření agenta SQL IaaS v [jednoduchém režimu](sql-agent-extension-manually-register-single-vm.md).
   1. volitelné Zakažte službu Express SQL Server zakázáním spouštění služby. 

1. **Můžu pomocí webu Azure Portal spravovat více instancí na jednom virtuálním počítači?**

   Ne. Správu portálu poskytuje rozšíření agenta SQL IaaS, které spoléhá na rozšíření agenta SQL Server IaaS. Stejně tak platí stejná omezení jako u rozšíření. Portál může spravovat jenom jednu výchozí instanci nebo jednu pojmenovanou instanci, pokud je nakonfigurovaná správně. Další informace najdete v tématu [SQL Server rozšíření agenta IaaS](sql-server-iaas-agent-extension-automate-management.md) . 


## <a name="updating-and-patching"></a>Aktualizace a opravy

1. **Návody změnit na jinou verzi nebo edici SQL Server na virtuálním počítači Azure?**

   Zákazníci můžou změnit verzi nebo edici SQL Serveru pomocí instalačního média, které obsahuje požadovanou verzi nebo edici SQL Serveru. Po změně edice pomocí webu Azure Portal upravte vlastnost edice virtuálního počítače, aby přesně odpovídala fakturaci za virtuální počítač. Další informace najdete v tématu [Změna edice SQL Server virtuálního počítače](change-sql-server-edition.md). Všechny verze SQL Serveru se fakturují stejně, takže po změně verze SQL Serveru nemusíte dělat nic dalšího.

1. **Kde získám instalační médium potřebné ke změně edice nebo verze SQL Serveru?**

   Zákazníci, kteří mají [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default), můžou instalační médium získat z centra [Volume Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Zákazníci, kteří nemají Software Assurance, mohou použít instalační médium z Azure Marketplace SQL Server image virtuálního počítače, která má požadovanou edici.
   
1. **Jak se na virtuálním počítači s SQL Serverem instalují aktualizace a aktualizace Service Pack?**

   Virtuální počítače umožňují kontrolu hostitelského počítače včetně doby a způsobu použití aktualizací. V případě operačního systému můžete aktualizace Windows instalovat ručně nebo můžete povolit službu plánování [Automatizované opravy](automated-patching.md). Automatizované opravy nainstalují jakékoli aktualizace, které jsou označené jako důležité, včetně aktualizací SQL Serveru v této kategorii. Ostatní volitelné aktualizace SQL Server se musí instalovat ručně.

1. **Můžu po registraci pomocí rozšíření agenta SQL IaaS upgradovat instanci SQL Server 2008/2008 R2?**

   Pokud je operační systém Windows Server 2008 R2 nebo novější, ano. K upgradu verze a edice SQL Server můžete použít libovolné instalační médium a pak můžete upgradovat [IaaS režim rozšíření SQL](sql-server-iaas-agent-extension-automate-management.md#management-modes), a to z _žádného agenta_ na _úplný_. Díky tomu budete mít přístup ke všem výhodám rozšíření SQL IaaS, jako je Správa portálu, automatizované zálohování a automatizované opravy. Pokud je verze operačního systému Windows Server 2008, je podporován pouze režim bez agenta. 

1. **Jak získám bezplatné rozšířené aktualizace zabezpečení pro instance SQL Serveru 2008 a SQL Serveru 2008 R2 na konci podpory?**

   [Bezplatné rozšířené aktualizace zabezpečení](sql-server-2008-extend-end-of-support.md) můžete získat přesunutím SQL Server tak, jak se nachází na virtuálním počítači Azure. Další informace najdete v tématu věnovaném [možnostem na konci podpory](/sql/sql-server/end-of-support/sql-server-end-of-life-overview). 
  
   

## <a name="general"></a>Obecné

1. **Jsou SQL Server na virtuálních počítačích Azure podporované instance clusterů s podporou převzetí služeb při selhání (FCI)?**

   Ano. Pro subsystém úložiště můžete nainstalovat instanci clusteru s podporou převzetí služeb při selhání s využitím úrovně [Premium (PFS File Shares)](failover-cluster-instance-premium-file-share-manually-configure.md) nebo [prostorů úložiště s přímým přístupem (S2D)](failover-cluster-instance-storage-spaces-direct-manually-configure.md) . Soubory úrovně Premium poskytují vstupně-výstupní operace za sekundu a propustnost, které budou vyhovovat potřebám řady úloh. Pro úlohy náročné na v/v zvažte použití prostorů úložiště s přímým přístupem na spravovaných Premium nebo extrémně-discích. Alternativně můžete použít řešení clusteringu nebo úložišť třetích stran, jak je popsáno v tématu [Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > V tuto chvíli se _úplné_ [rozšíření agenta SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md) nepodporuje pro SQL Server FCI v Azure. Doporučujeme odinstalovat _úplné_ rozšíření z virtuálních počítačů, které jsou součástí FCI, a místo toho nainstalovat rozšíření v _jednoduchém_ režimu. Toto rozšíření podporuje funkce, jako je automatické zálohování a opravy a některé funkce portálu pro SQL Server. Po odinstalaci _úplného_ agenta nebudou tyto funkce fungovat u SQL serverch virtuálních počítačů.

1. **Jaký je rozdíl mezi SQL Servermi virtuálními počítači a službou SQL Database?**

   V koncepčním provozu SQL Server na virtuálním počítači Azure se neliší od spuštění SQL Server ve vzdáleném datacentru. Na rozdíl od [Azure SQL Database](../../database/sql-database-paas-overview.md) nabízí databázi jako službu. Pomocí SQL Database nemáte přístup k počítačům, které hostují vaše databáze. Úplné porovnání najdete v tématu Volba [cloudového SQL Server možnosti: databáze Azure SQL (PaaS) nebo SQL Server na virtuálních počítačích Azure (IaaS)](../../azure-sql-iaas-vs-paas-what-is-overview.md).

1. **Návody nainstalovat SQL Data Tools na mém virtuálním počítači Azure?**

    Stáhněte si a nainstalujte nástroje SQL Data Tools z [Microsoft SQL Server Data Tools – Business Intelligence for Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313).

1. **Podporují se na virtuálních počítačích s SQL Server distribuované transakce s MSDTC?**
   
    Ano. Místní služba DTC je podporovaná pro SQL Server 2016 SP2 a vyšší. Avšak aplikace musí být testovány při použití skupin dostupnosti Always On, protože transakce probíhající během převzetí služeb při selhání se nezdaří a musí se opakovat. Služba DTC (CLUSTERED DTC) je dostupná od Windows serveru 2019. 

## <a name="sql-server-iaas-agent-extension"></a>Rozšíření agenta SQL Server IaaS

1. **Mám zaregistrovat SQL Server virtuální počítač zřízený z SQL Server Image v Azure Marketplace?**

   No. Microsoft automaticky registruje virtuální počítače zřízené z SQL Server imagí v Azure Marketplace. Registrace s rozšířením se vyžaduje jenom v případě, že se virtuální počítač *nezřídil z* SQL Server imagí v Azure Marketplace a SQL Server byl samoobslužně nainstalován.

1. **Je k dispozici rozšíření agenta SQL IaaS pro všechny zákazníky?** 

   Ano. Zákazníci by měli své SQL Server virtuálních počítačů registrovat s příponou, pokud nepoužívali SQL Server Image ze Azure Marketplace a místo toho SQL Server, nebo pokud se vlastní VHD nainstalují. Virtuální počítače vlastněné všemi typy předplatných (Direct, smlouva Enterprise a Cloud Solution Provider) se můžou registrovat pomocí rozšíření agenta SQL IaaS.

1. **Jaký je výchozí režim správy při registraci pomocí rozšíření agenta SQL IaaS?**

   Výchozí režim správy při registraci v rozšíření agenta SQL IaaS je *odlehčený*. Pokud není při registraci v rozšíření nastavená vlastnost SQL Server Management, režim se nastaví jako odlehčený a vaše služba SQL Server se nerestartuje. Doporučuje se nejprve v jednoduchém režimu zaregistrovat s rozšířením agenta SQL IaaS a pak v časovém intervalu pro správu a údržbu provést upgrade na úplný. Podobně je výchozí Správa také odlehčená při použití [funkce automatické registrace](sql-agent-extension-automatic-registration-all-vms.md).

1. **Jaké jsou požadavky pro registraci pomocí rozšíření agenta SQL IaaS?**

   Nejsou k dispozici žádné požadavky k registraci s rozšířením agenta SQL IaaS, než je na virtuálním počítači nainstalováno SQL Server. Všimněte si, že pokud je rozšíření agenta SQL IaaS nainstalované v plném režimu, SQL Server služba se restartuje, takže to uděláte během doporučeného časového období údržby.

1. **Provede se registrace pomocí rozšíření agenta SQL IaaS instalace agenta na mém virtuálním počítači?**

   Ano, registrace pomocí rozšíření agenta SQL IaaS v režimu úplné správy nainstaluje agenta do virtuálního počítače. Registruje se v odlehčeném nebo neagentovém režimu. 

   Registrace pomocí rozšíření agenta SQL IaaS ve zjednodušeném režimu kopíruje jenom *binární soubory* rozšíření agenta SQL IaaS do virtuálního počítače, ale neinstaluje agenta. Tyto binární soubory se pak použijí k instalaci agenta, když se režim správy upgraduje na úplný.


1. **Provede se registrace na svém virtuálním počítači pomocí SQL Server IaaS pro restartování rozšíření agenta SQL?**

   Závisí na režimu zadaném během registrace. Je-li zadán režim Lightweight nebo unagent, služba SQL Server nebude restartována. Pokud ale nastavíte režim správy jako plný, služba SQL Server se restartuje. Funkce automatické registrace registruje virtuální počítače s SQL Server v jednoduchém režimu, pokud verze Windows serveru není 2008. v takovém případě se SQL Server virtuální počítač zaregistruje v režimu bez agenta. 

1. **Jaký je rozdíl mezi režimy jednoduchého a neagent správy při registraci v rozšíření agenta SQL IaaS?** 

   Režim správy neagentů je jediným dostupným režimem správy pro SQL Server 2008 a SQL Server 2008 R2 v systému Windows Server 2008. Pro všechny novější verze Windows serveru jsou dva dostupné režimy spravovatelnosti zjednodušené a úplné. 

   Režim neagentů vyžaduje, aby byly vlastnosti SQL Server verze a edice nastavené zákazníkem. Odlehčený režim dotazuje virtuální počítač na nalezení verze a edice instance SQL Server.

1. **Můžu se zaregistrovat s rozšířením agenta SQL IaaS bez zadání typu licence SQL Server?**

   No. Typ licence SQL Server není volitelnou vlastností při registraci s rozšířením agenta SQL IaaS. Musíte nastavit typ licence SQL Server jako průběžné platby nebo Zvýhodněné hybridní využití Azure při registraci pomocí rozšíření agenta SQL IaaS ve všech režimech spravovatelnosti (agent, odlehčené a úplné). Pokud máte nainstalovanou bezplatnou verzi SQL Server, jako je například vývojář nebo zkušební edice, musíte se zaregistrovat s průběžnými platbami podle aktuálního využití. Zvýhodněné hybridní využití Azure je k dispozici jenom pro placené verze SQL Server, jako jsou edice Enterprise a Standard.

1. **Můžu upgradovat rozšíření SQL Server IaaS z režimu bez agenta na režim Full?**

   No. Upgrade režimu spravovatelnosti na úplný nebo lehký není pro režim bez agenta k dispozici. Toto je technické omezení Windows serveru 2008. Nejprve budete muset upgradovat operační systém na Windows Server 2008 R2 nebo novější a pak budete moct upgradovat na režim úplné správy. 

1. **Můžu upgradovat rozšíření SQL Server IaaS z režimu prostého režimu na režim Full?**

   Ano. Upgrade režimu spravovatelnosti z jednoduchého na plný se podporuje prostřednictvím Azure PowerShell nebo Azure Portal. Tím se aktivuje restartování služby SQL Server.

1. **Můžu SQL Server rozšíření IaaS z úplného režimu snížit na režim bez agenta nebo režimu prosté správy?**

   No. Přechod do režimu spravovatelnosti rozšíření SQL Server IaaS se nepodporuje. Režim spravovatelnosti nejde downgradovat z úplného režimu na režim Lightweight nebo unagent a nedá se downgradovat z prostého režimu na režim bez agenta. 

   Pokud chcete změnit režim spravovatelnosti z plné možnosti správy, [zrušte registraci](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) SQL Server virtuálního počítače z rozšíření agenta SQL IaaS vyřazením _prostředku_ virtuálního počítače SQL a opětovným registrem SQL Server virtuálního počítače pomocí rozšíření agenta SQL IaaS v jiném režimu správy.

1. **Můžu se zaregistrovat pomocí rozšíření agenta SQL IaaS z Azure Portal?**

   No. Registrace pomocí rozšíření agenta SQL IaaS není k dispozici v Azure Portal. Registrace pomocí rozšíření agenta SQL IaaS se podporuje jenom pomocí Azure CLI nebo Azure PowerShell. 

1. **Je možné zaregistrovat virtuální počítač s rozšířením agenta SQL IaaS ještě před tím, než se nainstaluje SQL Server?**

   No. Aby se virtuální počítač mohl úspěšně zaregistrovat do rozšíření agenta SQL IaaS, musí mít aspoň jednu instanci SQL Server (databázový stroj). Pokud na virtuálním počítači není žádná SQL Server instance, nový prostředek Microsoft. SqlVirtualMachine bude ve stavu selhání.

1. **Můžu zaregistrovat virtuální počítač s rozšířením agenta SQL IaaS, pokud existuje více instancí SQL Server?**

   Ano, pokud je na virtuálním počítači výchozí instance. Rozšíření agenta SQL IaaS bude registrovat pouze jednu instanci SQL Server (databázový stroj). Rozšíření agenta SQL IaaS bude v případě více instancí registrovat výchozí instanci SQL Server.

1. **Můžu u SQL Server instance clusteru s podporou převzetí služeb při selhání zaregistrovat rozšíření agenta SQL IaaS?**

   Ano. SQL Server instancí clusteru s podporou převzetí služeb při selhání na virtuálním počítači Azure se dá v jednoduchém režimu zaregistrovat s rozšířením agenta SQL IaaS. SQL Server instancí clusteru s podporou převzetí služeb při selhání se ale nedá upgradovat na režim úplné správy.

1. **Můžu zaregistrovat virtuální počítač s rozšířením agenta SQL IaaS, pokud je nakonfigurovaná Skupina dostupnosti Always On?**

   Ano. Neexistují žádná omezení pro registraci instance SQL Server na virtuálním počítači Azure s rozšířením agenta SQL IaaS, pokud se účastníte konfigurace skupiny dostupnosti Always On.

1. **Jaké jsou náklady na registraci pomocí rozšíření agenta SQL IaaS nebo upgrade na úplný režim správy?**

   Žádné K registraci s rozšířením agenta SQL IaaS nebo pomocí některého ze tří režimů správy se neúčtují žádné poplatky. Správa virtuálního počítače s SQL Server s rozšířením je zcela zadarmo. 

1. **Jaký je dopad na výkon při použití různých režimů správy?**

   Při použití režimů nespravovaného *agenta* a *zjednodušené* správy to nemá žádný vliv. Použití *úplného* režimu správy ze dvou služeb, které jsou nainstalovány do operačního systému, má minimální dopad. Dají se monitorovat pomocí Správce úloh a zobrazují se v integrované konzole Windows Services. 

   Názvy těchto dvou služeb:
   - `SqlIaaSExtensionQuery` (Zobrazované jméno- `Microsoft SQL Server IaaS Query Service` )
   - `SQLIaaSExtension` (Zobrazované jméno- `Microsoft SQL Server IaaS Agent` )

1. **Návody odebrat rozšíření?**

   Odeberte rozšíření zrušením [registrace](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) SQL Server virtuálního počítače z rozšíření agenta SQL IaaS. 

## <a name="resources"></a>Zdroje informací

**Virtuální počítače s Windows**:

* [Přehled SQL Server na virtuálním počítači s Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Zřízení SQL Server na virtuálním počítači s Windows](create-sql-vm-portal.md)
* [Migrace databáze na SQL Server na virtuálním počítači Azure](migrate-to-vm-from-sql-server.md)
* [Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Osvědčené postupy výkonu pro SQL Server v Azure Virtual Machines](performance-guidelines-best-practices.md)
* [Modely aplikací a vývojové strategie pro SQL Server v Azure Virtual Machines](application-patterns-development-strategies.md)

**Virtuální počítače se systémem Linux**:

* [Přehled SQL Server na virtuálním počítači se systémem Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Zřízení SQL Server na virtuálním počítači se systémem Linux](../linux/sql-vm-create-portal-quickstart.md)
* [Nejčastější dotazy (Linux)](../linux/frequently-asked-questions-faq.md)
* [Dokumentace k SQL Server on Linux](/sql/linux/sql-server-linux-overview)
