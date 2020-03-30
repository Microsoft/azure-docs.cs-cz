---
title: Nelze získat přístup k této chybě podnikové aplikace pomocí aplikace Proxy aplikace
description: Jak vyřešit běžné problémy s přístupem s aplikacemi proxy aplikací Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d61f4b4bce9b8287dc13237f071684ea5d135fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275469"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>Chyba "Nelze získat přístup k této podnikové aplikaci" při použití aplikace Proxy aplikace

Tento článek vám pomůže vyřešit běžné problémy s chybou "Tato podniková aplikace není přístupná" v aplikaci proxy aplikace Azure AD.

## <a name="overview"></a>Přehled

Když se zobrazí tato chyba, najděte na stránce chyby stavový kód. Tento kód je pravděpodobně jedním z následujících stavových kódů:

- **Časový rozsah brány**: Služba Proxy aplikace se nemůže dostat ke konektoru. Tato chyba obvykle označuje problém s přiřazení konektoru, konektor sám nebo pravidla sítě kolem konektoru.
- **Chybná brána**: Konektor se nemůže dostat k back-endové aplikaci. Tato chyba může znamenat chybnou konfiguraci aplikace.
- **Zakázáno**: Uživatel není oprávněn k přístupu k aplikaci. K této chybě může dojít buď v případě, že uživatel není přiřazen k aplikaci ve službě Azure Active Directory, nebo pokud v back-endu uživatel nemá oprávnění k přístupu k aplikaci.

Chcete-li najít kód, podívejte se na text v levém dolním rohu chybové zprávy pro pole "Stavový kód". Také se podívejte na další tipy v dolní části stránky.

![Příklad: Chyba časového opojení brány](./media/application-proxy-sign-in-bad-gateway-timeout-error/connection-problem.png)

Podrobnosti o tom, jak řešit hlavní příčinu těchto chyb a další podrobnosti o navrhovaných opravách, naleznete v příslušné části níže.

## <a name="gateway-timeout-errors"></a>Chyby časového času brány

Časový čas brány nastane, když se služba pokusí dosáhnout konektoru a není schopen v rámci časového časového času. Tato chyba je obvykle způsobena aplikací přiřazenou skupině konektorů bez pracovních konektorů nebo některé porty vyžadované konektorem nejsou otevřeny.

## <a name="bad-gateway-errors"></a>Chybné chyby brány

Chybná chyba brány označuje, že konektor není schopen dosáhnout back-endové aplikace. ujistěte se, že jste publikovali správnou aplikaci. Běžné chyby, které způsobují tuto chybu jsou:

- Překlep nebo chyba v interní adrese URL
- Nezveřejňuje kořenové adresáře aplikace. Například publikování, <http://expenses/reimbursement> ale pokus o přístup<http://expenses>
- Problémy s konfigurací delegování omezenou delegováním protokolu Kerberos (KCD)
- Problémy s back-endovou aplikací

## <a name="forbidden-errors"></a>Zakázané chyby

Pokud se zobrazí zakázaná chyba, uživatel nebyl přiřazen k aplikaci. Tato chyba může být buď ve službě Azure Active Directory nebo v back-endové aplikaci.

