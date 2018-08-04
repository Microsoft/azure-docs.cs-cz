---
title: 'Azure Active Directory Domain Services: Řešení potíží s konfigurací instanční objekt služby | Dokumentace Microsoftu'
description: Řešení potíží s konfigurací instanční objekt služby Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ergreenl
ms.openlocfilehash: 407b9732574880cd64036e92fe0c7fac169b7346
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503280"
---
# <a name="troubleshoot-invalid-service-principal-configuration-for-your-managed-domain"></a>Řešení potíží s neplatná konfigurace instančního objektu vaší spravované domény

Tento článek pomáhá odstraňovat potíže a řešit chyby konfigurace související se instanční objekt služby, které za následek následující upozornění:

## <a name="alert-aadds102-service-principal-not-found"></a>Upozornění AADDS102: Instanční objekt služby nebyl nalezen

**Zpráva s výstrahou:** *instančního objektu A vyžaduje se pro Azure AD Domain Services fungovat správně se odstranil z adresáře služby Azure AD. Tato konfigurace má dopad na schopnost Microsoftu monitorovat, spravovat, opravovat a synchronizovat vaši spravovanou doménu.*

[Instanční](../active-directory/develop/active-directory-application-objects.md) jsou aplikace, které společnost Microsoft používá ke správě, aktualizovat a spravovat vaši spravovanou doménu. Pokud jsou odstraněny, přestane fungovat Microsoftu neblokujete možnost pro vaši doménu.


## <a name="check-for-missing-service-principals"></a>Vyhledat chybějící instančních objektů
Určit, která služba objekty zabezpečení muset znovu vytvořit pomocí následujících kroků:

1. Přejděte [podnikové aplikace – všechny aplikace](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) stránky na webu Azure Portal.
2. V **zobrazit** rozevíracího seznamu vyberte **všechny aplikace** a klikněte na tlačítko **použít**.
3. Pomocí následující tabulky, hledání pro každé ID aplikace vložením ID do vyhledávacího pole a stisknutím klávesy enter. Pokud výsledky hledání jsou prázdné, je nutné znovu vytvořit instanční objekt pomocí následujících kroků ve sloupci "řešení".

| ID aplikace | Řešení |
| :--- | :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Znovu vytvořte chybějící instančního objektu pomocí Powershellu](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [Znovu zaregistrovat do oboru názvů Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [Znovu zaregistrovat do oboru názvů Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Instanční objekty, které vlastní opravit](#service-principals-that-self-correct) |

## <a name="recreate-a-missing-service-principal-with-powershell"></a>Znovu vytvořte chybějící instančního objektu pomocí Powershellu
Postupujte podle těchto kroků, pokud objekt služby s ID ```2565bd9d-da50-47d4-8b85-4c97f669dc36``` chybí z adresáře služby Azure AD.

**Řešení:** budete potřebovat Azure AD Powershellu k provedení těchto kroků. Informace o instalaci Azure AD Powershellu najdete v tématu [v tomto článku](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

A tento problém vyřešit, zadejte v okně Powershellu následující příkazy:
1. Instalace modulu Azure AD PowerShell a importujte ho.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Zkontrolujte, zda je objekt služby, vyžaduje se pro Azure AD Domain Services chybí ve vašem adresáři spuštěním následujícího příkazu Powershellu:

    ```powershell
    Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
    ```

3. Vytvoření instančního objektu služby tak, že zadáte následující příkaz Powershellu:

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

4. Po vytvoření na chybějící službu objektu zabezpečení, počkejte po dobu dvou hodin a kontrola stavu spravované domény.


## <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>Znovu zaregistrovat do oboru názvů Microsoft AAD pomocí webu Azure portal
Postupujte podle těchto kroků, pokud objekt služby s ID ```443155a6-77f3-45e3-882b-22b3a8d431fb``` nebo ```abba844e-bc0e-44b0-947a-dc74e5d09022``` chybí z adresáře služby Azure AD.

**Řešení:** pomocí následujících kroků obnovíte Domain Services na adresáři:

1. Přejděte [předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) stránky na webu Azure Portal.
2. Vyberte předplatné, z tabulky, který je přidružený k vaší spravované domény
3. Použití na levém navigačním panelu zvolte **poskytovatelů prostředků**
4. Vyhledejte "Microsoft.AAD" v tabulce a klikněte na **znovu zaregistrovat.**
5. K zajištění, že výstraha vyřeší, zobrazení stavu stránky vaší spravované domény do dvou hodin.


## <a name="service-principals-that-self-correct"></a>Instanční objekty, které vlastní opravit
Postupujte podle těchto kroků, pokud objekt služby s ID ```d87dcbc6-a371-462e-88e3-28ad15ec4e64``` chybí z adresáře služby Azure AD.

**Řešení:** Azure AD Domain Services může rozpoznat, kdy se tento konkrétní instanční objekt se nenašel, chybně nakonfigurovaná nebo odstranit. Služba automaticky znovu vytvoří tento instanční objekt. Ale budete muset aplikaci odstranit a objekt, který pracoval s odstraněné aplikace, stejně jako při certifikace navyšování, aplikace a objekt již nebude moci upravit nový instanční objekt. To povede k novou chybu ve vaší doméně. Postupujte podle kroků uvedených v [části AADDS105](#alert-aadds105-password-synchronization-application-is-out-of-date) jak tomuto problému zabránit. Po zkontrolujte stav vaší spravované domény po dvou hodin k zajištění, že nový instanční objekt služby byly znovu vytvořeny.


## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Upozornění AADDS105: Aplikace synchronizace hesla je zastaralá

**Zpráva s výstrahou:** odstranila a potom znovu vytvoří instanční objekt s ID aplikace "d87dcbc6-a371-462e-88e3-28ad15ec4e64". Opětovnému vytvoření zůstanou za nekonzistentní oprávnění v Azure AD Domain Services prostředky potřebné pro služby spravované domény. Může mít vliv na synchronizaci hesel ve vaší spravované doméně.


**Řešení:** budete potřebovat Azure AD Powershellu k provedení těchto kroků. Informace o instalaci Azure AD Powershellu najdete v tématu [v tomto článku](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

A tento problém vyřešit, zadejte v okně Powershellu následující příkazy:
1. Instalace modulu Azure AD PowerShell a importujte ho.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```
2. Odstranit staré aplikace a pomocí následujících příkazů Powershellu

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```
3. Po odstranění i v systému se sám opravit a znovu vytvoří aplikace potřebné pro synchronizaci hesel. Zajistěte, aby že byl opraven výstrahu, počkejte po dobu dvou hodin a zkontrolovat stav vaší domény.


## <a name="contact-us"></a>Kontaktujte nás
Obraťte se na Azure Active Directory Domain Services produktovému týmu a [podělit se o názory, nebo pro podporu](active-directory-ds-contact-us.md).
