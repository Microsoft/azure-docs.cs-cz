---
title: Začínáme s Azure CLI pro Batch
description: Rychlý úvod k příkazům Batch v rozhraní příkazového řádku Azure CLI pro správu prostředků služby Azure Batch
ms.topic: how-to
ms.date: 07/24/2018
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: befe2f6fe67ce7c2c4bb61153344103ee2aa70cb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768428"
---
# <a name="manage-batch-resources-with-azure-cli"></a>Správa prostředků služby Batch pomocí Azure CLI

Azure CLI je prostředí příkazového řádku Azure pro správu prostředků Azure. Je možné používat ho v systémech macOS, Linux a Windows. Azure CLI je optimalizované pro správu prostředků Azure z příkazového řádku. Rozhraní příkazového řádku Azure CLI můžete používat ke správě účtů služby Azure Batch a ke správě prostředků, jako jsou fondy, úlohy a úkoly. V rozhraní příkazového řádku Azure CLI můžete používat skripty pro mnoho stejných úkolů, které se provádějí prostřednictvím rozhraní API služby Batch, webu Azure Portal a rutin PowerShellu služby Batch.

Tento článek obsahuje přehled používání rozhraní [Azure CLI verze 2.0](/cli/azure) se službou Batch. V článku [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli) najdete přehled používání rozhraní příkazového řádku s Azure.

## <a name="set-up-the-azure-cli"></a>Nastavení Azure CLI

Nejnovější verzi Azure CLI můžete spustit ve službě [Azure Cloud Shell](../cloud-shell/overview.md). Pokud chcete nainstalovat Azure CLI místně, postupujte podle kroků uvedených v tématu [Instalace Azure CLI](/cli/azure/install-azure-cli).

> [!TIP]
> Instalaci rozhraní příkazového řádku Azure CLI doporučujeme často aktualizovat, abyste mohli využívat výhody, které vám přinášejí aktualizace a vylepšení služby.
> 
> 

## <a name="command-help"></a>Nápověda k příkazům

Pro každý příkaz v rámci rozhraní příkazového řádku Azure CLI můžete zobrazit nápovědu, pokud za název příkazu přidáte parametr `-h`. Jiné parametry vynechejte. Například:

* Pokud chcete zobrazit nápovědu pro příkaz `az`, zadejte: `az -h`
* Pokud chcete vypsat seznam všech příkazů Batch v rámci rozhraní příkazového řádku, zadejte: `az batch -h`
* Pokud chcete získat nápovědu k vytvoření účtu Batch, zadejte: `az batch account create -h`

Pokud si nejste jisti, pomocí parametru příkazového řádku `-h` můžete zobrazit nápovědu pro kterýkoli příkaz rozhraní příkazového řádku Azure CLI.



Kromě toho si prohlédněte referenční dokumentaci rozhraní příkazového řádku Azure CLI, kde najdete podrobnosti o [příkazech rozhraní příkazového řádku Azure CLI pro službu Batch](/cli/azure/batch). 

## <a name="log-in-and-authenticate"></a>Přihlášení a ověření

Pokud chcete používat rozhraní příkazového řádku Azure CLI se službou Batch, potřebujete se přihlásit a provést ověření. Je třeba provést dva jednoduché kroky:

1. **Přihlaste se k Azure.** Přihlášení k Azure vám umožní přístup k příkazům správce Azure Resource Manager včetně příkazů [služby Batch Management](batch-management-dotnet.md).  
2. **Přihlaste se ke svému účtu Batch.** Přihlášení k účtu Batch vám umožní přístup k příkazům služby Batch.   

### <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Existuje několik různých způsobů přihlášení k Azure, které jsou podrobně popsány v článku [Přihlášení pomocí Azure CLI](/cli/azure/authenticate-azure-cli):

1. [Interaktivní přihlášení](/cli/azure/authenticate-azure-cli): Přihlaste se interaktivně, pokud spouštíte příkazy rozhraní příkazového řádku Azure CLI sami z příkazového řádku.
2. [Přihlaste se pomocí instančního objektu](/cli/azure/authenticate-azure-cli). Pokud spouštíte příkazy rozhraní příkazového řádku Azure CLI ze skriptu nebo aplikace, přihlaste se pomocí instančního objektu.

