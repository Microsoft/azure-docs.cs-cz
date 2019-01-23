---
title: Použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight
description: Zjistěte, jak vytvořit pomocí Azure Data Lake Storage Gen2 s Azure HDInsight clustery.
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: howto
ms.date: 01/10/2019
ms.author: hrasheed
ms.openlocfilehash: 1d43c7b6dd1bdec0a2507d8ce1a3883f5ce31a39
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54479476"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight

Azure Data Lake Storage (úložiště Data Lake) Gen2 je sada funkcí, které jsou vyhrazené pro analýzy velkých objemů dat, postavená na Azure Blob storage. Data Lake Storage Gen2 je výsledkem kombinace možností úložiště Azure Blob storage a Azure Data Lake Storage Gen1. Výsledkem je služba, která nabízí funkce z Azure Data Lake Storage Gen1, jako je například sémantiku systému souborů, adresář a zabezpečení na úrovni souborů a škálování s nízkými náklady, vrstveného úložiště, funkce vysoká dostupnost/zotavení po havárii pro obnovení z Azure Blob úložiště.

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 dostupnosti

Azure Data Lake Storage Gen2 je k dispozici jako řešením úložiště pro téměř všechny typy clusteru Azure HDInsight jako výchozí a dalšího účtu úložiště. HBase, ale může mít pouze jeden účet Data Lake Storage Gen2.

> [!Note] 
> Jakmile vyberete Data Lake Storage Gen2 jako vaše **typ primárního úložiště**, nelze vybrat účet Data Lake Storage Gen1 jako další úložiště.

## <a name="creating-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Vytvoření clusteru služby HDInsight s Data Lake Storage Gen2

K vytvoření clusteru HDInsight, který používá Data Lake Storage Gen2 pro úložiště, použijte následující kroky k vytvoření účtu Data Lake Storage Gen2, který je nakonfigurovaný správně.

