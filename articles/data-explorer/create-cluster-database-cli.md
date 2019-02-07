---
title: Vytvoření clusteru Průzkumník dat Azure a databázi pomocí rozhraní příkazového řádku
description: Tento článek popisuje způsob vytvoření clusteru Průzkumník dat Azure a databáze pomocí Azure CLI
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: howto
ms.date: 1/31/2019
ms.openlocfilehash: a4c9156ef80f05e247b1cfef0acd56b601a2db65
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812680"
---
# <a name="create-an-azure-data-explorer-cluster-and-a-database-using-cli"></a>Vytvoření clusteru Průzkumník dat Azure a databázi pomocí rozhraní příkazového řádku

Tento článek popisuje postup vytvoření clusteru Průzkumník dat Azure a databáze pomocí Azure CLI.

## <a name="whats-the-difference-between-the-management-plane-and-data-plane-apis"></a>Jaký je rozdíl mezi rovinou správy a rovina dat rozhraní API

Existují dvě různé knihovny rozhraní API, rozhraní API roviny správy a Data.
Rozhraní API pro správu umožňují spravovat prostředky, například vytvoření clusteru, vytvořit databázi, odstranit datové připojení, změnit počet instancí počet atd. Rovina dat rozhraní API se používají k interakci s daty, spouštět dotazy, zpracování příjmu dat atd.

## <a name="configure-the-cli-parameters"></a>Konfigurovat parametry příkazového řádku

Přihlášení k účtu

```Bash
az login
```

Nastavte předplatné, ve kterém chcete cluster vytvořit.

```Bash
az account set --subscription "your_subscription"
```

## <a name="create-the-azure-data-explorer-cluster"></a>Vytvoření clusteru Průzkumník dat Azure

Vytvoření clusteru pomocí následujícího příkazu.

```Bash
az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
```

Zadejte následující hodnoty

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | jméno | *azureclitest* | Požadovaný název vašeho clusteru.|
   | SKU | *D13_v2* | SKU, které se použije pro váš cluster. |
   | resource-group | *testrg* | Název skupiny prostředků, ve kterém by se vytvořil cluster. |

Pokud chcete, existují další volitelné parametry, které můžete použít, jako je například kapacita clusteru atd.

Pokud chcete zkontrolovat, zda byl úspěšně vytvořen cluster, můžete spustit

```Bash
az kusto cluster show --name azureclitest --resource-group testrg
```

Pokud výsledek obsahuje "provisioningState" s hodnotou "ÚSPĚCH", znamená to, že cluster byl úspěšně vytvořen.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Vytvoření databáze v Průzkumníku dat Azure clusteru

Vytvoření databáze pomocí následujícího příkazu.

```Bash
az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
```

Zadejte následující hodnoty

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | Název clusteru | *azureclitest* | Název vašeho clusteru kde má vytvořit.|
   | jméno | *clidatabase* | Požadovaný název vaší databáze.|
   | resource-group | *testrg* | Název skupiny prostředků, ve kterém by se vytvořil cluster. |
   | Konfigurace soft-delete období | *3650:00:00:00* | Množství času, které se mají data ukládat tak, aby byl k dispozici pro dotazy. |
   | Horká doby uložení v mezipaměti | *3650:00:00:00* | Množství času, které se mají data ukládat v mezipaměti. |

Zobrazí databáze, kterou jste vytvořili spuštěním

```Bash
az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
```

Je to Teď máte cluster a databázi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud plánujete postupovat podle našich dalších rychlých startů a kurzů, vytvořené prostředky zachovejte.

Po odstranění clusteru se odstraní také všechny databáze v ní. Používá následující příkaz k odstranění clusteru.

```Bash
az kusto cluster delete --name azureclitest --resource-group testrg
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Ingestovat data z centra událostí do Průzkumníku dat Azure](ingest-data-event-hub.md)
