---
title: Zálohování a obnovení – Azure CLI – Azure Database for MariaDB
description: Naučte se zálohovat a obnovovat Server v Azure Database for MariaDB pomocí Azure CLI.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: a6e46efd7f998437c3998df9a989ef9e1500e888
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664830"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mariadb-using-the-azure-cli"></a>Postup zálohování a obnovení serveru v Azure Database for MariaDB pomocí rozhraní příkazového řádku Azure

Azure Database for MariaDB servery se pravidelně zálohují, aby se povolily funkce obnovení. Pomocí této funkce můžete obnovit server a všechny jeho databáze k dřívějšímu bodu v čase na novém serveru.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- Potřebujete [Azure Database for MariaDB Server a databázi](quickstart-create-mariadb-server-database-using-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Tento průvodce návodem vyžaduje verzi 2,0 nebo novější v rozhraní příkazového řádku Azure CLI. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="set-backup-configuration"></a>Nastavení konfigurace zálohování

Můžete vybrat konfiguraci serveru pro buď místně redundantní zálohy, nebo geograficky redundantní zálohy při vytváření serveru.

> [!NOTE]
> Po vytvoření serveru se nedá přepnout druh redundance, který je geograficky redundantní vs místně redundantní.
>

Při vytváření serveru prostřednictvím příkazu se `az mariadb server create` `--geo-redundant-backup` rozhodne možnost redundance zálohy. Pokud `Enabled` jsou pořízeny geograficky redundantní zálohy. Nebo pokud `Disabled` jsou pořízeny místně redundantní zálohy.

Doba uchovávání záloh je nastavená parametrem `--backup-retention` .

Další informace o nastavení těchto hodnot během vytváření najdete v tématu [rychlý Start pro Azure Database for MariaDB Server CLI](quickstart-create-mariadb-server-database-using-azure-cli.md).

Dobu uchovávání záloh serveru lze změnit následujícím způsobem:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

Předchozí příklad změní dobu uchovávání záloh mydemoserver na 10 dní.

Doba uchovávání záloh určuje, jak daleko se obnovení k určitému bodu v čase dá načíst, protože je založené na dostupných zálohách. Obnovení k bodu v čase je popsáno dále v následující části.

## <a name="server-point-in-time-restore"></a>Obnovení k časovému okamžiku serveru

Server můžete obnovit k předchozímu bodu v čase. Obnovená data se zkopírují na nový server a stávající server zůstane tak, jak je. Například pokud je tabulka omylem vyřazena v poledne ještě dnes, můžete ji obnovit do času těsně před poledne. Pak můžete načíst chybějící tabulku a data z obnovené kopie serveru.

K obnovení serveru použijte příkaz Azure CLI [AZ MariaDB Server Restore](/cli/azure/mariadb/server#az-mariadb-server-restore) .

### <a name="run-the-restore-command"></a>Spuštění příkazu RESTORE

Server obnovíte tak, že na příkazovém řádku Azure CLI zadáte tento příkaz:

```azurecli-interactive
az mariadb server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

`az mariadb server restore`Příkaz vyžaduje následující parametry:

| Nastavení | Navrhovaná hodnota | Popis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Skupina prostředků, ve které existuje zdrojový server.  |
| name | mydemoserver-restored | Název nového serveru, který se vytvoří příkazem restore. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Vyberte bod v čase, který chcete obnovit. Tato datum a čas musí být v rámci doby uchovávání záloh zdrojového serveru. Použijte formát data a času ISO8601. Můžete například použít vlastní místní časové pásmo, například `2018-03-13T05:59:00-08:00` . Můžete použít také formát UTC Zulu, například `2018-03-13T13:59:00Z` . |
| source-server | mydemoserver | Název nebo ID zdrojového serveru, ze kterého se má provést obnovení. |

WWhen obnovíte Server k dřívějšímu bodu v čase, vytvoří se nový server. Původní server a jeho databáze ze zadaného bodu v čase se zkopírují na nový server.

Hodnoty umístění a cenové úrovně obnoveného serveru zůstanou stejné jako u původního serveru. 

Po dokončení procesu obnovení Najděte nový server a ověřte, že se data obnovila podle očekávání. Nový server má stejné přihlašovací jméno a heslo správce serveru, které bylo platné pro existující server v době zahájení obnovení. Heslo lze změnit na stránce **Přehled** nového serveru.

Nový server vytvořený během obnovy nemá koncové body služby virtuální sítě, které existovaly na původním serveru. Tato pravidla je potřeba pro tento nový server nastavit samostatně. Obnoví se pravidla brány firewall z původního serveru.

## <a name="geo-restore"></a>Geografické obnovení

Pokud jste server nakonfigurovali pro geograficky redundantní zálohy, můžete vytvořit nový server ze zálohy stávajícího serveru. Tento nový server se dá vytvořit v libovolné oblasti, kterou Azure Database for MariaDB k dispozici.  

Pokud chcete vytvořit server pomocí geograficky redundantní zálohy, použijte příkaz Azure CLI `az mariadb server georestore` .

> [!NOTE]
> Při prvním vytvoření serveru nemusí být pro geografickou obnovu k dispozici okamžitě. Naplnění potřebných metadat může trvat několik hodin.
>

Pokud chcete server geograficky obnovit, zadejte na příkazovém řádku Azure CLI tento příkaz:

```azurecli-interactive
az mariadb server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen5_8
```

Tento příkaz vytvoří nový server s názvem *mydemoserver-geograficky obnovený* v východní USA, který bude patřit do *myresourcegroup*. Je to Pro obecné účelyý Server Gen 5 s 8 virtuální jádra. Server se vytvoří z geograficky redundantní zálohy *mydemoserver*, která je také ve skupině prostředků *myresourcegroup*

Pokud chcete vytvořit nový server v jiné skupině prostředků z existujícího serveru, pak v `--source-server` parametru, který jste zaznamenali název serveru, jako v následujícím příkladu:

```azurecli-interactive
az mariadb server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMariaDB/servers/mydemoserver" --location eastus --sku-name GP_Gen5_8

```

`az mariadb server georestore`Příkaz vyžaduje následující parametry:

| Nastavení | Navrhovaná hodnota | Popis  |
| --- | --- | --- |
|resource-group| myresourcegroup | Název skupiny prostředků, do které bude nový server patřit|
|name | mydemoserver – geograficky obnovené | Název nového serveru. |
|source-server | mydemoserver | Název existujícího serveru, jehož geograficky redundantní zálohy jsou používány. |
|location | eastus | Umístění nového serveru. |
|sku-name| GP_Gen5_8 | Tento parametr nastaví cenovou úroveň, generaci výpočtů a počet virtuální jádra nového serveru. GP_Gen5_8 se mapuje na Pro obecné účely, Gen 5 Server s 8 virtuální jádra.|

Při vytváření nového serveru geografickým obnovením se zdědí stejná velikost úložiště a cenová úroveň jako na zdrojovém serveru. Tyto hodnoty nelze během vytváření změnit. Po vytvoření nového serveru se velikost úložiště dá škálovat.

Po dokončení procesu obnovení Najděte nový server a ověřte, že se data obnovila podle očekávání. Nový server má stejné přihlašovací jméno a heslo správce serveru, které bylo platné pro existující server v době zahájení obnovení. Heslo lze změnit na stránce **Přehled** nového serveru.

Nový server vytvořený během obnovy nemá koncové body služby virtuální sítě, které existovaly na původním serveru. Tato pravidla je potřeba pro tento nový server nastavit samostatně. Obnoví se pravidla brány firewall z původního serveru.

## <a name="next-steps"></a>Další kroky

- Další informace o [zálohování](concepts-backup.md) služby
- Další informace o [replikách](concepts-read-replicas.md)
- Další informace o možnostech [kontinuity podnikových aplikací](concepts-business-continuity.md)
