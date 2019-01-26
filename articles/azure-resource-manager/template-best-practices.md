---
title: Osvědčené postupy pro šablony Azure Resource Manageru
description: Popisuje doporučené postupy pro vytváření šablon Azure Resource Manageru. Nabízí návrhy, aby se zabránilo běžné problémy při použití šablony.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2018
ms.author: tomfitz
ms.openlocfilehash: 0736ed32fa6f17cc840d6b144503409365c33d84
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55077931"
---
# <a name="azure-resource-manager-template-best-practices"></a>Azure Resource Manageru šablony osvědčené postupy

Tento článek obsahuje doporučení, jak k vytvoření šablony Resource Manageru. Tato doporučení umožňuje vyhnout se běžné problémy při použití šablony k nasazení řešení.

Doporučení o tom, jak řídit vaše předplatná Azure, najdete v tématu [Základní kostra Azure enterprise: Zásady správného řízení předplatná](/azure/architecture/cloud-adoption/appendix/azure-scaffold?toc=%2Fen-us%2Fazure%2Fazure-resource-manager%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json).

Doporučení, jak vytvářet šablony, které fungují ve všech prostředích cloudu Azure, najdete v části [šablon vývoj Azure Resource Manageru pro cloud konzistence](templates-cloud-consistency.md).

## <a name="parameters"></a>Parametry
Informace v této části mohou být užitečné při práci s [parametry](resource-manager-templates-parameters.md).

### <a name="general-recommendations-for-parameters"></a>Obecná doporučení pro parametry

* Minimalizace používání parametrů. Místo toho použijte proměnné nebo literálových hodnot pro vlastnosti, které není potřeba zadat během nasazování.

* Použijte formát camelCase pro názvy parametrů.

* Použití parametrů pro nastavení, které se liší podle prostředí, jako je SKU, velikost nebo kapacity.

* Použití parametrů pro názvy prostředků, které se mají určit pro snadnější identifikaci.

* Zadejte popis každý parametr v metadatech:

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* Definujte výchozí hodnoty pro parametry, které nejsou citlivé. Zadáním výchozí hodnota je jednodušší, pokud chcete nasadit šablonu a uživatelům vaší šablony ukázkovým odpovídající hodnotu. Všechny výchozí hodnoty pro parametr musí být platný pro všechny uživatele ve výchozí konfiguraci nasazení. 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* Pokud chcete zadat volitelný parametr, nepoužívejte prázdný řetězec jako výchozí hodnotu. Místo toho použijte hodnotu literálu nebo výraz jazyka k vytvoření hodnotu.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   },
   ```

* Nepoužívejte parametr pro verzi rozhraní API pro typ prostředku. Vlastnosti prostředku a hodnoty se můžou lišit podle čísla verze. Technologie IntelliSense v editoru kódu nelze určit správné schéma, pokud verze rozhraní API je nastavena na parametr. Místo toho verzi pevně zakódovat rozhraní API v šabloně.

* Použití `allowedValues` opatrně. Používejte pouze v případě, že jste zkontrolujte, zda že některé hodnoty nejsou zahrnuté v možnosti povolené. Pokud používáte `allowedValues` příliš široce může blokovat platný nasazení podle není udržování aktuálnosti seznamu.

* Pokud název parametru v šabloně odpovídá parametru v příkazu pro nasazení prostředí PowerShell, správce prostředků řeší tento konflikt názvů tak, že přidáte Příponové **FromTemplate** parametru šablony. Například pokud zahrnete parametr s názvem **ResourceGroupName** v šabloně, je v konfliktu s **ResourceGroupName** parametr [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) rutiny. Během nasazení, zobrazí výzva k zadání hodnoty pro **ResourceGroupNameFromTemplate**.

### <a name="security-recommendations-for-parameters"></a>Doporučení zabezpečení pro parametry

* Vždy používejte parametry pro uživatelská jména a hesla (nebo tajné kódy).

* Použití `securestring` pro všechny tajné kódy a hesla. Pokud předáte citlivá data v objektu JSON, použijte `secureObject` typu. Parametry šablony zabezpečený řetězec nebo objekt zabezpečeného typy nelze přečíst po nasazení prostředků. 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* Neposkytuje výchozí hodnoty pro uživatelská jména, hesla nebo libovolnou hodnotu, která vyžaduje `secureString` typu.

* Neposkytují výchozí hodnoty pro vlastnosti, které zvyšují možností útoku aplikace.

### <a name="location-recommendations-for-parameters"></a>Doporučení pro umístění pro parametry

* Parametr použít k určení umístění pro prostředky a nastavení výchozí hodnoty `resourceGroup().location`. Poskytuje umístění parametru umožňuje uživatelům šablony a zadejte umístění, ke kterým má oprávnění k nasazení do.

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   },
   ```

