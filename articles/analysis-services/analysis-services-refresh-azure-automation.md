---
title: Aktualizovat Azure Analysis Services modely pomocí Azure Automation | Microsoft Docs
description: Tento článek popisuje, jak model kódu aktualizuje pro Azure Analysis Services pomocí Azure Automation.
author: chrislound
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: chlound
ms.openlocfilehash: fe811c81d0774393f40dc5c8403d1af8b22da109
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019133"
---
# <a name="refresh-with-azure-automation"></a>Aktualizace pomocí Azure Automation

Pomocí Azure Automation a PowerShellových runbooků můžete provádět operace automatizované aktualizace dat ve vašich tabelárních modelech Azure Analysis.  

Příklad v tomto článku používá [modul SQL SQLServer](/powershell/module/sqlserver/?view=sqlserver-ps). Ukázková sada Runbook PowerShellu, která demonstruje aktualizaci modelu, je k dispozici dále v tomto článku.  

## <a name="authentication"></a>Authentication

Všechna volání musí být ověřena pomocí platného tokenu Azure Active Directory (OAuth 2).  Příklad v tomto článku používá k ověření Azure Analysis Services instanční objekt (SPN). Další informace najdete v tématu [Vytvoření instančního objektu pomocí služby Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="prerequisites"></a>Požadavky

> [!IMPORTANT]
> Následující příklad předpokládá, že je brána firewall Azure Analysis Services zakázaná. Pokud je povolená brána firewall, musí být do pravidla brány firewall zahrnutá veřejná IP adresa iniciátoru žádosti.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Nainstalujte moduly SqlServer z Galerie prostředí PowerShell.

1. V Azure Automation účtu klikněte na **moduly**a pak **Procházet Galerie**.

2. Na panelu hledání vyhledejte **SQLServer**.

    ![Hledat moduly](./media/analysis-services-refresh-azure-automation/1.png)

3. Vyberte SqlServer a pak klikněte na **importovat**.
 
    ![Importovat modul](./media/analysis-services-refresh-azure-automation/2.png)

4. Klikněte na **OK**.
 
### <a name="create-a-service-principal-spn"></a>Vytvoření instančního objektu (SPN)

Další informace o vytváření instančního objektu najdete v tématu [Vytvoření instančního objektu pomocí Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="configure-permissions-in-azure-analysis-services"></a>Konfigurace oprávnění v Azure Analysis Services
 
Objekt služby, který vytvoříte, musí mít na serveru oprávnění správce serveru. Další informace najdete v tématu [Přidání instančního objektu k roli správce serveru](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>Návrh sady Azure Automation Runbook

1. V účtu Automation vytvořte prostředek s **přihlašovacími údaji** , který se použije k bezpečnému uložení instančního objektu.

    ![Snímek obrazovky zobrazující stránku přihlašovací údaje se zvolenou akcí přidat pověření](./media/analysis-services-refresh-azure-automation/6.png)

2. Zadejte podrobnosti přihlašovacích údajů. Do pole **uživatelské jméno**zadejte ID aplikace instančního objektu (AppID) a potom zadejte do pole **heslo**tajný klíč instančního objektu.

    ![Vytvořit přihlašovací údaj](./media/analysis-services-refresh-azure-automation/7.png)

3. Importujte Runbook služby Automation.

    ![Snímek obrazovky zobrazující stránku sady Runbook s vybranou akcí importovat sadu Runbook](./media/analysis-services-refresh-azure-automation/8.png)

4. Vyhledejte soubor [Refresh-Model.ps1](#sample-powershell-runbook) , zadejte **název** a **Popis**a pak klikněte na **vytvořit**.

    > [!NOTE]
    > Pokud chcete vytvořit soubor s názvem Refresh-Model.ps1 a uložit ho do sady Runbook, použijte v části [ukázkový powershellový Runbook](#sample-powershell-runbook) na konci tohoto dokumentu.

    ![Importovat Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. Po vytvoření se Runbook automaticky přejde do režimu úprav.  Vyberte **Publikovat**.

    ![Publikování Runbooku](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > Prostředek přihlašovacích údajů, který byl vytvořen dříve, je načten sadou Runbook pomocí příkazu **Get-AutomationPSCredential** .  Tento příkaz se pak předává příkazu PowerShellu **Invoke-ProcessASADatabase** , který provádí ověřování pro Azure Analysis Services.

6. Otestujte Runbook kliknutím na tlačítko **Start**.

    ![Snímek obrazovky zobrazující stránku "Přehled" se zvolenou akcí spustit.](./media/analysis-services-refresh-azure-automation/11.png)

7. Vyplňte parametry **DatabaseName**, **ANALYSISSERVER**a **REFRESHTYPE** a pak klikněte na **OK**. Parametr **WEBHOOKDATA** není při ručním spuštění sady Runbook vyžadován.

    ![Spuštění runbooku](./media/analysis-services-refresh-azure-automation/12.png)

Pokud se sada Runbook úspěšně provedla, zobrazí se výstup podobný následujícímu:

![Úspěšné spuštění](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Použití Azure Automation sady Runbook, která je samostatná

Runbook se dá nakonfigurovat tak, aby aktivoval Azure Analysis Services aktualizace modelu na základě plánu.

Dá se nakonfigurovat takto:

1. V sadě Runbook Automation klikněte na **plány**a pak **na Přidat plán**.
 
    ![Vytvořit plán](./media/analysis-services-refresh-azure-automation/14.png)

2. Klikněte na **naplánovat**  >  **vytvořit nový plán**a pak vyplňte podrobnosti.

    ![Konfigurovat plán](./media/analysis-services-refresh-azure-automation/15.png)

3. Klikněte na **Vytvořit**.

4. Vyplňte parametry pro daný plán. Budou použity při každém spuštění triggeru sady Runbook. Parametr **WEBHOOKDATA** by měl zůstat prázdný při spuštění prostřednictvím plánu.

    ![Konfigurace parametrů](./media/analysis-services-refresh-azure-automation/16.png)

5. Klikněte na **OK**.

## <a name="consume-with-data-factory"></a>Využití s Data Factory

Pokud chcete sadu Runbook využívat pomocí Azure Data Factory, vytvořte nejprve **Webhook** pro sadu Runbook. **Webhook** poskytne adresu URL, kterou je možné volat pomocí Azure Data Factory webové aktivity.

> [!IMPORTANT]
> K vytvoření **Webhooku**se musí **publikovat**stav Runbooku.

1. V sadě Runbook Automation klikněte na **Webhooky**a pak klikněte na **Přidat Webhook**.

   ![Přidat Webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. Dejte Webhooku název a vypršení platnosti.  Název pouze identifikuje Webhook uvnitř Runbooku služby Automation, ale netvoří součást adresy URL.

   >[!CAUTION]
   >Před zavřením Průvodce Nezapomeňte zkopírovat jeho adresu URL, protože ji nelze vrátit po zavření.
    
   ![Konfigurace Webhooku](./media/analysis-services-refresh-azure-automation/18.png)

    Parametry Webhooku mohou zůstat prázdné.  Při konfiguraci webové aktivity Azure Data Factory lze parametry předat do těla webového volání.

3. V Data Factory konfigurace **aktivity webu**

### <a name="example"></a>Příklad

   ![Ukázková aktivita webu](./media/analysis-services-refresh-azure-automation/19.png)

**Adresa URL** je adresa URL vytvořená z Webhooku.

**Tělo** je dokument JSON, který by měl obsahovat následující vlastnosti:


|Vlastnost  |Hodnota  |
|---------|---------|
|**AnalysisServicesDatabase**     |Název databáze Azure Analysis Services <br/> Příklad: AdventureWorksDB         |
|**AnalysisServicesServer**     |Název serveru Azure Analysis Services. <br/> Příklad: https: \/ /westus.asazure.Windows.NET/Servers/MyServer/Models/AdventureWorks/         |
|**DatabaseRefreshType**     |Typ aktualizace, která má být provedena. <br/> Příklad: Full         |

Příklad těla JSON:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Tyto parametry jsou definovány ve skriptu Runbooku PowerShell.  Po spuštění aktivity webu je předaná datová část JSON WEBHOOKDATA.

Toto se deserializovat a uloží jako parametry PowerShellu, které pak používá příkaz Invoke-ProcesASDatabase PowerShellu.

![Deserializovaný Webhook](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Použít Hybrid Worker s Azure Analysis Services

Virtuální počítač Azure se statickou veřejnou IP adresou se dá použít jako Hybrid Worker Azure Automation.  Tuto veřejnou IP adresu je pak možné přidat do brány Azure Analysis Services firewall.

> [!IMPORTANT]
> Ujistěte se, že je veřejná IP adresa virtuálního počítače nakonfigurovaná jako statická.
>
>Další informace o konfiguraci Azure Automation hybridních pracovních procesů najdete v tématu [instalace Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md#hybrid-runbook-worker-installation).

Po nakonfigurování Hybrid Worker vytvořte Webhook, jak je popsáno v části věnované [použití s Data Factory](#consume-with-data-factory).  Jediným rozdílem je, že **Run on**  >  při konfiguraci Webhooku vyberte možnost spustit při**Hybrid Worker** .

Příklad Webhooku s použitím Hybrid Worker:

![Příklad Hybrid Worker Webhooku](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Ukázka Runbooku PowerShellu

Následující fragment kódu je příklad, jak provést Azure Analysis Services aktualizace modelu pomocí Runbooku PowerShellu.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## <a name="next-steps"></a>Další kroky

[ukázky](analysis-services-samples.md)  
[REST API](/rest/api/analysisservices/servers)