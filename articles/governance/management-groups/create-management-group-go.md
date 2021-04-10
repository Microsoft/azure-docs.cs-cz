---
title: 'Rychlý Start: Vytvoření skupiny pro správu pomocí nástroje přejít'
description: V tomto rychlém startu použijete příkaz Přejít k vytvoření skupiny pro správu, která slouží k uspořádání prostředků do hierarchie prostředků.
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 7b6ca4d10f2a86ecb55fec2afe72b4aabfbb94f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100101762"
---
# <a name="quickstart-create-a-management-group-with-go"></a>Rychlý Start: Vytvoření skupiny pro správu pomocí nástroje přejít

Skupiny pro správu jsou kontejnery, které vám pomůžou spravovat přístup, zásady a dodržování předpisů v různých předplatných. Vytvořte tyto kontejnery, abyste vytvořili efektivní a efektivní hierarchii, která se dá použít s [Azure Policy](../policy/overview.md) a [řízení přístupu na základě rolí Azure](../../role-based-access-control/overview.md). Další informace o skupinách pro správu najdete v tématu [uspořádání prostředků pomocí skupin pro správu Azure](overview.md).

První skupina pro správu vytvořená v adresáři může trvat až 15 minut, než se dokončí. Při nastavování služby skupiny pro správu v Azure pro váš adresář existují procesy, které se spouštějí poprvé. Po dokončení procesu obdržíte oznámení. Další informace najdete v tématu [počáteční nastavení skupin pro správu](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

- Objekt služby Azure, včetně _ClientID_ a _clientSecret_. Pokud nemáte instanční objekt pro použití s Azure Policy nebo chcete vytvořit nový, přečtěte si téma [knihovny pro správu Azure pro ověřování .NET](/dotnet/azure/sdk/authentication#mgmt-auth).
  Přeskočte krok pro instalaci balíčků .NET Core, jak to provedeme v dalších krocích.

- Libovolný uživatel Azure AD v tenantovi může vytvořit skupinu pro správu bez oprávnění k zápisu skupiny pro správu, která jsou přiřazená tomuto uživateli, pokud není povolena [ochrana hierarchie](./how-to/protect-resource-hierarchy.md#setting---require-authorization) . Tato nová skupina pro správu se stal podřízenou skupinou kořenové skupiny pro správu nebo [výchozí skupině pro správu](./how-to/protect-resource-hierarchy.md#setting---default-management-group) a autorovi je přiděleno přiřazení role "vlastník". Služba skupiny pro správu umožňuje tuto možnost, takže přiřazení rolí není nutné na kořenové úrovni. Žádní uživatelé nemají přístup ke kořenové skupině pro správu při jejím vytvoření. Aby nedocházelo k tomu, že by globální Správci služby Azure AD mohli začít používat skupiny pro správu, umožníme vytváření počátečních skupin pro správu na kořenové úrovni.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-management-group-package"></a>Přidat balíček skupiny pro správu

Pokud chcete povolit možnost přejít ke správě skupin pro správu, musí se balíček přidat. Tento balíček funguje bez ohledu na to, kde se dá použít, včetně [bash ve Windows 10](/windows/wsl/install-win10) nebo lokálně nainstalovaných.

1. Ověřte, že je nainstalovaná nejnovější instalace (minimálně **1,15**). Pokud ještě není nainstalovaný, Stáhněte si ho na adrese [golang.org](https://golang.org/dl/).

1. Ověřte, že je nainstalované nejnovější rozhraní příkazového řádku Azure (aspoň **2.5.1**). Pokud ještě není nainstalovaná, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Aby bylo možné povolit příkaz Přejít k použití `auth.NewAuthorizerFromCLI()` metody v následujícím příkladu, je nutné Azure CLI. Informace o dalších možnostech najdete v tématu [Azure SDK pro go – další podrobnosti o ověřování](https://github.com/Azure/azure-sdk-for-go#more-authentication-details).

1. Ověření prostřednictvím rozhraní příkazového řádku Azure

   ```azurecli
   az login
   ```

1. V prostředí s možností výběru nainstalujte požadované balíčky pro skupiny pro správu:

   ```bash
   # Add the management group package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="application-setup"></a>Nastavení aplikace

S přidanými balíčky přejít do vašeho prostředí je čas nastavit aplikaci, která může vytvořit skupinu pro správu.

1. Vytvořte aplikaci přejít a uložte následující zdroj `mgCreate.go` :

   ```Go
   package main
   
   import (
    "context"
    "fmt"
    "os"
   
    mg "github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups"
    "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
    // Get variables from command line arguments
    var mgName = os.Args[1]
   
    // Create and authorize a client
    mgClient := mg.NewClient()
    authorizer, err := auth.NewAuthorizerFromCLI()
    if err == nil {
        mgClient.Authorizer = authorizer
    } else {
        fmt.Printf(err.Error())
    }
   
    // Create the request
    Request := mg.CreateManagementGroupRequest{
        Name: &mgName,
    }
   
    // Run the query and get the results
    var results, queryErr = mgClient.CreateOrUpdate(context.Background(), mgName, Request, "no-cache")
    if queryErr == nil {
        fmt.Printf("Results: " + fmt.Sprint(results) + "\n")
    } else {
        fmt.Printf(queryErr.Error())
    }
   }
   ```

1. Sestavte aplikaci v cestách:

   ```bash
   go build mgCreate.go
   ```

1. Vytvořte skupinu pro správu pomocí kompilované aplikace typu přejít. Nahraďte `<Name>` názvem vaší nové skupiny pro správu:

   ```bash
   mgCreate "<Name>"
   ```

Výsledkem je nová skupina pro správu v kořenové skupině pro správu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete z prostředí přejít odebrat nainstalované balíčky, můžete k tomu použít následující příkaz:

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili skupinu pro správu, která bude organizovat svou hierarchii prostředků. Skupina pro správu může obsahovat předplatná nebo jiné skupiny pro správu.

Pokud chcete získat další informace o skupinách pro správu a o tom, jak spravovat hierarchii prostředků, pokračujte tady:

> [!div class="nextstepaction"]
> [Správa prostředků pomocí skupin pro správu](./manage.md)