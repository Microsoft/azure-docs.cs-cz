---
title: Osvědčené postupy pro šablony
description: Popisuje doporučené přístupy k vytváření Azure Resource Manager šablon. Nabízí návrhy, aby se předešlo běžným problémům při používání šablon.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: c62bde8fc8cfc79330d13b7b2ff4f778dadf1339
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96497975"
---
# <a name="arm-template-best-practices"></a>Osvědčené postupy pro šablonu ARM

V tomto článku se dozvíte, jak používat Doporučené postupy při sestavování šablony ARM. Tato doporučení vám pomůžou vyhnout se běžným problémům při použití šablony ARM k nasazení řešení.

## <a name="template-limits"></a>Omezení šablon

Omezte velikost šablony na 4 MB a každý soubor parametrů na 64 KB. Limit velikosti 4 MB se vztahuje na konečný stav šablony po rozbalení pomocí iterativních definic prostředků a hodnot proměnných a parametrů.

Máte také omezení:

* parametry 256
* proměnné 256
* 800 prostředků (včetně počtu kopií)
* 64 výstupní hodnoty
* 24 576 znaků ve výrazu šablony

Můžete překročit několik omezení šablon pomocí vnořené šablony. Další informace najdete v tématu [použití propojených šablon při nasazování prostředků Azure](linked-templates.md). Chcete-li snížit počet parametrů, proměnných nebo výstupů, můžete zkombinovat několik hodnot do objektu. Další informace najdete v tématu [objekty jako parametry](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

## <a name="resource-group"></a>Skupina prostředků

Když nasadíte prostředky do skupiny prostředků, skupina prostředků uloží metadata o prostředcích. Metadata se ukládají do umístění skupiny prostředků.

Pokud je oblast skupiny prostředků dočasně nedostupná, nemůžete aktualizovat prostředky ve skupině prostředků, protože metadata nejsou k dispozici. Prostředky v jiných oblastech budou pořád fungovat podle očekávání, ale nemůžete je aktualizovat. Pokud chcete minimalizovat riziko, vyhledejte skupinu prostředků a prostředky ve stejné oblasti.

## <a name="parameters"></a>Parametry

Informace v této části mohou být užitečné při práci s [parametry](template-parameters.md).

### <a name="general-recommendations-for-parameters"></a>Obecná doporučení pro parametry

* Minimalizujte použití parametrů. Místo toho použijte proměnné nebo hodnoty literálů pro vlastnosti, které není nutné zadávat během nasazování.

* Pro názvy parametrů použijte ve stylu CamelCase Case.

* Parametry můžete použít pro nastavení, která se liší podle prostředí, například podle skladové položky, velikosti nebo kapacity.

* Použijte parametry pro názvy prostředků, které chcete zadat pro snadnější identifikaci.

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

* Definujte výchozí hodnoty pro parametry, které nejsou citlivé. Zadáním výchozí hodnoty je snazší šablonu nasadit a uživatelé šablony uvidí příklad odpovídající hodnoty. Všechny výchozí hodnoty pro parametr musí být platné pro všechny uživatele ve výchozí konfiguraci nasazení. 
   
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

* Chcete-li zadat volitelný parametr, nepoužívejte jako výchozí hodnotu prázdný řetězec. Místo toho použijte literálovou hodnotu nebo výraz jazyka pro vytvoření hodnoty.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   },
   ```

* Používejte `allowedValues` zřídka. Použijte ji pouze v případě, že je nutné zajistit, aby některé hodnoty nebyly zahrnuty do povolených možností. Pokud používáte `allowedValues` moc široce, můžete zablokovat platná nasazení tím, že seznam nezůstane v aktuálním stavu.

* Pokud název parametru ve vaší šabloně odpovídá parametru v příkazu PowerShell Deployment, Správce prostředků vyřeší tento konflikt názvů přidáním přípony **FromTemplate** do parametru Template. Například pokud zahrnete parametr s názvem **ResourceGroupName** v šabloně, je v konfliktu s parametrem **ResourceGroupName** v rutině [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) . Během nasazování budete vyzváni k zadání hodnoty pro **ResourceGroupNameFromTemplate**.

### <a name="security-recommendations-for-parameters"></a>Doporučení zabezpečení pro parametry

* Vždy používejte parametry pro uživatelská jména a hesla (nebo tajné klíče).

* Používá `securestring` se pro všechna hesla a tajné klíče. Pokud předáte citlivé údaje v objektu JSON, použijte `secureObject` typ. Parametry šablony se zabezpečeným řetězcem nebo typy zabezpečených objektů nelze přečíst po nasazení prostředků. 
   
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

* Nezadávejte výchozí hodnoty pro uživatelská jména, hesla ani žádná hodnota, která vyžaduje `secureString` typ.

* Neposkytněte výchozí hodnoty pro vlastnosti, které zvyšují prostor pro útoky v aplikaci.

### <a name="location-recommendations-for-parameters"></a>Doporučení pro umístění parametrů

* Pomocí parametru zadejte umístění pro prostředky a nastavte výchozí hodnotu na `resourceGroup().location` . Zadání parametru Location umožňuje uživatelům šablony určit umístění, ke kterému mají oprávnění k nasazení.

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

* Nezadávejte `allowedValues` pro parametr Location. Zadaná umístění nemusí být k dispozici ve všech cloudech.

* Pro prostředky, které jsou pravděpodobně ve stejném umístění, použijte hodnotu parametru Location. Tento přístup minimalizuje počet vyzvání uživatelů k zadání informací o poloze.

* U prostředků, které nejsou k dispozici ve všech umístěních, použijte samostatný parametr nebo zadejte hodnotu umístění literálu.

## <a name="variables"></a>Proměnné

Následující informace můžou být užitečné při práci s [proměnnými](template-variables.md):

* Pro názvy proměnných použijte ve stylu CamelCase Case.

* Použijte proměnné pro hodnoty, které je třeba použít více než jednou v šabloně. Pokud se hodnota používá jenom jednou, pevně zakódovaná hodnota usnadňuje čtení šablony.

* Použijte proměnné pro hodnoty, které vytváříte ze složitých uspořádání funkcí šablon. Pokud se složitý výraz zobrazuje pouze v proměnných, šablona je snazší číst.

* V části **proměnné** v šabloně nemůžete použít [odkazovou](template-functions-resource.md#reference) funkci. **Odkazovaná** funkce odvozuje svou hodnotu z běhového stavu prostředku. Proměnné jsou však při počáteční analýze šablony vyřešeny. Sestavte hodnoty, které vyžadují **odkazovou** funkci přímo v **části** **Resources** nebo Outputs šablony.

* Přidejte proměnné pro názvy prostředků, které musí být jedinečné.

* Pomocí [smyčky kopírování v proměnných](copy-variables.md) vytvořte opakovaný vzor objektů JSON.

* Odeberte nepoužité proměnné.

## <a name="api-version"></a>Verze rozhraní API

Nastavte `apiVersion` vlastnost na pevně kódovanou verzi rozhraní API pro daný typ prostředku. Při vytváření nové šablony doporučujeme pro typ prostředku použít nejnovější verzi rozhraní API. Chcete-li zjistit dostupné hodnoty, přečtěte si téma [Reference k šabloně](/azure/templates/).

Pokud vaše šablona funguje podle očekávání, doporučujeme, abyste dál používali stejnou verzi rozhraní API. Pomocí stejné verze rozhraní API se nemusíte starat o zásadní změny, které by mohly být představené v novějších verzích.

Nepoužívejte parametr pro verzi rozhraní API. Vlastnosti a hodnoty prostředků se mohou lišit podle verze rozhraní API. Technologie IntelliSense v editoru kódu nemůže určit správné schéma, pokud je verze rozhraní API nastavena na parametr. Pokud předáte verzi rozhraní API, která neodpovídá vlastnostem ve vaší šabloně, nasazení se nezdaří.

Nepoužívejte proměnné pro verzi rozhraní API. Konkrétně Nepoužívejte [funkci poskytovatelé](template-functions-resource.md#providers) k dynamickému získání verzí rozhraní API během nasazování. Dynamicky načtená verze rozhraní API nemusí odpovídat vlastnostem ve vaší šabloně.

## <a name="resource-dependencies"></a>Závislosti prostředků

Při rozhodování, jaké [závislosti](define-resource-dependency.md) se mají nastavit, postupujte podle následujících pokynů:

* Použijte **odkazovou** funkci a předejte název prostředku k nastavení implicitní závislosti mezi prostředky, které potřebují sdílet vlastnost. Nepřidejte explicitní `dependsOn` element, pokud jste již definovali implicitní závislost. Tento přístup snižuje riziko zbytečných závislostí. Příklad nastavení implicitní závislosti naleznete v tématu [implicitní závislost](define-resource-dependency.md#reference-and-list-functions).

* Nastavte podřízený prostředek jako závislý na svém nadřazeném prostředku.

* Prostředky s [prvkem podmínky](conditional-resource-deployment.md) nastavenou na hodnotu false jsou automaticky odebrány z pořadí závislostí. Nastavte závislosti, jako by byl prostředek vždy nasazen.

* Závislosti se dají uspořádat bez explicitního nastavování. Například váš virtuální počítač závisí na virtuálním síťovém rozhraní a rozhraní virtuální sítě závisí na virtuální síti a veřejných IP adresách. Proto se virtuální počítač nasadí po všech třech prostředcích, ale virtuální počítač se explicitně nenastaví jako závislý na všech třech prostředcích. Tento přístup objasňuje pořadí závislostí a usnadňuje změnu šablony později.

* Pokud je možné určit hodnotu před nasazením, zkuste prostředek nasadit bez závislosti. Pokud například hodnota konfigurace potřebuje název jiného prostředku, možná nebudete potřebovat závislost. Tyto pokyny nefungují vždycky, protože některé prostředky ověřují existenci druhého prostředku. Pokud se zobrazí chyba, přidejte závislost.

## <a name="resources"></a>Zdroje a prostředky

Následující informace můžou být užitečné při práci s [prostředky](template-syntax.md#resources):

* Aby mohli jiní přispěvatelé pochopit účel prostředku, zadejte **Komentáře** pro každý prostředek v šabloně:
   
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

* Pokud v šabloně používáte *veřejný koncový bod* (například veřejný koncový bod služby Azure Blob Storage), *neprovádějte pevný kód* oboru názvů. K dynamickému načtení oboru názvů použijte **odkazovou** funkci. Tento přístup můžete použít k nasazení šablony do různých prostředí veřejného oboru názvů, aniž byste museli ručně měnit koncový bod v šabloně. Nastavte verzi rozhraní API na stejnou verzi, kterou používáte pro účet úložiště v šabloně:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Pokud je účet úložiště nasazený ve stejné šabloně, kterou vytváříte, a název účtu úložiště se v šabloně nesdílí s jiným prostředkem, nemusíte při odkazování na prostředek zadávat obor názvů poskytovatele ani apiVersion. Následující příklad ukazuje zjednodušenou syntaxi:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
       }
   }
   ```
     
   Můžete také vytvořit odkaz na existující účet úložiště, který je v jiné skupině prostředků:

   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('existingStorageAccountName')), '2019-06-01').primaryEndpoints.blob]"
       }
   }
   ```

* Přiřaďte veřejné IP adresy virtuálnímu počítači pouze v případě, že je aplikace vyžaduje. Pokud se chcete připojit k virtuálnímu počítači (VM) pro ladění nebo pro účely správy nebo správy, použijte pravidla příchozího překladu adres (NAT), bránu virtuální sítě nebo JumpBox.
   
     Další informace o připojení k virtuálním počítačům najdete v těchto tématech:
   
   * [Spouštění virtuálních počítačů pro N-vrstvou architekturu v Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
   * [Nastavení přístupu WinRM pro virtuální počítače v Azure Resource Manager](../../virtual-machines/windows/winrm.md)
   * [Povolení externího přístupu k VIRTUÁLNÍmu počítači pomocí Azure Portal](../../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Povolení externího přístupu k VIRTUÁLNÍmu počítači pomocí PowerShellu](../../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Povolení externího přístupu k VIRTUÁLNÍmu počítači se systémem Linux pomocí Azure CLI](../../virtual-machines/linux/nsg-quickstart.md)

* Vlastnost **domainNameLabel** pro veřejné IP adresy musí být jedinečná. Hodnota **domainNameLabel** musí mít délku 3 až 63 znaků a musí následovat po pravidlech určených tímto regulárním výrazem: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$` . Vzhledem k tomu, že funkce **uniqueString** generuje řetězec o délce 13 znaků, parametr **dnsPrefixString** je omezen na 50 znaků:

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

