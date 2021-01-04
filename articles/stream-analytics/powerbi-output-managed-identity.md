---
title: Použití spravované identity k ověření Azure Stream Analytics úlohy pro Power BI výstupu
description: Tento článek popisuje, jak pomocí spravovaných identit ověřit vaši Azure Stream Analyticsovou úlohu pro Power BI výstupu.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 3/10/2020
ms.openlocfilehash: 6aea427848afd39b9b9ca166856470a2f9b88694
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705094"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi-preview"></a>Použití spravované identity k ověření Azure Stream Analytics úlohy do Power BI (Preview)

[Spravované ověřování identity](../active-directory/managed-identities-azure-resources/overview.md) pro výstup do Power BI poskytuje Stream Analytics úlohy přímý přístup k pracovnímu prostoru v rámci účtu Power BI. Tato funkce umožňuje plně automatizovanou instalaci Stream Analytics úloh, protože už není potřeba, aby se uživatel interaktivně přihlásil k Power BI prostřednictvím Azure Portal. Kromě toho dlouhodobě běžící úlohy, které zapisují do Power BI, jsou teď lépe podporované, protože nebudete muset úlohu pravidelně znovu autorizovat.

V tomto článku se dozvíte, jak povolit spravovanou identitu pro Power BI výstupy Stream Analytics úlohy prostřednictvím Azure Portal a prostřednictvím nasazení Azure Resource Manager.

## <a name="prerequisites"></a>Požadavky

K použití této funkce jsou potřeba následující:

- Účet Power BI s [licencí pro](/power-bi/service-admin-purchasing-power-bi-pro).

- Upgradovaný pracovní prostor v rámci vašeho účtu Power BI. Další podrobnosti najdete v tématu oznámení o této funkci v [Power BI](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/) .

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>Vytvoření Stream Analytics úlohy pomocí Azure Portal

1. Vytvořte novou Stream Analytics úlohu nebo otevřete existující úlohu v Azure Portal. Z řádku nabídek umístěného na levé straně obrazovky vyberte **spravovaná identita** umístěná v části **Konfigurovat**. Ujistěte se, že je vybraná možnost použít spravovanou identitu přiřazenou systémem, a potom v dolní části obrazovky vyberte tlačítko **Uložit** .

   ![Konfigurace Stream Analytics spravované identity](./media/common/stream-analytics-enable-managed-identity.png)

2. Před konfigurací výstupu udělte úloze Stream Analytics přístup k pracovnímu prostoru Power BI podle pokynů v části [poskytnutí přístupu ke službě Stream Analytics k pracovnímu prostoru Power BI](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) tohoto článku.

