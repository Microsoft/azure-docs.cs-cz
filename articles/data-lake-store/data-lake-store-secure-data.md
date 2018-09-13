---
title: Zabezpečení dat uložených v Azure Data Lake Storage Gen1 | Dokumentace Microsoftu
description: Další informace o zabezpečení dat v Azure Data Lake Storage Gen1 pomocí skupiny a seznamy řízení přístupu
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 92135c005779bf262c16a132b6dd6b7dace8d450
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44717790"
---
# <a name="securing-data-stored-in-azure-data-lake-storage-gen1"></a>Zabezpečení dat uložených v Azure Data Lake Storage Gen1
Zabezpečení dat v Azure Data Lake Storage Gen1 je přístup založený na třech krocích.  Jak na základě rolí přístup ovládacího prvku (RBAC) a seznamy řízení přístupu (ACL) musí být nastavena na plně povolit přístup k datům pro uživatele a skupiny zabezpečení.

1. Začněte vytvořením skupiny zabezpečení v Azure Active Directory (AAD). Tyto skupiny zabezpečení slouží k implementaci řízení přístupu na základě role (RBAC) na webu Azure portal. Další informace najdete v tématu [řízení přístupu na základě rolí ve službě Microsoft Azure](../role-based-access-control/role-assignments-portal.md).
2. Přiřadíte k účtu Azure Data Lake Storage Gen1 skupin zabezpečení AAD. Tato volba určuje přístup k účtu Data Lake Storage Gen1 z portálu a správu operace z portálu nebo rozhraní API.
3. Přiřazení skupin zabezpečení AAD jako přístup seznamů řízení (ACL) v systému souborů Data Lake Store.
4. Kromě toho můžete také nastavit rozsah IP adres pro klienty, kteří můžou přistupovat k datům v Data Lake Store.

