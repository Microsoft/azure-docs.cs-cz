---
title: Ověření výstupu objektu blob pomocí Azure Stream Analytics spravované identity
description: Tento článek popisuje, jak používat spravované identity k ověření úlohy Azure Stream Analytics na výstupu úložiště objektů blob Azure.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 13f48a9e0bc3ed8f8c4d5f1b7da4b6c03f54cdf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129970"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage-output"></a>Ověření úlohy Azure Stream Analytics ve výstupu úložiště objektů blob Azure pomocí spravované identity

[Ověřování spravované identity](../active-directory/managed-identities-azure-resources/overview.md) pro výstup do úložiště objektů blob Azure poskytuje úlohám Stream Analytics přímý přístup k účtu úložiště namísto použití připojovacího řetězce. Kromě lepšího zabezpečení umožňuje tato funkce také zapisovat data do účtu úložiště ve virtuální síti (VNET) v rámci Azure.

Tento článek ukazuje, jak povolit spravovanou identitu pro výstupy objektů blob úlohy Stream Analytics prostřednictvím portálu Azure a prostřednictvím nasazení Azure Resource Manager.

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>Vytvoření úlohy Stream Analytics pomocí portálu Azure

1. Vytvořte novou úlohu Stream Analytics nebo otevřete existující úlohu na webu Azure Portal. Na řádku nabídek umístěném na levé straně obrazovky vyberte **Položku Spravovaná identita** umístěná v části **Konfigurovat**. Ujistěte se, že je vybrána možnost Použít spravovanou identitu přiřazenou systémem, a potom klepněte na tlačítko **Uložit** v dolní části obrazovky.

   ![Konfigurace spravované identity Služby Stream Analytics](./media/common/stream-analytics-enable-managed-identity.png)

