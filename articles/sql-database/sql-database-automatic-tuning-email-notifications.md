---
title: Průvodce automatickým laděním e-mailových oznámení
description: Povolte e-mailová oznámení pro automatické ladění dotazů azure SQL database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: b48c37a6e607d121416ebae4d74e58f39670b79a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821930"
---
# <a name="email-notifications-for-automatic-tuning"></a>E-mailová oznámení pro automatické ladění

Doporučení pro optimalizaci databáze SQL jsou generována [automatickým laděním](sql-database-automatic-tuning.md)azure sql database . Toto řešení průběžně monitoruje a analyzuje úlohy databází SQL, které poskytují přizpůsobená doporučení pro ladění pro každou jednotlivou databázi související s vytvářením indexu, odstraněním indexu a optimalizací plánů spuštění dotazu.

Sql Database Automatické optimalizace doporučení lze zobrazit na [webu Azure portal](sql-database-advisor-portal.md), načíst pomocí volání rozhraní REST [API](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) nebo pomocí [příkazů T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) a [PowerShell.](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) Tento článek je založen na použití skriptu Prostředí PowerShell k načtení doporučení automatického ladění.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Tyto rutiny naleznete v tématu [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Automatizace e-mailových oznámení pro doporučení automatického ladění

Následující řešení automatizuje odesílání e-mailových oznámení obsahujících doporučení automatického ladění. Popsané řešení spočívá v automatizaci spuštění skriptu prostředí PowerShell pro načítání doporučení pro ladění pomocí [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)a automatizace plánování úlohy doručování e-mailů pomocí [Microsoft Flow](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Vytvoření účtu Azure Automation

Chcete-li použít Azure Automation, prvním krokem je vytvoření účtu automatizace a jeho konfigurace s prostředky Azure pro spuštění skriptu PowerShell. Další informace o Azure Automation a jejích možnostech najdete [v tématu Začínáme s automatizací Azure](https://docs.microsoft.com/azure/automation/automation-offering-get-started).

Následujícím postupem vytvořte účet Azure Automation prostřednictvím metody výběru a konfigurace aplikace Automation z webu Marketplace:

- Přihlášení k portálu Azure
- Klikněte na "**+ Vytvořit zdroj**" v levém horním rohu
- Hledat "**Automatizace**" (stiskněte enter)
- Klikněte na aplikaci Automatizace ve výsledcích vyhledávání

![Přidání azure automatizace](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

- Jakmile se v podokně "Vytvořit účet automatizace" klikněte na **"Vytvořit"**
- Naplnit požadované informace: zadejte název pro tento účet automatizace, vyberte ID předplatného Azure a prostředky Azure, které se mají použít pro spuštění skriptu Prostředí PowerShell
- Pro možnost **"Vytvořit účet Azure Run As"** vyberte **Ano,** chcete-li nakonfigurovat typ účtu, pod kterým běží skript PowerShellu s pomocí Azure Automation. Další informace o typech účtů najdete v tématu [Spuštění účtu](https://docs.microsoft.com/azure/automation/automation-create-runas-account)
- Vytvoření automatizačního účtu uzavřete kliknutím na **vytvořit**

> [!TIP]
> Zaznamenejte název účtu Azure Automation, ID předplatného a prostředky (například kopírování a vkládání do poznámkového bloku) přesně tak, jak jste zadali při vytváření aplikace Automation. Tyto informace budete potřebovat později.
>

Pokud máte několik předplatných Azure, pro které chcete vytvořit stejnou automatizaci, musíte tento proces zopakovat pro ostatní předplatná.

## <a name="update-azure-automation-modules"></a>Aktualizace modulů Azure Automation

Skript Prostředí PowerShell pro načtení doporučení automatického ladění používá příkazy [Get-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Get-azResource) a [Get-AzSqlDatabaseRecommendedAction,](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) pro které je vyžadován modul Azure verze 4 a vyšší.

- V případě, že vaše moduly Azure potřebují aktualizaci, najdete v tématu [podpora modulů Az v Azure Automation](../automation/az-modules.md).

## <a name="create-azure-automation-runbook"></a>Vytvoření runbooku Azure Automation

Dalším krokem je vytvoření runbooku v Azure Automation, ve kterém se nachází skript Prostředí PowerShell pro načítání doporučení pro optimalizaci.

Chcete-li vytvořit novou runbook Azure Automation, postupujte takto:

- Přístup k účtu Azure Automation, který jste vytvořili v předchozím kroku
- V podokně účtů automatizace klikněte na položku nabídky**Runbook**na levé straně a vytvořte novou runbook Azure Automation se skriptem Prostředí PowerShell. Další informace o vytváření runbooků pro automatizaci najdete [v tématu Vytvoření novésady Runbook](../automation/manage-runbooks.md#create-a-runbook).
- Chcete-li přidat nový soubor Runbook, klikněte na možnost nabídky "**+Přidat runbook**" a potom klikněte na "**Rychlé vytvoření – Vytvoření novésady Runbook**".
- V podokně Runbook zadejte název sady Runbook (pro účely tohoto příkladu se používá **"AutomaticTuningEmailAutomation**"), vyberte typ sady Runbook jako **prostředí PowerShell** a napište popis tohoto sady Runbook, který popisuje jeho účel.
- Kliknutím na tlačítko **Vytvořit** dokončete vytvoření nového runbooku.

![Přidání runbooku Azure automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Chcete-li načíst skript prostředí PowerShell do vytvořené hospo-

- V podokně **"Edit PowerShell Runbook"** vyberte ve stromu nabídek položku**RUNBOOKS**a rozbalte zobrazení, dokud neuvidíte název sady Runbook (v tomto příkladu **"AutomaticTuningEmailAutomation").** Vyberte tuto runbook.
- Na prvním řádku "Edit PowerShell Runbook" (počínaje číslem 1) zkopírujte a vložte následující kód skriptu prostředí PowerShell. Tento skript prostředí PowerShell je k dispozici tak, abyste mohli začít. Upravte skript tak, aby vyhovoval vašim potřebám.

V záhlaví poskytnutého skriptu PowerShellu je `<SUBSCRIPTION_ID_WITH_DATABASES>` potřeba nahradit ID předplatného Azure. Informace o tom, jak načíst ID předplatného Azure, najdete [v tématu Získání identifikátoru GUID předplatného Azure](https://blogs.msdn.microsoft.com/mschray/20../../getting-your-azure-subscription-guid-new-portal/).

V případě několika odběrů je můžete přidat jako čárku k vlastnosti "$subscriptions" v záhlaví skriptu.

```powershell
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

                # Loop through all Automatic tuning recommendation types
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

Kliknutím na tlačítko**Uložit**v pravém horním rohu skript uložte. Až budete se skriptem spokojeni, klikněte na tlačítko**Publikovat**a publikujte tento runbook.

V hlavním podokně runbooku můžete kliknout na tlačítko **"Start"** **a otestovat** skript. Kliknutím na "**Výstup**" zobrazíte výsledky spuštěného skriptu. Tento výstup bude obsah vašeho e-mailu. Ukázkový výstup ze skriptu lze vidět na následujícím snímku obrazovky.

![Spuštění doporučení automatického ladění zobrazení pomocí Azure Automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Ujistěte se, že upravit obsah přizpůsobením skriptu PowerShell u svých potřeb.

S výše uvedenými kroky se v Azure Automation načte skript Prostředí PowerShell pro načtení doporučení automatického ladění. Dalším krokem je automatizace a plánování úlohy doručování e-mailů.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatizace e-mailových úloh pomocí Microsoft Flow

Chcete-li řešení dokončit, vytvořte v Microsoft Flow tok automatizace, který se skládá ze tří akcí (úloh):

1. "**Azure Automation – vytvoření úlohy**" – používá se ke spuštění skriptu Prostředí PowerShell k načtení doporučení automatického ladění v runbooku Azure Automation
2. "**Azure Automation – získat výstup úlohy**" – používá se k načtení výstupu z spuštěného skriptu PowerShellu
3. "**Office 365 Outlook – Odeslat e-mail**" – používá se k rozesílce e-mailu. E-maily se posílají pomocí účtu Office 365 jednotlivce, který vytváří tok.

Další informace o možnostech Microsoft Flow najdete [v tématu Začínáme s Microsoft Flowm](https://docs.microsoft.com/flow/getting-started).

Předpokladem tohoto kroku je zaregistrovat se k účtu [Microsoft Flow](https://flow.microsoft.com) a přihlásit se. Jakmile je uvnitř řešení, postupujte takto a nastavte **nový tok**:

- Přístup k položce nabídky**Moje toky**
- V části Moje toky vyberte odkaz "**+Vytvořit z prázdné**" v horní části stránky
- Klikněte na odkaz "**Vyhledat stovky konektorů a spouštěčů**" v dolní části stránky
- Ve vyhledávacím poli zadejte "**opakování**" a z výsledků hledání vyberte "**Plán - Opakování**", chcete-li naplánovat spuštění úlohy doručení e-mailu.
- V podokně Opakování v poli Frekvence vyberte frekvenci plánování pro tento tok, který má být proveden, například odeslat automatický e-mail za minutu, hodinu, den, týden atd.

Dalším krokem je přidání tří úloh (vytvoření, získání výstupu a odeslání e-mailu) do nově vytvořeného opakovaného toku. Chcete-li provést přidání požadovaných úloh do toku, postupujte takto:

1. Vytvořit akci pro spuštění skriptu PowerShellu pro načtení doporučení pro ladění

   - Vyberte "**+Nový krok**", následovaný "**Přidat akci**" uvnitř podokna toku opakování
   - Ve vyhledávacím poli typ "**automatizace**" a vyberte "**Azure Automation – Vytvořit úlohu**" z výsledků hledání
   - V podokně Vytvořit úlohu nakonfigurujte vlastnosti úlohy. Pro tuto konfiguraci budete potřebovat podrobnosti o id předplatného Azure, skupině prostředků a účtu **automatizace, které byly dříve zaznamenány** v **podokně Účet automatizace**. Další informace o možnostech dostupných v této části najdete v [tématu Azure Automation – vytvoření úlohy](https://docs.microsoft.com/connectors/azureautomation/#create-job).
   - Dokončení vytvoření této akce kliknutím na tlačítko **"Uložit tok**"

2. Vytvořit akci pro načtení výstupu ze spuštěného skriptu Prostředí PowerShell

   - Vyberte "**+Nový krok**", následovaný "**Přidat akci**" uvnitř podokna toku opakování
   - Ve vyhledávání soubor typ "**automatizace**" a vyberte "**Azure Automation – Získat výstup úlohy**" z výsledků hledání. Další informace o možnostech dostupných v této části najdete v [tématu Azure Automation – získat výstup úlohy](https://docs.microsoft.com/connectors/azureautomation/#get-job-output).
   - Vyplňte požadovaná pole (podobně jako vytvoření předchozí úlohy) – naplnění ID předplatného Azure, skupiny prostředků a účtu automatizace (jak bylo zadáno v podokně Účet automatizace).
   - Klikněte do pole **" ID úlohy**" pro nabídku **" Dynamický obsah**". V této nabídce vyberte možnost "**ID úlohy**".
   - Dokončení vytvoření této akce kliknutím na tlačítko **"Uložit tok**"

3. Vytvoření akce pro odesílání e-mailů pomocí integrace Office 365

   - Vyberte "**+Nový krok**", následovaný "**Přidat akci**" uvnitř podokna toku opakování
   - Ve vyhledávání soubor typu "**odeslat e-mail**" a vyberte "**Office 365 Outlook – Odeslat e-mail**" z výsledků hledání
   - Do pole **"Do"** zadejte e-mailovou adresu, na kterou je třeba odeslat e-mail s oznámením.
   - V poli**Předmět**v předmětu e-mailu, například "Automatické ladění doporučení e-mailové oznámení"
   - Klikněte do pole **"Tělo**" pro "**Dynamický obsah**" menu se zobrazí. V této nabídce v části "**Získat výstup úlohy**" vyberte "**Obsah**"
   - Dokončení vytvoření této akce kliknutím na tlačítko **"Uložit tok**"

> [!TIP]
> Chcete-li odesílat automatické e-maily různým příjemcům, vytvořte samostatné toky. V těchto dodatečných tocích změňte e-mailovou adresu příjemce v poli Do a řádek předmětu e-mailu v poli Předmět. Vytváření nových runbooků v Azure Automation s přizpůsobenými skripty PowerShellu (například se změnou ID předplatného Azure) umožňuje další přizpůsobení automatizovaných scénářů, například posílání e-mailů samostatným příjemcům na automatické ladění doporučení pro samostatná předplatná.
>

Výše uvedené uzavírá kroky potřebné ke konfiguraci pracovního postupu úlohy doručování e-mailů. Celý tok skládající se ze tří vytvořených akcí je zobrazen na následujícím obrázku.

![Zobrazit tok automatických oznámení o ladění e-mailů](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

Chcete-li tok otestovat, klikněte v pravém horním rohu uvnitř podokna toku na tlačítko**Spustit.**

Statistiky spuštění automatizovaných úloh, které ukazují úspěšnost odeslaných e-mailových oznámení, jsou zobrazeny v podokně Analýzy toku.

![Běh toku pro automatické ladění e-mailových oznámení](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

Analýza toku je užitečná pro sledování úspěšnosti spuštění úloh a v případě potřeby pro řešení potíží.  V případě řešení potíží můžete také chtít prozkoumat protokol spuštění skriptu Prostředí PowerShell přístupné prostřednictvím aplikace Azure Automation.

Konečný výstup automatizovaného e-mailu vypadá podobně jako následující e-mail přijatý po sestavení a spuštění tohoto řešení:

![Ukázkový výstup e-mailu z automatického ladění e-mailových oznámení](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

Úpravou skriptu prostředí PowerShell můžete upravit výstup a formátování automatického e-mailu podle svých potřeb.

V závislosti na vlastních scénářích můžete dále přizpůsobit řešení pro vytváření e-mailových oznámení na základě konkrétní události ladění a více příjemcům pro více předplatných nebo databází.

## <a name="next-steps"></a>Další kroky

- Další informace o tom, jak vám automatické ladění může pomoci zlepšit výkon databáze, najdete [v tématu Automatické ladění v Azure SQL Database](sql-database-automatic-tuning.md).
- Pokud chcete povolit automatické ladění v Azure SQL Database ke správě úlohy, přečtěte si informace [o povolení automatického ladění](sql-database-automatic-tuning-enable.md).
- Pokud chcete ručně zkontrolovat a použít doporučení pro automatické ladění, přečtěte si informace [o hledání a použití doporučení pro výkon](sql-database-advisor-portal.md).