Tento článek obsahuje pokyny o tom, jak pomocí webu Azure portal k provedení výše uvedených úkolech. Podrobné informace o způsob implementace zabezpečení na úrovni účtu a dat v Data Lake Store najdete v tématu [zabezpečení v Azure Data Lake Store](data-lake-store-security-overview.md). Podrobné informace o informace o způsobu implementace seznamů řízení přístupu v Azure Data Lake Store najdete v tématu [přehled o řízení přístupu v Data Lake Store](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Storage Gen1**. Pokyny k jeho vytvoření najdete v tématu [Začínáme s Azure Data Lake Store](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Vytvoření skupin zabezpečení v Azure Active Directory
Pokyny k vytváření skupin zabezpečení AAD a jak přidat uživatele do skupiny, najdete v části [Správa skupin zabezpečení ve službě Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

> [!NOTE] 
> Uživatelé a další skupiny můžete přidat do skupiny ve službě Azure AD pomocí webu Azure portal. Nicméně, chcete-li přidat hlavní název služby do skupiny, použijte [modulu prostředí PowerShell Azure AD](../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-azure-data-lake-store-accounts"></a>Přiřazení uživatelů nebo skupin zabezpečení pro účty Azure Data Lake Store
Když přiřadíte uživatele nebo skupiny zabezpečení pro účty Azure Data Lake Store, můžete řídit přístup ke operace správy účtu pomocí rozhraní API Azure Resource Manageru a webu Azure portal. 

1. Otevřete účet Azure Data Lake Store. V levém podokně klikněte na tlačítko **všechny prostředky**a potom v okně všechny prostředky klikněte na název účtu, ke kterému chcete přiřadit uživatele nebo skupinu zabezpečení.

2. V okně účtu Data Lake Store, klikněte na tlačítko **řízení přístupu (IAM)**. V okně ve výchozím nastavení obsahuje vlastníky předplatného jako vlastník.
   
    ![Skupinu zabezpečení přiřadit k účtu Azure Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.select.user.icon.png "skupinu zabezpečení přiřadit k účtu Azure Data Lake Storage Gen1")

3. V **řízení přístupu (IAM)** okna, klikněte na tlačítko **přidat** otevřít **přidat oprávnění** okno. V **přidat oprávnění** okno, vyberte **Role** pro uživatele nebo skupiny. Hledat skupiny zabezpečení, kterou jste vytvořili dříve v Azure Active Directory a vyberte ji. Pokud máte mnoho uživatelů a skupin pro vyhledávání z použít **vyberte** textového pole k filtrování podle názvu skupiny. 
   
    ![Přidání role pro uživatele](./media/data-lake-store-secure-data/adl.add.user.1.png "přidat roli pro uživatele")
   
    **Vlastníka** a **Přispěvatel** role poskytují přístup k různým funkcím pro správu v účtu data lake. Uživatelé, kteří budou pracovat s daty v data lake, ale stále potřeba, chcete-li zobrazit informace o účtu pro správu, budete moci přidat do **čtečky** role. Rozsah z těchto rolí je omezen na operacích správy vztahující se k účtu Azure Data Lake Storage Gen1.
   
    Pro operace s daty oprávnění systému souborů jednotlivých definovat, co můžete dělat uživatele. Proto uživatele s role čtenáře můžete jenom zobrazit nastavení pro správu, které jsou přidružené k účtu, ale může potenciálně číst a zapisovat data podle oprávnění systému souborů přiřazené. Oprávnění systému souborů data Lake Store jsou popsány v [přiřadit skupinu zabezpečení jako seznamů ACL do systému souborů Azure Data Lake Storage Gen1](#filepermissions).

    > [!IMPORTANT]
    > Pouze **vlastníka** role automaticky povolí přístupu k systému souborů. **Přispěvatel**, **čtečky**, a všechny ostatní role vyžadovat seznamů ACL umožňuje všechny úrovně přístupu do složek a souborů.  **Vlastníka** role poskytuje superuživatele oprávnění souborů a složek, které prostřednictvím seznamů ACL se nedá přepsat. Další informace o mapování zásady RBAC pro přístup k datům najdete v tématu [RBAC pro správu účtu](data-lake-store-security-overview.md#rbac-for-account-management).

4. Pokud chcete přidat skupiny/uživatele, který není uveden v **přidat oprávnění** okně můžete pozvat je tak, že zadáte e-mailová adresa v **vyberte** textové pole a jejich výběrem ze seznamu.
   
    ![Přidat skupinu zabezpečení](./media/data-lake-store-secure-data/adl.add.user.2.png "přidat skupinu zabezpečení")
   
5. Klikněte na **Uložit**. Měli byste vidět skupiny zabezpečení přidat, jak je znázorněno níže.
   
    ![Skupiny zabezpečení přidat](./media/data-lake-store-secure-data/adl.add.user.3.png "přidá skupinu zabezpečení")

6. Vaše skupiny zabezpečení pro uživatele má nyní přístup k účtu Azure Data Lake Storage Gen1. Pokud chcete poskytnout přístup k určitým uživatelům, můžete je přidat do skupiny zabezpečení. Podobně pokud chcete odvolat přístup pro uživatele, můžete ho odebrat ze skupiny zabezpečení. Můžete také přiřadit více skupin zabezpečení k účtu. 

## <a name="filepermissions"></a>Přiřazení uživatelů nebo skupin zabezpečení jako seznamů ACL do systému souborů Azure Data Lake Storage Gen1
Přiřazením uživateli nebo skupinami zabezpečení do systému souborů Azure Data Lake je nastavit řízení přístupu s daty uloženými v Azure Data Lake Store.

1. V okně účtu Data Lake Storage Gen1, klikněte na tlačítko **Průzkumník dat**.
   
    ![Zobrazit data pomocí Průzkumníku dat](./media/data-lake-store-secure-data/adl.start.data.explorer.png "zobrazit data pomocí Průzkumníku dat")
2. V **Průzkumník dat** okna, klikněte na složku, pro kterou chcete nakonfigurovat seznam ACL a potom klikněte na tlačítko **přístup**. Pokud chcete přiřadit seznamy řízení přístupu k souboru, musíte nejprve klikněte na soubor zobrazit jeho náhled a potom klikněte na **přístup** z **náhled souboru** okno.
   
    ![Nastavení seznamů ACL v systému souborů Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "nastavit seznamy ACL v systému souborů Data Lake")
3. **Přístup** okno seznam vlastníků a přiřadit oprávnění již přiřazen do kořenového adresáře. Klikněte na tlačítko **přidat** ikonu, čímž přidáte další přístupové seznamy ACL.
    > [!IMPORTANT]
    > Nastavení přístupových oprávnění pro jeden soubor neuděluje nutně přístup uživatele nebo skupiny do tohoto souboru. Cesta k souboru musí být přístupné pro přiřazené uživatele nebo skupiny. Další informace a příklady najdete v tématu [běžných scénářů souvisejících s oprávněními](data-lake-store-access-control.md#common-scenarios-related-to-permissions).
   
    ![Seznam standardních a vlastních přístup](./media/data-lake-store-secure-data/adl.acl.2.png "seznam standardních a vlastních přístup")
   
   * **Vlastníky** a **všichni ostatní** poskytují přístup k systému UNIX, kde můžete určit čtení, zápis, provedení (rwx) tři třídy jedinečných uživatelů: vlastník, skupiny a další.
   * **Přiřadit oprávnění** odpovídá seznamech ACL POSIX, která vám umožní nastavit oprávnění pro konkrétní pojmenované uživatele nebo skupiny nad rámec vlastníka souboru nebo skupiny. 
     
     Další informace najdete v tématu [seznamy řízení přístupu HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Další informace o způsobu implementace seznamů ACL v Data Lake Storage Gen1 najdete v tématu [řízení přístupu v Data Lake Storage Gen1](data-lake-store-access-control.md).
4. Klikněte na tlačítko **přidat** ikony otevřete **přiřadit oprávnění** okno. V tomto okně klikněte na tlačítko **vyberte uživatele nebo skupinu**a potom v **vyberte uživatele nebo skupinu** okno, vyhledejte skupinu zabezpečení, kterou jste vytvořili dříve v Azure Active Directory. Pokud máte spoustu skupin má vyhledat, použijte k filtrování názvu skupiny do textového pole v horní části. Klikněte na skupinu, kterou chcete přidat a potom klikněte na tlačítko **vyberte**.
   
    ![Přidat skupinu](./media/data-lake-store-secure-data/adl.acl.3.png "přidat skupinu")
5. Klikněte na tlačítko **vyberte oprávnění**vyberte oprávnění, zda bude použito oprávnění k rekurzivnímu a určuje, zda chcete přiřadit oprávnění jako přístupový seznam ACL, výchozí seznam ACL, nebo obojí. Klikněte na **OK**.
   
    ![Přiřadit oprávnění k seskupení](./media/data-lake-store-secure-data/adl.acl.4.png "přiřadit oprávnění k seskupení")
   
    Další informace o oprávněních v Data Lake Storage Gen1 a výchozí/přístupové seznamy ACL, naleznete v tématu [řízení přístupu v Data Lake Storage Gen1](data-lake-store-access-control.md).
6. Po kliknutí na tlačítko **Ok** v **vyberte oprávnění** okno, nově přidané skupiny a příslušná oprávnění bude nyní obsažena v **přístup** okno.
   
    ![Přiřadit oprávnění k seskupení](./media/data-lake-store-secure-data/adl.acl.5.png "přiřadit oprávnění k seskupení")
   
   > [!IMPORTANT]
   > V aktuální verzi, může mít až 28 položek v rámci **přiřazena oprávnění**. Pokud chcete přidat více než 28 uživatele, měli byste vytvořit skupiny zabezpečení přidat uživatele do skupiny zabezpečení, přidejte poskytují přístup do těchto skupin zabezpečení pro účet Data Lake Storage Gen1.
   > 
   > 
7. V případě potřeby můžete také upravit oprávnění k přístupu po přidání skupiny. Zaškrtněte políčko pro každý typ oprávnění (čtení, zápis, Execute) založené na tom, jestli chcete odebrat nebo přiřaďte oprávnění ke skupině zabezpečení nebo zrušte. Klikněte na tlačítko **Uložit** a uložte změny, nebo **zahodit** vrátit zpět změny.

## <a name="set-ip-address-range-for-data-access"></a>Nastavte rozsah IP adres pro přístup k datům
Azure Data Lake Storage Gen1 umožňuje další uzamčení přístupu s vaším úložištěm dat na úrovni sítě. Můžete povolit bránu firewall, zadejte IP adresu nebo definovat rozsah IP adres pro důvěryhodného klienty. Po povolení pouze klienti, kteří mají IP adresy v rámci definovaného rozsahu můžete připojit k úložišti.

![Přístup k nastavení brány firewall a IP](./media/data-lake-store-secure-data/firewall-ip-access.png "brány Firewall, nastavení a IP adresy")

## <a name="remove-security-groups-for-an-azure-data-lake-storage-gen1-account"></a>Odebrat skupiny zabezpečení pro účet Azure Data Lake Storage Gen1
Když odeberete z účtů Azure Data Lake Storage Gen1 skupiny zabezpečení, se mění přístup pouze pro operace správy účtu pomocí webu Azure Portal a rozhraní API Azure Resource Manageru.  

Přístup k datům se nemění a je dál spravovaný přístup seznamy ACL.  Výjimkou jsou uživatelé nebo skupiny v roli vlastníky.  Tito uživatelé už nejsou odebírat vlastníky role uživatele nebo skupiny a jejich přístup se překlopil na nastavení seznamu ACL pro přístup. 

1. V okně účtu Data Lake Storage Gen1, klikněte na tlačítko **řízení přístupu (IAM)**. 
   
    ![Skupinu zabezpečení přiřadit k účtu Azure Data Lake](./media/data-lake-store-secure-data/adl.select.user.icon.png "skupinu zabezpečení přiřadit k účtu Azure Data Lake")
2. V **řízení přístupu (IAM)** okna, klikněte na skupiny zabezpečení, kterou chcete odebrat. Klikněte na tlačítko **odebrat**.
   
    ![Odebrat skupinu zabezpečení](./media/data-lake-store-secure-data/adl.remove.group.png "odebrat skupinu zabezpečení")

## <a name="remove-security-group-acls-from-azure-data-lake-storage-gen1-file-system"></a>Odebrat skupinu zabezpečení seznamů ACL systému souborů Azure Data Lake Storage Gen1
Odebrat skupinu zabezpečení seznamů ACL systému souborů Azure Data Lake Store, změníte přístup k datům v Data Lake Storage Gen1.

1. V okně účtu Data Lake Storage Gen1, klikněte na tlačítko **Průzkumník dat**.
   
    ![Vytváření adresářů v účtu Data Lake](./media/data-lake-store-secure-data/adl.start.data.explorer.png "vytvoření adresářů v účtu Data Lake")
2. V **Průzkumník dat** okna, klikněte na složku, pro kterou chcete odebrat seznam řízení přístupu a potom klikněte na **přístup**. K odebrání seznamů ACL pro soubor, musí nejprve klikněte na soubor zobrazit jeho náhled a potom klikněte na **přístup** z **náhled souboru** okno. 
   
    ![Nastavení seznamů ACL v systému souborů Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "nastavit seznamy ACL v systému souborů Data Lake")
3. V **přístup** okna, klikněte na skupinu zabezpečení, kterou chcete odebrat. V **přístup podrobnosti** okna, klikněte na tlačítko **odebrat**.
   
    ![Přiřadit oprávnění k seskupení](./media/data-lake-store-secure-data/adl.remove.acl.png "přiřadit oprávnění k seskupení")

## <a name="see-also"></a>Další informace najdete v tématech
* [Přehled služby Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Kopírování dat z Azure Storage BLOB do Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Použití Azure Data Lake Analytics s Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Začínáme se službou Data Lake Storage Gen1 pomocí Powershellu](data-lake-store-get-started-powershell.md)
* [Začínáme se službou Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Zobrazení protokolů diagnostiky pro Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md)

