---
title: Automatické ladění e-mailová oznámení s postupy Průvodce – Azure SQL Database | Dokumentace Microsoftu
description: Povolení e-mailová oznámení pro ladění automatické dotazů Azure SQL Database.
services: sql-database
author: danimir
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: v-daljep
ms.openlocfilehash: 546a21c4be13fdd0d06e29253d73c6b67cb8c91c
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2018
ms.locfileid: "46498466"
---
# <a name="email-notifications-for-automatic-tuning"></a>E-mailová oznámení pro automatické ladění

SQL Database, Azure SQL Database jsou generovány doporučení pro vyladění [automatické ladění](sql-database-automatic-tuning.md). Toto řešení nepřetržitě monitoruje a analyzuje úloh SQL Database poskytuje přizpůsobená doporučení pro každé jednotlivé databáze týkající se vytváření indexů, odstranění indexu a optimalizace plánů spouštění dotazu pro optimalizaci.

SQL Database doporučení automatického ladění můžete zobrazit v [webu Azure portal](sql-database-advisor-portal.md), načtený pomocí [rozhraní REST API](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) volá, nebo pomocí [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) a [ Prostředí PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabaserecommendedaction) příkazy. Tento článek je založen na pomocí Powershellového skriptu se načíst doporučení automatického ladění.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Automatizace e-mailová oznámení pro doporučení automatického ladění

