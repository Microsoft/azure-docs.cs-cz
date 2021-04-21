---
title: Konfigurace vlastního klíče pro šifrování dat služby Azure Event Hubs v klidovém prostředí
description: Tento článek poskytuje informace o tom, jak nakonfigurovat vlastní klíč pro šifrování služby Azure Event Hubs data REST.
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 33587812121051d93aa8b939c3df70530ba65c5e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812439"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Konfigurace klíčů spravovaných zákazníkem pro šifrování dat Azure Event Hubs v klidovém formátu pomocí Azure Portal
Azure Event Hubs poskytuje šifrování neaktivních dat pomocí šifrování služby Azure Storage (Azure SSE). Služba Event Hubs používá Azure Storage k ukládání dat. Všechna data uložená pomocí Azure Storage se šifrují pomocí klíčů spravovaných Microsoftem. Pokud používáte vlastní klíč (také označovaný jako Bring Your Own Key (BYOK) nebo klíč spravovaný zákazníkem), data se pořád šifrují pomocí klíče spravovaného společností Microsoft, ale navíc klíč spravovaný společností Microsoft bude zašifrovaný pomocí klíče spravovaného zákazníkem. Tato funkce umožňuje vytvořit, otočit, zakázat a odvolat přístup k klíčům spravovaným zákazníkem, které se používají k šifrování klíčů spravovaných společností Microsoft. Povolení funkce BYOK je jednorázovým procesem nastavení v oboru názvů.

> [!NOTE]
> - Funkce BYOK je podporována [Event Hubs vyhrazenými clustery s jedním tenantů](event-hubs-dedicated-overview.md) . Nedá se povolit pro standardní Event Hubs obory názvů.
> - Šifrování lze povolit pouze pro nové nebo prázdné obory názvů. Pokud obor názvů obsahuje centra událostí, operace šifrování selže.

Pomocí Azure Key Vault můžete spravovat klíče a auditovat používání klíčů. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů. Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../key-vault/general/overview.md)

V tomto článku se dozvíte, jak nakonfigurovat Trezor klíčů pomocí klíčů spravovaných zákazníkem pomocí Azure Portal. Informace o tom, jak vytvořit Trezor klíčů pomocí Azure Portal, najdete v tématu [rychlý Start: vytvoření Azure Key Vault pomocí Azure Portal](../key-vault/general/quick-create-portal.md).

> [!IMPORTANT]
> Použití klíčů spravovaných zákazníkem v Azure Event Hubs vyžaduje, aby měl Trezor klíčů nakonfigurované dvě požadované vlastnosti. Jsou to:  **obnovitelné odstranění** a **Nemazat**. Tyto vlastnosti jsou ve výchozím nastavení povolené, když v Azure Portal vytvoříte nový trezor klíčů. Pokud ale potřebujete tyto vlastnosti v existujícím trezoru klíčů povolit, musíte použít buď PowerShell, nebo rozhraní příkazového řádku Azure CLI.

## <a name="enable-customer-managed-keys"></a>Povolit klíče spravované zákazníkem
Pokud chcete povolit klíčům spravovaným zákazníkem v Azure Portal, postupujte následovně:

