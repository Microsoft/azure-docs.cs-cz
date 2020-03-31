---
title: Zálohování a obnovení – rozhraní příkazového řádku Azure – databáze Azure pro PostgreSQL – jeden server
description: Zjistěte, jak nastavit konfigurace zálohování a obnovit server v Azure Database for PostgreSQL – Single Server pomocí rozhraní příkazového řádku Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: f0ea24133d7b6acdc4b099ee21a8711a2d99095d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74775687"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Jak zálohovat a obnovit server v Azure Database for PostgreSQL – jeden server pomocí azure cli

Azure Database pro postgreSQL servery jsou pravidelně zálohovány, aby povolit funkce obnovení. Pomocí této funkce můžete obnovit server a všechny jeho databáze na dřívější bod v čase, na novém serveru.

## <a name="prerequisites"></a>Požadavky
Chcete-li dokončit tento návod, potřebujete:
- [Databáze Azure pro postgreSQL server a databázi](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> Tento návod vyžaduje, abyste používali Azure CLI verze 2.0 nebo novější. Chcete-li verzi potvrdit, zadejte `az --version`na příkazovém řádku příkazového řádku Azure . Informace o instalaci nebo upgradu najdete [v tématu Instalace příkazového příkazového příkazu k webu Azure]( /cli/azure/install-azure-cli).

## <a name="set-backup-configuration"></a>Nastavit konfiguraci zálohování

Můžete si vybrat mezi konfigurací serveru pro místně redundantní zálohy nebo geograficky redundantní zálohy při vytváření serveru. 

> [!NOTE]
> Po vytvoření serveru nelze přepnout druh redundance, kterou má, geograficky redundantní vs místně redundantní.
>

Při vytváření serveru `az postgres server create` pomocí příkazu `--geo-redundant-backup` parametr rozhodne o možnosti redundance zálohování. Pokud `Enabled`jsou přijata geograficky redundantní zálohy. Nebo `Disabled` pokud jsou převzaty místně redundantní zálohy. 

Doba uchování zálohy je `--backup-retention-days`nastavena parametrem . 

Další informace o nastavení těchto hodnot během vytváření naleznete v [databázi Azure pro postgresql server CLI Quickstart](quickstart-create-server-database-azure-cli.md).

Dobu uchování záloh y serveru lze změnit následujícím způsobem:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

Předchozí příklad změní dobu uchování zálohy mydemoserver na 10 dní.

Doba uchovávání záloh určuje, jak daleko zpět v čase lze načíst obnovení bodu v čase, protože je založen na zálohování k dispozici. Obnovení bodu v čase je popsáno dále v další části.

## <a name="server-point-in-time-restore"></a>Obnovení bodu v čase serveru
Server můžete obnovit do předchozího bodu v čase. Obnovená data jsou zkopírována na nový server a existující server zůstane tak, jak je. Pokud je například tabulka dnes v poledne omylem vynechána, můžete obnovit čas těsně před polednem. Potom můžete načíst chybějící tabulku a data z obnovené kopie serveru. 

Chcete-li server obnovit, použijte příkaz obnovení serveru Azure CLI [az postgres.](/cli/azure/postgres/server)

### <a name="run-the-restore-command"></a>Spuštění příkazu obnovit

Chcete-li server obnovit, zadejte na příkazovém řádku Azure CLI následující příkaz:

```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

Příkaz `az postgres server restore` vyžaduje následující parametry:

| Nastavení | Navrhovaná hodnota | Popis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Skupina prostředků, kde zdrojový server existuje.  |
| jméno | mydemoserver-restored | Název nového serveru, který se vytvoří příkazem restore. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Vyberte bod v čase, do který chcete obnovit. Tato datum a čas musí být v rámci doby uchovávání záloh zdrojového serveru. Použijte formát data a času ISO8601. Můžete například použít vlastní místní časové pásmo, například `2018-03-13T05:59:00-08:00`. Můžete také použít formát UTC Zulu, `2018-03-13T13:59:00Z`například . |
| source-server | mydemoserver | Název nebo ID zdrojového serveru, ze kterého se má provést obnovení. |

Při obnovení serveru do dřívějšího bodu v čase je vytvořen nový server. Původní server a jeho databáze od zadaného bodu v čase jsou zkopírovány do nového serveru.

Hodnoty umístění a cenové úrovně pro obnovený server zůstávají stejné jako původní server. 

Po dokončení procesu obnovení vyhledejte nový server a ověřte, zda jsou data obnovena podle očekávání. Nový server má stejné přihlašovací jméno správce serveru a heslo, které bylo platné pro existující server v době, kdy bylo obnovení zahájeno. Heslo lze změnit ze stránky **Přehled** nového serveru.

Nový server vytvořený během obnovení nemá pravidla brány firewall nebo koncové body služby Virtuální síť, které existovaly na původním serveru. Tato pravidla je třeba nastavit samostatně pro tento nový server.

## <a name="geo-restore"></a>Geografické obnovení
Pokud jste server nakonfigurovali pro geograficky redundantní zálohy, lze ze zálohy tohoto existujícího serveru vytvořit nový server. Tento nový server lze vytvořit v libovolné oblasti, která Azure Database pro PostgreSQL je k dispozici.  

Chcete-li vytvořit server pomocí geograficky redundantní `az postgres server georestore` zálohy, použijte příkaz Azure CLI.

> [!NOTE]
> Při prvním vytvoření serveru nemusí být okamžitě k dispozici pro geografické obnovení. Může trvat několik hodin, než budou naplněna potřebná metadata.
>

Chcete-li server geograficky obnovit, zadejte na příkazovém řádku Azure CLI následující příkaz:

```azurecli-interactive
az postgres server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen4_8 
```
Tento příkaz vytvoří nový server s názvem *mydemoserver-georestored* v usa – východ, který bude patřit do *skupiny myresourcegroup*. Jedná se o obecný účel, Gen 4 server s 8 virtuálními jádry. Server je vytvořen z geograficky redundantní zálohy *mydemoserver*, který je také ve skupině prostředků *myresourcegroup*

Pokud chcete vytvořit nový server v jiné skupině prostředků než existující `--source-server` server, pak v parametru byste kvalifikovali název serveru jako v následujícím příkladu:

```azurecli-interactive
az postgres server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver" --location eastus --sku-name GP_Gen4_8

```

Příkaz `az postgres server georestore` vyžaduje následující parametry:

| Nastavení | Navrhovaná hodnota | Popis  |
| --- | --- | --- |
|resource-group| myresourcegroup | Název skupiny prostředků, do které bude nový server patřit.|
|jméno | mydemoserver-georestored | Název nového serveru. |
|source-server | mydemoserver | Název existujícího serveru, jehož geograficky redundantní zálohy se používají. |
|location | eastus | Umístění nového serveru. |
|sku-name| GP_Gen4_8 | Tento parametr nastaví cenovou úroveň, generování výpočetních prostředků a počet virtuálních jader nového serveru. GP_Gen4_8 mapy na general purpose, Gen 4 server s 8 virtuálními jádry.|

Při vytváření nového serveru geografickým obnovením zdědí stejnou velikost úložiště a cenovou úroveň jako zdrojový server. Tyto hodnoty nelze během vytváření změnit. Po vytvoření nového serveru lze zvětšit velikost úložiště.

Po dokončení procesu obnovení vyhledejte nový server a ověřte, zda jsou data obnovena podle očekávání. Nový server má stejné přihlašovací jméno správce serveru a heslo, které bylo platné pro existující server v době, kdy bylo obnovení zahájeno. Heslo lze změnit ze stránky **Přehled** nového serveru.

Nový server vytvořený během obnovení nemá pravidla brány firewall nebo koncové body služby Virtuální síť, které existovaly na původním serveru. Tato pravidla je třeba nastavit samostatně pro tento nový server.

## <a name="next-steps"></a>Další kroky
- Další informace o [zálohách služby](concepts-backup.md)
- Další informace o [replikách](concepts-read-replicas.md)
- Další informace o možnostech [kontinuity provozu](concepts-business-continuity.md)
