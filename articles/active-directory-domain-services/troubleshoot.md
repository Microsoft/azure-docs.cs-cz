---
title: Řešení potíží s Azure Active Directory Domain Services | Microsoft Docs
description: Naučte se řešit běžné chyby při vytváření nebo správě Azure Active Directory Domain Services
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: 9593fe71fc4a29678d58d7c67699210a4a39f95e
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967371"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Běžné chyby a kroky při řešení potíží pro Azure Active Directory Domain Services

Jako centrální součást identity a ověřování pro aplikace Azure Active Directory Domain Services (Azure služba AD DS) někdy problémy. Pokud narazíte na problémy, budete mít k dispozici několik běžných chybových zpráv a souvisejících kroků pro řešení potíží, které vám pomohou znovu spustit. Pro další pomoc s řešením potíží můžete také [otevřít žádost o podporu Azure][azure-support] .

Tento článek popisuje kroky pro řešení běžných problémů v Azure služba AD DS.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Nemůžete povolit Azure AD Domain Services pro adresář služby Azure AD

Pokud máte problémy s povolením služby Azure služba AD DS, přečtěte si následující běžné chyby a kroky k jejich vyřešení:

| **Ukázková chybová zpráva** | **Řešení** |
| --- |:--- |
| *Název aaddscontoso.com se již v této síti používá. Zadejte název, který se nepoužívá.* |[Konflikt názvů domén ve virtuální síti](troubleshoot.md#domain-name-conflict) |
| *V tomto tenantovi Azure AD nešlo povolit doménové služby. Služba nemá dostatečná oprávnění k aplikaci s názvem Azure AD Domain Services Sync. Odstraňte aplikaci nazvanou Azure AD Domain Services Sync a potom se pokuste pro vašeho tenanta Azure AD povolit Domain Services.* |[Služba Domain Services nemá dostatečná oprávnění k aplikaci Azure AD Domain Services Sync.](troubleshoot.md#inadequate-permissions) |
| *V tomto tenantovi Azure AD nešlo povolit doménové služby. Aplikace Domain Services ve vašem tenantovi Azure AD nemá požadovaná oprávnění k povolení doménových služeb. Odstraňte aplikaci s identifikátorem aplikace d87dcbc6-a371-462e-88e3-28ad15ec4e64 a potom se pokuste pro vašeho tenanta Azure AD povolit Domain Services.* |[Aplikace Domain Services není ve vašem tenantovi Azure AD správně nakonfigurovaná.](troubleshoot.md#invalid-configuration) |
| *V tomto tenantovi Azure AD nešlo povolit doménové služby. Aplikace Microsoft Azure AD je v tenantovi Azure AD zakázaná. Povolte aplikaci s identifikátorem aplikace 00000002-0000-0000-C000-000000000000 a potom se pokuste pro vašeho tenanta Azure AD povolit Domain Services.* |[Aplikace Microsoft Graph je v tenantovi Azure AD zakázaná.](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Konflikt názvů domén

**Chybová zpráva**

*Název aaddscontoso.com se již v této síti používá. Zadejte název, který se nepoužívá.*

**Řešení**

Ověřte, že nemáte existující prostředí služba AD DS se stejným názvem domény, nebo s partnerským vztahem, virtuální síť. Můžete mít například doménu služba AD DS s názvem *aaddscontoso.com* , která běží na virtuálních počítačích Azure. Když se pokusíte povolit spravovanou doménu Azure služba AD DS se stejným názvem domény *aaddscontoso.com* ve virtuální síti, požadovaná operace se nezdařila.

Příčinou tohoto selhání je konflikty názvů pro název domény ve virtuální síti. Vyhledávání DNS kontroluje, jestli existující služba AD DS prostředí reaguje na požadovaný název domény. Pokud chcete tuto chybu vyřešit, použijte pro nastavení spravované domény jiný název nebo zrušte zřízení stávající domény služba AD DS a pak zkuste znovu povolit Azure služba AD DS.

### <a name="inadequate-permissions"></a>Nedostatečná oprávnění

**Chybová zpráva**

*V tomto tenantovi Azure AD nešlo povolit doménové služby. Služba nemá dostatečná oprávnění k aplikaci s názvem Azure AD Domain Services Sync. Odstraňte aplikaci nazvanou Azure AD Domain Services Sync a potom se pokuste pro vašeho tenanta Azure AD povolit Domain Services.*

**Řešení**

Ověřte, jestli existuje aplikace s názvem *Azure AD Domain Services synchronizovat* v adresáři služby Azure AD. Pokud tato aplikace existuje, odstraňte ji a zkuste to znovu, abyste mohli Azure služba AD DS povolit. Chcete-li vyhledat existující aplikaci a v případě potřeby ji odstranit, proveďte následující kroky:

1. V Azure Portal v navigační nabídce vlevo vyberte **Azure Active Directory** .
1. Vyberte **podnikové aplikace**. Zvolte možnost *všechny aplikace* z rozevírací nabídky **Typ aplikace** a pak vyberte **použít**.
1. Do vyhledávacího pole zadejte *Azure AD Domain Services synchronizovat*. Pokud aplikace existuje, vyberte ji a zvolte **Odstranit**.
1. Po odstranění aplikace se pokuste službu Azure služba AD DS znovu povolit.

### <a name="invalid-configuration"></a>Neplatná konfigurace

**Chybová zpráva**

*V tomto tenantovi Azure AD nešlo povolit doménové služby. Aplikace Domain Services ve vašem tenantovi Azure AD nemá požadovaná oprávnění k povolení doménových služeb. Odstraňte aplikaci s identifikátorem aplikace d87dcbc6-a371-462e-88e3-28ad15ec4e64 a potom se pokuste pro vašeho tenanta Azure AD povolit Domain Services.*

**Řešení**

Ověřte, jestli máte v adresáři Azure AD existující aplikaci s názvem *AzureActiveDirectoryDomainControllerServices* s identifikátorem aplikace *d87dcbc6-a371-462e-88e3-28ad15ec4e64* . Pokud tato aplikace existuje, odstraňte ji a zkuste to znovu, abyste mohli Azure služba AD DS povolit.

Pomocí následujícího skriptu prostředí PowerShell vyhledejte existující instanci aplikace a v případě potřeby ji odstraňte:

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

### <a name="microsoft-graph-disabled"></a>Microsoft Graph zakázané

**Chybová zpráva**

*V tomto tenantovi Azure AD nešlo povolit doménové služby. Aplikace Microsoft Azure AD je v tenantovi Azure AD zakázaná. Povolte aplikaci s identifikátorem aplikace 00000002-0000-0000-C000-000000000000 a potom se pokuste pro vašeho tenanta Azure AD povolit Domain Services.*

**Řešení**

Ověřte, zda jste zakázali aplikaci s identifikátorem *00000002-0000-0000-C000-000000000000*. Tato aplikace je Microsoft Azure AD aplikace a poskytuje Graph API přístup k vašemu tenantovi služby Azure AD. Aby bylo možné synchronizovat tenanta Azure AD, musí být tato aplikace povolená.

Chcete-li zjistit stav této aplikace a v případě potřeby ji povolit, proveďte následující kroky:

1. V Azure Portal v navigační nabídce vlevo vyberte **Azure Active Directory** .
1. Vyberte **podnikové aplikace**. Zvolte možnost *všechny aplikace* z rozevírací nabídky **Typ aplikace** a pak vyberte **použít**.
1. Do vyhledávacího pole zadejte *00000002-0000-0000-C000-00000000000*. Vyberte aplikaci a pak zvolte **vlastnosti**.
1. Pokud se **povolí uživatelům přihlášení** , nastavte hodnotu na *Ano*a *pak*vyberte **Uložit**.
1. Jakmile aplikaci povolíte, zkuste službu Azure služba AD DS znovu povolit.

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Uživatelé se nemůžou přihlásit ke spravované doméně Azure AD Domain Services

Pokud se jeden nebo více uživatelů v tenantovi Azure AD nemůže přihlásit ke spravované doméně, proveďte následující kroky pro řešení potíží:

* **Formát přihlašovacích údajů** – zkuste zadat přihlašovací údaje pomocí formátu UPN, například `dee@aaddscontoso.onmicrosoft.com` . Formát hlavního názvu uživatele (UPN) je doporučeným způsobem, jak zadat přihlašovací údaje v Azure služba AD DS. Ujistěte se, že je tento hlavní název uživatele ve službě Azure AD správně nakonfigurovaný.

    *SAMAccountName* pro váš účet, například *AADDSCONTOSO\driley* , se může vygenerovat automaticky, pokud je ve vašem tenantovi víc uživatelů se stejnou předponou UPN nebo pokud je předpona hlavního názvu uživatele moc dlouhá. Proto se formát *sAMAccountName* pro váš účet může lišit od toho, co očekáváte nebo používáte ve vaší místní doméně.

* **Synchronizace hesel** – Ujistěte se, že jste povolili synchronizaci hesel [jenom pro cloudové uživatele][cloud-only-passwords] nebo pro [hybridní prostředí pomocí Azure AD Connect][hybrid-phs].
    * **Hybrid synchronizované účty:** Pokud jsou příslušné uživatelské účty synchronizované z místního adresáře, ověřte následující oblasti:
    
      * Nasadili jste nebo aktualizovali [nejnovější doporučenou verzi Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).
      * Nakonfigurovali jste Azure AD Connect k [provedení úplné synchronizace][hybrid-phs].
      * V závislosti na velikosti adresáře může trvat nějakou dobu, než se uživatelské účty a hodnoty hash přihlašovacích údajů zpřístupní ve spravované doméně. Před pokusem o ověření vůči spravované doméně nezapomeňte počkat dostatečně dlouho.
      * Pokud potíže potrvají po ověření předchozích kroků, zkuste restartovat *službu Microsoft Azure AD Sync*. Na serveru Azure AD Connect otevřete příkazový řádek a spusťte následující příkazy:
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * **Pouze cloudové účty**: Pokud je ovlivněný uživatelský účet pouze cloudový uživatelský účet, ujistěte se, že [uživatel změnil heslo po povolení služby Azure služba AD DS][cloud-only-passwords]. Toto resetování hesla způsobí vygenerování požadovaných hodnot hash přihlašovacích údajů pro spravovanou doménu.

* **Ověřte, zda je uživatelský účet aktivní**: ve výchozím nastavení pět neplatných pokusů o zadání hesla během 2 minut ve spravované doméně způsobí, že se uživatelský účet zamkne po dobu 30 minut. Uživatel se nemůže přihlásit, pokud je účet uzamčen. Po 30 minutách se uživatelský účet automaticky odemkne.
  * Neplatné pokusy o zadání hesla ve spravované doméně nezamkne uživatelský účet ve službě Azure AD. Uživatelský účet je uzamčen pouze ve spravované doméně. Podívejte se na stav uživatelského účtu v *konzole pro správu služby Active Directory (ADAC)* pomocí [virtuálního počítače pro správu][management-vm], nikoli v Azure AD.
  * Můžete taky [nakonfigurovat podrobné zásady pro hesla][password-policy] , abyste změnili výchozí prahovou hodnotu uzamčení a dobu trvání.

* **Externí účty** – ověřte, že účet ovlivněného uživatele není externím účtem v TENANTOVI Azure AD. Příklady externích účtů zahrnují účty Microsoft, jako například `dee@live.com` uživatelské účty z externího adresáře služby Azure AD. V Azure služba AD DS neukládají přihlašovací údaje externích uživatelských účtů, takže se nemůžou přihlásit ke spravované doméně.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Ve spravované doméně je nejmíň jedna výstraha.

Pokud jsou ve spravované doméně aktivní výstrahy, může proces ověřování zabránit správnému fungování.

Pokud chcete zjistit, jestli existují aktivní výstrahy, [Zkontrolujte stav spravované domény][check-health]. Pokud se zobrazí nějaké výstrahy, [vyřešte je a vyřešte][troubleshoot-alerts].

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Uživatelé odebraní z vašeho tenanta Azure AD nejsou odebraní z vaší spravované domény

Azure AD chrání před náhodným odstraněním uživatelských objektů. Když odstraníte uživatelský účet z tenanta Azure AD, odpovídající objekt uživatele se přesune do složky Koš. Když je tato operace odstranění synchronizovaná s vaší spravovanou doménou, je odpovídající uživatelský účet označený jako zakázaný. Tato funkce vám pomůže obnovit a zrušit uživatelský účet.

Uživatelský účet zůstává ve spravované doméně v zakázaném stavu, a to i v případě, že znovu vytvoříte uživatelský účet se stejným hlavním názvem uživatele (UPN) v adresáři služby Azure AD. Pokud chcete odebrat uživatelský účet ze spravované domény, musíte ho nuceně odstranit z tenanta Azure AD.

Pokud chcete plně odebrat uživatelský účet ze spravované domény, odstraňte ho trvale z tenanta Azure AD pomocí rutiny [Remove-MsolUser][Remove-MsolUser] PowerShellu s `-RemoveFromRecycleBin` parametrem.

## <a name="next-steps"></a>Další kroky

Pokud budete mít i nadále problémy, [otevřete žádost o podporu pro Azure][azure-support] , kde najdete další pomoc při řešení potíží.

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