3. Přejděte do části **výstupy** úlohy analýzy streamu, vyberte **+ Přidat** a pak zvolte **Power BI**. Pak vyberte tlačítko **autorizovat** a přihlaste se pomocí účtu Power BI.

   ![Autorizovat pomocí Power BI účtu](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. Po autorizaci se vyplní rozevírací seznam všemi pracovními prostory, ke kterým máte přístup. Vyberte pracovní prostor, který jste povolili v předchozím kroku. Pak vyberte **spravovaná identita** jako režim ověřování. Nakonec vyberte tlačítko **Uložit** .

   ![Konfigurace výstupu Power BI se spravovanou identitou](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Nasazení podle modelu Azure Resource Manager

Azure Resource Manager umožňuje plně automatizovat nasazení Stream Analytics úlohy. Šablony Správce prostředků můžete nasadit pomocí Azure PowerShell nebo rozhraní příkazového [řádku Azure](/cli/azure/). Níže uvedené příklady používají rozhraní příkazového řádku Azure CLI.


1. Pomocí spravované identity můžete vytvořit prostředek **Microsoft. StreamAnalytics/streamingjobs** , a to tak, že do oddílu prostředků v šabloně správce prostředků zadáte následující vlastnost:

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   Tato vlastnost oznamuje Azure Resource Manager, že má vytvořit a spravovat identitu pro vaši Stream Analytics úlohu. Níže je příklad šablony Správce prostředků, která nasazuje Stream Analytics úlohu se zapnutou spravovanou identitou a Power BI výstupní jímka, která používá spravovanou identitu:

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

    Nasaďte úlohu výše do skupiny prostředků s **příkladem** pomocí následujícího příkazu Azure CLI:

    ```azurecli
    az deployment group create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. Po vytvoření úlohy použijte Azure Resource Manager k načtení úplné definice úlohy.

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    Výše uvedený příkaz vrátí odpověď podobnou této:

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

    Pokud plánujete použít REST API Power BI k přidání úlohy Stream Analytics do pracovního prostoru Power BI, poznamenejte si vrácenou "principalId".

3. Teď, když je úloha vytvořená, přejděte k části [poskytnutí přístupu úlohy Stream Analytics k pracovnímu prostoru Power BI](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) v tomto článku.


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>Poskytněte pracovnímu prostoru Power BI přístup k úloze Stream Analytics.

Teď, když je úloha Stream Analytics vytvořená, může být dána přístup k pracovnímu prostoru Power BI. Jakmile budete mít přístup k úloze, počkejte několik minut, než se identita rozšíří.

### <a name="use-the-power-bi-ui"></a>Použití uživatelského rozhraní Power BI

   > [!Note]
   > Aby bylo možné přidat úlohu Stream Analytics do pracovního prostoru Power BI pomocí uživatelského rozhraní, je také nutné povolit přístup instančního objektu v nastavení pro **vývojáře** na portálu pro správu Power BI. Další podrobnosti najdete v tématu [Začínáme s instančním objektem](/power-bi/developer/embed-service-principal) .

1. Přejděte do nastavení přístupu k pracovnímu prostoru. Další podrobnosti najdete v tomto článku: [poskytnutí přístupu k vašemu pracovnímu prostoru](/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace).

2. Do textového pole zadejte název vaší Stream Analytics úlohy a jako úroveň přístupu vyberte **Přispěvatel** .

3. Vyberte **Přidat** a zavřete podokno.

   ![Přidat úlohu Stream Analytics do pracovního prostoru Power BI](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>Použití rutin Power BI PowerShellu

1. Nainstalujte `MicrosoftPowerBIMgmt` rutiny Power BI PowerShellu.

   > [!Important]
   > Ujistěte se prosím, že používáte verzi 1.0.821 nebo novější z rutin.

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. Přihlaste se k Power BI.

```powershell
Login-PowerBI
```

3. Přidejte úlohu Stream Analytics jako přispěvatel do pracovního prostoru.

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>Použití Power BI REST API

Úlohu Stream Analytics lze také přidat jako Přispěvatel k pracovnímu prostoru pomocí REST API přímo přidat skupinu uživatelů. Úplnou dokumentaci k tomuto rozhraní API najdete tady: [skupiny – přidat uživatele skupiny](/rest/api/power-bi/groups/addgroupuser).

**Ukázkový požadavek**
```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/users
```
Text požadavku
```json
{
    "groupUserAccessRight": "Contributor",
    "identifier": "<principal-id>",
    "principalType": "App"
}
```

## <a name="remove-managed-identity"></a>Odebrat spravovanou identitu

Spravovaná identita vytvořená pro Stream Analytics úlohu se odstraní jenom v případě, že se úloha odstraní. Neexistuje způsob, jak odstranit spravovanou identitu, aniž byste úlohu odstranili. Pokud už nechcete používat spravovanou identitu, můžete pro výstup změnit metodu ověřování. Spravovaná identita bude i nadále existovat, dokud se úloha neodstraní, a použije se, pokud se rozhodnete znovu použít spravované ověřování identity.

## <a name="limitations"></a>Omezení
Níže jsou uvedena omezení této funkce:

- Klasické pracovní prostory Power BI nejsou podporovány.

- Účty Azure bez Azure Active Directory.

- Přístup s více klienty není podporován. Instanční objekt vytvořený pro danou úlohu Stream Analytics se musí nacházet ve stejném klientovi Azure Active Directory, v němž byla úloha vytvořena, a nelze jej použít s prostředkem, který se nachází v jiném Azure Active Directory tenantovi.

- [Identita přiřazená uživateli](../active-directory/managed-identities-azure-resources/overview.md) není podporována. To znamená, že nemůžete zadat vlastní instanční objekt, který bude používat jejich Stream Analytics úlohy. Instanční objekt musí být vygenerovaný Azure Stream Analytics.

## <a name="next-steps"></a>Další kroky

* [Integrace řídicího panelu Power BI s Azure Stream Analytics](./stream-analytics-power-bi-dashboard.md)
* [Porozumění výstupům z Azure Stream Analytics](./stream-analytics-define-outputs.md)