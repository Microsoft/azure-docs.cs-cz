---
title: Zabezpečení dat uložených v Azure Data Lake Storage Gen1 | Microsoft Docs
description: Naučte se zabezpečit data v Azure Data Lake Storage Gen1 pomocí skupin a seznamů řízení přístupu.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: ac7666f4c4e68d24499f9c097dc9bd021d270355
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92370691"
---
# <a name="securing-data-stored-in-azure-data-lake-storage-gen1"></a>Zabezpečení dat uložených ve službě Azure Data Lake Storage Gen1
Zabezpečení dat v Azure Data Lake Storage Gen1 je třístavový přístup.  Řízení přístupu na základě role Azure (Azure RBAC) a seznamy řízení přístupu (ACL) musí být nastavené tak, aby plně povolovaly přístup k datům pro uživatele a skupiny zabezpečení.

1. Začněte vytvořením skupin zabezpečení v Azure Active Directory (Azure AD). Tyto skupiny zabezpečení se používají k implementaci řízení přístupu na základě rolí Azure (Azure RBAC) v Azure Portal. Další informace najdete v tématu [Azure RBAC](../role-based-access-control/role-assignments-portal.md).
2. Přiřaďte skupiny zabezpečení Azure AD k účtu Data Lake Storage Gen1. Tato kontrola řídí přístup k účtu Data Lake Storage Gen1 z portálu a operací správy z portálu nebo rozhraní API.
3. Přiřaďte skupiny zabezpečení Azure AD jako seznamy řízení přístupu (ACL) v systému souborů Data Lake Storage Gen1.
4. Kromě toho můžete také nastavit rozsah IP adres pro klienty, kteří budou mít přístup k datům v Data Lake Storage Gen1.

