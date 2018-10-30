---
title: Tom, jak bezpečně migrace uživatelů mezi licencemi produktů pomocí licencování pro skupiny ve službě Azure Active Directory | Dokumentace Microsoftu
description: Popisuje doporučený postup pro migraci uživatelů mezi licencemi různých produktů (Office 365 Enterprise E1 a E3) pomocí licencování na základě skupin
services: active-directory
keywords: Licencování Azure AD
documentationcenter: ''
author: piotrci
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/29/2018
ms.author: piotrci
ms.openlocfilehash: 643339545dac6ec35ab44f2a05fbe417dea2bb71
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50211787"
---
# <a name="how-to-safely-migrate-users-between-product-licenses-by-using-group-based-licensing"></a>Tom, jak bezpečně migrace uživatelů mezi licencemi produktů pomocí licencování na základě skupin

Tento článek popisuje doporučenou metodou pro přesun uživatelů mezi licencemi produktů při použití skupinové licence. Cílem tohoto přístupu je zajistit, že nedochází ke ztrátě služeb nebo dat během migrace: by uživatelé přepínat mezi produkty bez problémů. Dvě varianty proces migrace zahrnuje:

-   Jednoduchá migrace mezi licencemi produktů, které neobsahují slovo konfliktní plány služeb, jako je migrace mezi Office 365 Enterprise E3 a Office 365 Enterprise E5.

