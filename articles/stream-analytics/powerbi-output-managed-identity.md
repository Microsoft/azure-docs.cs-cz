---
title: Ověření úlohy Azure Stream Analytics ve výstupu Power BI pomocí spravované identity
description: Tento článek popisuje, jak používat spravované identity k ověření úlohy Azure Stream Analytics na výstupu Power BI.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/10/2020
ms.openlocfilehash: 31a5195038ef25acadc08e2acbedf8471b25833c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261410"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi"></a>Ověření úlohy Azure Stream Analytics v Power BI pomocí spravované identity

[Ověřování spravované identity](../active-directory/managed-identities-azure-resources/overview.md) pro výstup do Power BI poskytuje úlohám Stream Analytics přímý přístup k pracovnímu prostoru v rámci vašeho účtu Power BI. Tato funkce umožňuje, aby nasazení úloh Stream Analytics byla plně automatizovaná, protože už není nutné, aby se uživatel interaktivně přihlašuje k Power BI prostřednictvím portálu Azure. Kromě toho jsou teď lépe podporované dlouho běžící úlohy, které zapisují do Power BI, protože nebudete muset úlohu pravidelně znovu autorizovat.

Tento článek ukazuje, jak povolit spravovanou identitu pro výstupy Power BI pro úlohu Stream Analytics prostřednictvím portálu Azure a prostřednictvím nasazení Azure Resource Manageru.

## <a name="prerequisites"></a>Požadavky

Pro použití této funkce jsou vyžadovány následující:

- Účet Power BI s [licencí Pro](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro).

- Upgradovaný pracovní prostor v rámci účtu Power BI. Další podrobnosti najdete v [oznámení Power BI](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/) o této funkci.

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>Vytvoření úlohy Stream Analytics pomocí portálu Azure

1. Vytvořte novou úlohu Stream Analytics nebo otevřete existující úlohu na webu Azure Portal. Na řádku nabídek umístěném na levé straně obrazovky vyberte **Položku Spravovaná identita** umístěná v části **Konfigurovat**. Ujistěte se, že je vybrána možnost Použít systémově přiřazenou spravovanou identitu, a pak vdolní části obrazovky vyberte tlačítko **Uložit.**

   ![Konfigurace spravované identity Služby Stream Analytics](./media/common/stream-analytics-enable-managed-identity.png)

2. Než nakonfigurujete výstup, podejte pracovnímu prostoru Služby Stream Analytics přístup k pracovnímu prostoru Power BI podle pokynů v části [Udělit práci Služby Streamování Analytics k pracovnímu prostoru Power BI](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) v tomto článku.

