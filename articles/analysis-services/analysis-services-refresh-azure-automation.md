---
title: Aktualizace modelů služby Azure Analysis Services s využitím Azure Automation | Dokumentace Microsoftu
description: Zjistěte, jak kód aktualizuje model s využitím Azure Automation.
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 1897193f0ae781029a7303c42ca8eeaa51389892
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64920579"
---
# <a name="refresh-with-azure-automation"></a>Aktualizace pomocí služby Azure Automation

Pomocí Azure Automation a Powershellové Runbooky provedením automatizovaných datových operace aktualizace na vašich tabulkových modelů analýzy Azure.  

V příkladu v tomto článku se používá [moduly Powershellu SqlServer](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps).

Ukázkový Runbook Powershellu, který ukazuje aktualizace modelu je k dispozici dále v tomto článku.  

## <a name="authentication"></a>Authentication

Všechna volání musí být ověřené na platný token Azure Active Directory (OAuth 2).  V příkladu v tomto článku budete používat k ověřování do služby Azure Analysis Services hlavní služby (SPN).

Další informace o vytvoření instančního objektu najdete v tématu]

## <a name="prerequisites"></a>Požadavky

> [!IMPORTANT]
> V následujícím příkladu se předpokládá, že brána firewall služby Azure Analysis Services je zakázaná. Pokud je povolená brána firewall, veřejnou IP adresu iniciátoru požadavek potřebovat uvedení na seznamu povolených v bráně firewall.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Instalace systému SQL Server moduly z Galerie prostředí PowerShell.

1. Ve vašem účtu Azure Automation, klikněte na tlačítko **moduly**, pak **procházet galerii**.

2. Na panelu hledání vyhledejte **SqlServer**.

    ![Hledat moduly](./media/analysis-services-refresh-azure-automation/1.png)

3. Vyberte systému SQL Server a pak klikněte na tlačítko **Import**.
 
    ![Import modulu](./media/analysis-services-refresh-azure-automation/2.png)

4. Klikněte na **OK**.
 
### <a name="create-a-service-principal-spn"></a>Vytvoření hlavního názvu služby (SPN)

