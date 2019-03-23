---
title: Řešení potíží s kombinovaná registrace pro samoobslužné resetování HESLA Azure AD a vícefaktorové ověřování (preview) – Azure Active Directory
description: Řešení potíží s Azure Multi-Factor Authentication AD a resetování hesla pomocí samoobslužné služby kombinovaná registrace (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: d926f7312b62e788289939dfd81c236a33503b43
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370461"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>Řešení potíží s kombinovat informace o registraci zabezpečení (preview)

Informace uvedené v tomto článku můžete Příručka pro správce, kteří jsou řešení problémů s prostředím kombinovaná registrace hlášené nástrojem jejich koncové uživatele.

|     |
| --- |
| Kombinované zabezpečení informace o registraci pro resetování hesla pomocí samoobslužné služby Azure Multi-Factor Authentication a Azure AD je funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="audit-logs"></a>Protokoly auditu

Události evidované pro kombinované registrace jsou v kategorii "Metody ověřování" ve službě Azure AD protokoly auditu.

![Auditování Azure AD protokoly událostí registrace zobrazující rozhraní](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

Následuje seznam všech událostí auditu generovaných kombinovaná registrace:

| Aktivita | Status | Důvod | Popis |
| --- | --- | --- | --- |
| Uživatel zaregistrován všechny požadované bezpečnostní údaje | Úspěch | Uživatel zaregistrován všechny požadované bezpečnostní údaje. | Pokud uživatel úspěšně dokončil registraci, dojde k této události.|
| Uživatel zaregistrován všechny požadované bezpečnostní údaje | Selhání | Uživatel zrušil registraci informací o zabezpečení. | Tato událost nastane, pokud uživatel zruší registraci v režimu přerušení.|
| Uživatel zaregistrován bezpečnostní údaje | Úspěch | Uživatel zaregistrován "method". | Tato událost nastane, pokud se uživatel zaregistruje jednotlivé metody. "Method" může být aplikace Authenticator s telefonu, e-mailu, bezpečnostní otázky, heslo aplikace, alternativní telefonní atd.| 
| Uživatel zkontrolovat bezpečnostní údaje | Úspěch | Uživatel úspěšně zkontrolovat bezpečnostní údaje. | Tato událost nastane, pokud uživatel klikne na tlačítko "Vypadá v pořádku" na stránce Zkontrolovat informace o zabezpečení.|
| Uživatel zkontrolovat bezpečnostní údaje | Selhání | Uživateli se nepodařilo zkontrolovat bezpečnostní údaje. | Tato událost nastane, pokud uživatel klikne na tlačítko "Dobrá" na bezpečnostní údaje, zkontrolujte stránku, ale něco selže v back-endu.|
| Uživatel odstranil bezpečnostní údaje | Úspěch | Uživatel odstranil "method". | Této události dojde, když uživatel odstraní jednotlivé metody. "Method" může být aplikace Authenticator s telefonu, e-mailu, bezpečnostní otázky, heslo aplikace, alternativní telefonní atd.|
| Uživatel odstranil bezpečnostní údaje | Selhání | Uživatele se nepovedlo odstranit "method". | Když uživatel pokusí odstranit metodu, ale selže z nějakého důvodu dojde k této události. "Method" může být aplikace Authenticator s telefonu, e-mailu, bezpečnostní otázky, heslo aplikace, alternativní telefonní atd.|
| Uživatel změnil výchozí bezpečnostní údaje | Úspěch | Uživatel změnil výchozí bezpečnostní údaje na "method". | Když uživatel změní jejich výchozí metoda dojde k této události. "Method" může být oznámení aplikace Authenticator, kód ze své ověřovací aplikace nebo token, volání + X XXXXXXXXXX Text kód na + X XXXXXXXXX atd.|
| Uživatel změnil výchozí bezpečnostní údaje | Selhání | Změna výchozí bezpečnostní údaje na "method" uživatele se nezdařila. | Když uživatel se pokusí změnit jeho výchozí metodu, ale selže z nějakého důvodu dojde k této události. "Method" může být oznámení aplikace Authenticator, kód ze své ověřovací aplikace nebo token, volání + X XXXXXXXXXX Text kód na + X XXXXXXXXX atd.|

## <a name="troubleshooting-interrupt-mode"></a>Řešení potíží s režimu přerušení

| Příznak | Postup při řešení potíží |
| --- | --- |
| Jakou metodu můžu očekávali mi nezobrazují. | 1. Zkontrolujte, jestli má uživatel roli správce Azure AD. Pokud ano, zkontrolujte rozdíly zásad samoobslužného resetování HESLA správce. <br> 2. Určete, jestli se uživatel dojde v důsledku vynucení registrace MFA nebo vynucení registrace samoobslužného resetování HESLA. Projděte si vývojový diagram v režimech kombinovaná registrace k určení, které metody mají být zobrazena. <br> 3. Určete, jak nedávno změnila zásad MFA nebo samoobslužné resetování HESLA. Pokud byla poslední změnu, může trvat nějakou dobu aktualizované zásady rozšíření.|

## <a name="troubleshooting-manage-mode"></a>Řešení potíží s spravovat režimu

| Příznak | Postup při řešení potíží |
| --- | --- |
| Není k dispozici možnost přidávat konkrétní metody. | 1. Určete, jestli je pro vícefaktorové ověřování, nebo pro samoobslužné resetování HESLA povolené metody. <br> 2. Pokud je povolené metody, znovu tyto zásady a počkejte 1 – 2 hodiny před testováním znovu. <br> 3. Pokud je povolené metody, ujistěte se, že uživatel není již nastavit maximální počet této metody, která budete moct nastavit.|

## <a name="disable-combined-registration"></a>Zakázat kombinovaná registrace

Pokud uživatel zaregistruje jejich telefonní číslo nebo mobilní aplikace na novém kombinaci prostředí, naše služby razítka sada příznaků (StrongAuthenticationMethods) pro tyto metody na tohoto uživatele. Tato funkce umožňuje uživateli provádět Azure Multi-Factor Authentication (MFA) u těchto metod pokaždé, když se vyžaduje vícefaktorové ověřování.

Metody, které uživatelé registrovat prostřednictvím tohoto nového prostředí mít nastavenou vlastnost StrongAuthenticationMethods. Pokud správce umožňuje verzi preview, uživatelé registrovat prostřednictvím nové prostředí a správce zakáže náhled, uživatelům se může nechtěně zaregistrovat pro vícefaktorové ověřování také.

Pokud uživatel, který dokončí kombinovat registrace přejde na aktuální samoobslužného resetování hesla registrační stránku, na [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup), se zobrazí výzva k provedení MFA před přístupem k této stránce. Tento krok je očekávané chování z hlediska technické, ale pro uživatele, kteří byla dříve registrována pro samoobslužné resetování HESLA, se jenom tento krok je nové chování. I když tento krok navíc zlepšit stav zabezpečení uživatele tím, že poskytuje další úroveň zabezpečení, Správce může být vhodné vrátit svým uživatelům tak, aby už nejsou schopná provádět vícefaktorové ověřování.  

### <a name="how-to-roll-back-users"></a>Tom, jak vrátit zpět uživatele

Pokud jste jako správce chcete resetovat nastavení MFA pro uživatele, vytvořili jsme skript prostředí PowerShell, který vymaže vlastnost StrongAuthenticationMethods pro mobilní aplikace uživatele a/nebo telefonní číslo. Spuštěním tohoto skriptu pro vaše uživatele znamená, že se bude muset znovu zaregistrovat pro vícefaktorové ověřování v případě potřeby. Doporučujeme, abyste před vrácení zpět všech ovlivněných uživatelů: testování vrácení zpět s uživateli jednu nebo dvě.

Následující kroky umožňují vrátit zpět, uživatele nebo skupiny uživatelů:

#### <a name="prerequisites"></a>Požadavky

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

#### <a name="rollback"></a>Vrácení zpět

V okně Powershellu spusťte následující příkaz po aktualizaci zvýrazněného umístění. Zadejte přihlašovací údaje globálního správce po zobrazení výzvy. Skript se výstup výsledku operace aktualizace jednotlivých uživatelů.

`<script location> -path <user file location>`

### <a name="disable-preview-experience"></a>Zakázat prostředí ve verzi preview

Pokud chcete zakázat prostředí ve verzi preview pro vaše uživatele, proveďte následující kroky:

1. Přihlaste se k webu Azure portal jako globální správce nebo Správce uživatelů.
2. Přejděte do **Azure Active Directory**, **uživatelská nastavení**, **umožňuje spravovat nastavení přístupu funkce ve verzi preview panel**.
3. V části **uživatelé můžou používat funkce verze preview pro registraci a správu bezpečnostních údajů**, nastavte na volič **žádný** a klikněte na tlačítko **Uložit**.

Uživatelům se už výzva k registraci pomocí prostředí ve verzi preview.

## <a name="next-steps"></a>Další postup

* [Další informace o verzi public preview kombinovaná registrace pro samoobslužné resetování hesla a ověřování Azure Multi-Factor Authentication](concept-registration-mfa-sspr-combined.md)
