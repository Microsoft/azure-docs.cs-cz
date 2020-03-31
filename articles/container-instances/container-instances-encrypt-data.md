---
title: Šifrování dat nasazení
description: Informace o šifrování trvalých dat pro prostředky instancí kontejneru a o šifrování dat pomocí klíče spravovaného zákazníkem
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: ad232c5d9df9f6bfae3a79dbd72e2c68143be949
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080356"
---
# <a name="encrypt-deployment-data"></a>Šifrování dat nasazení

Při spuštění prostředků Azure Container Instances (ACI) v cloudu služba ACI shromažďuje a zachová data související s vašimi kontejnery. ACI automaticky šifruje tato data, pokud je trvalé v cloudu. Toto šifrování chrání vaše data, aby vám pomohlo splnit závazky vaší organizace v oblasti zabezpečení a dodržování předpisů. ACI také poskytuje možnost šifrovat tato data pomocí vlastního klíče, což vám dává větší kontrolu nad daty souvisejícími s nasazením ACI.

## <a name="about-aci-data-encryption"></a>Šifrování dat ACI 

Data v ACI jsou šifrována a dešifrována pomocí 256bitového šifrování AES. Je povolena pro všechna nasazení ACI a není nutné upravovat nasazení nebo kontejnery využít tohoto šifrování. To zahrnuje metadata o nasazení, proměnné prostředí, klíče předávané do kontejnerů a protokoly trvalé po zastavení kontejnerů, takže je stále můžete vidět. Šifrování nemá vliv na výkon skupiny kontejnerů a neexistuje žádné další náklady na šifrování.

## <a name="encryption-key-management"></a>Správa šifrovacího klíče

Můžete se spolehnout na klíče spravované společností Microsoft pro šifrování dat kontejneru nebo můžete spravovat šifrování pomocí vlastních klíčů. Následující tabulka porovnává tyto možnosti: 

|    |    Klíče spravované společností Microsoft     |     Klíče spravované zákazníkem     |
|----|----|----|
|    Operace šifrování/dešifrování    |    Azure    |    Azure    |
|    Úložiště klíčů    |    Úložiště klíčů Microsoftu    |    Azure Key Vault    |
|    Odpovědnost za střídání klíčů    |    Microsoft    |    Zákazník    |
|    Přístup ke klíči    |    Pouze microsoft    |    Microsoft, zákazník    |

Zbytek dokumentu pokrývá kroky potřebné k šifrování dat nasazení ACI pomocí klíče (klíč spravovaný zákazníkem). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Šifrování dat pomocí klíče spravovaného zákazníkem

### <a name="create-service-principal-for-aci"></a>Vytvořit instanční objekt pro ACI

Prvním krokem je zajistit, aby váš [tenant Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) měl přidělený instanční objekt pro udělování oprávnění službě Azure Container Instances. 

> [!IMPORTANT]
> Chcete-li spustit následující příkaz a úspěšně vytvořit instanční objekt, potvrďte, že máte oprávnění k vytváření instančních objektů v tenantovi.
>

Následující příkaz příkazu příkazu příkazu příkazu cli nastaví aci SP ve vašem prostředí Azure:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

Výstup ze spuštění tohoto příkazu by měl zobrazit instanční objekt služby, který byl nastaven s "displayName": "Azure Container Instance Service."

V případě, že se vám nepodaří úspěšně vytvořit instanční objekt:
* potvrďte, že k tomu máte oprávnění ve vašem tenantovi
* zkontrolujte, zda instanční objekt již existuje ve vašem tenantovi pro nasazení do ACI. Můžete to udělat `az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9` spuštěním a použitím tohoto instančního objektu místo

### <a name="create-a-key-vault-resource"></a>Vytvoření prostředku trezoru klíčů

