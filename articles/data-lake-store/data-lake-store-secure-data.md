---
title: Zabezpečení dat uložených v Azure Data Lake Storage Gen1 | Dokumenty společnosti Microsoft
description: Zjistěte, jak zabezpečit data v Azure Data Lake Storage Gen1 pomocí skupin a seznamů řízení přístupu
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: cebdff5ed233516683df3330e8fd3332ded664e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260303"
---
# <a name="securing-data-stored-in-azure-data-lake-storage-gen1"></a>Zabezpečení dat uložených ve službě Azure Data Lake Storage Gen1
Zabezpečení dat v Azure Data Lake Storage Gen1 je třístupňový přístup.  Řízení přístupu na základě rolí (RBAC) a seznamy řízení přístupu (ACL) musí být nastaveny tak, aby plně umožňovaly přístup k datům pro uživatele a skupiny zabezpečení.

1. Začněte vytvořením skupin zabezpečení ve službě Azure Active Directory (AAD). Tyto skupiny zabezpečení se používají k implementaci řízení přístupu na základě rolí (RBAC) na webu Azure Portal. Další informace najdete [v tématu Řízení přístupu na základě rolí v Microsoft Azure](../role-based-access-control/role-assignments-portal.md).
2. Přiřaďte skupiny zabezpečení AAD k účtu Data Lake Storage Gen1. To řídí přístup k účtu Data Lake Storage Gen1 z portálu a operace správy z portálu nebo rozhraní API.
3. Přiřaďte skupiny zabezpečení AAD jako seznamy řízení přístupu (ACL) v systému souborů Data Lake Storage Gen1.
4. Kromě toho můžete také nastavit rozsah IP adres pro klienty, kteří mají přístup k datům v datovém úložišti Data Lake Storage Gen1.

