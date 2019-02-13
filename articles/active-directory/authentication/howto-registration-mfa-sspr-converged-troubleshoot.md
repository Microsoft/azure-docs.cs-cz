---
title: Zakázat sblížené registrace pro samoobslužné resetování HESLA Azure AD a vícefaktorové ověřování (Public preview)
description: Zakázat ověřování Azure Multi-Factor Authentication AD a resetování hesla pomocí samoobslužné služby registrace (Public preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89423793dd7a93dfa1f324678fccb0b3ac17a8f3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56186043"
---
# <a name="disable-azure-ad-converged-registration-public-preview"></a>Zakázat Azure AD konvergované registrace (Public preview)

Pokud uživatel zaregistruje jejich telefonní číslo nebo mobilní aplikace na novém konvergované prostředí, naše služby razítka sada příznaků (StrongAuthenticationMethods) pro tyto metody na tohoto uživatele. Tato funkce umožňuje uživateli provádět Azure Multi-Factor Authentication (MFA) u těchto metod pokaždé, když se vyžaduje vícefaktorové ověřování.

Metody, které uživatelé registrovat prostřednictvím tohoto nového prostředí mít nastavenou vlastnost StrongAuthenticationMethods. Toto chování se vrátí taky po verzi public preview je k dispozici. Pokud správce umožňuje verzi preview, uživatelé registrovat prostřednictvím nové prostředí a správce zakáže náhled, uživatelům se může nechtěně zaregistrovat pro vícefaktorové ověřování také.

Pokud uživatel, který dokončí konvergované registrace přejde na aktuální stránku registrace samoobslužného resetování HESLA na [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup), se zobrazí výzva k provedení MFA před přístupem k této stránce. Tento krok je očekávané chování z hlediska technické, ale pro uživatele, kteří byla dříve registrována pro samoobslužné resetování HESLA, se jenom tento krok je nové chování. I když tento krok navíc zlepšit stav zabezpečení uživatele tím, že poskytuje další úroveň zabezpečení, Správce může být vhodné vrátit svým uživatelům tak, aby už nejsou schopná provádět vícefaktorové ověřování.  

## <a name="how-to-roll-back-users"></a>Tom, jak vrátit zpět uživatele

Pokud jste jako správce chcete resetovat nastavení MFA pro uživatele, vytvořili jsme skript prostředí PowerShell, který vymaže vlastnost StrongAuthenticationMethods pro mobilní aplikace uživatele a/nebo telefonní číslo. Spuštěním tohoto skriptu pro vaše uživatele znamená, že se bude muset znovu zaregistrovat pro vícefaktorové ověřování v případě potřeby. Doporučujeme, abyste před vrácení zpět všech ovlivněných uživatelů: testování vrácení zpět s uživateli jednu nebo dvě.

Následující kroky umožňují vrátit zpět, uživatele nebo skupiny uživatelů:

### <a name="pre-requisites"></a>Požadavky

1. Je potřeba nainstalovat příslušnými moduly Azure AD PowerShell. V okně Powershellu spusťte tyto příkazy pro instalaci modulů:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Uložte seznam ID nebo ID objektů ovlivněného uživatele k počítači jako textový soubor s jedno ID na řádek. Poznamenejte si umístění souboru.
1. Uložte následující skript do svého počítače a poznamenejte si umístění skriptu:

```powershell
<# 
//********************************************************
//*                                                      *
//*   Copyright (C) Microsoft. All rights reserved.      *
//*                                                      *
//********************************************************
#>

param($path)

# Define Remediation Fn
function RemediateUser {

    param  
    (
        $ObjectId
    )

    $user = Get-MsolUser -ObjectId $ObjectId

    Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

    $hasMfaRelyingParty = $false
    foreach($p in $user.StrongAuthenticationRequirements)
    {
        if ($p.RelyingParty -eq "*")
        {
            $hasMfaRelyingParty = $true
            Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
        }
    }

    if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
    {
        Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
        Write-Host "Rolling back user ..." -ForegroundColor Yellow
        Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
        Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
    }
    else
    {
        Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
    }

    Write-Host ""
    Start-Sleep -Milliseconds 750
}

# Connect
Import-Module MSOnline
Connect-MsolService

foreach($line in Get-Content $path)
{
    RemediateUser -ObjectId $line
}
```

### <a name="rollback"></a>Vrácení zpět

V okně Powershellu spusťte následující příkaz po aktualizaci zvýrazněného umístění. Zadejte přihlašovací údaje globálního správce po zobrazení výzvy. Skript se výstup výsledku operace aktualizace jednotlivých uživatelů.

`<script location> -path <user file location>`

## <a name="disable-preview-experience"></a>Zakázat prostředí ve verzi preview

Pokud chcete zakázat prostředí ve verzi preview pro vaše uživatele, proveďte následující kroky:

1. Přihlaste se k webu Azure portal jako globální správce nebo Správce uživatelů.
2. Přejděte do **Azure Active Directory**, **uživatelská nastavení**, **umožňuje spravovat nastavení přístupu funkce ve verzi preview panel**.
3. V části **uživatelé můžou používat funkce verze preview pro registraci a správu bezpečnostních údajů**, nastavte na volič **žádný** a klikněte na tlačítko **Uložit**.

Uživatelům se už výzva k registraci pomocí prostředí ve verzi preview.

## <a name="next-steps"></a>Další postup

[Další informace o verzi public preview sblížené registrace pro samoobslužné resetování hesla a ověřování Azure Multi-Factor Authentication](concept-registration-mfa-sspr-converged.md)
