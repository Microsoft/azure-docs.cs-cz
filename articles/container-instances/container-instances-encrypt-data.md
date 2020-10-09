---
title: Šifrování dat nasazení
description: Informace o šifrování trvalých dat pro prostředky instance kontejneru a o tom, jak šifrovat data pomocí klíče spravovaného zákazníkem
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 1c45999dbb354e8c2d550be82cdf37a6694d2dbb
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91825671"
---
# <a name="encrypt-deployment-data"></a>Šifrování dat nasazení

Při spuštění Azure Container Instances (ACI) prostředků v cloudu služba ACI shromažďuje a ukládá data týkající se vašich kontejnerů. ACI automaticky šifruje tato data, když je trvale v cloudu. Toto šifrování chrání vaše data, aby bylo možné pokrýt závazky týkající se zabezpečení a dodržování předpisů vaší organizace. ACI také poskytuje možnost šifrovat tato data pomocí vlastního klíče. tím získáte větší kontrolu nad daty souvisejícími s nasazeními ACI.

## <a name="about-aci-data-encryption"></a>Informace o šifrování dat ACI 

Data v ACI se šifrují a dešifrují pomocí 256 šifrování AES. Je povolená pro všechna nasazení ACI a nemusíte měnit nasazení nebo kontejnery, abyste mohli toto šifrování využít. Patří sem metadata týkající se nasazení, proměnných prostředí, klíčů předávaných do vašich kontejnerů a protokoly zachované po zastavení kontejnerů, abyste je mohli i nadále zobrazovat. Šifrování nemá vliv na výkon skupiny kontejnerů a za šifrování se neúčtují žádné další náklady.

## <a name="encryption-key-management"></a>Správa šifrovacích klíčů

Pro šifrování dat kontejneru můžete spoléhat na klíče spravované Microsoftem, nebo můžete šifrování spravovat pomocí vlastních klíčů. Následující tabulka porovnává tyto možnosti: 

|    |    Klíče spravované Microsoftem     |     Klíče spravované zákazníkem     |
|----|----|----|
|    **Operace šifrování a dešifrování**    |    Azure    |    Azure    |
|    **Úložiště klíčů**    |    Úložiště klíčů Microsoftu    |    Azure Key Vault    |
|    **Zodpovědnost za střídání klíčů**    |    Partnerský vztah Microsoftu    |    Zákazník    |
|    **Přístup ke klíči**    |    Jenom Microsoft    |    Microsoft, zákazník    |

Zbývající část dokumentu popisuje kroky potřebné k zašifrování dat nasazení ACI s klíčem (klíč spravovaný zákazníkem). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Šifrování dat pomocí klíče spravovaného zákazníkem

### <a name="create-service-principal-for-aci"></a>Vytvoření instančního objektu pro ACI

Prvním krokem je zajistit, aby měl váš [tenant Azure](../active-directory/develop/quickstart-create-new-tenant.md) instanční objekt přiřazený pro udělení oprávnění službě Azure Container Instances. 

> [!IMPORTANT]
> Pokud chcete spustit následující příkaz a úspěšně vytvořit instanční objekt, potvrďte, že máte oprávnění k vytváření instančních objektů ve vašem tenantovi.
>

Následující příkaz rozhraní příkazového řádku nastaví ACI SP v prostředí Azure:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

Výstup z běhu tohoto příkazu by měl Ukázat instanční objekt, který se nastavil s názvem DisplayName (název služby Azure Container instance).

V případě, že nemůžete úspěšně vytvořit instanční objekt:
* Ověřte, že máte ve svém tenantovi oprávnění k tomu.
* Zkontrolujte, jestli už ve vašem tenantovi neexistuje instanční objekt pro nasazení do ACI. Můžete to udělat tak, že ho spustíte `az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9` a použijete místo toho tento instanční objekt.

### <a name="create-a-key-vault-resource"></a>Vytvoření prostředku Key Vault

