---
title: Konfigurace vlastního klíče pro šifrování dat centra událostí Azure v klidovém stavu
description: Tento článek obsahuje informace o tom, jak nakonfigurovat vlastní klíč pro šifrování úložiště dat centra událostí Azure.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: spelluru
ms.openlocfilehash: f515d3ad832db7f78f98111ab67628a2874033ff
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459130"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Konfigurace klíčů spravovaných zákazníkem pro šifrování dat služby Azure Event Hubs v klidovém stavu pomocí portálu Azure
Azure Event Hubs poskytuje šifrování dat v klidovém stavu pomocí šifrování služby Azure Storage Service (Azure SSE). Event Hubs spoléhá na Azure Storage pro ukládání dat a ve výchozím nastavení, všechna data, která jsou uložená s Azure Storage je šifrována pomocí klíčů spravovaných Microsoftem. 

## <a name="overview"></a>Přehled
Azure Event Hubs teď podporuje možnost šifrování dat v klidovém stavu pomocí klíčů spravovaných microsoftem nebo klíčů spravovaných zákazníky (Bring Your Own Key – BYOK). Tato funkce umožňuje vytvářet, otáčet, zakázat a odvolat přístup ke klíčům spravovaným zákazníkem, které se používají k šifrování dat centra událostí Azure v klidovém stavu.

Povolení funkce BYOK je proces jednorázového nastavení v oboru názvů.

> [!NOTE]
> Funkce BYOK je podporována [clustery s vyhrazenými clustery s jedním tenantem.](event-hubs-dedicated-overview.md) Nelze povolit pro standardní obory názvů Event Hubs.

Azure Key Vault můžete použít ke správě klíčů a auditování využití klíčů. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít Azure Key Vault API ke generování klíčů. Další informace o Azure Key Vault najdete v tématu [Co je Azure Key Vault?](../key-vault/general/overview.md)

Tento článek ukazuje, jak nakonfigurovat trezor klíčů pomocí klíčů spravovaných zákazníkem pomocí portálu Azure. Informace o tom, jak vytvořit trezor klíčů pomocí webu Azure Portal, najdete v [tématu Úvodní příručka: Nastavení a načtení tajného klíče z Azure Key Vault pomocí portálu Azure](../key-vault/secrets/quick-create-portal.md).

> [!IMPORTANT]
> Použití klíčů spravovaných zákazníkem s Azure Event Hubs vyžaduje, aby trezor klíčů měl nakonfigurované dvě požadované vlastnosti. Jsou to: **Obnovitelné odstranění** a **Nečistit**. Tyto vlastnosti jsou ve výchozím nastavení povolené při vytváření nového trezoru klíčů na webu Azure Portal. Pokud však potřebujete povolit tyto vlastnosti v existujícím trezoru klíčů, musíte použít buď PowerShell nebo Azure CLI.

## <a name="enable-customer-managed-keys"></a>Povolení klíčů spravovaných zákazníkem
Pokud chcete povolit klíče spravované zákazníky na webu Azure Portal, postupujte takto:

