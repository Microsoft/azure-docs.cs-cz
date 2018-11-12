---
title: Instalace úloh elastické databáze | Dokumentace Microsoftu
description: Instalace elastických úloh funkci provede.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 09/14/2018
ms.openlocfilehash: c2fb3ae20ea2b6ba71408a734c1e0c82cbeb699c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51233571"
---
# <a name="installing-elastic-database-jobs-overview"></a>Přehled instalace úloh elastické databáze

[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]



[**Úlohy elastic Database** ](sql-database-elastic-jobs-overview.md) lze nainstalovat pomocí Powershellu nebo na webu Azure portal. Můžete získat přístup k vytváření a Správa úloh pomocí rozhraní API prostředí PowerShell, pouze v případě, že balíček nainstalujete, prostředí PowerShell. Kromě toho rozhraní API prostředí PowerShell poskytuje výrazně víc funkcí než na portálu v daném okamžiku.

Pokud jste již nainstalovali **úlohy elastické databáze** prostřednictvím portálu ze stávajícího **elastického fondu**, nejnovější verze prostředí Powershell preview obsahuje skripty pro upgrade existující instalace. Důrazně doporučujeme upgradovat na nejnovější verzi vaší instalace **úlohy elastické databáze** komponenty, abyste mohli využívat nové funkce, které jsou zveřejňovány prostřednictvím rozhraní API Powershellu.