Další informace o vytvoření instančního objektu najdete v tématu [vytvoření instančního objektu pomocí webu Azure portal](../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="configure-permissions-in-azure-analysis-services"></a>Konfigurace oprávnění ve službě Azure Analysis Services
 
Instanční objekt služby, které vytvoříte, musí mít oprávnění správce serveru na serveru. Další informace najdete v tématu [přidání hlavního názvu služby k roli správce serveru](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>Návrh Runbooku Azure Automation

1. V účtu Automation, vytvořit **pověření** prostředek, který se použije k bezpečnému ukládání instanční objekt služby.

    ![vytvoření přihlašovacích údajů](./media/analysis-services-refresh-azure-automation/6.png)

2. Zadejte podrobnosti přihlašovacích údajů.  Pro **uživatelské jméno**, zadejte **SPN ClientId**, pro **heslo**, zadejte **tajný klíč hlavní název služby**.

    ![vytvoření přihlašovacích údajů](./media/analysis-services-refresh-azure-automation/7.png)

3. Import Runbooku Automation

    ![Import Runbooku](./media/analysis-services-refresh-azure-automation/8.png)

4. Vyhledat **aktualizace Model.ps1** soubor, zadejte **název** a **popis**a potom klikněte na tlačítko **vytvořit**.

    ![Import Runbooku](./media/analysis-services-refresh-azure-automation/9.png)

5. Po vytvoření Runbooku ho automaticky přejde do režimu úprav.  Vyberte **Publikovat**.

    ![Publikování Runbooku](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > Prostředek přihlašovacích údajů, který byl vytvořen dříve jsou načítána pro sadu runbook pomocí **Get-AutomationPSCredential** příkazu.  Tento příkaz je pak předán **Invoke-ProcessASADatabase** příkazu Powershellu k provedení ověření ke službě Azure Analysis Services.

6. Test runbooku kliknutím **Start**.

    ![Spuštění Runbooku](./media/analysis-services-refresh-azure-automation/11.png)

7. Vyplňte **DATABASENAME**, **ANALYSISSERVER**, a **REFRESHTYPE** parametry a pak klikněte na tlačítko **OK**. **WEBHOOKDATA** parametr se nevyžaduje, když je Runbook spustit ručně.

    ![Spuštění Runbooku](./media/analysis-services-refresh-azure-automation/12.png)

Pokud sada Runbook byla úspěšně provedena, zobrazí se výstup podobný tomuto:

![Úspěšná spuštění](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Použít samostatný Runbooku Azure Automation

Sada Runbook je možné nakonfigurovat na trigger služby Azure Analysis Services vzor aktualizace na základě plánu.

Toto můžete nakonfigurovat následujícím způsobem:

1. V sadě Runbook služby Automation, klikněte na tlačítko **plány**, pak **přidat plán**.
 
    ![Vytvoření plánu](./media/analysis-services-refresh-azure-automation/14.png)

2. Klikněte na tlačítko **plán** > **vytvoření nového plánu**a potom vyplňte podrobnosti.

    ![Konfigurace plánu](./media/analysis-services-refresh-azure-automation/15.png)

3. Klikněte na možnost **Vytvořit**.

4. Vyplňte parametry pro daný plán. Tyto vlastnosti budou použity pokaždé, když sada Runbook spustí. **WEBHOOKDATA** parametr by měl být ponecháno prázdné při spuštění prostřednictvím plánu.

    ![Konfigurovat parametry](./media/analysis-services-refresh-azure-automation/16.png)

5. Klikněte na **OK**.

## <a name="consume-with-data-factory"></a>Využívat pomocí služby Data Factory

Chcete-li využívají sadu runbook pomocí služby Azure Data Factory, nejprve vytvořit **Webhooku** pro sadu runbook. **Webhooku** bude poskytovat adresu URL, které je možné vyvolat přes webové aktivity služby Azure Data Factory.

> [!IMPORTANT]
> Chcete-li vytvořit **Webhooku**, musí být stav sady Runbook **publikováno**.

1. V sadě Runbook služby Automation, klikněte na tlačítko **Webhooky**a potom klikněte na tlačítko **přidat Webhook**.

   ![Add Webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. Webhook zadejte název a vypršení platnosti.  Název identifikuje pouze Webhooku v Runbooku Automation, to není součástí adresy URL.

   >[!CAUTION]
   >Ujistěte se, že zkopírujete adresu URL před jeho zavřením na budete nemůže si ji zpět po zavření.
    
   ![Konfigurace Webhooku](./media/analysis-services-refresh-azure-automation/18.png)

    Parametry webhooku může zůstat prázdné.  Při konfiguraci služby Azure Data Factory webová aktivita, parametry mohou být předány do textu z webového volání.

3. Ve službě Data Factory, konfigurace **webové aktivity**

### <a name="example"></a>Příklad:

   ![Ukázkové webové aktivity](./media/analysis-services-refresh-azure-automation/19.png)

**URL** je adresa URL vytvořené z Webhooku.

**Tělo** je dokument JSON, který by měl obsahovat následující vlastnosti:


|Vlastnost  |Hodnota  |
|---------|---------|
|**AnalysisServicesDatabase**     |Název databáze Azure Analysis Services <br/> Příklad: AdventureWorksDB         |
|**AnalysisServicesServer**     |Název serveru Azure Analysis Services. <br/> Příklad: https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType**     |Typ aktualizace provádět. <br/> Příklad: Úplná         |

Příklad JSON textu:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Tyto parametry jsou definované v sadě runbook skript prostředí PowerShell.  Při spuštění aktivity webu je datová část JSON, který je předán parametr WEBHOOKDATA.

Toto je deserializovat a uloží jako parametry Powershellu, které jsou pak používány příkaz Invoke-ProcesASDatabase Powershellu.

![Deserializovaná Webhooku](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Použití procesu Hybrid Worker služby Azure Analysis Services

Virtuální počítač Azure se statickou veřejnou IP adresu je možné jako Azure Automation Hybrid Worker.  Tato veřejná IP adresa je potom možné přidat do brány firewall služby Azure Analysis Services.

> [!IMPORTANT]
> Ujistěte se, že je nakonfigurovaný jako statickou veřejnou IP adresu virtuálního počítače.
>
>Další informace o konfiguraci Azure Automation Hybrid Worker, najdete v článku [automatizaci prostředků v datovém centru nebo v cloudu s využitím procesu Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker).

Po nakonfigurování hybridního pracovního procesu vytvoření Webhooku, jak je popsáno v části [využívání pomocí služby Data Factory](#consume-with-data-factory).  Jediný rozdíl zde je výběr **spouštět** > **Hybrid Worker** možnost při konfiguraci Webhooku.

Webhook příklad použití procesu Hybrid Worker:

![Příklad hybridního pracovního procesu Webhooku](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Ukázkový Runbook Powershellu

Následující fragment kódu je příklad toho, jak provést aktualizaci modelu služby Azure Analysis Services pomocí Powershellového Runbooku.

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


## <a name="next-steps"></a>Další postup

[Ukázky](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
