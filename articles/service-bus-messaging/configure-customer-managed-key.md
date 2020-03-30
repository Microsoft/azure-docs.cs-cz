---
title: Konfigurace vlastního klíče pro šifrování dat azure service bus v klidovém stavu
description: Tento článek obsahuje informace o tom, jak nakonfigurovat vlastní klíč pro šifrování úložiště dat Azure Service Bus.
services: service-bus-messaging
ms.service: service-bus
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: aschhab
ms.openlocfilehash: aeb9a9730ddc61793e49c9e042906457e0068d9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624090"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Konfigurace klíčů spravovaných zákazníky pro šifrování dat Azure Service Bus v klidovém stavu pomocí portálu Azure
Azure Service Bus Premium poskytuje šifrování dat v klidovém stavu pomocí šifrování služby Azure Storage Service (Azure SSE). Service Bus Premium spoléhá na Azure Storage pro ukládání dat a ve výchozím nastavení všechna data, která jsou uložená ve službě Azure Storage je šifrována pomocí klíčů spravovaných Microsoftem. 

## <a name="overview"></a>Přehled
Azure Service Bus teď podporuje možnost šifrování dat v klidovém stavu pomocí klíčů spravovaných microsoftem nebo klíčů spravovaných zákazníky (Bring Your Own Key - BYOK). Tato funkce umožňuje vytvářet, otáčet, zakázat a odvolat přístup ke klíčům spravovaným zákazníkem, které se používají k šifrování služby Azure Service Bus v klidovém stavu.

Povolení funkce BYOK je proces jednorázového nastavení v oboru názvů.

> [!NOTE]
> Existují některé upozornění pro klienta spravovaného klíče pro šifrování na straně služby. 
>   * Tato funkce je podporovaná úrovní [Azure Service Bus Premium.](service-bus-premium-messaging.md) Nelze povolit pro obory názvů service bus úrovně standard.
>   * Šifrování lze povolit pouze pro nové nebo prázdné obory názvů. Pokud obor názvů obsahuje data, operace šifrování se nezdaří.

Azure Key Vault můžete použít ke správě klíčů a auditování využití klíčů. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít Azure Key Vault API ke generování klíčů. Další informace o Azure Key Vault najdete v tématu [Co je Azure Key Vault?](../key-vault/key-vault-overview.md)

Tento článek ukazuje, jak nakonfigurovat trezor klíčů pomocí klíčů spravovaných zákazníkem pomocí portálu Azure. Informace o tom, jak vytvořit trezor klíčů pomocí webu Azure Portal, najdete v [tématu Úvodní příručka: Nastavení a načtení tajného klíče z Azure Key Vault pomocí portálu Azure](../key-vault/quick-create-portal.md).

> [!IMPORTANT]
> Použití klíčů spravovaných zákazníky s Azure Service Bus vyžaduje, aby trezor klíčů měl nakonfigurované dvě požadované vlastnosti. Jsou to: **Obnovitelné odstranění** a **Nečistit**. Tyto vlastnosti jsou ve výchozím nastavení povolené při vytváření nového trezoru klíčů na webu Azure Portal. Pokud však potřebujete povolit tyto vlastnosti v existujícím trezoru klíčů, musíte použít buď PowerShell nebo Azure CLI.

## <a name="enable-customer-managed-keys"></a>Povolení klíčů spravovaných zákazníkem
Pokud chcete povolit klíče spravované zákazníky na webu Azure Portal, postupujte takto:

