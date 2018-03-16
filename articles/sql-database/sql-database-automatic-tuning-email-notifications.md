---
title: "Automatické ladění e-mailem oznámení s postupy Průvodce – Azure SQL Database | Microsoft Docs"
description: "Azure SQL Database analyzuje dotazu SQL a automaticky přizpůsobí zatížení uživatele."
services: sql-database
author: danimir
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 02/05/2018
ms.author: v-daljep
ms.openlocfilehash: a2799e45fbb54531289a89082f13e5ce0856c376
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="email-notifications-for-automatic-tuning"></a>E-mailová oznámení pro automatické ladění

Databáze SQL Azure SQL Database generované vyladění doporučení [automatické ladění](sql-database-automatic-tuning.md). Toto řešení nepřetržitě monitoruje a analyzuje zatížení poskytnout databází SQL přizpůsobit ladění doporučení pro každé jednotlivé databáze týkající se vytvoření indexu, odstranění indexu a optimalizace plány provádění dotazů.

SQL databáze automatické ladění doporučení lze zobrazit v [portál Azure](sql-database-advisor-portal.md), načtené s [REST API](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) volá, nebo pomocí [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) a [ Prostředí PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabaserecommendedaction) příkazy. Tento článek je založena na pomocí skriptu prostředí PowerShell k načtení automatické ladění doporučení.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Automatizovat e-mailová oznámení pro automatické ladění doporučení

