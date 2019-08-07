---
title: 'Azure Active Directory Domain Services: Průvodce odstraňováním potíží | Microsoft Docs'
description: Průvodce odstraňováním potíží pro Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: acb001417b85b8ff45b2617e148e8b1961f3cbfa
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2019
ms.locfileid: "68772978"
---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Azure AD Domain Services – Průvodce odstraňováním potíží
Tento článek popisuje Rady pro řešení problémů, se kterými se můžete setkat při nastavování nebo správě služby Azure Active Directory (AD) Domain Services.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Nemůžete povolit Azure AD Domain Services pro adresář služby Azure AD
Tato část vám pomůže při řešení chyb při pokusu o povolení Azure AD Domain Services pro váš adresář.

Vyberte kroky pro řešení potíží, které odpovídají chybové zprávě, se kterou se setkáte.

| **Chybová zpráva** | **Řešení** |
| --- |:--- |
| *Název contoso100.com se už v síti používá. Zadejte název, který se nepoužívá.* |[Konflikt názvů domén ve virtuální síti](troubleshoot.md#domain-name-conflict) |
| *Domain Services nelze povolit v tomto tenantovi Azure AD. Služba nemá dostatečná oprávnění pro aplikaci s názvem Azure AD Domain Services Sync. Odstraňte aplikace s názvem Azure AD Domain Services Sync a potom se pokuste pro vašeho tenanta Azure AD povolit Domain Services.* |[Domain Services nemají odpovídající oprávnění k aplikaci Azure AD Domain Services Sync.](troubleshoot.md#inadequate-permissions) |
| *Domain Services nelze povolit v tomto tenantovi Azure AD. Aplikace Domain Services ve vašem tenantovi Azure AD nemá požadovaná oprávnění k povolení Domain Services. Odstraňte aplikaci s identifikátorem aplikace d87dcbc6-a371-462e-88e3-28ad15ec4e64 a potom se pokuste pro vašeho tenanta Azure AD povolit Domain Services.* |[Aplikace Domain Services není ve vašem tenantovi správně nakonfigurovaná.](troubleshoot.md#invalid-configuration) |
| *Domain Services nelze povolit v tomto tenantovi Azure AD. Aplikace Microsoft Azure AD je ve vašem tenantovi Azure AD zakázaná. Povolte aplikaci s identifikátorem aplikace 00000002-0000-0000-c000-000000000000 a potom se pokuste pro vašeho tenanta Azure AD povolit Domain Services.* |[Aplikace Microsoft Graph je v tenantovi Azure AD zakázaná.](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Konflikt názvů domén
**Chybová zpráva:**

*Název contoso100.com se už v síti používá. Zadejte název, který se nepoužívá.*

**Nápravy**

Ujistěte se, že v této virtuální síti nemáte k dispozici existující doménu se stejným názvem domény. Například předpokládejme, že již máte doménu „contoso.com“ k dispozici na vybrané virtuální síti. Později se pokusíte povolit Azure AD Domain Services spravovanou doménu se stejným názvem domény (to znamená ' contoso.com ') v této virtuální síti. Při pokusu o povolení Azure AD Domain Services dojde k chybě.

Příčinou této chyby je konflikt názvů pro název domény v této virtuální síti. V této situaci musíte použít jiný název pro nastavení domény spravované službou Azure AD Domain Services. Případně můžete zrušit existující doménu a poté pokračovat v povolení služby Azure AD Domain Services.

### <a name="inadequate-permissions"></a>Nedostatečná oprávnění
**Chybová zpráva:**

*Domain Services nelze povolit v tomto tenantovi Azure AD. Služba nemá dostatečná oprávnění pro aplikaci s názvem Azure AD Domain Services Sync. Odstraňte aplikace s názvem Azure AD Domain Services Sync a potom se pokuste pro vašeho tenanta Azure AD povolit Domain Services.*

**Nápravy**

Zkontrolujte, jestli v adresáři Azure AD není nějaká aplikace s názvem Azure AD Domain Services synchronizovat. Pokud tato aplikace existuje, odstraňte ji a pak znovu povolte Azure AD Domain Services.

Proveďte následující kroky, chcete-li zjistit přítomnost aplikace a odstranit ji, pokud aplikace existuje:

1. V [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/)přejděte do části **aplikace** v adresáři služby Azure AD.
2. V rozevíracím seznamu **Zobrazit** vyberte **všechny aplikace** . V rozevíracím seznamu **stav aplikací** vyberte **libovolné** . V rozevíracím seznamu **viditelnosti aplikace** vyberte **libovolné** .
3. Do vyhledávacího pole zadejte **Azure AD Domain Services Sync** . Pokud aplikace existuje, klikněte na ni a kliknutím na tlačítko **Odstranit** na panelu nástrojů je odstraňte.
4. Po odstranění aplikace se pokuste povolit Azure AD Domain Services znovu.

### <a name="invalid-configuration"></a>Neplatná konfigurace
**Chybová zpráva:**

*Domain Services nelze povolit v tomto tenantovi Azure AD. Aplikace Domain Services ve vašem tenantovi Azure AD nemá požadovaná oprávnění k povolení Domain Services. Odstraňte aplikaci s identifikátorem aplikace d87dcbc6-a371-462e-88e3-28ad15ec4e64 a potom se pokuste pro vašeho tenanta Azure AD povolit Domain Services.*

**Nápravy**

Zkontrolujte, jestli máte v adresáři Azure AD aplikaci s názvem ' AzureActiveDirectoryDomainControllerServices ' (s identifikátorem aplikace d87dcbc6-a371-462e-88e3-28ad15ec4e64). Pokud tato aplikace existuje, je nutné ji odstranit a pak Azure AD Domain Services znovu povolit.

K vyhledání aplikace a jejím odstranění použijte následující skript prostředí PowerShell.

> [!NOTE]
> Tento skript používá rutiny **Azure AD PowerShell verze 2** . Úplný seznam všech dostupných rutin a stažení modulu najdete v [referenční dokumentaci prostředí PowerShell pro AzureAD](https://msdn.microsoft.com/library/azure/mt757189.aspx).
>
>

```powershell
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```
<br>

### <a name="microsoft-graph-disabled"></a>Microsoft Graph zakázané
**Chybová zpráva:**

V tomto tenantovi Azure AD nešlo povolit doménové služby. Aplikace Microsoft Azure AD je ve vašem tenantovi Azure AD zakázaná. Povolte aplikaci s identifikátorem aplikace 00000002-0000-0000-C000-000000000000 a potom se pokuste pro vašeho tenanta Azure AD povolit Domain Services.

**Nápravy**

Zkontrolujte, zda jste zakázali aplikaci s identifikátorem 00000002-0000-0000-C000-000000000000. Tato aplikace je Microsoft Azure AD aplikace a poskytuje Graph API přístup k vašemu tenantovi služby Azure AD. Azure AD Domain Services potřebuje, aby tato aplikace byla povolená k synchronizaci vašeho tenanta Azure AD s vaší spravovanou doménou.

Tuto chybu můžete vyřešit tak, že tuto aplikaci povolíte a pak zkusíte povolit službu Domain Services pro vašeho tenanta Azure AD.


## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Uživatelé se nemůžou přihlásit ke spravované doméně Azure AD Domain Services
Pokud se jednomu nebo více uživatelům v tenantovi Azure AD nemůže přihlásit k nově vytvořené spravované doméně, proveďte následující kroky pro řešení potíží:

* **Přihlášení pomocí formátu UPN:** Zkuste se přihlásit pomocí formátu UPN (například joeuser@contoso.com) namísto formátu SAMAccountName (CONTOSO\joeuser). SAMAccountName se může automaticky vygenerovat pro uživatele, jejichž předpona hlavního názvu uživatele je příliš dlouhá nebo je stejná jako jiný uživatel ve spravované doméně. Formát UPN je v rámci tenanta Azure AD zaručený jako jedinečný.

> [!NOTE]
> K přihlášení do spravované domény Azure AD Domain Services doporučujeme použít formát UPN.
>
>

* Zkontrolujte, že jste [povolili synchronizaci hesel](active-directory-ds-getting-started-password-sync.md) podle kroků uvedených v příručce Začínáme.
* **Externí účty:** Ujistěte se, že příslušný uživatelský účet není externím účtem v tenantovi Azure AD. Příklady externích účtů zahrnují účty Microsoft (napříkladjoe@live.com) nebo uživatelské účty z externího adresáře služby Azure AD. Vzhledem k tomu, že Azure AD Domain Services nemá pověření pro takové uživatelské účty, tito uživatelé se nebudou moci přihlásit ke spravované doméně.
* **Synchronizované účty:** Pokud jsou ovlivněné uživatelské účty synchronizované z místního adresáře, ověřte, že:

  * Nasadili jste nebo aktualizovali [nejnovější doporučenou verzi Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).
  * Nakonfigurovali jste Azure AD Connect k [provedení úplné synchronizace](active-directory-ds-getting-started-password-sync.md).
  * V závislosti na velikosti adresáře může trvat nějakou dobu, než jsou uživatelské účty a hodnoty hash přihlašovacích údajů dostupné v Azure AD Domain Services. Před opakováním pokusu o ověření zajistěte, abyste čekali dostatečně dlouho.
  * Pokud potíže potrvají i po ověření výše uvedených kroků, zkuste restartovat službu Microsoft Azure AD Sync. Z synchronizačního počítače spusťte příkazový řádek a spusťte následující příkazy:

    1. NET STOP ' Microsoft Azure AD Sync '
    2. NET Start ' Microsoft Azure AD Sync '
* **Jenom cloudové účty**: Pokud je ovlivněný uživatelský účet jediným uživatelským účtem, ujistěte se, že uživatel změnil heslo po povolení Azure AD Domain Services. Tento krok způsobí, že se vygenerují hodnoty hash přihlašovacích údajů potřebné pro Azure AD Domain Services.
* **Ověřte, zda je uživatelský účet aktivní**: Pokud je účet uživatele uzamčený, nebude se moct přihlásit, dokud nebude znovu aktivní jeho účet. Pět neplatných pokusů o zadání hesla ve spravované doméně způsobí, že se uživatelský účet zamkne po dobu 30 minut. Po 30 minutách se uživatelský účet automaticky odemkne.
  * Neplatné pokusy o zadání hesla ve spravované doméně nezamkne uživatelský účet ve službě Azure AD. Uživatelský účet je uzamčený pouze v rámci vaší Azure AD Domain Services spravované domény. Podívejte se na stav uživatelského účtu pomocí konzoly pro správu služby Active Directory (ADAC) pro spravovanou doménu Azure služba AD DS, nikoli v Azure AD.
  * Můžete také [nakonfigurovat podrobné zásady pro hesla, které mění výchozí prahovou hodnotu uzamčení a dobu trvání](https://docs.microsoft.com/azure/active-directory-domain-services/password-policy).

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Ve spravované doméně je nejmíň jedna výstraha.

Další informace najdete v článku věnovaném [řešení potíží s výstrahami](troubleshoot-alerts.md) v tématu Jak vyřešit výstrahy ve spravované doméně.

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Uživatelé odebrání z vašeho tenanta Azure AD se ze spravované domény neodebrali.
Azure AD vás chrání před náhodným odstraněním objektů uživatelů. Když odstraníte uživatelský účet z vašeho tenanta Azure AD, odpovídající objekt uživatele se přesune do složky Koš. Když je tato operace odstranění synchronizovaná do vaší spravované domény, způsobí, že odpovídající účet uživatele bude označen jako zakázaný. Tato funkce vám pomůže obnovit nebo zrušit odstranění uživatelského účtu později.

Uživatelský účet zůstává ve spravované doméně v zakázaném stavu, i když v adresáři Azure AD znovu vytvoříte uživatelský účet se stejným hlavním názvem uživatele (UPN). Pokud chcete odebrat uživatelský účet ze spravované domény, musíte ho nuceně odstranit z vašeho tenanta Azure AD.

Pokud chcete uživatelský účet úplně odebrat ze spravované domény, odstraňte ho trvale z vašeho tenanta Azure AD. Použijte rutinu `-RemoveFromRecycleBin` prostředí PowerShell s možností, jak je popsáno v tomto [článku na webu MSDN.](/previous-versions/azure/dn194132(v=azure.100)) `Remove-MsolUser`


## <a name="contact-us"></a>Kontaktujte nás
Kontaktujte tým Azure Active Directory Domain Services produktů, abyste mohli [sdílet zpětnou vazbu nebo podporu](contact-us.md).
