---
title: Poradce při potížích s kombinovanou registrací – Služba Azure Active Directory
description: Poradce při potížích s vícefaktorovým ověřováním Azure AD a kombinovanou registrací samoobslužného hesla
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 04/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c840df2c53554519f62a3d1d7a7d8b305187ffb
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450934"
---
# <a name="troubleshooting-combined-security-information-registration"></a>Poradce při potížích s kombinovanou registrací informací o zabezpečení

Informace v tomto článku jsou určeny k průvodcování správci, kteří řeší problémy hlášené uživateli kombinované registrace.

## <a name="audit-logs"></a>Protokoly auditu

Události protokolované pro kombinovanou registraci jsou v kategorii Metody ověřování v protokolech auditu služby Azure AD.

![Rozhraní protokolů auditu Azure AD zobrazující události registrace](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

V následující tabulce jsou uvedeny všechny události auditu generované kombinovanou registrací:

| Aktivita | Status | Důvod | Popis |
| --- | --- | --- | --- |
| Uživatel zaregistroval všechny požadované bezpečnostní údaje | Úspěch | Uživatel zaregistroval všechny požadované bezpečnostní údaje. | K této události dochází, když uživatel úspěšně dokončil registraci.|
| Uživatel zaregistroval všechny požadované bezpečnostní údaje | Selhání | Uživatel zrušil registraci bezpečnostních údajů. | K této události dochází, když uživatel zruší registraci z režimu přerušení.|
| Uživatel registrované bezpečnostní údaje | Úspěch | Uživatel registrovaná *metoda*. | K této události dochází, když uživatel zaregistruje individuální metodu. *Metoda* může být aplikace Authenticator, telefon, e-mail, bezpečnostní otázky, heslo aplikace, alternativní telefon a tak dále.| 
| Uživatel zkontroloval bezpečnostní údaje | Úspěch | Uživatel úspěšně zkontroloval bezpečnostní údaje. | K této události dojde, když uživatel vybere na stránce kontroly bezpečnostních **údajů.**|
| Uživatel zkontroloval bezpečnostní údaje | Selhání | Uživateli se nepodařilo zkontrolovat bezpečnostní údaje. | K této události dojde, když uživatel vybere **vypadá dobře** na stránce kontroly bezpečnostních údajů, ale něco selže na back-endu.|
| Uživatel odstranil bezpečnostní údaje | Úspěch | Metoda odstraněna *uživatelem*. | K této události dochází, když uživatel odstraní jednotlivé metody. *Metoda* může být aplikace Authenticator, telefon, e-mail, bezpečnostní otázky, heslo aplikace, alternativní telefon a tak dále.|
| Uživatel odstranil bezpečnostní údaje | Selhání | Uživateli se nepodařilo odstranit *metodu*. | K této události dochází, když se uživatel pokusí odstranit metodu, ale pokus se z nějakého důvodu nezdaří. *Metoda* může být aplikace Authenticator, telefon, e-mail, bezpečnostní otázky, heslo aplikace, alternativní telefon a tak dále.|
| Uživatel změnil výchozí bezpečnostní údaje | Úspěch | Uživatel změnil výchozí bezpečnostní údaje pro *metodu*. | K této události dochází, když uživatel změní výchozí metodu. *Metoda* může být Oznámení aplikace Authenticator, Kód z mé ověřovací aplikace nebo tokenu, Call +X XXXXXXXXXX, Text kód +X XXXXXXXXX a tak dále.|
| Uživatel změnil výchozí bezpečnostní údaje | Selhání | Uživateli se nepodařilo změnit výchozí informace o zabezpečení *metody*. | K této události dochází, když se uživatel pokusí změnit výchozí metodu, ale pokus se z nějakého důvodu nezdaří. *Metoda* může být Oznámení aplikace Authenticator, Kód z mé ověřovací aplikace nebo tokenu, Call +X XXXXXXXXXX, Text kód +X XXXXXXXXX a tak dále.|

## <a name="troubleshooting-interrupt-mode"></a>Poradce při potížích s režimem přerušení

| Příznak | Postup při řešení potíží |
| --- | --- |
| Nevidím metody, které jsem očekával. | 1. Zkontrolujte, jestli má uživatel roli správce Azure AD. Pokud ano, zobrazte rozdíly zásad správce sspr. <br> 2. Zjistěte, zda je uživatel přerušen z důvodu vynucení registrace vícefaktorového ověřování nebo vynucení registrace snop. Viz [vývojový diagram](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) v části "Kombinované registrační režimy" určit, které metody by měly být zobrazeny. <br> 3. Určete, jak nedávno byla změněna zásada vícefaktorového ověřování nebo samodotykového ověřování. Pokud byla změna nedávná, může chvíli trvat, než se aktuagovaná zásada rozšíří.|

## <a name="troubleshooting-manage-mode"></a>Poradce při potížích se správou režimu

| Příznak | Postup při řešení potíží |
| --- | --- |
| Nemám možnost přidat konkrétní metodu. | 1. Zjistěte, zda je metoda povolena pro vícefaktorové ověřování nebo pro samodotykové ověřování. <br> 2. Pokud je metoda povolena, uložte zásady znovu a počkejte 1-2 hodiny před testováním znovu. <br> 3. Pokud je metoda povolena, ujistěte se, že uživatel ještě nenastavil maximální počet této metody, kterou může nastavit.|

## <a name="disable-combined-registration"></a>Zakázat kombinovanou registraci

Když uživatel zaregistruje telefonní číslo nebo mobilní aplikaci v novém kombinovaném prostředí, naše služba označí sadu příznaků (StrongAuthenticationMethods) pro tyto metody pro tohoto uživatele. Tato funkce umožňuje uživateli provádět vícefaktorové ověřování s těmito metodami vždy, když je vyžadováno vícefaktorové ověřování.

Pokud správce povolí náhled, uživatelé se zaregistrují prostřednictvím nového prostředí a pak správce zakáže náhled, uživatelé mohou nevědomky být registrováni také pro vícefaktorové ověřování.

Pokud uživatel, který dokončil kombinovanou registraci, přejde na aktuální stránku [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)registrace samoobslužného hesla (SSPR) na adrese , bude uživatel před přístupem k této stránce vyzván k provedení vícefaktorového ověřování. Tento krok se očekává z technického hlediska, ale je nový pro uživatele, kteří byli dříve registrováni pouze pro sspr. I když tento další krok vylepšuje stav zabezpečení uživatele tím, že poskytuje další úroveň zabezpečení, správci mohou chtít vrátit zpět své uživatele, aby již nemohli provádět vícefaktorové ověřování.  

### <a name="how-to-roll-back-users"></a>Jak vrátit zpět uživatele

Pokud jako správce chcete obnovit nastavení vícefaktorového ověřování uživatele, můžete použít skript Prostředí PowerShell uvedený v další části. Skript vymaže vlastnost StrongAuthenticationMethods pro mobilní aplikaci nebo telefonní číslo uživatele. Pokud spustíte tento skript pro uživatele, budou muset znovu zaregistrovat pro vícefaktorové ověřování, pokud ho potřebují. Doporučujeme testování vrácení zpět s jedním nebo dvěma uživateli před vrácením zpět všechny ovlivněné uživatele.

Následující kroky vám pomohou vrátit zpět uživatele nebo skupinu uživatelů.

#### <a name="prerequisites"></a>Požadavky

1. Nainstalujte příslušné moduly Prostředí Azure AD PowerShell. V okně Prostředí PowerShell spusťte tyto příkazy k instalaci modulů:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Uložte seznam ID ovlivněných objektů uživatele do počítače jako textový soubor s jedním ID na řádek. Poznamenejte si umístění souboru.
1. Uložte následující skript do počítače a poznamenejte si umístění skriptu:

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

V okně Prostředí PowerShell spusťte následující příkaz a zařazuj umístění skriptů a uživatelských souborů. Po zobrazení výzvy zadejte pověření globálního správce. Skript bude výstup výsledek každé operace aktualizace uživatele.

`<script location> -path <user file location>`

### <a name="disable-the-updated-experience"></a>Zakázání aktualizovaného prostředí

Chcete-li uživatelům zakázat aktualizované prostředí, proveďte následující kroky:

1. Přihlaste se k portálu Azure jako správce uživatele.
2. Přejděte na nastavení uživatele **služby Azure Active** > **Directory:** > **Správa nastavení funkcí náhledu přístupového panelu**.
3. V části **Uživatelé mohou používat funkce náhledu pro registraci a správu bezpečnostních údajů**, nastavte volič na **žádný**a pak vyberte **Uložit**.

Uživatelé již nebudou vyzváni k registraci pomocí aktualizovaného prostředí.

## <a name="next-steps"></a>Další kroky

* [Další informace o kombinované registraci samoobslužného resetování hesla a azure multifaktorové ověřování](concept-registration-mfa-sspr-combined.md)
