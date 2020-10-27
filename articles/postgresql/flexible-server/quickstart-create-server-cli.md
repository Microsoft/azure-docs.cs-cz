---
title: 'Rychlý Start: vytvoření serveru – Azure CLI-Azure Database for PostgreSQL-flexibilní Server'
description: V tomto rychlém startu se dozvíte, jak pomocí rozhraní příkazového řádku Azure vytvořit Azure Database for PostgreSQL flexibilní Server ve skupině prostředků Azure.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/22/2020
ms.custom: mvc
ms.openlocfilehash: 75d76c64c10bf3ecc28c32452618048119bb9a59
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547618"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-flexible-server-using-azure-cli"></a>Rychlý Start: vytvoření Azure Database for PostgreSQL flexibilního serveru pomocí Azure CLI

V tomto rychlém startu se dozvíte, jak pomocí příkazů rozhraní příkazového [řádku Azure](/cli/azure/get-started-with-azure-cli) v [Azure Cloud Shell](https://shell.azure.com) vytvořit Azure Database for PostgreSQL flexibilní Server během pěti minut. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

> [!IMPORTANT] 
> Azure Database for PostgreSQL flexibilní Server je momentálně ve verzi Preview.

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) je bezplatné interaktivní prostředí, které můžete použít ke spuštění kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem.

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Můžete také otevřít Cloud Shell na samostatné kartě prohlížeče, a to tak, že kliknete na [https://shell.azure.com/bash](https://shell.azure.com/bash) . Vyberte **Kopírovat** pro zkopírování bloků kódu, vložení do Cloud Shell a vyberte **ENTER** pro spuštění.

Pokud dáváte přednost instalaci a používání rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2,0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Požadavky

K účtu se budete muset přihlásit pomocí příkazu [AZ Login](/cli/azure/reference-index#az-login) . Poznamenejte si vlastnost **ID** , která se vztahuje k **ID předplatného** pro váš účet Azure.

```azurecli-interactive
az login
```

Pomocí příkazu [AZ Account set](/cli/azure/account#az-account-set) vyberte konkrétní předplatné ve vašem účtu. Poznamenejte si hodnotu **ID** z výstupu **AZ Login** , který se použije jako hodnota argumentu **Subscription** v příkazu. Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém se má prostředek účtovat. Pokud chcete získat veškeré předplatné, použijte příkaz [AZ Account list](/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Vytvoření flexibilního serveru

Vytvořte [skupinu prostředků Azure](../../azure-resource-manager/management/overview.md) pomocí `az group create` příkazu a pak vytvořte PostgreSQL flexibilní server uvnitř této skupiny prostředků. Měli byste zadat jedinečný název. Následující příklad vytvoří skupinu prostředků s názvem `myresourcegroup` v umístění `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Pomocí příkazu vytvořte flexibilní server `az postgres flexible-server create` . Server může obsahovat více databází. Následující příkaz vytvoří server pomocí výchozího nastavení služby a hodnot z [místního kontextu](/cli/azure/local-context)rozhraní příkazového řádku Azure. 

```azurecli
az postgres flexible-server create
```

Vytvořený server má následující atributy: 
- Automaticky vygenerovaný název serveru, uživatelské jméno správce, heslo správce, název skupiny prostředků (Pokud už není zadané v místním kontextu) a ve stejném umístění jako vaše skupina prostředků 
- Výchozí nastavení služby pro zbývající konfigurace serveru: výpočetní vrstva (Pro obecné účely), velikost služby COMPUTE/SKU (D2s_v3-2 vCore, 8 GB RAM), doba uchování zálohy (7 dní) a verze PostgreSQL (12)
- Výchozí metoda připojení je privátní přístup (Integration VNet) s automaticky generovanou virtuální sítí a podsítí.

> [!NOTE] 
> Po vytvoření serveru se metoda připojení nedá změnit. Pokud jste například vybrali *privátní přístup (Integration VNET)* během vytváření, pak po vytvoření se nemůžete změnit na *veřejný přístup (povolených IP adres)* . Důrazně doporučujeme vytvořit server s privátním přístupem k zabezpečenému přístupu k serveru pomocí integrace virtuální sítě. Přečtěte si další informace o privátním přístupu v [článku koncepty](./concepts-networking.md).

Pokud chcete změnit výchozí nastavení, přečtěte si referenční dokumentaci k Azure CLI. <!--FIXME --> Úplný seznam konfigurovatelných parametrů rozhraní příkazového řádku. 

> [!NOTE]
> Připojení Azure Database for PostgreSQL komunikovat přes port 5432. Pokud se pokusíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 5432 povolen. V takovém případě se k serveru nemůžete připojit, dokud vaše IT oddělení neotevře port 5432.

## <a name="get-the-connection-information"></a>Získání informací o připojení

Pokud se chcete připojit k serveru, budete muset zadat informace o hostiteli a přihlašovací údaje pro přístup.

```azurecli-interactive
az postgres flexible-server show --resource-group myresourcegroup --name mydemoserver
```

Výsledek je ve formátu JSON. Poznamenejte si **fullyQualifiedDomainName** a **administratorLogin** .

<!--FIXME-->
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/flexibleServers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "name": "Standard_D2s_v3",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "publicAccess": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 131072
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/flexibleServers",
  "userVisibleState": "Ready",
  "version": "12"
}
```

## <a name="connect-using-postgresql-command-line-client"></a>Připojit pomocí klienta příkazového řádku PostgreSQL

Protože se flexibilní Server vytvořil s *privátním přístupem (Integration VNET)* , budete se muset připojit k serveru z prostředku ve stejné virtuální síti jako váš server. Můžete vytvořit virtuální počítač a přidat ho do vytvořené virtuální sítě. 

Po vytvoření virtuálního počítače se můžete na počítač SSH a nainstalovat nástroj příkazového řádku **[psql](https://www.postgresql.org/download/)** .

Pomocí psql se připojte pomocí níže uvedeného příkazu. Nahraďte hodnoty skutečným názvem serveru a heslem. 

```bash
psql -h mydemoserver.postgres.database.azure.com -u mydemouser -p
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud tyto prostředky nepotřebujete pro další rychlý start nebo kurz, můžete je pomocí následujícího příkazu odstranit:

```azurecli-interactive
az group delete --name myresourcegroup
```

Pokud byste chtěli odstranit jenom nově vytvořený server, můžete spustit `az postgres flexible-server delete` příkaz.

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[Nasazení aplikace Django pomocí App Service a PostgreSQL](tutorial-django-app-service-postgres.md)