Toto řešení automatizuje odesílání e-mailová oznámení, který obsahuje doporučení automatického ladění. Popisuje řešení se skládá z automatizace pro spuštění skriptu prostředí PowerShell pro načtení pomocí doporučení pro vyladění [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro), a automatizaci plánování pomocí úlohy doručování e-mailu [Microsoft Flow ](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Vytvoření účtu Azure Automation.

Pomocí Azure Automation, prvním krokem je vytvoření účtu automation a konfigurace s prostředky Azure pro účely provádění skriptu prostředí PowerShell. Další informace o službě Azure Automation a možnosti najdete v tématu [Začínáme s Azure automation](https://docs.microsoft.com/azure/automation/automation-offering-get-started).

Postupujte podle těchto kroků můžete vytvořit účet Azure Automation prostřednictvím metody výběru a konfigurace automatizace aplikace z Marketplace:

- Přihlaste se na webu Azure portal
- Klikněte na "**+ vytvořit prostředek**" v levém horním rohu
- Vyhledejte "**automatizace**" (stisknutím klávesy enter)
- Klikněte na tlačítko v aplikaci automatizace ve výsledcích hledání

![Přidání služby Azure automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

- Jednou v podokně "Vytvoření účet služby Automation" klikněte na "**vytvořit**"
- Vyplnění požadovaných informací: Zadejte název pro tento účet automation, vyberte předplatné Azure ID a Azure prostředky použitého pro spuštění skriptu prostředí PowerShell
- Pro "**vytvoření účtu Azure spustit jako**" možnost, vyberte **Ano** nakonfigurovat typ účtu, pod kterou Powershellu skript se spustí pomocí služby Azure Automation. Další informace o typech účtů najdete v tématu [účet Spustit jako](https://docs.microsoft.com/azure/automation/automation-create-runas-account)
- Vytvoření účtu automation uzavřít po kliknutí na **Create**

> [!TIP]
> Zaznamenejte název účtu služby Azure Automation, ID předplatného a prostředky (například kopírování a vkládání do poznámkového bloku) přesně tak, jak zadat při vytváření aplikací automatizace. Tyto informace později budete potřebovat.
>

Pokud máte několik předplatných Azure, pro které chcete sestavení stejné služby automation, musíte tento postup opakujte pro vaše předplatná.

## <a name="update-azure-automation-modules"></a>Aktualizace modulů Azure Automation.

Skript prostředí PowerShell k načtení automatického ladění doporučení používá [Get-AzureRmResource](https://docs.microsoft.com/powershell/module/AzureRM.Resources/Get-AzureRmResource) a [Get-AzureRmSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlDatabaseRecommendedAction) příkazy aktualizací modulů Azure verze 4 a novější.

Postupujte podle těchto kroků provedete aktualizaci modulů Azure Powershellu:

- Přístup k podoknu aplikace automatizace a vyberte "**moduly**" v levé nabídce (přejděte dolů je tato položka nabídky v části sdílené prostředky).
- V podokně moduly, klikněte na "**aktualizace modulů Azure**" v horní a počkejte, až se zobrazí zpráva "moduly Azure se aktualizovaly". Dokončení tohoto procesu může trvat několik minut.

![Aktualizace modulů Azure automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-02.png)

Požadovaná verze azurerm.resources zavedla a AzureRM.Sql moduly musí být verze 4 a vyšší.

## <a name="create-azure-automation-runbook"></a>Vytvoření Runbooku Azure Automation

Dalším krokem je vytvoření sady Runbook ve službě Azure Automation, které se nachází skript prostředí PowerShell pro načítání doporučení pro optimalizaci.

Postupujte podle těchto kroků a vytvořte novou sadu runbook Azure Automation:

- Přístup k účtu Azure Automation, který jste vytvořili v předchozím kroku
- Jednou v podokně účtu automation, klikněte na "**sady Runbook**" položky nabídky na levé straně, chcete-li vytvořit novou sadu runbook Azure Automation pomocí skriptu prostředí PowerShell. Další informace o vytváření runbooků služeb automatizace, najdete v článku [vytváří se nový runbook](../automation/automation-creating-importing-runbook.md).
- Pokud chcete přidat novou sadu runbook, klikněte na "**+ přidat runbook**" možnost nabídky a potom kliknout na "**rychlé vytvoření – vytvořit nový runbook**".
- V podokně sady Runbook, zadejte název vaší sady runbook (pro účely tohoto příkladu "**AutomaticTuningEmailAutomation**" se používá), vyberte typ runbooku jako **Powershellu** a napište popis Tato sada runbook popisující její účel.
- Klikněte na **vytvořit** tlačítko Dokončit vytváření nové sady runbook

![Přidat runbook Azure automation.](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Použijte následující postup načtení skriptu prostředí PowerShell v runbooku vytvořit:

- Uvnitř "**upravit Powershellový Runbook**"podokně, vyberte možnost"**sady RUNBOOK**" v nabídce stromu a rozbalte položku zobrazení, dokud se nezobrazí název sady runbook (v tomto příkladu " **AutomaticTuningEmailAutomation**"). Vyberte tuto sadu runbook.
- Na prvním řádku "upravit Powershellový Runbook" (počínaje číslem 1) kopírování a vkládání následující kód skriptu prostředí PowerShell. Tento skript Powershellu se poskytuje jako-je vám pomůžou začít. Upravte skript tak suite vašim potřebám.

V záhlaví dodaný skript Powershellu, budete muset nahradit `<SUBSCRIPTION_ID_WITH_DATABASES>` s ID vašeho předplatného Azure. Zjistěte, jak načíst ID vašeho předplatného Azure, najdete v článku [získání vaší GUID předplatného Azure](https://blogs.msdn.microsoft.com/mschray/2016/03/18/getting-your-azure-subscription-guid-new-portal/).

V případě několika předplatných přidáním jako oddělených čárkou pro vlastnost "$subscriptions" v hlavičce skriptu.

```PowerShell
# PowerShell script to retrieve Azure SQL Database Automatic tuning recommendations.
#
# Provided “as-is” with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID 
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {    
    Select-AzureRmSubscription -SubscriptionId $subscriptionId    
    $rgs = Get-AzureRmResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzureRmResource -ResourceGroupName $rgname -ResourceType $resourceType    

            # Loop through all databases
            # Extract resource groups, servers and databases
            foreach ($resource in $resources) {
                $resourceId = $resource.ResourceId
                if ($resourceId -match ".*RESOURCEGROUPS/(?<content>.*)/PROVIDERS.*") {
                    $ResourceGroupName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*SERVERS/(?<content>.*)/DATABASES.*") {
                    $ServerName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*/DATABASES/(?<content>.*)") {
                    $DatabaseName = $matches['content']
                } else {
                    continue 
                }

                # Skip if master
                if ($DatabaseName -eq "master") {
                    continue
                }

                # Loop through all Automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzureRmSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
                    foreach ($r in $recs) {
                        if ($r.State.CurrentValue -eq "Active") {
                            $object = New-Object -TypeName PSObject
                            $object | Add-Member -Name 'SubscriptionId' -MemberType Noteproperty -Value $subscriptionId
                            $object | Add-Member -Name 'ResourceGroupName' -MemberType Noteproperty -Value $r.ResourceGroupName
                            $object | Add-Member -Name 'ServerName' -MemberType Noteproperty -Value $r.ServerName
                            $object | Add-Member -Name 'DatabaseName' -MemberType Noteproperty -Value $r.DatabaseName
                            $object | Add-Member -Name 'Script' -MemberType Noteproperty -Value $r.ImplementationDetails.Script
                            $results += $object
                        }
                    }
                }                
            }
        }
    }
}

# Format and output results for the email
$table = $results | Format-List
Write-Output $table
```

Klikněte "**Uložit**" tlačítko v pravém horním rohu skript uložte. Pokud jste spokojeni s skriptu, klikněte "**publikovat**" tlačítko Publikovat tuto sadu runbook. 

V podokně hlavní runbook můžete kliknout na "**Start**" tlačítko **testování** skript. Klikněte na "**výstup**" pro zobrazení výsledků spuštění skriptu. Tento výstup se má být obsah e-mailu. Ukázkový výstup ze skriptu můžete vidět na následujícím snímku obrazovky.

![Spuštění zobrazení doporučení s využitím Azure Automation automatického ladění](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Nezapomeňte upravit obsah přizpůsobením skript Powershellu pro vaše potřeby.

Pomocí výše uvedených kroků načtení skriptu prostředí PowerShell načíst doporučení automatického ladění ve službě Azure Automation. Dalším krokem je k automatizaci a naplánovat úlohu doručování e-mailu.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatizace úloh e-mailu s Microsoft Flow

K dokončení řešení, jako poslední krok, vytvoření automatizace toku v Microsoft Flow složený ze tří akcí (úlohy): 

1. "**Azure Automation – vytvoření úlohy**" – používá se ke spuštění skriptu prostředí PowerShell k načtení automatického ladění doporučení v runbooku Azure Automation
2. "**Azure Automation – získat výstup úlohy**" – používá se k načtení výstupu z provedený skript prostředí PowerShell
3. "**Office 365 Outlook – odeslat e-mail**" – používá se pro odeslání e-mailu. Odeslání e-mailů pomocí účtu Office 365 vytváří se tok uživatele.

Další informace o funkcích Microsoft Flow, najdete v článku [Začínáme s Microsoft Flow](https://docs.microsoft.com/flow/getting-started).

Předpokladem pro tento krok je zaregistrovat [Microsoft Flow](https://flow.microsoft.com) účtu a k přihlášení. Jednou v řešení, použijte následující postup nastavení **nový tok**:

- Přístup "**Moje toky**" položky nabídky
- Uvnitř Moje toky, vyberte "**+ vytvořit z prázdné**" odkazu v horní části stránky
- Klikněte na odkaz "**vyhledat stovky konektorů a aktivačních událostí**" v dolní části stránky
- Hledání zadejte do pole "**opakování**" a vyberte "**plán – opakování**" naplánování spuštění úlohy doručování e-mailu ve výsledcích hledání.
- V podokně opakování v poli Frekvence vyberte frekvenci, plánování, tento tok spustit, jako je odesílání automatizovaných e-mailové každou minutu, hodinu, dne, týdne, atd.

Dalším krokem je přidání tří úlohy (vytvoření, výstup get a odesílání e-mailu) do nově vytvořeného opakovaného toku. K provedení přidání požadované úlohy s tokem, postupujte podle těchto kroků:

1. Vytvoření akce pro spuštění skriptu prostředí PowerShell k načtení doporučení pro vyladění
- Vyberte "**+ nový krok**", za nímž následují"**přidat akci**" v podokně toku opakování
- Hledání zadejte do pole "**automatizace**"a vyberte"**Azure Automation – vytvořit úlohu**" ve výsledcích hledání
- V podokně úlohy vytvoření konfigurace vlastností projektu. Pro tuto konfiguraci budete potřebovat podrobnosti ID, skupiny prostředků předplatného Azure a účet Automation **zaznamenaných** na **podokně účtu Automation**. Další informace o možnostech dostupných v této části najdete v tématu [Azure Automation – vytvoření úlohy](https://docs.microsoft.com/connectors/azureautomation/#Create_job).
- Dokončení vytváření tuto akci kliknutím na "**uložit tok**"

2. Vytvoření akce načíst výstup z provedený skript prostředí PowerShell
- Vyberte "**+ nový krok**", za nímž následují"**přidat akci**" v podokně toku opakování
- Hledání zaznamenaná typu "**automatizace**"a vyberte"**Azure Automation – získat výstup úlohy**" ve výsledcích hledání. Další informace o možnostech dostupných v této části najdete v tématu [Azure Automation – získat výstup úlohy](https://docs.microsoft.com/connectors/azureautomation/#Get_job_output).
- Naplnit pole vyžaduje (podobné jako vytvoření předchozí úlohy) – naplnění vašeho předplatného Azure ID, skupinu prostředků a účet Automation (jak je zadáno v podokně účtu Automation)
- Klikněte do pole "**ID úlohy**" pro "**dynamický obsah**" nabídky se zobrazí. V rámci této nabídky vyberte možnost "**ID úlohy**".
- Dokončení vytváření tuto akci kliknutím na "**uložit tok**"

3. Vytvoření akce Odeslat e-mailu pomocí integrace Office 365
- Vyberte "**+ nový krok**", za nímž následují"**přidat akci**" v podokně toku opakování
- Hledání zaznamenaná typu "**odeslat e-mailu**"a vyberte"**Office 365 Outlook – odeslat e-mail**" ve výsledcích hledání
- V "**k**" typ e-mailovou adresu, na který budete muset odeslat e-mail s oznámením pole
- V "**subjektu**" v předmětu e-mailu, například "doporučení automatického ladění e-mailová oznámení" typ pole
- Klikněte do pole "**tělo**" pro "**dynamický obsah**" nabídky se zobrazí. Z v rámci této nabídky v části "**získat výstup úlohy**", vyberte možnost"**obsahu**" 
- Dokončení vytváření tuto akci kliknutím na "**uložit tok**"

> [!TIP]
> K odesílání automatizovaných e-mailů různých příjemcům, vytvořte samostatné toky. V těchto dalších toků změňte příjemce e-mailovou adresu v poli "Do" a řádek předmětu e-mailu v poli "Subjekt". Vytvoření nové sady runbook ve službě Azure Automation s vlastní skripty prostředí PowerShell (například změny ID předplatného Azure) umožňuje další přizpůsobení automatizované scénářů, jako je například e-mailem žádají samostatné příjemce na automatické ladění doporučení pro samostatné předplatné.
>

Výše uvedené končí kroky potřebnými ke konfiguraci pracovního postupu úloh doručování e-mailu. Celý tok, který se skládá ze tří akcí, které jsou vytvořené je znázorněno na následujícím obrázku.

![Zobrazení automatického ladění toku e-mailových oznámení](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

Pro testování tohoto toku, klikněte na "**Run Now**" v pravém horním rohu v podokně toku.

Statistiky spouštění automatizovaných úloh zobrazující úspěšnost e-mailové upozornění, můžete zobrazit z podokna analýza toku.

![Spuštění toku pro automatické ladění e-mailových oznámení](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

Flow analytics je užitečné pro sledování úspěchu se prováděné úlohy a pokud je to nutné pro řešení potíží.  V případě s řešením problémů, můžete také chtít prozkoumat protokolu spuštění skriptu PowerShell přístupné prostřednictvím aplikace služby Azure Automation.

Finální výstup z automatizovaných e-mailu vypadá podobně jako na následující e-mailu až po sestavování a spouštění tohoto řešení:

![Ukázkový výstup e-mailu z automatického ladění e-mailových oznámení](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

Úpravou skript prostředí PowerShell můžete upravit výstup a formátování automatizovaných e-mailu podle vašich potřeb.

Může být dále přizpůsobit řešení tak, aby sestavení e-mailová oznámení na základě konkrétní události ladění a několika příjemcům pro více předplatných nebo databáze, v závislosti na vaší vlastní scénáře. 

## <a name="next-steps"></a>Další postup

- Přečtěte si více o tom, jak automatického ladění vám může pomoct zlepšit výkon databáze, najdete v článku [automatického ladění ve službě Azure SQL Database](sql-database-automatic-tuning.md).
- Pokud chcete povolit automatické ladění ve službě Azure SQL Database a spravovat vaše úlohy, naleznete v tématu [povolení automatického ladění](sql-database-automatic-tuning-enable.md).
- Chcete-li ručně zkontrolovat a použít automatické optimalizace doporučení, najdete v článku [vyhledání a použití doporučení k výkonu](sql-database-advisor-portal.md).