2. V okně výstupních vlastností výstupního propadu úložiště objektů blob Azure vyberte rozevírací seznam Režim ověřování a zvolte **Spravovaná identita**. Informace týkající se ostatních vlastností výstupu [najdete v tématu Principy výstupů z Azure Stream Analytics](./stream-analytics-define-outputs.md). Jakmile budete hotovi, klikněte na **Uložit**.

   ![Konfigurace výstupu úložiště objektů blob Azure](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. Teď, když je úloha vytvořená, najdete v tomto článku [část Udělit práci služby Stream Analytics k vašemu účtu úložiště.](#give-the-stream-analytics-job-access-to-your-storage-account)

## <a name="azure-resource-manager-deployment"></a>Nasazení podle modelu Azure Resource Manager

Pomocí Azure Resource Manager uděláte plně automatizovat nasazení úlohy Stream Analytics. Šablony Správce prostředků můžete nasadit buď pomocí Azure PowerShellu, nebo [pomocí příkazového příkazového příkazu k Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). Níže uvedené příklady používají azure cli.


1. Prostředek **Microsoft.StreamAnalytics/streamingjobs** se spravovanou identitou můžete vytvořit tak, že do části prostředků šablony Správce prostředků zahrnuli následující vlastnosti:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Tato vlastnost říká Azure Resource Manager vytvořit a spravovat identitu pro úlohu Stream Analytics. Níže je ukázka šablony Správce prostředků, která nasazuje úlohu Stream Analytics s povolenou spravovanou identitou a jímkou výstupu objektu blob, která používá spravovanou identitu:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "MyStreamingJob",
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
                                "serialization": {
                                    "type": "JSON",
                                    "properties": {
                                        "encoding": "UTF8"
                                    }
                                },
                                "datasource":{
                                    "type":"Microsoft.Storage/Blob",
                                    "properties":{
                                        "storageAccounts": [
                                            { "accountName": "MyStorageAccount" }
                                        ],
                                        "container": "test",
                                        "pathPattern": "segment1/{date}/segment2/{time}",
                                        "dateFormat": "yyyy/MM/dd",
                                        "timeFormat": "HH",
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

    Výše uvedenou úlohu lze nasadit do skupiny prostředků **ExampleGroup** pomocí níže uvedeného příkazu Azure CLI:

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. Po vytvoření úlohy můžete použít Azure Resource Manager k načtení úplné definice úlohy.

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    Výše uvedený příkaz vrátí odpověď jako níže:

    ```json
    {
        "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/streamingjobs/{RESOURCE_NAME}",
        "identity": {
            "principalId": "{PRINCIPAL_ID}",
            "tenantId": "{TENANT_ID}",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "{RESOURCE_NAME}",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "{JOB_ID}",
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
        "resourceGroup": "{RESOURCE_GROUP}",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

   Poznamenejte si **principalId** z definice úlohy, která identifikuje spravovanou identitu vaší úlohy v rámci služby Azure Active Directory a bude použita v dalším kroku k udělení přístupu úlohy Stream Analytics k účtu úložiště.

3. Teď, když je úloha vytvořená, najdete v tomto článku [část Udělit práci služby Stream Analytics k vašemu účtu úložiště.](#give-the-stream-analytics-job-access-to-your-storage-account)


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>Pořiďte práci stream analytics ke svému účtu úložiště

Existují dvě úrovně přístupu, které můžete dát své úloze Stream Analytics:

1. **Přístup na úrovni kontejneru:** tato možnost poskytuje práci přístup k konkrétní existující kontejner.
2. **Přístup na úrovni účtu:** tato možnost poskytuje úloze obecný přístup k účtu úložiště, včetně možnosti vytvářet nové kontejnery.

Pokud nepotřebujete úlohu k vytvoření kontejnerů vaším jménem, měli byste zvolit **přístup na úrovni kontejneru,** protože tato možnost udělí úloze požadovanou minimální úroveň přístupu. Obě možnosti jsou vysvětleny níže pro portál Azure a příkazový řádek.

### <a name="grant-access-via-the-azure-portal"></a>Udělení přístupu prostřednictvím portálu Azure

#### <a name="container-level-access"></a>Přístup na úrovni kontejneru

1. Přejděte do konfiguračního podokna kontejneru v rámci účtu úložiště.

2. Na levé straně vyberte **řízení přístupu (IAM).**

3. V části "Přidat přiřazení role" klikněte na **Přidat**.

4. V podokně přiřazení rolí:

    1. Nastavení **role** na "Přispěvatel dat objektů blob úložiště"
    2. Ujistěte **se, že přiřadit přístup k** rozevírací informace je nastavena na "Azure AD uživatele, skupiny nebo instanční objekt".
    3. Do vyhledávacího pole zadejte název úlohy Stream Analytics.
    4. Vyberte úlohu Stream Analytics a klikněte na **Uložit**.

   ![Udělit přístup ke kontejneru](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>Přístup na úrovni účtu

1. Přejděte na svůj účet úložiště.

2. Na levé straně vyberte **řízení přístupu (IAM).**

3. V části "Přidat přiřazení role" klikněte na **Přidat**.

4. V podokně přiřazení rolí:

    1. Nastavení **role** na "Přispěvatel dat objektů blob úložiště"
    2. Ujistěte **se, že přiřadit přístup k** rozevírací informace je nastavena na "Azure AD uživatele, skupiny nebo instanční objekt".
    3. Do vyhledávacího pole zadejte název úlohy Stream Analytics.
    4. Vyberte úlohu Stream Analytics a klikněte na **Uložit**.

   ![Udělit přístup k účtu](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>Udělení přístupu prostřednictvím příkazového řádku

#### <a name="container-level-access"></a>Přístup na úrovni kontejneru

Chcete-li udělit přístup k určitému kontejneru, spusťte následující příkaz pomocí příkazového příkazu Azure:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>Přístup na úrovni účtu

Chcete-li udělit přístup k celému účtu, spusťte následující příkaz pomocí příkazového příkazu Azure:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>Povolení přístupu k virtuální síti

Při konfiguraci **brány firewall a virtuálních sítí**účtu úložiště můžete volitelně povolit síťový provoz z jiných důvěryhodných služeb společnosti Microsoft. Když se Stream Analytics ověřuje pomocí spravované identity, poskytuje důkaz, že požadavek pochází z důvěryhodné služby. Níže jsou uvedeny pokyny k povolení této výjimky přístupu virtuální sítě.

1.  Přejděte do podokna Brány firewall a virtuální sítě v konfiguračním podokně účtu úložiště.
2.  Zajistěte, aby byla povolena možnost Povolit důvěryhodným službám společnosti Microsoft přístup k tomuto účtu úložiště.
3.  Pokud jste ji povolili, klepněte na tlačítko **Uložit**.

   ![Povolení přístupu k virtuální síti](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="limitations"></a>Omezení
Níže jsou uvedeny aktuální omezení této funkce:

1. Klasické účty Azure Storage.

2. Účty Azure bez Azure Active Directory.

3. Přístup pro více klientů není podporován. Objekt zabezpečení služby vytvořený pro danou úlohu Stream Analytics se musí nasazet ve stejném tenantovi služby Azure Active Directory, ve kterém byla úloha vytvořena, a nelze jej použít s prostředkem, který je umístěn v jiném tenantovi služby Azure Active Directory.

4. [Přiřazená identita uživatele není podporována.](../active-directory/managed-identities-azure-resources/overview.md) To znamená, že uživatel nemůže zadat vlastní instanční objekt, který má být použit jejich úlohou Stream Analytics. Instanční objekt musí být generován službou Azure Stream Analytics.

## <a name="next-steps"></a>Další kroky

* [Principy výstupů z Azure Stream Analytics](./stream-analytics-define-outputs.md)
* [Azure Stream Analytics vlastní dělení výstupu objektu blob](./stream-analytics-custom-path-patterns-blob-storage-output.md)
