---
title: Přiřazení role čtenáři adresáře ke skupině Azure AD a správě přiřazení rolí
description: Tento článek vás provede povolením role čtenáři adresáře pomocí skupin Azure AD ke správě přiřazení rolí Azure AD pomocí Azure SQL Database, spravované instance Azure SQL a Azure synapse Analytics.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: d6c447deedbdcc4f2439fc069f368db88b3560b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91278018"
---
# <a name="tutorial-assign-directory-readers-role-to-an-azure-ad-group-and-manage-role-assignments"></a>Kurz: přiřazení role čtenářů adresáře ke skupině Azure AD a správa přiřazení rolí

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> Přiřazení role **čtenáři adresáře** ke skupině v tomto článku je ve **verzi Public Preview**. 

Tento článek vás provede vytvořením skupiny v Azure Active Directory (Azure AD) a přiřazením této skupiny k roli [**čtenáři adresáře**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) . Oprávnění čtenářů adresářů umožňují vlastníkům skupiny přidávat do skupiny další členy, jako je [spravovaná identita](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) [Azure SQL Database](sql-database-paas-overview.md), [spravovaná instance Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md)a [Azure synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). To obchází nutnost, aby správce [globálního správce](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) nebo [privilegované role](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) přiřadil roli čtenářů adresáře přímo pro každou identitu logického serveru Azure SQL v tenantovi.

V tomto kurzu se používá funkce zavedené v části [použití cloudových skupin ke správě přiřazení rolí v Azure Active Directory (Preview)](../../active-directory/users-groups-roles/roles-groups-concept.md). 

Další informace o výhodách přiřazení role čtenáři adresáře ke skupině Azure AD pro Azure SQL najdete v tématu [role čtečky adresářů v Azure Active Directory pro Azure SQL](authentication-aad-directory-readers-role.md).

## <a name="prerequisites"></a>Požadavky

- Instance Azure AD. Další informace najdete v tématu [Konfigurace a Správa ověřování Azure AD pomocí Azure SQL](authentication-aad-configure.md).
- SQL Database, Managed instance SQL nebo Azure synapse.

## <a name="directory-readers-role-assignment-using-the-azure-portal"></a>Přiřazení role čtenáři adresáře pomocí Azure Portal

### <a name="create-a-new-group-and-assign-owners-and-role"></a>Vytvoří novou skupinu a přiřadí vlastníky a role.

