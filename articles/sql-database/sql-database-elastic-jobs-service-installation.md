---
title: Instalace úlohy elastické databáze | Microsoft Docs
description: Provede procesem instalace funkce elastické úlohy.
services: sql-database
manager: craigg
author: ddove
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 4aeb18a4cdd0fc115c0d604797cf60c867455337
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="installing-elastic-database-jobs-overview"></a>Instalace úlohy elastické databáze – přehled
[**Elastické databáze úlohy** ](sql-database-elastic-jobs-overview.md) lze nainstalovat pomocí prostředí PowerShell nebo prostřednictvím portálu Azure. Můžete získat přístup k vytvářet a spravovat úlohy pomocí rozhraní API prostředí PowerShell, pouze v případě, že budete instalovat balíček prostředí PowerShell. Kromě toho rozhraní API prostředí PowerShell poskytuje výrazně víc funkcí než portálu v daném okamžiku.

Pokud jste již nainstalovali **úlohy elastické databáze** prostřednictvím portálu ze stávajícího **elastický fond**, nejnovější prostředí Powershell preview zahrnuje skripty, které upgradovat existující instalaci. Důrazně doporučujeme pro upgrade na nejnovější instalace **úlohy elastické databáze** součásti, aby bylo možné využívat nové funkce, které jsou zveřejňovány prostřednictvím rozhraní API prostředí PowerShell.

