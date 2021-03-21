---
title: Ověřit výstup objektu BLOB pomocí spravované identity Azure Stream Analytics
description: Tento článek popisuje, jak pomocí spravovaných identit ověřit úlohu Azure Stream Analytics do výstupu služby Azure Blob Storage.
author: kim-ale
ms.author: kimal
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 369348133f7395f5db5b5923bd438cec8e4ad733
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954374"
---
# <a name="use-managed-identity-preview-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage"></a>Použití spravované identity (Preview) k ověření Azure Stream Analytics úlohy do Azure Blob Storage

[Spravované ověřování identity](../active-directory/managed-identities-azure-resources/overview.md) (Preview) pro výstup do služby Azure Blob storage poskytuje Stream Analytics úlohy přímý přístup k účtu úložiště namísto použití připojovacího řetězce. Kromě vylepšeného zabezpečení tato funkce také umožňuje zapisovat data do účtu úložiště v Virtual Network (VNET) v rámci Azure.

V tomto článku se dozvíte, jak povolit spravovanou identitu pro výstupy objektů BLOB Stream Analytics úlohy prostřednictvím Azure Portal a prostřednictvím Azure Resource Manager nasazení.

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>Vytvoření Stream Analytics úlohy pomocí Azure Portal

1. Vytvořte novou Stream Analytics úlohu nebo otevřete existující úlohu v Azure Portal. Z řádku nabídek umístěného na levé straně obrazovky vyberte **spravovaná identita** umístěná v části **Konfigurovat**. Ujistěte se, že je vybraná možnost použít spravovanou identitu přiřazenou systémem, a pak klikněte na tlačítko **Uložit** v dolní části obrazovky.

   ![Konfigurace Stream Analytics spravované identity](./media/common/stream-analytics-enable-managed-identity.png)