1. Přejděte do oboru názvů Service Bus Premium.
2. Na stránce **Nastavení** oboru názvů služby Service Bus vyberte **Možnost Šifrování**.
3. Vyberte **šifrování klíče spravované zákazníkem v klidovém stavu,** jak je znázorněno na následujícím obrázku.

    ![Povolit klíč spravovaný zákazníkem](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Nastavení trezoru klíčů s klíči

Po povolení klíčů spravovaných zákazníkem je potřeba přidružit klíč spravovaný zákazníkem k oboru názvů Azure Service Bus. Service Bus podporuje pouze Azure Key Vault. Pokud povolíte **možnost Šifrování s klíčem spravovaným zákazníkem** v předchozí části, musíte mít klíč importován do úložiště klíčů Azure. Klíče musí mít také pro klíč nakonfigurovány **funkce Obnovitelné odstranění** a **Neodstraňovat.** Tato nastavení lze nakonfigurovat pomocí [prostředí PowerShell](../key-vault/key-vault-soft-delete-powershell.md) nebo [CLI](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection).

1. Chcete-li vytvořit nový trezor klíčů, postupujte podle [úvodního panelu Azure](../key-vault/key-vault-overview.md)Key Vault . Další informace o importu existujících klíčů naleznete [v tématu O klíčích, tajných klíčích a certifikátech](../key-vault/about-keys-secrets-and-certificates.md).
1. Chcete-li při vytváření trezoru zapnout ochranu proti měkkému odstranění i vymazání, použijte příkaz [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) .

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Chcete-li přidat ochranu proti vymazání do existujícího trezoru (který již má povoleno obnovitelné odstranění), použijte příkaz [az keyvault update.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Vytvořte klíče následujícím postupem:
    1. Chcete-li vytvořit novou klávesu, vyberte **Generovat/importovat** z nabídky **Klávesy** v části **Nastavení**.
        
        ![Tlačítko Vybrat generovat/importovat](./media/configure-customer-managed-key/select-generate-import.png)

    1. Nastavte **možnosti** **pro generování** a pojmenujte klíč.

        ![Vytvoření klíče](./media/configure-customer-managed-key/create-key.png) 

    1. Nyní můžete vybrat tento klíč, který chcete přidružit k oboru názvů Service Bus pro šifrování z rozevíracího seznamu. 

        ![Vybrat klíč z trezoru klíčů](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > Pro redundanci můžete přidat až 3 klíče. V případě, že platnost jednoho z klíčů vypršela nebo není přístupná, budou ostatní klíče použity k šifrování.
        
    1. Vyplňte podrobnosti klíče a klepněte na **tlačítko Vybrat**. To umožní šifrování dat v klidovém stavu v oboru názvů pomocí klientského spravovaného klíče. 


    > [!IMPORTANT]
    > Pokud chcete používat klíč spravovaný zákazníkem spolu s geo zotavením po havárii, přečtěte si níže uvedené 
    >
    > Chcete-li povolit šifrování v klidovém stavu s klíčem spravovaným zákazníkem, je [nastavena zásada přístupu](../key-vault/key-vault-secure-your-key-vault.md) pro spravovanou identitu service bus u zadaného Azure KeyVault. Tím je zajištěn řízený přístup k azure keyvault z oboru názvů Azure Service Bus.
    >
    > Z tohoto důvodu:
    > 
    >   * Pokud je pro obor názvů Service Bus již povoleno [geografické zotavení po havárii](service-bus-geo-dr.md) a chcete povolit klíč spravovaný zákazníkem, pak 
    >     * Přerušení párování
    >     * [Nastavte zásady přístupu](../key-vault/managed-identity.md) pro spravovanou identitu pro primární i sekundární obory názvů do trezoru klíčů.
    >     * Nastavte šifrování v primárním oboru názvů.
    >     * Znovu spárujte primární a sekundární obory názvů.
    > 
    >   * Pokud chcete povolit geodr v oboru názvů Service Bus, kde je již nastaven klíč spravovaný zákazníkem, pak -
    >     * [Nastavte zásady přístupu](../key-vault/managed-identity.md) pro spravovanou identitu pro sekundární obor názvů do trezoru klíčů.
    >     * Spárujte primární a sekundární obory názvů.


## <a name="rotate-your-encryption-keys"></a>Otočení šifrovacích klíčů

Klíč v trezoru klíčů můžete otočit pomocí mechanismu střídání trezorů klíčů Azure. Další informace naleznete v [tématu Nastavení střídání klíčů a auditování](../key-vault/key-vault-key-rotation-log-monitoring.md). Data aktivace a vypršení platnosti lze také nastavit tak, aby automatizovala střídání klíčů. Služba Service Bus detekuje nové verze klíčů a začne je automaticky používat.

## <a name="revoke-access-to-keys"></a>Odvolání přístupu ke klíčům

Zrušením přístupu k šifrovacím klíčům se data ze služby Service Bus nevymaže. Data však nelze získat přístup z oboru názvů Service Bus. Šifrovací klíč můžete odvolat prostřednictvím zásad přístupu nebo odstraněním klíče. Přečtěte si další informace o zásadách přístupu a zabezpečení trezoru klíčů z [zabezpečeného přístupu k trezoru klíčů](../key-vault/key-vault-secure-your-key-vault.md).

Po odvolání šifrovacího klíče se služba Service Bus v šifrovaném oboru názvů stane nefunkční. Pokud je povolen přístup ke klíči nebo je obnoven odstraněný klíč, služba Service Bus vybere klíč, abyste měli přístup k datům z oboru názvů šifrované služby Service Bus.

## <a name="use-resource-manager-template-to-enable-encryption"></a>Povolení šifrování pomocí šablony Správce prostředků
Tato část ukazuje, jak provést následující úkoly pomocí **šablon Azure Resource Manager**. 

1. Vytvořte obor názvů **premium** Service Bus s **identitou spravované služby**.
2. Vytvořte **trezor klíčů** a udělte identitě služby přístup k trezoru klíčů. 
3. Aktualizujte obor názvů Service Bus informacemi o trezoru klíčů (klíč/hodnota). 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>Vytvoření prémiového oboru názvů service bus s identitou spravované služby
Tato část ukazuje, jak vytvořit obor názvů Azure Service Bus s identitou spravované služby pomocí šablony Azure Resource Manager a PowerShellu. 

1. Vytvořte šablonu Azure Resource Manager k vytvoření oboru názvů úrovně Service Bus premium s identitou spravované služby. Název souboru: **CreateServiceBusPremiumNamespace.json**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
    
             }
          }
       ],
       "outputs":{
          "ServiceBusNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.ServiceBus/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Vytvořte soubor parametrů šablony s **názvem: CreateServiceBusPremiumNamespaceParams.json**. 

    > [!NOTE]
    > Nahraďte následující hodnoty: 
    > - `<ServiceBusNamespaceName>`- Název oboru názvů Service Bus
    > - `<Location>`- Umístění vašeho oboru názvů Service Bus

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    ```
3. Spusťte následující příkaz Prostředí PowerShell a nasadíte šablonu a vytvořte tak prémiový obor názvů Service Bus. Potom načtěte ID oboru názvů Service Bus a použijte jej později. Před `{MyRG}` spuštěním příkazu nahraďte názvem skupiny prostředků.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Udělit přístup k identitě oboru názvů služby Service Bus do trezoru klíčů

1. Spuštěním následujícího příkazu vytvořte trezor klíčů s **povolenou ochranou proti vymazání** a **měkkým odstraněním.** 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    (NEBO)
    
    Chcete-li aktualizovat **existující trezor klíčů**, spusťte následující příkaz . Před spuštěním příkazu zadejte hodnoty pro názvy skupin prostředků a trezoru klíčů. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Nastavte zásady přístupu trezoru klíčů tak, aby spravovaná identita oboru názvů Service Bus mohla přistupovat k hodnotě klíče v trezoru klíčů. Použijte ID oboru názvů Service Bus z předchozí části. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Šifrování dat v oboru názvů Service Bus pomocí klíče spravovaného zákazníkem z trezoru klíčů
Zatím jste provedli následující kroky: 

1. Vytvořil i prémiový obor názvů se spravovanou identitou.
2. Vytvořte trezor klíčů a udělte spravované identitě přístup k trezoru klíčů. 

V tomto kroku aktualizujete obor názvů Service Bus informacemi o trezoru klíčů. 

1. Vytvořte soubor JSON s názvem **UpdateServiceBusNamespaceWithEncryption.json** s následujícím obsahem: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. Vytvoření souboru parametru šablony: **UpdateServiceBusNamespaceWithEncryptionParams.json**.

    > [!NOTE]
    > Nahraďte následující hodnoty: 
    > - `<ServiceBusNamespaceName>`- Název oboru názvů Service Bus
    > - `<Location>`- Umístění vašeho oboru názvů Service Bus
    > - `<KeyVaultName>`- Název trezoru klíčů
    > - `<KeyName>`- Název klíče v trezoru klíčů  

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. Spusťte následující příkaz Prostředí PowerShell a nasaďte šablonu Správce prostředků. Před `{MyRG}` spuštěním příkazu nahraďte názvem skupiny prostředků. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>Další kroky
Viz následující články:
- [Přehled sběrnice](service-bus-messaging-overview.md)
- [Trezor klíčů – přehled](../key-vault/key-vault-overview.md)