Následující řešení automatizuje odesílání e-mailová oznámení obsahující automatické ladění doporučení. Popisuje řešení se skládá z automatizaci provádění skriptu prostředí PowerShell pro získání vyladění doporučení pomocí [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro), a automatizace plánování pomocí úlohy doručení e-mailu [Flow Microsoft ](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Vytvoření účtu Azure Automation.

Pokud chcete používat Azure Automation, prvním krokem je vytvoření účtu automation a konfigurace s prostředky Azure pro spuštění skriptu prostředí PowerShell. Další informace o Azure Automation a jeho funkce, najdete v části [Začínáme s Azure automation](https://docs.microsoft.com/azure/automation/automation-offering-get-started).

Postupujte podle těchto kroků můžete vytvořit účet Azure Automation prostřednictvím metody výběru a konfigurace automatizace aplikace z Marketplace:

- Přihlaste se k portálu Azure
- Klikněte na "**+ vytvořit prostředek**" v levém horním rohu
- Vyhledejte "**automatizace**" (stiskněte klávesu enter)
- Klikněte na aplikaci automatizace ve výsledcích hledání

![Přidání služby Azure automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

- Jednou v podokně "Vytvořit automatizace účet", klikněte na "**vytvořit**"
- Naplnění požadované informace: Zadejte název pro tento účet služby automation, vyberte předplatné Azure ID a Azure prostředky má být použit pro provádění skriptu prostředí PowerShell
- Pro "**vytvořit Azure účet Spustit jako**" Vyberte možnost **Ano** nakonfigurovat typ účtu, pod které PowerShell skript se spustí pomocí Azure Automation. Další informace o typech účtů najdete v tématu [účet Spustit jako](https://docs.microsoft.com/azure/automation/automation-create-runas-account)
- Vytvoření účtu automation dokončete kliknutím na **vytvořit**

> [!TIP]
> Zaznamenejte přesně tak, jak zadat při vytváření aplikace automatizace váš název účtu Azure Automation, ID předplatného a prostředky (například kopírování vložením programu Poznámkový blok). Je třeba tyto informace později.
>

Pokud máte několik předplatných Azure, pro které chcete sestavit automatizaci stejné, musíte tento postup opakujte pro jiné předplatné.

## <a name="update-azure-automation-modules"></a>Aktualizovat moduly Azure Automation.

Skript prostředí PowerShell k načtení automatické ladění doporučení používá [Get-AzureRmResource](https://docs.microsoft.com/powershell/module/AzureRM.Resources/Get-AzureRmResource) a [Get-AzureRmSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlDatabaseRecommendedAction) příkazy, pro které aktualizaci moduly Azure verze 4 a novější je nutné.

Postupujte podle těchto kroků provedete aktualizaci modulů prostředí Azure PowerShell:

- Přístup k podoknu automatizace aplikace a vyberte "**moduly**" v nabídce na levé straně (přejděte dolů tuto položku nabídky je pod sdílené prostředky).
- V podokně moduly, klikněte na "**aktualizace Azure moduly**" v horní a počkejte, až se zobrazí zpráva "Azure moduly byly aktualizovány". Dokončení tohoto procesu může trvat několik minut.

![Aktualizovat moduly Azure automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-02.png)

Požadovaná verze z AzureRM.Resources a AzureRM.Sql moduly musí být verze 4 a vyšší.

## <a name="create-azure-automation-runbook"></a>Vytvoření služby Azure Automation Runbook

Dalším krokem je vytvoření sady Runbook ve službě Azure Automation, která se nachází skript prostředí PowerShell k načtení ladění doporučení.

Postupujte podle těchto kroků můžete vytvořit novou sadu runbook automatizace Azure:

- Přístup k účtu Azure Automation, kterou jste vytvořili v předchozím kroku
- Jednou v podokně účet automation, klikněte na "**Runbooky**" položku nabídky na levé straně, chcete-li vytvořit novou sadu runbook automatizace Azure pomocí skriptu prostředí PowerShell. Další informace o vytváření runbooků služeb automatizace najdete v tématu [vytváření nových runbooků](../automation/automation-creating-importing-runbook.md).
- Chcete-li přidat novou sadu runbook, klikněte na "**+ přidat runbook**" možnost nabídky a klikněte na "**rychlé vytvoření – vytvořit nový runbook**".
- V podokně sady Runbook, zadejte název vaší sady runbook (pro účely tohoto příkladu "**AutomaticTuningEmailAutomation**" se používá), vyberte typ runbook jako **prostředí PowerShell** a zápis popis Tato sada runbook k popisu jeho účel.
- Klikněte na **vytvořit** tlačítko dokončete vytváření nové sady runbook

![Přidání služby Azure automation runbook](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Použijte následující postup načtení skriptu prostředí PowerShell v runbooku vytvořit:

- Uvnitř "**upravit Runbook Powershellu**"podokně, vyberte možnost"**RUNBOOKY**" v nabídce stromu a rozbalte zobrazení, dokud neuvidíte název sady runbook (v tomto příkladu " **AutomaticTuningEmailAutomation**"). Vyberte tuto sadu runbook.
- Na první řádek "Upravit prostředí PowerShell sady runbook" (počínaje číslem 1) zkopírujte vložte následující kód skriptu prostředí PowerShell. Tento skript prostředí PowerShell je k dispozici jako-se, abyste mohli začít. Upravte skript tak, aby sada vašim potřebám.

V hlavičce poskytnutého skriptu prostředí PowerShell, je třeba nahradit `<SUBSCRIPTION_ID_WITH_DATABASES>` s vaším ID předplatného Azure. Zjistěte, jak zjistit ID předplatného Azure, najdete v tématu [získávání vaší GUID předplatného Azure](https://blogs.msdn.microsoft.com/mschray/2016/03/18/getting-your-azure-subscription-guid-new-portal/).

V případě několik předplatných může přidejte je jako oddělených čárkou pro vlastnost "$subscriptions" v hlavičce skriptu.

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
Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

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

Klikněte "**Uložit**" tlačítko v pravém horním rohu Uložte skript. Jakmile budete spokojeni pomocí skriptu, klikněte "**publikovat**" tlačítko Publikovat tuto sadu runbook. 

V podokně hlavní runbook můžete kliknout na "**spustit**" tlačítko pro **testování** skript. Klikněte na "**výstup**" pro zobrazení výsledků skript spustit. Tento výstup bude obsah e-mailu. Ukázkový výstup ze skriptu můžete vidět na následujícím snímku obrazovky.

![Spuštění zobrazení Automatické ladění doporučení s Azure Automation.](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Je třeba upravit obsah přizpůsobením skript prostředí PowerShell vašim potřebám.

Pomocí výše uvedené kroky je skript prostředí PowerShell k načtení automatické ladění doporučení načten ve službě Azure Automation. Dalším krokem je automatizovat a plán úlohy doručení e-mailu.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatizovat úlohy e-mailu s Microsoft Flow

K dokončení tohoto řešení jako poslední krok, vytvořte tok automatizace v Flow Microsoft, který se skládá ze tří akcí (úlohy): 

1. "**Azure Automation - vytvořit úlohu**" – používá se pro spuštění skriptu prostředí PowerShell k načtení automatické ladění doporučení uvnitř runbook automatizace Azure.
2. "**Azure Automation - Get výstup úlohy**" – používá se pro načíst výstup z spustit skript prostředí PowerShell
3. "**Office 365 Outlook – e-mailu**" – používá se k odeslání e-mailu. E-maily jsou odeslány se pomocí účtu Office 365 jednotlivých vytváření toku.

Další informace o možnostech Flow Microsoft najdete v tématu [Začínáme se službou Microsoft Flow](https://docs.microsoft.com/flow/getting-started).

Předpokladem pro tento krok je, aby si zaregistrovat [Microsoft Flow](https://flow.microsoft.com) účtu a k přihlášení. Jednou uvnitř řešení, postupujte podle těchto kroků provedete nastavení **nové toku**:

- Přístup "**Moje toky**" položky nabídky
- Uvnitř Moje toky, vyberte "**+ vytvořit z prázdné**" odkaz v horní části stránky
- Klikněte na odkaz "**vyhledávání ve velkém množství konektory a aktivační události**" v dolní části stránky
- V typu pole hledání "**opakování**" a vyberte možnost "**plán - opakování**" ve výsledcích hledání naplánovat spuštění úlohy doručení e-mailu.
- V podokně opakování v poli Frekvence vyberte plánování četnost pro tento postup provést, jako je odesílání automatizovaných e-mailu každou minutu, hodinu, den, týden, atd.

Dalším krokem je přidání tři úlohy (vytvořit, výstup get a odesílání e-mailu) do nově vytvořené opakovaného toku. Aby bylo možné přidat požadované úlohy s tokem, postupujte takto:

1. Vytvořit akci pro spuštění skriptu prostředí PowerShell k načtení vyladění doporučení
- Vyberte "**+ nový krok**", za nímž následují"**přidat akci**" v podokně toku opakování
- V typu pole hledání "**automatizace**"a vyberte možnost"**Azure Automation – vytvořit úlohu**" ve výsledcích hledání
- V podokně úlohy Vytvořit nakonfigurujte vlastnosti úlohy. Pro tuto konfiguraci budete potřebovat Podrobnosti předplatného Azure ID, skupinu prostředků a účet Automation **dříve zaznamenaných** na **účet Automation podokně**. Další informace o možnostech dostupných v této části najdete v tématu [Azure Automation – vytvoření úlohy](https://docs.microsoft.com/connectors/azureautomation/#Create_job).
- Dokončení vytváření tuto akci kliknutím na "**uložit toku**"

2. Vytvořit akce načíst výstup z spustit skript prostředí PowerShell
- Vyberte "**+ nový krok**", za nímž následují"**přidat akci**" v podokně toku opakování
- V hledání selhala typu "**automatizace**"a vyberte možnost"**Azure Automation – výstup úlohy Get**" ve výsledcích hledání. Další informace o možnostech dostupných v této části najdete v tématu [Azure Automation – výstup úlohy Get](https://docs.microsoft.com/connectors/azureautomation/#Get_job_output).
- Naplnění polí požadované (podobně jako na vytváření předchozí úlohy) – naplnění vašemu předplatnému Azure ID, skupinu prostředků a účet Automation (jak je zadáno v podokně účet Automation)
- Klikněte do pole "**ID úlohy**" pro "**dynamický obsah**" nabídky objeví. V rámci této nabídky vyberte možnost "**ID úlohy**".
- Dokončení vytváření tuto akci kliknutím na "**uložit toku**"

3. Vytvořit akce k odeslání e-mailu pomocí integrace Office 365
- Vyberte "**+ nový krok**", za nímž následují"**přidat akci**" v podokně toku opakování
- V hledání selhala typu "**e-mailovou zprávu**"a vyberte možnost"**Office 365 Outlook – e-mailu**" ve výsledcích hledání
- V "**k**" pole typu v e-mailovou adresu, na kterou budete muset odeslat e-mailové oznámení
- V "**subjektu**" v předmětu e-mailu, například "Automatické ladění doporučení e-mailová oznámení" typ pole
- Klikněte do pole "**textu**" pro "**dynamický obsah**" nabídky objeví. Z v rámci této nabídky v části "**získat výstup úlohy**", vyberte možnost"**obsahu**" 
- Dokončení vytváření tuto akci kliknutím na "**uložit toku**"

> [!TIP]
> Chcete-li odesílání automatizovaných e-mailů jiný příjemce, vytvořte samostatné toky. V těchto dalších toky změňte příjemce e-mailovou adresu v poli "Na" a řádek předmětu e-mailu v poli "Subjekt". Vytvoření nové sady runbook ve službě Azure Automation se přizpůsobit skriptů prostředí PowerShell (například v změně ID předplatného Azure) umožňuje další přizpůsobení automatizované scénářů, například je třeba e-mailem na samostatné příjemce na automatické ladění doporučení pro samostatné předplatné.
>

Výše ukončí krocích potřebných ke konfiguraci pracovního postupu úlohy doručení e-mailu. Celého toku, který se skládá ze tří akcí, které jsou vytvořené je znázorněno na následujícím obrázku.

![Zobrazení Automatické ladění e-mailu oznámení](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

Chcete-li otestovat toku, klikněte na "**spustit nyní**" v pravém horním rohu v podokně toku.

Statistiky spouštění automatizovaných úloh, zobrazující úspěch e-mailová oznámení odeslaná, můžete zobrazit v podokně analytics toku.

![Spuštění toku automatické ladění e-mailových oznámení](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

Tok analytics je užitečné pro monitorování úspěchu při spuštění úlohy a v případě potřeby pro řešení potíží.  V případě řešení potíží, můžete také chtít vyhledejte v protokolu spuštění skriptu PowerShell přístupné prostřednictvím aplikace Azure Automation.

Finální výstup automatizovaných e-mailu, vypadá podobně jako následující e-maily přijaté za vytváření a spouštění tohoto řešení:

![Ukázkový výstup e-mailu z automatické ladění e-mailových oznámení](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

Úpravou skriptu prostředí PowerShell, můžete upravit výstup a formátování automatizovaných e-mailu vašim potřebám.

K vytvoření e-mailových oznámení založené na konkrétní události ladění a několika příjemcům, pro víc předplatných nebo databáze, v závislosti na vlastní scénáře řešení může přizpůsobit. 

## <a name="next-steps"></a>Další kroky

- Zjistěte více v tom, jak automatické ladění můžete zvýšit výkon databáze, najdete v části [automatické ladění ve službě Azure SQL Database](sql-database-automatic-tuning.md).
- Pokud chcete povolit automatické ladění v databázi SQL Azure ke správě velikosti pracovní zátěže, najdete v části [povolit automatické ladění](sql-database-automatic-tuning-enable.md).
- Chcete-li ručně zkontrolovat a použít automatické ladění doporučení, přečtěte si téma [najít a použít výkonu doporučení](sql-database-advisor-portal.md).
