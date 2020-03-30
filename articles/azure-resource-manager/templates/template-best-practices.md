---
title: Osvědčené postupy pro šablony
description: Popisuje doporučené přístupy pro vytváření šablon Azure Resource Manager. Nabízí návrhy, jak se vyhnout běžným problémům při používání šablon.
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 870636d6457d842c89f261c2537644c17a335294
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156408"
---
# <a name="arm-template-best-practices"></a>Doporučené postupy pro šablonu ARM

Tento článek obsahuje doporučení, jak vytvořit šablonu Správce prostředků Azure (ARM). Tato doporučení vám pomohou vyhnout se běžným problémům při nasazení řešení pomocí šablony ARM.

Doporučení, jak řídit vaše předplatná Azure, najdete v [tématu Azure enterprise scaffold: Prescriptive subscription governance](/azure/architecture/cloud-adoption/appendix/azure-scaffold?toc=%2Fen-us%2Fazure%2Fazure-resource-manager%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json).

Doporučení, jak vytvářet šablony, které fungují ve všech cloudových prostředích Azure, najdete v [tématu Vývoj šablon Azure Resource Manageru pro konzistenci cloudu](templates-cloud-consistency.md).

## <a name="template-limits"></a>Limity šablon

Omezte velikost šablony na 4 MB a každý soubor parametrů na 64 kB. Limit 4 MB platí pro konečný stav šablony po rozšíření o definice iterativních prostředků a hodnoty pro proměnné a parametry. 

Jste také omezeni na:

* 256 parametrů
* 256 proměnných
* 800 zdrojů (včetně počtu kopií)
* 64 výstupních hodnot
* 24 576 znaků ve výrazu šablony

