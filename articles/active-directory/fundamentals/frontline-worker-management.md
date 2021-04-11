---
title: Správa pracovních procesů prvotní – Azure Active Directory
description: Seznamte se s možnostmi správy pracovních procesů prvotní, které jsou k dispozici prostřednictvím portálu my Personáls.
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: cchiedo
author: Chrispine-Chiedo
manager: CelesteDG
ms.reviewer: stevebal
ms.openlocfilehash: 32cee4ca0558166c44ff83c5ce9d61360e79e535
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969144"
---
# <a name="frontline-worker-management"></a>Správa pracovních procesů prvotní

Prvotní pracovní procesy pro více než 80% globálních pracovníků. V důsledku vysoké škály, rychlého obratu a fragmentovaných procesů prvotní pracovníci často nedost nástrojů na to, aby jejich náročné úlohy byly trochu jednodušší. Správa pracovních procesů prvotní přináší digitální transformaci na celou prvotní pracovní síly. Zaměstnanci můžou zahrnovat manažery, prvotní pracovníky, operace a IT oddělení.

Prvotní Worker Management umožňuje prvotní pracovní síly tím, že tyto činnosti usnadňují provádění následujících činností:
- Zjednodušení běžných IT úkolů pomocí mých zaměstnanců
- Snadné připojování prvotních pracovníků prostřednictvím zjednodušeného ověřování
- Bezproblémové zřizování sdílených zařízení a zabezpečení přihlašování pracovníků prvotní

## <a name="delegated-user-management-through-my-staff"></a>Delegovaná Správa uživatelů prostřednictvím mých zaměstnanců

Azure Active Directory (Azure AD) poskytuje možnost delegovat správu uživatelů na správce prvotní prostřednictvím [portálu my personál](../roles/my-staff-configure.md), což pomáhá ušetřit cenné časy a snižovat rizika. Díky povolení zjednodušeného resetování hesel a správy telefonů přímo z úložiště nebo z podlahové výroby můžou manažeři udělit přístup zaměstnancům bez směrování požadavku prostřednictvím helpdesku, IT nebo provozu.

![Delegovaná Správa uživatelů na portálu my personál](media/concept-fundamentals-frontline-worker/delegated-user-management.png)

## <a name="accelerated-onboarding-with-simplified-authentication"></a>Urychlené připojování s zjednodušeným ověřováním

Moji zaměstnanci také umožňují správcům prvotní registrovat telefonní čísla členů týmu pro [přihlášení SMS](../authentication/howto-authentication-sms-signin.md). V mnoha svislých prvotní pracovní procesy udržuje kombinaci místních uživatelských jmen a hesel, což je řešení, které je často náročné, nákladné a náchylné k chybám. Když povolí ověřování pomocí služby SMS Sign-in, můžou se prvotní pracovníci přihlašovat pomocí [jednotného přihlašování (SSO)](../manage-apps/what-is-single-sign-on.md) pro Microsoft teams a dalších aplikací pomocí jenom jejich telefonního čísla a jednorázového hesla, které se odesílá prostřednictvím SMS. Tím se přihlašujete, aby se prvotní pracovníci mohli snadno a bezpečně doručovat k aplikacím, které potřebují.

![Přihlášení SMS](media/concept-fundamentals-frontline-worker/sms-signin.png)

Správci prvotní můžou pomocí spravované aplikace na domovské obrazovce (MHS) také dovolit přístup k určité sadě aplikací na svých vyhrazených zařízeních s Androidem, která jsou zaregistrovaná v Intune. Vyhrazená zařízení jsou zaregistrovaná v [režimu sdíleného zařízení Azure AD](../develop/msal-shared-devices.md). Pokud je v celoobrazovkovém režimu s více aplikacemi v konzole Microsoft Endpoint Manager (MEM) nakonfigurovaná služba MHS, automaticky se spustí jako výchozí domovská obrazovka zařízení a koncovým uživatelům se zobrazí jako *jediná* Domovská obrazovka. Další informace najdete v tématu [Konfigurace aplikace pro domovskou obrazovku spravované Microsoftem pro Android Enterprise](/mem/intune/apps/app-configuration-managed-home-screen-app).

## <a name="secure-sign-out-of-frontline-workers-from-shared-devices"></a>Zabezpečení přihlášení prvotních pracovních procesů ze sdílených zařízení

Mnoho společností používá sdílená zařízení, aby prvotní pracovníci mohli provádět transakce v inventáři a prostředcích, aniž by to mělo starosti se zřizováním a sledováním jednotlivých zařízení. Pomocí sdíleného zařízení je možné, aby prvotní pracovní proces bezpečně odhlásil ze všech aplikací na jakémkoli sdíleném zařízení před tím, než ho dokončí zpátky do centra, nebo ho předá společník při dalším posunu. Microsoft Teams je jedna z aplikací, která je aktuálně podporovaná na sdílených zařízeních, a umožňuje pracovníkům v prvotní zobrazovat úkoly, které jsou jim přiřazeny. Jakmile se pracovní podproces odhlásí ze sdíleného zařízení, Intune a Azure AD vymaže všechna firemní data, aby bylo možné zařízení bezpečně předat k dalšímu přidružení. Tuto schopnost můžete integrovat do všech obchodních aplikací pro [iOS](../develop/msal-ios-shared-devices.md) a [Android](../develop/msal-android-shared-devices.md) pomocí [knihovny Microsoft Authentication Library](../develop/msal-overview.md).

![Přihlášení ke sdílenému zařízení](media/concept-fundamentals-frontline-worker/shared-device-signout.png)

## <a name="next-steps"></a>Další kroky

- Další informace o správě delegovaných uživatelů najdete v [dokumentaci pro uživatele z mých zaměstnanců](../user-help/my-staff-team-manager.md).
- Postup při zřizování příchozích uživatelů z SAP SuccessFactors najdete v kurzu [konfigurace SAP SuccessFactors pro zřizování uživatelů služby Active Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md).
- Pro příchozí zřizování uživatelů z Workday si přečtěte kurz [Konfigurace pracovního dne pro Automatické zřizování uživatelů](../saas-apps/workday-inbound-tutorial.md).
