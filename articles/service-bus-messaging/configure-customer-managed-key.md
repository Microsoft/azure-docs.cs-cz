---
title: Konfigurace vlastního klíče pro šifrování Azure Service Busch dat v klidovém prostředí
description: Tento článek poskytuje informace o tom, jak nakonfigurovat vlastní klíč pro šifrování Azure Service Bus data REST.
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: 5d14c8953819575d1c2688520838135efc7121e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100378311"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Konfigurace klíčů spravovaných zákazníkem pro šifrování Azure Service Bus dat v klidovém formátu pomocí Azure Portal
Azure Service Bus Premium poskytuje šifrování neaktivních dat pomocí šifrování služby Azure Storage (Azure SSE). Service Bus Premium používá k ukládání dat Azure Storage. Všechna data uložená pomocí Azure Storage se šifrují pomocí klíčů spravovaných Microsoftem. Pokud používáte vlastní klíč (také označovaný jako Bring Your Own Key (BYOK) nebo klíč spravovaný zákazníkem), data se pořád šifrují pomocí klíče spravovaného společností Microsoft, ale navíc klíč spravovaný společností Microsoft bude zašifrovaný pomocí klíče spravovaného zákazníkem. Tato funkce umožňuje vytvořit, otočit, zakázat a odvolat přístup k klíčům spravovaným zákazníkem, které se používají k šifrování klíčů spravovaných společností Microsoft. Povolení funkce BYOK je jednorázovým procesem nastavení v oboru názvů.

Pro šifrování na straně služby jsou k dispozici určitá upozornění ke spravovanému klíči zákazníka. 
- Tuto funkci podporuje Azure Service Bus úrovně [Premium](service-bus-premium-messaging.md) . Nedá se povolit pro obory názvů Service Bus úrovně Standard.
- Šifrování lze povolit pouze pro nové nebo prázdné obory názvů. Pokud obor názvů obsahuje nějaké fronty nebo témata, operace šifrování se nezdaří.

Pomocí Azure Key Vault můžete spravovat klíče a auditovat používání klíčů. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů. Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../key-vault/general/overview.md)

V tomto článku se dozvíte, jak nakonfigurovat Trezor klíčů pomocí klíčů spravovaných zákazníkem pomocí Azure Portal. Informace o tom, jak vytvořit Trezor klíčů pomocí Azure Portal, najdete v tématu [rychlý Start: vytvoření Azure Key Vault pomocí Azure Portal](../key-vault/general/quick-create-portal.md).

> [!IMPORTANT]
> Použití klíčů spravovaných zákazníkem s Azure Service Bus vyžaduje, aby měl Trezor klíčů nakonfigurované dvě požadované vlastnosti. Jsou to:  **obnovitelné odstranění** a **Nemazat**. Tyto vlastnosti jsou ve výchozím nastavení povolené, když v Azure Portal vytvoříte nový trezor klíčů. Pokud ale potřebujete tyto vlastnosti v existujícím trezoru klíčů povolit, musíte použít buď PowerShell, nebo rozhraní příkazového řádku Azure CLI.

## <a name="enable-customer-managed-keys"></a>Povolit klíče spravované zákazníkem
Pokud chcete povolit klíčům spravovaným zákazníkem v Azure Portal, postupujte následovně:

