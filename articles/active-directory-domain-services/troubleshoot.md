---
title: Řešení potíží se službou Azure Active Directory Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak řešit běžné chyby při vytváření nebo správě služby Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: e17112cbe2a494a585cd5a09c36cfe449d3d433c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365811"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Běžné chyby a kroky řešení potíží pro službu Azure Active Directory Domain Services

Jako centrální součást identity a ověřování pro aplikace, Azure Active Directory Domain Services (Azure AD DS) má někdy problémy. Pokud narazíte na problémy, existují některé běžné chybové zprávy a související kroky řešení potíží, které vám pomohou znovu spustit věci. Kdykoli můžete také [otevřít žádost o podporu Azure][azure-support] pro další pomoc při řešení potíží.

Tento článek obsahuje postupy řešení potíží pro běžné problémy ve službě Azure AD DS.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Pro adresář Azure AD nelze povolit služby Azure AD Domain Services.

Pokud máte problémy s povolením Služby Azure AD DS, přečtěte si následující běžné chyby a kroky k jejich vyřešení:

| **Ukázková chybová zpráva** | **Rozlišení** |
| --- |:--- |
| *Název aaddscontoso.com je již v této síti používán. Zadejte název, který se nepoužívá.* |[Konflikt názvů domén ve virtuální síti](troubleshoot.md#domain-name-conflict) |
| *Služby domény nelze povolit v tomto tenantovi Služby Azure AD. Služba nemá dostatečná oprávnění k aplikaci s názvem Synchronizace doménových služeb Azure AD. Odstraňte aplikaci s názvem Synchronizace doménových služeb Azure AD a zkuste povolit služby domény pro vašeho klienta Azure AD.* |[Domain Services nemá dostatečná oprávnění k aplikaci Synchronizace doménových služeb Azure AD](troubleshoot.md#inadequate-permissions) |
| *Služby domény nelze povolit v tomto tenantovi Služby Azure AD. Aplikace Domain Services ve vašem tenantovi Azure AD nemá požadovaná oprávnění k povolení služby Domain Services. Odstraňte aplikaci s identifikátorem aplikace d87dcbc6-a371-462e-88e3-28ad15ec4e64 a zkuste povolit domain services pro vašeho klienta Azure AD.* |[Aplikace Domain Services není správně nakonfigurovaná ve vašem tenantovi Azure AD.](troubleshoot.md#invalid-configuration) |
| *Služby domény nelze povolit v tomto tenantovi Služby Azure AD. Aplikace Microsoft Azure AD je ve vašem tenantovi Azure AD deaktivována. Povolte aplikaci s identifikátorem aplikace 00000002-0000-0000-c000-00000000000000a zkuste povolit služby domény pro vašeho klienta Azure AD.* |[Aplikace Microsoft Graph je ve vašem tenantovi Azure AD deaktivována.](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Konflikt názvu domény

**Chybová zpráva**

*Název aaddscontoso.com je již v této síti používán. Zadejte název, který se nepoužívá.*

**Rozlišení**

Zkontrolujte, zda nemáte existující prostředí služby AD DS se stejným názvem domény ve stejné virtuální síti nebo ve virtuální síti s partnerským partnerem. Například můžete mít doménu služby AD DS s názvem *aaddscontoso.com,* která běží na virtuálních počítačích Azure. Při pokusu o povolení spravované domény Azure AD DS se stejným názvem domény *aaddscontoso.com* ve virtuální síti, požadovaná operace se nezdaří.

Toto selhání je způsobeno konflikty názvů pro název domény ve virtuální síti. Vyhledávání DNS zkontroluje, zda existující prostředí služby AD DS odpovídá na požadovaný název domény. Chcete-li tuto chybu vyřešit, použijte jiný název k nastavení spravované domény Služby Azure AD DS nebo zrušení zřizování stávající domény služby AD DS a zkuste znovu povolit Azure AD DS.

### <a name="inadequate-permissions"></a>Nedostatečná oprávnění

**Chybová zpráva**

*Služby domény nelze povolit v tomto tenantovi Služby Azure AD. Služba nemá dostatečná oprávnění k aplikaci s názvem Synchronizace doménových služeb Azure AD. Odstraňte aplikaci s názvem Synchronizace doménových služeb Azure AD a zkuste povolit služby domény pro vašeho klienta Azure AD.*

**Rozlišení**

Zkontrolujte, jestli je aplikace s názvem *Azure AD Domain Services Sync* ve vašem adresáři Azure AD. Pokud tato aplikace existuje, odstraňte ji a zkuste ji znovu povolit Azure AD DS. Chcete-li vyhledat existující aplikaci a v případě potřeby ji odstranit, proveďte následující kroky:

1. Na webu Azure Portal vyberte **Azure Active Directory** z nabídky navigace na levé straně.
1. Vyberte **podnikové aplikace**. Z rozevírací nabídky **Typ aplikace** zvolte *Všechny aplikace* a pak vyberte **Použít**.
1. Do vyhledávacího pole zadejte *Synchronizaci domén Azure AD*. Pokud aplikace existuje, vyberte ji a zvolte **Odstranit**.
1. Po odstranění aplikace zkuste znovu povolit Azure AD DS.

### <a name="invalid-configuration"></a>Neplatná konfigurace

**Chybová zpráva**

*Služby domény nelze povolit v tomto tenantovi Služby Azure AD. Aplikace Domain Services ve vašem tenantovi Azure AD nemá požadovaná oprávnění k povolení služby Domain Services. Odstraňte aplikaci s identifikátorem aplikace d87dcbc6-a371-462e-88e3-28ad15ec4e64 a zkuste povolit domain services pro vašeho klienta Azure AD.*

**Rozlišení**

Zkontrolujte, jestli máte existující aplikaci s názvem *AzureActiveDirectoryDomainControllerServices* s identifikátorem aplikace *d87dcbc6-a371-462e-88e3-28ad15ec4e64* v adresáři Azure AD. Pokud tato aplikace existuje, odstraňte ji a zkuste ji znovu povolit Azure AD DS.

Pomocí následujícího skriptu Prostředí PowerShell vyhledejte existující instanci aplikace a v případě potřeby ji odstraňte:

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

### <a name="microsoft-graph-disabled"></a>Microsoft Graph zakázáno

**Chybová zpráva**

*Služby domény nelze povolit v tomto tenantovi Služby Azure AD. Aplikace Microsoft Azure AD je ve vašem tenantovi Azure AD deaktivována. Povolte aplikaci s identifikátorem aplikace 00000002-0000-0000-c000-00000000000000a zkuste povolit služby domény pro vašeho klienta Azure AD.*

**Rozlišení**

Zkontrolujte, zda jste zakázali aplikaci s identifikátorem *00000002-0000-0000-c000-000000000000000*. Tato aplikace je aplikace Microsoft Azure AD a poskytuje rozhraní GRAPH API přístup k tenantovi Azure AD. Chcete-li synchronizovat klienta Azure AD, musí být tato aplikace povolena.

Chcete-li zkontrolovat stav této aplikace a v případě potřeby ji povolit, proveďte následující kroky:

1. Na webu Azure Portal vyberte **Azure Active Directory** z nabídky navigace na levé straně.
1. Vyberte **podnikové aplikace**. Z rozevírací nabídky **Typ aplikace** zvolte *Všechny aplikace* a pak vyberte **Použít**.
1. Do vyhledávacího pole zadejte *00000002-0000-0000-c000-00000000000000*. Vyberte aplikaci a pak zvolte **Vlastnosti**.
1. Pokud je **možnost Povolení k přihlášení uživatelům** nastavena na *hodnotu Ne*, nastavte hodnotu na *Ano*, vyberte možnost **Uložit**.
1. Po povolení aplikace zkuste znovu povolit Službu Azure AD DS.

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Uživatelé se nemůžou přihlásit ke spravované doméně Azure AD Domain Services

Pokud se jeden nebo více uživatelů ve vašem tenantovi Azure AD nemůže přihlásit ke spravované doméně Azure AD DS, proveďte následující kroky řešení potíží:

* **Formát pověření** – Zkuste pomocí formátu UPN zadat `dee@aaddscontoso.onmicrosoft.com`přihlašovací údaje, například . Formát HLAVNÍHO uživatele je doporučený způsob, jak zadat přihlašovací údaje ve službě Azure AD DS. Ujistěte se, že tento upn je správně nakonfigurovaný ve službě Azure AD.

    *Název SAMAccountName* pro váš účet, například *AADDSCONTOSO\driley,* může být automaticky vygenerován, pokud je ve vašem tenantovi více uživatelů se stejnou předponou UPN nebo pokud je předpona hlavního názvu uživatele příliš dlouhá. Proto se formát *SAMAccountName* pro váš účet může lišit od toho, co očekáváte nebo používáte v místní doméně.

* **Synchronizace hesel** – ujistěte se, že jste povolili synchronizaci hesel pro [uživatele pouze v cloudu][cloud-only-passwords] nebo pro [hybridní prostředí pomocí Služby Azure AD Connect][hybrid-phs].
    * **Hybridní synchronizované účty:** Pokud jsou ovlivněné uživatelské účty synchronizovány z místního adresáře, ověřte následující oblasti:
    
      * Nasadili jste nebo aktualizovali [nejnovější doporučenou verzi Služby Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).
      * Nakonfigurovali jste Azure AD Connect tak, aby [prováděla úplnou synchronizaci][hybrid-phs].
      * V závislosti na velikosti vašeho adresáře může chvíli trvat, než budou uživatelské účty a hash pověření k dispozici ve službě Azure AD DS. Ujistěte se, že čekáte dost dlouho, než se pokusíte ověřit proti spravované doméně.
      * Pokud problém přetrvává i po ověření předchozích kroků, zkuste restartovat *službu Synchronizace Microsoft Azure AD*. Na serveru Azure AD Connect otevřete příkazový řádek a spusťte následující příkazy:
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * **Účty pouze pro cloud**: Pokud je ovlivněný uživatelský účet pouze pro cloudový uživatelský účet, ujistěte se, že [uživatel změnil své heslo po povolení Služby Azure AD DS][cloud-only-passwords]. Toto resetování hesla způsobí, že se generují požadovaná hashe pověření pro služby Azure AD Domain Services.

* **Ověřte, zda je uživatelský účet aktivní**: Ve výchozím nastavení způsobí, že pět neplatných pokusů o zadání hesla během 2 minut ve spravované doméně bude uživatelský účet uzamčen po dobu 30 minut. Uživatel se nemůže přihlásit, když je účet uzamčen. Po 30 minutách se uživatelský účet automaticky odemkne.
  * Neplatné pokusy o heslo ve spravované doméně Azure AD DS nezamykají uživatelský účet ve službě Azure AD. Uživatelský účet je uzamčen pouze v rámci spravované domény. Zkontrolujte stav uživatelského účtu v *konzole ADAC (ADAC) služby Active Directory* pomocí [virtuálního počítače pro správu][management-vm], nikoli ve službě Azure AD.
  * Můžete také [nakonfigurovat zásady jemných odstupňovaných hesel][password-policy] pro změnu výchozí prahové hodnoty uzamčení a doby trvání.

* **Externí účty** – zkontrolujte, zda ovlivněný uživatelský účet není externí účet v tenantovi Azure AD. Příklady externích účtů zahrnují `dee@live.com` účty Microsoft, jako jsou účty Nebo uživatelské účty z externího adresáře Azure AD. Azure AD DS neukládá přihlašovací údaje pro externí uživatelské účty, takže se nemůžou přihlásit ke spravované doméně.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Ve spravované doméně je jedno nebo více upozornění.

Pokud existují aktivní výstrahy na spravované doméně Azure AD DS, může zabránit procesu ověřování pracovat správně.

Chcete-li zjistit, zda existují nějaké aktivní výstrahy, [zkontrolujte stav spravované domény Azure AD DS][check-health]. Pokud se zobrazí nějaká upozornění, [odstraňte jejich problémy a vyřešte je][troubleshoot-alerts].

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Uživatelé odebraní z vašeho tenanta Azure AD nejsou odebraní z vaší spravované domény

Azure AD chrání před náhodným odstraněním uživatelských objektů. Když odstraníte uživatelský účet z klienta Azure AD, odpovídající objekt uživatele se přesune do koše. Když je tato operace odstranění synchronizována se spravovanou doménou Azure AD DS, odpovídající uživatelský účet je označen jako zakázaný. Tato funkce vám pomůže obnovit nebo obnovit uživatelský účet.

Uživatelský účet zůstane ve stavu zakázáno ve spravované doméně Azure AD DS, i když znovu vytvořit uživatelský účet se stejným upn v adresáři Azure AD. Chcete-li odebrat uživatelský účet ze spravované domény Azure AD DS, musíte ho násilně odstranit z tenanta Azure AD.

Pokud chcete úplně odebrat uživatelský účet ze spravované domény Azure AD DS, odstraňte uživatele trvale z vašeho klienta Azure AD pomocí rutiny [Remove-MsolUser][Remove-MsolUser] PowerShell s parametrem. `-RemoveFromRecycleBin`

## <a name="next-steps"></a>Další kroky

Pokud máte i nadále problémy, [otevřete žádost o podporu Azure][azure-support] pro další pomoc při řešení potíží.

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