Pro účely tohoto článku vám ukážeme, jak se k Azure přihlásit interaktivně. V příkazovém řádku napište [az login](/cli/azure/reference-index#az_login):

```azurecli
# Log in to Azure and authenticate interactively.
az login
```

Příkaz `az login` vrátí token, který můžete použít k ověření, jak je vidět zde. Postupem podle zobrazených pokynů otevřete webovou stránku a odešlete token do služby Azure:

![Přihlaste se k Azure.](./media/batch-cli-get-started/az-login.png)

Příklady uvedené v části Ukázkové skripty prostředí také ukazují, jak spustit relaci rozhraní příkazového řádku Azure CLI pomocí interaktivního přihlášení k Azure. Jakmile se přihlásíte, můžete volat příkazy pro práci s prostředky služby Batch Management včetně účtů Batch, klíčů, balíčků aplikací a kvót.  

### <a name="log-in-to-your-batch-account"></a>Přihlášení k účtu Batch

Pokud chcete rozhraní příkazového řádku Azure CLI používat ke správě prostředků služby Batch, jako jsou fondy, úlohy a úkoly, musíte se přihlásit k účtu Batch a provést ověření. Ke službě Batch se přihlásíte pomocí příkazu [az batch account login](/cli/azure/batch/account#az_batch_account_login). 

Máte dvě možnosti ověření proti účtu Batch:

- **Pomocí ověřování Azure Active Directory (Azure AD)** 

    Ověření pomocí služby Azure AD je výchozí možností, pokud používáte rozhraní příkazového řádku Azure CLI se službou Batch. Tuto možnost doporučujeme pro většinu scénářů. 
    
    Při interaktivním přihlášení k Azure, které je popsáno v předchozí části, jsou vaše přihlašovací údaje uloženy v mezipaměti, takže rozhraní příkazového řádku Azure CLI vás může přihlásit k účtu Batch pomocí stejných přihlašovacích údajů. Pokud se k Azure přihlásíte pomocí instančního objektu, použijí se tyto přihlašovací údaje také k přihlášení k účtu Batch.

    Výhodou Azure AD je, že nabízí řízení přístupu na základě role Azure (Azure RBAC). V případě Azure RBAC závisí přístup uživatelů na jejich přiřazené roli, a ne na tom, jestli mají klíče účtu nebo ne. Místo správy klíčů účtu můžete spravovat role Azure a nechat Azure AD zpracovat přístup a ověřování.  

     Pokud se chcete přihlásit k účtu Batch pomocí služby Azure AD, použijte volání příkazu [az batch account login](/cli/azure/batch/account#az_batch_account_login): 

    ```azurecli
    az batch account login -g myresource group -n mybatchaccount
    ```

- **Pomocí ověřování pomocí sdíleného klíče**

    [Ověření pomocí sdíleného klíče](/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service#authentication-via-shared-key) používá k ověření příkazů rozhraní příkazového řádku Azure CLI pro službu Batch klíče pro přístup k účtu.

    Pokud vytváříte skripty rozhraní příkazového řádku Azure CLI k automatizaci volání příkazů služby Batch, můžete použít buď ověření pomocí sdíleného klíče, anebo instanční objekt služby Azure AD. V některých scénářích může být ověření pomocí sdíleného klíče jednodušší než vytvoření instančního objektu.  

    Pokud se chcete přihlásit s ověřením pomocí sdíleného klíče, zahrňte v příkazovém řádku možnost `--shared-key-auth`:

    ```azurecli
    az batch account login -g myresourcegroup -n mybatchaccount --shared-key-auth
    ```

Příklady uvedené v části Ukázkové skripty prostředí ukazují, jak se k účtu Batch přihlásit v rozhraní příkazového řádku Azure CLI jak pomocí služby Azure AD, tak i pomocí sdíleného klíče.

## <a name="use-azure-batch-cli-extension-commands"></a>Použití příkazů rozšíření rozhraní příkazového řádku služby Azure Batch

Pokud nainstalujete rozšíření rozhraní příkazového řádku služby Azure Batch, můžete pomocí Azure CLI spouštět kompletní dávkové úlohy bez psaní kódu. Příkazy Batch, které toto rozšíření podporuje, umožňují použití šablon JSON k vytváření fondů, úloh a úkolů pomocí Azure CLI. Pomocí příkazů rozhraní příkazového řádku rozšíření můžete také nahrávat vstupní soubory úloh do účtu služby Azure Storage přidruženého k účtu Batch a stahovat z něj výstupní soubory úloh. Další informace najdete v tématu [použití Azure Batch šablon CLI a přenosu souborů](batch-cli-templates.md).

## <a name="script-examples"></a>Příklady skriptu

Viz [příklady skriptu CLI](./scripts/batch-cli-sample-create-account.md) pro službu Batch k provedení běžných úloh. Tyto příklady pokrývají mnoho příkazů dostupných ve službě Azure Batch pomocí příkazového řádku Azure CLI pro vytvoření a správu účtů, fondů, úloh a úkolů.

## <a name="json-files-for-resource-creation"></a>Soubory JSON pro vytváření prostředků

Při vytváření prostředků Batch, jako jsou fondy a úlohy, můžete určit soubor JSON obsahující konfiguraci nového prostředku namísto předávání jejích parametrů v podobě parametrů příkazového řádku. Například:

```azurecli
az batch pool create my_batch_pool.json
```

Ačkoli mnoho prostředků služby Batch můžete vytvářet pouze prostřednictvím parametrů příkazového řádku, některé funkce vyžadují, abyste určili soubor ve formátu JSON obsahující podrobnosti o prostředku. Soubor JSON je například třeba použít, pokud chcete určit soubory prostředků pro úkol při spuštění.

Pokud si chcete prohlédnout syntaxi souboru JSON vyžadovanou k vytvoření prostředku, prostudujte si dokumentaci [Reference k rozhraní REST API služby Batch][rest_api]. Každé z témat Přidání *typ prostředku* v dokumentaci Reference k rozhraní REST API obsahuje ukázkové skripty JSON pro vytvoření příslušného prostředku. Tyto ukázkové skripty JSON můžete použít jako šablony pro soubory JSON a používat je v rozhraní příkazového řádku Azure CLI. Pokud si například chcete prohlédnout syntaxi skriptu JSON pro vytvoření fondu, podívejte se na téma [Přidání fondu k účtu][rest_add_pool].

Ukázkový skript, který určuje soubor JSON, najdete v článku [Spuštění úlohy a úkolů pomocí služby Batch](./scripts/batch-cli-sample-run-job.md).

> [!NOTE]
> Pokud při vytváření prostředku určíte soubor JSON, budou všechny ostatní parametry zadané v příkazovém řádku pro příslušný prostředek ignorovány.
> 
> 

## <a name="efficient-queries-for-batch-resources"></a>Efektivní dotazy na prostředky služby Batch

Každý typ prostředku Batch podporuje příkaz `list`, který zadá dotaz na účet Batch a vypíše seznam prostředků příslušného typu. Můžete například vypsat seznam fondů v rámci vašeho účet a seznam úkolů v rámci úloh:

```azurecli
az batch pool list
az batch task list --job-id job001
```

Při dotazování služby Batch pomocí operace `list` můžete určit klauzuli OData a omezit tak množství vrácených dat. Protože veškeré filtrování probíhá na straně serveru, přenášejí se pouze data, která požadujete. Při provádění operací vypsání seznamu můžete pomocí těchto klauzulí snížit využití šířky pásma (a tedy čas).

Následující tabulka popisuje klauzule OData podporované službou Batch:

| Klauzule | Description |
|---|---|
| `--select-clause [select-clause]` | Vrátí podmnožinu vlastností pro každou entitu. |
| `--filter-clause [filter-clause]` | Vrátí pouze ty entity, které odpovídají zadanému výrazu OData. |
| `--expand-clause [expand-clause]` | Získá informace o entitách v rámci jediného základního volání REST. Klauzule expand v současné době podporuje pouze vlastnost `stats`. |

Ukázkový skript, který ukazuje, jak používat klauzuli OData, najdete v článku [Spuštění úlohy a úkolů pomocí služby Batch](./scripts/batch-cli-sample-run-job.md).

Další informace o provádění efektivních dotazů pomocí příkazu list s klauzulemi OData najdete v článku [Efektivní dotazování na službu Azure Batch](batch-efficient-list-queries.md).

## <a name="troubleshooting-tips"></a>Rady pro řešení potíží

Následující tipy mohou pomoci při řešení potíží s rozhraním příkazového řádku Azure CLI:

* Použití parametru `-h` k získání **textu nápovědy** pro kterýkoli příkaz rozhraní příkazového řádku CLI
* Pomocí parametrů `-v` a `-vv` zobrazíte **podrobný** výstup příkazu. Pokud zahrnete příznak `-vv`, zobrazí rozhraní příkazového řádku Azure CLI příslušné požadavky a odpovědi služby REST. Tyto přepínače jsou užitečné pro zobrazení úplného chybového výstupu.
* Pomocí parametru `--json` můžete zobrazit **výstup příkazu ve formátu JSON**. Příkaz `az batch pool show pool001 --json` například zobrazí vlastnosti fondu pool001 ve formátu JSON. Pak můžete tento výstup zkopírovat a upravit pro použití v příkazu `--json-file` (viz soubory JSON dříve v tomto článku).
<!---Loc Comment: Please, check link [JSON files] since it's not redirecting to any location.--->

## <a name="next-steps"></a>Další kroky

* Viz [dokumentace k Azure CLI](/cli/azure).
* Přečtěte si o [pracovních postupech služby Batch a primárních prostředcích](batch-service-workflow-features.md) , jako jsou fondy, uzly, úlohy a úkoly.
* Přečtěte si o používání šablon Batch k vytváření fondů, úloh a úkolů bez psaní kódu, který se [používá Azure Batch šablon CLI a přenosu souborů](batch-cli-templates.md).

[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: /rest/api/batchservice/
[rest_add_pool]: /rest/api/batchservice/pool/add
