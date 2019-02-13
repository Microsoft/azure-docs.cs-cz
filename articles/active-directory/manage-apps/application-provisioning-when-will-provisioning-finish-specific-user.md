---
title: Podívejte se, kdy konkrétní uživatel bude mít přístup k aplikaci | Dokumentace Microsoftu
description: Jak zjistit, kdy kriticky důležité uživatel moct získat přístup k aplikaci, kterou jste nakonfigurovali pro zřizování uživatelů pomocí Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d2bb3b7385467d2606a2a4fa0afb43b9440ab79
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163100"
---
# <a name="find-out-when-a-specific-user-will-be-able-to-access-an-application"></a>Zjistit, kdy konkrétní uživatel bude mít přístup k aplikaci
Používáte automatické zřizování uživatelů pomocí aplikace Azure AD automaticky zřizovat a aktualizace uživatelských účtů v aplikaci na základě věci, jako je [přiřazení uživatelů a skupin](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) při pravidelném plánovaném časový interval, obvykle každých 10 minut.

## <a name="how-long-does-it-take"></a>Jak dlouho trvá?

Čas potřebný pro daného uživatele, které se mají zřídit závisí hlavně na tom, zda počáteční "úplné" synchronizace již došlo k.

První synchronizace mezi službami Azure AD a aplikace může trvat 20 minut i několik hodin, v závislosti na velikosti adresáře služby Azure AD a počet uživatelů v oboru pro zřizování. 

Následné synchronizuje po počáteční synchronizaci být rychlejší (například do 10 minut), jako zřizovací služba ukládá vodoznaky, představující stav obou systémů po počáteční synchronizaci, zvýšení výkonu následné synchronizace.

## <a name="how-to-check-the-status-of-a-user"></a>Jak zkontrolovat stav uživatele

Zobrazíte stav zřizování pro vybraného uživatele najdete v protokolech auditování ve službě Azure AD.

Zřizování protokolů auditu je možný na webu Azure Portal, **Azure Active Directory &gt; podnikové aplikace &gt; \[název aplikace\] &gt; protokoly auditu** kartu. Filtrovat protokoly **zřizování účtů** kategorie zobrazíte jen zřizování události pro tuto aplikaci. Můžete hledat uživatele na základě "Odpovídající ID", která byla konfigurována pro ně mapování atributů. 

Například, pokud jste nakonfigurovali "hlavní název uživatele" nebo "e-mailovou adresu" jako odpovídající atribut na straně služby Azure AD a uživatel není zřizování má hodnotu "audrey@contoso.com", potom hledání v protokolech auditu "audrey@contoso.com" a poté zkontrolovat položky Vrátí.

Zřizování protokoly auditu zaznamenejte všechny operace prováděné zřizovací služba, včetně:

* Dotazování služby Azure AD pro přiřazené uživatele, kteří jsou v oboru pro zřizování
* Dotazování na cílové aplikace existenci těchto uživatelů
* Porovnávání objektů uživatelů mezi systémem
* Přidání, aktualizace nebo zakázání uživatelský účet v cílovém systému založené na porovnání

## <a name="next-steps"></a>Další postup
[Automatizace zřizování a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)"