* Nezadávejte `allowedValues` parametru umístění. Umístění, které jste zadali, nemusí být k dispozici ve všech cloudech.

* Použijte hodnotu parametru umístění pro prostředky, které by mohly být ve stejném umístění. Tento přístup minimalizuje počet pokusů, které se uživatelům zobrazí výzva k zadání umístění informací.

* Pro prostředky, které nejsou dostupné ve všech umístěních použít samostatný parametr nebo zadat hodnotu literálu umístění.

## <a name="variables"></a>Proměnné

Následující informace mohou být užitečné při práci s [proměnné](resource-manager-templates-variables.md):

* Použijte proměnné pro hodnoty, které je nutné použít více než jednou v šabloně. Pokud hodnota je použit pouze jednou, pevně zakódované hodnotu díky šablony snadněji čtou.

* Použijte proměnné pro hodnoty, které vytvoříte z komplexních uspořádání šablony funkce. Šablona je lépe čitelný, když složitý výraz se zobrazí jenom v proměnné.

* Nepoužívejte proměnné pro `apiVersion` prostředku. Verze rozhraní API Určuje schéma prostředku. Často nelze změnit verzi beze změny vlastností pro prostředek.

* Nelze použít [odkaz](resource-group-template-functions-resource.md#reference) fungovat v **proměnné** část šablony. **Odkaz** funkce odvozuje svou hodnotu z prostředku běhový stav. Proměnné jsou však přeložit při počáteční analýze šablony. Konstruktor hodnoty tuto potřebu **odkaz** fungovat přímo v **prostředky** nebo **výstupy** část šablony.

* Zahrnout proměnné pro názvy prostředků, které musí být jedinečný.

* Použití [kopírovací smyčkou do proměnných](resource-group-create-multiple.md#variable-iteration) vytvořit opakované vzorek objektů JSON.

* Odeberte nepoužité proměnné.

## <a name="resource-dependencies"></a>Závislosti prostředků

Při rozhodování o tom, co [závislosti](resource-group-define-dependencies.md) k nastavení, použijte následující pokyny:

* Použití **odkaz** fungovat a předejte mu název prostředku implicitní závislosti mezi prostředky, které potřebují ke sdílení vlastnost nastavit. Nepřidávejte explicitní `dependsOn` prvku, když už máte definované implicitní závislostí. Tento přístup snižuje riziko toho, že zbytečné závislosti.

* Nastavte jako závisí na jeho nadřazený prostředek podřízený prostředek.

* Prostředky s [podmínky](resource-manager-templates-resources.md#condition) nastaven na hodnotu false, budou automaticky odebrány z pak pořadí závislostí. Nastavte závislosti, jako kdyby je vždy nasazený prostředek.

* Umožněte závislosti cascade bez nastavení je explicitně. Například vašeho virtuálního počítače závisí na rozhraní virtuální sítě a virtuální síťové rozhraní závisí na virtuální sítě a veřejné IP adresy. Proto tento virtuální počítač je nasazený všechny tři prostředky, ale není explicitně nastavit virtuální počítač jako závislé na všechny tři zdroje. Tento postup vysvětluje pak pořadí závislostí a usnadňuje později změnit šablonu.

* Pokud hodnotu nelze určit před nasazením, zkuste nasazení prostředků bez závislosti. Například hodnota konfigurace musí název jiného prostředku, nemusí potřebovat závislost. Tyto pokyny vždycky nefunguje, protože některé prostředky ověřte existenci jiný prostředek. Pokud obdržíte chybu, přidejte závislosti.

## <a name="resources"></a>Zdroje a prostředky

Následující informace mohou být užitečné při práci s [prostředky](resource-manager-templates-resources.md):

* Chcete-li pomoci ostatních uživatelů pochopit účel prostředku, zadejte **komentáře** pro každý prostředek v šabloně:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Pokud používáte *veřejný koncový bod* v šabloně (třeba Azure Blob storage veřejný koncový bod), *nechcete pevně zakódovat* obor názvů. Použití **odkaz** funkce, která se dynamicky načíst obor názvů. Tento přístup můžete použít k nasazení šablony do prostředí jiný obor názvů veřejné beze změny ručně koncový bod v šabloně. Nastavte verzi rozhraní API na stejnou verzi, kterou používáte pro účet úložiště v šabloně:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Pokud účet úložiště je nasazená ve stejné šabloně, kterou vytváříte, není nutné určit obor názvů poskytovatele při odkazování na prostředek. Následující příklad znázorňuje zjednodušenou syntaxi:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Pokud máte jiné hodnoty v šabloně, které jsou nakonfigurovány pro použití veřejných obor názvů, změnit tyto hodnoty tak, aby odrážely stejné **odkaz** funkce. Například můžete nastavit **storageUri** vlastnost diagnostického profilu virtuálního počítače:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Můžete také využít existující účet úložiště, který je v jiné skupině prostředků:

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* Přiřadíte veřejné IP adresy virtuálního počítače pouze v případě, že aplikace vyžaduje. Pro připojení k virtuálnímu počítači (VM) pro ladění nebo pro správu nebo účely správy, použijte pravidla příchozího překladu adres, bránu virtuální sítě nebo jumpbox.
   
     Další informace o připojení k virtuálním počítačům naleznete v tématu:
   
   * [Spuštění virtuálních počítačů pro N-vrstvou architekturu v Azure](../guidance/guidance-compute-n-tier-vm.md)
   * [Nastavení přístupu WinRM pro virtuální počítače v Azure Resource Manageru](../virtual-machines/windows/winrm.md)
   * [Povolení externího přístupu k vašemu virtuálnímu počítači pomocí webu Azure portal](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Povolení externího přístupu k vašemu virtuálnímu počítači pomocí Powershellu](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Povolení externího přístupu k virtuálním počítačům s Linuxem pomocí Azure CLI](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)

* **Popisek_názvu_domény** vlastnost pro veřejné IP adresy musí být jedinečný. **Popisek_názvu_domény** hodnota musí být dlouhý 3 až 63 znaků a řídí se pravidly určené tento regulární výraz: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Protože **uniqueString** funkce z nich generuje řetězec, který je 13 znaků dlouhá a **dnsPrefixString** parametr je omezen na 50 znaků:

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* Když přidáte heslo k rozšíření vlastních skriptů, použijte **commandToExecute** vlastnost **protectedSettings** vlastnost:
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > Chcete-li zajistit, že tajných kódů se šifrují, pokud jsou předány jako parametry pro virtuální počítače a rozšíření, použijte **protectedSettings** vlastnost příslušné rozšíření.
   > 
   > 

## <a name="outputs"></a>Výstupy

Pokud použijete šablonu k vytvoření veřejné IP adresy, přidejte [výstupy části](resource-manager-templates-outputs.md) , který vrátí podrobnosti o IP adresu a plně kvalifikovaný název domény (FQDN). Výstupní hodnoty můžete snadno získat podrobnosti o veřejné IP adresy a plně kvalifikované názvy domény po nasazení.

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="next-steps"></a>Další postup

* Informace o struktuře souboru šablony Resource Manageru najdete v tématu [Princip struktury a syntaxe šablon Azure Resource Manageru](resource-group-authoring-templates.md).
* Doporučení, jak vytvářet šablony, které fungují ve všech prostředích cloudu Azure, najdete v části [šablon vývoj Azure Resource Manageru pro cloud konzistence](templates-cloud-consistency.md).