1. Vytvoření uživatelsky přiřazené spravovanou identitu, pokud ho ještě nemáte. Zobrazit [vytvoření, list, delete nebo přiřadit roli, kterou chcete přiřadit uživatele spravovanou identitu pomocí webu Azure portal](/../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal#create-a-user-assigned-managed-identity.md).

    ![Vytvoření spravované identity přiřazené uživatelem](./media/hdinsight-hadoop-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

1. Vytvoření účtu úložiště Azure Data Lake Storage Gen2. Ujistěte se, **hierarchický systém souborů** je povolená možnost. Zobrazit [rychlý start: Vytvoření účtu úložiště Azure Data Lake Storage Gen2](/../storage/blobs/data-lake-storage-quickstart-create-account.md) další podrobnosti.

    ![Snímek obrazovky znázorňující vytvoření účtu úložiště na webu Azure Portal](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)
 
1. Přiřazení spravovaných identit k **Přispěvatel dat objektu Blob služby Storage (Preview)** role v účtu úložiště. Zobrazit [Správa přístupových práv k datům objektů Blob v Azure a fronty pomocí RBAC (Preview)](/../storage/common/storage-auth-aad-rbac#assign-a-role-scoped-to-the-storage-account-in-the-azure-portal.md)

    1. V [webu Azure portal](https://portal.azure.com), přejděte do svého účtu úložiště.
    1. Vyberte svůj účet úložiště a pak vyberte **řízení přístupu (IAM)** zobrazíte nastavení řízení přístupu k účtu. Vyberte **přiřazení rolí** kartu pro zobrazení seznamu přiřazení rolí.
    
        ![Snímek obrazovky zobrazující nastavení řízení přístupu úložiště](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
    1. Klikněte na tlačítko **přidat přiřazení role** tlačítko Přidat novou roli.
    1. V **přidat přiřazení role** okna, vyberte **Přispěvatel dat objektu Blob služby Storage (Preview)** role. Vyberte předplatné, které má spravované identity a účtu úložiště. V dalším kroku prohledávat při vyhledávání uživatelsky přiřazené spravovanou identitu, který jste vytvořili dříve. Nakonec vyberte spravovanou identitu a zobrazí se v části **Vybraní členové**.
    
        ![Snímek obrazovky ukazující, jak přiřadit roli RBAC](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role2.png)
    
    1. Klikněte na **Uložit**. Uživatelsky přiřazené identity, který jste vybrali, je teď uvedený v části **Přispěvatel** role.

    1. Po tomto počátečním nastavení je dokončeno, můžete vytvořit cluster na portálu. Cluster musí být ve stejné oblasti Azure jako účet úložiště. V **úložiště** části nabídky vytváření clusteru, vyberte následující možnosti:
        
        * Pro **typ primárního úložiště**, klikněte na tlačítko **Azure Data Lake Storage Gen2**.
        * V části **vyberte účet úložiště**, vyhledejte a vyberte nově vytvořený účet úložiště Data Lake Storage Gen2.
        
            ![Nastavení úložiště pro Data Lake Storage Gen2 pomocí Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
        
        * V části **Identity** vyberte správné předplatné a nově vytvořený uživatel přiřazený spravovaná identita.
        
            ![Nastavení identity pro Azure HDInsight pomocí Data Lake Storage Gen2](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Řízení přístupu pro Data Lake Storage Gen2 v HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Jaké druhy oprávnění, která podporuje Data Lake Storage Gen2?

Azure Data Lake Storage Gen2 implementuje model řízení přístupu, který podporuje Azure na základě řízení přístupu Role (RBAC) a seznamy řízení přístupu POSIX jako (ACL). Seznamy řízení přístupu data Lake Storage Gen1 podporována pouze pro řízení přístupu k datům.

Azure na základě rolí řízení přístupu (RBAC) používá přiřazení rolí pro efektivní použití sady oprávnění pro uživatele, skupiny nebo instanční objekty pro prostředky Azure. Obvykle tyto prostředky Azure, jsou přednastaveny omezené prostředky nejvyšší úrovně (například účty Azure Storage). Pro Azure Storage a Azure Data Lake Storage Gen2 tento mechanismus rozšířilo a prostředek systému souborů.

 Další informace o oprávnění k souborům pomocí RBAC najdete v tématu [řízení přístupu na základě Role v Azure (RBAC)](/../storage/blobs/data-lake-storage-access-control#azure-role-based-access-control-rbac.md).

Další informace o oprávnění k souborům pomocí seznamů ACL, naleznete v tématu [seznamy řízení přístupu na soubory a adresáře](/../storage/blobs/data-lake-storage-access-control#access-control-lists-on-files-and-directories.md).


### <a name="how-do-i-control-access-to-my-data-in-gen2"></a>Jak řídit přístup k Gen2 má data?

Možnost pro váš cluster HDInsight pro přístup k souborům v Data Lake Storage Gen2 je řízen pomocí spravované identity. Spravovaná identita je identita zaregistrovaný ve službě Azure AD, jehož přihlašovací údaje se spravují přes Azure. Nemusíte registrovat instančních objektů ve službě Azure AD a udržovat přihlašovací údaje, jako třeba certifikáty.

Existují dva typy identit spravovaných služeb Azure: systém přiřadil a uživatelsky přiřazené. Azure HDInsight používá spravované uživatelsky přiřazené identity pro přístup k Azure Data Lake Storage Gen2. Uživatel přiřazenou spravovaná identita je vytvořen jako samostatný prostředek Azure. Prostřednictvím procesu vytvoření Azure vytvoří identitu v tenantovi Azure AD důvěryhodném pro použité předplatné. Po vytvoření identity je možné ji přiřadit k jedné nebo několika instancím služeb Azure. Životní cyklus identity přiřazené uživatelem se spravuje nezávisle na životním cyklu instancí služeb Azure, ke kterým je přiřazená. Další informace o spravovaných identit najdete v tématu [jak spravovaných identit pro práci s prostředky Azure](/../active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka.md).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-using-hive-or-other-services"></a>Jak nastavit oprávnění pro uživatele Azure AD k dotazování dat v Data Lake Storage Gen2 pomocí Hive nebo jiných služeb?

Pomocí skupin zabezpečení Azure AD jako přiřazené instanční objekt v seznamech ACL. Nepřiřazujte přímo jednotlivým uživatelům nebo instančních objektů s využitím přístupová oprávnění k souboru. Při použití skupin zabezpečení pro řízení toku oprávnění, můžete přidávat a odebírat uživatele nebo instanční objekty bez znovu použitím seznamů řízení přístupu k celé adresářovou strukturu. Stačí jenom přidat nebo odebrat uživatele z příslušné skupiny zabezpečení Azure AD. Seznamy ACL nejsou děděna a proto obnovení seznamů řízení přístupu vyžaduje aktualizaci jejich seznamu ACL u všech souborů a podadresáře.

## <a name="next-steps"></a>Další postup

* [Použití Azure Data Lake Storage Gen2 Preview s využitím clusterů Azure HDInsight](/../storage/blobs/data-lake-storage-use-hdi-cluster.md)
* [Aktualizace integrace Azure HDInsight s Data Lake Storage Gen2 preview – řízení přístupu a zabezpečení](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Úvod do služby Azure Data Lake Storage Gen2 ve verzi Preview](/../storage/blobs/data-lake-storage-introduction.md)