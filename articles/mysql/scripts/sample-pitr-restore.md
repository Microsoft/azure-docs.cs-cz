---
title: Skript Azure CLI – Obnovení serveru Azure Database for MySQL k dřívějšímu bodu v čase
description: Tento ukázkový skript rozhraní příkazového řádku obnoví server Azure Database for MySQL k dřívějšímu bodu v čase.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: bea3a8057e9b99dc5847e23541d41c5c68393d48
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266207"
---
# <a name="restore-an-azure-database-for-mysql-server-using-azure-cli"></a>Obnovení serveru Azure Database for MySQL pomocí Azure CLI
Tento ukázkový skript rozhraní příkazového řádku obnoví jeden server Azure Database for MySQL k dřívějšímu bodu v čase.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Ukázkový skript
V tomto ukázkovém skriptu změňte zvýrazněné řádky a upravte uživatelské jméno a heslo správce. Nahraďte ID předplatného použité v příkazech az monitor vlastním ID předplatného.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/backup-restore-pitr/backup-restore.sh?highlight=18-19 "Restore Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/backup-restore-pitr/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| **Příkaz** | **Poznámky** |
|---|---|
| [az group create](/cli/azure/group#create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az mysql server create](/cli/azure/mysql/server#create) | Vytvoří server MySQL, který je hostitelem databází. |
| [az mysql server restore](/cli/azure/mysql/server#restore) | Obnoví server ze zálohy. |
| [az group delete](/cli/azure/group#delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky
- Další informace o Azure CLI najdete tady: [Dokumentace k Azure CLI](/cli/azure).
- Vyzkoušejte i další skripty: [Ukázky v Azure CLI pro službu Azure Database for MySQL](../sample-scripts-azure-cli.md)
