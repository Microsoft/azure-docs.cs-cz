---
title: Řešení potíží s podmíněným přístupem pomocí nástroje What If – Azure Active Directory
description: Kde zjistíte, jaké zásady podmíněného přístupu byly aplikovány a proč
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0112ab53c501d639d3f8e0d09d82ef3a12cb93a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94837242"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>Řešení potíží s podmíněným přístupem pomocí nástroje What If

[Nástroj what if](what-if-tool.md) v podmíněném přístupu je výkonný, když se snažíte pochopit, proč zásada byla nebo nebyla pro uživatele v konkrétní situaci použita, nebo pokud by se zásada použila ve známém stavu.

Nástroj what if se nachází v what if **Azure Portal**  >  **Azure Active Directory**  >  **podmíněného přístupu**  >  .

![Nástroj pro What If podmíněného přístupu ve výchozím stavu](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> Nástroj What If aktuálně nevyhodnocuje zásady v režimu pouze sestavy.

## <a name="gathering-information"></a>Shromažďování informací

Nástroj What If vyžaduje pouze **uživatele** , aby bylo možné začít. 

Následující další informace jsou volitelné, ale pomůžou vám zúžit rozsah pro konkrétní případy.

* Cloudové aplikace nebo akce
* IP adresa 
* Země/oblast
* Platforma zařízení
* Klientské aplikace (Preview)
* Stav zařízení (Preview) 
* Riziko přihlášení

Tyto informace se můžou shromažďovat od uživatele, zařízení nebo protokolu přihlášení k Azure AD.

## <a name="generating-results"></a>Generování výsledků

Zadejte kritéria shromážděná v předchozí části a vyberte **What If** pro vygenerování seznamu výsledků. 

V libovolném okamžiku můžete vybrat možnost **obnovit** a zrušit tak zadání všech kritérií a návrat do výchozího stavu.

## <a name="evaluating-results"></a>Vyhodnocení výsledků

### <a name="policies-that-will-apply"></a>Zásady, které se použijí

V tomto seznamu se zobrazí všechny zásady podmíněného přístupu, které by se použily pro dané podmínky. Seznam bude zahrnovat ovládací prvky pro udělení a relaci, které platí. Příklady zahrnují vyžadování vícefaktorového ověřování pro přístup ke konkrétní aplikaci.

### <a name="policies-that-will-not-apply"></a>Zásady, které se nepoužijí

V tomto seznamu se zobrazí zásady podmíněného přístupu, které se nepoužijí, pokud se použijí podmínky. Seznam bude obsahovat všechny zásady a důvod, proč se nepoužijí. Mezi příklady patří uživatelé a skupiny, které se můžou vyloučit ze zásad.

## <a name="use-case"></a>Případ použití

Řada organizací vytváří zásady založené na umístěních v síti, povoluje důvěryhodná umístění a blokuje umístění, kde by neměl dojít k přístupu.

Aby bylo možné ověřit, zda byla konfigurace patřičně provedena, správce může použít nástroj What If k napodobení přístupu, z umístění, které by mělo být povoleno a z umístění, které by mělo být odepřeno.

[![What If nástroj zobrazující výsledky s přístupem blok](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png#lightbox)

V této instanci by měl uživatel zablokován přístup k libovolné cloudové aplikaci na cestě k Severní Korea, protože společnost Contoso zablokovala přístup z tohoto umístění.

Tento test se dá rozšířit tak, aby zahrnoval další datové body, abyste mohli zúžit rozsah.

## <a name="next-steps"></a>Další kroky

* [Co je podmíněný přístup?](overview.md)
* [Co je Azure Active Directory Identity Protection?](../identity-protection/overview-identity-protection.md)
* [Co je identita zařízení?](../devices/overview.md)
* [Jak to funguje: Multi-Factor Authentication Azure AD](../authentication/concept-mfa-howitworks.md)