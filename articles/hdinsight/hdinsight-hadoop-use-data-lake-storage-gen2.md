---
title: Použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight
description: Další informace o použití Azure Data Lake Storage Gen2 s využitím clusterů Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: hrasheed
ms.openlocfilehash: 45b34d12fbcecbf5f6bf1225c5bb82c5385224ed
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58338390"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight

Azure Data Lake Storage Gen2 je Cloudová služba úložiště vyhrazené pro analýzy velkých objemů dat, postavená na Azure Blob storage. Data Lake Storage Gen2 kombinuje funkce úložiště objektů Blob v Azure a Azure Data Lake Storage Gen1. Výsledný služba nabízí funkce z Azure Data Lake Storage Gen1, jako je například sémantiku systému souborů, zabezpečení na úrovni adresář nebo soubor a škálovatelnost, spolu s vrstveného úložiště s nízkými náklady, vysokou dostupnost a možnosti zotavení po havárii z úložiště objektů Blob v Azure.

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 dostupnosti

Data Lake Storage Gen2 je k dispozici jako řešením úložiště pro téměř všechny typy clusteru Azure HDInsight jako výchozí a dalšího účtu úložiště. HBase, však může mít pouze jeden účet Data Lake Storage Gen2.

> [!Note] 
> Po výběru Data Lake Storage Gen2 jako vaše **typ primárního úložiště**, nelze vybrat účet Data Lake Storage Gen1 jako další úložiště.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Vytvoření clusteru s Data Lake Storage Gen2 prostřednictvím webu Azure portal

Vytvoření clusteru HDInsight, která využívá úložiště Data Lake Storage Gen2, postupujte podle těchto kroků a nakonfigurujte účet Data Lake Storage Gen2.

### <a name="create-a-user-managed-identity"></a>Vytvoření identity spravované uživatelem

Vytvoření uživatelsky přiřazené spravovanou identitu, pokud ho ještě nemáte. Zobrazit [vytvoření, list, delete nebo přiřadit roli, kterou chcete přiřadit uživatele spravovanou identitu pomocí webu Azure portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity). Další informace o spravovaných pracovních identit v Azure HDInsight, naleznete v tématu [spravovaných identit v Azure HDInsight](hdinsight-managed-identities.md).