-   Složitější migrace mezi produkty, které obsahují některé konfliktní plány služeb, jako je migrace mezi Office 365 Enterprise E1 a Office 365 Enterprise E3. Další informace o konfliktech najdete v tématu [konfliktní plány služeb](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-problem-resolution-azure-portal#conflicting-service-plans) a [služby plány, které nelze přiřadit ve stejnou dobu](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-product-and-service-plan-reference#service-plans-that-cannot-be-assigned-at-the-same-time).

Tento článek obsahuje ukázkový kód Powershellu, který slouží k provedení kroků migrace a ověření. Kód je užitečné zejména pro rozsáhlé operace, kde není praktické kroky provést ručně.

## <a name="before-you-begin"></a>Než začnete
Než začnete s migrací, je důležité ověřit, že některé předpoklady platí pro všechny uživatele, které chcete migrovat. Nejsou-li předpoklady platí pro všechny uživatele, může migrace selhat pro některé. Díky tomu někteří uživatelé možná ztratíte přístup ke službám nebo datům. Je třeba ověřit následující předpoklady:

-   Uživatelé mají *zdroj licence* , která je přiřazena pomocí licencování na základě skupiny. Licence k produktu pro přesun směrem z dědí z jedné zdrojové skupiny a nejsou přímo přiřadit.

    >[!NOTE]
    >Pokud také přiřazení licencí přímo, může bránit použití *cílové licence*. Další informace o [Přímá synchronizace a přiřazení licence skupině](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-advanced#direct-licenses-coexist-with-group-licenses). Můžete chtít použít [skript prostředí PowerShell](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-ps-examples#check-if-user-license-is-assigned-directly-or-inherited-from-a-group) chcete kontrolovat, jestli uživatelé mají přímé licence.

-   Máte dostatek dostupné licence pro cíl produktu. Pokud nemáte k dispozici dostatek licencí, nemusí někteří uživatelé získat *cílové licence*. Je možné [Zkontrolujte počet dostupných licencí](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products).

-   Uživatelé nemají k dispozici další licence na přiřazené produkty, které může dojít ke konfliktu s *cílové licence* nebo zabránit odebrání *zdroj licence*. Například licenci z doplňkovým produktem jako Workplace Analytics nebo Projectu Online, který obsahuje závislost na další produkty.

-   Zjistěte, jak se spravují skupiny ve vašem prostředí. Například je-li spravovat skupiny v místním a synchronizovat je se do služby Azure Active Directory (Azure AD) prostřednictvím služby Azure AD Connect, pak můžete přidávat nebo odebírat uživatele pomocí místního systému. Změny se synchronizují do služby Azure AD a získat vyzvednou licencování na základě skupiny chvíli trvá. Pokud používáte dynamické členství ve skupinách Azure AD, můžete přidat či odebrat uživatele úpravou jejich atributy. Celkový proces migrace ale zůstává stejná. Jediným rozdílem je, jak můžete přidat nebo odebrat uživatele pro členství ve skupině.

## <a name="migrate-users-between-products-that-dont-have-conflicting-service-plans"></a>Migrace uživatelů mezi produkty, které nemají konfliktní plány služeb
Cíl migrace je změna uživatelské licence pomocí licencování na základě skupiny *zdroj licence* (v tomto příkladu: Office 365 Enterprise E3) k *cílové licence* (v tomto příkladu: Office 365 Enterprise E5). Tyto dva produkty v tomto scénáři neobsahují konfliktní plány služeb tak můžou být plně přiřazeno ve stejnou dobu bez konfliktu. V žádném bodě během migrace by uživatelé ztratit přístup ke službám nebo datům. Migrace se provádí malých dávkách"." Můžete ověřovat výsledek pro jednotlivé dávky a minimalizovat rozsahu potíží, které mohou nastat během procesu. Celkově procesu vypadá takto:

1.  Uživatelé jsou členy skupiny zdroje a dědí *zdroj licence* z této skupiny.

2.  Vytvořte cílovou skupinu s *cílové licence* , ale bez členů.

3.  Přidáte skupinu uživatelů cílovou skupinu. Licencování na základě skupiny vybere změny a přiřadí *cílové licence*. Proces může trvat delší dobu, v závislosti na velikosti dávky a dalších aktivit v tenantovi.

4.  Ověřte, že batch uživatelé budou plně zpracovány pomocí licencování na základě skupiny. Potvrďte, že každý uživatel má *cílové licence* přiřazené. Zkontrolujte, jestli uživatelé neměli ukládaly do chybového stavu, jako je například je v konfliktu s jinými produkty nebo chybějící dostatek licencí. Další informace o chybách najdete v tématu [licencování řešení problému skupiny služby Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-problem-resolution-azure-portal).

5.  V tuto chvíli uživatelé mají i *zdroj licence* a *cílové licence* přiřazené.

6.  Odeberte stejné dávky uživatele ze skupiny zdroje. Licencování na základě skupiny reaguje na změnu a *zdrojové licence* odeberou se od uživatelů.

7.  Postup opakujte pro další dávky uživatelů.

### <a name="migrate-a-single-user-by-using-the-azure-portal"></a>Migrace jednoho uživatele pomocí webu Azure portal
Toto je jednoduché návod, jak migrovat jenom jednoho konkrétního uživatele.

**Krok 1**: uživatel má *zdroj licence* , který je zděděno od skupiny. Nejsou žádné přímé přiřazení licence:

![Uživatel s licencí zdrojové, zděděná ze skupiny](./media/licensing-groups-change-licenses/UserWithSourceLicenseInherited.png)

**Krok 2**: uživatel je přidán do cílové skupiny a licencování na základě skupiny zpracovává změny. Uživatel nyní má i *zdroj licence* a *cílové licence* , která se dědí ze skupin:

![Uživatel s licencí zdrojovém i cílovém zděděno od skupiny](./media/licensing-groups-change-licenses/UserWithBothSourceAndTargetLicense.png)

**Krok 3**: uživatel je odebrán ze skupiny pro zdroj a licencování na základě skupiny zpracovává změny. Uživatel nyní má pouze *cílové licence*:

![Uživatel s licencí cílové zděděné ze skupiny](./media/licensing-groups-change-licenses/UserWithTargetLicenseAssigned.png)

### <a name="automate-migration-by-using-azure-powershell"></a>Automatizujte migraci pomocí Azure Powershellu
Následující fragment kódu ukazuje, jak automatizovat proces migrace pro rozsáhlé operace.

> [!NOTE]
> Vzorový kód používá funkce Powershellu, které jsou součástí [poslední část](#powershell-automation-of-migration-and-verification-steps) tohoto článku.

```
# A batch of users that we want to migrate in this iteration.
# The batch can be specified as an array of User Principal Names (string) or ObjectIds (Guid).
# Note: The batch can be loaded from a text file that represents a larger batch of users that we want to migrate.
[string[]]$usersToMigrate = 'MigrationUser@tailspinonline.com','MigrationUser2@tailspinonline.com'

############### NON-CONFLICTING LICENSES SCENARIO ################

# The source group and source license to remove the user from.
$sourceGroupId = [Guid]'b82c04f0-ce30-4ff1-bac7-735d92d83036'
$sourceSkuId = 'TailspinOnline:ENTERPRISEPACK'      # <-- This is the Office 365 Enterprise E3 product.

# The target group and target license to assign to the user.
$targetGroupId = [Guid]'bcf279d1-5ad5-46a5-b469-4b8a552aa2fe'
$targetSkuId = 'TailspinOnline:ENTERPRISEPREMIUM'   # <-- This is the Office 365 Enterprise E5 product.

if(-Not (VerifyAssumptions $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId))
{
    throw "Some users did not pass validation checks. See the output for details. Aborting migration process."
}

Write-Host "STEP 1: Adding users to the target group $targetGroupId. This will assign the target license $targetSkuId to all users"
AddUsersToGroup $usersToMigrate $targetGroupId

# Verify that the target license shows up in the conflict state for each user on the list.
# This step runs in a loop, forever, until all of the users are in the expected state.
# Group-based licensing (GBL) can take some time to reflect the changes on users.
# As a result, the loop should terminate after a period of time that's dependent on the size of the user collection.
# Note: If the loop hasn't terminated after a long period of time, stop the script.
#       Inspect the users that are reported as not yet in the expected state.
#       Verify that the users are not blocked for some other reason.
ExecuteVerificationLoop ${function:VerifySourceandTargetLicensePresent} 'STEP 2: Checking if all users still have the source license and now also have the target license from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId

# Now it's safe to remove the users from the source group.
Write-Host "STEP 3: Removing users from the source group $sourceGroupId. This will remove the source license $sourceSkuId."
RemoveUsersFromGroup $usersToMigrate $sourceGroupId

# Verify that the target license is now active on each user and the source license is removed.
ExecuteVerificationLoop ${function:VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup} 'STEP 4: Checking if all users have source license removed and target license assigned from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId
```

**Ukázkový výstup (migrace dva uživatele)**

```
Verifying initial assumptions:
Enough TailspinOnline:ENTERPRISEPREMIUM licenses available (13) for users: 2.
migrationuser@tailspinonline.com                OK
migrationuser2@tailspinonline.com               OK
Checks passed for all 2 users.

STEP 1: Adding users to the target group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe. This will assign the target license TailspinOnline:ENTERPRISEPREMIUM to all users
Adding user MigrationUser@tailspinonline.com to group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe
Adding user MigrationUser2@tailspinonline.com to group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe

STEP 2: Checking if all users still have the source license and now also have the target license from target group. Check iteration: 1

MigrationUser@tailspinonline.com                Expected state: NO
MigrationUser2@tailspinonline.com               Expected state: NO

Total users checked: 2. In expected state: 0. Not yet: 2
Not all users are in expected state. Waiting 60 seconds to try again.

STEP 2: Checking if all users still have the source license and now also have the target license from target group. Check iteration: 2

MigrationUser@tailspinonline.com                Expected state: YES
MigrationUser2@tailspinonline.com               Expected state: YES

Total users checked: 2. In expected state: 2. Not yet: 0
Check passed for all users. Exiting check loop.

STEP 3: Removing users from the source group b82c04f0-ce30-4ff1-bac7-735d92d83036. This will remove the source license TailspinOnline:ENTERPRISEPACK.
Removing user MigrationUser@tailspinonline.com from group b82c04f0-ce30-4ff1-bac7-735d92d83036
Removing user MigrationUser2@tailspinonline.com from group b82c04f0-ce30-4ff1-bac7-735d92d83036

STEP 4: Checking if all users have source license removed and target license assigned from target group. Check iteration: 1

MigrationUser@tailspinonline.com                Expected state: NO
MigrationUser2@tailspinonline.com               Expected state: NO

Total users checked: 2. In expected state: 0. Not yet: 2
Not all users are in expected state. Waiting 60 seconds to try again.

STEP 4: Checking if all users have source license removed and target license assigned from target group. Check iteration: 2

MigrationUser@tailspinonline.com                Expected state: YES
MigrationUser2@tailspinonline.com               Expected state: YES

Total users checked: 2. In expected state: 2. Not yet: 0
Check passed for all users. Exiting check loop.
```

## <a name="migrate-users-between-products-that-have-conflicting-service-plans"></a>Migrace uživatelů mezi produkty, které mají konfliktní plány služeb
Cíl migrace je změna uživatelské licence pomocí licencování na základě skupiny *zdroj licence* (v tomto příkladu: Office 365 Enterprise E1) k *cílové licence* (v tomto příkladu: Office 365 Enterprise E3). Tyto dva produkty v tomto scénáři obsahují konfliktní plány služeb, takže budete muset vyřešit konflikt hladce migrovat uživatele. Další informace o těchto konfliktech najdete v tématu [licencování řešení problému skupiny služby Active Directory: konfliktní plány služeb](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-problem-resolution-azure-portal#conflicting-service-plans). V žádném bodě během migrace by uživatelé ztratit přístup ke službám nebo datům. Migrace se provádí malých dávkách"." Můžete ověřovat výsledek pro jednotlivé dávky a minimalizovat rozsahu potíží, které mohou nastat během procesu. Celkově procesu vypadá takto:

1.  Uživatelé jsou členy skupiny zdroje a dědí *zdroj licence* z této skupiny.

2.  Vytvořte cílovou skupinu s *cílové licence* , ale bez členů.

3.  Přidáte skupinu uživatelů cílovou skupinu. Licencování na základě skupiny vybere změny a pokusí přiřadit *cílové licence*. Přiřazení se nepovedlo kvůli konfliktům mezi službami v tyto dva produkty. Licencování na základě skupiny zaznamenává selhání za chybu u každého uživatele. Proces může trvat delší dobu, v závislosti na velikosti dávky a dalších aktivit v tenantovi.

4.  Ověřte, že batch uživatelé budou plně zpracovány pomocí licencování na základě skupiny. Potvrďte, že každý uživatel má chyba konfliktu zaznamenána. Zkontrolujte, že někteří uživatelé neměli ukládaly do stavu došlo k neočekávané chybě. Další informace o chybách najdete v tématu [licencování řešení problému skupiny služby Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-problem-resolution-azure-portal).

5.  V tuto chvíli mít uživatelé dál *zdroj licence* a konflikty pro *cílové licence*. Ještě uživatelé nemají *cílové licence* přiřazené.

6.  Odeberte stejné dávky uživatele ze skupiny zdroje. Licencování na základě skupiny reaguje na změnu a *zdroj licence* Odebereme z každého uživatele. Chyba konfliktu Odebereme ve stejnou dobu (když žádné další licence na produkt přispívá k chybě) a *cílové licence* je přiřazen. Tento proces zajišťuje, že nedochází ke ztrátě služeb nebo dat během přechodu.

7.  Postup opakujte pro další dávky uživatelů.

### <a name="migrate-a-single-user-by-using-the-azure-portal"></a>Migrace jednoho uživatele pomocí webu Azure portal
Toto je jednoduché návod, jak migrovat jenom jednoho konkrétního uživatele.

**Krok 1**: uživatel má *zdroj licence* , který je zděděno od skupiny. Nejsou žádné přímé přiřazení licence:

![Uživatel s licencí zdrojové, zděděná ze skupiny](./media/licensing-groups-change-licenses/UserWithSourceLicenseInheritedConflictScenario.png)

**Krok 2**: uživatel je přidán do cílové skupiny a licencování na základě skupiny zpracovává změny. Vzhledem k tomu, že uživatel má stále *zdroj licence*, *cílové licence* je v chybovém stavu kvůli konfliktu:

![Uživatel s licencí zdrojové, zděděná ze skupiny a cíl licence v chybovém stavu](./media/licensing-groups-change-licenses/UserWithSourceLicenseAndTargetLicenseInConflict.png)

**Krok 3**: uživatel je odebrán ze skupiny pro zdroj a licencování na základě skupiny zpracovává změny. *Cílové licence* platí pro uživatele:

![Uživatel s licencí cílové zděděné ze skupiny](./media/licensing-groups-change-licenses/UserWithTargetLicenseAssignedConflictScenario.png)


### <a name="automate-migration-by-using-azure-powershell"></a>Automatizujte migraci pomocí Azure Powershellu
Následující fragment kódu ukazuje, jak automatizovat proces migrace pro rozsáhlé operace.

> [!NOTE]
> Vzorový kód používá funkce Powershellu, které jsou součástí [poslední část](#powershell-automation-of-migration-and-verification-steps) tohoto článku.

```
# A batch of users that we want to migrate in this iteration.
# The batch can be specified as an array of User Principal Names (string) or ObjectIds (Guid).
# Note: The batch can be loaded from a text file that represents a larger batch of users that we want to migrate.
[string[]]$usersToMigrate = 'MigrationUser@tailspinonline.com', 'MigrationUser2@tailspinonline.com'

############### CONFLICTING LICENSES SCENARIO ################

# The source group and source license to remove the user from.
$sourceGroupId = [Guid]'b82c04f0-ce30-4ff1-bac7-735d92d83036'
$sourceSkuId = 'TailspinOnline:STANDARDPACK'             # <-- This is the Office 365 Enterprise E1 product.

# The target group and target license to assign to the user.
$targetGroupId = [Guid]'bcf279d1-5ad5-46a5-b469-4b8a552aa2fe'
$targetSkuId = 'TailspinOnline:ENTERPRISEPACK'           # <-- This is the Office 365 Enterprise E3 product.

# Assumptions before migration:
# 1. Users are already in the source group and they have the source license assigned from that group.
# 2. Users don't have the same source license assigned from another group at the same time,
#    and they don't have the source license assigned directly.
#    IMPORTANT: If Assumption 2 isn't true, removing users from the source group in STEP 3
#               won't result in the target license being correctly applied.
# 3. There are enough available licenses to assign a target license to all of the users that are being migrated.
if(-Not (VerifyAssumptions $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId))
{
    throw "Some users did not pass validation checks. See the output for details. Aborting migration process."
}

Write-Host "STEP 1: Adding users to the target group $targetGroupId. This will put target license $targetSkuId in conflict state with the source license $sourceSkuId"
AddUsersToGroup $usersToMigrate $targetGroupId

# Verify that the target license shows up in the conflict state for each user on the list.
# This step runs in a loop, forever, until all of the users are in the expected state.
# Group-based licensing (GBL) can take some time to reflect the changes on users.
# As a result, the loop should terminate after a period of time that's dependent on the size of the user collection.
# Note: If the loop hasn't terminated after a long period of time, stop the script.
#       Inspect the users that are reported as not yet in the expected state.
#       Verify that the users are not blocked for some other reason.
ExecuteVerificationLoop ${function:VerifySourceLicensePresentAndTargetLicenseInConflictState} 'STEP 2: Checking if all users still have the source license and are in conflict state for license from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId

# Now it's safe to remove the users from the source group.
Write-Host "STEP 3: Removing users from the source group $sourceGroupId. This will remove the source license $sourceSkuId and remove the conflict on target license $targetSkuId which will become assigned."
RemoveUsersFromGroup $usersToMigrate $sourceGroupId

# Verify that the target license is now active on each user and the source license is removed.
ExecuteVerificationLoop ${function:VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup} 'STEP 4: Checking if all users have source license removed and target license assigned from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId
```

**Ukázkový výstup (migrace dva uživatele)**

```
Verifying initial assumptions:
Enough TailspinOnline:ENTERPRISEPACK licenses available (61) for users: 2.
migrationuser@tailspinonline.com                OK
migrationuser2@tailspinonline.com               OK
Checks passed for all 2 users.
STEP 1: Adding users to the target group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe. This will put target license TailspinOnline:ENTERPRISEPACK in conflict state with the source license TailspinOnline:STANDARDPACK
Adding user MigrationUser@tailspinonline.com to group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe
Adding user MigrationUser2@tailspinonline.com to group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe

STEP 2: Checking if all users still have the source license and are in conflict state for license from target group. Check iteration: 1

MigrationUser@tailspinonline.com                Expected state: NO
MigrationUser2@tailspinonline.com               Expected state: NO

Total users checked: 2. In expected state: 0. Not yet: 2
Not all users are in expected state. Waiting 60 seconds to try again.

STEP 2: Checking if all users still have the source license and are in conflict state for license from target group. Check iteration: 3

MigrationUser@tailspinonline.com                Expected state: YES
MigrationUser2@tailspinonline.com               Expected state: YES

Total users checked: 2. In expected state: 2. Not yet: 0
Check passed for all users. Exiting check loop.

STEP 3: Removing users from the source group b82c04f0-ce30-4ff1-bac7-735d92d83036. This will remove the source license TailspinOnline:STANDARDPACK and remove the conflict on target license TailspinOnline:ENTERPRISEPACK which will become assigned.
Removing user MigrationUser@tailspinonline.com from group b82c04f0-ce30-4ff1-bac7-735d92d83036
Removing user MigrationUser2@tailspinonline.com from group b82c04f0-ce30-4ff1-bac7-735d92d83036

STEP 4: Checking if all users have source license removed and target license assigned from target group. Check iteration: 1

MigrationUser@tailspinonline.com                Expected state: NO
MigrationUser2@tailspinonline.com               Expected state: NO

Total users checked: 2. In expected state: 0. Not yet: 2
Not all users are in expected state. Waiting 60 seconds to try again.

STEP 4: Checking if all users have source license removed and target license assigned from target group. Check iteration: 3

MigrationUser@tailspinonline.com                Expected state: YES
MigrationUser2@tailspinonline.com               Expected state: YES

Total users checked: 2. In expected state: 2. Not yet: 0
Check passed for all users. Exiting check loop.
```

<h2 id="powershell-automation-of-migration-and-verification-steps">Spouštění kódu Powershellu k automatizaci a ověření migrace</h2>

Tato část obsahuje kódem Powershellu, který je potřeba spouštět skripty, které jsou popsány v tomto článku.

>[!WARNING]
>Tento kód slouží jako příklad pro demonstrační účely. Pokud máte v úmyslu používat ve vašem prostředí, zvažte nejprve testování kódu v malém měřítku, nebo v samostatné testovacího tenanta. Budete muset upravit kód pro konkrétní potřeby vašeho prostředí.

Ke spouštění kódu, postupujte podle pokynů v [Azure AD PowerShell verze 1.0 knihovny](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0). Před spuštěním skriptu, spusťte `connect-msolservice` rutiny pro přihlášení k tenantovi.

```
# BEGIN: Helper functions that are used in the scripts.

# GetUserObject function
# Retrieve a user object based on the ObjectId or UserPrincipalName.
function GetUserObject
{
    [OutputType([Microsoft.Online.Administration.User])]
    Param([object]$userId)

    $userIdType = $userId.GetType()

    if($userIdType -eq [Guid])
    {
        return Get-MsolUser -ObjectId $userId
    }
    elseif($userIdType -eq [string])
    {
        return Get-MsolUser -UserPrincipalName $userId
    }
    else
    {
        throw "User Id type must be a Guid or a string (UserPrincipalName). The user id type was: $($userIdType.Name)"
    }
}

# GetGuidUserId function
# Get a Guid objectId for a user, even when a UserPrincipal string is passed in.
# Guid ids are needed for group membership manipulation, where UPNs cannot be used.
function GetGuidUserId
{
    [OutputType([Guid])]
    Param([object]$userId)

    [Guid]$guidId = [Guid]::Empty
    if($userId.GetType() -eq [String])
    {
        $user = GetUserObject $userId
        return $user.ObjectId
    }
    elseif($userId.GetType() -eq [Guid])
    {
        return $userId
    }
    else
    {
        throw "UserId type must be a Guid or a string (UserPrincipalName). The user id type was: $($userId.GetType().Name)"
    }
}

# AddUsersToGroup function
# Add a collection of users to a group.
# Note: This function fails if a user is already a member of the specified group.
function AddUsersToGroup
{
    Param([object[]]$userIds, [Guid]$groupId)

    foreach($userId in $userIds)
    {
        $fetchId = GetGuidUserId $userId
        Write-Host "Adding user $userId to group $groupId"
        Add-MsolGroupMember -GroupObjectId $groupId -GroupMemberType User -GroupMemberObjectId $fetchId
    }
}

# RemoveUsersFromGroup function
# Remove a collection of users from a group.
# Note: This function fails if a user is not a member of the specified group.
function RemoveUsersFromGroup
{
    Param([object[]]$userIds, [Guid]$groupId)

    foreach($userId in $userIds)
    {
        $fetchId = GetGuidUserId $userId
        Write-Host "Removing user $userId from group $groupId"
        Remove-MsolGroupMember -GroupObjectId $groupId -GroupMemberType User -GroupMemberObjectId $fetchId
    }
}

# GetUserLicense function
# Return the license object that corresponds to the skuId.
# Return NULL if no object is found.
function GetUserLicense
{
    [OutputType([Microsoft.Online.Administration.UserLicense])]
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    # Look for the specific license SKU in all of the licenses that are assigned to the user.
    foreach($license in $user.Licenses)
    {
        if ($license.AccountSkuId -ieq $skuId)
        {
            return $license
        }
    }
    return $null
}

# IsLicenseAssignedToUser function
# Check if the specific SKU license is assigned to the user,
#    regardless of how the license is assigned (directly or via GBL).
function IsLicenseAssignedToUser
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [string]$skuId)

    $license = GetUserLicense $user $skuId

    return ($license -ne $null)
}

# GetObjectIdsAssigningLicense function
function GetObjectIdsAssigningLicense
{
    [OutputType([Guid[]])]
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    $license = GetUserLicense $user $skuId

    if ($license -ne $null)
    {
        return [Guid[]]$license.GroupsAssigningLicense
    }
    return [Array]::CreateInstance([Guid],0)
}

# UserHasLicenseAssignedFromThisGroup function
# Return TRUE if the user inherits the license from the specific group.
# Note: This function returns true only if the license is successfully assigned from the group.
#       If the license is in an error state, the function return false.
function UserHasLicenseAssignedFromThisGroup
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId, [Guid]$groupId)

    [Guid[]]$objectsAssigningLicense = GetObjectIdsAssigningLicense $user $skuId

    # GroupsAssigningLicense contains a collection of object IDs for assigning the license.
    # This could be a group object or a user object (contrary to what the name suggests).
    foreach ($assignmentSource in $objectsAssigningLicense)
    {
        # If the collection contains at least one ID that doesn't match the user ID, the license is inherited from a group.
        # Note: The license might also be assigned directly, in addition to being inherited.
        if ($assignmentSource -ieq $groupId)
        {
            return $true
        }
    }
    return $false
}

# GetErrorsForLicense function
# Return error objects for a specific license.
function GetErrorsForLicense
{
    [OutputType([Microsoft.Online.Administration.IndirectLicenseError[]])]
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    [Microsoft.Online.Administration.IndirectLicenseError[]] $errorObjects = $user.IndirectLicenseErrors | Where {"$($_.AccountSku.AccountName):$($_.AccountSku.SkuPartNumber)" -ieq $skuId}

    if($errorObjects -eq $null)
    {
        #there are no errors at all
        return [Array]::CreateInstance([Microsoft.Online.Administration.IndirectLicenseError],0)
    }

    return $errorObjects
}

# GetErrorForLicenseFromGroup function
# Return an error label that's associated with a specific license that's inherited from a specific group.
# Return $null if there's no error.
function GetErrorForLicenseFromGroup
{
    [OutputType([string])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$groupId, [string]$skuId)


    # There are some errors. Check if any of the errors are associated with the group.
    foreach($licenseError in GetErrorsForLicense $user $skuId)
    {
        if($licenseError.ReferencedObjectId -eq $groupId)
        {
            return $licenseError.Error
        }
    }
    return $null
}

# IsExpectedLicenseStateForGroup function
# Check if the license is in an expected state for a given group.
# If expectedError is set to a value, the function checks if the license is in the specific error state for the group.
# If expectedError is NULL, the function checks if the license is successfully assigned from the group.
function IsExpectedLicenseStateForGroup
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$groupId, [string]$skuId, [string]$expectedError)

    # The license is expected to be fully assigned from the group and not in an error state.
    if([string]::IsNullOrEmpty($expectedError))
    {
        # Check if the assigned license is inherted from the expected group and without an error on it.
        return (UserHasLicenseAssignedFromThisGroup $user $skuId $groupId)
    }
    # The license is expected to be in the specific error state on the specific group.
    else
    {
        $error = GetErrorForLicenseFromGroup $user $groupId $skuId
        return ($error -ieq $expectedError)
    }
}

# VerifySourceLicensePresentAndTargetLicenseInConflictState function
# Detect if the licenses are in a specific state where the source license is assigned, but the target license is in a conflict state.
# Note: If the source license is gone, the function throws an exception to abort the script.
#       The conflict state can signify that something went wrong with the migration steps and the user lost access to services.
function VerifySourceLicensePresentAndTargetLicenseInConflictState
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    # Check if the user still has the source license. If not, abort the script because something is seriously wrong.
    if(-Not (UserHasLicenseAssignedFromThisGroup $user $sourceSkuId $sourceGroupId))
    {
        throw "User $($user.UserPrincipalName) ($($user.ObjectId)) does not have the expected license $sourceSkuId from source group $sourceGroupId, which may result in loss of access and data. This is unexpected and should be investigated. Aborting execution."
    }
    # Check if the target license is in conflict, as expected.
    $conflictError = 'MutuallyExclusiveViolation'
    return (IsExpectedLicenseStateForGroup $user $targetGroupId $targetSkuId $conflictError)
}

# VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup function
# Detect if the licenses are in a specific state where the source license isn't present,
#    but the target license is correctly assigned.
# Note: If the source license is gone and the target license isn't present,
#       the function throws an exception to abort the script.
#       Something went wrong and the user may have lost access to services.
function VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    # Check if user has the source license completely removed, which is a prerequisite to the target license eventually kicking in.
    if(IsLicenseAssignedToUser $user $sourceSkuId)
    {
        return $false
    }

    # Check if the user has the target license at all. If not, abort the script because something is seriously wrong.
    if(-Not (IsLicenseAssignedToUser $user $targetSkuId))
    {
        throw "User $($user.UserPrincipalName) ($($user.ObjectId)) does not have the expected license $targetSkuId assigned, which may result in loss of access and data. This is unexpected and should be investigated. Aborting execution."
    }
    # Check if the target license is assigned from the expected target group, and no longer in an error state.
    return (IsExpectedLicenseStateForGroup $user $targetGroupId $targetSkuId $null)
}

# VerifySourceandTargetLicensePresent function
# Detect if the licenses are in the specific state where the source license is assigned and the target license is also assigned.
# Note: If the source license is gone, the function throws an exception to abort the script.
#       This state can signify that something went wrong with the migration steps and the user lost access to services.
function VerifySourceandTargetLicensePresent
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    #check user still has source license - if not, abort because something is seriously wrong
    if(-Not (UserHasLicenseAssignedFromThisGroup $user $sourceSkuId $sourceGroupId))
    {
        throw "User $($user.UserPrincipalName) ($($user.ObjectId)) does not have the expected license $sourceSkuId from source group $sourceGroupId, which may result in loss of access and data. This is unexpected and should be investigated. Aborting execution."
    }
    #check if the target license is also assigned from the target group
    return (UserHasLicenseAssignedFromThisGroup $user $targetSkuId $targetGroupId)
}

# VerifyAssumptionsForUser function
# Verify basic assumptions that should be true for a user before we execute the migration process.
# The function prints details about the verification steps.
# Return TRUE if all of the assumptions are true.
function VerifyAssumptionsForUser
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    $userName = $user.UserPrincipalName
    # 1. The user has the source license assigned from the source group.
    if(-Not (UserHasLicenseAssignedFromThisGroup $user $sourceSkuId $sourceGroupId))
    {
        Write-Host "$userName does not have source license $sourceSkuId assigned from source group $sourceGroupId."
        return $false
    }

    # 2. The user does't have the same source license assigned from another group at the same time,
    #    and the user doesn't have the source license assigned directly.
    [Guid[]]$otherObjectsAssigningLicense = GetObjectIdsAssigningLicense $user $sourceSkuId | Where {$_ -ne $sourceGroupId}
    foreach($otherObject in $otherObjectsAssigningLicense)
    {
        if($otherObject -eq $user.ObjectId)
        {
            Write-Host "$userName has source license assigned directly to the user."
        }
        else
        {
            Write-Host "$username has source license assigned from an additional unexpected group $otherObject."
        }
    }
    if($otherObjectsAssigningLicense -and $otherObjectsAssigningLicense.Count -gt 0)
    {
        return $false
    }

    # 3. The user doesn't have the target license assigned.
    if(IsLicenseAssignedToUser $user $targetSkuId)
    {
        Write-Host "$userName already has target license assigned."
        return $false
    }

    # 4. The user doesn't have the target license in an error state from some groups.
    [Microsoft.Online.Administration.IndirectLicenseError[]]$licenseErrors = GetErrorsForLicense $user $targetSkuId
    foreach($licenseError in $licenseErrors)
    {
        Write-Host "$userName has target license in error state $($licenseError.Error) from unexpected group $($licenseError.ReferencedObjectId)."
    }
    if($licenseErrors -and $licenseErrors.Count -gt 0)
    {
        return $false
    }

    Write-Host "$userName`t`tOK"
    return $true
}

# VerifyAssumptions function
# Check if all of the users to be migrated are in a correct state.
function VerifyAssumptions
{
    [OutputType([bool])]
    Param([object[]]$userIds, [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    Write-Host "Verifying initial assumptions:"

    # Check if there are enough target licenses for all of the users.
    $skuState = Get-MsolAccountSku | Where {$_.AccountSkuId -ieq $targetSkuId}

    if($skuState -eq $null)
    {
        Write-Host "Target license SKU $targetSkuId does not exist in the tenant at all."
        return $false
    }

    $availableLicenses = $skuState.ActiveUnits - $skuState.ConsumedUnits

    if($userIds.Count -gt $availableLicenses)
    {
        Write-Host "Not enough licenses for all users. User count: $($userIds.Count), licenses available: $availableLicenses"
        return $false
    }
    else
    {
        Write-Host "Enough $targetSkuId licenses available ($availableLicenses) for users: $($userIds.Count)."
    }

    # Check if each user to be migrated is in an expected state.
    $usersOK = 0
    $usersNotOK = 0
    foreach($userId in $userIds)
    {
        $user = GetUserObject $userId
        if(VerifyAssumptionsForUser $user $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId)
        {
            ++$usersOK
        }
        else
        {
            ++$usersNotOK
        }
    }
    if($usersNotOK -gt 0)
    {
        Write-Host "Checks passed for $usersOK users, but failed for $usersNotOK users."
        return $false
    }
    Write-Host "Checks passed for all $usersOK users."
    return $true
}

# ExecuteVerificationLoop function
# Execute a verification function (passed in as a delegate by using $checkFunction) for each user.
# The function tracks how many users passed/failed verification.
# The function repeats the verification loop until all of the users have passed the check.
#   The loop may never terminate if some users never reach the expected state.
#   If the loop doesn't terminate, you should investigate to determine the cause.
# Note: If the verification function fails with an exception,
#       such as the function detects an unexpected user state,
#       the loop terminates and investigation into the user state is needed.
function ExecuteVerificationLoop
{
    Param([System.Management.Automation.ScriptBlock]$checkFunction, [string]$consoleMessage, [object[]]$userIds,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    # How long to wait until the loop is retried.
    $sleepIntervalSeconds = 60
    $retryIteration = 1

    While($true)
    {
        Write-Host "`n$consoleMessage. Check iteration: $retryIteration`n"

        $usersInExpectedState = 0
        $usersNotYet = 0

        foreach ($userId in $userIds)
        {
            $user = GetUserObject $userId
            if($checkFunction.InvokeReturnAsIs($user, $sourceGroupId, $sourceSkuId, $targetGroupId, $targetSkuId))
            {
                Write-Host "$userId`t`tExpected state: YES"
                ++$usersInExpectedState
            }
            else
            {
                Write-Host "$userId`t`tExpected state: NO"
                ++$usersNotYet
            }
        }

        Write-Host "`nTotal users checked: $($userIds.Count). In expected state: $usersInExpectedState. Not yet: $usersNotYet"

        if($usersNotYet -eq 0)
        {
            Write-Host "Check passed for all users. Exiting check loop."
            return
        }

        ++$retryIteration
        Write-Host "Not all users are in expected state. Waiting $sleepIntervalSeconds seconds to try again."
        Start-Sleep -Seconds $sleepIntervalSeconds
    }
}
# END: Helper functions that are used in the script.

# BEGIN: Execute the script.

# Enable strict execution mode.
Set-StrictMode -Version latest
# Stop the script when the first exception is thrown.
$ErrorActionPreference = "Stop"

# A batch of users that we want to migrate in this iteration.
# The batch can be specified as an array of User Principal Names (string) or ObjectIds (Guid).
# Note: The batch can be loaded from a text file that represents a larger batch of users that we want to migrate.
[string[]]$usersToMigrate = 'MigrationUser@tailspinonline.com', 'MigrationUser2@tailspinonline.com'

############### CONFLICTING LICENSES SCENARIO ################

# The source group and source license to remove the user from.
$sourceGroupId = [Guid]'b82c04f0-ce30-4ff1-bac7-735d92d83036'
$sourceSkuId = 'TailspinOnline:STANDARDPACK'             # <-- This is the Office 365 Enterprise E1 product.

# The target group and target license to assign to the user.
$targetGroupId = [Guid]'bcf279d1-5ad5-46a5-b469-4b8a552aa2fe'
$targetSkuId = 'TailspinOnline:ENTERPRISEPACK'           # <-- This is the Office 365 Enterprise E3 product.

# Assumptions before migration:
# 1. Users are already in the source group and they have the source license assigned from that group.
# 2. Users don't have the same source license assigned from another group at the same time,
#    and they don't have the source license assigned directly.
#    IMPORTANT: If Assumption 2 isn't true, removing users from the source group in STEP 3
#               won't result in the target license being correctly applied.
# 3. There are enough available licenses to assign a target license to all of the users that are being migrated.
if(-Not (VerifyAssumptions $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId))
{
    throw "Some users did not pass validation checks. See the output for details. Aborting migration process."
}

Write-Host "STEP 1: Adding users to the target group $targetGroupId. This will put target license $targetSkuId in conflict state with the source license $sourceSkuId"
AddUsersToGroup $usersToMigrate $targetGroupId

# Verify that the target license shows up in the conflict state for each user on the list.
# This step runs in a loop, forever, until all of the users are in the expected state.
# Group-based licensing (GBL) can take some time to reflect the changes on users.
# As a result, the loop should terminate after a period of time that's dependent on the size of the user collection.
# Note: If the loop hasn't terminated after a long period of time, stop the script.
#       Inspect the users that are reported as not yet in the expected state.
#       Verify that the users are not blocked for some other reason.
ExecuteVerificationLoop ${function:VerifySourceLicensePresentAndTargetLicenseInConflictState} 'STEP 2: Checking if all users still have the source license and are in conflict state for license from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId

# Now it's safe to remove the users from the source group.
Write-Host "STEP 3: Removing users from the source group $sourceGroupId. This will remove the source license $sourceSkuId and remove the conflict on target license $targetSkuId which will become assigned."
RemoveUsersFromGroup $usersToMigrate $sourceGroupId

# Verify that target license is now active on each user and the source license is removed.
ExecuteVerificationLoop ${function:VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup} 'STEP 4: Checking if all users have source license removed and target license assigned from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId

# END: Execute the script.
```

## <a name="next-steps"></a>Další postup

Další informace o další scénáře pro správu licencí pomocí skupin v následujících článcích:

* [Přiřazení licencí ke skupině v Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)
* [Identifikace a řešení potíží s licencemi pro skupinu v Azure Active Directory](../users-groups-roles/licensing-groups-resolve-problems.md)
* [Postup migrace jednotlivě licencovaných uživatelů na licencování na základě skupin v Azure Active Directory](../users-groups-roles/licensing-groups-migrate-users.md)
* [Další scénáře licencování na základě skupin v Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)
* [Příklady prostředí PowerShell pro licencování na základě skupiny v Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
