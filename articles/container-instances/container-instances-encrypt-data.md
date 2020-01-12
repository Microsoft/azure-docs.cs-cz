---
title: Šifrovat data nasazení
description: Informace o šifrování trvalých dat pro prostředky instance kontejneru a o tom, jak šifrovat data pomocí klíče spravovaného zákazníkem
ms.topic: article
ms.date: 01/10/2020
ms.author: danlep
ms.openlocfilehash: 146effd7f1a7ad1ddd94886d1a79e2914bd1c94b
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75904208"
---
# <a name="encrypt-deployment-data"></a>Šifrovat data nasazení

Při spuštění Azure Container Instances (ACI) prostředků v cloudu služba ACI shromažďuje a ukládá data týkající se vašich kontejnerů. ACI automaticky šifruje tato data, když je trvale v cloudu. Toto šifrování chrání vaše data, aby bylo možné pokrýt závazky týkající se zabezpečení a dodržování předpisů vaší organizace. ACI také poskytuje možnost šifrovat tato data pomocí vlastního klíče. tím získáte větší kontrolu nad daty souvisejícími s nasazeními ACI.

## <a name="about-aci-data-encryption"></a>Informace o šifrování dat ACI 

Data v ACI se šifrují a dešifrují pomocí 256 šifrování AES. Je povolená pro všechna nasazení ACI a nemusíte měnit nasazení nebo kontejnery, abyste mohli toto šifrování využít. Patří sem metadata týkající se nasazení, proměnných prostředí, klíčů předávaných do vašich kontejnerů a protokoly zachované po zastavení kontejnerů, abyste je mohli i nadále zobrazovat. Šifrování nemá vliv na výkon skupiny kontejnerů a za šifrování se neúčtují žádné další náklady.

## <a name="encryption-key-management"></a>Správa šifrovacích klíčů

Pro šifrování dat kontejneru můžete spoléhat na klíče spravované Microsoftem, nebo můžete šifrování spravovat pomocí vlastních klíčů. Následující tabulka porovnává tyto možnosti: 

|    |    Klíče spravované společností Microsoft     |     Klíče spravované zákazníkem     |
|----|----|----|
|    Operace šifrování a dešifrování    |    Azure    |    Azure    |
|    Úložiště klíčů    |    Úložiště klíčů Microsoftu    |    Azure Key Vault    |
|    Zodpovědnost za střídání klíčů    |    Microsoft    |    Zákazník    |
|    Přístup ke klíči    |    Jenom Microsoft    |    Microsoft, zákazník    |

Zbývající část dokumentu popisuje kroky potřebné k zašifrování dat nasazení ACI s klíčem (klíč spravovaný zákazníkem). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Šifrování dat pomocí klíče spravovaného zákazníkem

### <a name="create-service-principal-for-aci"></a>Vytvoření instančního objektu pro ACI

Prvním krokem je zajistit, aby měl váš [tenant Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) instanční objekt přiřazený pro udělení oprávnění službě Azure Container Instances. 

Následující příkaz rozhraní příkazového řádku nastaví ACI SP v prostředí Azure:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

Výstup z běhu tohoto příkazu by měl Ukázat instanční objekt, který se nastavil s názvem DisplayName (název služby Azure Container instance).

### <a name="create-a-key-vault-resource"></a>Vytvoření prostředku Key Vault

Vytvořte Azure Key Vault pomocí [Azure Portal](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault), [CLI](https://docs.microsoft.com/azure/key-vault/quick-create-cli)nebo [PowerShellu](https://docs.microsoft.com/azure/key-vault/quick-create-powershell). 

Pro vlastnosti vašeho trezoru klíčů použijte následující pokyny: 
* Název: je vyžadován jedinečný název. 
* Předplatné: vyberte předplatné.
* V části Skupina prostředků vyberte existující skupinu prostředků nebo vytvořte novou a zadejte název skupiny prostředků.
* V rozevírací nabídce umístění vyberte umístění.
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
* Nastavte *klíčová oprávnění* pro zahrnutí klíčového oprávnění **Get** a **Unwrap** ![nastavit klíčová oprávnění](./media/container-instances-encrypt-data/set-key-permissions.png)
* V případě *Vyberte objekt zabezpečení*vyberte **Azure Container instance Service** .
* V dolní části klikněte na **Přidat** . 

Zásady přístupu by se teď měly zobrazit v zásadách přístupu trezoru klíčů.

![Nové zásady přístupu](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>Úprava šablony nasazení JSON

> [!IMPORTANT]
> Šifrování dat nasazení pomocí klíče spravovaného zákazníkem je dostupné v nejnovější verzi rozhraní API (2019-12-01), která se v tuto chvíli zavádí. Tuto verzi rozhraní API zadejte v šabloně nasazení. Pokud s tím máte nějaké problémy, obraťte se prosím na podporu Azure.

Jakmile nastavíte klíč trezoru klíčů a zásadu přístupu, přidejte do šablony nasazení ACI následující vlastnost. Další informace o nasazení prostředků ACI pomocí šablony v tomto [kurzu: nasazení skupiny s více kontejnery pomocí šablony Správce prostředků](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 

Konkrétně v části vlastností skupiny kontejnerů v šabloně nasazení přidejte "encryptionProperties", která obsahuje následující hodnoty:
* vaultBaseUrl: název DNS vašeho trezoru klíčů najdete v okně Přehled prostředku trezoru klíčů na portálu.
* keyName: název klíče vygenerovaného dříve.
* Version: aktuální verze klíče. To můžete najít kliknutím na vlastní klíč (v části klíče v části nastavení v prostředku trezoru klíčů).


```json
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
            "containers": {
                [...]
            }
        }
    }
]
```

### <a name="deploy-your-resources"></a>Nasazení prostředků

Pokud jste vytvořili a upravili soubor šablony na ploše, můžete ho nahrát do adresáře Cloud Shell přetažením souboru do něj. 

Vytvořte skupinu prostředků pomocí příkazu [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Šablonu nasaďte pomocí příkazu [AZ Group Deployment Create][az-group-deployment-create] .

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

Během několika sekund by se měla zobrazit první odezva z Azure. Až se nasazení dokončí, všechna data související s tím, která jsou trvale zachovaná službou ACI, se zašifrují pomocí zadaného klíče.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create