---
title: Zabezpečení dat uložených v Azure Data Lake Store | Microsoft Docs
description: Zjistěte, jak k zabezpečení dat v Azure Data Lake Store pomocí skupin a seznamy řízení přístupu
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
ms.openlocfilehash: 5b83f02c55d0aa7b2e122d7fc8c9ef5734cdd924
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="securing-data-stored-in-azure-data-lake-store"></a>Zabezpečení dat uložených v Azure Data Lake Store
Zabezpečení dat v Azure Data Lake Store je přístup třech krocích.  Jak na základě rolí přístup řízení (RBAC) a seznamy řízení přístupu (ACL) musí být nastavena na plně povolit přístup k datům pro uživatele a skupiny zabezpečení.

1. Začněte vytvořením skupiny zabezpečení v Azure Active Directory (AAD). Tyto skupiny zabezpečení slouží k implementaci řízení přístupu na základě role (RBAC) na portálu Azure. Další informace najdete v tématu [řízení přístupu na základě rolí ve službě Microsoft Azure](../role-based-access-control/role-assignments-portal.md).
2. Skupiny zabezpečení AAD přiřadíte k účtu Azure Data Lake Store. To řídí přístup k účtu Data Lake Store z portál a management operace z portálu nebo rozhraní API.
3. Přiřazení skupiny zabezpečení AAD jako přístup seznamy řízení (ACL) v systému souborů Data Lake Store.
4. Kromě toho můžete také nastavit rozsah IP adres pro klienty, kteří měli přístup k datům v Data Lake Store.