1. Pro tuto úvodní instalaci se vyžaduje oprávnění správce s [globálním správcem](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) nebo [privilegovaný rolí](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .
1. Přihlaste se privilegovanému uživateli do [Azure Portal](https://portal.azure.com).
1. Přejít na prostředek **Azure Active Directory** . V části **spravováno**, přejít na **skupiny**. Vyberte **Nová skupina** a vytvořte novou skupinu.
1. Jako typ skupiny vyberte **zabezpečení** a vyplňte zbývající pole. Ujistěte se, že nastavení **rolí Azure AD je možné přiřadit ke skupině (Preview)** , která je přepnuta na **Ano**. Pak skupině přiřaďte roli **čtenáři adresáře** Azure AD.
1. Přiřaďte uživatele Azure AD jako vlastníky ke skupině, která se vytvořila. Vlastníkem skupiny může být běžný uživatel služby AD bez přiřazené role pro správu Azure AD. Vlastníkem by měl být uživatel, který spravuje vaše SQL Database, spravovanou instanci SQL nebo Azure synapse.

   :::image type="content" source="media/authentication-aad-directory-readers-role/new-group.png" alt-text="AAD-New-Group":::

1. Vyberte **Vytvořit**.

### <a name="checking-the-group-that-was-created"></a>Kontroluje se skupina, která se vytvořila.

> [!NOTE]
> Ujistěte se, že **typ skupiny** je **zabezpečení**. *Microsoft 365* skupiny se pro Azure SQL nepodporují.

Chcete-li ověřit a spravovat vytvořenou skupinu, vraťte se zpět do podokna **skupiny** v Azure Portal a vyhledejte název skupiny. Další vlastníky a členy lze přidat do nabídky **vlastníci** a **Členové** v nastavení **Spravovat** po výběru skupiny. Můžete také zkontrolovat **přiřazené role** pro skupinu.

:::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-group-created.png" alt-text="AAD-New-Group":::

### <a name="add-azure-sql-managed-identity-to-the-group"></a>Přidat spravovanou identitu SQL Azure do skupiny

> [!NOTE]
> Pro tento příklad používáme spravovanou instanci SQL, ale podobný postup můžete použít pro SQL Database nebo Azure Synapse k dosažení stejných výsledků.

Pro následné kroky už správce globálního správce nebo privilegované role není potřeba.

1. Přihlaste se k Azure Portal jako uživatel spravující spravovanou instanci SQL a je vlastníkem skupiny, kterou jste vytvořili dříve.

1. V Azure Portal vyhledejte název prostředku **spravované instance SQL** .

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance.png" alt-text="AAD-New-Group":::

   Během vytváření spravované instance SQL se pro vaši instanci vytvořila identita Azure. Vytvořená identita má stejný název jako předpona názvu spravované instance SQL. Instanční objekt pro identitu spravované instance SQL, který se vytvoří jako aplikace Azure AD, najdete pomocí následujících kroků:

    - Přejít na prostředek **Azure Active Directory** . V nastavení **Spravovat** vyberte **podnikové aplikace**. **ID objektu** je identita instance.
    
    :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance-service-principal.png" alt-text="AAD-New-Group":::

1. Přejít na prostředek **Azure Active Directory** . V části **spravováno**, přejít na **skupiny**. Vyberte skupinu, kterou jste vytvořili. V části **spravované** nastavení vaší skupiny vyberte **Členové**. Vyberte **přidat členy** a přidejte instanční objekt spravované instance SQL jako člena skupiny, a to tak, že vyhledáte výše uvedený název.

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-add-managed-instance-service-principal.png" alt-text="AAD-New-Group":::

> [!NOTE]
> Rozšíření oprávnění instančního objektu prostřednictvím systému Azure může trvat několik minut, než se povolí přístup k Graph APIům služby Azure AD. Možná budete muset několik minut počkat, než zřídíte správce Azure AD pro spravovanou instanci SQL.

### <a name="remarks"></a>Poznámky

V případě SQL Database a Azure synapse je možné identitu serveru vytvořit během vytváření logického serveru SQL Azure nebo po vytvoření serveru. Další informace o tom, jak vytvořit nebo nastavit identitu serveru v SQL Database nebo Azure synapse, najdete v tématu [Povolení instančních objektů pro vytváření uživatelů Azure AD](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users).

V případě spravované instance SQL musí být role **čtenáři adresáře** přiřazena k identitě spravované instance předtím, než můžete [nastavit správce Azure AD pro spravovanou instanci](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

Přiřazení role **čtenáři adresáře** k identitě serveru není vyžadováno pro SQL Database nebo Azure synapse při nastavování správce Azure AD pro logický Server. Pokud ale chcete povolit vytvoření objektu Azure AD v SQL Database nebo Azure synapse jménem aplikace služby Azure AD, je potřeba role **čtenáři adresáře** . Pokud není role přiřazená k identitě logického serveru SQL, vytváření uživatelů Azure AD v Azure SQL selže. Další informace najdete v tématu [Azure Active Directory instančního objektu se službou Azure SQL](authentication-aad-service-principal.md).

## <a name="directory-readers-role-assignment-using-powershell"></a>Přiřazení role čtenáři adresáře pomocí PowerShellu

> [!IMPORTANT]
> Správce [globálního správce](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) nebo [privilegované role](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) bude muset spustit tyto úvodní kroky. Kromě PowerShellu nabízí Azure AD Microsoft Graph rozhraní API k [Vytvoření skupiny s přiřazením rolí v Azure AD](../../active-directory/users-groups-roles/roles-groups-create-eligible.md#using-microsoft-graph-api).

1. Stáhněte si modul Azure AD Preview PowerShellu pomocí následujících příkazů. Může být nutné spustit PowerShell jako správce.

    ```powershell
    Install-Module azureadpreview
    Import-Module azureadpreview
    #To verify that the module is ready to use, use the following command:
    Get-Module azureadpreview
    ```

1. Připojte se k tenantovi služby Azure AD.

    ```powershell
    Connect-AzureAD
    ```

1. Vytvořte skupinu zabezpečení, která přiřadí roli **čtenáři adresáře** .

    - `DirectoryReaderGroup`, `Directory Reader Group` a `DirRead` lze změnit podle vaší preference.

    ```powershell
    $group = New-AzureADMSGroup -DisplayName "DirectoryReaderGroup" -Description "Directory Reader Group" -MailEnabled $False -SecurityEnabled $true -MailNickName "DirRead" -IsAssignableToRole $true
    $group
    ```

1. Přiřaďte ke skupině roli **čtenáři adresáře** .

    ```powershell
    # Displays the Directory Readers role information
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Directory Readers'" 
    $roleDefinition
    ```

    ```powershell
    # Assigns the Directory Readers role to the group
    $roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id
    $roleAssignment
    ```

1. Přiřaďte k této skupině vlastníky.

    - Nahraďte `<username>` uživatelem, který chcete vlastnit pro tuto skupinu. Několik vlastníků lze přidat opakováním těchto kroků.

    ```powershell
    $RefObjectID = Get-AzureADUser -ObjectId "<username>"
    $RefObjectID
    ```

    ```powershell
    $GrOwner = Add-AzureADGroupOwner -ObjectId $group.ID -RefObjectId $RefObjectID.ObjectID
    ```

    Ověřte vlastníky skupiny pomocí následujícího příkazu:

    ```powershell
    Get-AzureADGroupOwner -ObjectId $group.ID
    ```

    Můžete také ověřit vlastníky skupiny v [Azure Portal](https://portal.azure.com). Postupujte podle kroků v části [Kontrola vytvořené skupiny](#checking-the-group-that-was-created).

### <a name="assigning-the-service-principal-as-a-member-of-the-group"></a>Přiřazení instančního objektu jako člena skupiny

Pro následné kroky už správce globálního správce nebo privilegované role není potřeba.

1. Pomocí vlastníka skupiny, který také spravuje prostředek SQL Azure, spusťte následující příkaz pro připojení k Azure AD.

    ```powershell
    Connect-AzureAD
    ```

1. Přiřaďte instanční objekt jako člena skupiny, kterou jste vytvořili.

    - Nahraďte `<ServerName>` názvem logického serveru SQL Azure nebo názvem spravované instance. Další informace najdete v části [Přidání identity služby Azure SQL Service do skupiny](#add-azure-sql-managed-identity-to-the-group) .

    ```powershell
    # Returns the service principal of your Azure SQL resource
    $miIdentity = Get-AzureADServicePrincipal -SearchString "<ServerName>"
    $miIdentity
    ```

    ```powershell
    # Adds the service principal to the group as a member
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId 
    ```

    Následující příkaz vrátí ID objektu zabezpečení služby, což znamená, že byl přidán do skupiny:

    ```powershell
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId
    ```

## <a name="next-steps"></a>Další kroky

- [Role čtenáři adresáře v Azure Active Directory pro Azure SQL](authentication-aad-directory-readers-role.md)
- [Kurz: vytvoření uživatelů Azure AD pomocí aplikací Azure AD](authentication-aad-service-principal-tutorial.md)
- [Konfigurace a Správa ověřování Azure AD pomocí Azure SQL](authentication-aad-configure.md)