## <a name="prerequisites"></a>Požadavky
* Předplatné Azure. Bezplatná zkušební verze, najdete v části [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Azure PowerShell Nainstalujte nejnovější verzi pomocí [instalačního programu webové platformy](https://go.microsoft.com/fwlink/p/?linkid=320376). Podrobné informace najdete v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).
* [Nástroje příkazového řádku NuGet](https://nuget.org/nuget.exe) se používá k instalaci balíčku úlohy elastické databáze. Další informace naleznete v tématu http://docs.nuget.org/docs/start-here/installing-nuget.

## <a name="download-and-import-the-elastic-database-jobs-powershell-package"></a>Stažení a import balíčků Elastic Database úlohy Powershellu
1. Spusťte okno příkazového řádku Microsoft Azure Powershellu a přejděte do adresáře, kam jste stáhli nástroje příkazového řádku NuGet (nuget.exe).
2. Stáhnout a naimportovat **úlohy elastické databáze** balíček do aktuálního adresáře pomocí následujícího příkazu:
   
        PS C:\>.\nuget install Microsoft.Azure.SqlDatabase.Jobs -prerelease
   
    **Úlohy elastické databáze** soubory jsou umístěny v místním adresáři ve složce s názvem **Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x** kde *x.x.xxxx.x* odráží číslo verze. Rutiny Powershellu (včetně klienta knihovny DLL debuggle) jsou umístěné v **tools\ElasticDatabaseJobs** podadresář a skripty prostředí PowerShell k instalaci, upgradu a odinstalaci taky umístěné ve **nástroje** podadresáře.
3. Přejděte na nástroje podadresář ve složce Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x zadáním disk cd nástroje, například:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

4. Spusťte skript.\InstallElasticDatabaseJobsCmdlets.ps1 zkopírovat do $home\Documents\WindowsPowerShell\Modules ElasticDatabaseJobs adresáře. To také automaticky importuje modul pro použití, například:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobsCmdlets.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobsCmdlets.ps1

## <a name="install-the-elastic-database-jobs-components-using-powershell"></a>Nainstalujte součásti úlohy elastické databáze pomocí Powershellu
1. Spusťte příkazové okno Microsoft Azure Powershellu a přejděte na \tools podadresář ve složce Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x: zadejte cd \tools
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

2. Spustí.\InstallElasticDatabaseJobs.ps1 skript prostředí PowerShell a zadejte hodnoty pro jeho požadované proměnné. Tento skript vytvoří komponent popsaných v [úlohy elastické databáze komponenty a ceny](sql-database-elastic-jobs-overview.md#components-and-pricing) spolu s konfigurací cloudové služby Azure odpovídajícím způsobem používat závislé součásti.

        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobs.ps1

Když spustíte tento příkaz, okno se otevře s žádostí o **uživatelské jméno** a **heslo**. Toto není vaše přihlašovací údaje Azure, zadejte uživatelské jméno a heslo, které se budou přihlašovací údaje správce, kterou chcete vytvořit pro nový server.

Pro požadované nastavení, lze upravit parametry uvedené v této ukázkové volání. Následující poskytuje další informace o chování každého parametru:

<table style="width:100%">
  <tr>
    <th>Parametr</th>
    <th>Popis</th>
  </tr>

<tr>
    <td>ResourceGroupName</td>
    <td>Poskytuje název skupiny prostředků Azure vytvořené tak, aby obsahovala nově vytvořený komponenty Azure. Tento parametr výchozí hodnotu "__ElasticDatabaseJob". Nedoporučujeme tuto hodnotu změnit.</td>
    </tr>

</tr>

    <tr>
    <td>ResourceGroupLocation</td>
    <td>Poskytuje umístění Azure, který má být použit pro nově vytvořený komponenty Azure. Tento parametr nastaví jako výchozí umístění střed USA.</td>
</tr>

<tr>
    <td>ServiceWorkerCount</td>
    <td>Obsahuje počet pracovních procesů služby k instalaci. Tento parametr výchozí hodnotu 1. Vyšší počet pracovních procesů lze použít pro horizontální navýšení kapacity služby a k zajištění vysoké dostupnosti. Doporučujeme použít pro nasazení, které vyžadují vysokou dostupnost služby "2".</td>
    </tr>

</tr>
    <tr>
    <td>ServiceVmSize</td>
    <td>Poskytuje velikost virtuálního počítače pro použití v rámci cloudové služby. Výchozí hodnota tohoto parametru A0. Hodnoty parametrů A0/A1 a A2 a A3 nepřijmou, které způsobují roli pracovního procesu, který chcete použít pro velikost ExtraSmall/malé/střední/velký v uvedeném pořadí. Další informace o velikosti rolí pracovního procesu, viz Fo [úlohy elastické databáze komponenty a ceny](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</tr>
    <tr>
    <td>SqlServerDatabaseSlo</td>
    <td>Poskytuje výpočetního prostředí pro edici Standard. Tento parametr výchozí hodnotu S0. Hodnoty parametru findwebconfigpath S0/S1/S2/S3/S4/S6/S9 a S12 na úrovni Standard jsou přijímány, které způsobují Azure SQL Database k použití příslušného výpočetního prostředí. Další informace o velikostech výpočetních SQL Database, najdete v části [úlohy elastické databáze komponenty a ceny](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</tr>
    <tr>
    <td>SqlServerAdministratorUserName</td>
    <td>Poskytuje uživatelské jméno správce pro nově vytvořený server Azure SQL Database. Pokud není zadán, otevře se okno přihlašovacích údajů prostředí PowerShell na výzvu k zadání přihlašovacích údajů.</td>
</tr>

</tr>
    <tr>
    <td>SqlServerAdministratorPassword</td>
    <td>Poskytuje heslo správce pro nově vytvořený server Azure SQL Database. Pokud není zadaný, otevře se okno přihlašovacích údajů prostředí PowerShell na výzvu k zadání přihlašovacích údajů.</td>
</tr>
</table>

Pro systémy, které se zaměřují s velkým počtem úloh běžících paralelně s velkým počtem databází, se doporučuje zadejte parametry, například: - ServiceWorkerCount 2 - ServiceVmSize A2 - SqlServerDatabaseSlo S2.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\InstallElasticDatabaseJobs.ps1 -ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2

## <a name="update-an-existing-elastic-database-jobs-components-installation-using-powershell"></a>Aktualizace stávající Elastic Database úlohy součásti instalace pomocí Powershellu
**Úlohy elastic Database** je aktualizovat v rámci stávající instalace pro škálování a vysokou dostupnost. Díky tomuto procesu si pro budoucí aktualizace kódu služby bez nutnosti vyřadit a znovu vytvořit databázi ovládacího prvku. Tento proces je také možné v rámci stejné verze upravit velikost virtuálního počítače. službu nebo počet pracovních procesů serveru.

Velikost virtuálního počítače při instalaci aktualizace, spusťte následující skript s parametry aktualizovány hodnoty podle vašeho výběru.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\UpdateElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\UpdateElasticDatabaseJobs.ps1 -ServiceVmSize A1 -ServiceWorkerCount 2

<table style="width:100%">
  <tr>
  <th>Parametr</th>
  <th>Popis</th>
</tr>

  <tr>
    <td>ResourceGroupName</td>
    <td>Určuje název skupiny prostředků Azure při byly původně nainstalované komponenty úlohy elastické databáze. Tento parametr výchozí hodnotu "__ElasticDatabaseJob". Protože nedoporučuje se tuto hodnotu změnit, není nutné tento parametr zadán.</td>
    </tr>
</tr>

</tr>

  <tr>
    <td>ServiceWorkerCount</td>
    <td>Obsahuje počet pracovních procesů služby k instalaci.  Tento parametr výchozí hodnotu 1.  Vyšší počet pracovních procesů lze použít pro horizontální navýšení kapacity služby a k zajištění vysoké dostupnosti.  Doporučujeme použít pro nasazení, které vyžadují vysokou dostupnost služby "2".</td>
</tr>

</tr>

    <tr>
    <td>ServiceVmSize</td>
    <td>Poskytuje velikost virtuálního počítače pro použití v rámci cloudové služby. Výchozí hodnota tohoto parametru A0. Hodnoty parametrů A0/A1 a A2 a A3 nepřijmou, které způsobují roli pracovního procesu, který chcete použít pro velikost ExtraSmall/malé/střední/velký v uvedeném pořadí. Další informace o velikosti rolí pracovního procesu, viz Fo [úlohy elastické databáze komponenty a ceny](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</table>

## <a name="install-the-elastic-database-jobs-components-using-the-portal"></a>Nainstalujte součásti úlohy elastické databáze pomocí portálu
Jakmile budete mít [vytvoření elastického fondu](sql-database-elastic-pool-manage-portal.md), můžete nainstalovat **úlohy elastické databáze** komponenty umožňují provádění úloh správy na každou databázi v elastickém fondu. Na rozdíl od při použití **úlohy elastické databáze** rozhraní API Powershellu, rozhraní portálu je momentálně omezené jenom na pouze spuštěním s existující fond.

**Odhadovaný čas dokončení:** 10 minut.

1. V zobrazení řídicí panel elastického fondu pomocí [webu Azure portal](https://portal.azure.com/#) , klikněte na tlačítko **vytvořit úlohu**.
2. Pokud vytváříte úlohu poprvé, musíte nainstalovat **úlohy elastické databáze** kliknutím **podmínky verze PREVIEW**.
3. Přijměte podmínky kliknutím na zaškrtávací políčko.
4. V zobrazení "Instalace služby", klikněte na tlačítko **přihlašovací údaje k úloze**.
   
    ![Instalace služby][1]
5. Zadejte uživatelské jméno a heslo správce databáze Jako součást instalace je vytvořen nový server Azure SQL Database. V rámci tohoto nového serveru novou databázi, označovanou jako databáze ovládací prvek se a použít tak, aby obsahovala metadata pro úlohy elastické databáze. Uživatelské jméno a heslo, které vytvořili tady se používají pro účely protokolování databázi správy. Samostatné přihlašovací údaje se používá pro spuštění skriptu s databázemi, v rámci fondu.
   
    ![Vytvořte uživatelské jméno a heslo][2]
6. Klikněte na tlačítko OK. Součásti jsou vytvořeny pro vás za několik minut v novém [skupiny prostředků](../azure-resource-manager/resource-group-overview.md). Novou skupinu prostředků je připnutý na úvodní panel, jak je znázorněno níže. Úlohy po vytvoření, elastic database (cloudové služby, SQL Database, Service Bus a úložiště) se vytvoří ve skupině.
   
    ![Skupina prostředků v úvodní panel][3]
7. Pokud se pokusíte vytvořit nebo spravovat úlohu, když je instalace úloh elastické databáze, při zadávání **pověření** se zobrazí následující zpráva.
   
    ![Stále probíhá nasazení][4]

Pokud odinstalaci je potřeba, odstraňte skupinu prostředků. Zobrazit [odinstalování součásti úlohy elastické databáze](sql-database-elastic-jobs-uninstall.md).

## <a name="next-steps"></a>Další postup
Zkontrolujte přihlašovací údaj se příslušná oprávnění pro spuštění skriptu se vytvoří pro každou databázi ve skupině pro další informace najdete v tématu [zabezpečení služby SQL Database](sql-database-manage-logins.md).
Zobrazit [vytváření a správa úlohy Elastic Database](sql-database-elastic-jobs-create-and-manage.md) začít.

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/not-done.png
