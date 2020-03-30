---
title: Aktualizace modelů Azure Analysis Services pomocí Azure Automation | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak kódovat aktualizace modelu pro Služby Azure Analysis Services pomocí Azure Automation.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: a79123d57f80474e1871ef68f9a92ea9417089ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572358"
---
# <a name="refresh-with-azure-automation"></a>Aktualizace pomocí Azure Automation

Pomocí Azure Automation a PowerShell Runbook můžete provádět automatizované operace aktualizace dat na tabulkových modelech Azure Analysis.  

Příklad v tomto článku používá [moduly PowerShell SqlServer](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps).

Ukázka powershellové runbooku, která ukazuje aktualizaci modelu, je k dispozici dále v tomto článku.  

## <a name="authentication"></a>Ověřování

Všechna volání musí být ověřena pomocí platného tokenu služby Azure Active Directory (OAuth 2).  Příklad v tomto článku použije instanční objekt (SPN) k ověření služby Azure Analysis Services.

Další informace o vytvoření instančního objektu najdete v [tématu Vytvoření instančního objektu pomocí portálu Azure](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="prerequisites"></a>Požadavky

> [!IMPORTANT]
> Následující příklad předpokládá, že brána firewall služby Azure Analysis Services je zakázána. Pokud je brána firewall povolena, bude nutné, aby byla veřejná IP adresa iniciátoru požadavku uvedena na seznamu povolených adres.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Nainstalujte moduly SqlServer z galerie PowerShellu.

1. V účtu Azure Automation klikněte na **Moduly**a pak **procházet galerii**.

2. Na vyhledávacím panelu vyhledejte **sqlserver**.

    ![Vyhledávací moduly](./media/analysis-services-refresh-azure-automation/1.png)

3. Vyberte sqlserver a klepněte na **tlačítko Importovat**.
 
    ![Importovat modul](./media/analysis-services-refresh-azure-automation/2.png)

4. Klikněte na tlačítko **OK**.
 
### <a name="create-a-service-principal-spn"></a>Vytvoření instančního objektu (SPN)

Další informace o vytvoření instančního objektu najdete [v tématu Vytvoření instančního objektu pomocí portálu Azure](../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="configure-permissions-in-azure-analysis-services"></a>Konfigurace oprávnění ve službě Azure Analysis Services
 
Instanční objekt, který vytvoříte, musí mít na serveru oprávnění správce serveru. Další informace naleznete [v tématu Přidání instančního objektu do role správce serveru](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>Návrh sady Runbook Azure Automation

1. V účtu automatizace vytvořte prostředek **pověření,** který bude použit k bezpečnému uložení instančního objektu.

    ![Vytvořit pověření](./media/analysis-services-refresh-azure-automation/6.png)

2. Zadejte podrobnosti o pověření.  Do **uživatelského jména**zadejte **clientid hlavního názvu spn**, pro **heslo**, zadejte **tajný klíč spn**.

    ![Vytvořit pověření](./media/analysis-services-refresh-azure-automation/7.png)

3. Import runbooku automatizace

    ![Importovat runbook](./media/analysis-services-refresh-azure-automation/8.png)

4. Vyhledejte soubor **Refresh-Model.ps1,** zadejte **název** a **popis**a klepněte na tlačítko **Vytvořit**.

    ![Importovat runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. Po vytvoření runbooku se automaticky přepne do režimu úprav.  Vyberte **Publikovat**.

    ![Publikovat runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > Prostředek pověření, který byl vytvořen dříve, je načten runbookpomocí příkazu **Get-AutomationPSCredential.**  Tento příkaz je pak předán příkazu **Invoke-ProcessASADatabase** PowerShell k provedení ověřování služby Azure Analysis Services.

6. Otestujte runbook klepnutím na tlačítko **Start**.

    ![Spuštění runbooku](./media/analysis-services-refresh-azure-automation/11.png)

7. Vyplňte parametry **DATABASENAME**, **ANALYSISSERVER**a **REFRESHTYPE** a klepněte na tlačítko **OK**. Parametr **WEBHOOKDATA** není vyžadován, pokud je sada Runbook spuštěna ručně.

    ![Spuštění runbooku](./media/analysis-services-refresh-azure-automation/12.png)

Pokud se runbook úspěšně provedl, obdržíte výstup, jako je následující:

![Úspěšné spuštění](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Použití samostatné sady Runbook Azure Automation

Runbook můžete nakonfigurovat tak, aby aktivovat aktualizaci modelu Služby Azure Analysis Services na plánovaném základě.

To lze nakonfigurovat takto:

1. V runbooku automatizace klikněte na **Plány**a potom **na Add a Schedule**.
 
    ![Vytvořit plán](./media/analysis-services-refresh-azure-automation/14.png)

2. Klikněte na **Naplánovat** > **vytvoření nového plánu**a vyplňte podrobnosti.

    ![Konfigurace plánu](./media/analysis-services-refresh-azure-automation/15.png)

3. Klikněte na **Vytvořit**.

4. Vyplňte parametry plánu. Ty se použijí pokaždé, když se spustí runbook. Parametr **WEBHOOKDATA** by měl zůstat prázdný při spuštění prostřednictvím plánu.

    ![Konfigurace parametrů](./media/analysis-services-refresh-azure-automation/16.png)

5. Klikněte na tlačítko **OK**.

## <a name="consume-with-data-factory"></a>Spotřebovávat s továrně dat

Chcete-li spustit knihu využívat pomocí Azure Data Factory, nejprve vytvořte **webhookpro** runbook. **Webhook** bude poskytovat adresu URL, kterou lze volat prostřednictvím webové aktivity Azure Data Factory.

> [!IMPORTANT]
> Chcete-li vytvořit **webhooku**, musí být **publikován**stav sady Runbook .

1. V aplikaci Automation Runbook klikněte na **Webhooks**a potom klepněte na **tlačítko Přidat webhook**.

   ![Přidat webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. Pojmenujte webhooku a expirace.  Název identifikuje pouze Webhook uvnitř runbook automatizace, netvoří součást adresy URL.

   >[!CAUTION]
   >Před zavřením průvodce zkontrolujte, zda adresu URL zkopírujete, protože ji nelze po zavření získat zpět.
    
   ![Konfigurace webhooku](./media/analysis-services-refresh-azure-automation/18.png)

    Parametry pro webhooku mohou zůstat prázdné.  Při konfiguraci webové aktivity Azure Data Factory, parametry lze předat do těla webového volání.

3. Konfigurace **webové aktivity** v datové továrně v Datové továrně

### <a name="example"></a>Příklad

   ![Příklad webové aktivity](./media/analysis-services-refresh-azure-automation/19.png)

**Adresa URL** je adresa URL vytvořená z webhooku.

**Tělo** je dokument JSON, který by měl obsahovat následující vlastnosti:


|Vlastnost  |Hodnota  |
|---------|---------|
|**AnalysisServicesDatabase**     |Název databáze Služby Azure Analysis Services <br/> Příklad: AdventureWorksDB         |
|**AnalysisServicesServer**     |Název serveru Služby Azure Analysis Services. <br/> Příklad: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType**     |Typ aktualizace, která má být provést. <br/> Příklad: Úplné         |

Příklad těla JSON:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Tyto parametry jsou definovány ve skriptu prostředí Runbook PowerShell.  Při spuštění webové aktivity je předaná datová část JSON WEBHOOKDATA.

To je deserializována a uložena jako parametry prostředí PowerShell, které jsou pak použity příkazem Invoke-ProcesASDatabase PowerShell.

![Rekonstruovaný webhook](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Použití hybridního pracovníka se službami Azure Analysis Services

Virtuální počítač Azure se statickou veřejnou IP adresou se dá použít jako hybridní pracovník Azure Automation.  Tuto veřejnou IP adresu pak můžete přidat do brány firewall služby Azure Analysis Services.

> [!IMPORTANT]
> Ujistěte se, že je veřejná IP adresa virtuálního počítače nakonfigurovaná jako statická.
>
>Další informace o konfiguraci hybridních pracovníků Azure Automation najdete [v tématu Automatizace prostředků v datovém centru nebo cloudu pomocí hybridního runbookworkeru](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker).

Po konfiguraci hybridního pracovníka vytvořte webhooku, jak je popsáno v části [Spotřebujte s factory dat](#consume-with-data-factory).  Jediným rozdílem je zde vybrat **spustit na** > **hybridní pracovník** možnost při konfiguraci Webhook.

Příklad webového háku pomocí hybridního pracovníka:

![Příklad hybridního pracovního háčku](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Ukázka runbooku prostředí PowerShell

Následující fragment kódu je příkladem toho, jak provést aktualizaci modelu Služby Azure Analysis Services pomocí sady Runbook prostředí PowerShell.

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
[ROZHRANÍ API PRO ODPOČINEK](https://docs.microsoft.com/rest/api/analysisservices/servers)