Vytvořte Azure Key Vault pomocí [Azure Portal](../key-vault/secrets/quick-create-portal.md#create-a-vault), [CLI](../key-vault/secrets/quick-create-cli.md)nebo [PowerShellu](../key-vault/secrets/quick-create-powershell.md). 

Pro vlastnosti vašeho trezoru klíčů použijte následující pokyny: 
* Název: Je potřeba zadat jedinečný název. 
* Předplatné: Zvolte předplatné.
* V části Skupina prostředků vyberte existující skupinu prostředků nebo vytvořte novou a zadejte název skupiny prostředků.
* V rozevírací nabídce Umístění zvolte umístění.
* Ostatní možnosti můžete ponechat na jejich výchozích hodnotách nebo vybrat na základě dalších požadavků.

> [!IMPORTANT]
> Při použití klíčů spravovaných zákazníkem k šifrování šablony nasazení ACI se doporučuje nastavit následující dvě vlastnosti trezoru klíčů, obnovitelné odstranění a Nemazat. Tyto vlastnosti nejsou ve výchozím nastavení povolené, ale můžete je povolit pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI v novém nebo existujícím trezoru klíčů.

### <a name="generate-a-new-key"></a>Vygenerovat nový klíč 

Po vytvoření trezoru klíčů přejděte k prostředku v Azure Portal. V levé navigační nabídce okna prostředků v části Nastavení klikněte na **klíče**. Pokud chcete vygenerovat nový klíč, klikněte v zobrazení pro "klíče" na "vygenerovat/importovat". Pro tento klíč použijte libovolný jedinečný název a všechny další předvolby podle vašich požadavků. 

![Vygenerovat nový klíč](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>Nastavení zásad přístupu

Vytvořte nové zásady přístupu, které umožní službě ACI přístup k vašemu klíči.

* Po vygenerování klíče zpátky v okně prostředku trezoru klíčů v části Nastavení klikněte na **zásady přístupu**.
* Na stránce zásady přístupu pro váš Trezor klíčů klikněte na **Přidat zásady přístupu**.
* Nastavte *klíčová oprávnění* tak, aby zahrnovala oprávnění kláves **získat** a **Rozbalit** klíč sady klíčů. ![](./media/container-instances-encrypt-data/set-key-permissions.png)
* V případě *Vyberte objekt zabezpečení*vyberte **Azure Container instance Service** .
* V dolní části klikněte na **Přidat** . 

Zásady přístupu by se teď měly zobrazit v zásadách přístupu trezoru klíčů.

![Nové zásady přístupu](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>Úprava šablony nasazení JSON

> [!IMPORTANT]
> Šifrování dat nasazení pomocí klíče spravovaného zákazníkem je dostupné v nejnovější verzi rozhraní API (2019-12-01), která se v tuto chvíli zavádí. Tuto verzi rozhraní API zadejte v šabloně nasazení. Pokud s tím máte nějaké problémy, obraťte se prosím na podporu Azure.

Jakmile nastavíte klíč trezoru klíčů a zásadu přístupu, přidejte do šablony nasazení ACI následující vlastnosti. Další informace o nasazení prostředků ACI pomocí šablony v tomto [kurzu: nasazení skupiny s více kontejnery pomocí šablony Správce prostředků](./container-instances-multi-container-group.md). 
* V části `resources` nastavte `apiVersion` na `2019-12-01` .
* V části vlastnosti skupiny kontejnerů v šabloně nasazení přidejte `encryptionProperties` , který obsahuje následující hodnoty:
  * `vaultBaseUrl`: název DNS vašeho trezoru klíčů najdete v okně Přehled prostředku trezoru klíčů na portálu.
  * `keyName`: název klíče vygenerovaného dříve.
  * `keyVersion`: aktuální verze klíče. To můžete najít kliknutím na vlastní klíč (v části klíče v části nastavení v prostředku trezoru klíčů).
* V části vlastnosti skupiny kontejnerů přidejte `sku` vlastnost s hodnotou `Standard` . `sku`Vlastnost je povinná v rozhraní API verze 2019-12-01.

Následující fragment šablony zobrazuje tyto další vlastnosti pro šifrování dat nasazení:

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

Toto je kompletní šablona přizpůsobená pomocí šablony v [kurzu: nasazení skupiny s více kontejnery pomocí šablony Správce prostředků](./container-instances-multi-container-group.md). 

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

Pokud jste vytvořili a upravili soubor šablony na ploše, můžete ho nahrát do adresáře Cloud Shell přetažením souboru do něj. 

Vytvořte skupinu prostředků pomocí příkazu [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Nasaďte šablonu pomocí příkazu [AZ Deployment Group Create][az-deployment-group-create] .

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

Během několika sekund by se měla zobrazit první odezva z Azure. Až se nasazení dokončí, všechna data související s tím, která jsou trvale zachovaná službou ACI, se zašifrují pomocí zadaného klíče.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group/#az-deployment-group-create