## <a name="prerequisites"></a>Požadavky
* Předplatné Azure. Bezplatná zkušební verze, najdete v části [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Azure Powershell Nainstalujte nejnovější verzi pomocí [instalačního programu webové platformy](http://go.microsoft.com/fwlink/p/?linkid=320376). Podrobné informace najdete v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).
* [Nástroj příkazového řádku NuGet](https://nuget.org/nuget.exe) slouží k instalaci balíčku úlohy elastické databáze. Další informace najdete v tématu http://docs.nuget.org/docs/start-here/installing-nuget.

## <a name="download-and-import-the-elastic-database-jobs-powershell-package"></a>Stahování a import balíčku prostředí PowerShell úlohy elastické databáze
1. Spusťte příkazové okno prostředí PowerShell Microsoft Azure a přejděte do adresáře, kam jste stáhli Nástroj příkazového řádku NuGet (nuget.exe).
2. Stahování a import **úlohy elastické databáze** balíček do aktuální adresář pomocí následujícího příkazu:
   
        PS C:\>.\nuget install Microsoft.Azure.SqlDatabase.Jobs -prerelease
   
    **Úlohy elastické databáze** soubory jsou umístěny do místního adresáře do složky s názvem **Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x** kde *x.x.xxxx.x* zobrazuje číslo verze. Rutiny prostředí PowerShell (včetně klienta knihoven DLL) jsou umístěné v **tools\ElasticDatabaseJobs** podadresář a skriptů prostředí PowerShell k instalaci, upgrade a odinstalaci jsou taky umístěné ve **nástroje** podadresář.
3. Přejděte do nástroje podadresář ve složce Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x zadáním disk cd nástroje, například:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

4. Spusťte skript.\InstallElasticDatabaseJobsCmdlets.ps1 chcete zkopírovat do adresáře ElasticDatabaseJobs do $home\Documents\WindowsPowerShell\Modules. To také automaticky importuje modul pro použití, například:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobsCmdlets.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobsCmdlets.ps1

## <a name="install-the-elastic-database-jobs-components-using-powershell"></a>Nainstalujte komponenty úlohy elastické databáze pomocí prostředí PowerShell
1. Spusťte příkazové okno Microsoft Azure PowerShell a přejděte na \tools podadresář ve složce Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x: Zadejte \tools disku cd
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

2. Spustit.\InstallElasticDatabaseJobs.ps1 skript prostředí PowerShell a zadejte hodnoty pro její požadované proměnné. Tento skript vytvoří komponent popsaných v [úlohy elastické databáze součásti a ceny](sql-database-elastic-jobs-overview.md#components-and-pricing) společně s konfigurací Azure Cloud Service odpovídajícím způsobem používat závislé součásti.

        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobs.ps1

Když spustíte tento příkaz, okno otevře požadující **uživatelské jméno** a **heslo**. Toto není vaše přihlašovací údaje Azure, zadejte uživatelské jméno a heslo, které budou přihlašovací údaje správce, kterou chcete vytvořit pro nový server.

Parametry zadané na toto ukázkové volání lze upravit pro požadovaná nastavení. Následující poskytuje další informace o chování jednotlivých parametrů:

<table style="width:100%">
  <tr>
    <th>Parametr</th>
    <th>Popis</th>
  </tr>

<tr>
    <td>ResourceGroupName</td>
    <td>Poskytuje název skupiny prostředků Azure k vytvoření, která obsahují nově vytvořený Azure komponenty. Tento parametr výchozí "__ElasticDatabaseJob". Není doporučeno tuto hodnotu změnit.</td>
    </tr>

</tr>

    <tr>
    <td>ResourceGroupLocation</td>
    <td>Poskytuje umístění Azure, který se má použít pro nově vytvořený Azure součásti. Tento parametr výchozí umístění střed USA.</td>
</tr>

<tr>
    <td>ServiceWorkerCount</td>
    <td>Obsahuje počet pracovních procesů služby pro instalaci. Tento parametr výchozí hodnotu 1. Vyšší počet pracovních procesů lze škálovat službu a pro zajištění vysoké dostupnosti. Doporučujeme použít pro nasazení, která vyžadují vysokou dostupnost služby "2".</td>
    </tr>

</tr>
    <tr>
    <td>ServiceVmSize</td>
    <td>Poskytuje velikost virtuálního počítače pro použití v rámci cloudové služby. Tento parametr výchozí A0. Hodnoty parametrů A0 nebo A1 nebo A2 nebo A3, jsou přijaty které způsobit role pracovního procesu sloužící velikost: mimořádně malý nebo malá nebo střední nebo velké v uvedeném pořadí. Další informace o velikosti role pracovního procesu, viz Fo [úlohy elastické databáze součásti a ceny](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</tr>
    <tr>
    <td>SqlServerDatabaseSlo</td>
    <td>Poskytuje cíle na úrovni služby pro Standard edition. Tento parametr výchozí hodnotu S0. Hodnoty parametru S0/S1 nebo S2/S3 nebo S4/S6/S9/S12, které způsobují databáze SQL Azure používat příslušné SLO jsou přijaty. Další informace o slo databáze SQL najdete v tématu [úlohy elastické databáze součásti a ceny](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</tr>
    <tr>
    <td>SqlServerAdministratorUserName</td>
    <td>Poskytuje uživatelské jméno správce pro nově vytvořený serveru Azure SQL Database. Není-li zadána, bude vyžadovat přihlašovací údaje otevřete okno přihlašovací údaje prostředí PowerShell.</td>
</tr>

</tr>
    <tr>
    <td>SqlServerAdministratorPassword</td>
    <td>Poskytuje heslo správce pro nově vytvořený serveru Azure SQL Database. Když není zadaná, bude vyžadovat přihlašovací údaje otevřete okno přihlašovací údaje prostředí PowerShell.</td>
</tr>
</table>

Pro systémy, které cílí má velký počet úloh spuštěných současně pro velký počet databází, se doporučuje zadejte parametry, jako například: - ServiceWorkerCount 2 - ServiceVmSize A2 - SqlServerDatabaseSlo S2.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\InstallElasticDatabaseJobs.ps1 -ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2

## <a name="update-an-existing-elastic-database-jobs-components-installation-using-powershell"></a>Aktualizace stávající elastické databáze úlohy součásti instalace pomocí prostředí PowerShell
**Elastické databáze úlohy** můžete aktualizovat v rámci existující instalace pro škálování a vysokou dostupností. Tento proces umožňuje pro budoucí upgrady kódu služby bez nutnosti vyřadit a znovu vytvořte databázi ovládacího prvku. Tento proces lze také v rámci stejné verze a změňte velikost virtuálního počítače služby nebo počet pracovního procesu serveru.

Pokud chcete aktualizovat velikost virtuálního počítače instalace, spusťte následující skript s parametry aktualizovány na hodnoty podle svého výběru.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\UpdateElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\UpdateElasticDatabaseJobs.ps1 -ServiceVmSize A1 -ServiceWorkerCount 2

<table style="width:100%">
  <tr>
  <th>Parametr</th>
  <th>Popis</th>
</tr>

  <tr>
    <td>ResourceGroupName</td>
    <td>Určuje název skupiny prostředků Azure používá při byly původně nainstalované komponenty úlohy elastické databáze. Tento parametr výchozí "__ElasticDatabaseJob". Vzhledem k tomu, že není doporučeno tuto hodnotu změnit, není nutné, zadejte tento parametr.</td>
    </tr>
</tr>

</tr>

  <tr>
    <td>ServiceWorkerCount</td>
    <td>Obsahuje počet pracovních procesů služby pro instalaci.  Tento parametr výchozí hodnotu 1.  Vyšší počet pracovních procesů lze škálovat službu a pro zajištění vysoké dostupnosti.  Doporučujeme použít pro nasazení, která vyžadují vysokou dostupnost služby "2".</td>
</tr>

</tr>

    <tr>
    <td>ServiceVmSize</td>
    <td>Poskytuje velikost virtuálního počítače pro použití v rámci cloudové služby. Tento parametr výchozí A0. Hodnoty parametrů A0 nebo A1 nebo A2 nebo A3, jsou přijaty které způsobit role pracovního procesu sloužící velikost: mimořádně malý nebo malá nebo střední nebo velké v uvedeném pořadí. Další informace o velikosti role pracovního procesu, viz Fo [úlohy elastické databáze součásti a ceny](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</table>

## <a name="install-the-elastic-database-jobs-components-using-the-portal"></a>Nainstalujte komponenty úlohy elastické databáze pomocí portálu
Jakmile máte [vytvoření fondu elastické databáze](sql-database-elastic-pool-manage-portal.md), můžete nainstalovat **úlohy elastické databáze** součásti povolit spouštění úlohy správy na každou databázi v elastickém fondu. Na rozdíl od při použití **úlohy elastické databáze** rozhraní API prostředí PowerShell, rozhraní portálu je momentálně omezené do pouze prováděných na existující fond.

**Odhadovaný čas dokončení:** 10 minut.

1. Z elastického fondu pomocí zobrazení řídicího panelu [portál Azure](https://portal.azure.com/#) , klikněte na tlačítko **vytvořit úlohu**.
2. Pokud vytváříte úlohu poprvé, musíte nainstalovat **úlohy elastické databáze** kliknutím **podmínky**.
3. Přijměte podmínky kliknutím na zaškrtávací políčko.
4. V zobrazení "Instalace služby", klikněte na **úlohy pověření**.
   
    ![Instalace služby][1]
5. Zadejte uživatelské jméno a heslo pro správce databáze Jako součást instalace se vytvoří nový server Azure SQL Database. V rámci tohoto serveru je novou databázi, označuje jako databázi řízení vytvořit a používat tak, aby obsahovala metadata pro úlohy elastické databáze. Uživatelské jméno a heslo vytvořená zde se používají pro účely přihlášení k databázi řízení. Samostatné přihlašovací údaje se používá pro spuštění skriptu s databázemi, v rámci fondu.
   
    ![Vytvořte uživatelské jméno a heslo][2]
6. Klikněte na tlačítko OK. Součásti jsou vytvořeny pro vás za pár minut v nové [skupiny prostředků](../azure-resource-manager/resource-group-overview.md). Novou skupinu prostředků je připnutý na úvodní panel, jak je uvedeno níže. Úlohy po vytvoření, elastické databáze (Cloudová služba, databáze SQL, Service Bus a úložiště) jsou vytvořeny ve skupině.
   
    ![Skupina prostředků v Tabule start][3]
7. Pokud se pokusíte vytvořit ani spravovat úlohu při instalaci úlohy elastické databáze, pokud poskytuje **pověření** zobrazí se následující zpráva.
   
    ![Stále probíhá nasazení][4]

Pokud odinstalaci je potřeba, odstraňte skupinu prostředků. V tématu [postup odinstalace komponenty úlohy elastické databáze](sql-database-elastic-jobs-uninstall.md).

## <a name="next-steps"></a>Další postup
Zkontrolujte přihlašovací údaj se příslušná oprávnění pro spuštění skriptu se vytvoří na každou databázi ve skupině, další informace najdete v tématu [zabezpečení databáze SQL](sql-database-manage-logins.md).
V tématu [vytváření a Správa úloh elastické databáze](sql-database-elastic-jobs-create-and-manage.md) začít pracovat.

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/not-done.png