Vytvořte Azure Key Vault pomocí [portálu Azure Portal](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault), [CLI](https://docs.microsoft.com/azure/key-vault/quick-create-cli)nebo [PowerShellu](https://docs.microsoft.com/azure/key-vault/quick-create-powershell). 

Vlastnosti trezoru klíčů použijte podle následujících pokynů: 
* Název: Je potřeba zadat jedinečný název. 
* Předplatné: Zvolte předplatné.
* V části Skupina prostředků zvolte existující skupinu prostředků nebo vytvořte nový a zadejte název skupiny prostředků.
* V rozevírací nabídce Umístění zvolte umístění.
* Ostatní možnosti můžete ponechat na výchozích hodnotách nebo vybrat na základě dalších požadavků.

> [!IMPORTANT]
> Při použití klíčů spravovaných zákazníkem k šifrování šablony nasazení ACI se doporučuje nastavit následující dvě vlastnosti v trezoru klíčů, obnovitelné odstranění a nevyčistit. Tyto vlastnosti nejsou ve výchozím nastavení povolené, ale lze povolit pomocí powershellu nebo rozhraní příkazového příkazu Azure v novém nebo existujícím trezoru klíčů.

### <a name="generate-a-new-key"></a>Generovat nový klíč 

Po vytvoření trezoru klíčů přejděte na prostředek na webu Azure Portal. V levé navigační nabídce okna prostředků klikněte v části Nastavení na **klávesy**. V zobrazení "Klíče" klikněte na tlačítko Generovat/Importovat a vygenerujte nový klíč. Použijte libovolný jedinečný název pro tento klíč a všechny další předvolby na základě vašich požadavků. 

![Generovat nový klíč](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>Nastavení zásad přístupu

Vytvořte nové zásady přístupu umožňující službě ACI přístup k vašemu klíči.

* Po vygenerování klíče klikněte v části Nastavení na **položku Zásady přístupu**zpět v okně prostředků trezoru klíčů .
* Na stránce Zásady přístupu pro trezor klíčů klikněte na **Přidat zásady přístupu**.
* Nastavení *oprávnění klíče* tak, aby zahrnovala oprávnění klíče **Get** and **Unwrap Key** ![Set](./media/container-instances-encrypt-data/set-key-permissions.png)
* V *pole Vybrat hlavní*položku vyberte **službu Azure Container Instance Service.**
* Klikněte na **Přidat** dole. 

Zásady přístupu by se nyní měly zobrazit v zásadách přístupu trezoru klíčů.

![Nové zásady přístupu](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>Úprava šablony nasazení JSON

> [!IMPORTANT]
> Šifrování dat nasazení pomocí klíče spravovaného zákazníkem je k dispozici v nejnovější verzi rozhraní API (2019-12-01), která se aktuálně zavádí. Tuto verzi rozhraní API zadejte v šabloně nasazení. Pokud s tím máte nějaké problémy, obraťte se na podporu Azure.

Po nastavení klíče trezoru klíčů a zásad přístupu přidejte do šablony nasazení ACI následující vlastnosti. Další informace o nasazení prostředků ACI se šablonou v [kurzu: Nasazení skupiny s více kontejnery pomocí šablony Správce prostředků](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 
* V `resources`části `apiVersion` `2019-12-01`najetna na .
* V části vlastnosti skupiny kontejnerů `encryptionProperties`v šabloně nasazení přidejte soubor , který obsahuje následující hodnoty:
  * `vaultBaseUrl`: Název DNS trezoru klíčů naleznete v přehledovém okně prostředku trezoru klíčů na portálu
  * `keyName`: název klíče generovaného dříve
  * `keyVersion`: aktuální verze klíče. To lze nalézt kliknutím na samotný klíč (v části "Klíče" v sekci Nastavení zdroje trezoru klíčů)
* Pod vlastnostmi skupiny `sku` kontejnerů `Standard`přidejte vlastnost s hodnotou . Vlastnost `sku` je vyžadována v rozhraní API verze 2019-12-01.

Následující fragment šablony zobrazuje tyto další vlastnosti šifrování dat nasazení:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "encryptionProperties": {
                "vaultBaseUrl": "https://example.vault.azure.net",
                "keyName": "acikey",
                "keyVersion": "xxxxxxxxxxxxxxxx"
            },
            "sku": "Standard",
            "containers": {
                [...]
            }
        }
    }
]
```

Následuje úplná šablona upravená ze šablony v [kurzu: Nasazení skupiny s více kontejnery pomocí šablony Správce prostředků](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "encryptionProperties": {
            "vaultBaseUrl": "https://example.vault.azure.net",
            "keyName": "acikey",
            "keyVersion": "xxxxxxxxxxxxxxxx"
        },
        "sku": "Standard",  
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

### <a name="deploy-your-resources"></a>Nasazení prostředků

Pokud jste soubor šablony vytvořili a upravili na ploše, můžete ho nahrát do adresáře prostředí Cloud Shell přetažením souboru do něj. 

Vytvořte skupinu prostředků pomocí příkazu [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Nasaďte šablonu pomocí příkazu [vytvořit nasazení skupiny az.][az-group-deployment-create]

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

Během několika sekund by se měla zobrazit první odezva z Azure. Po dokončení nasazení budou všechna data související s tímto nasazením ustálená službou ACI zašifrována pomocí zadanýho klíče.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
