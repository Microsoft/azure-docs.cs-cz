---
title: Průvodce automatickým laděním e-mailových oznámení
description: Povolí e-mailová oznámení pro Azure SQL Database automatické ladění dotazů.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/03/2019
ms.openlocfilehash: a373a28a180b2a6c72f6a291b9d1437a2e88d9ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96500950"
---
# <a name="email-notifications-for-automatic-tuning"></a>E-mailová oznámení pro automatické ladění
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Doporučení pro ladění Azure SQL Database vygeneruje Azure SQL Database [Automatické ladění](automatic-tuning-overview.md). Toto řešení průběžně monitoruje a analyzuje úlohy databází poskytujících přizpůsobená doporučení pro ladění pro každou jednotlivou databázi související s vytvářením indexu, odstraňováním indexu a optimalizací plánů provádění dotazů.

Doporučení pro automatické ladění Azure SQL Database můžete zobrazit v [Azure Portal](database-advisor-find-recommendations-portal.md), načíst pomocí [REST API](/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) volání nebo pomocí příkazů [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) a [PowerShellu](/powershell/module/az.sql/get-azsqldatabaserecommendedaction) . Tento článek je založený na použití skriptu PowerShellu k načtení doporučení pro automatické vyladění.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Automatizace e-mailových oznámení pro doporučení automatického ladění

