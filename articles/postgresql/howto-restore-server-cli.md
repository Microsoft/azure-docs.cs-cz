---
title: Zálohování a obnovení – Azure CLI – Azure Database for PostgreSQL – jeden server
description: Naučte se nastavovat konfigurace zálohování a obnovovat Server v Azure Database for PostgreSQL-Single server pomocí Azure CLI.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/25/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: ef397eb67c1f60c14fb36bf455236d84b730f611
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94659569"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Postup zálohování a obnovení serveru na serveru Azure Database for PostgreSQL pomocí Azure CLI

Azure Database for PostgreSQL servery se pravidelně zálohují, aby se povolily funkce obnovení. Pomocí této funkce můžete obnovit server a všechny jeho databáze k dřívějšímu bodu v čase na novém serveru.

## <a name="prerequisites"></a>Požadavky
Postup dokončení této příručky:

- Potřebujete [Azure Database for PostgreSQL Server a databázi](quickstart-create-server-database-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

 - Tento článek vyžaduje Azure CLI verze 2,0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="set-backup-configuration"></a>Nastavení konfigurace zálohování

Můžete vybrat konfiguraci serveru pro buď místně redundantní zálohy, nebo geograficky redundantní zálohy při vytváření serveru. 

> [!NOTE]
> Po vytvoření serveru se nedá přepnout druh redundance, který je geograficky redundantní vs místně redundantní.
>

Při vytváření serveru prostřednictvím příkazu se `az postgres server create` `--geo-redundant-backup` rozhodne možnost redundance zálohy. Pokud `Enabled` jsou pořízeny geograficky redundantní zálohy. Nebo pokud `Disabled` jsou pořízeny místně redundantní zálohy. 

Doba uchovávání záloh je nastavená parametrem `--backup-retention-days` . 

Další informace o nastavení těchto hodnot během vytváření najdete v tématu [rychlý Start pro Azure Database for PostgreSQL Server CLI](quickstart-create-server-database-azure-cli.md).

Dobu uchovávání záloh serveru lze změnit následujícím způsobem:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

Předchozí příklad změní dobu uchovávání záloh mydemoserver na 10 dní.

Doba uchovávání záloh určuje, jak daleko se obnovení k určitému bodu v čase dá načíst, protože je založené na dostupných zálohách. Obnovení k bodu v čase je popsáno dále v následující části.

## <a name="server-point-in-time-restore"></a>Obnovení k časovému okamžiku serveru
Server můžete obnovit k předchozímu bodu v čase. Obnovená data se zkopírují na nový server a stávající server zůstane tak, jak je. Například pokud je tabulka omylem vyřazena v poledne ještě dnes, můžete ji obnovit do času těsně před poledne. Pak můžete načíst chybějící tabulku a data z obnovené kopie serveru. 

K obnovení serveru použijte příkaz Azure CLI [AZ Postgres Server Restore](/cli/azure/postgres/server) .

### <a name="run-the-restore-command"></a>Spuštění příkazu RESTORE

Server obnovíte tak, že na příkazovém řádku Azure CLI zadáte tento příkaz:

```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

`az postgres server restore`Příkaz vyžaduje následující parametry:

| Nastavení | Navrhovaná hodnota | Popis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Skupina prostředků, ve které existuje zdrojový server.  |
| name | mydemoserver-restored | Název nového serveru, který se vytvoří příkazem restore. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Vyberte bod v čase, který chcete obnovit. Tato datum a čas musí být v rámci doby uchovávání záloh zdrojového serveru. Použijte formát data a času ISO8601. Můžete například použít vlastní místní časové pásmo, například `2018-03-13T05:59:00-08:00` . Můžete použít také formát UTC Zulu, například `2018-03-13T13:59:00Z` . |
| source-server | mydemoserver | Název nebo ID zdrojového serveru, ze kterého se má provést obnovení. |

Když obnovíte Server do dřívějšího bodu v čase, vytvoří se nový server. Původní server a jeho databáze ze zadaného bodu v čase se zkopírují na nový server.

Hodnoty umístění a cenové úrovně obnoveného serveru zůstanou stejné jako u původního serveru. 

Po dokončení procesu obnovení Najděte nový server a ověřte, že se data obnovila podle očekávání. Nový server má stejné přihlašovací jméno a heslo správce serveru, které bylo platné pro existující server v době zahájení obnovení. Heslo lze změnit na stránce **Přehled** nového serveru.

Nový server vytvořený během obnovování neobsahuje pravidla brány firewall nebo koncové body služeb virtuální sítě, které byly na původním serveru. Tato pravidla je potřeba pro tento nový server nastavit samostatně.

## <a name="geo-restore"></a>Geografické obnovení
Pokud jste server nakonfigurovali pro geograficky redundantní zálohy, můžete vytvořit nový server ze zálohy stávajícího serveru. Tento nový server se dá vytvořit v libovolné oblasti, kterou Azure Database for PostgreSQL k dispozici.  

Pokud chcete vytvořit server pomocí geograficky redundantní zálohy, použijte příkaz Azure CLI `az postgres server georestore` .

> [!NOTE]
> Při prvním vytvoření serveru nemusí být pro geografickou obnovu k dispozici okamžitě. Naplnění potřebných metadat může trvat několik hodin.
>

Pokud chcete server geograficky obnovit, zadejte na příkazovém řádku Azure CLI tento příkaz:

```azurecli-interactive
az postgres server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen4_8 
```
Tento příkaz vytvoří nový server s názvem *mydemoserver-geograficky obnovený* v východní USA, který bude patřit do *myresourcegroup*. Je to Pro obecné účely se serverem Gen 4 s 8 virtuální jádra. Server se vytvoří z geograficky redundantní zálohy *mydemoserver*, která je také ve skupině prostředků *myresourcegroup*

Pokud chcete vytvořit nový server v jiné skupině prostředků z existujícího serveru, pak v `--source-server` parametru, který jste zaznamenali název serveru, jako v následujícím příkladu:

```azurecli-interactive
az postgres server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver" --location eastus --sku-name GP_Gen4_8

```

`az postgres server georestore`Příkaz vyžaduje následující parametry:

| Nastavení | Navrhovaná hodnota | Popis  |
| --- | --- | --- |
|resource-group| myresourcegroup | Název skupiny prostředků, do které bude nový server patřit|
|name | mydemoserver – geograficky obnovené | Název nového serveru. |
|source-server | mydemoserver | Název existujícího serveru, jehož geograficky redundantní zálohy jsou používány. |
|location | eastus | Umístění nového serveru. |
|sku-name| GP_Gen4_8 | Tento parametr nastaví cenovou úroveň, generaci výpočtů a počet virtuální jádra nového serveru. GP_Gen4_8 se mapuje na server s Pro obecné účely a gen 4 s 8 virtuální jádra.|

Při vytváření nového serveru geografickým obnovením se zdědí stejná velikost úložiště a cenová úroveň jako na zdrojovém serveru. Tyto hodnoty nelze během vytváření změnit. Po vytvoření nového serveru se velikost úložiště dá škálovat.

Po dokončení procesu obnovení Najděte nový server a ověřte, že se data obnovila podle očekávání. Nový server má stejné přihlašovací jméno a heslo správce serveru, které bylo platné pro existující server v době zahájení obnovení. Heslo lze změnit na stránce **Přehled** nového serveru.

Nový server vytvořený během obnovování neobsahuje pravidla brány firewall nebo koncové body služeb virtuální sítě, které byly na původním serveru. Tato pravidla je potřeba pro tento nový server nastavit samostatně.

## <a name="next-steps"></a>Další kroky
- Další informace o [zálohování](concepts-backup.md) služby
- Další informace o [replikách](concepts-read-replicas.md)
- Další informace o možnostech [kontinuity podnikových aplikací](concepts-business-continuity.md)
