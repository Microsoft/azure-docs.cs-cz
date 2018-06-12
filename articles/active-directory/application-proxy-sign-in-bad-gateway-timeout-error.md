---
title: Nelze získat přístup k této chybě podnikové aplikace při použití aplikace Proxy aplikace | Microsoft Docs
description: Jak řešit obvyklé problémy přístup s aplikací Azure AD Application Proxy.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: harshja
ms.openlocfilehash: 1c6c6f56f8e4d8d1d7a10bd07679732d64b86e23
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35292299"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>Chyba "Nelze podnikové k této aplikaci přístup" při použití aplikace Proxy aplikace

Tento článek vám pomůže vyřešit společné problémy se chyba "Tato podnikové aplikace nelze získat přístup" na Azure AD Application Proxy aplikace.

## <a name="overview"></a>Přehled
Když se tato chyba, nalezen stavový kód pro chybovou stránku. Tento kód je jedním z následujících stavů:

-   **Vypršel časový limit brány**: Proxy aplikace služby je možné vás zastihnout konektor. Tato chyba obvykle značí problému s přiřazením konektoru, konektor sám, nebo sítě pravidla kolem konektor.

-   **Chybná brána**: konektor se nelze spojit s back-end aplikace. Tato chyba může znamenat nesprávnou konfiguraci aplikace.

-   **Je zakázané**: uživatel nemá oprávnění pro přístup k aplikaci. K této chybě může dojít, když uživatel není přiřazen k aplikaci v Azure Active Directory nebo pokud na back-end uživatel nemá oprávnění pro přístup k aplikaci.

Pokud chcete najít kód, podívejte se na text v levém dolním chybové zprávy pro pole "Kód stavu". Také vyhledejte všechny další tipy v dolní části stránky.

   ![Chyba vypršení časového limitu brány](./media/application-proxy/connection-problem.png)

Podrobnosti o tom, jak vyřešit hlavní příčinu tyto chyby a další podrobnosti o navrhované opravy najdete v části odpovídající níže.

## <a name="gateway-timeout-errors"></a>Chyby vypršení časového limitu brány

Vypršel časový limit brány dojde, pokud služba se pokusí kontaktovat konektor a nemůže časovém limitu. Tato chyba je obvykle způsobena aplikace přiřazené ke skupině konektoru se žádný pracovní konektory, nebo některé porty vyžadované službou konektoru není otevřené.


## <a name="bad-gateway-errors"></a>Chybný chyby brány

Chybná brána chyby označuje, že konektor je možné vás zastihnout na back-end aplikace. Ujistěte se, že jste publikovali správnou aplikaci. Běžné chyby, které příčinou této chyby patří:

-   Máte překlep nebo chybu v interní adresa URL

-   Publikování není kořenové aplikace. Například publikování <http://expenses/reimbursement> , ale při pokusu o přístup <http://expenses>

-   Problémy s konfigurací protokolu Kerberos vynuceným delegování použitím (KCD)

-   Problémy s back-end aplikace

## <a name="forbidden-errors"></a>Zakázané chyby

Pokud se zobrazí chyba zakázané, uživateli nebyla přiřazena k aplikaci. Tato chyba může být buď v Azure Active Directory nebo na back-end aplikace.