3. Přejděte do části **Výstupy úlohy** Stream Analytic, vyberte **+ Přidat**a pak zvolte **Power BI**. Pak vyberte tlačítko **Autorizovat** a přihlaste se pomocí svého účtu Power BI.

   ![Autorizace s účtem Power BI](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. Po autorizaci bude rozevírací seznam naplněn všemi pracovními prostory, ke kterým máte přístup. Vyberte pracovní prostor, který jste autorizovali v předchozím kroku. Pak vyberte **spravovanou identitu** jako "režim ověřování". Nakonec vyberte tlačítko **Uložit.**

   ![Konfigurace výstupu Power BI pomocí spravované identity](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Nasazení podle modelu Azure Resource Manager

Azure Resource Manager umožňuje plně automatizovat nasazení úlohy Stream Analytics. Šablony Správce prostředků můžete nasadit buď pomocí Azure PowerShellu, nebo [pomocí příkazového příkazového příkazu k Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). Níže uvedené příklady používají azure cli.


1. Prostředek **Microsoft.StreamAnalytics/streamingjobs** se spravovanou identitou můžete vytvořit tak, že do části prostředků šablony Správce prostředků zahrnuli následující vlastnosti:

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   Tato vlastnost říká Azure Resource Manager vytvořit a spravovat identitu pro úlohu Stream Analytics. Níže je ukázka šablony Správce prostředků, která nasazuje úlohu Stream Analytics s povolenou spravovanou identitou a jímkou výstupu Power BI, která používá spravovanou identitu:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "pbi_managed_id",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "datasource":{
                                    "type":"PowerBI",
                                    "properties":{
                                        "dataset": "dataset_name",
                                        "table": "table_name",
                                        "groupId": "01234567-89ab-cdef-0123-456789abcdef",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    Nasazení výše uvedené úlohy do skupiny prostředků **ExampleGroup** pomocí níže uvedeného příkazu Azure CLI:

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. Po vytvoření úlohy použijte Azure Resource Manager k načtení úplné definice úlohy.

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    Výše uvedený příkaz vrátí odpověď jako níže:

    ```json
    {
        "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/streamingjobs/<resource-name>",
        "identity": {
            "principalId": "<principal-id>",
            "tenantId": "<tenant-id>",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "<resource-name>",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "<job-id>",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "<resource-group>",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

    Pokud plánujete použít rozhraní REST API Power BI k přidání úlohy Stream Analytics do pracovního prostoru Power BI, poznamenejte si vrácené "principalId".

3. Teď, když je úloha vytvořená, pokračujte v tomto článku v části [Předat úlohě Stream Analytics k pracovnímu prostoru Power BI.](#give-the-stream-analytics-job-access-to-your-power-bi-workspace)


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>Pořiďte pracovní muka Stream Analytics přístup k pracovnímu prostoru Power BI

Teď, když byla vytvořena úloha Stream Analytics, může jí být udělen přístup k pracovnímu prostoru Power BI.

### <a name="use-the-power-bi-ui"></a>Použití uj.

   > [!Note]
   > Abyste mohli přidat úlohu Stream Analytics do pracovního prostoru Power BI pomocí uživatelského rozhraní, musíte taky povolit přístup k hlavnímu nastavení služby v **nastavení vývojáře** na portálu pro správu Power BI. Další podrobnosti [najdete v tématu Začínáme s instančním objektem.](https://docs.microsoft.com/power-bi/developer/embed-service-principal)

1. Přejděte do nastavení přístupu k pracovnímu prostoru. Další podrobnosti naleznete v tomto článku: [Udělit přístup k pracovnímu prostoru](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace).

2. Do textového pole zadejte název úlohy Stream Analytics a jako úroveň přístupu vyberte **Přispěvatel.**

3. Vyberte **Přidat** a zavřete podokno.

   ![Přidání úlohy Stream Analytics do pracovního prostoru Power BI](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>Použití rutin PowerShellu PowerShellu

1. Nainstalujte rutiny `MicrosoftPowerBIMgmt` PowerShellu PowerShellu.

   > [!Important]
   > Ujistěte se, že používáte verzi 1.0.821 nebo novější rutiny.

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. Přihlaste se k Power BI.

```powershell
Login-PowerBI
```

3. Přidejte úlohu Stream Analytics jako přispěvatele do pracovního prostoru.

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>Použití rozhraní API Power BI REST

Úlohu Stream Analytics lze také přidat jako přispěvatele do pracovního prostoru pomocí rozhraní REST API "Přidat uživatele skupiny". Úplnou dokumentaci k tomuto rozhraní API naleznete zde: [Skupiny - Přidat uživatele skupiny](https://docs.microsoft.com/rest/api/power-bi/groups/addgroupuser).

**Ukázkový požadavek**
```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/users
```
Text žádosti
```json
{
    "groupUserAccessRight": "Contributor",
    "identifier": "<principal-id>",
    "principalType": "App"
}
```

## <a name="limitations"></a>Omezení
Níže jsou uvedeny omezení této funkce:

- Klasické pracovní prostory Power BI nejsou podporované.

- Účty Azure bez Azure Active Directory.

- Přístup pro více klientů není podporován. Objekt zabezpečení služby vytvořený pro danou úlohu Stream Analytics se musí nasazet ve stejném tenantovi služby Azure Active Directory, ve kterém byla úloha vytvořena, a nelze jej použít s prostředkem, který je umístěn v jiném tenantovi služby Azure Active Directory.

- [Přiřazená identita uživatele není podporována.](../active-directory/managed-identities-azure-resources/overview.md) To znamená, že nemůžete zadat vlastní instanční objekt, který bude používat jejich úloha Stream Analytics. Instanční objekt musí být generován službou Azure Stream Analytics.

## <a name="next-steps"></a>Další kroky

* [Integrace řídicího panelu Power BI s Azure Stream Analytics](./stream-analytics-power-bi-dashboard.md)
* [Principy výstupů z Azure Stream Analytics](./stream-analytics-define-outputs.md)