Informace o tom, jak přiřadit uživatele k aplikaci v Azure, najdete v [dokumentaci ke konfiguraci](application-proxy-add-on-premises-application.md#test-the-application).

Pokud potvrdíte, že je uživatel přiřazen k aplikaci v Azure, zkontrolujte konfiguraci uživatele v back-endové aplikaci. Pokud používáte delegování omezeného delegování protokolu Kerberos nebo integrované ověřování systému Windows, přečtěte si pokyny na stránce Poradce při potížích s disky KCD.

## <a name="check-the-applications-internal-url"></a>Zkontrolujte interní adresu URL aplikace.

Jako první rychlý krok zkontrolujte a opravte interní adresu URL otevřením aplikace prostřednictvím **podnikových aplikací**a výběrem nabídky **Proxy aplikace.** Ověřte, zda interní adresa URL je adresa URL používaná z místní sítě pro přístup k aplikaci.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Zkontrolujte, zda je aplikace přiřazena k pracovní skupině konektorů

Chcete-li ověřit, že aplikace je přiřazena k pracovní skupině konektorů:

1. Otevřete aplikaci na portálu tak, že přejdete do **služby Azure Active Directory**, kliknete na **Podnikové aplikace**a potom na **Všechny aplikace.** Otevřete aplikaci a v levé nabídce vyberte **Proxy aplikace.**
1. Podívejte se na pole Skupina konektorů. Pokud ve skupině nejsou žádné aktivní konektory, zobrazí se upozornění. Pokud nevidíte žádná upozornění, přejděte k ověření všech požadovaných portů.
1. Pokud se zobrazuje nesprávná skupina konektorů, vyberte správnou skupinu pomocí rozevíracího souboru a potvrďte, že se již nezobrazují žádná upozornění. Pokud se zobrazuje zamýšlená skupina konektorů, klepnutím na varovnou zprávu otevřete stránku se správou konektorů.
1. Odtud existuje několik způsobů, jak vrtat dále:

   - Přesunutí aktivního konektoru do skupiny: Pokud máte aktivní spojnici, která by měla patřit do této skupiny a má zorné pole do cílové back-endové aplikace, můžete spojnici přesunout do přiřazené skupiny. Chcete-li tak učinit, klepněte na konektor. V poli Skupina konektorů vyberte správnou skupinu pomocí rozevíracího seznamu a klepněte na tlačítko Uložit.
   - Stáhněte si nový konektor pro tuto skupinu: Na této stránce můžete získat odkaz na [stažení nového konektoru](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download). Nainstalujte konektor na počítač s přímým přímkem do back-endové aplikace. Konektor je obvykle nainstalován na stejném serveru jako aplikace. Pomocí odkazu ke stažení konektoru ke stažení konektoru do cílového počítače. Dále klikněte na konektor a pomocí rozevíracího souboru "Skupina konektorů" zkontrolujte, zda patří do správné skupiny.
   - Prozkoumejte neaktivní konektor: Pokud se konektor zobrazuje jako neaktivní, nemůže se k službě dostat. Tato chyba je obvykle z důvodu blokování některých požadovaných portů. Chcete-li tento problém vyřešit, přesuňte se k ověření všech požadovaných portů.

Po použití těchto kroků k zajištění aplikace je přiřazen a skupina s pracovní konektory, otestujte aplikaci znovu. Pokud stále nefunguje, pokračujte další částí.

## <a name="check-all-required-ports-are-open"></a>Zkontrolujte, zda jsou všechny požadované porty otevřené

Chcete-li ověřit, zda jsou všechny požadované porty otevřené, přečtěte si dokumentaci k otevření portů. Pokud jsou všechny požadované porty otevřené, přejděte k další části.

## <a name="check-for-other-connector-errors"></a>Kontrola dalších chyb konektoru

Pokud žádný z výše uvedených vyřešit problém, dalším krokem je hledat problémy nebo chyby s konektor sám. Některé běžné chyby můžete zobrazit v [dokumentu Poradce při potížích](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors).

Můžete také podívat přímo na protokoly konektoru k identifikaci všech chyb. Mnoho chybových zpráv sdílí konkrétní doporučení pro opravy. Chcete-li zobrazit protokoly, naleznete v [dokumentaci konektory](application-proxy-connectors.md#under-the-hood).

## <a name="additional-resolutions"></a>Další řešení

Pokud výše uvedené problém nevyřešilo, existuje několik různých možných příčin. Chcete-li identifikovat problém:

Pokud je aplikace nakonfigurována pro použití integrovaného ověřování systému Windows (IWA), otestujte aplikaci bez jednotného přihlášení. Pokud ne, přejděte k dalšímu odstavci. Chcete-li zkontrolovat aplikaci bez jednotného přihlášení, otevřete aplikaci prostřednictvím **podnikových aplikací** a přejděte do nabídky **Jednotné přihlášení.** Změňte rozevírací seznam z "Integrované ověřování systému Windows" na "Azure AD jednotné přihlašování zakázáno".

Nyní otevřete prohlížeč a zkuste znovu získat přístup k aplikaci. Měli byste být vyzváni k ověření a dostat se do aplikace. Pokud jste schopni ověřit, problém je s konfigurací kerberos omezené delegování (KCD), který umožňuje jednotné přihlašování. Další informace naleznete na stránce Poradce při potížích s kcd.

Pokud se chyba zobrazuje i nadále, přejděte do počítače, ve kterém je nainstalován konektor, otevřete prohlížeč a pokuste se dosáhnout interní adresy URL použité pro aplikaci. Konektor funguje jako jiný klient ze stejného počítače. Pokud se nemůžete dostat k aplikaci, zjistěte, proč se tento počítač nemůže dostat k aplikaci, nebo použijte konektor na serveru, který má přístup k aplikaci.

Pokud můžete dosáhnout aplikace z tohoto počítače, chcete-li hledat problémy nebo chyby s konektor sám. Některé běžné chyby můžete zobrazit v [dokumentu Poradce při potížích](application-proxy-troubleshoot.md#connector-errors). Můžete také podívat přímo na protokoly konektoru k identifikaci všech chyb. Mnoho našich chybových zpráv může sdílet konkrétnější doporučení pro opravy. Chcete-li se dozvědět, jak zobrazit protokoly, naleznete [v naší dokumentaci konektory](application-proxy-connectors.md#under-the-hood).

## <a name="next-steps"></a>Další kroky

[Principy konektorů proxy aplikací Azure AD](application-proxy-connectors.md)