Tento článek obsahuje pokyny o tom, jak pomocí portálu Azure k provádění úloh výše. Podrobnější informace o tom, jak Data Lake Store implementuje zabezpečení na úrovni účtu a data, najdete v části [zabezpečení v Azure Data Lake Store](data-lake-store-security-overview.md). Nabídnout detailní informace o tom, jak jsou implementované seznamy ACL v Azure Data Lake Store najdete v tématu [přehled o řízení přístupu v Data Lake Store](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Store**. Pokyny o tom, jak vytvořit najdete v tématu [Začínáme s Azure Data Lake Store](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Vytvoření skupin zabezpečení v Azure Active Directory
Pokyny k vytváření skupin zabezpečení AAD a jak přidat uživatele do skupiny, najdete v části [Správa skupin zabezpečení v Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

> [!NOTE] 
> Uživatelé a další skupiny můžete přidat do skupiny ve službě Azure AD pomocí portálu Azure. Ale chcete-li přidat hlavní název služby do skupiny, použijte [modulu Azure AD PowerShell](../active-directory/active-directory-accessmanagement-groups-settings-v2-cmdlets.md).
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-azure-data-lake-store-accounts"></a>Přiřadit uživatele nebo skupiny zabezpečení účtů Azure Data Lake Store
Když přiřadíte uživatele nebo skupiny zabezpečení účtů Azure Data Lake Store, můžete řídit přístup ke operace správy v účtu pomocí portálu Azure a rozhraní API Správce Azure Resource Manager. 

1. Otevřete účet Azure Data Lake Store. V levém podokně klikněte na tlačítko **všechny prostředky**a potom v okně všechny prostředky, klikněte na název účtu, ke kterému chcete přiřadit uživatele nebo novou skupinu zabezpečení.

2. V okně účtu Data Lake Store, klikněte na tlačítko **řízení přístupu (IAM)**. V okně ve výchozím nastavení jsou vlastníky předplatného uvedené jako vlastník.
   
    ![Přiřazení skupiny zabezpečení k účtu Azure Data Lake Store](./media/data-lake-store-secure-data/adl.select.user.icon.png "skupinu zabezpečení přiřadit k účtu Azure Data Lake Store")

3. V **řízení přístupu (IAM)** okně klikněte na tlačítko **přidat** otevřete **přidat oprávnění** okno. V **přidat oprávnění** vyberte **Role** pro uživatele nebo skupinu. Hledat skupiny zabezpečení, kterou jste vytvořili dříve v Azure Active Directory a vyberte jej. Pokud máte mnoho uživatelů a skupin pro vyhledávání z, použijte **vyberte** textové pole k filtrovat podle názvu skupiny. 
   
    ![Přidání role pro uživatele](./media/data-lake-store-secure-data/adl.add.user.1.png "přidání role pro uživatele")
   
    **Vlastníka** a **Přispěvatel** role umožňují přístup k různým funkcím pro správu v účtu data lake. Pro uživatele, kteří budou pracovat s daty v data lake, ale stále potřeba zobrazit informace o účtu pro správu, můžete přidat je do **čtečky** role. Rozsah těchto rolí je omezený na operace správy vztahující se k účtu Azure Data Lake Store.
   
    Pro datové operace oprávnění systému souborů jednotlivých definovat, co můžete dělat uživatele. Proto uživatele s role Čtenář můžete zobrazit jenom nastavení pro správu, které jsou přidružené k účtu, ale můžete potenciálně čtení a zápisu dat podle přiřazeného oprávnění systému souborů. Oprávnění systému souborů data Lake Store jsou popsány v [skupinu zabezpečení přiřadit jako seznamy ACL pro systém souborů Azure Data Lake Store](#filepermissions).

    > [!IMPORTANT]
    > Pouze **vlastníka** role automaticky povolí přístupu k systému souborů. **Přispěvatel**, **čtečky**, a všechny ostatní role vyžadovat seznamy ACL, chcete-li povolit žádné úroveň přístupu k složek a souborů.  **Vlastníka** role poskytuje superuživatele souborům a složkám, které nebylo možné přepsat pomocí seznamů řízení přístupu. Další informace o tom, jak zásady RBAC mapování na přístup k datům, najdete v části [RBAC pro správu účtu](data-lake-store-security-overview.md#rbac-for-account-management).

4. Pokud chcete přidat skupinu nebo uživatele, který není uveden ve **přidat oprávnění** okně můžete pozvat je zadáním e-mailové adresy ve **vyberte** textového pole a potom je vyberete ze seznamu.
   
    ![Přidat skupinu zabezpečení](./media/data-lake-store-secure-data/adl.add.user.2.png "přidat skupinu zabezpečení")
   
5. Klikněte na **Uložit**. Měli byste vidět skupiny zabezpečení přidat, jak je uvedeno níže.
   
    ![Skupiny zabezpečení přidat](./media/data-lake-store-secure-data/adl.add.user.3.png "přidat skupinu zabezpečení")

6. Skupině zabezpečení nebo uživatelů nyní má přístup k účtu Azure Data Lake Store. Pokud chcete k poskytování přístupu konkrétním uživatelům, můžete je přidat do skupiny zabezpečení. Podobně pokud chcete odvolat přístup pro uživatele, můžete je odebrat ze skupiny zabezpečení. Víc skupin zabezpečení můžete také přiřadit k účtu. 

## <a name="filepermissions"></a>Přiřadit uživatele nebo skupiny zabezpečení jako seznamy řízení přístupu k systému souborů Azure Data Lake Store
Přiřazením zabezpečení uživatelů nebo skupin na systém souborů Azure Data Lake nastavit řízení přístupu na data uložená v Azure Data Lake Store.

1. V okně účtu Data Lake Store klikněte na možnost **Průzkumník dat**.
   
    ![Zobrazení dat pomocí Průzkumníku dat](./media/data-lake-store-secure-data/adl.start.data.explorer.png "zobrazení dat pomocí Průzkumníku dat")
2. V **Průzkumníku dat** okně klikněte na složku, pro který chcete konfigurovat seznam ACL a potom klikněte na **přístup**. Přiřadit seznamy řízení přístupu k souboru, musíte nejprve klikněte na soubor zobrazit jeho náhled a potom klikněte na **přístup** z **náhled souboru** okno.
   
    ![Nastavit seznamy ACL v systému souborů Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "nastavit seznamy ACL v Data Lake systému souborů")
3. **Přístup** uvádí vlastníci a přiřadit oprávnění již přiřazen do kořenového adresáře. Klikněte **přidat** ikonu, čímž přidáte další seznamy ACL přístupu.
    > [!IMPORTANT]
    > Nastavení oprávnění přístupu pro jeden soubor neuděluje nutně přístup uživatele nebo skupiny do tohoto souboru. Cesta k souboru musí být přístupné pro přiřazené uživatele nebo skupiny. Další informace a příklady naleznete v tématu [běžné scénáře související s oprávnění](data-lake-store-access-control.md#common-scenarios-related-to-permissions).
   
    ![Standardní a vlastní přístup](./media/data-lake-store-secure-data/adl.acl.2.png "standardní a vlastní přístup")
   
   * **Vlastníky** a **všichni ostatní** poskytovat přístup stylu systému UNIX, kde můžete určit číst, zapisovat, provést (rwx) třídy tři odlišné uživatelů: vlastník, skupiny a dalších.
   * **Oprávnění přiřazena** odpovídá POSIX seznamy ACL, které vám umožní nastavit oprávnění pro konkrétní jmenovaní uživatelé nebo skupiny nad rámec vlastník souboru nebo skupiny. 
     
     Další informace najdete v tématu [seznamy ACL HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Další informace o tom, jak jsou implementované seznamy ACL v Data Lake Store najdete v tématu [řízení přístupu v Data Lake Store](data-lake-store-access-control.md).
4. Klikněte **přidat** ikonu otevřete **přiřadit oprávnění** okno. V tomto okně klikněte na tlačítko **vyberte uživatele nebo skupiny**a potom v **vyberte uživatele nebo skupiny** okno, podívejte se na skupinu zabezpečení, kterou jste vytvořili dříve v Azure Active Directory. Pokud máte spoustu skupin vyhledávat od, použijte k filtrování název skupiny v horní části textového pole. Klikněte na skupinu, kterou chcete přidat a pak klikněte na **vyberte**.
   
    ![Přidat skupinu](./media/data-lake-store-secure-data/adl.acl.3.png "přidat skupinu")
5. Klikněte na tlačítko **vyberte oprávnění**, vyberte oprávnění, jestli bude použito oprávnění k rekurzivnímu a jestli chcete přiřadit oprávnění jako přístupový seznam ACL, výchozí seznam ACL, nebo obojí. Klikněte na **OK**.
   
    ![Přiřazení oprávnění k seskupení](./media/data-lake-store-secure-data/adl.acl.4.png "přiřadit oprávnění k seskupení")
   
    Další informace o oprávněních v Data Lake Store a seznamy ACL výchozí nebo přístupu najdete v tématu [řízení přístupu v Data Lake Store](data-lake-store-access-control.md).
6. Po kliknutí na **Ok** v **vyberte oprávnění** okno, nově přidané skupiny a přidružená tato oprávnění se teď uvedený v **přístup** okno.
   
    ![Přiřazení oprávnění k seskupení](./media/data-lake-store-secure-data/adl.acl.5.png "přiřadit oprávnění k seskupení")
   
   > [!IMPORTANT]
   > V aktuální verzi, může mít až 28 položky v rámci **oprávnění přiřazena**. Pokud chcete přidat více než 28 uživatele, měli byste vytvořit skupiny zabezpečení přidat uživatele do skupiny zabezpečení, přidejte poskytovat přístup do těchto skupin zabezpečení pro účet Data Lake Store.
   > 
   > 
7. V případě potřeby můžete také upravit přístupová oprávnění, po přidání skupiny. Zaškrtněte políčko pro každý typ oprávnění (pro čtení, zápisu, spouštění) založené na tom, zda chcete odebrat nebo přiřadit tato oprávnění ke skupině zabezpečení nebo zrušte. Klikněte na tlačítko **Uložit** a uložte změny, nebo **zahodit** vrátit zpět změny.

## <a name="set-ip-address-range-for-data-access"></a>Nastavit rozsah IP adres pro přístup k datům
Azure Data Lake Store umožňuje další uzamčení přístup k úložišti dat na úrovni sítě. Můžete povolit bránu firewall, zadejte IP adresu nebo zadejte rozsah IP adres pro klienty důvěryhodné. Po povolení pouze klienti, kteří mají IP adresy v definovaném rozsahu může připojit k úložišti.

![Nastavení brány firewall a IP přístup](./media/data-lake-store-secure-data/firewall-ip-access.png "nastavení a IP adresu brány Firewall")

## <a name="remove-security-groups-for-an-azure-data-lake-store-account"></a>Odebrání skupiny zabezpečení k účtu Azure Data Lake Store
Když odeberete skupin zabezpečení z účtů Azure Data Lake Store, jsou pouze změna přístup na operace správy v účtu, pomocí webu Azure Portal a rozhraní API Správce Azure Resource Manager.  

Přístup k datům se neliší a stále spravovaný přístup seznamy ACL.  Výjimkou jsou uživatelé nebo skupiny v roli vlastníky.  Uživatelé nebo skupiny odebral z role vlastníky již nejsou superuživatele a jejich přístup se vrátí k nastavení seznamu ACL přístupu. 

1. V okně účtu Data Lake Store, klikněte na tlačítko **řízení přístupu (IAM)**. 
   
    ![Přiřazení skupiny zabezpečení k účtu Azure Data Lake](./media/data-lake-store-secure-data/adl.select.user.icon.png "skupinu zabezpečení přiřadit k účtu Azure Data Lake")
2. V **řízení přístupu (IAM)** okně klikněte na skupiny zabezpečení, kterou chcete odebrat. Klikněte na tlačítko **odebrat**.
   
    ![Odebrat skupinu zabezpečení](./media/data-lake-store-secure-data/adl.remove.group.png "odebrat skupinu zabezpečení")

## <a name="remove-security-group-acls-from-azure-data-lake-store-file-system"></a>Odebrat skupinu zabezpečení seznamy ACL systému souborů Azure Data Lake Store
Když odeberete skupiny zabezpečení seznamy ACL systému souborů Azure Data Lake Store, změníte přístup k datům v Data Lake Store.

1. V okně účtu Data Lake Store klikněte na možnost **Průzkumník dat**.
   
    ![Vytváření adresářů v účtu Data Lake](./media/data-lake-store-secure-data/adl.start.data.explorer.png "vytváření adresářů v účtu Data Lake")
2. V **Průzkumníku dat** okně klikněte na složku, pro který chcete odebrat seznamu ACL a potom klikněte na **přístup**. K odebrání seznamů řízení přístupu k souboru, musíte nejprve klikněte na soubor zobrazit jeho náhled a potom klikněte na **přístup** z **náhled souboru** okno. 
   
    ![Nastavit seznamy ACL v systému souborů Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "nastavit seznamy ACL v Data Lake systému souborů")
3. V **přístup** okně klikněte na skupinu zabezpečení, kterou chcete odebrat. V **přístup podrobnosti** okně klikněte na tlačítko **odebrat**.
   
    ![Přiřazení oprávnění k seskupení](./media/data-lake-store-secure-data/adl.remove.acl.png "přiřadit oprávnění k seskupení")

## <a name="see-also"></a>Další informace najdete v tématech
* [Přehled Azure Data Lake Store](data-lake-store-overview.md)
* [Kopírování dat z úložiště objektů BLOB Azure do Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
* [Použití Azure Data Lake Analytics se službou Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight se službou Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Začínáme s Data Lake Store pomocí prostředí PowerShell](data-lake-store-get-started-powershell.md)
* [Začínáme s Data Lake Store pomocí sady .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Zobrazení protokolů diagnostiky pro Data Lake Store](data-lake-store-diagnostic-logs.md)