1. Přejděte do clusteru Event Hubs úrovně Dedicated.
1. Vyberte obor názvů, na kterém chcete povolit BYOK.
1. Na stránce **Nastavení** v oboru názvů Event Hubs vyberte **šifrování**. 
1. Vyberte **šifrování klíče spravovaného zákazníkem v klidovém** formátu, jak je znázorněno na následujícím obrázku. 

    ![Povolení klíče spravovaného zákazníkem](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Nastavení trezoru klíčů s klíči
Po povolení klíčů spravovaných zákazníkem je potřeba přidružit spravovaný klíč zákazníka k vašemu oboru názvů Azure Event Hubs. Event Hubs podporuje pouze Azure Key Vault. Pokud zapnete možnost **šifrování pomocí klíče spravovaného zákazníkem** v předchozí části, je potřeba, abyste klíč importovali do Azure Key Vault. Klíče musí také obsahovat **obnovitelné odstranění** a pro tento klíč **se** neodstraňují konfigurace. Tato nastavení se dají nakonfigurovat pomocí [PowerShellu](../key-vault/general/key-vault-recovery.md) nebo rozhraní příkazového [řádku](../key-vault/general/key-vault-recovery.md).

1. Pokud chcete vytvořit nový trezor klíčů, postupujte podle pokynů pro [rychlý start](../key-vault/general/overview.md)Azure Key Vault. Další informace o importu existujících klíčů najdete v tématu [informace o klíčích, tajných klíčích a certifikátech](../key-vault/general/about-keys-secrets-certificates.md).
1. Pokud chcete při vytváření trezoru zapnout ochranu pomocí obnovitelného odstranění i vyprázdnění, použijte příkaz [AZ datatrezor Create](/cli/azure/keyvault#az_keyvault_create) .

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Pokud chcete přidat ochranu vyprázdnit do existujícího trezoru (který už má povolené obnovitelné odstranění), použijte příkaz [AZ klíčů trezor Update](/cli/azure/keyvault#az_keyvault_update) .

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Pomocí následujících kroků vytvořte klíče:
    1. Pokud chcete vytvořit nový klíč, vyberte v nabídce **klíče** v části **Nastavení** možnost **Generovat/importovat** .
        
        ![Vybrat tlačítko pro vygenerování/import](./media/configure-customer-managed-key/select-generate-import.png)
    1. Nastavte **Možnosti** , které se mají **vygenerovat** , a zadejte název klíče.

        ![Vytvoření klíče](./media/configure-customer-managed-key/create-key.png) 
    1. Nyní můžete vybrat tento klíč, který chcete přidružit k oboru názvů Event Hubs pro šifrování z rozevíracího seznamu. 

        ![Vyberte klíč z trezoru klíčů.](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Zadejte podrobnosti pro klíč a klikněte na **Vybrat**. Tím se povolí šifrování klíče spravovaného společností Microsoft s klíčem (klíč spravovaný zákazníkem). 


## <a name="rotate-your-encryption-keys"></a>Otočení šifrovacích klíčů
Svůj klíč můžete v trezoru klíčů otočit pomocí mechanismu rotace trezorů klíčů Azure. Data o aktivaci a vypršení platnosti je také možné nastavit na automatizaci střídání klíčů. Služba Event Hubs detekuje nové verze klíčů a automaticky je začne používat.

## <a name="revoke-access-to-keys"></a>Odvolat přístup k klíčům
Odvolání přístupu k šifrovacím klíčům neodstraní data z Event Hubs. K datům ale nelze přicházet z oboru názvů Event Hubs. Šifrovací klíč můžete odvolat pomocí zásad přístupu nebo odstraněním klíče. Přečtěte si další informace o zásadách přístupu a zabezpečení trezoru klíčů před [zabezpečeným přístupem k trezoru klíčů](../key-vault/general/security-features.md).

Po odvolání šifrovacího klíče se služba Event Hubs v zašifrovaném oboru názvů stane nefunkčním. Pokud je povolený přístup ke klíči nebo je obnovený klíč, Event Hubs služba vybere klíč, abyste měli přístup k datům z šifrovaného názvového prostoru Event Hubs.

## <a name="set-up-diagnostic-logs"></a>Nastavení diagnostických protokolů 
Nastavení protokolů diagnostiky pro obory názvů s povoleným BYOK poskytuje požadované informace o těchto operacích. Tyto protokoly je možné povolit a později streamovat do centra událostí nebo analyzovat prostřednictvím Log Analytics nebo streamovat do úložiště a provádět přizpůsobené analýzy. Další informace o diagnostických protokolech najdete v tématu [Přehled diagnostických protokolů Azure](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="enable-user-logs"></a>Povolit protokoly uživatelů
Pomocí těchto kroků povolte protokoly pro klíče spravované zákazníkem.

1. V Azure Portal přejděte na obor názvů, který má povolenou možnost BYOK.
1. V části **monitorování** vyberte **nastavení diagnostiky** .

    ![Vybrat nastavení diagnostiky](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Vyberte **+ Přidat nastavení diagnostiky**. 

    ![Vyberte Přidat nastavení diagnostiky.](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Zadejte **název** a vyberte, kam chcete zasílat streamování protokolů.
1. Vyberte **CustomerManagedKeyUserLogs** a **uložte**. Tato akce povolí protokoly pro BYOK v oboru názvů.

    ![Vybrat možnost uživatelských protokolů pro klíč uživatele spravovaný zákazníkem](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Schéma protokolu 
Všechny protokoly jsou uložené ve formátu JavaScript Object Notation (JSON). Každá položka má pole řetězce, která používají formát popsaný v následující tabulce. 

| Název | Description |
| ---- | ----------- | 
| /TN | Popis úlohy, která selhala. |
| ID aktivity | Interní ID, které se používá ke sledování. |
| category | Definuje klasifikaci úkolu. Například pokud je klíč z vašeho trezoru klíčů zakázaný, pak se jedná o kategorii informací, nebo pokud se klíč nedá rozdělit, může dojít k chybě. |
| resourceId | ID prostředku Azure Resource Manager |
| keyVault | Úplný název trezoru klíčů |
| key | Název klíče, který slouží k šifrování oboru názvů Event Hubs. |
| verze | Verze používaného klíče |
| operation | Operace, která se provádí na klíči v trezoru klíčů. Můžete například zakázat/povolit klíč, zalamovat nebo rozbalení. |
| kód | Kód, který je přidružen k operaci. Příklad: kód chyby 404 znamená, že klíč nebyl nalezen. |
| zpráva | Jakákoli chybová zpráva přidružená k operaci |

Tady je příklad protokolu pro klíč spravovaný zákazníkem:

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="use-resource-manager-template-to-enable-encryption"></a>Použití šablony Správce prostředků k povolení šifrování
V této části se dozvíte, jak provádět následující úlohy pomocí **Azure Resource Manager šablon**. 

1. Vytvořte **obor názvů Event Hubs** s identitou spravované služby.
2. Vytvořte **Trezor klíčů** a Udělte identitě služby přístup k trezoru klíčů. 
3. Aktualizujte obor názvů Event Hubs s informacemi o trezoru klíčů (klíč/hodnota). 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Vytvoření clusteru Event Hubs a oboru názvů s identitou spravované služby
V této části se dozvíte, jak vytvořit obor názvů Azure Event Hubs s identitou spravované služby pomocí šablony Azure Resource Manager a PowerShellu. 

1. Vytvořte šablonu Azure Resource Manager pro vytvoření oboru názvů Event Hubs s identitou spravované služby. Název souboru: **CreateEventHubClusterAndNamespace.js**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Vytvořte soubor parametrů šablony s názvem: **CreateEventHubClusterAndNamespaceParams.jsv**. 

    > [!NOTE]
    > Nahraďte následující hodnoty: 
    > - `<EventHubsClusterName>` – Název Event Hubsho clusteru    
    > - `<EventHubsNamespaceName>` – Název oboru názvů Event Hubs
    > - `<Location>` – Umístění vašeho oboru názvů Event Hubs

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. Spuštěním následujícího příkazu PowerShellu nasaďte šablonu a vytvořte obor názvů Event Hubs. Pak načtěte ID Event Hubs oboru názvů pro pozdější použití. `{MyRG}`Před spuštěním příkazu nahraďte názvem skupiny prostředků.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Udělení přístupu k identitě oboru názvů Event Hubs k trezoru klíčů

1. Spusťte následující příkaz, který vytvoří Trezor klíčů s povoleným **ochranou vyprázdnění** a **obnovitelného odstranění** . 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    ANI    
    
    Spuštěním následujícího příkazu aktualizujte **existující Trezor klíčů**. Před spuštěním příkazu zadejte hodnoty pro názvy skupin prostředků a trezorů klíčů. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Nastavte zásady přístupu trezoru klíčů tak, aby spravovaná identita oboru názvů Event Hubs mohla přistupovat k hodnotě klíče v trezoru klíčů. Použijte ID Event Hubs oboru názvů z předchozí části. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Šifrování dat v Event Hubs obor názvů s klíčem spravovaným zákazníkem z trezoru klíčů
V tomto případě jste provedli následující kroky: 

1. Vytvořili jste obor názvů Premium se spravovanou identitou.
2. Vytvořte Trezor klíčů a udělený přístup ke spravovaným identitám do trezoru klíčů. 

V tomto kroku aktualizujete obor názvů Event Hubs s použitím informací o trezoru klíčů. 

1. Vytvořte soubor JSON s názvem **CreateEventHubClusterAndNamespace.js** s následujícím obsahem: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
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

2. Vytvořte soubor parametrů šablony: **UpdateEventHubClusterAndNamespaceParams.jsna**. 

    > [!NOTE]
    > Nahraďte následující hodnoty: 
    > - `<EventHubsClusterName>` – Název Event Hubsho clusteru.        
    > - `<EventHubsNamespaceName>` – Název oboru názvů Event Hubs
    > - `<Location>` – Umístění vašeho oboru názvů Event Hubs
    > - `<KeyVaultName>` – Název vašeho trezoru klíčů
    > - `<KeyName>` – Název klíče v trezoru klíčů

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
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
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>Řešení potíží
V rámci osvědčeného postupu doporučujeme vždy povolit protokoly, jako jsou uvedené v předchozí části. Pomáhá sledovat aktivity, když je povolené šifrování BYOK. Pomáhá také při určování rozsahu problémů.

Níže jsou uvedené běžné kódy chyb, které se hledají, když je povolené šifrování BYOK.

| Akce | Kód chyby | Výsledný stav dat |
| ------ | ---------- | ----------------------- | 
| Odebrat oprávnění k zabalení a rozbalení z trezoru klíčů | 403 |    Nepřístupný |
| Odebere členství role AAD z objektu zabezpečení AAD, který udělil oprávnění k zabalení nebo rozbalení. | 403 |  Nepřístupný |
| Odstranění šifrovacího klíče z trezoru klíčů | 404 | Nepřístupný |
| Odstranění trezoru klíčů | 404 | Nedostupné (předpokládá, že je povolené obnovitelné odstranění, což je povinné nastavení.) |
| Doba vypršení platnosti šifrovacího klíče se mění tak, že už vypršela její platnost. | 403 |   Nepřístupný  |
| Mění se NBF (ne dřív), takže šifrovací klíč klíče není aktivní. | 403 | Nepřístupný  |
| Výběr možnosti **Povolení protokolu MSFT** pro bránu firewall trezoru klíčů nebo jiným blokování síťového přístupu k trezoru klíčů, který má šifrovací klíč | 403 | Nepřístupný |
| Přesun trezoru klíčů do jiného tenanta | 404 | Nepřístupný |  
| Přerušované síťové potíže nebo výpadky DNS/AAD/MSI |  | K dispozici pomocí šifrovacího klíče dat uložených v mezipaměti |

> [!IMPORTANT]
> Pokud chcete povolit geografickou možnost DR na oboru názvů, který používá šifrování BYOK, sekundární obor názvů pro párování musí být v vyhrazeném clusteru a musí mít povolenou spravovanou identitu přiřazenou systémem. Další informace najdete v tématu [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="next-steps"></a>Další kroky
Viz následující články:
- [Přehled Event Hubs](event-hubs-about.md)
- [Přehled Key Vault](../key-vault/general/overview.md)