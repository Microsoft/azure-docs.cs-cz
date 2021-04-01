---
title: Vyřešit výstrahy instančního objektu v Azure AD Domain Services | Microsoft Docs
description: Naučte se řešit výstrahy konfigurace instančního objektu pro Azure Active Directory Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 15dba02d3db45d663b8ba0aa7eb93bbc3f388532
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645870"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>Známé problémy: výstrahy instančního objektu v Azure Active Directory Domain Services

[Instanční objekty](../active-directory/develop/app-objects-and-service-principals.md) jsou aplikace, které platforma Azure používá ke správě, aktualizaci a údržbě spravované domény služby Azure Active Directory Domain Services (Azure služba AD DS). Pokud dojde k odstranění instančního objektu, bude to mít vliv na funkčnost ve spravované doméně.

Tento článek vám pomůže odstranit a vyřešit výstrahy konfigurace související s instančním objektem.

## <a name="alert-aadds102-service-principal-not-found"></a>Upozornění AADDS102: instanční objekt se nenašel.

### <a name="alert-message"></a>Zpráva výstrahy

*Instanční objekt vyžadovaný pro správné fungování Azure AD Domain Services se odstranil z adresáře služby Azure AD. Tato konfigurace má vliv na schopnost Microsoftu monitorovat, spravovat, opravovat a synchronizovat vaši spravovanou doménu.*

Pokud se požadovaný objekt služby odstraní, platforma Azure nemůže provádět automatizované úlohy správy. Spravovaná doména nemusí správně používat aktualizace nebo vytvářet zálohy.

### <a name="check-for-missing-service-principals"></a>Kontrolovat chybějící objekty služby

Chcete-li zjistit, který instanční objekt chybí a je třeba jej znovu vytvořit, proveďte následující kroky:

1. V Azure Portal v navigační nabídce vlevo vyberte **Azure Active Directory** .
1. Vyberte **podnikové aplikace**. Zvolte možnost *všechny aplikace* z rozevírací nabídky **Typ aplikace** a pak vyberte **použít**.
1. Vyhledejte všechna následující ID aplikací. Pokud se nenajde žádná existující aplikace, použijte postup *řešení* k vytvoření instančního objektu nebo opětovné registraci oboru názvů.

    | ID aplikace | Řešení |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Znovu vytvořit chybějící instanční objekt](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [Znovu zaregistrujte obor názvů Microsoft. AAD.](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [Znovu zaregistrujte obor názvů Microsoft. AAD.](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Znovu zaregistrujte obor názvů Microsoft. AAD.](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>Znovu vytvořit chybějící instanční objekt

Pokud v adresáři Azure AD chybí ID aplikace *2565bd9d-DA50-47d4-8B85-4c97f669dc36* , použijte Azure AD PowerShell k provedení následujících kroků. Další informace najdete v tématu [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

1. V případě potřeby nainstalujte modul Azure AD PowerShell a naimportujte ho následujícím způsobem:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. Nyní znovu vytvořte instanční objekt pomocí rutiny [New-AzureAdServicePrincipal][New-AzureAdServicePrincipal] :

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

Stav spravované domény se automaticky aktualizuje během dvou hodin a výstraha se odstraní.

### <a name="re-register-the-microsoft-aad-namespace"></a>Znovu zaregistrujte obor názvů Microsoft AAD.

Pokud v adresáři služby Azure AD chybí ID aplikace *443155a6-77f3-45e3-882b-22b3a8d431fb*, *abba844e-bc0e-44b0-947a-dc74e5d09022* nebo *d87dcbc6-a371-462e-88e3-28ad15ec4e64* , proveďte následující kroky a znovu zaregistrujte poskytovatele prostředků *Microsoft. aad* :

1. V Azure Portal vyhledejte a vyberte **předplatná**.
1. Vyberte předplatné přidružené k vaší spravované doméně.
1. V levém navigačním panelu vyberte **poskytovatelé prostředků**.
1. Vyhledejte *Microsoft. aad* a pak vyberte **znovu registrovat**.

Stav spravované domény se automaticky aktualizuje během dvou hodin a výstraha se odstraní.

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Výstraha AADDS105: aplikace synchronizace hesel je zastaralá.

### <a name="alert-message"></a>Zpráva výstrahy

*Instanční objekt s ID aplikace "d87dcbc6-a371-462e-88e3-28ad15ec4e64" byl odstraněn a pak znovu vytvořen. Opětovné vytvoření opouští za nekonzistentní oprávnění na Azure AD Domain Services prostředky, které jsou potřeba pro obsluhu vaší spravované domény. Může to mít vliv na synchronizaci hesel ve spravované doméně.*

Azure služba AD DS automaticky synchronizuje uživatelské účty a přihlašovací údaje ze služby Azure AD. Pokud dojde k potížím s aplikací služby Azure AD, která se používá pro tento proces, synchronizace přihlašovacích údajů mezi Azure služba AD DS a Azure AD se nezdařila.

### <a name="resolution"></a>Řešení

K opětovnému vytvoření aplikace služby Azure AD používané pro synchronizaci přihlašovacích údajů použijte Azure AD PowerShell k provedení následujících kroků. Další informace najdete v tématu [instalace Azure AD PowerShellu](/powershell/azure/active-directory/install-adv2).

1. V případě potřeby nainstalujte modul Azure AD PowerShell a naimportujte ho následujícím způsobem:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Nyní odstraňte starou aplikaci a objekt pomocí následujících rutin PowerShellu:

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $spObject
    ```

Po odstranění obou aplikací se platforma Azure automaticky znovu vytvoří a pokusí se obnovit synchronizaci hesel. Stav spravované domény se automaticky aktualizuje během dvou hodin a výstraha se odstraní.

## <a name="next-steps"></a>Další kroky

Pokud stále máte problémy, [otevřete žádost o podporu Azure][azure-support] , kde najdete další pomoc při řešení potíží.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
