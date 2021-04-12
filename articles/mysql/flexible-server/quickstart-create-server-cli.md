---
title: 'Rychlý Start: vytvoření serveru – Azure CLI-Azure Database for MySQL-flexibilní Server'
description: V tomto rychlém startu se dozvíte, jak pomocí rozhraní příkazového řádku Azure vytvořit Azure Database for MySQL flexibilní Server ve skupině prostředků Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a63c6f074178794db38b47950e176dd729344a54
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492724"
---
# <a name="quickstart-create-an-azure-database-for-mysql-flexible-server-using-azure-cli"></a>Rychlý Start: vytvoření Azure Database for MySQL flexibilního serveru pomocí Azure CLI

V tomto rychlém startu se dozvíte, jak pomocí příkazů rozhraní příkazového [řádku Azure](/cli/azure/get-started-with-azure-cli) v [Azure Cloud Shell](https://shell.azure.com) vytvořit Azure Database for MySQL flexibilní Server během pěti minut. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

> [!IMPORTANT] 
> Azure Database for MySQL flexibilní Server je momentálně ve verzi Public Preview.

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

```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Vytvoření flexibilního serveru

Pomocí příkazu vytvořte [skupinu prostředků Azure](../../azure-resource-manager/management/overview.md) `az group create` a pak v této skupině prostředků vytvořte svůj flexibilní Server MySQL. Měli byste zadat jedinečný název. Následující příklad vytvoří skupinu prostředků s názvem `myresourcegroup` v umístění `eastus2`.

```azurecli-interactive
az group create --name myresourcegroup --location eastus2
```

Pomocí příkazu vytvořte flexibilní server `az mysql flexible-server create` . Server může obsahovat více databází. Následující příkaz vytvoří server pomocí výchozího nastavení služby a hodnot z [místního kontextu](/cli/azure/local-context)rozhraní příkazového řádku Azure. 

```azurecli-interactive
az mysql flexible-server create
```

Vytvořený server má následující atributy: 
- Automaticky vygenerovaný název serveru, uživatelské jméno správce, heslo správce, název skupiny prostředků (Pokud už není zadané v místním kontextu) a ve stejném umístění jako vaše skupina prostředků 
- Výchozí nastavení služby pro zbývající konfigurace serveru: výpočetní vrstva (shluky), výpočetní velikost/SKU (B1MS), doba uchování zálohy (7 dní) a verze MySQL (5,7)
- Výchozí metoda připojení je privátní přístup (Integration VNet) s automaticky generovanou virtuální sítí a podsítí.

> [!NOTE] 
> Po vytvoření serveru se metoda připojení nedá změnit. Pokud jste například vybrali *privátní přístup (Integration VNET)* během vytváření, pak po vytvoření se nemůžete změnit na *veřejný přístup (povolených IP adres)* . Důrazně doporučujeme vytvořit server s privátním přístupem k zabezpečenému přístupu k serveru pomocí integrace virtuální sítě. Přečtěte si další informace o privátním přístupu v [článku koncepty](./concepts-networking.md).

Pokud chcete změnit výchozí nastavení, přečtěte si v [referenční dokumentaci](/cli/azure/mysql/flexible-server) k Azure CLI úplný seznam KONFIGUROVATELNÝCH parametrů CLI. 

Níže je uvedený ukázkový výstup: 

```json
Command group 'mysql flexible-server' is in preview. It may be changed/removed in a future release.
Creating Resource Group 'groupXXXXXXXXXX'...
Creating new vnet "serverXXXXXXXXXVNET" in resource group "groupXXXXXXXXXX"...
Creating new subnet "serverXXXXXXXXXSubnet" in resource group "groupXXXXXXXXXX" and delegating it to "Microsoft.DBforMySQL/flexibleServers"...
Creating MySQL Server 'serverXXXXXXXXX' in group 'groupXXXXXXXXXX'...
Your server 'serverXXXXXXXXX' is using sku 'Standard_B1ms' (Paid Tier). Please refer to https://aka.ms/mysql-pricing for pricing details
Creating MySQL database 'flexibleserverdb'...
Make a note of your password. If you forget, you would have to reset your password with 'az mysql flexible-server update -n serverXXXXXXXXX -g groupXXXXXXXXXX -p <new-password>'.
{
  "connectionString": "server=serverXXXXXXXXX.mysql.database.azure.com;database=flexibleserverdb;uid=secureusername;pwd=securepasswordstring",
  "databaseName": "flexibleserverdb",
  "host": "serverXXXXXXXXX.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.DBforMySQL/flexibleServers/serverXXXXXXXXX",
  "location": "East US 2",
  "password": "securepasswordstring",
  "resourceGroup": "groupXXXXXXXXXX",
  "skuname": "Standard_B1ms",
  "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.Network/virtualNetworks/serverXXXXXXXXXVNET/subnets/serverXXXXXXXXXSubnet",
  "username": "secureusername",
  "version": "5.7"
}
```

Pokud chcete změnit výchozí nastavení, přečtěte si v [referenční dokumentaci](/cli/azure/mysql/flexible-server) k Azure CLI úplný seznam KONFIGUROVATELNÝCH parametrů CLI. 

## <a name="create-a-database"></a>Vytvoření databáze
Spusťte následující příkaz, který vytvoří databázi **newdatabase** , pokud jste ji ještě nevytvořili.

```azurecli-interactive
az mysql flexible-server db create -d newdatabase
```

> [!NOTE]
> Připojení ke službě Azure Database for MySQL komunikují přes port 3306. Pokud se pokoušíte připojit z podnikové sítě, odchozí provoz přes port 3306 nemusí být povolený. V takovém případě se k serveru nemůžete připojit, dokud vaše IT oddělení neotevře port 3306.

## <a name="get-the-connection-information"></a>Získání informací o připojení

Pokud se chcete připojit k serveru, budete muset zadat informace o hostiteli a přihlašovací údaje pro přístup.

```azurecli-interactive
az mysql flexible-server show --resource-group myresourcegroup --name mydemoserver
```

Výsledek je ve formátu JSON. Poznamenejte si **fullyQualifiedDomainName** a **administratorLogin**. Níže je ukázka výstupu JSON: 

```json
{
  "administratorLogin": "myadminusername",
  "administratorLoginPassword": null,
  "delegatedSubnetArguments": {
    "subnetArmResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/mydemoserverVNET/subnets/mydemoserverSubnet"
  },
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/flexibleServers/mydemoserver",
  "location": "East US 2",
  "name": "mydemoserver",
  "publicNetworkAccess": "Disabled",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 0,
    "name": "Standard_B1ms",
    "tier": "Burstable"
  },
  "storageProfile": {
    "backupRetentionDays": 7,
    "fileStorageSkuName": "Premium_LRS",
    "storageAutogrow": "Disabled",
    "storageIops": 0,
    "storageMb": 10240
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/flexibleServers",
  "version": "5.7"
}
```

## <a name="connect-and-test-the-connection-using-azure-cli"></a>Připojení a otestování připojení pomocí Azure CLI

Azure Database for MySQL flexibilní server vám umožní připojit se k serveru MySQL pomocí příkazu Azure CLI ```az mysql flexible-server connect``` . Tento příkaz umožňuje testovat připojení k databázovému serveru, vytvořit rychlou úvodní databázi a spouštět dotazy přímo na vašem serveru, aniž byste museli instalovat mysql.exe nebo MySQL Workbench.  Můžete také použít příkaz Spustit v interaktivním režimu pro spuštění více dotazů.

Spusťte následující skript, který otestuje a ověří připojení k databázi z vývojového prostředí.

```azurecli-interactive
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```
**Příklad:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```
Pro úspěšné připojení byste měli vidět následující výstup:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Pokud selhalo připojení, zkuste Tato řešení:
- Ověřte, jestli je na klientském počítači otevřený port 3306.
- Pokud je uživatelské jméno a heslo správce serveru správné
- Pokud jste nakonfigurovali pravidlo brány firewall pro klientský počítač
- Pokud jste server nakonfigurovali pomocí privátního přístupu ve virtuální síti, ujistěte se, že je váš klientský počítač ve stejné virtuální síti.

Spuštěním následujícího příkazu spusťte jeden dotaz pomocí ```--querytext``` argumentu ```-q``` .

```azurecli-interactive
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Příklad:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```
Další informace o použití ```az mysql flexible-server connect``` příkazu najdete v dokumentaci k [připojení a dotazům](connect-azure-cli.md) .

## <a name="connect-using-mysql-command-line-client"></a>Připojení pomocí klienta příkazového řádku MySQL

Pokud jste vytvořili flexibilní Server pomocí privátního přístupu (Integration VNet), budete se muset připojit k serveru z prostředku ve stejné virtuální síti jako váš server. Můžete vytvořit virtuální počítač a přidat ho do virtuální sítě vytvořené pomocí flexibilního serveru. Další informace najdete v tématu Konfigurace [dokumentace k privátnímu přístupu](how-to-manage-virtual-network-portal.md) .

Pokud jste vytvořili flexibilní Server pomocí veřejného přístupu (povolených IP adres), můžete přidat místní IP adresu do seznamu pravidel brány firewall na serveru. Podrobné pokyny najdete v [dokumentaci k vytvoření nebo správě pravidel brány firewall](how-to-manage-firewall-portal.md) .

K připojení serveru z místního prostředí můžete použít buď [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) , nebo [MySQL Workbench](./connect-workbench.md) . Azure Database for MySQL flexibilní Server podporuje připojení klientských aplikací ke službě MySQL pomocí protokolu TLS (Transport Layer Security), dříve označovaného jako SSL (Secure Sockets Layer) (SSL). TLS je průmyslový standardní protokol, který zajišťuje šifrovaná síťová připojení mezi databázovým serverem a klientskými aplikacemi, což vám umožní dodržovat požadavky na dodržování předpisů. Pokud se chcete připojit k serveru MySQL Flexible, budete muset stáhnout [veřejný certifikát SSL](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) pro ověření certifikační autority. Další informace o připojení k šifrovaným připojením nebo zakázání protokolu SSL najdete v tématu [připojení k Azure Database for MySQL-flexibilnímu serveru s poznámkou v dokumentaci k zašifrovaným připojením](how-to-connect-tls-ssl.md) .

Následující příklad ukazuje, jak se připojit k flexibilnímu serveru pomocí rozhraní příkazového řádku MySQL. Pokud už není nainstalovaný, nainstaluje se příkaz MySQL Command-line. Budete si stahovat certifikát DigiCertGlobalRootCA potřebný pro připojení SSL. Pro vymáhání ověření certifikátu TLS/SSL použijte nastavení--SSL-Mode = požadované připojovací řetězec. Předat cestu k místnímu souboru certifikátu k parametru--SSL-CA. Nahraďte hodnoty skutečným názvem serveru a heslem.

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

Pokud jste zřídili flexibilní Server pomocí **veřejného přístupu**, můžete také použít [Azure Cloud Shell](https://shell.azure.com/bash) pro připojení k flexibilnímu serveru pomocí předem nainstalovaného klienta MySQL, jak je znázorněno níže:

Aby bylo možné použít Azure Cloud Shell k připojení k flexibilnímu serveru, bude potřeba, abyste povolili přístup k síti z Azure Cloud Shell k flexibilnímu serveru. Chcete-li to dosáhnout, můžete přejít do okna **sítě** v Azure Portal pro váš flexibilní Server MySQL a zaškrtnout políčko v části **Brána firewall** , které uvádí, "povolení veřejného přístupu z jakékoli služby Azure v rámci Azure na tento server", jak je znázorněno na následujícím snímku obrazovky, a kliknutím na Uložit nastavení zachovat.

 > :::image type="content" source="./media/quickstart-create-server-portal/allow-access-to-any-azure-service.png" alt-text="Snímek obrazovky, který ukazuje, jak Azure Cloud Shell přístup k síti MySQL flexibilnímu serveru pro konfiguraci sítě veřejného přístupu.":::
 
 
> [!NOTE]
> Zaškrtnutím **tohoto seznamu povolíte veřejný přístup z jakékoli služby Azure v rámci Azure do tohoto serveru** , který se má použít jenom pro vývoj nebo testování. Nakonfiguruje bránu firewall tak, aby povolovala připojení z IP adres přidělených libovolné službě nebo prostředku Azure, včetně připojení z předplatných ostatních zákazníků.

Klikněte na tlačítko **vyzkoušet** a spusťte Azure Cloud Shell a pomocí následujících příkazů se připojte k flexibilnímu serveru. V příkazu použijte název svého serveru, uživatelské jméno a heslo. 

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!IMPORTANT]
> Při připojování k flexibilnímu serveru pomocí Azure Cloud Shell budete muset použít parametr--SSL = true a ne--SSL-Mode = REQUIRED.
> Primárním důvodem je Azure Cloud Shell, který se dodává s předinstalovaným mysql.exe klientem z distribuce MariaDB, která vyžaduje parametr--SSL, zatímco klient MySQL od distribuce Oracle vyžaduje parametr--SSL-Mode.

Pokud se během připojování k flexibilnímu serveru, který jste použili dříve, zobrazí následující chybová zpráva, nezmeškali jste nastavení pravidla brány firewall pomocí příkazu "povolení veřejného přístupu z jakékoli služby Azure v rámci Azure na tento server" zmíněného dřív, nebo když se možnost neuloží. Zkuste prosím nastavit bránu firewall a zkuste to znovu.

Chyba 2002 (HY000): Nelze se připojit k serveru MySQL <servername> (115)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud tyto prostředky nepotřebujete pro další rychlý start nebo kurz, můžete je pomocí následujícího příkazu odstranit:

```azurecli-interactive
az group delete --name myresourcegroup
```

Pokud byste chtěli odstranit jenom nově vytvořený server, můžete spustit `az mysql server delete` příkaz.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Další kroky

>[!div class="nextstepaction"]
> [Připojení a dotazování pomocí Azure CLI](connect-azure-cli.md) 
>  [Připojení k Azure Database for MySQL-flexibilnímu serveru pomocí šifrovaných připojení](how-to-connect-tls-ssl.md) 
>  [Vytvoření webové aplikace PHP (Laravel) pomocí MySQL](tutorial-php-database-app.md)
