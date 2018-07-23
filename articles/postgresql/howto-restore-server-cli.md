---
title: Zálohování a obnovení serveru ve službě Azure Database for PostgreSQL | Dokumentace Microsoftu
description: Informace o zálohování a obnovení serveru ve službě Azure Database for PostgreSQL pomocí rozhraní příkazového řádku Azure.
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 04/01/2018
ms.openlocfilehash: de0d6ee32380367bfba4a27958c9c1e739b5dba3
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173422"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-cli"></a>Jak zálohovat a obnovovat server ve službě Azure Database for PostgreSQL pomocí Azure CLI

## <a name="backup-happens-automatically"></a>Zálohování se automaticky stane
Azure Database for PostgreSQL servery se pravidelně zálohují k povolení funkce obnovení. Pomocí této funkce může obnovení serveru a jeho databázím do starší v daném okamžiku, na novém serveru.

## <a name="prerequisites"></a>Požadavky
K dokončení této příručce s postupy, potřebujete:
- [– Azure Database for PostgreSQL server a databáze](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> Tato příručka vyžaduje použití Azure CLI verze 2.0 nebo novější. K potvrzení verze příkazového řádku Azure CLI, zadejte `az --version`. Pro instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="set-backup-configuration"></a>Nastavit konfiguraci zálohování

Provedete výběr mezi konfigurací serveru pro místně redundantní zálohy nebo geograficky redundantní zálohy při vytváření serveru. 

> [!NOTE]
> Po vytvoření serveru, typ redundance, které obsahuje, není možné přepnout místně redundantní, geograficky redundantní vs.
>

Při vytváření serveru prostřednictvím `az postgres server create` příkazu `--geo-redundant-backup` parametr rozhodne vaše možnosti redundance zálohy. Pokud `Enabled`, geograficky redundantní zálohy jsou prováděny. Nebo, pokud `Disabled` místně redundantní zálohy jsou prováděny. 

Období uchování zálohy je nastavena parametrem `--backup-retention-days`. 

Další informace o nastavení tyto hodnoty během vytváření, najdete v článku [– Azure Database for PostgreSQL server rychlého startu CLI](quickstart-create-server-database-azure-cli.md).

Období uchování zálohy serveru můžete změnit následujícím způsobem:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --backup-retention-days 10
```

V předchozím příkladu změní období uchovávání záloh mydemoserver na 10 dnů.

Období uchování zálohy se řídí jak daleko zpět v čase, které mohou být načteny obnovení bodu v čase, protože je založen na dostupné zálohy. Obnovení k určitému bodu v čase je popsána dále v další části.

## <a name="server-point-in-time-restore"></a>Obnovení bodu v čase serveru
Server můžete obnovit k dřívějšímu bodu v čase. Obnovená data se zkopíruje na nový server a existující server je ponechán beze změny. Například pokud tabulku omylem, bude vynechána. v poledne ještě dnes můžete obnovit do doby před polednem. Potom můžete načíst chybějící tabulku a data z obnovené kopie serveru. 

K obnovení serveru, použijte rozhraní příkazového řádku Azure [az postgres server restore](/cli/azure/postgres/server#az_postgres_server_restore) příkazu.

### <a name="run-the-restore-command"></a>Spusťte příkaz restore

Při obnovování serveru, příkazového řádku Azure CLI, zadejte následující příkaz:

```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

`az postgres server restore` Příkaz vyžaduje následující parametry:
| Nastavení | Navrhovaná hodnota | Popis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Skupina prostředků, které se nachází na zdrojovém serveru.  |
| jméno | mydemoserver-restored | Název nového serveru, který se vytvoří příkazem restore. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Vyberte bod v čase, chcete-li obnovit. Tato datum a čas musí být v rámci doby uchovávání záloh zdrojového serveru. Použijte formát data a času ISO8601. Například můžete použít vlastní místní časové pásmo, jako například `2018-03-13T05:59:00-08:00`. Můžete také použít formát UTC Zulu například `2018-03-13T13:59:00Z`. |
| source-server | mydemoserver | Název nebo ID zdrojového serveru, ze kterého se má provést obnovení. |

Při obnovení serveru k dřívějšímu bodu v čase, se vytvoří nový server. Původní server a jeho databázím ze zadaného bodu v čase se zkopírují na nový server.

Umístění a cenová úroveň obnoveného serveru budou stejné jako původní server. 

Po dokončení procesu obnovení, vyhledejte na nový server a ověřte, že budou data obnovena podle očekávání.

Nový server vytvořené během obnovení nemá pravidla brány firewall, které existovaly na původním serveru. Pravidla brány firewall musí nastavit zvlášť pro tento nový server.

## <a name="geo-restore"></a>Geografické obnovení
Pokud jste nakonfigurovali server pro geograficky redundantní zálohy, lze vytvořit nový server ze zálohy existujícího serveru. Tento nový server lze vytvořit v libovolné oblasti Azure Database for PostgreSQL je k dispozici.  

K vytvoření serveru pomocí geograficky redundantní zálohy, použijte rozhraní příkazového řádku Azure `az postgres server georestore` příkazu.

> [!NOTE]
> Při prvním vytvoření serveru nemusí být hned dostupné pro geografické obnovení. Může trvat několik hodin nezbytných metadat, který se má naplnit.
>

Geografické obnovení serveru příkazového řádku Azure CLI zadejte následující příkaz:

```azurecli-interactive
az postgres server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen4_8 
```
Tento příkaz vytvoří nový server volá *mydemoserver georestored* v oblasti východní USA, který bude patřit *myresourcegroup*. Je obecné účely Gen 4 server s 8 jádry VCORE. Na serveru je vytvořený z geograficky redundantní zálohy *mydemoserver*, což je také ve skupině prostředků *myresourcegroup*

Pokud chcete vytvořit nový server v jiné skupině prostředků z existujícího serveru, pak v `--source-server` parametr by kvalifikovat název serveru, jako v následujícím příkladu:

```azurecli-interactive
az postgres server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver" --location eastus --sku-name GP_Gen4_8

```

`az postgres server georestore` Příkaz requies následující parametry:
| Nastavení | Navrhovaná hodnota | Popis  |
| --- | --- | --- |
|resource-group| myresourcegroup | Název skupiny prostředků na nový server bude patřit do.|
|jméno | mydemoserver georestored | Název nového serveru. |
|source-server | mydemoserver | Název existujícího serveru, jehož geograficky redundantní zálohy se používají. |
|location | eastus | Umístění nového serveru. |
|Název skladové položky| GP_Gen4_8 | Tento parametr nastavuje cenové úrovně, generace výpočetních a počet virtuálních jader pro nový server. GP_Gen4_8 mapuje pro obecné účely Gen 4 server s 8 jádry VCORE.|


>[!Important]
>Při vytváření nového serveru geografické obnovení, dědí jako zdrojový server stejnou velikostí úložiště a cenovou úroveň. Tyto hodnoty nelze změnit během vytváření. Po vytvoření nového serveru je možné škálovat jeho velikost.

Po dokončení procesu obnovení, vyhledejte na nový server a ověřte, že budou data obnovena podle očekávání.

Nový server vytvořené během obnovení nemá pravidla brány firewall, které existovaly na původním serveru. Pravidla brány firewall musí nastavit zvlášť pro tento nový server.

## <a name="next-steps"></a>Další postup
- Další informace týkající se služby [zálohy](concepts-backup.md).
- Další informace o [kontinuita podnikových procesů](concepts-business-continuity.md) možnosti.
