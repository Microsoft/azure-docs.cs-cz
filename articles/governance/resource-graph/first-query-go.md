---
title: 'Rychlý Start: první dotaz na cestách'
description: V tomto rychlém startu budete postupovat podle pokynů pro povolení balíčku Resource Graph pro přechod a spuštění prvního dotazu.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 4949801b3dc97904680d09e685fd225812a0e14d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920058"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-go"></a>Rychlý Start: spuštění prvního dotazu na graf prostředku pomocí příkazu Přejít

Prvním krokem k použití Azure Resource graphu je ověření, že jsou nainstalované požadované balíčky pro instalaci. Tento rychlý Start vás provede procesem přidávání balíčků do instalace nástroje.

Na konci tohoto procesu se balíčky přidají do vaší instalace a spustí se první dotaz na diagram prostředku.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="add-the-resource-graph-package"></a>Přidat balíček grafu prostředků

Pokud chcete povolit příkaz Přejít do grafu prostředků Azure, je potřeba balíček přidat. Tento balíček funguje bez ohledu na to, kde se dá použít, včetně [bash ve Windows 10](/windows/wsl/install-win10) nebo lokálně nainstalovaných.

1. Ověřte, že je nainstalovaná nejnovější instalace (minimálně **1,14**). Pokud ještě není nainstalovaný, Stáhněte si ho na adrese [golang.org](https://golang.org/dl/).

1. Ověřte, že je nainstalované nejnovější rozhraní příkazového řádku Azure (aspoň **2.5.1**). Pokud ještě není nainstalovaná, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Aby bylo možné povolit příkaz Přejít k použití `auth.NewAuthorizerFromCLI()` metody v následujícím příkladu, je nutné Azure CLI. Informace o dalších možnostech najdete v tématu [Azure SDK pro go – další podrobnosti o ověřování](https://github.com/Azure/azure-sdk-for-go#more-authentication-details).

1. Ověření prostřednictvím rozhraní příkazového řádku Azure

   ```azurecli
   az login
   ```

1. V prostředí s možností výběru nainstalujte požadované balíčky pro Azure Resource Graph:

   ```bash
   # Add the Resource Graph package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="run-your-first-resource-graph-query"></a>Spusťte nejdříve dotaz na Resource Graph použitím Azure CLI

Pomocí balíčků pro cestách přidaných do vašeho prostředí podle vlastního výběru je čas vyzkoušet jednoduchý dotaz grafu prostředků. Dotaz vrátí prvních pět prostředků Azure s **názvem** a **typem prostředku** každého prostředku.

1. Vytvořte aplikaci přejít a uložte následující zdroj `argQuery.go` :

   ```Go
   package main
   
   import (
      "fmt"
      "os"
      "context"
      "strconv"
      arg "github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph"
      "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
       // Get variables from command line arguments
       var query = os.Args[1]
       var subList = os.Args[2:]
   
       // Create and authorize a ResourceGraph client
       argClient := arg.New()
       authorizer, err := auth.NewAuthorizerFromCLI()
       if err == nil {
           argClient.Authorizer = authorizer
       } else {
           fmt.Printf(err.Error())
       }
     
       // Set options
       RequestOptions := arg.QueryRequestOptions {
           ResultFormat: "objectArray",
       }
     
       // Create the query request
       Request := arg.QueryRequest {
           Subscriptions: &subList,
           Query: &query,
           Options: &RequestOptions,
       }
     
       // Run the query and get the results
       var results, queryErr = argClient.Resources(context.Background(), Request)
       if queryErr == nil {
           fmt.Printf("Resources found: " + strconv.FormatInt(*results.TotalRecords, 10) + "\n")
           fmt.Printf("Results: " + fmt.Sprint(results.Data) + "\n")
       } else {
           fmt.Printf(queryErr.Error())
       }
   }
   ```

1. Sestavte aplikaci v cestách:

   ```bash
   go build argQuery.go
   ```

1. Spusťte svůj první dotaz na Azure Resource Graph pomocí kompilované aplikace. Nahraďte `<SubID>` ID předplatného:

   ```bash
   argQuery "Resources | project name, type | limit 5" "<SubID>"
   ```

   > [!NOTE]
   > Na tento dotaz příklad neposkytuje modifikátor řazení jako `order by`, opakované spouštění tohoto dotazu pravděpodobně poskytne jinou sadu zdrojů na jednu žádost.

1. Změňte první parametr na `argQuery` a změňte dotaz na `order by` vlastnost **Name** . Nahraďte `<SubID>` ID předplatného:

   ```bash
   argQuery "Resources | project name, type | limit 5 | order by name asc" "<SubID>"
   ```

   > [!NOTE]
   > Stejně jako u prvního dotazu opakované spouštění tohoto dotazu pravděpodobně poskytne jinou sadu zdrojů na jednu žádost. Pořadí příkazů dotazů je důležité. V tomto příkladu `order by` přichází po `limit`. Toto pořadí příkazů nejprve omezí výsledky dotazu a pak je vyřadí.

1. Změňte první parametr na `argQuery` a změňte dotaz na první `order by` vlastnost **název** a potom `limit` na pět nejlepších výsledků. Nahraďte `<SubID>` ID předplatného:

   ```bash
   argQuery "Resources | project name, type | order by name asc | limit 5" "<SubID>"
   ```

Když se konečný dotaz několikrát spustí, předpokládá se, že se nic ve vašem prostředí nemění, vrácené výsledky jsou konzistentní a seřazené podle vlastnosti **Name** , ale pořád se omezí na pět nejlepších výsledků.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete z prostředí přejít odebrat nainstalované balíčky, můžete k tomu použít následující příkaz:

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přidali balíčky grafu prostředků do prostředí přechodu a spustíte svůj první dotaz. Chcete-li získat další informace o jazyku grafu prostředků, přejděte na stránku podrobností dotazovacího jazyka.

> [!div class="nextstepaction"]
> [Získat další informace o dotazovacím jazyku](./concepts/query-language.md)