Některé limity šablon můžete překročit pomocí vnořené šablony. Další informace najdete [v tématu Používání propojených šablon při nasazování prostředků Azure](linked-templates.md). Chcete-li snížit počet parametrů, proměnných nebo výstupů, můžete do objektu zkombinovat několik hodnot. Další informace naleznete v [tématu Objekty jako parametry](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

## <a name="resource-group"></a>Skupina prostředků

Při nasazování prostředků do skupiny prostředků skupina prostředků ukládá metadata o prostředcích. Metadata jsou uložena v umístění skupiny prostředků.

Pokud je oblast skupiny prostředků dočasně nedostupná, nemůžete aktualizovat prostředky ve skupině prostředků, protože metadata nejsou k dispozici. Prostředky v jiných oblastech budou stále fungovat podle očekávání, ale nelze je aktualizovat. Chcete-li minimalizovat riziko, vyhledejte skupinu prostředků a prostředky ve stejné oblasti.

## <a name="parameters"></a>Parametry

Informace v této části mohou být užitečné při práci s [parametry](template-parameters.md).

### <a name="general-recommendations-for-parameters"></a>Obecná doporučení pro parametry

* Minimalizujte používání parametrů. Místo toho použijte proměnné nebo literál hodnoty pro vlastnosti, které není nutné zadat během nasazení.

* Pro názvy parametrů použijte pouzdro na velbloudy.

* Použijte parametry pro nastavení, která se liší v závislosti na prostředí, jako je skladová položka, velikost nebo kapacita.

* Parametry použijte pro názvy prostředků, které chcete zadat pro snadnou identifikaci.

* Zadejte popis každého parametru v metadatech:

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

* Definujte výchozí hodnoty pro parametry, které nejsou citlivé. Zadáním výchozí hodnoty je jednodušší nasadit šablonu a uživatelům šablony se zobrazí příklad odpovídající hodnoty. Jakákoli výchozí hodnota parametru musí být platná pro všechny uživatele ve výchozí konfiguraci nasazení. 
   
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

* Chcete-li zadat volitelný parametr, nepoužívejte prázdný řetězec jako výchozí hodnotu. Místo toho použijte hodnotu literálu nebo výraz jazyka k vytvoření hodnoty.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   },
   ```

* Nepoužívejte parametr pro verzi rozhraní API pro typ prostředku. Vlastnosti a hodnoty prostředků se mohou lišit podle čísla verze. Technologie IntelliSense v editoru kódu nemůže určit správné schéma, pokud je verze rozhraní API nastavena na parametr. Místo toho pevně kód verze rozhraní API v šabloně.

* Používejte `allowedValues` střídmě. Použijte ji pouze v případě, že musíte zajistit, aby některé hodnoty nebyly zahrnuty v povolených možnostech. Pokud používáte `allowedValues` příliš široce, můžete blokovat platná nasazení tím, že nebude váš seznam aktuální.

* Pokud název parametru v šabloně odpovídá parametru v příkazu nasazení prostředí PowerShell, Správce prostředků vyřeší tento konflikt pojmenování přidáním přípony **FromTemplate** do parametru šablony. Pokud například do šablony zahrnete parametr s názvem **ResourceGroupName,** dojde v konfliktu s parametrem **ResourceGroupName** v rutině [New-AzResourceGroupDeployment.](/powershell/module/az.resources/new-azresourcegroupdeployment) Během nasazení budete vyzváni k zadání hodnoty pro **ResourceGroupNameFromTemplate**.

### <a name="security-recommendations-for-parameters"></a>Bezpečnostní doporučení pro parametry

* Vždy používejte parametry pro uživatelská jména a hesla (nebo tajné kódy).

* Používá `securestring` se pro všechna hesla a tajné klíče. Pokud předáte citlivá data v objektu `secureObject` JSON, použijte typ. Parametry šablony se zabezpečenými řetězci nebo typy zabezpečených objektů nelze číst po nasazení prostředků. 
   
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

* Nezadávat výchozí hodnoty pro uživatelská jména, hesla nebo `secureString` hodnotu, která vyžaduje typ.

* Nezařazujte výchozí hodnoty pro vlastnosti, které zvyšují oblast povrchu útoku aplikace.

### <a name="location-recommendations-for-parameters"></a>Doporučení pro umístění parametrů

* Pomocí parametru určete umístění prostředků a nastavte `resourceGroup().location`výchozí hodnotu na . Poskytnutí parametru umístění umožňuje uživatelům šablony určit umístění, do kterého mají oprávnění k nasazení.

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

* Nezadávejte `allowedValues` parametr umístění. Zadaná umístění nemusí být dostupná ve všech cloudech.

* Použijte hodnotu parametru umístění pro prostředky, které budou pravděpodobně ve stejném umístění. Tento přístup minimalizuje počet, kolikrát jsou uživatelé požádáni o poskytnutí informací o poloze.

* Pro prostředky, které nejsou k dispozici ve všech umístěních, použijte samostatný parametr nebo zadejte hodnotu umístění literálu.

## <a name="variables"></a>Proměnné

Při práci s [proměnnými](template-variables.md)mohou být užitečné následující informace :

* Použijte pouzdro na velbloudy pro názvy proměnných.

* Proměnné používejte pro hodnoty, které je třeba použít více než jednou v šabloně. Pokud je hodnota použita pouze jednou, pevně zakódovaná hodnota usnadňuje čtení šablony.

* Proměnné používejte pro hodnoty, které vytvoříte ze složitého uspořádání funkcí šablony. Šablona je čitelnější, když se složitý výraz zobrazuje pouze v proměnných.

* Nepoužívejte proměnné pro `apiVersion` prostředek. Verze rozhraní API určuje schéma prostředku. Často nelze změnit verzi bez změny vlastností prostředku.

* Referenční [funkci](template-functions-resource.md#reference) nelze použít v části **proměnné** šablony. **Referenční** funkce odvozuje jeho hodnotu ze stavu běhu prostředku. Proměnné jsou však vyřešeny během počáteční analýzy šablony. Vytvořte hodnoty, které potřebují **referenční** funkci přímo v části **zdroje** nebo **výstupy** šablony.

* Zahrnout proměnné pro názvy prostředků, které musí být jedinečné.

* Pomocí [smyčky kopírování v proměnných](copy-variables.md) vytvořte opakovaný vzorek objektů JSON.

* Odeberte nepoužívané proměnné.

## <a name="resource-dependencies"></a>Závislosti prostředků

Při rozhodování o tom, jaké [závislosti](define-resource-dependency.md) nastavit, použijte následující pokyny:

* Pomocí **referenční** funkce a předavadě v názvu prostředku nastavte implicitní závislost mezi prostředky, které potřebují sdílet vlastnost. Nepřidávejte explicitní `dependsOn` prvek, pokud jste již definovali implicitní závislost. Tento přístup snižuje riziko vzniku zbytečných závislostí.

* Nastavte podřízený prostředek jako závislý na nadřazeném prostředku.

* Prostředky s [prvkem podmínky](conditional-resource-deployment.md) nastaveným na hodnotu false jsou automaticky odebrány z pořadí závislostí. Nastavte závislosti, jako by byl prostředek vždy nasazen.

* Uchujte závislosti kaskádové bez jejich explicitního nastavení. Například váš virtuální počítač závisí na rozhraní virtuální sítě a rozhraní virtuální sítě závisí na virtuální síti a veřejných IP adresách. Proto virtuální počítač se nasadí po všech třech prostředků, ale není explicitně nastavit virtuální počítač jako závislé na všech třech prostředků. Tento přístup objasňuje pořadí závislostí a usnadňuje pozdější změnu šablony.

* Pokud lze určit hodnotu před nasazením, zkuste nasadit prostředek bez závislosti. Například pokud hodnota konfigurace potřebuje název jiného prostředku, pravděpodobně nebudete potřebovat závislost. Tento návod ne funguje vždy, protože některé prostředky ověřují existenci jiného prostředku. Pokud se zobrazí chyba, přidejte závislost.

## <a name="resources"></a>Prostředky

Při práci s [prostředky](template-syntax.md#resources)mohou být užitečné následující informace :

* Chcete-li ostatním přispěvatelům pomoci pochopit účel zdroje, zadejte **komentáře** pro každý zdroj v šabloně:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-06-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Pokud používáte *veřejný koncový bod* v šabloně (například veřejný koncový bod úložiště objektů blob Azure), *nepoužívejte pevný kód* oboru názvů. Pomocí **referenční** funkce můžete dynamicky načíst obor názvů. Tento přístup můžete použít k nasazení šablony do různých prostředí veřejného oboru názvů bez ruční změny koncového bodu v šabloně. Nastavte verzi rozhraní API na stejnou verzi, kterou používáte pro účet úložiště v šabloně:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Pokud je účet úložiště nasazený ve stejné šabloně, kterou vytváříte, a název účtu úložiště není sdílen s jiným prostředkem v šabloně, nemusíte při odkazování na prostředek zadávat obor názvů zprostředkovatele ani apiVersion. Následující příklad ukazuje zjednodušenou syntaxi:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
       }
   }
   ```
     
   Můžete také odkazovat na existující účet úložiště, který je v jiné skupině prostředků:

   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('existingStorageAccountName')), '2019-06-01').primaryEndpoints.blob]"
       }
   }
   ```

* Přiřaďte virtuálnímu počítači veřejné IP adresy pouze v případě, že to aplikace vyžaduje. Chcete-li se připojit k virtuálnímu počítači (VM) pro ladění nebo pro účely správy nebo správy, použijte příchozí pravidla NAT, bránu virtuální sítě nebo jumpbox.
   
     Další informace o připojení k virtuálním počítačům najdete v tématu:
   
   * [Spouštění virtuálních počítačů pro n-vrstvou architekturu v Azure](../../guidance/guidance-compute-n-tier-vm.md)
   * [Nastavení přístupu winrm pro virtuální počítače ve Správci prostředků Azure](../../virtual-machines/windows/winrm.md)
   * [Povolení externího přístupu k virtuálnímu počítači pomocí portálu Azure](../../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Povolení externího přístupu k virtuálnímu počítači pomocí PowerShellu](../../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Povolení externího přístupu k virtuálnímu počítači s Linuxem pomocí azure cli](../../virtual-machines/virtual-machines-linux-nsg-quickstart.md)

* Vlastnost **domainNameLabel** pro veřejné IP adresy musí být jedinečná. Hodnota **domainNameLabel** musí mít hodnotu 3 až 63 znaků a dodržovat `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`pravidla určená tímto regulárním výrazem: . Vzhledem k tomu, že funkce **uniqueString** generuje řetězec dlouhý 13 znaků, je parametr **dnsPrefixString** omezen na 50 znaků:

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

* Když přidáte heslo do vlastního rozšíření skriptu, použijte vlastnost **commandToExecute** ve vlastnosti **protectedSettings:**
   
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
   > Chcete-li zajistit, že tajné klíče jsou šifrovány, když jsou předány jako parametry virtuálním počítačům a rozšíření, použijte **protectedSettings** vlastnost příslušných rozšíření.
   > 
   > 

## <a name="next-steps"></a>Další kroky

* Informace o struktuře souboru šablony naleznete [v tématu Understand the structure and syntax of ARM templates](template-syntax.md).
* Doporučení, jak vytvářet šablony, které fungují ve všech cloudových prostředích Azure, najdete v [tématu Vývoj šablon ARM pro konzistenci cloudu](templates-cloud-consistency.md).