Následující řešení automatizuje odesílání e-mailových oznámení obsahujících doporučení automatického ladění. Popsané řešení se skládá z automatizace spouštění skriptu PowerShellu pro načítání doporučení pro ladění pomocí [Azure Automation](../../automation/automation-intro.md)a automatizace plánování úloh doručování e-mailů pomocí [Microsoft Flow](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Vytvořit účet Azure Automation

Pokud chcete použít Azure Automation, prvním krokem je vytvoření účtu Automation a jeho konfigurace pomocí prostředků Azure, které se použijí ke spuštění skriptu PowerShellu. Další informace o Azure Automation a jejích funkcích najdete v tématu [Začínáme s Azure Automation](../../automation/index.yml).

Pomocí těchto kroků vytvoříte účet Azure Automation prostřednictvím metody výběru a konfigurace aplikace automatizace z Azure Marketplace:

1. Přihlaste se k webu Azure Portal.
1. V levém horním rohu klikněte na **+ vytvořit prostředek**.
1. Vyhledejte "**Automation**" (stiskněte klávesu ENTER).
1. Ve výsledcích hledání klikněte na aplikaci Automation.

    ![Přidává se Azure Automation.](./media/automatic-tuning-email-notifications-configure/howto-email-01.png)

1. Jednou v podokně vytvořit účet Automation klikněte na **vytvořit**.
1. Naplňte požadované informace: zadejte název pro tento účet Automation, vyberte ID předplatného Azure a prostředky Azure, které se mají použít pro spuštění skriptu PowerShellu.
1. U možnosti **vytvořit účet Spustit v Azure jako** vyberte **Ano** , pokud chcete nakonfigurovat typ účtu, pod kterým bude skript PowerShellu spuštěný, a to díky Azure Automation. Další informace o typech účtů najdete v tématu [účet Spustit jako](../../automation/manage-runas-account.md).
1. Kliknutím na **vytvořit vytvořte** účet Automation.

> [!TIP]
> Poznamenejte si název svého účtu Azure Automation, ID předplatného a prostředky (například kopírování a vkládání do poznámkového bloku) přesně tak, jak jste zadali při vytváření aplikace automatizace. Tyto informace budete potřebovat později.

Pokud máte několik předplatných Azure, pro které byste chtěli sestavit stejnou automatizaci, musíte tento postup zopakovat pro vaše další odběry.

## <a name="update-azure-automation-modules"></a>Aktualizovat Azure Automation moduly

Skript PowerShellu pro načtení doporučení automatického ladění používá příkazy [Get-AzResource](/powershell/module/az.Resources/Get-azResource) a [Get-AzSqlDatabaseRecommendedAction](/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) , pro které se vyžaduje modul Azure verze 4 a vyšší.

- V případě, že je potřeba aktualizovat moduly Azure, přečtěte si téma [AZ Module Support in Azure Automation](../../automation/shared-resources/modules.md).

## <a name="create-azure-automation-runbook"></a>Vytvoření sady Runbook Azure Automation

Dalším krokem je vytvoření Runbooku v Azure Automation, ve kterém se nachází skript PowerShellu pro načtení doporučení pro ladění.

Pomocí těchto kroků vytvořte novou Azure Automation sadu Runbook:

1. Přihlaste se k účtu Azure Automation, který jste vytvořili v předchozím kroku.
1. V podokně účet Automation klikněte na položku nabídky **Runbooky** na levé straně a vytvořte novou Azure Automation sadu Runbook pomocí skriptu PowerShellu. Další informace o vytváření runbooků automatizace najdete v tématu [Vytvoření nové sady Runbook](../../automation/manage-runbooks.md#create-a-runbook).
1. Chcete-li přidat novou sadu Runbook, klikněte na položku nabídky "**+ Přidat sadu Runbook**" a pak klikněte na možnost **rychlé vytvoření – vytvořit nový Runbook**.
1. V podokně Sada Runbook zadejte název Runbooku (pro účely tohoto příkladu se používá "**AutomaticTuningEmailAutomation**"), vyberte typ Runbooku jako **PowerShell** a napište popis této sady Runbook, abyste popsali její účel.
1. Kliknutím na tlačítko **vytvořit** dokončete vytváření nového Runbooku.

    ![Přidat Runbook služby Azure Automation](./media/automatic-tuning-email-notifications-configure/howto-email-03.png)

Pomocí těchto kroků načtete skript prostředí PowerShell do vytvořeného Runbooku:

1. V podokně **Upravit powershellový Runbook** vyberte v části strom nabídky možnost **Runbooky** a rozbalte zobrazení, dokud se nezobrazí název Runbooku (v tomto příkladu "**AutomaticTuningEmailAutomation**"). Vyberte tuto sadu Runbook.
1. Na prvním řádku "upravit PowerShellový Runbook" (počínaje číslem 1) zkopírujte a vložte následující kód skriptu PowerShellu. Tento skript PowerShellu se poskytuje tak, jak je, abyste mohli začít. Upravte skript tak, aby vyhovoval vašim potřebám.

V hlavičce zadaného skriptu PowerShellu je potřeba nahradit `<SUBSCRIPTION_ID_WITH_DATABASES>` ID vašeho předplatného Azure. Informace o tom, jak načíst ID předplatného Azure, najdete v tématu [Získání identifikátoru GUID předplatného Azure](/archive/blogs/mschray/getting-your-azure-subscription-guid-new-portal).

V případě několika předplatných je můžete přidat do vlastnosti "$subscriptions" v záhlaví skriptu, a to jako oddělovač.

```powershell
# PowerShell script to retrieve Azure SQL Database automatic tuning recommendations.
#
# Provided "as-is" with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {
    Select-AzSubscription -SubscriptionId $subscriptionId
    $rgs = Get-AzResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzResource -ResourceGroupName $rgname -ResourceType $resourceType

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

                # Loop through all automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
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

Kliknutím na tlačítko **Uložit** v pravém horním rohu skript uložíte. Až budete s skriptem spokojeni, klikněte na tlačítko **publikovat** a publikujte tento Runbook.

V hlavním podokně sady Runbook můžete zvolit, že chcete skript **otestovat** kliknutím na tlačítko **Start**. Kliknutím na **výstup** zobrazíte výsledky provedeného skriptu. Tento výstup bude odpovídat obsahu vašeho e-mailu. Vzorový výstup ze skriptu lze vidět na následujícím snímku obrazovky.

![Spustit zobrazování doporučení pro automatické ladění pomocí Azure Automation](./media/automatic-tuning-email-notifications-configure/howto-email-04.png)

Upravte obsah úpravou skriptu PowerShellu tak, aby vyhovoval vašim potřebám.

Pomocí výše uvedených kroků se skript PowerShellu, který načte doporučení automatického ladění, načte v Azure Automation. Dalším krokem je automatizace a naplánování úlohy doručování e-mailů.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatizace e-mailových úloh pomocí Microsoft Flow

K dokončení řešení, jako v posledním kroku, vytvořte tok automatizace v Microsoft Flow skládající se ze tří akcí (úlohy):

- "**Azure Automation-Create Job**" – slouží ke spuštění skriptu PowerShellu k načtení doporučení automatického ladění v rámci sady Azure Automation Runbook.
- "**Azure Automation-získat výstup úlohy**" – slouží k načtení výstupu z spouštěného skriptu PowerShellu.
- "**Office 365 Outlook – poslat e-mail**" – slouží k odeslání e-mailu. E-maily se odesílají pomocí pracovního nebo školního účtu jednotlivce, který vytváří tok.

Další informace o funkcích Microsoft Flow najdete v tématu [Začínáme s Microsoft Flow](/flow/getting-started).

Předpokladem pro tento krok je registrace účtu [Microsoft Flow](https://flow.microsoft.com) a přihlášení. V rámci řešení postupujte podle těchto kroků a nastavte **Nový tok**:

1. Přístup k položce nabídky "**Moje toky**".
1. V části Moje toky vyberte odkaz **+ vytvořit z prázdného** seznamu v horní části stránky.
1. Klikněte na odkaz **Vyhledat stovky konektorů a triggerů** v dolní části stránky.
1. Do vyhledávacího pole zadejte "**opakování**" a ve výsledcích hledání vyberte "**plán – opakování**", abyste naplánovali spuštění úlohy doručení e-mailu.
1. V podokně opakování v poli frekvence vyberte četnost plánování pro spuštění tohoto toku, například odeslat automatizovaný e-mail každou minutu, hodinu, den, týden atd.

Dalším krokem je přidání tří úloh (vytvoření, načtení výstupu a odeslání e-mailu) do nově vytvořeného opakovaného toku. K dokončení přidávání požadovaných úloh do toku použijte následující postup:

1. Vytvořit akci pro spuštění skriptu prostředí PowerShell pro načtení doporučení pro ladění

   - Vyberte **+ Nový krok** a potom v podokně tok opakování **přidejte akci**.
   - Do vyhledávacího pole zadejte "**Automation**" a z výsledků hledání vyberte **Azure Automation – vytvořit úlohu**.
   - V podokně vytvořit úlohu nakonfigurujte vlastnosti úlohy. V případě této konfigurace budete potřebovat podrobnosti o ID předplatného Azure, skupině prostředků a účtu Automation, který **jste předtím nahráli** v **podokně účtu Automation**. Další informace o možnostech dostupných v této části najdete v tématu [Azure Automation-Create Job](/connectors/azureautomation/#create-job).
   - Vytvoření této akce dokončíte kliknutím na **Uložit tok**.

2. Vytvoří akci pro načtení výstupu z spouštěného skriptu PowerShellu.

   - Vyberte **+ Nový krok** a potom v podokně tok opakování **přidejte akci**.
   - Do vyhledávacího pole zadejte "**Automation**" a z výsledků hledání vyberte **Azure Automation – získat výstup úlohy**. Další informace o možnostech dostupných v této části najdete v tématu [Azure Automation – získání výstupu úlohy](/connectors/azureautomation/#get-job-output).
   - Vyplňte požadovaná pole (podobně jako při vytváření předchozí úlohy) – naplňte ID předplatného Azure, skupinu prostředků a účet Automation (zadané v podokně účet Automation).
   - Klikněte do pole **ID úlohy**, které se zobrazí v nabídce **dynamického obsahu**. V této nabídce vyberte možnost "**ID úlohy**".
   - Vytvoření této akce dokončíte kliknutím na **Uložit tok**.

3. Vytvoření akce k odeslání e-mailu pomocí integrace Office 365

   - Vyberte **+ Nový krok** a potom v podokně tok opakování **přidejte akci**.
   - Do vyhledávacího pole zadejte "**Odeslat e-mail**" a vyberte "**Office 365 Outlook – odeslat e-mail**" z výsledků hledání.
   - Do pole "**do**" zadejte e-mailovou adresu, na kterou potřebujete odeslat oznamovací e-mail.
   - V poli **Předmět** zadejte v předmětu vašeho e-mailu například "automatické ladění doporučení e-mailové oznámení".
   - Klikněte do pole "**tělo**" v nabídce "**dynamický obsah**", která se zobrazí. V této nabídce v části **načíst výstup úlohy** vyberte **obsah**.
   - Vytvoření této akce dokončíte kliknutím na **Uložit tok**.

> [!TIP]
> K odesílání automatizovaných e-mailů různým příjemcům vytvořte samostatné toky. V těchto dalších tocích změňte e-mailovou adresu příjemce v poli Komu a na řádek Předmět e-mailu v poli předmět. Vytváření nových sad Runbook v Azure Automation pomocí přizpůsobených skriptů PowerShellu (například se změnou ID předplatného Azure) umožňuje další přizpůsobení automatizovaných scénářů, například e-mailem oddělených příjemců na doporučení automatizovaného ladění pro samostatné odběry.

Výše uvedený postup končí kroky potřebnými ke konfiguraci pracovního postupu úlohy doručení e-mailu. Na následujícím obrázku je zobrazen celý tok tvořený třemi akcemi, které jsou sestaveny.

![Zobrazit tok pro e-mailová oznámení automatického ladění](./media/automatic-tuning-email-notifications-configure/howto-email-05.png)

Tok otestujete kliknutím na "**Spustit nyní**" v pravém horním rohu v podokně Flow.

Statistiky spuštění automatizovaných úloh, které zobrazují úspěšnost e-mailových oznámení, se dají zobrazit v podokně analýza toku.

![Spuštění toku pro automatické ladění e-mailových oznámení](./media/automatic-tuning-email-notifications-configure/howto-email-06.png)

Podokno analýza toků je užitečné pro sledování úspěšnosti provádění úloh a v případě potřeby při řešení potíží.  V případě řešení potíží můžete také kontrolovat protokol spuštění skriptu PowerShellu, který je přístupný prostřednictvím aplikace Azure Automation.

Konečný výstup automatizovaného e-mailu vypadá podobně jako následující e-mail přijatý po sestavení a spuštění tohoto řešení:

![Ukázkový výstup e-mailů z automatického ladění e-mailových oznámení](./media/automatic-tuning-email-notifications-configure/howto-email-07.png)

Úpravou skriptu PowerShellu můžete upravit výstup a formátování automatizovaného e-mailu podle vašich potřeb.

Řešení můžete dále přizpůsobit pro vytváření e-mailových oznámení na základě konkrétní události ladění a více příjemcům pro více předplatných nebo databází v závislosti na vašich vlastních scénářích.

## <a name="next-steps"></a>Další kroky

- Další informace o tom, jak automatické ladění vám může pomoci zlepšit výkon databáze, najdete v tématu [Automatické ladění v Azure SQL Database](automatic-tuning-overview.md).
- Pokud chcete povolit automatické ladění v Azure SQL Database ke správě úloh, přečtěte si téma [Povolení automatického ladění](automatic-tuning-enable.md).
- Pokud chcete ručně zkontrolovat a použít doporučení automatického ladění, přečtěte si téma [hledání a použití doporučení pro výkon](database-advisor-find-recommendations-portal.md).