Naučte se přiřazovat uživatele k aplikaci v Azure, najdete v článku [konfigurace dokumentaci](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal#add-a-test-user).

Pokud můžete potvrdit, že uživatel je přiřazen k aplikaci v Azure, zkontrolujte konfiguraci uživatele v aplikaci back-end. Pokud používáte ověřování systému Windows omezeného delegování nebo integrovaný protokolu Kerberos, naleznete na stránce řešení potíží s použitím KCD pokyny.

## <a name="check-the-applications-internal-url"></a>Zkontrolujte interní adresa URL aplikace.

Jako první krok rychlý, dvakrát zkontrolujte a opravte interní adresa URL otevřením aplikace prostřednictvím **podnikové aplikace, které**, pak výběrem **Proxy aplikace** nabídky. Ověřte, že interní adresa URL je použili z vaší místní sítě pro přístup k aplikaci.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Zkontrolujte, že aplikace je přiřazenou funkční konektor skupiny

K ověření aplikace je přiřazen k funkční konektor skupiny:

1.  Otevřete aplikaci na portálu přejděte na **Azure Active Directory**, kliknutím na na **podnikové aplikace, které**, pak **všechny aplikace.** Otevřete aplikaci a pak vyberte **Proxy aplikace** v levé nabídce.

2.  Podívejte se na pole konektor skupiny. Pokud nejsou žádné aktivní konektory ve skupině, zobrazí se upozornění. Pokud nevidíte žádné upozornění, přejděte na "ověřit všechny požadované porty jsou seznam povolených adres".

3.  Pokud chybnou konektor skupiny se zobrazuje, použijte rozevírací nabídky vyberte správné skupině, a potvrďte, že už se nezobrazují žádné upozornění. Pokud se zobrazuje zamýšlené skupiny konektor, klikněte na tlačítko upozornění otevřete stránku s konektoru správy.

4.  Tady je několik způsobů, jak přejít k podrobnostem Další:

  * Přesunout do skupiny pro konektor služby active: Pokud máte aktivní konektor, musí patřit do této skupiny a má směrem pohledu do cílové aplikace back-end, konektor můžete přesunout do skupiny přiřazené. Chcete-li to provést, klikněte na tlačítko konektor. V poli "Konektor skupina" pomocí rozevíracího seznamu vyberte správné skupině, a klikněte na Uložit.

  * Stáhněte si nový konektor pro tuto skupinu: na této stránce můžete získat odkaz na [stáhnout nový konektor](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download). Konektor nainstalujte na počítač s přímé směrem pohledu na back-end aplikace. Typicall, konektor je nainstalován na stejném serveru jako aplikace. Použijte stahování konektoru odkaz ke stažení konektoru na cílovém počítači. V dalším kroku klikněte na konektor a ujistěte se, že patří do správné skupiny pomocí "Konektor skupinu" rozevíracího seznamu.

  * Prozkoumat neaktivní konektor: Pokud se konektor zobrazovat jako neaktivní, je možné vás zastihnout na službu. Tato chyba je obvykle kvůli některé požadované porty blokován. Chcete-li tento problém vyřešit, přesunete na "ověřit všechny požadované porty jsou povolené."

Po použití těchto kroků zkontrolujte, že aplikace je přiřadit ke skupině se práce konektory, testovat aplikaci znovu. Pokud ještě není funkční, pokračujte v další části.

## <a name="check-all-required-ports-are-whitelisted"></a>Zkontrolujte všechny požadované porty jsou seznam povolených adres

Pokud chcete ověřit, že všechny požadované, jsou otevřené porty, najdete v dokumentaci na Otevřít porty. Pokud jsou otevřené požadované porty, přejděte na další části.

## <a name="check-for-other-connector-errors"></a>Zkontrolujte další chyby konektoru

Pokud žádná z výše uvedeného problém vyřešit, je dalším krokem hledání problémy a chyby s konektor sám. Zobrazí se některé běžné chyby v [Poradce při potížích dokumentu](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors). 

Můžete také zobrazit přímo v protokolech konektoru k identifikaci případné chyby. Mnoho z chybových zpráv sdílet konkrétní doporučení pro opravy. K zobrazení protokolů, najdete v článku [konektory dokumentaci](manage-apps/application-proxy-connectors.md#under-the-hood).

## <a name="additional-resolutions"></a>Další řešení

Pokud se výše nepomohly problém vyřešit, existuje několik různých důvodů. Chcete-li identifikovat problém:

Pokud vaše aplikace je nakonfigurovaný na použití integrovaného ověřování systému Windows (IWA), testování aplikace bez jednotného přihlašování. Pokud ne, přechod na další odstavec. Chcete-li zkontrolovat aplikaci bez jednotného přihlašování, otevřete aplikaci prostřednictvím **podnikové aplikace,** a přejděte na **jednotné přihlašování** nabídky. Změňte z rozevírací nabídky z "Integrované ověřování systému Windows" na "Azure AD jednotné přihlašování zakázáno". 

Nyní otevřete prohlížeč a snaží o přístup k aplikaci znovu. Měla zobrazit výzva k ověřování a dostat se do aplikace. Pokud jste mohli ověřit, je ale problém s konfigurací protokolu Kerberos vynuceným delegování použitím (KCD), která umožňuje jednotné přihlašování. Další informace naleznete na stránce řešení potíží s použitím KCD.

Pokud budete pokračovat zobrazí chyba, přejděte k počítači, kde je konektor nainstalovaný, spusťte prohlížeč a se pokoušejí připojit interní adresa URL pro aplikaci. Konektor funguje jako jiného klienta ze stejného počítače. Pokud aplikace není možné dosáhnout, zjistěte, proč tento počítač se nepodařilo dosáhnout aplikace, nebo použít konektor na serveru, který se bude moct získat přístup k aplikaci.

Pokud chcete vyhledat potíže či chyby s konektor sám aplikace z počítače, může dosáhnout. Zobrazí se některé běžné chyby v [Poradce při potížích dokumentu](manage-apps/application-proxy-troubleshoot.md#connector-errors). Můžete také zobrazit přímo v protokolech konektoru k identifikaci případné chyby. Mnoho z našich chybových zpráv moci sdílet více konkrétní doporučení pro opravy. Další postupy k zobrazení protokolů najdete v tématu [dokumentaci konektory](manage-apps/application-proxy-connectors.md#under-the-hood).

## <a name="next-steps"></a>Další postup
[Pochopení konektory proxy aplikace služby Azure AD](manage-apps/application-proxy-connectors.md)
