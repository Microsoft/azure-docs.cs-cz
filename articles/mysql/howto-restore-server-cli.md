---
title: Postup zálohování a obnovení serveru se ve službě Azure Database pro databázi MySQL
description: Informace o zálohování a obnovení serveru se ve službě Azure Database pro databázi MySQL pomocí rozhraní příkazového řádku Azure.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 04/01/2018
ms.openlocfilehash: 5e8ac9da4df2428191e8a7e6402f2ac06e695503
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-cli"></a>Postup zálohování a obnovení serveru ve službě Azure Database pro databázi MySQL pomocí rozhraní příkazového řádku Azure

## <a name="backup-happens-automatically"></a>Zálohování se automaticky stane
Azure databáze MySQL servery jsou pravidelně zálohovány k povolení funkce obnovení. Pomocí této funkce můžete obnovit na server a všechny jeho databáze do starší bodu v čase, na nový server.

## <a name="prerequisites"></a>Požadavky
Chcete-li provést tento postup průvodce, je třeba:
- [Azure databáze MySQL server a databáze](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> Postupy: Průvodce vyžaduje, že používáte Azure CLI verze 2.0 nebo novější. Chcete-li ověřit verzi příkazového řádku Azure CLI zadejte `az --version`. K instalaci nebo upgradu, najdete v části [nainstalovat Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="add-the-extension"></a>Přidání rozšíření
Přidejte aktualizované rozšíření pro správu služby Azure Database for MySQL pomocí následujícího příkazu:
```azurecli-interactive
az extension add --name rdbms
``` 

Zkontrolujte, že máte nainstalovanou správnou verzi rozšíření. 
```azurecli-interactive
az extension list
```

Vrácený kód JSON by měl obsahovat následující: 
```json
{
    "extensionType": "whl",
    "name": "rdbms",
    "version": "0.0.5"
}
```

Pokud se nevrátí verze 0.0.5, spuštěním následujícího příkazu rozšíření aktualizujte: 
```azurecli-interactive
az extension update --name rdbms
```


## <a name="set-backup-configuration"></a>Konfigurace zálohování sady

Volba mezi konfigurace vašeho serveru pro místně redundantní zálohy nebo geograficky redundantní zálohy při vytváření serveru provedete. 

> [!NOTE]
> Po vytvoření serveru druh redundance, kterou má, nelze přepnout místně redundantní geograficky redundantní vs.
>

Při vytváření serveru prostřednictvím `az mysql server create` příkaz, `--geo-redundant-backup` parametr rozhodne svoji možnost redundance zálohování. Pokud `Enabled`, jsou provedeny geograficky redundantní zálohy. Nebo, pokud `Disabled` jsou provedeny místně redundantní zálohy. 

Doba uchovávání záloh je nastavena parametrem `--backup-retention-days`. 

Další informace o nastavení tyto hodnoty během vytváření najdete v tématu [Azure databáze MySQL server rychlý start rozhraní příkazového řádku pro](quickstart-create-mysql-server-database-using-azure-cli.md).

Doba uchovávání záloh serveru lze změnit takto:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --backup-retention-days 10
```

V předchozím příkladu změny období uchovávání záloh mydemoserver 10 dní.

Doba uchovávání záloh řídí jak daleko zpět v době obnovení bodu v čase se dá načíst, protože je založena na zálohování, které jsou k dispozici. Obnovení bodu v čase je popsána dále v další části.

## <a name="server-point-in-time-restore"></a>Obnovení bodu v čase serveru
Server můžete obnovit do předchozího bodu v čase. Obnovená data se zkopíruje na nový server a existující server je ponechán beze. Například pokud tabulka omylem, bude vynechána. v poledne dnes můžete obnovit na čas těsně před polednem. Potom můžete načíst chybějící tabulku a dat z obnovené kopie serveru. 

K obnovení serveru, použijte rozhraní příkazového řádku Azure [obnovení serveru mysql az](/cli/azure/mysql/server#az_mysql_server_restore) příkaz.

### <a name="run-the-restore-command"></a>Spusťte příkaz restore

K obnovení serveru, na příkazovém řádku Azure CLI, zadejte následující příkaz:

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

`az mysql server restore` Příkaz vyžaduje následující parametry:
| Nastavení | Navrhovaná hodnota | Popis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Skupinu prostředků, kde existuje na zdrojovém serveru.  |
| jméno | mydemoserver-restored | Název nového serveru, který se vytvoří příkazem restore. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Vyberte bod v čase k obnovení. Tato datum a čas musí být v rámci doby uchovávání záloh zdrojového serveru. Použijte formát ISO8601 data a času. Například můžete použít vlastní místní časové pásmo, jako například `2018-03-13T05:59:00-08:00`. Můžete také použít formát zulština UTC, například `2018-03-13T13:59:00Z`. |
| source-server | mydemoserver | Název nebo ID zdrojového serveru, ze kterého se má provést obnovení. |

Při obnovení serveru do dřívějšího bodu v čase, se vytvoří nový server. Původní server a její databáze ze zadaného bodu v čase se zkopírují na nový server.

Umístění a cenovou úroveň hodnoty pro obnovené server zůstat stejné jako původní server. 

Po dokončení procesu obnovení, vyhledejte na nový server a ověřte, že budou data obnovena podle očekávání.

## <a name="geo-restore"></a>Geografické obnovení
Pokud jste nakonfigurovali server pro geograficky redundantní zálohy, lze vytvořit nový server ze zálohy tento existující server. Tento nový server lze vytvořit v libovolné oblasti, že je k dispozici Azure Database pro databázi MySQL.  

K vytvoření serveru pomocí geograficky redundantní zálohy, použijte rozhraní příkazového řádku Azure `az mysql server georestore` příkaz.

> [!NOTE]
> Při prvním vytvoření serveru nemusí být okamžitě k dispozici pro geografické obnovení. Může trvat několik hodin, než nezbytné metadata vyplnit.
>

Chcete-li geografické obnovení serveru, na příkazovém řádku Azure CLI zadejte následující příkaz:

```azurecli-interactive
az mysql server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen4_8 
```
Tento příkaz vytvoří nový server volá *mydemoserver georestored* v oblasti Východ USA, který bude patřit *myresourcegroup*. Je obecné účely, server Gen 4 s 8 vCores. Server je vytvořen z geograficky redundantní zálohy *mydemoserver*, což je také ve skupině prostředků *myresourcegroup*

Pokud chcete vytvořit nový server v jiné skupině prostředků ze stávajícího serveru, pak v `--source-server` parametr vyberete název serveru jako v následujícím příkladu:

```azurecli-interactive
az mysql server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMySQL/servers/mydemoserver" --location eastus --sku-name GP_Gen4_8

```

`az mysql server georestore` Příkaz requies následující parametry:
| Nastavení | Navrhovaná hodnota | Popis  |
| --- | --- | --- |
|resource-group| myresourcegroup | Název skupiny prostředků na nový server bude patřit do.|
|jméno | mydemoserver georestored | Název nového serveru. |
|source-server | mydemoserver | Název existující server, jehož geograficky redundantní zálohy se používají. |
|location | eastus | Umístění nového serveru. |
|Název SKU| GP_Gen4_8 | Tento parametr nastavuje cenovou úroveň, výpočetní generování a počet vCores nového serveru. GP_Gen4_8 mapuje obecné účely, server Gen 4 s 8 vCores.|


>[!Important]
>Při vytváření nového serveru geograficky obnovením, zdědí stejnou velikost úložiště a cenovou úroveň jako zdrojový server. Tyto hodnoty nelze změnit během vytváření. Po vytvoření nového serveru, je možné škálovat jeho velikost úložiště.

Po dokončení procesu obnovení, vyhledejte na nový server a ověřte, že budou data obnovena podle očekávání.

## <a name="next-steps"></a>Další postup
- Další informace o službě service [zálohování](concepts-backup.md).
- Další informace o [kontinuity podnikových procesů](concepts-business-continuity.md) možnosti.