1. Přejděte do vyhrazeného clusteru Centra událostí.
1. Vyberte obor názvů, ve kterém chcete povolit byok.
1. Na stránce **Nastavení** oboru názvů Event Hubs vyberte **Šifrování**. 
1. Vyberte **šifrování klíče spravované zákazníkem v klidovém stavu,** jak je znázorněno na následujícím obrázku. 

    ![Povolit klíč spravovaný zákazníkem](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Nastavení trezoru klíčů s klíči
Po povolení klíčů spravovaných zákazníkem je potřeba přidružit klíč spravovaný zákazníkem k oboru názvů Centra událostí Azure. Centra událostí podporuje jenom Azure Key Vault. Pokud povolíte **možnost Šifrování s klíčem spravovaným zákazníkem** v předchozí části, musíte mít klíč importován do úložiště klíčů Azure. Klíče musí mít také pro klíč nakonfigurovány **funkce Obnovitelné odstranění** a **Neodstraňovat.** Tato nastavení lze nakonfigurovat pomocí [prostředí PowerShell](../key-vault/general/soft-delete-powershell.md) nebo [CLI](../key-vault/general/soft-delete-cli.md#enabling-purge-protection).

1. Chcete-li vytvořit nový trezor klíčů, postupujte podle [úvodního panelu Azure](../key-vault/general/overview.md)Key Vault . Další informace o importu existujících klíčů naleznete [v tématu O klíčích, tajných klíčích a certifikátech](../key-vault/about-keys-secrets-and-certificates.md).
1. Chcete-li při vytváření trezoru zapnout ochranu proti měkkému odstranění i vymazání, použijte příkaz [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) .

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Chcete-li přidat ochranu proti vymazání do existujícího trezoru (který již má povoleno obnovitelné odstranění), použijte příkaz [az keyvault update.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Vytvořte klíče následujícím postupem:
    1. Chcete-li vytvořit novou klávesu, vyberte **Generovat/importovat** z nabídky **Klávesy** v části **Nastavení**.
        
        ![Tlačítko Vybrat generovat/importovat](./media/configure-customer-managed-key/select-generate-import.png)
    1. Nastavte **možnosti** **pro generování** a pojmenujte klíč.

        ![Vytvoření klíče](./media/configure-customer-managed-key/create-key.png) 
    1. Nyní můžete vybrat tento klíč, který chcete přidružit k oboru názvů Centra událostí pro šifrování z rozevíracího seznamu. 

        ![Vybrat klíč z trezoru klíčů](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Vyplňte podrobnosti klíče a klepněte na **tlačítko Vybrat**. To umožní šifrování dat v klidovém stavu v oboru názvů pomocí klientského spravovaného klíče. 


## <a name="rotate-your-encryption-keys"></a>Otočení šifrovacích klíčů
Klíč v trezoru klíčů můžete otočit pomocí mechanismu střídání trezorů klíčů Azure. Další informace naleznete v [tématu Nastavení střídání klíčů a auditování](../key-vault/secrets/key-rotation-log-monitoring.md). Data aktivace a vypršení platnosti lze také nastavit tak, aby automatizovala střídání klíčů. Služba Event Hubs rozpozná nové verze klíčů a začne je automaticky používat.

## <a name="revoke-access-to-keys"></a>Odvolání přístupu ke klíčům
Zrušením přístupu k šifrovacím klíčům se data z centra událostí nevymaže. Data však nelze přistupovat z oboru názvů Centra událostí. Šifrovací klíč můžete odvolat prostřednictvím zásad přístupu nebo odstraněním klíče. Přečtěte si další informace o zásadách přístupu a zabezpečení trezoru klíčů z [zabezpečeného přístupu k trezoru klíčů](../key-vault/general/secure-your-key-vault.md).

Po odvolání šifrovacího klíče se služba Event Hubs v šifrovaném oboru názvů stane nefunkční. Pokud je přístup ke klíči povolen nebo je obnoven klíč pro odstranění, služba Event Hubs vybere klíč, abyste měli přístup k datům z oboru názvů zašifrovaných center událostí.

## <a name="set-up-diagnostic-logs"></a>Nastavení diagnostických protokolů 
Nastavení diagnostických protokolů pro obory názvů s povolenou funkcí BYOK poskytuje požadované informace o operacích, když je obor názvů šifrován pomocí klíčů spravovaných zákazníkem. Tyto protokoly lze povolit a později streamovat do centra událostí nebo analyzovat prostřednictvím analýzy protokolů nebo streamované do úložiště za účelem provádění vlastníanalýzy. Další informace o diagnostických protokolech najdete v [tématu Přehled protokolů diagnostiky Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="enable-user-logs"></a>Povolení protokolů uživatelů
Následujícím postupem povolte protokoly pro klíče spravované zákazníkem.

1. Na webu Azure Portal přejděte do oboru názvů, který má povolenou funkcí BYOK.
1. V části **Sledování**vyberte Nastavení **diagnostiky** .

    ![Výběr nastavení diagnostiky](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Vyberte **možnost +Přidat diagnostické nastavení**. 

    ![Vybrat nastavení přidat diagnostiku](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Zadejte **název** a vyberte, kam chcete datové proudy protokolů.
1. Vyberte **CustomerManagedKeyUserLogs** a **Uložit**. Tato akce umožňuje protokoly pro BYOK v oboru názvů.

    ![Vybrat možnost protokolů klíčových uživatelů spravovaných zákazníkem](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Schéma protokolu 
Všechny protokoly jsou uloženy ve formátu JavaScript Object Notation (JSON). Každá položka obsahuje řetězcová pole, která používají formát popsaný v následující tabulce. 

| Název | Popis |
| ---- | ----------- | 
| Název_úkolu | Popis úlohy, která se nezdařila. |
| ActivityId | Interní ID, které se používá pro sledování. |
| category | Definuje klasifikaci úkolu. Pokud je například klíč z trezoru klíčů zakázán, jedná se o informační kategorii nebo pokud klíč nelze rozbalit, může se dostat pod chybu. |
| resourceId | ID prostředků Správce prostředků Azure |
| keyVault | Úplný název trezoru klíčů. |
| key | Název klíče, který se používá k šifrování oboru názvů Event Hubs. |
| version | Verze klíče, který se používá. |
| Operace | Operace, která se provádí na klíč i v trezoru klíčů. Například zakázat/povolit klíč, obtékat nebo rozbalit |
| kód | Kód, který je přidružen k operaci. Příklad: Kód chyby 404 znamená, že klíč nebyl nalezen. |
| zpráva | Všechny chybové zprávy spojené s operací |

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

## <a name="use-resource-manager-template-to-enable-encryption"></a>Povolení šifrování pomocí šablony Správce prostředků
Tato část ukazuje, jak provést následující úkoly pomocí **šablon Azure Resource Manager**. 

1. Vytvořte **obor názvů Event Hubs** s identitou spravované služby.
2. Vytvořte **trezor klíčů** a udělte identitě služby přístup k trezoru klíčů. 
3. Aktualizujte obor názvů Event Hubs informacemi o trezoru klíčů (klíč/hodnota). 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Vytvoření clusteru centra událostí a oboru názvů s identitou spravované služby
Tato část ukazuje, jak vytvořit obor názvů Centra událostí Azure s identitou spravované služby pomocí šablony Azure Resource Manager a Prostředí PowerShell. 

1. Vytvořte šablonu Azure Resource Manager u vytvoření oboru názvů Event Hubs s identitou spravované služby. Pojmenujte soubor: **CreateEventHubClusterAndNamespace.json**: 

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
2. Vytvořte soubor parametrů šablony s **názvem: CreateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Nahraďte následující hodnoty: 
    > - `<EventHubsClusterName>`- Název clusteru Event Hubs    
    > - `<EventHubsNamespaceName>`- Název oboru názvů Event Hubs
    > - `<Location>`- Umístění oboru názvů Event Hubs

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
3. Spusťte následující příkaz Prostředí PowerShell a nasadíte šablonu a vytvořte obor názvů Event Hubs. Potom načtěte ID oboru názvů Event Hubs a použijte ho později. Před `{MyRG}` spuštěním příkazu nahraďte názvem skupiny prostředků.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Udělit přístup k identitě oboru názvů centra událostí do trezoru klíčů

1. Spuštěním následujícího příkazu vytvořte trezor klíčů s **povolenou ochranou proti vymazání** a **měkkým odstraněním.** 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    (NEBO)    
    
    Chcete-li aktualizovat **existující trezor klíčů**, spusťte následující příkaz . Před spuštěním příkazu zadejte hodnoty pro názvy skupin prostředků a trezoru klíčů. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Nastavte zásady přístupu trezoru klíčů tak, aby spravovaná identita oboru názvů Event Hubs mohla přistupovat k hodnotě klíče v trezoru klíčů. Použijte ID oboru názvů Event Hubs z předchozí části. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Šifrování dat v oboru názvů Event Hubs pomocí klíče spravovaného zákazníkem z trezoru klíčů
Zatím jste provedli následující kroky: 

1. Vytvořil i prémiový obor názvů se spravovanou identitou.
2. Vytvořte trezor klíčů a udělte spravované identitě přístup k trezoru klíčů. 

V tomto kroku aktualizujete obor názvů Event Hubs informacemi o trezoru klíčů. 

1. Vytvořte soubor JSON s názvem **CreateEventHubClusterAndNamespace.json** s následujícím obsahem: 

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

2. Vytvoření souboru parametru šablony: **UpdateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Nahraďte následující hodnoty: 
    > - `<EventHubsClusterName>`- Název clusteru Event Hubs.        
    > - `<EventHubsNamespaceName>`- Název oboru názvů Event Hubs
    > - `<Location>`- Umístění oboru názvů Event Hubs
    > - `<KeyVaultName>`- Název trezoru klíčů
    > - `<KeyName>`- Název klíče v trezoru klíčů

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
3. Spusťte následující příkaz Prostředí PowerShell a nasaďte šablonu Správce prostředků. Před `{MyRG}` spuštěním příkazu nahraďte názvem skupiny prostředků. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>Řešení potíží
Jako osvědčený postup vždy povolte protokoly, jako je uvedeno v předchozí části. Pomáhá při sledování aktivit, pokud je povoleno šifrování BYOK. To také pomáhá při vymezení dolů problémy.

Následují běžné chybové kódy, které je třeba vyhledat, když je povoleno šifrování BYOK.

| Akce | Kód chyby | Výsledný stav dat |
| ------ | ---------- | ----------------------- | 
| Odebrání oprávnění k obtékání nebo rozbalení z trezoru klíčů | 403 |    Nepřístupné |
| Odebrání členství v roli AAD z objektu Zabezpečení AAD, který udělil oprávnění zalamování nebo rozbalení | 403 |  Nepřístupné |
| Odstranění šifrovacího klíče z trezoru klíčů | 404 | Nepřístupné |
| Odstranění trezoru klíčů | 404 | Nepřístupné (předpokládá, že je povoleno obnovitelné odstranění, což je požadované nastavení.) |
| Změna doby vypršení platnosti šifrovacího klíče tak, že jeho platnost již vypršela | 403 |   Nepřístupné  |
| Změna NBF (ne dříve) tak, aby šifrovací klíč nebyl aktivní | 403 | Nepřístupné  |
| Výběr možnosti **Povolit službu MSFT** pro bránu firewall trezoru klíčů nebo jiné blokování přístupu k síti k trezoru klíčů, který má šifrovací klíč | 403 | Nepřístupné |
| Přesunutí trezoru klíčů do jiného klienta | 404 | Nepřístupné |  
| Občasný problém se sítí nebo výpadek DNS/AAD/MSI |  | Přístupné pomocí šifrovacího klíče dat uložených v mezipaměti |

> [!IMPORTANT]
> Chcete-li povolit geo-DR v oboru názvů, který používá šifrování BYOK, musí být sekundární obor názvů pro párování ve vyhrazeném clusteru a musí mít v něm povolenou spravovanou identitu přiřazenou systémem. Další informace najdete v tématu [Spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="next-steps"></a>Další kroky
Viz následující články:
- [Přehled centra událostí](event-hubs-about.md)
- [Trezor klíčů – přehled](../key-vault/general/overview.md)