2. V okně Vlastnosti výstupu výstupní jímky služby Azure Blob Storage vyberte rozevírací seznam režim ověřování a zvolte **spravovaná identita**. Informace týkající se dalších vlastností výstup naleznete v tématu [Principy výstupů z Azure Stream Analytics](./stream-analytics-define-outputs.md). Jakmile budete hotovi, klikněte na **Uložit**.

   ![Konfigurace výstupu služby Azure Blob Storage](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. Teď, když je úloha vytvořená, přečtěte si část [poskytnutí přístupu k účtu úložiště](#give-the-stream-analytics-job-access-to-your-storage-account) v tomto článku v části Stream Analytics úlohy.

## <a name="azure-resource-manager-deployment"></a>Nasazení podle modelu Azure Resource Manager

Pomocí Azure Resource Manager můžete plně automatizovat nasazení Stream Analytics úlohy. Šablony Správce prostředků můžete nasadit pomocí Azure PowerShell nebo rozhraní příkazového [řádku Azure](/cli/azure/). Níže uvedené příklady používají rozhraní příkazového řádku Azure CLI.


1. Pomocí spravované identity můžete vytvořit prostředek **Microsoft. StreamAnalytics/streamingjobs** , a to tak, že do oddílu prostředků v šabloně správce prostředků zadáte následující vlastnost:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Tato vlastnost oznamuje Azure Resource Manager, že má vytvořit a spravovat identitu pro vaši Stream Analytics úlohu. Níže je příklad šablony Správce prostředků, která nasazuje Stream Analytics úlohu se zapnutou spravovanou identitou a výstupní jímka objektu blob, která používá spravovanou identitu:

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

    Výše uvedenou úlohu můžete nasadit do skupiny prostředků **example** pomocí následujícího příkazu Azure CLI:

    ```azurecli
    az deployment group create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. Po vytvoření úlohy můžete použít Azure Resource Manager k načtení úplné definice úlohy.

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    Výše uvedený příkaz vrátí odpověď podobnou této:

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

   Poznamenejte si **principalId** z definice úlohy, která identifikuje spravovanou identitu vaší úlohy v rámci Azure Active Directory a použije se v dalším kroku k udělení přístupu úlohy Stream Analytics k účtu úložiště.

3. Teď, když je úloha vytvořená, přečtěte si část [poskytnutí přístupu k účtu úložiště](#give-the-stream-analytics-job-access-to-your-storage-account) v tomto článku v části Stream Analytics úlohy.


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>Poskytněte účtu úložiště přístup k úloze Stream Analytics.

Existují dvě úrovně přístupu, které můžete použít k poskytnutí Stream Analytics úlohy:

1. **Přístup na úrovni kontejneru:** Tato možnost nastaví přístup úlohy ke konkrétnímu existujícímu kontejneru.
2. **Přístup na úrovni účtu:** Tato možnost poskytuje úlohu obecný přístup k účtu úložiště, včetně možnosti vytvářet nové kontejnery.

Pokud nepotřebujete úlohu k vytváření kontejnerů vaším jménem, měli byste zvolit **přístup na úrovni kontejneru** , protože tato možnost udělí úlohy minimální požadovanou úroveň přístupu. Obě možnosti jsou vysvětleny níže pro Azure Portal a příkazového řádku.

### <a name="grant-access-via-the-azure-portal"></a>Udělení přístupu přes Azure Portal

#### <a name="container-level-access"></a>Přístup na úrovni kontejneru

1. Přejděte do konfiguračního podokna kontejneru v rámci svého účtu úložiště.

2. Na levé straně vyberte **Access Control (IAM)** .

3. V části přidat přiřazení role klikněte na **Přidat**.

4. V podokně přiřazení role:

    1. Nastavte **roli** na Přispěvatel dat objektů BLOB úložiště.
    2. Zajistěte, aby byl ovládací prvek **přiřadit přístup k** rozevíracímu seznamu nastaven na možnost "uživatel", skupina nebo instanční objekt služby Azure AD.
    3. Do vyhledávacího pole zadejte název vaší Stream Analytics úlohy.
    4. Vyberte úlohu Stream Analytics a klikněte na **Uložit**.

   ![Udělení přístupu k kontejneru](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>Přístup na úrovni účtu

1. Přejděte na svůj účet úložiště.

2. Na levé straně vyberte **Access Control (IAM)** .

3. V části přidat přiřazení role klikněte na **Přidat**.

4. V podokně přiřazení role:

    1. Nastavte **roli** na Přispěvatel dat objektů BLOB úložiště.
    2. Zajistěte, aby byl ovládací prvek **přiřadit přístup k** rozevíracímu seznamu nastaven na možnost "uživatel", skupina nebo instanční objekt služby Azure AD.
    3. Do vyhledávacího pole zadejte název vaší Stream Analytics úlohy.
    4. Vyberte úlohu Stream Analytics a klikněte na **Uložit**.

   ![Udělení přístupu k účtu](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>Udělení přístupu prostřednictvím příkazového řádku

#### <a name="container-level-access"></a>Přístup na úrovni kontejneru

Pokud chcete udělit přístup ke konkrétnímu kontejneru, spusťte následující příkaz pomocí Azure CLI:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>Přístup na úrovni účtu

Pokud chcete povolit přístup k celému účtu, spusťte následující příkaz pomocí Azure CLI:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>Povolit přístup k virtuální síti

Při konfiguraci **bran firewall a virtuálních sítí** v účtu úložiště můžete volitelně v síťovém provozu z jiných důvěryhodných služeb Microsoftu. Když se Stream Analytics ověřuje pomocí spravované identity, poskytne důkaz o tom, že žádost pochází z důvěryhodné služby. Níže jsou uvedené pokyny, jak povolit tuto výjimku přístupu k virtuální síti.

1.    V podokně Konfigurace účtu úložiště přejděte do podokna brány firewall a virtuální sítě.
2.    Zajistěte, aby byla povolená možnost Povolit důvěryhodné služby Microsoftu pro přístup k tomuto účtu úložiště.
3.    Pokud jste ho povolili, klikněte na **Uložit**.

   ![Povolit přístup k virtuální síti](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="remove-managed-identity"></a>Odebrat spravovanou identitu

Spravovaná identita vytvořená pro Stream Analytics úlohu se odstraní jenom v případě, že se úloha odstraní. Neexistuje způsob, jak odstranit spravovanou identitu, aniž byste úlohu odstranili. Pokud už nechcete používat spravovanou identitu, můžete pro výstup změnit metodu ověřování. Spravovaná identita bude i nadále existovat, dokud se úloha neodstraní, a použije se, pokud se rozhodnete znovu použít spravované ověřování identity.

## <a name="limitations"></a>Omezení
Níže jsou uvedena aktuální omezení této funkce:

1. Účty klasického Azure Storage.

2. Účty Azure bez Azure Active Directory.

3. Přístup s více klienty není podporován. Instanční objekt vytvořený pro danou úlohu Stream Analytics se musí nacházet ve stejném klientovi Azure Active Directory, v němž byla úloha vytvořena, a nelze jej použít s prostředkem, který se nachází v jiném Azure Active Directory tenantovi.

4. [Identita přiřazená uživateli](../active-directory/managed-identities-azure-resources/overview.md) není podporována. To znamená, že uživatel nemůže zadat vlastní instanční objekt, který bude používat jejich Stream Analytics úlohy. Instanční objekt musí být vygenerovaný Azure Stream Analytics.

## <a name="next-steps"></a>Další kroky

* [Porozumění výstupům z Azure Stream Analytics](./stream-analytics-define-outputs.md)
* [Azure Stream Analytics vlastní dělení výstupu objektů BLOB](./stream-analytics-custom-path-patterns-blob-storage-output.md)
