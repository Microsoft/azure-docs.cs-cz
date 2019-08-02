---
title: 'Azure Active Directory Domain Services: Řešení potíží s instančními objekty | Microsoft Docs'
description: Odstraňování potíží s konfigurací instančního objektu pro Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: 9e5fa8c84f5e7ca58117666846b603a118826150
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234142"
---
# <a name="troubleshoot-invalid-service-principal-configurations-for-azure-active-directory-domain-services"></a>Odstraňování potíží s neplatnými konfiguracemi instančních objektů pro Azure Active Directory Domain Services

Tento článek vám pomůže vyřešit problémy s konfigurací související s instančními objekty, které jsou výsledkem následující výstrahy:

## <a name="alert-aadds102-service-principal-not-found"></a>AADDS102 výstrahy: Instanční objekt se nenašel.

**Zpráva upozornění:** *Instanční objekt vyžadovaný pro správné fungování Azure AD Domain Services se odstranil z adresáře služby Azure AD. Tato konfigurace má vliv na schopnost Microsoftu monitorovat, spravovat, opravovat a synchronizovat vaši spravovanou doménu.*

[Instanční objekty](../active-directory/develop/app-objects-and-service-principals.md) jsou aplikace, které Microsoft používá ke správě, aktualizaci a údržbě spravované domény. Pokud jsou odstraněny, přeruší schopnost společnosti Microsoft vaši doménu.


## <a name="check-for-missing-service-principals"></a>Kontrolovat chybějící objekty služby
Pomocí následujících kroků určíte, které instanční objekty se musí znovu vytvořit:

1. Přejděte na stránku [podnikové aplikace – všechny aplikace](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) v Azure Portal.
2. V rozevíracím seznamu **Zobrazit** vyberte **všechny aplikace** a klikněte na **použít**.
3. Pomocí následující tabulky vyhledejte jednotlivá ID aplikace tak, že do vyhledávacího pole vložíte ID a stisknete klávesu ENTER. Pokud jsou výsledky hledání prázdné, musíte instanční objekt znovu vytvořit podle kroků ve sloupci "řešení".

| ID aplikace | Řešení |
| :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Opětovné vytvoření chybějícího instančního objektu pomocí PowerShellu](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [Znovu zaregistrovat do oboru názvů Microsoft. AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [Znovu zaregistrovat do oboru názvů Microsoft. AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Znovu zaregistrovat do oboru názvů Microsoft. AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |

## <a name="recreate-a-missing-service-principal-with-powershell"></a>Opětovné vytvoření chybějícího instančního objektu pomocí PowerShellu
Pokud v adresáři služby Azure AD chybí instanční objekt s ```2565bd9d-da50-47d4-8b85-4c97f669dc36``` ID, postupujte podle těchto kroků.

**Řešení:** K provedení těchto kroků potřebujete Azure AD PowerShell. Informace o instalaci Azure AD PowerShellu najdete v [tomto článku](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Tento problém vyřešíte tak, že v okně PowerShellu zadáte následující příkazy:
1. Nainstalujte modul Azure AD PowerShell a naimportujte ho.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Spuštěním následujícího příkazu PowerShellu ověřte, jestli v adresáři chybí instanční objekt vyžadovaný Azure AD Domain Services:

    ```powershell
    Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
    ```

3. Vytvořte instanční objekt zadáním následujícího příkazu PowerShellu:

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

4. Po vytvoření chybějícího instančního objektu Počkejte dvě hodiny a ověřte stav spravované domény.


## <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>Znovu se zaregistrujte do oboru názvů Microsoft AAD pomocí Azure Portal
Pokud v adresáři služby Azure AD chybí instanční objekt s ```443155a6-77f3-45e3-882b-22b3a8d431fb``` ID ```abba844e-bc0e-44b0-947a-dc74e5d09022``` nebo ```d87dcbc6-a371-462e-88e3-28ad15ec4e64``` nebo, postupujte podle těchto kroků.

**Řešení:** Pomocí následujícího postupu obnovte ve svém adresáři doménové služby:

1. V Azure Portal přejděte [](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na stránku předplatná.
2. Vyberte předplatné z tabulky, která je přidružená k vaší spravované doméně.
3. Pomocí navigace na levé straně vyberte poskytovatelé **prostředků** .
4. V tabulce vyhledejte "Microsoft. AAD" a klikněte na **znovu registrovat** .
5. Chcete-li zajistit, aby byla výstraha vyřešena, zobrazte stránku stav spravované domény ve dvou hodinách.


## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>AADDS105 výstrahy: Aplikace synchronizace hesel je zastaralá.

**Zpráva upozornění:** Instanční objekt s ID aplikace "d87dcbc6-a371-462e-88e3-28ad15ec4e64" byl odstraněn a pak znovu vytvořen. Opětovné vytvoření opouští za nekonzistentní oprávnění na Azure AD Domain Services prostředky, které jsou potřeba pro obsluhu vaší spravované domény. Může to mít vliv na synchronizaci hesel ve spravované doméně.


**Řešení:** K provedení těchto kroků potřebujete Azure AD PowerShell. Informace o instalaci Azure AD PowerShellu najdete v [tomto článku](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Tento problém vyřešíte tak, že v okně PowerShellu zadáte následující příkazy:
1. Nainstalujte modul Azure AD PowerShell a naimportujte ho.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```
2. Odstraňte starou aplikaci a objekt pomocí následujících příkazů PowerShellu.

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```
3. Po odstranění dojde v systému k nápravě a opětovnému vytvoření aplikací potřebných pro synchronizaci hesel. Chcete-li zajistit nápravu výstrahy, počkejte dvě hodiny a zkontrolujte stav vaší domény.


## <a name="contact-us"></a>Kontaktujte nás
Kontaktujte tým Azure Active Directory Domain Services produktů, abyste mohli [sdílet zpětnou vazbu nebo podporu](contact-us.md).
