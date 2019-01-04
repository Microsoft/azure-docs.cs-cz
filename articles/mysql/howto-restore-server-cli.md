---
title: Zálohování a obnovení serveru ve službě Azure Database for MySQL
description: Informace o zálohování a obnovení serveru ve službě Azure Database for MySQL pomocí Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/01/2018
ms.openlocfilehash: 6033dbbe6105968e5ef5839f0cfa560ac7f70fb6
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53537779"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-cli"></a>Jak zálohovat a obnovovat server ve službě Azure Database for MySQL pomocí Azure CLI

## <a name="backup-happens-automatically"></a>Zálohování se automaticky stane
Azure Database for MySQL servery se pravidelně zálohují k povolení funkce obnovení. Pomocí této funkce může obnovení serveru a jeho databázím do starší v daném okamžiku, na novém serveru.

## <a name="prerequisites"></a>Požadavky
K dokončení této příručce s postupy, potřebujete:
- [– Azure Database for MySQL serveru a databáze](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> Tato příručka vyžaduje použití Azure CLI verze 2.0 nebo novější. K potvrzení verze příkazového řádku Azure CLI, zadejte `az --version`. Pro instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="set-backup-configuration"></a>Nastavit konfiguraci zálohování

Provedete výběr mezi konfigurací serveru pro místně redundantní zálohy nebo geograficky redundantní zálohy při vytváření serveru. 

> [!NOTE]
> Po vytvoření serveru, typ redundance, které obsahuje, není možné přepnout místně redundantní, geograficky redundantní vs.
>

Při vytváření serveru prostřednictvím `az mysql server create` příkazu `--geo-redundant-backup` parametr rozhodne vaše možnosti redundance zálohy. Pokud `Enabled`, geograficky redundantní zálohy jsou prováděny. Nebo, pokud `Disabled` místně redundantní zálohy jsou prováděny. 

Období uchování zálohy je nastavena parametrem `--backup-retention`. 

Další informace o nastavení tyto hodnoty během vytváření, najdete v článku [– Azure Database for MySQL server rychlého startu CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

Období uchování zálohy serveru můžete změnit následujícím způsobem:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

V předchozím příkladu změní období uchovávání záloh mydemoserver na 10 dnů.

Období uchování zálohy se řídí jak daleko zpět v čase, které mohou být načteny obnovení bodu v čase, protože je založen na dostupné zálohy. Obnovení k určitému bodu v čase je popsána dále v další části.

## <a name="server-point-in-time-restore"></a>Obnovení bodu v čase serveru
Server můžete obnovit k dřívějšímu bodu v čase. Obnovená data se zkopíruje na nový server a existující server je ponechán beze změny. Například pokud tabulku omylem, bude vynechána. v poledne ještě dnes můžete obnovit do doby před polednem. Potom můžete načíst chybějící tabulku a data z obnovené kopie serveru. 

K obnovení serveru, použijte rozhraní příkazového řádku Azure [az mysql server restore](/cli/azure/mysql/server#az-mysql-server-restore) příkazu.

### <a name="run-the-restore-command"></a>Spusťte příkaz restore

Při obnovování serveru, příkazového řádku Azure CLI, zadejte následující příkaz:

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

`az mysql server restore` Příkaz vyžaduje následující parametry:

| Nastavení | Navrhovaná hodnota | Popis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Skupina prostředků, které se nachází na zdrojovém serveru.  |
| jméno | mydemoserver-restored | Název nového serveru, který se vytvoří příkazem restore. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Vyberte bod v čase, chcete-li obnovit. Tato datum a čas musí být v rámci doby uchovávání záloh zdrojového serveru. Použijte formát data a času ISO8601. Například můžete použít vlastní místní časové pásmo, jako například `2018-03-13T05:59:00-08:00`. Můžete také použít formát UTC Zulu například `2018-03-13T13:59:00Z`. |
| source-server | mydemoserver | Název nebo ID zdrojového serveru, ze kterého se má provést obnovení. |

Při obnovení serveru k dřívějšímu bodu v čase, se vytvoří nový server. Původní server a jeho databázím ze zadaného bodu v čase se zkopírují na nový server.

Umístění a cenová úroveň obnoveného serveru budou stejné jako původní server. 

Po dokončení procesu obnovení, vyhledejte na nový server a ověřte, že budou data obnovena podle očekávání.

## <a name="geo-restore"></a>Geografické obnovení
Pokud jste nakonfigurovali server pro geograficky redundantní zálohy, lze vytvořit nový server ze zálohy existujícího serveru. Tento nový server lze vytvořit v libovolné oblasti Azure Database for MySQL je k dispozici.  

K vytvoření serveru pomocí geograficky redundantní zálohy, použijte rozhraní příkazového řádku Azure `az mysql server georestore` příkazu.

> [!NOTE]
> Při prvním vytvoření serveru nemusí být hned dostupné pro geografické obnovení. Může trvat několik hodin nezbytných metadat, který se má naplnit.
>

Geografické obnovení serveru příkazového řádku Azure CLI zadejte následující příkaz:

```azurecli-interactive
az mysql server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen4_8 
```
Tento příkaz vytvoří nový server volá *mydemoserver georestored* v oblasti východní USA, který bude patřit *myresourcegroup*. Je obecné účely Gen 4 server s 8 jádry VCORE. Na serveru je vytvořený z geograficky redundantní zálohy *mydemoserver*, což je také ve skupině prostředků *myresourcegroup*

Pokud chcete vytvořit nový server v jiné skupině prostředků z existujícího serveru, pak v `--source-server` parametr by kvalifikovat název serveru, jako v následujícím příkladu:

```azurecli-interactive
az mysql server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMySQL/servers/mydemoserver" --location eastus --sku-name GP_Gen4_8

```

`az mysql server georestore` Příkaz requies následující parametry:

| Nastavení | Navrhovaná hodnota | Popis  |
| --- | --- | --- |
|resource-group| myresourcegroup | Název skupiny prostředků na nový server bude patřit do.|
|jméno | mydemoserver georestored | Název nového serveru. |
|source-server | mydemoserver | Název existujícího serveru, jehož geograficky redundantní zálohy se používají. |
|location | eastus | Umístění nového serveru. |
|sku-name| GP_Gen4_8 | Tento parametr nastavuje cenové úrovně, generace výpočetních a počet virtuálních jader pro nový server. GP_Gen4_8 mapuje pro obecné účely Gen 4 server s 8 jádry VCORE.|


>[!Important]
>Při vytváření nového serveru geografické obnovení, dědí jako zdrojový server stejnou velikostí úložiště a cenovou úroveň. Tyto hodnoty nelze změnit během vytváření. Po vytvoření nového serveru je možné škálovat jeho velikost.

Po dokončení procesu obnovení, vyhledejte na nový server a ověřte, že budou data obnovena podle očekávání.

## <a name="next-steps"></a>Další postup
- Další informace týkající se služby [zálohy](concepts-backup.md).
- Další informace o [kontinuita podnikových procesů](concepts-business-continuity.md) možnosti.