Tento článek obsahuje pokyny, jak používat portál Azure k provádění výše uvedených úkolů. Podrobné informace o tom, jak Data Lake Storage Gen1 implementuje zabezpečení na úrovni účtu a dat, najdete [v tématu Zabezpečení v Azure Data Lake Storage Gen1](data-lake-store-security-overview.md). Podrobné informace o tom, jak jsou akly implementovány v gen1 úložiště datového jezera, naleznete [v tématu Přehled řízení přístupu v úložišti datových jezer Gen1](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Gen1 úložiště datového jezera**. Pokyny k jeho vytvoření najdete v tématu [Začínáme s Azure Data Lake Storage Gen1.](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Vytvoření skupin zabezpečení ve službě Azure Active Directory
Pokyny k vytvoření skupin zabezpečení AAD a přidání uživatelů do skupiny najdete [v tématu Správa skupin zabezpečení ve službě Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

> [!NOTE] 
> Uživatele i další skupiny můžete přidat do skupiny ve službě Azure AD pomocí portálu Azure. Chcete-li však přidat instanční objekt do skupiny, použijte [modul Prostředí PowerShell služby Azure AD](../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-data-lake-storage-gen1-accounts"></a>Přiřazení uživatelů nebo skupin zabezpečení k účtům Data Lake Storage Gen1
Když přiřadíte uživatele nebo skupiny zabezpečení k účtům Data Lake Storage Gen1, řídíte přístup k operacím správy na účtu pomocí portálu Azure portal a Azure Resource Manager API. 

1. Otevřete účet Data Lake Storage Gen1. V levém podokně klikněte na **Všechny prostředky**a potom v okně Všechny prostředky klikněte na název účtu, ke kterému chcete přiřadit uživatele nebo skupinu zabezpečení.

2. V okně účtu Data Lake Storage Gen1 klikněte na **Řízení přístupu (IAM).** Okno ve výchozím nastavení uvádí vlastníky předplatného jako vlastníka.
   
    ![Přiřazení skupiny zabezpečení k účtu Azure Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.select.user.icon1.png "Přiřazení skupiny zabezpečení k účtu Azure Data Lake Storage Gen1")

3. V okně **Řízení přístupu (IAM)** klikněte na **Přidat** a otevřete okno **Přidat oprávnění.** V okně **Přidat oprávnění** vyberte **roli** pro uživatele nebo skupinu. Vyhledejte skupinu zabezpečení, kterou jste vytvořili dříve ve službě Azure Active Directory, a vyberte ji. Pokud máte hodně uživatelů a skupin, ze kterých chcete vyhledávat, **vyfiltrujte** podle názvu skupiny textové pole Vybrat. 
   
    ![Přidání role pro uživatele](./media/data-lake-store-secure-data/adl.add.user.1.png "Přidání role pro uživatele")
   
    Role **Vlastník** a **Přispěvatel** poskytují přístup k různým funkcím správy na účtu datového jezera. Pro uživatele, kteří budou pracovat s daty v datovém jezeře, ale stále potřebují zobrazit informace o správě účtu, je můžete přidat do role **Čtečka.** Rozsah těchto rolí je omezen na operace správy související s účtem Data Lake Storage Gen1.
   
    Pro datové operace definují jednotlivá oprávnění systému souborů, co mohou uživatelé dělat. Uživatel s rolí Čtečka proto může zobrazit pouze nastavení správy přidružená k účtu, ale může potenciálně číst a zapisovat data na základě oprávnění systému souborů, která jsou jim přiřazena. Oprávnění souborového systému Data Lake Storage Gen1 jsou popsána na adrese [Přiřadit skupinu zabezpečení jako aklů k systému souborů Azure Data Lake Storage Gen1](#filepermissions).

    > [!IMPORTANT]
    > Přístup k systému souborů automaticky povolí pouze role **Vlastník.** **Přispěvatel**, **čtenář**a všechny ostatní role vyžadují seznamy AC, aby bylo možné povolit jakoukoli úroveň přístupu ke složkám a souborům.  Role **Vlastník** poskytuje oprávnění k souborům a složkám superuživatele, která nelze přepsat prostřednictvím seznamů ACL. Další informace o tom, jak zásady RBAC mapují přístup k datům, naleznete v [tématu RBAC for account management](data-lake-store-security-overview.md#rbac-for-account-management).

4. Pokud chcete přidat skupinu nebo uživatele, který není uveden v okně **Přidat oprávnění,** můžete je pozvat zadáním jejich e-mailové adresy do textového pole **Vybrat** a pak je vybrat ze seznamu.
   
    ![Přidání skupiny zabezpečení](./media/data-lake-store-secure-data/adl.add.user.2.png "Přidání skupiny zabezpečení")
   
5. Klikněte na **Uložit**. Měli byste vidět skupinu zabezpečení přidán, jak je uvedeno níže.
   
    ![Přidána skupina zabezpečení](./media/data-lake-store-secure-data/adl.add.user.3.png "Přidána skupina zabezpečení")

6. Vaše skupina uživatelů a zabezpečení má nyní přístup k účtu Data Lake Storage Gen1. Pokud chcete poskytnout přístup konkrétním uživatelům, můžete je přidat do skupiny zabezpečení. Podobně pokud chcete odvolat přístup pro uživatele, můžete jej odebrat ze skupiny zabezpečení. K účtu můžete také přiřadit více skupin zabezpečení. 

## <a name="assign-users-or-security-groups-as-acls-to-the-data-lake-storage-gen1-file-system"></a><a name="filepermissions"></a>Přiřazení uživatelů nebo skupin zabezpečení jako aklů k systému souborů Data Lake Storage Gen1
Přiřazením skupin uživatelů/zabezpečení k systému souborů Data Lake Storage Gen1 nastavíte řízení přístupu k datům uloženým v programu Data Lake Storage Gen1.

1. V okně účtu Data Lake Storage Gen1 klikněte na **Průzkumník dat**.
   
    ![Zobrazení dat pomocí Průzkumníka dat](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Zobrazení dat pomocí Průzkumníka dat")
2. V okně **Průzkumníka dat** klikněte na složku, pro kterou chcete nakonfigurovat přístupový soubor, a potom klepněte na **tlačítko Access**. Chcete-li k souboru přiřadit alokace AC, musíte nejprve klepnout na soubor, abyste ho zobrazili, a pak klepněte na **položku Access** z okna **Náhled souboru.**
   
    ![Nastavení aklů v systému souborů Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.acl.1.png "Nastavení aklů v systému souborů Data Lake Storage Gen1")
3. Okno **Access** obsahuje seznam vlastníků a přiřazených oprávnění, která jsou již přiřazena ke kořenovému adresáři. Kliknutím na ikonu **Přidat** přidejte další přístupové položky Access.
    > [!IMPORTANT]
    > Nastavení přístupových oprávnění pro jeden soubor nemusí nutně udělit uživateli nebo skupině přístup k tomuto souboru. Cesta k souboru musí být přístupná přiřazenému uživateli nebo skupině. Další informace a příklady naleznete [v tématu Běžné scénáře související s oprávněními](data-lake-store-access-control.md#common-scenarios-related-to-permissions).
   
    ![Seznam standardního a vlastního přístupu](./media/data-lake-store-secure-data/adl.acl.2.png "Seznam standardního a vlastního přístupu")
   
   * **Vlastníci** a **Všichni ostatní** poskytují přístup ve stylu unixu, kde zadáte čtení, zápis, spuštění (rwx) do tří odlišných tříd uživatelů: vlastník, skupina a další.
   * **Přiřazená oprávnění** odpovídají seznamům ACS POSIX, které umožňují nastavit oprávnění pro konkrétní pojmenované uživatele nebo skupiny mimo vlastníka nebo skupinu souboru. 
     
     Další informace naleznete v [tématu HDFS ACLs](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Další informace o tom, jak jsou implementovány akly v datovém úložišti jezera Gen1, naleznete [v tématu Řízení přístupu v úložišti datových jezer Gen1](data-lake-store-access-control.md).
4. Kliknutím na ikonu **Přidat** otevřete okno **Přiřadit oprávnění.** V tomto okně klikněte na **Vybrat uživatele nebo skupinu**a potom v okně **Vybrat uživatele nebo skupinu** vyhledejte skupinu zabezpečení, kterou jste vytvořili dříve ve službě Azure Active Directory. Pokud máte hodně skupin, ze kterých chcete vyhledávat, vyfiltrujte podle názvu skupiny textové pole v horní části. Klikněte na skupinu, kterou chcete přidat, a potom klikněte na **Vybrat**.
   
    ![Přidání skupiny](./media/data-lake-store-secure-data/adl.acl.3.png "Přidání skupiny")
5. Klepněte na **tlačítko Vybrat oprávnění**, vyberte oprávnění, zda mají být oprávnění použita pro rekurzivně a zda chcete přiřadit oprávnění jako přístupový přístupový přístupový kód, výchozí přístupový soubor ACL nebo obojí. Klikněte na tlačítko **OK**.
   
    ![Přiřazení oprávnění ke skupině](./media/data-lake-store-secure-data/adl.acl.4.png "Přiřazení oprávnění ke skupině")
   
    Další informace o oprávněních v gen1 úložiště datového jezera a výchozích a přístupových hodnotách AC, najdete [v tématu Řízení přístupu v souboru Data Lake Storage Gen1](data-lake-store-access-control.md).
6. Po klepnutí na **tlačítko Ok** v okně **Vybrat oprávnění** budou nově přidaná skupina a přidružená oprávnění nyní uvedena v okně **Access.**
   
    ![Přiřazení oprávnění ke skupině](./media/data-lake-store-secure-data/adl.acl.5.png "Přiřazení oprávnění ke skupině")
   
   > [!IMPORTANT]
   > V aktuální verzi můžete mít v části **Přiřazená oprávnění**až 28 položek . Pokud chcete přidat více než 28 uživatelů, měli byste vytvořit skupiny zabezpečení, přidat uživatele do skupin zabezpečení, přidat přístup k těmto skupinám zabezpečení pro účet Data Lake Storage Gen1.
   > 
   > 
7. V případě potřeby můžete také upravit přístupová oprávnění po přidání skupiny. Zrušte nebo zaškrtněte políčko pro každý typ oprávnění (Čtení, Zápis, Spuštění) podle toho, zda chcete odebrat nebo přiřadit toto oprávnění skupině zabezpečení. Kliknutím na **Uložit** změny uložte nebo **Je můžete** vrátit zpět.

## <a name="set-ip-address-range-for-data-access"></a>Nastavení rozsahu IP adres pro přístup k datům
Data Lake Storage Gen1 umožňuje dále uzamknout přístup k úložišti dat na úrovni sítě. Můžete povolit bránu firewall, zadat adresu IP nebo definovat rozsah IP adres pro důvěryhodné klienty. Po povolení se k úložišti mohou připojit pouze klienti, kteří mají adresy IP v rámci definovaného rozsahu.

![Nastavení brány firewall a přístup k IP adrese](./media/data-lake-store-secure-data/firewall-ip-access.png "Nastavení brány firewall a ADRESA IP")

## <a name="remove-security-groups-for-a-data-lake-storage-gen1-account"></a>Odebrání skupin zabezpečení pro účet Data Lake Storage Gen1
Když odeberete skupiny zabezpečení z účtů Data Lake Storage Gen1, měníte přístup k operacím správy na účtu pomocí Azure Portal a Azure Resource Manager API.  

Přístup k datům se nemění a je stále spravován přístupovými aloky ACL.  Výjimkou jsou uživatelé nebo skupiny v roli Vlastníci.  Uživatelé/skupiny odebrané z role Vlastníci již nejsou super uživatelé a jejich přístup se vrátí k přístupu k nastavení acl. 

1. V okně účtu Data Lake Storage Gen1 klikněte na **Řízení přístupu (IAM).** 
   
    ![Přiřazení skupiny zabezpečení k účtu Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.select.user.icon.png "Přiřazení skupiny zabezpečení k účtu Data Lake Storage Gen1")
2. V okně **Řízení přístupu (IAM)** klikněte na skupiny zabezpečení, které chcete odebrat. Klikněte na **Odebrat**.
   
    ![Skupina zabezpečení byla odebrána.](./media/data-lake-store-secure-data/adl.remove.group.png "Skupina zabezpečení byla odebrána.")

## <a name="remove-security-group-acls-from-a-data-lake-storage-gen1-file-system"></a>Odebrání aklů skupiny zabezpečení ze systému souborů Data Lake Storage Gen1
Když odeberete počet aklů skupiny zabezpečení ze systému souborů Data Lake Storage Gen1, změníte přístup k datům v účtu Data Lake Storage Gen1.

1. V okně účtu Data Lake Storage Gen1 klikněte na **Průzkumník dat**.
   
    ![Vytvoření adresářů v účtu Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Vytvoření adresářů v účtu Data Lake Storage Gen1")
2. V okně **Průzkumníka dat** klikněte na složku, pro kterou chcete odebrat přístupový soubor, a potom klepněte na **tlačítko Access**. Chcete-li odebrat alokace ACL pro soubor, musíte nejprve klepnout na soubor, abyste ho zobrazili, a pak klepněte na **položku Access** z okna **Náhled souboru.** 
   
    ![Nastavení aklů v systému souborů Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.acl.1.png "Nastavení aklů v systému souborů Data Lake Storage Gen1")
3. V okně **Access** klikněte na skupinu zabezpečení, kterou chcete odebrat. V okně **Access details** klepněte na **tlačítko Odebrat**.
   
    ![Přiřazení oprávnění ke skupině](./media/data-lake-store-secure-data/adl.remove.acl.png "Přiřazení oprávnění ke skupině")

## <a name="see-also"></a>Viz také
* [Přehled Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Kopírování dat z objektů BLOB úložiště Azure do data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Použití Azure Data Lake Analytics s datelake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight s úložištěm datových jezer Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Začínáme se službou Data Lake Storage Gen1 s využitím PowerShellu](data-lake-store-get-started-powershell.md)
* [Začínáme s úložištěm datových jezer Gen1 pomocí sady .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Přístup k diagnostickým protokolům pro úložiště datových jezer Gen1](data-lake-store-diagnostic-logs.md)

