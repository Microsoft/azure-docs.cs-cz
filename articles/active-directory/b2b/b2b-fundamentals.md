---
title: Doporučené postupy a doporučení služby Azure Active Directory B2B
description: Seznamte se s doporučenými postupy a doporučeními pro přístup uživatelů typu Host (Business-to-business) k firmě (B2B) ve službě Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54f5721ef606b6ea916f5a00031c58f5e2adeb0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050846"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Doporučené postupy služby Azure Active Directory B2B
Tento článek obsahuje doporučení a osvědčené postupy pro spolupráci mezi podniky (B2B) ve službě Azure Active Directory (Azure AD).

   > [!IMPORTANT]
   > **března 2021**, Microsoft již nebude podporovat uplatnění pozvánek vytvořením nespravované účty Azure AD a tenanty pro scénáře spolupráce B2B. V rámci přípravy doporučujeme zákazníkům, aby se přihlásili k [jednorázovému ověření hesla e-mailem](one-time-passcode.md). Vítáme vaši zpětnou vazbu k této funkci veřejného náhledu a jsme nadšeni, že můžeme vytvořit ještě více způsobů spolupráce.

## <a name="b2b-recommendations"></a>B2B doporučení
| Doporučení | Komentáře |
| --- | --- |
| Pro optimální přihlašovací prostředí federate s poskytovateli identity | Kdykoli je to možné, federate přímo s poskytovateli identity, aby pozvaní uživatelé mohli přihlásit do sdílených aplikací a prostředků, aniž by museli vytvářet účty Microsoft (MSA) nebo Azure AD účty. Pomocí funkce [Federace Google](google-federation.md) můžete uživatelům typu B2B umožnit přihlášení pomocí svých účtů Google. Nebo můžete použít [funkci Přímé federace (náhled)](direct-federation.md) k nastavení přímé federace s jakoukoli organizací, jejíž zprostředkovatel identity (IdP) podporuje protokol SAML 2.0 nebo WS-Fed. |
| Použití funkce E-mail s jednorázovým heskódem (preview) pro hosty B2B, kteří se nemohou ověřit jiným způsobem | Funkce [E-mail s jednorázovým přístupovým kódem (preview)](one-time-passcode.md) ověřuje uživatele typu Host b2B, když je nelze ověřit jinými prostředky, jako je Azure AD, účet Microsoft (MSA) nebo federace Google. Když uživatel hostu uplatní pozvánku nebo přistupuje ke sdílenému prostředku, může požádat o dočasný kód, který je odeslán na jeho e-mailovou adresu. Poté zadají tento kód, aby se mohli dál přihlašovat. |
| Přidání firemního brandingu na přihlašovací stránku | Přihlašovací stránku si můžete přizpůsobit tak, aby byla intuitivnější pro uživatele typu Host B2B. Podívejte se, jak [přidat firemní značku pro přihlášení a stránky přístupového panelu](../fundamentals/customize-branding.md). |
| Přidejte své prohlášení o zásadách ochrany osobních údajů do prostředí pro uplatnění uživatele typu Host B2B | Adresu URL prohlášení o zásadách ochrany osobních údajů vaší organizace můžete přidat k prvnímu procesu uplatnění pozvánky, aby pozvaný uživatel musel souhlasit s podmínkami ochrany osobních údajů, aby mohl pokračovat. Přečtěte [si postup: Přidání informací o ochraně osobních údajů vaší organizace ve službě Azure Active Directory](https://aka.ms/adprivacystatement). |
| Použití funkce hromadné pozvánky (náhledu) k pozvání více uživatelů typu Host B2B současně | Pozvěte do vaší organizace více uživatelů typu Host současně pomocí funkce hromadného náhledu pozvánek na webu Azure Portal. Tato funkce umožňuje nahrát soubor CSV k vytvoření uživatelů typu Host B2B a hromadnému odesílání pozvánek. Viz [Výuka hromadného pozvání uživatelů B2B](tutorial-bulk-invite.md). |
| Vynucení zásad podmíněného přístupu pro vícefaktorové ověřování (MFA) | Doporučujeme vynucovat zásady MFA u aplikací, které chcete sdílet s partnerskými uživateli B2B. Tímto způsobem bude vícefaktorové informace vynucovány v aplikacích ve vašem tenantovi bez ohledu na to, zda partnerská organizace používá vícefaktorové informace. Viz [Podmíněný přístup pro uživatele spolupráce B2B](conditional-access.md). |
| Pokud vynucujete zásady podmíněného přístupu založenéna zařízení, povolte přístup uživatelům B2B pomocí seznamů vyloučení. | Pokud jsou ve vaší organizaci povoleny zásady podmíněného přístupu založené na zařízení, budou zařízení typu Host B2B blokována, protože je vaše organizace nespravuje. Můžete vytvořit seznamy vyloučení obsahující konkrétní partnerské uživatele, které je vyloučí ze zásad podmíněného přístupu založeného na zařízení. Viz [Podmíněný přístup pro uživatele spolupráce B2B](conditional-access.md). |
| Použití adresy URL specifické pro klienta při poskytování přímých odkazů na uživatele typu Host B2B | Jako alternativu k e-mailu s pozvánkou můžete hostovi poskytnout přímý odkaz na vaši aplikaci nebo portál. Tento přímý odkaz musí být specifický pro klienta, což znamená, že musí obsahovat ID klienta nebo ověřenou doménu, aby host mohl být ověřen ve vašem tenantovi, kde se sdílená aplikace nachází. Viz [Možnost i pro usiova pro uživatele typu Host](redemption-experience.md). |
| Při vývoji aplikace použijte UserType k určení uživatelského prostředí typu hosta.  | Pokud vyvíjíte aplikaci a chcete poskytnout různá prostředí pro uživatele klienta a uživatele typu Host, použijte vlastnost UserType. Deklarace Typu UserType není aktuálně zahrnuta v tokenu. Aplikace by měly používat rozhraní Microsoft Graph API k dotazování adresáře pro uživatele získat jejich UserType. |
| Změnit vlastnost UserType *pouze* v případě, že se změní vztah uživatele k organizaci. | I když je možné použít PowerShell převést UserType vlastnost pro uživatele z Člena na Host (a naopak), měli byste změnit tuto vlastnost pouze v případě, že vztah uživatele k vaší organizaci změní. Viz [Vlastnosti uživatele typu Host B2B](user-properties.md).|

## <a name="next-steps"></a>Další kroky

[Správa sdílení B2B](delegate-invitations.md)
