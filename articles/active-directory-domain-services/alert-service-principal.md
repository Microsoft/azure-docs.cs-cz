---
title: Řešení výstrah hlavního povinného poskytování služeb ve službě Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak řešit potíže s výstrahami konfigurace primárního serveru služby Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/20/2019
ms.author: iainfou
ms.openlocfilehash: 175bfe63176b78c5aeafc7147c46dd5ab1110325
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71257966"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>Známé problémy: Výstrahy hlavního povinného provozu služby ve službě Azure Active Directory Domain Services

[Instanční objekty](../active-directory/develop/app-objects-and-service-principals.md) jsou aplikace, které platforma Azure používá ke správě, aktualizaci a údržbě spravované domény Azure AD DS. Pokud je odstraněn instanční objekt, dojde k ovlivnění funkčnosti spravované domény služby Azure AD DS.

Tento článek vám pomůže vyřešit a vyřešit výstrahy konfigurace související s objektem zabezpečení služby.

## <a name="alert-aadds102-service-principal-not-found"></a>Výstraha AADDS102: Instanční objekt služby nebyl nalezen.

### <a name="alert-message"></a>Výstražná zpráva

*Instanční objekt požadovaný pro správnou funkci služby Azure AD Domain Services byl odstraněn z adresáře Azure AD. Tato konfigurace má vliv na schopnost společnosti Microsoft monitorovat, spravovat, opravovat a synchronizovat spravovanou doménu.*

Pokud je odstraněn ý požadovaný instanční objekt, platforma Azure nemůže provádět úlohy automatizované správy. Spravovaná doména Azure AD DS nemusí správně použít aktualizace nebo převzít zálohy.

### <a name="check-for-missing-service-principals"></a>Kontrola chybějících instančních objektů

Chcete-li zkontrolovat, který instanční objekt chybí a který je třeba znovu vytvořit, proveďte následující kroky:

1. Na webu Azure Portal vyberte **Azure Active Directory** z nabídky navigace na levé straně.
1. Vyberte **podnikové aplikace**. Z rozevírací nabídky **Typ aplikace** zvolte *Všechny aplikace* a pak vyberte **Použít**.
1. Vyhledejte každé ID aplikace. Pokud není nalezena žádná existující aplikace, postupujte podle kroků *řešení* k vytvoření instančního objektu nebo znovu zaregistrovat obor názvů.

    | ID aplikace | Řešení |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Znovu vytvořit chybějící instanční objekt](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [Opětovná registrace oboru názvů Microsoft.AAD](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [Opětovná registrace oboru názvů Microsoft.AAD](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Opětovná registrace oboru názvů Microsoft.AAD](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>Znovu vytvořit chybějící instanční objekt

Pokud id aplikace *2565bd9d-da50-47d4-8b85-4c97f669dc36* chybí ve vašem adresáři Azure AD, použijte Azure AD PowerShell k dokončení následujících kroků. Další informace najdete [v tématu instalace prostředí Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

1. Nainstalujte modul Azure AD PowerShell a importujte ho následujícím způsobem:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. Nyní znovu vytvořte instanční objekt pomocí rutiny [New-AzureAdServicePrincipal:][New-AzureAdServicePrincipal]

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

Stav spravované domény Azure AD DS se automaticky aktualizuje do dvou hodin a odebere výstrahu.

### <a name="re-register-the-microsoft-aad-namespace"></a>Opětovná registrace oboru názvů Microsoft AAD

Pokud id aplikace *443155a6-77f3-45e3-882b-22b3a8d431fb*, *abba844e-bc0e-44b0-94 7a-dc74e5d09022*nebo *d87dcbc6-a371-462e-88e3-28ad15ec4e64* chybí ve vašem adresáři Azure AD, Chcete-li znovu zaregistrovat zprostředkovatele prostředků *Microsoft.AAD,* proveďte následující kroky:

1. Na webu Azure Portal vyhledejte a vyberte **Předplatná**.
1. Zvolte předplatné přidružené k vaší spravované doméně Azure AD DS.
1. V levém navigačním panelu zvolte **Zprostředkovatelé prostředků**.
1. Vyhledejte *microsoft.aad*a vyberte **možnost Znovu zaregistrovat**.

Stav spravované domény Azure AD DS se automaticky aktualizuje do dvou hodin a odebere výstrahu.

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Výstraha AADDS105: Aplikace pro synchronizaci hesel je zastaralá

### <a name="alert-message"></a>Výstražná zpráva

*Instanční objekt s ID aplikace "d87dcbc6-a371-462e-88e3-28ad15ec4e64" byl odstraněn a znovu vytvořen. Rekreace za sebou zanechává nekonzistentní oprávnění na prostředky služby Azure AD Domain Services potřebné k provozu spravované domény. Synchronizace hesel ve spravované doméně může být ovlivněna.*

Azure AD DS automaticky synchronizuje uživatelské účty a přihlašovací údaje z Azure AD. Pokud se problém s aplikací Azure AD používá pro tento proces, synchronizace přihlašovacích údajů mezi Azure AD DS a Azure AD nezdaří.

### <a name="resolution"></a>Řešení

Chcete-li znovu vytvořit aplikaci Azure AD používanou pro synchronizaci pověření, použijte Azure AD PowerShell k dokončení následujících kroků. Další informace najdete [v tématu instalace prostředí Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

1. Nainstalujte modul Azure AD PowerShell a importujte ho následujícím způsobem:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Nyní odstraňte starou aplikaci a objekt pomocí následujících rutin prostředí PowerShell:

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```

Po odstranění obou aplikací platforma Azure automaticky znovu vytvoří a pokusí se obnovit synchronizaci hesel. Stav spravované domény Azure AD DS se automaticky aktualizuje do dvou hodin a odebere výstrahu.

## <a name="next-steps"></a>Další kroky

Pokud máte stále problémy, [otevřete žádost o podporu Azure][azure-support] pro další pomoc při řešení potíží.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
