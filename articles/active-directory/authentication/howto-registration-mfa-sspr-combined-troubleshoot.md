---
title: Řešení potíží s kombinovaná registrace pro samoobslužné resetování HESLA Azure AD a Vícefaktorové ověřování (preview) – Azure Active Directory
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
ms.openlocfilehash: 40918493071fe0dd694c43e2b087a2bf7eb197d8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60414613"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>Řešení potíží s kombinovat informace o registraci zabezpečení (preview)

Informace v tomto článku je určená pro správce, kteří jsou řešení potíží s problémy, které nahlásili uživatelé prostředí kombinovaná registrace.

|     |
| --- |
| Kombinované zabezpečení registrační informace pro ověřování Azure Multi-Factor Authentication a resetování hesla pomocí samoobslužné služby Azure Active Directory (Azure AD) je funkce ve verzi public preview služby Azure AD. Další informace o verzích Preview najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="audit-logs"></a>Protokoly auditu

Události evidované pro kombinované registrace jsou v kategorii metody ověřování ve službě Azure AD protokoly auditu.

![Auditování Azure AD protokoly událostí registrace zobrazující rozhraní](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

Následující tabulka uvádí všechny auditovat události generované modulem kombinovaná registrace:

| Aktivita | Status | Reason | Popis |
| --- | --- | --- | --- |
| Uživatel zaregistrován všechny požadované bezpečnostní údaje | Úspěch | Uživatel zaregistrován všechny požadované bezpečnostní údaje. | Pokud uživatel úspěšně dokončil registraci, dojde k této události.|
| Uživatel zaregistrován všechny požadované bezpečnostní údaje | Selhání | Uživatel zrušil registraci informací o zabezpečení. | Tato událost nastane, pokud uživatel zruší registraci v režimu přerušení.|
| Uživatel zaregistrován bezpečnostní údaje | Úspěch | Uživatel zaregistrovaný *metoda*. | Tato událost nastane, pokud se uživatel zaregistruje jednotlivé metody. *Metoda* může být ověřovací aplikace, Phone, e-mailu, zabezpečení dotazy, aplikace heslo, alternativní telefonní a tak dále.| 
| Uživatel zkontrolovat bezpečnostní údaje | Úspěch | Uživatel úspěšně zkontrolovat bezpečnostní údaje. | K této události dojde, když uživatel vybere **vypadá dobře** na stránce Zkontrolovat informace o zabezpečení.|
| Uživatel zkontrolovat bezpečnostní údaje | Selhání | Uživateli se nepodařilo zkontrolovat bezpečnostní údaje. | K této události dojde, když uživatel vybere **vypadá dobře** na bezpečnostní údaje, zkontrolujte stránku, ale něco selže na back-endu.|
| Uživatel odstranil bezpečnostní údaje | Úspěch | Uživatel odstranil *metoda*. | Této události dojde, když uživatel odstraní jednotlivé metody. *Metoda* může být ověřovací aplikace, Phone, e-mailu, zabezpečení dotazy, aplikace heslo, alternativní telefonní a tak dále.|
| Uživatel odstranil bezpečnostní údaje | Selhání | Uživatele se nepovedlo odstranit *metoda*. | Když se uživatel pokusí odstranit metodu, ale pokus selže z nějakého důvodu dojde k této události. *Metoda* může být ověřovací aplikace, Phone, e-mailu, zabezpečení dotazy, aplikace heslo, alternativní telefonní a tak dále.|
| Uživatel změnil výchozí bezpečnostní údaje | Úspěch | Uživatel změnil výchozí informace o zabezpečení pro *metoda*. | Když uživatel změní výchozí metoda dojde k této události. *Metoda* může být oznámení aplikace Authenticator, kód ze své ověřovací aplikace nebo token, volání + X XXXXXXXXXX, Text, kód + X XXXXXXXXX a tak dále.|
| Uživatel změnil výchozí bezpečnostní údaje | Selhání | Změna výchozí informace o zabezpečení pro uživatele se nezdařila. *metoda*. | Když uživatel se pokusí změnit výchozí metodu, ale pokus selže z nějakého důvodu dojde k této události. *Metoda* může být oznámení aplikace Authenticator, kód ze své ověřovací aplikace nebo token, volání + X XXXXXXXXXX, Text, kód + X XXXXXXXXX a tak dále.|

## <a name="troubleshooting-interrupt-mode"></a>Řešení potíží s režimu přerušení

| Příznak | Postup při řešení potíží |
| --- | --- |
| Jakou metodu můžu očekávali mi nezobrazují. | 1. Zkontrolujte, jestli má uživatel roli správce Azure AD. Pokud ano, zobrazte rozdíly zásad samoobslužného resetování HESLA správce. <br> 2. Určení, zda uživatel je přerušení kvůli registraci vynucení služby Multi-Factor Authentication nebo vynucení registrace samoobslužného resetování HESLA. Najdete v článku [vývojový diagram](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) v části "Kombinované registrace režimy" k určení, které metody mají být zobrazena. <br> 3. Určete, jak nedávno změnila zásady ověřování službou Multi-Factor Authentication nebo samoobslužné resetování HESLA. Pokud se poslední změny, může trvat nějakou dobu aktualizované zásady rozšíření.|

## <a name="troubleshooting-manage-mode"></a>Řešení potíží s spravovat režimu

| Příznak | Postup při řešení potíží |
| --- | --- |
| Není k dispozici možnost přidávat konkrétní metody. | 1. Určete, jestli je ověřování službou Multi-Factor Authentication nebo pro samoobslužné resetování HESLA povolené metody. <br> 2. Pokud je povolené metody, zásady uložit znovu a počkejte 1 – 2 hodiny před testováním znovu. <br> 3. Pokud je povolené metody, ujistěte se, že uživatel není již nastavit maximální počet této metody, která budete moct nastavit.|

## <a name="disable-combined-registration"></a>Zakázat kombinovaná registrace

Pokud uživatel zaregistruje telefonní číslo nebo mobilní aplikace na novém kombinaci prostředí, naše služby razítka sada příznaků (StrongAuthenticationMethods) pro tyto metody na tohoto uživatele. Tato funkce umožňuje uživateli provádět ověřování službou Multi-Factor Authentication s těmito metodami pokaždé, když je požadováno ověřování službou Multi-Factor Authentication.

Pokud správce umožňuje verzi preview, uživatelé registrovat prostřednictvím nové prostředí a správce zakáže náhled, uživatele může nechtěně zaregistrovat pro ověřování službou Multi-Factor Authentication také.

Pokud uživatel, který dokončí registraci kombinované přejde na stránku registrace resetování hesla aktuálního hesla pomocí samoobslužné služby v [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup), uživatel se vyzve k provedení ověřování Multi-Factor Authentication před přístupem k tuto stránku. Tento krok je očekáván z hlediska technické, ale je nová pro uživatele, kteří byla dříve registrována pro samoobslužné resetování HESLA, se pouze. I když tento krok navíc zlepšit stav zabezpečení uživatele tím, že poskytuje další úroveň zabezpečení, Správce může být vhodné vrátit zpět svým uživatelům tak, aby už budete moci provést ověřování službou Multi-Factor Authentication.  

### <a name="how-to-roll-back-users"></a>Tom, jak vrátit zpět uživatele

Pokud, jako správce, chcete resetovat nastavení služby Multi-Factor Authentication pro uživatele, můžete použít skript prostředí PowerShell uvedené v další části. Skript se vymazat vlastnost StrongAuthenticationMethods pro mobilní aplikace uživatele a/nebo telefonní číslo. Pokud spustíte tento skript pro vaše uživatele, budete potřebovat k zopakujte registraci u služby Multi-Factor Authentication, pokud je nutné. Doporučujeme testování vrácení zpět s uživateli jednu nebo dvě před vrácení zpět všechny ovlivněné uživatele.

Následující kroky umožňují vrátit zpět, uživatele nebo skupiny uživatelů.

#### <a name="prerequisites"></a>Požadavky

1. Nainstalujte odpovídající moduly Azure AD Powershellu. V okně Powershellu spusťte tyto příkazy pro instalaci modulů:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Uložte seznam ID objektů ovlivněného uživatele k počítači jako textový soubor s jedno ID na řádek. Poznamenejte si umístění souboru.
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

V okně Powershellu spusťte následující příkaz, a zadejte umístění souboru skriptu a uživatele. Zadejte přihlašovací údaje globálního správce po zobrazení výzvy. Skript se výstup výsledku operace aktualizace jednotlivých uživatelů.

`<script location> -path <user file location>`

### <a name="disable-the-preview-experience"></a>Zakázat prostředí ve verzi preview

Pokud chcete zakázat prostředí ve verzi preview pro vaše uživatele, proveďte tyto kroky:

1. Přihlaste se k webu Azure portal jako správce uživatelů.
2. Přejděte na **Azure Active Directory** > **uživatelská nastavení** > **umožňuje spravovat nastavení přístupu funkce ve verzi preview panel**.
3. V části **uživatelé můžou používat funkce verze preview pro registraci a správu bezpečnostních údajů**, nastavte na volič **žádný**a pak vyberte **Uložit**.

Uživatelům se už výzva k registraci prostředí ve verzi preview.

## <a name="next-steps"></a>Další postup

* [Další informace o verzi public preview kombinovaná registrace pro samoobslužné resetování hesla a ověřování Azure Multi-Factor Authentication](concept-registration-mfa-sspr-combined.md)