1. Přejděte do oboru názvů Service Bus Premium.
2. Na stránce **Nastavení** v oboru názvů Service Bus vyberte **šifrování**.
3. Vyberte **šifrování klíče spravovaného zákazníkem v klidovém** formátu, jak je znázorněno na následujícím obrázku.

    ![Povolení klíče spravovaného zákazníkem](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Nastavení trezoru klíčů s klíči

Po povolení klíčů spravovaných zákazníkem je potřeba přidružit spravovaný klíč zákazníka k vašemu oboru názvů Azure Service Bus. Service Bus podporuje pouze Azure Key Vault. Pokud zapnete možnost **šifrování pomocí klíče spravovaného zákazníkem** v předchozí části, je potřeba, abyste klíč importovali do Azure Key Vault. Klíče musí také obsahovat **obnovitelné odstranění** a pro tento klíč **se** neodstraňují konfigurace. Tato nastavení se dají nakonfigurovat pomocí [PowerShellu](../key-vault/general/key-vault-recovery.md) nebo rozhraní příkazového [řádku](../key-vault/general/key-vault-recovery.md).

1. Pokud chcete vytvořit nový trezor klíčů, postupujte podle pokynů pro [rychlý start](../key-vault/general/overview.md)Azure Key Vault. Další informace o importu existujících klíčů najdete v tématu [informace o klíčích, tajných klíčích a certifikátech](../key-vault/general/about-keys-secrets-certificates.md).
1. Pokud chcete při vytváření trezoru zapnout ochranu pomocí obnovitelného odstranění i vyprázdnění, použijte příkaz [AZ datatrezor Create](/cli/azure/keyvault#az-keyvault-create) .

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Pokud chcete přidat ochranu vyprázdnit do existujícího trezoru (který už má povolené obnovitelné odstranění), použijte příkaz [AZ klíčů trezor Update](/cli/azure/keyvault#az-keyvault-update) .

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Pomocí následujících kroků vytvořte klíče:
    1. Pokud chcete vytvořit nový klíč, vyberte v nabídce **klíče** v části **Nastavení** možnost **Generovat/importovat** .
        
        ![Vybrat tlačítko pro vygenerování/import](./media/configure-customer-managed-key/select-generate-import.png)

    1. Nastavte **Možnosti** , které se mají **vygenerovat** , a zadejte název klíče.

        ![Vytvoření klíče](./media/configure-customer-managed-key/create-key.png) 

    1. Nyní můžete vybrat tento klíč, který chcete přidružit k oboru názvů Service Bus pro šifrování z rozevíracího seznamu. 

        ![Vyberte klíč z trezoru klíčů.](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > Pro zajištění redundance můžete přidat až 3 klíče. V případě, že jedna z klíčů vypršela, nebo k ní nelze získat přístup, budou použity jiné klíče pro šifrování.
        
    1. Zadejte podrobnosti pro klíč a klikněte na **Vybrat**. Tím se povolí šifrování klíče spravovaného společností Microsoft s klíčem (klíč spravovaný zákazníkem). 


    > [!IMPORTANT]
    > Pokud chcete použít spravovaný klíč zákazníka spolu s geografickým zotavením po havárii, přečtěte si prosím tuto část. 
    >
    > Pokud chcete povolit šifrování klíče spravovaného Microsoftem pomocí spravovaného klíče zákazníka, nastaví se [zásada přístupu](../key-vault/general/secure-your-key-vault.md) pro spravovanou identitu Service Bus v zadaném úložišti klíčů Azure. Tím se zajistí řízený přístup k trezoru klíčů Azure z oboru názvů Azure Service Bus.
    >
    > Z tohoto důvodu:
    > 
    >   * Pokud je pro obor názvů Service Bus povolené [geografické zotavení po havárii](service-bus-geo-dr.md) a chcete povolit spravovaný klíč zákazníka, 
    >     * Přerušení párování
    >     * [Nastavte zásady přístupu](../key-vault/general/assign-access-policy-portal.md) pro spravovanou identitu pro primární i sekundární obory názvů do trezoru klíčů.
    >     * Nastavte šifrování v primárním oboru názvů.
    >     * Přespárujte primární a sekundární obory názvů.
    > 
    >   * Pokud chcete povolit geografickou možnost DR pro obor názvů Service Bus, kde je už nastavený zákazníkem spravovaný klíč, pak –
    >     * [Nastavte zásady přístupu](../key-vault/general/assign-access-policy-portal.md) pro spravovanou identitu pro sekundární obor názvů do trezoru klíčů.
    >     * Spáruje primární a sekundární obory názvů.


## <a name="rotate-your-encryption-keys"></a>Otočení šifrovacích klíčů

Svůj klíč můžete v trezoru klíčů otočit pomocí mechanismu rotace trezorů klíčů Azure. Data o aktivaci a vypršení platnosti je také možné nastavit na automatizaci střídání klíčů. Služba Service Bus detekuje nové verze klíčů a automaticky je začne používat.

## <a name="revoke-access-to-keys"></a>Odvolat přístup k klíčům

Odvolání přístupu k šifrovacím klíčům neodstraní data z Service Bus. K datům ale nelze přicházet z oboru názvů Service Bus. Šifrovací klíč můžete odvolat pomocí zásad přístupu nebo odstraněním klíče. Přečtěte si další informace o zásadách přístupu a zabezpečení trezoru klíčů před [zabezpečeným přístupem k trezoru klíčů](../key-vault/general/secure-your-key-vault.md).

Po odvolání šifrovacího klíče se služba Service Bus v zašifrovaném oboru názvů stane nefunkčním. Pokud je povolený přístup k klíči nebo dojde k obnovení odstraněného klíče, Service Bus služba vybere klíč, abyste mohli přistupovat k datům z šifrovaného názvového prostoru Service Bus.

## <a name="caching-of-keys"></a>Ukládání klíčů do mezipaměti
Instance Service Bus se dotazuje na uvedené šifrovací klíče každých 5 minut. Ukládá je do mezipaměti a používá je až do dalšího cyklického dotazování, což je po 5 minutách. Pokud je k dispozici alespoň jeden klíč, jsou fronty a témata přístupné. Pokud jsou všechny uvedené klíče nedostupné při cyklickém dotazování, všechny fronty a témata se stanou nedostupnými. 

Tady jsou další podrobnosti: 

- Každých 5 minut služba Service Bus dotazuje všechny klíče spravované zákazníkem uvedené v záznamu oboru názvů:
    - Pokud byl klíč otočen, záznam se aktualizuje pomocí nového klíče.
    - Pokud byl klíč odvolán, klíč se odebere ze záznamu.
    - Pokud jsou všechny klíče odvolány, stav šifrování oboru názvů je nastaven na **odvolaný**. K datům nelze přistup z oboru názvů Service Bus.. 
    

## <a name="use-resource-manager-template-to-enable-encryption"></a>Použití šablony Správce prostředků k povolení šifrování
V této části se dozvíte, jak provádět následující úlohy pomocí **Azure Resource Manager šablon**. 

1. Vytvořte obor názvů **premium** Service Bus s **identitou spravované služby**.
2. Vytvořte **Trezor klíčů** a Udělte identitě služby přístup k trezoru klíčů. 
3. Aktualizujte obor názvů Service Bus s informacemi o trezoru klíčů (klíč/hodnota). 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>Vytvoření oboru názvů Premium Service Bus s identitou spravované služby
V této části se dozvíte, jak vytvořit obor názvů Azure Service Bus s identitou spravované služby pomocí šablony Azure Resource Manager a PowerShellu. 

1. Vytvořte šablonu Azure Resource Manager pro vytvoření oboru názvů Service Bus úrovně Premium s identitou spravované služby. Název souboru: **CreateServiceBusPremiumNamespace.js**: 

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
2. Vytvořte soubor parametrů šablony s názvem: **CreateServiceBusPremiumNamespaceParams.jsv**. 

    > [!NOTE]
    > Nahraďte následující hodnoty: 
    > - `<ServiceBusNamespaceName>` – Název oboru názvů Service Bus
    > - `<Location>` – Umístění vašeho oboru názvů Service Bus

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
3. Spuštěním následujícího příkazu PowerShellu nasaďte šablonu a vytvořte obor názvů Premium Service Bus. Pak načtěte ID Service Bus oboru názvů pro pozdější použití. `{MyRG}`Před spuštěním příkazu nahraďte názvem skupiny prostředků.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Udělení přístupu k identitě oboru názvů Service Bus k trezoru klíčů

1. Spusťte následující příkaz, který vytvoří Trezor klíčů s povoleným **ochranou vyprázdnění** a **obnovitelného odstranění** . 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    ANI
    
    Spuštěním následujícího příkazu aktualizujte **existující Trezor klíčů**. Před spuštěním příkazu zadejte hodnoty pro názvy skupin prostředků a trezorů klíčů. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Nastavte zásady přístupu trezoru klíčů tak, aby spravovaná identita oboru názvů Service Bus mohla přistupovat k hodnotě klíče v trezoru klíčů. Použijte ID Service Bus oboru názvů z předchozí části. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Šifrování dat v Service Bus obor názvů s klíčem spravovaným zákazníkem z trezoru klíčů
V tomto případě jste provedli následující kroky: 

1. Vytvořili jste obor názvů Premium se spravovanou identitou.
2. Vytvořte Trezor klíčů a udělený přístup ke spravovaným identitám do trezoru klíčů. 

V tomto kroku aktualizujete obor názvů Service Bus s použitím informací o trezoru klíčů. 

1. Vytvořte soubor JSON s názvem **UpdateServiceBusNamespaceWithEncryption.js** s následujícím obsahem: 

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

2. Vytvořte soubor parametrů šablony: **UpdateServiceBusNamespaceWithEncryptionParams.jsna**.

    > [!NOTE]
    > Nahraďte následující hodnoty: 
    > - `<ServiceBusNamespaceName>` – Název oboru názvů Service Bus
    > - `<Location>` – Umístění vašeho oboru názvů Service Bus
    > - `<KeyVaultName>` – Název vašeho trezoru klíčů
    > - `<KeyName>` – Název klíče v trezoru klíčů  

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
3. Spusťte následující příkaz prostředí PowerShell, který nasadí šablonu Správce prostředků. `{MyRG}`Před spuštěním příkazu nahraďte názvem vaší skupiny prostředků. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>Další kroky
Viz následující články:
- [Přehled Service Bus](service-bus-messaging-overview.md)
- [Přehled Key Vault](../key-vault/general/overview.md)