* Když přidáte heslo do rozšíření vlastních skriptů, použijte vlastnost **commandToExecute** ve vlastnosti **protectedSettings** :
   
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
   > Aby se zajistilo šifrování tajných klíčů při jejich předávání jako parametrů virtuálním počítačům a rozšířením, použijte vlastnost **protectedSettings** relevantních rozšíření.
   > 

## <a name="use-test-toolkit"></a>Použít sadu testů

Sada nástrojů pro test šablon ARM je skript, který kontroluje, jestli vaše šablona používá Doporučené postupy. Pokud vaše šablona není kompatibilní s doporučenými postupy, vrátí seznam upozornění s navrhovanými změnami. Sada testů vám může pomáhat s postupem implementace osvědčených postupů ve vaší šabloně.

Po dokončení šablony spusťte sadu nástrojů test Toolkit, abyste viděli, zda existují způsoby, jak můžete zlepšit její implementaci. Další informace najdete v tématu [Sada nástrojů pro test šablon ARM](test-toolkit.md).

## <a name="next-steps"></a>Další kroky

* Informace o struktuře souboru šablony najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
* Doporučení k vytváření šablon, které fungují ve všech cloudových prostředích Azure, najdete v tématu [vývoj šablon ARM pro konzistenci cloudu](templates-cloud-consistency.md).