Tento článek poskytuje pokyny, jak použít Azure Portal k provedení výše uvedených úloh. Podrobné informace o tom, jak Data Lake Storage Gen1 implementuje zabezpečení na úrovni účtu a dat, najdete v tématu [zabezpečení v Azure Data Lake Storage Gen1](data-lake-store-security-overview.md). Podrobné informace o tom, jak se v Data Lake Storage Gen1 implementují seznamy řízení přístupu (ACL), najdete v tématu [přehled Access Control v Data Lake Storage Gen1](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Předpoklady
Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Data Lake Storage Gen1**. Pokyny, jak ho vytvořit, najdete v tématu Začínáme [s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md) .

## <a name="create-security-groups-in-azure-active-directory"></a>Vytvoření skupin zabezpečení v Azure Active Directory
Pokyny k vytvoření skupin zabezpečení služby Azure AD a přidání uživatelů do této skupiny najdete v tématu [Správa skupin zabezpečení v Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

> [!NOTE] 
> Do skupiny v Azure AD můžete přidat jak uživatele, tak i jiné skupiny pomocí Azure Portal. Pokud ale chcete přidat instanční objekt do skupiny, použijte [modul PowerShellu služby Azure AD](../active-directory/enterprise-users/groups-settings-v2-cmdlets.md).
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-data-lake-storage-gen1-accounts"></a>Přiřazení uživatelů nebo skupin zabezpečení k Data Lake Storage Gen1 účtům
Když přiřazujete uživatele nebo skupiny zabezpečení k Data Lake Storage Gen1 účtům, řídíte přístup k operacím správy na účtu pomocí rozhraní API Azure Portal a Azure Resource Manager. 

1. Otevřete Data Lake Storage Gen1 účet. V levém podokně klikněte na **všechny prostředky** a potom v okně všechny prostředky klikněte na název účtu, ke kterému chcete přiřadit uživatele nebo skupinu zabezpečení.

2. V okně Data Lake Storage Gen1 účtu klikněte na **Access Control (IAM)**. Ve výchozím nastavení se v okně zobrazí vlastníci předplatného jako vlastník.
   
    ![Přiřadit skupinu zabezpečení pro Azure Data Lake Storage Gen1 účet](./media/data-lake-store-secure-data/adl.select.user.icon1.png "Přiřadit skupinu zabezpečení pro Azure Data Lake Storage Gen1 účet")

3. V okně **Access Control (IAM)** kliknutím na **Přidat** otevřete okno **Přidat oprávnění** . V okně **Přidat oprávnění** vyberte **roli** pro uživatele nebo skupinu. Vyhledejte skupinu zabezpečení, kterou jste vytvořili dříve v Azure Active Directory a vyberte ji. Pokud máte mnoho uživatelů a skupin, ze kterých se má hledat, použijte k filtrování názvu skupiny pole **Vybrat** text. 
   
    ![Přidat roli pro uživatele](./media/data-lake-store-secure-data/adl.add.user.1.png "Přidat roli pro uživatele")
   
    Role **vlastník** a **Přispěvatel** poskytuje přístup k nejrůznějším funkcím správy v účtu Data Lake. Pro uživatele, kteří budou pracovat s daty v Data Lake, ale stále potřebují zobrazit informace o správě účtu, je můžete přidat do role **čtenáře** . Rozsah těchto rolí je omezený na operace správy související s Data Lake Storage Gen1m účtem.
   
    Pro datové operace určují jednotlivá oprávnění systému souborů, co můžou uživatelé dělat. Proto může uživatel, který má roli Čtenář, zobrazit jenom nastavení správy spojená s tímto účtem, ale může potenciálně číst a zapisovat data na základě oprávnění systému souborů, která jsou jim přiřazená. Data Lake Storage Gen1 oprávnění systému souborů jsou popsána v tématu [přiřazení skupiny zabezpečení jako seznamů ACL do systému souborů Azure Data Lake Storage Gen1](#filepermissions).

    > [!IMPORTANT]
    > Pouze role **vlastníka** automaticky povolí přístup k systému souborů. **Přispěvatel**, **Čtenář** a všechny ostatní role vyžadují seznamy řízení přístupu (ACL), které umožňují přístup ke složkám a souborům na úrovni.  Role **vlastníka** poskytuje oprávnění k souborům a složkám ve výhradním uživateli, která nelze přepsat prostřednictvím seznamů ACL. Další informace o tom, jak zásady Azure RBAC mapují na přístup k datům, najdete v tématu [Azure RBAC pro správu účtů](data-lake-store-security-overview.md#azure-rbac-for-account-management).

4. Pokud chcete přidat skupinu nebo uživatele, která není uvedená v okně **Přidat oprávnění** , můžete je pozvat zadáním jejich e-mailové adresy do textového pole **Vybrat** a pak je vybrat ze seznamu.
   
    ![Přidat skupinu zabezpečení](./media/data-lake-store-secure-data/adl.add.user.2.png "Přidat skupinu zabezpečení")
   
5. Klikněte na **Uložit**. Měla by se zobrazit skupina zabezpečení, jak je znázorněno níže.
   
    ![Přidala se skupina zabezpečení.](./media/data-lake-store-secure-data/adl.add.user.3.png "Přidala se skupina zabezpečení.")

6. Uživatel nebo skupina zabezpečení má teď přístup k účtu Data Lake Storage Gen1. Pokud chcete poskytnout přístup konkrétním uživatelům, můžete je přidat do skupiny zabezpečení. Podobně pokud chcete odvolat přístup pro uživatele, můžete je odebrat ze skupiny zabezpečení. K účtu můžete také přiřadit více skupin zabezpečení. 

## <a name="assign-users-or-security-groups-as-acls-to-the-data-lake-storage-gen1-file-system"></a><a name="filepermissions"></a>Přiřazení uživatelů nebo skupin zabezpečení jako seznamů ACL do systému souborů Data Lake Storage Gen1
Přiřazením skupin uživatelů a zabezpečení do Data Lake Storage Gen1 systému souborů nastavíte řízení přístupu pro data uložená v Data Lake Storage Gen1.

1. V okně Data Lake Storage Gen1 účtu klikněte na **Průzkumník dat**.
   
    ![Zobrazit data prostřednictvím Průzkumník dat](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Zobrazit data prostřednictvím Průzkumník dat")
2. V okně **Průzkumník dat** klikněte na složku, pro kterou chcete nakonfigurovat seznam řízení přístupu (ACL), a pak klikněte na **přístup**. Pokud chcete k souboru přiřadit seznamy řízení přístupu (ACL), musíte nejdřív kliknout na soubor a zobrazit jeho náhled a pak kliknout na **přístup** v okně **Náhled souboru** .
   
    ![Nastavení seznamů ACL pro Data Lake Storage Gen1 systému souborů](./media/data-lake-store-secure-data/adl.acl.1.png "Nastavení seznamů ACL pro Data Lake Storage Gen1 systému souborů")
3. V okně **přístup** jsou uvedeni vlastníci a přiřazená oprávnění, která jsou již k tomuto kořenu přiřazena. Kliknutím na ikonu **Přidat** přidejte další seznamy ACL pro přístup.
    > [!IMPORTANT]
    > Nastavení přístupových oprávnění pro jeden soubor nemusí nutně udělit uživateli nebo skupině přístup k tomuto souboru. Cesta k souboru musí být přístupná pro přiřazeného uživatele nebo skupinu. Další informace a příklady najdete v tématu [běžné scénáře týkající se oprávnění](data-lake-store-access-control.md#common-scenarios-related-to-permissions).
   
    ![Vypsat standardní a vlastní přístup](./media/data-lake-store-secure-data/adl.acl.2.png "Vypsat standardní a vlastní přístup")
   
   * **Vlastníci** a **Všichni ostatní** poskytují přístup ve stylu UNIX, kde zadáváte oprávnění číst, zapisovat, spouštět (RWX) třem jedinečným třídám uživatelů: vlastník, skupina a další.
   * **Přiřazená oprávnění** odpovídají seznamům ACL POSIX, které umožňují nastavit oprávnění pro konkrétní pojmenované uživatele nebo skupiny za vlastníka nebo skupinu souboru. 
     
     Další informace najdete v tématu [HDFS – seznamy ACL](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Další informace o implementaci seznamů řízení přístupu (ACL) v Data Lake Storage Gen1 najdete v tématu [Access Control v Data Lake Storage Gen1](data-lake-store-access-control.md).
4. Kliknutím na ikonu **Přidat** otevřete okno **přiřadit oprávnění** . V tomto okně klikněte na **Vybrat uživatele nebo skupinu** a potom v okně **Vybrat uživatele nebo skupinu** vyhledejte skupinu zabezpečení, kterou jste vytvořili dříve v Azure Active Directory. Pokud máte spoustu skupin, ze kterých můžete vyhledávat, použijte k filtrování názvu skupiny textové pole v horní části. Klikněte na skupinu, kterou chcete přidat, a potom klikněte na **Vybrat**.
   
    ![Přidat skupinu](./media/data-lake-store-secure-data/adl.acl.3.png "Přidat skupinu")
5. Klikněte na **vybrat oprávnění**, vyberte oprávnění, jestli se mají oprávnění použít pro rekurzivní a jestli chcete přiřadit oprávnění jako přístupový seznam ACL, výchozí seznam ACL nebo obojí. Klikněte na **OK**.
   
    ![Snímek obrazovky s oknem oprávnění k přiřazení s možností vybrat oprávnění, která se vyvolala a v okně vybrat oprávnění s možností OK.](./media/data-lake-store-secure-data/adl.acl.4.png "Přiřazení oprávnění ke skupině")
   
    Další informace o oprávněních v Data Lake Storage Gen1 a o výchozích nebo přístupových seznamech řízení přístupu najdete [v tématu Access Control v Data Lake Storage Gen1](data-lake-store-access-control.md).
6. Po kliknutí na **OK** v okně **vybrat oprávnění** se teď nově přidaná skupina a přidružená oprávnění zobrazí v okně **přístup** .
   
    ![Snímek obrazovky okna s přístupem s možností strojírenství dat s názvem.](./media/data-lake-store-secure-data/adl.acl.5.png "Přiřazení oprávnění ke skupině")
   
   > [!IMPORTANT]
   > V aktuální verzi můžete v části **přiřazená oprávnění** mít až 28 položek. Pokud chcete přidat více než 28 uživatelů, měli byste vytvořit skupiny zabezpečení, přidat uživatele do skupin zabezpečení, přidat uživatelům přístup k těmto skupinám zabezpečení pro účet Data Lake Storage Gen1.
   > 
   > 
7. V případě potřeby můžete také upravit přístupová oprávnění po přidání skupiny. Zrušte zaškrtnutí nebo zaškrtněte políčko u každého typu oprávnění (čtení, zápis, spuštění) na základě toho, zda chcete toto oprávnění odebrat nebo přiřadit ke skupině zabezpečení. Kliknutím na **Uložit** uložte změny, nebo **zrušíte** zrušení změn.

## <a name="set-ip-address-range-for-data-access"></a>Nastavení rozsahu IP adres pro přístup k datům
Data Lake Storage Gen1 vám umožní dál uzamknout přístup k úložišti dat na úrovni sítě. Můžete povolit bránu firewall, zadat IP adresu nebo definovat rozsah IP adres pro důvěryhodné klienty. Po povolení se můžou ke Storu připojit jenom klienti, kteří mají IP adresy v definovaném rozsahu.

![Nastavení brány firewall a přístup k protokolu IP](./media/data-lake-store-secure-data/firewall-ip-access.png "Nastavení a IP adresa brány firewall")

## <a name="remove-security-groups-for-a-data-lake-storage-gen1-account"></a>Odebrání skupin zabezpečení pro účet Data Lake Storage Gen1
Když odeberete skupiny zabezpečení z Data Lake Storage Gen1 účtů, měníte jenom přístup k operacím správy na účtu pomocí rozhraní API Azure Portal a Azure Resource Manager.  

Přístup k datům se nezměnil a je stále spravovaný pomocí seznamů ACL přístupu.  Výjimkou jsou uživatelé nebo skupiny v roli vlastníci.  Uživatelé/skupiny odebrané z role vlastníci už nejsou Super uživatelé a jejich přístup se vrátí k nastavení seznamu ACL přístupu. 

1. V okně Data Lake Storage Gen1 účtu klikněte na **Access Control (IAM)**. 
   
    ![Přiřadit skupinu zabezpečení pro Data Lake Storage Gen1 účet](./media/data-lake-store-secure-data/adl.select.user.icon.png "Přiřadit skupinu zabezpečení pro Data Lake Storage Gen1 účet")
2. V okně **Access Control (IAM)** klikněte na skupiny zabezpečení, které chcete odebrat. Klikněte na **Odebrat**.
   
    ![Skupina zabezpečení odebrána](./media/data-lake-store-secure-data/adl.remove.group.png "Skupina zabezpečení odebrána")

## <a name="remove-security-group-acls-from-a-data-lake-storage-gen1-file-system"></a>Odebrání seznamů řízení přístupu skupiny zabezpečení z Data Lake Storage Gen1ho systému souborů
Když odeberete seznamy řízení přístupu (ACL) skupiny zabezpečení z Data Lake Storage Gen1ho systému souborů, změníte přístup k datům v účtu Data Lake Storage Gen1.

1. V okně Data Lake Storage Gen1 účtu klikněte na **Průzkumník dat**.
   
    ![Vytváření adresářů v Data Lake Storage Gen1m účtu](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Vytváření adresářů v Data Lake Storage Gen1m účtu")
2. V okně **Průzkumník dat** klikněte na složku, pro kterou chcete odebrat seznam řízení přístupu (ACL), a pak klikněte na **přístup**. Pokud chcete odebrat seznamy ACL pro určitý soubor, musíte nejdřív kliknout na soubor a zobrazit jeho náhled a pak kliknout na **přístup** v okně **Náhled souboru** . 
   
    ![Nastavení seznamů ACL pro Data Lake Storage Gen1 systému souborů](./media/data-lake-store-secure-data/adl.acl.1.png "Nastavení seznamů ACL pro Data Lake Storage Gen1 systému souborů")
3. V okně **přístup** klikněte na skupinu zabezpečení, kterou chcete odebrat. V okně **Podrobnosti o přístupu** klikněte na **Odebrat**.
   
    ![Snímek obrazovky s oknem přístup s možností inženýrství dat s názvem a podrobností o přístupu s možností odebrat s názvem.](./media/data-lake-store-secure-data/adl.remove.acl.png "Přiřazení oprávnění ke skupině")

## <a name="see-also"></a>Viz také
* [Přehled Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Kopírování dat z objektů blob Azure Storage do Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Použití Azure Data Lake Analytics s Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Začínáme se službou Data Lake Storage Gen1 s využitím PowerShellu](data-lake-store-get-started-powershell.md)
* [Začínáme s Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Přístup k diagnostickým protokolům pro Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md)