![Vytvoření spravované identity přiřazené uživatelem](./media/hdinsight-hadoop-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Vytvoření účtu Data Lake Storage Gen2

Vytvoření účtu úložiště Azure Data Lake Storage Gen2. Ujistěte se, že **hierarchického oboru názvů** je povolená možnost. Další informace najdete v tématu [rychlý start: Vytvoření účtu úložiště Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

![Snímek obrazovky znázorňující vytvoření účtu úložiště na webu Azure Portal](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="setup-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Nastavení oprávnění pro spravovanou identitu účtu Data Lake Storage Gen2

Přiřazení spravovaných identit k **vlastník dat úložiště objektů Blob (Preview)** role v účtu úložiště. Další informace najdete v tématu [Správa přístupových práv k datům objektů Blob v Azure a fronty pomocí RBAC (Preview)](../storage/common/storage-auth-aad-rbac.md).

1. V [webu Azure portal](https://portal.azure.com), přejděte do svého účtu úložiště.
1. Vyberte svůj účet úložiště a pak vyberte **řízení přístupu (IAM)** zobrazíte nastavení řízení přístupu k účtu. Vyberte **přiřazení rolí** kartu pro zobrazení seznamu přiřazení rolí.
    
    ![Snímek obrazovky zobrazující nastavení řízení přístupu úložiště](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
1. Vyberte **+ přidat přiřazení role** tlačítko Přidat novou roli.
1. V **přidat přiřazení role** okna, vyberte **vlastník dat úložiště objektů Blob (Preview)** role. Vyberte předplatné, které má spravované identity a účtu úložiště. V dalším kroku prohledávat při vyhledávání uživatelsky přiřazené spravovanou identitu, který jste vytvořili dříve. Nakonec vyberte spravovanou identitu a zobrazí se v části **Vybraní členové**.
    
    ![Snímek obrazovky ukazující, jak přiřadit roli RBAC](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role3.png)
    
1. Vyberte **Uložit**. Uživatelsky přiřazené identity, který jste vybrali, je nyní obsažena v rámci zvolenou roli.
1. Po tomto počátečním nastavení je dokončeno, můžete vytvořit cluster na portálu. Cluster musí být ve stejné oblasti Azure jako účet úložiště. V **úložiště** části nabídky vytváření clusteru, vyberte následující možnosti:
        
    * Pro **typ primárního úložiště**vyberte **Azure Data Lake Storage Gen2**.
    * V části **vyberte účet úložiště**, vyhledejte a vyberte nově vytvořený účet úložiště Data Lake Storage Gen2.
        
        ![Nastavení úložiště pro Data Lake Storage Gen2 pomocí Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
    
    * V části **Identity**, vyberte správné předplatné a nově vytvořený uživatelsky přiřazené identity spravované.
        
        ![Nastavení identity pro Azure HDInsight pomocí Data Lake Storage Gen2](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)
        
> [!Note]
> Můžete přidat jeden nebo více účtů Data Lake Storage Gen2 jako sekundární úložiště ve stejném clusteru. Právě opakujte předchozí postup u každého účtu Data Lake Storage Gen2, který chcete přidat direktivu using stejnou identitou.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Vytvoření clusteru s Data Lake Storage Gen2 přes rozhraní příkazového řádku Azure

Je možné [stáhnout ukázkový soubor šablony](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) a [stáhnout ukázkový soubor parametrů](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Před použitím šablony, nahraďte řetězec `<SUBSCRIPTION_ID>` s vaším ID skutečné předplatného Azure. Také nahraďte řetězec `<PASSWORD>` pomocí hesla zvolená nastavení i heslo, které budete používat k přihlášení ke clusteru a heslo SSH.

Následující fragment kódu se provede počáteční takto:

1. Protokoly v ke svému účtu Azure.
1. Nastaví aktivní předplatné, ve kterém bude provedeno operace vytvoření.
1. Vytvoří novou skupinu prostředků pro nové aktivity nasazení s názvem `hdinsight-deployment-rg`.
1. Vytvoří spravovanou identitu přiřazené uživatele s názvem `test-hdinsight-msi`.
1. Přidá rozšíření rozhraní příkazového řádku Azure pro použití funkcí pro Data Lake Storage Gen2.
1. Vytvoří nový účet Data Lake Storage Gen2 `hdinsightadlsgen2`, s použitím `--hierarchical-namespace true` příznak.

```azurecli
az login
az account set --subscription <subscription_id>

# Create resource group
az group create --name hdinsight-deployment-rg --location eastus

# Create managed identity
az identity create -g hdinsight-deployment-rg -n test-hdinsight-msi

az extension add --name storage-preview

az storage account create --name hdinsightadlsgen2 \
    --resource-group hdinsight-deployment-rg \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Dalším kroku se přihlaste k portálu. Nový uživatel přiřazenou spravované identitě, kterou chcete přidat **Přispěvatel dat objektu Blob služby Storage (Preview)** role v účtu úložiště, jak je popsáno v kroku 3 v části [pomocí webu Azure portal](hdinsight-hadoop-use-data-lake-storage-gen2.md).

Po přiřazení role pro uživatelsky přiřazené spravovanou identitu, nasazení šablony pomocí následujícího fragmentu kódu.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group hdinsight-deployment-rg \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Řízení přístupu pro Data Lake Storage Gen2 v HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Jaké druhy oprávnění, která podporuje Data Lake Storage Gen2?

Data Lake Storage Gen2 používá model řízení přístupu, který podporuje řízení přístupu na základě role (RBAC) a seznamy řízení přístupu POSIX jako (ACL). Data Lake Storage Gen1 podporuje seznamů ACL pouze pro řízení přístupu k datům.

RBAC využívá přiřazení rolí pro efektivní použití sady oprávnění pro uživatele, skupiny nebo instanční objekty pro prostředky Azure. Obvykle tyto prostředky Azure, jsou přednastaveny omezené prostředky nejvyšší úrovně (například účty úložiště Azure). Pro Azure Storage a také Data Lake Storage Gen2 tento mechanismus rozšířilo a prostředek systému souborů.

 Další informace o oprávnění k souborům pomocí RBAC najdete v tématu [řízení přístupu Azure na základě rolí (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Další informace o oprávnění k souborům pomocí seznamů ACL, naleznete v tématu [seznamy řízení přístupu na soubory a adresáře](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Jak řízení přístupu v Data Lake Storage Gen2 mými daty?

Váš cluster HDInsight umožňuje přístup k souborům v Data Lake Storage Gen2 je řízen pomocí spravované identity. Spravovaná identita je identita v Azure Active Directory (Azure AD) jehož přihlašovací údaje se spravují přes Azure. Pomocí spravované identity nemusíte registrovat instančních objektů ve službě Azure AD nebo spravovat přihlašovací údaje, jako třeba certifikáty.

Služeb Azure má dva typy spravovaných identit: systém přiřadil a uživatelsky přiřazené. HDInsight používá spravované uživatelsky přiřazené identity pro přístup k Data Lake Storage Gen2. Uživatel přiřazenou spravovaná identita je vytvořen jako samostatný prostředek Azure. Prostřednictvím procesu vytvoření Azure vytvoří identitu v tenantovi Azure AD důvěryhodném pro použité předplatné. Po vytvoření identity je možné ji přiřadit k jedné nebo několika instancím služeb Azure.

Životní cyklus identity přiřazené uživatelem se spravuje nezávisle na životním cyklu instancí služeb Azure, ke kterým je přiřazená. Další informace o spravovaných identit najdete v tématu [jak spravovaných identit pro práci s prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Jak nastavit oprávnění pro uživatele Azure AD k dotazování dat v Data Lake Storage Gen2 s použitím Hive nebo jiné služby?

K nastavení oprávnění pro uživatele k dotazování dat. Použijte skupiny zabezpečení Azure AD jako přiřazené instanční objekt v seznamech ACL. Nepřiřazovat přímo oprávnění přístupu k souborům na jednotlivé uživatele nebo instanční objekty. Pokud používáte skupiny zabezpečení Azure AD pro řízení toku oprávnění, můžete přidávat a odebírat uživatele nebo instanční objekty bez opětovné použití seznamů ACL celý adresářové struktury. Stačí jenom přidat nebo odebrat uživatele z příslušné skupiny zabezpečení Azure AD. Seznamy ACL nejsou zděděny, tak obnovení seznamů řízení přístupu vyžaduje aktualizaci jejich seznamu ACL u všech souborů a podadresářů.

## <a name="next-steps"></a>Další postup

* [Použití Azure Data Lake Storage Gen2 Preview s využitím clusterů Azure HDInsight](../storage/blobs/data-lake-storage-use-hdi-cluster.md)
* [Aktualizace integrace Azure HDInsight s Data Lake Storage Gen2 preview – řízení přístupu a zabezpečení](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Úvod do služby Azure Data Lake Storage Gen2 ve verzi Preview](../storage/blobs/data-lake-storage-introduction.md)
