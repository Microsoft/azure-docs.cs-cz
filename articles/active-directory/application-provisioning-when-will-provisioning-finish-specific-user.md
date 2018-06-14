---
title: Podívejte se, kdy budou mít přístup k aplikaci konkrétního uživatele | Microsoft Docs
description: Jak zjistit, kdy je zásadní význam uživatel bude moci získat přístup k aplikaci, kterou jste nakonfigurovali pro zřizování uživatelů s Azure AD
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 42c305ad48e6994b1d2c729b849acf665e575807
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2017
ms.locfileid: "26613618"
---
# <a name="find-out-when-a-specific-user-will-be-able-to-access-an-application"></a>Zjistit, kdy konkrétní uživatel bude mít přístup k aplikaci
Při použití zřizování automatické uživatelů s aplikací, Azure AD automaticky zřizovat a aktualizace uživatelské účty v aplikaci na základě třeba [přiřazení uživatelů a skupin](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) v pravidelném plánovaném časovém intervalu, obvykle každých 10 minut.

## <a name="how-long-does-it-take"></a>Jak dlouho to trvá?

Čas potřebný pro daného uživatele zřídit závisí hlavně na tom, jestli počáteční synchronizaci "úplná" již došlo k.

První synchronizace mezi službou Azure AD a aplikace může trvat od 20 minut několik hodin v závislosti na velikosti adresáře služby Azure AD a počet uživatelů v oboru pro zřizování. 

Následné synchronizace po počáteční synchronizace být rychlejší (např. do 10 minut), jako službu zřizování ukládá vodoznaky, které představují stav obou systémů po počáteční synchronizaci, zvýšení výkonu následné synchronizace.

## <a name="how-to-check-the-status-of-a-user"></a>Jak zkontrolovat stav uživatele

A zjistit stav zřizování pro vybraného uživatele, najdete v protokolech auditu ve službě Azure AD.

Zřizování protokolů auditu na portálu Azure v přístupné **Azure Active Directory &gt; podnikové aplikace &gt; \[název aplikace\] &gt; protokolech auditování** kartě. Filtrovat protokoly **zřizování účtu** kategorii zobrazíte jen zřizování události pro tuto aplikaci. Můžete vyhledat uživatele podle "Odpovídající ID" nakonfigurovaný pro ně v mapování atributů. 

Například, pokud jste nakonfigurovali "hlavní název uživatele" nebo "e-mailovou adresu" jako odpovídající atribut na straně Azure AD a uživatel není zřizování má hodnotu "audrey@contoso.com", pak vyhledejte v protokolech auditu "audrey@contoso.com" a kontrola a vrácena žádná položka.

Zřizování protokoly auditu zaznamenejte všechny operace, které provádí službu zřizování, včetně:

* Dotazování Azure AD pro přiřazené uživatele, které jsou v oboru pro zřizování
* Dotaz na cílové aplikace existenci těchto uživatelů
* Porovnávání uživatelských objektů mezi systémem
* Přidání, aktualizace nebo deaktivace uživatelský účet v cílovém systému podle porovnání

## <a name="next-steps"></a>Další kroky
[Automatizovat uživatele zajišťování a rušení zajištění pro aplikace SaaS ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning):
