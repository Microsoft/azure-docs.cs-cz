---
title: Řešení potíží v kombinované registraci – Azure Active Directory
description: Řešení potíží se službou Azure AD Multi-Factor Authentication a samoobslužnou registrací pro resetování hesel (Preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab7c38d23cb1f05e07488810640aeb791ded3d4a
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847384"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>Řešení potíží se kombinovanou registrací zabezpečení informací (Preview)

Informace v tomto článku se týkají správců, kteří řeší problémy nahlášené uživateli kombinovaného prostředí registrace.

|     |
| --- |
| Souhrnná registrace informací o zabezpečení pro Azure Multi-Factor Authentication a Azure Active Directory (Azure AD) Samoobslužné resetování hesla je funkce Public Preview služby Azure AD. Další informace o verzích Preview najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="audit-logs"></a>Protokoly auditu

Události protokolované pro kombinovanou registraci jsou v kategorii metody ověřování v protokolech auditu Azure AD.

![Rozhraní protokolů auditu Azure AD zobrazující události registrace](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

V následující tabulce jsou uvedeny všechny události auditu generované kombinovanou registrací:

| Aktivita | Stav | Důvod | Popis |
| --- | --- | --- | --- |
| Uživatel zaregistroval všechny požadované bezpečnostní údaje. | Úspěch | Uživatel zaregistroval všechny požadované bezpečnostní údaje. | Tato událost nastane, pokud uživatel úspěšně dokončil registraci.|
| Uživatel zaregistroval všechny požadované bezpečnostní údaje. | Selhání | Uživatel zrušil registraci bezpečnostních údajů. | K této události dojde, když uživatel zruší registraci z režimu přerušení.|
| Uživatel zaregistroval informace o zabezpečení | Úspěch | *Metoda*registrovaná uživatelem | Tato událost nastane, když uživatel zaregistruje jednotlivou metodu. *Metoda* může být ověřovací aplikace, telefon, E-mail, bezpečnostní otázky, heslo aplikace, alternativní telefon a tak dále.| 
| Uživatel zkontroloval bezpečnostní informace. | Úspěch | Uživatel úspěšně zkontroloval informace o zabezpečení. | K této události dojde, když uživatel vybere na stránce Kontrola bezpečnostních údajů **dobrý tvar** .|
| Uživatel zkontroloval bezpečnostní informace. | Selhání | Uživateli se nepodařilo zkontrolovat informace o zabezpečení. | K této události dojde, když uživatel vybere na stránce Kontrola bezpečnostních údajů **dobrý tvar** , ale v back-endu dojde k nějakému problému.|
| Uživatel odstranil informace o zabezpečení. | Úspěch | Uživatel odstranil *metodu*. | Tato událost nastane, když uživatel odstraní jednotlivou metodu. *Metoda* může být ověřovací aplikace, telefon, E-mail, bezpečnostní otázky, heslo aplikace, alternativní telefon a tak dále.|
| Uživatel odstranil informace o zabezpečení. | Selhání | Uživateli se nepovedlo odstranit *metodu*. | K této události dojde, když se uživatel pokusí odstranit metodu, ale pokus z nějakého důvodu se nezdařil. *Metoda* může být ověřovací aplikace, telefon, E-mail, bezpečnostní otázky, heslo aplikace, alternativní telefon a tak dále.|
| Uživatel změnil výchozí bezpečnostní údaje. | Úspěch | Uživatel změnil výchozí bezpečnostní údaje pro *metodu*. | K této události dojde, když uživatel změní výchozí metodu. *Metoda* může být oznamování ověřovací aplikace, kód z aplikace ověřovatele nebo tokenu, zavolat + x XXXXXXXXXX, text a kód na + X XXXXXXXXX a tak dále.|
| Uživatel změnil výchozí bezpečnostní údaje. | Selhání | Uživateli se nepodařilo změnit výchozí bezpečnostní údaje pro *metodu*. | K této události dojde, když se uživatel pokusí změnit výchozí metodu, ale pokus z nějakého důvodu se nezdaří. *Metoda* může být oznamování ověřovací aplikace, kód z aplikace ověřovatele nebo tokenu, zavolat + x XXXXXXXXXX, text a kód na + X XXXXXXXXX a tak dále.|

## <a name="troubleshooting-interrupt-mode"></a>Řešení potíží s režimem přerušení

| Příznak | Postup při řešení potíží |
| --- | --- |
| Nezobrazuje se očekávané metody, které je třeba zobrazit. | 1. Ověřte, jestli má uživatel roli správce Azure AD. Pokud ano, podívejte se na rozdíly v zásadách správce SSPR. <br> 2. Zjistěte, jestli je uživatel přerušený kvůli Multi-Factor Authentication vynucení registrace nebo vynucení registrace SSPR. Informace o tom, které metody se mají zobrazit, najdete v tématu [vývojový diagram](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) v části kombinované režimy registrace. <br> 3. Zjistěte, jak nedávno došlo ke změně zásad Multi-Factor Authentication nebo SSPR. Pokud byla změna poslední, může trvat nějakou dobu, než se aktualizované zásady rozšíří.|

## <a name="troubleshooting-manage-mode"></a>Řešení potíží s režimem správy

| Příznak | Postup při řešení potíží |
| --- | --- |
| Nemám možnost přidat konkrétní metodu. | 1. Zjistěte, jestli je metoda povolená pro Multi-Factor Authentication nebo pro SSPR. <br> 2. Pokud je metoda povolená, uložte zásady znovu a počkejte 1-2 hodin před testováním. <br> 3. Pokud je tato metoda povolená, ujistěte se, že uživatel už nevytvořil maximální počet metod, které mají povoleno nastavení.|

## <a name="disable-combined-registration"></a>Zakázat kombinovanou registraci

Když uživatel zaregistruje telefonní číslo nebo mobilní aplikaci v novém kombinovaném prostředí, doplní naše služba sadu příznaků (StrongAuthenticationMethods) pro tyto metody tohoto uživatele. Tato funkce umožňuje uživateli provádět Multi-Factor Authentication s těmito metodami, kdykoli je to nutné Multi-Factor Authentication.

Pokud správce povolí verzi Preview, uživatelé se budou registrovat prostřednictvím nového prostředí a potom správce zakáže verzi Preview, uživatelé se můžou nevědomě zaregistrovat pro Multi-Factor Authentication taky.

Pokud uživatel, který dokončil kombinovanou registraci, přejde na stránku pro registraci samoobslužného resetování hesla (SSPR) na adrese [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup), zobrazí se uživateli výzva k provedení Multi-Factor Authentication předtím, než bude mít přístup k této stránce. Tento krok se očekává z technického hlediska, ale pro uživatele, kteří se dřív zaregistrovali jenom pro SSPR, je nový. I když tento dodatečný krok vylepšuje stav zabezpečení uživatele tím, že poskytuje další úroveň zabezpečení, správci můžou chtít vrátit zpátky své uživatele, aby už nemohli provádět Multi-Factor Authentication.  

### <a name="how-to-roll-back-users"></a>Vrácení uživatelů zpět

Pokud jako správce chcete resetovat nastavení Multi-Factor Authentication uživatele, můžete použít skript prostředí PowerShell, který je k dispozici v následující části. Skript vymaže vlastnost StrongAuthenticationMethods pro mobilní aplikaci nebo telefonní číslo uživatele. Pokud tento skript spustíte pro uživatele, bude nutné ho znovu zaregistrovat pro Multi-Factor Authentication, pokud ho potřebují. Před vrácením všech ovlivněných uživatelů doporučujeme otestovat vrácení zpět s jedním nebo dvěma uživateli.

Následující kroky vám pomůžou vrátit uživatele nebo skupinu uživatelů.

#### <a name="prerequisites"></a>Předpoklady

1. Nainstalujte příslušné moduly Azure AD PowerShellu. V okně PowerShellu spusťte tyto příkazy a nainstalujte moduly:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Uložte seznam ovlivněných ID objektů uživatele do vašeho počítače jako textový soubor s jedním ID na řádek. Poznamenejte si umístění souboru.
1. Do počítače uložte následující skript a poznamenejte si umístění skriptu:

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

V okně PowerShellu spusťte následující příkaz, který zadává skript a umístění souborů uživatele. Po zobrazení výzvy zadejte přihlašovací údaje globálního správce. Skript vypíše výstup každé operace aktualizace uživatele.

`<script location> -path <user file location>`

### <a name="disable-the-preview-experience"></a>Zakázat prostředí verze Preview

Chcete-li zakázat prostředí Preview pro uživatele, proveďte tyto kroky:

1. Přihlaste se k Azure Portal jako správce uživatele.
2. Přejděte na **Azure Active Directory** > **nastavení uživatele** > **Spravovat nastavení pro přístupové panely funkce verze Preview**.
3. V části **Uživatelé můžou používat funkce verze Preview k registraci a správě bezpečnostních údajů**, nastavit selektor na **žádný**a pak vybrat **Uložit**.

Uživatelé už nebudou vyzváni k registraci pomocí prostředí Preview.

## <a name="next-steps"></a>Další kroky

* [Přečtěte si další informace o veřejné verzi Preview kombinované registrace pro Samoobslužné resetování hesla a službu Azure Multi-Factor Authentication](concept-registration-mfa-sspr-combined.md)
