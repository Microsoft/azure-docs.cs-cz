---
title: Nemá přístup k této chybě podnikové aplikace při použití aplikace Proxy aplikací | Dokumentace Microsoftu
description: Jak řešit běžné potíže s přístupem k s aplikací Azure AD Application Proxy.
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
ms.date: 05/21/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc032a15a2938333cd25d05c271187f218f9be4d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56206799"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>Chyba "Nelze přistupovat k této podnikové aplikace" při použití aplikace Proxy aplikací

Tento článek pomůže při řešení běžných potíží chyba "Tato podnikové aplikace přístupné" v aplikaci Azure AD Application Proxy.

## <a name="overview"></a>Přehled
Když se zobrazí tato chyba, najdete stavový kód na chybové stránce. Tento kód je jedním z následujících stavů:

-   **Vypršel časový limit brány**: Proxy aplikace služby se nám kontaktovat konektoru. Tato chyba obvykle znamená potíže s přiřazením konektoru, konektor samostatně, nebo pravidel sítě kolem konektoru.

-   **Chybná brána**: Konektor se nepovedlo se kontaktovat back-end aplikace. Tato chyba může znamenat nesprávnou konfiguraci aplikace.

-   **Je zakázané**: Uživatel nemá oprávnění pro přístup k aplikaci. K této chybě může dojít, když uživatel není přiřazen k aplikaci v Azure Active Directory nebo pokud na back-endu uživatel nemá oprávnění pro přístup k aplikaci.

Chcete-li najít kód, podívejte se na text v levé dolní části v chybové zprávě pole "Stavový kód". Podívejte se také pro všechny další tipy v dolní části stránky.

   ![Chyba časového limitu brány](./media/application-proxy-sign-in-bad-gateway-timeout-error/connection-problem.png)

Podrobnosti o tom, jak vyřešit hlavní příčinu těchto chyb a další podrobnosti o návrhy jejich oprav naleznete v části odpovídající níže.

## <a name="gateway-timeout-errors"></a>Chyby časového limitu brány

Vypršel časový limit brány nastane, pokud se služba pokusí kontaktovat konektoru a nemůže v časovém limitu. Tato chyba je obvykle způsobeno aplikace přiřazená skupina konektorů s žádný funkční konektory nebo některé porty vyžadované službou konektoru nejsou otevřené.


## <a name="bad-gateway-errors"></a>Chyb Chybná brána

Chybná brána chyba označuje, že je konektor nemůže kontaktovat back-end aplikace. Ujistěte se, že jste publikovali správné aplikace. Běžné chyby, které příčinou této chyby jsou:

-   Máte překlep nebo chybu v interní adresa URL

-   Publikování není kořenovém adresáři aplikace. Například publikování <http://expenses/reimbursement> , ale při pokusu o přístup <http://expenses>

-   Problémy s konfigurací protokolu Kerberos omezené delegování (KCD)

-   Problémy s back-end aplikace

## <a name="forbidden-errors"></a>Zakázané chyby

Pokud se zobrazí chyby zakázáno, uživatel se nepřiřadila k aplikaci. Tato chyba mohla být buď v Azure Active Directory nebo na back-end aplikace.

Zjistěte, jak přiřadit uživatele k aplikaci v Azure, najdete v článku [dokumentaci konfigurační](application-proxy-add-on-premises-application.md#test-the-application).

Pokud se ujistíte, že uživatel je přiřazen k aplikaci v Azure, zkontrolujte konfiguraci uživatele v back-end aplikace. Pokud používáte ověřování Windows omezeného delegování/integrovaného protokolu Kerberos, najdete na stránce pokyny pro řešení potíží s KCD.

## <a name="check-the-applications-internal-url"></a>Zkontrolujte interní adresa URL aplikace

Jako první krok rychlý, pečlivě zkontrolujte a opravte interní adresa URL tak, že otevřete aplikaci prostřednictvím **podnikové aplikace**, vyberete **Proxy aplikací** nabídky. Ověřte, že interní adresa URL se bude používat z vaší místní sítě pro přístup k aplikaci.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Zkontrolujte, že aplikace je přiřazen k pracovní skupině konektorů

K ověření aplikace je přiřazený k pracovní skupině konektorů:

1.  Otevřete aplikaci tak, že přejdete na portálu **Azure Active Directory**, pak kliknete na **podnikové aplikace**, pak **všechny aplikace.** Otevřete aplikaci a pak vyberte **Proxy aplikací** v levé nabídce.

2.  Podívejte se na pole Skupina konektorů. Pokud ve skupině nejsou žádné aktivní konektory, zobrazí se upozornění. Pokud se nezobrazí žádné upozornění, přejděte na "ověřit všechny požadované porty jsou povolené".

3.  Pokud chybného se zobrazuje skupina konektorů, pomocí rozevírací nabídky vyberte správné skupině a potvrďte, že už se nezobrazují žádné upozornění. Pokud se zobrazuje odpovídající skupinu konektorů, klikněte na upozornění otevřete stránku se správou konektoru.

4.  Tady je několik způsobů, jak zobrazit další podrobnosti:

  * Konektor služby active lze přesuňte do skupiny: Pokud máte aktivní konektor, by měly patřit do této skupiny, který má dohled cílové aplikace back-end, můžete přesunout do přiřazené skupině konektoru. Uděláte to tak, klikněte na tento konektor. V poli "Skupina konektorů" pomocí rozevíracího seznamu vyberte správnou skupinu a pak klikněte na Uložit.

  * Stáhněte si nový konektor pro tuto skupinu: Z této stránky můžete získat odkaz na [si stáhli nový konektor](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download). Nainstalujte Connector na počítači s přístupem na dohled back-end aplikace. Typicall, konektoru nainstalovaná na stejném serveru jako aplikace. Použijte ke stažení konektoru odkaz ke stažení konektoru na cílovém počítači. V dalším kroku klikněte na tlačítko konektoru a ujistěte se, že patří do správné skupině. pomocí "Skupina konektorů" rozevíracího seznamu.

  * Prozkoumejte neaktivní konektoru: Pokud se konektor zobrazí jako neaktivní, se nepovedlo se kontaktovat službu. Tato chyba je obvykle kvůli některé požadované porty budou blokovány. Chcete-li tento problém vyřešit, přejděte na "ověřit všechny požadované porty jsou povolené."

Po použití těchto kroků zkontrolujte, že aplikace je přiřazenou ke skupině se práce konektory, otestujte aplikaci znovu spustit. Pokud stále nefunguje, pokračujte k další části.

## <a name="check-all-required-ports-are-whitelisted"></a>Zkontrolujte všechny požadované porty jsou povolené

Pokud chcete ověřit, že všechny požadované porty jsou otevřeny, naleznete v dokumentaci na porty. Pokud jsou otevřené požadované porty, přejděte k další části.

## <a name="check-for-other-connector-errors"></a>Zkontrolujte další chyby konektoru

Pokud žádný z výše uvedených problém nevyřešil, dalším krokem je najít problémy nebo chyby se konektor sám. Zobrazí se některé běžné chyby v [Poradce při potížích dokumentu](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors). 

Můžete se také podívat na protokoly konektoru a identifikovat chyby přímo. Mnohé z chybové zprávy sdílet konkrétní doporučení pro opravy. K zobrazení protokolů, najdete v článku [konektory dokumentaci](application-proxy-connectors.md#under-the-hood).

## <a name="additional-resolutions"></a>Další řešení

Pokud výše uvedené nevyřešilo problém, existuje několik různých možných příčin. Chcete-li identifikovat problém:

Pokud vaše aplikace je konfigurován pro použití integrovaného ověřování Windows (IWA), otestujte aplikaci bez jednotného přihlašování. Pokud ne, přejít k dalším odstavcem. Ke kontrole aplikaci bez jednotného přihlašování, otevřete aplikaci prostřednictvím **podnikové aplikace,** a Přejít **Single Sign-On** nabídky. Změňte na rozevírací seznam z "Integrované ověřování Windows" na "Azure AD jednotné přihlašování zakázáno". 

Nyní otevřete prohlížeč a zkuste znovu spustit aplikaci. By měl být zobrazení výzvy k ověření a získání do aplikace. Pokud jste mohli ověřit, je problém s konfigurací protokolu Kerberos omezené delegování (KCD), která umožňuje jednotné přihlašování. Další informace naleznete na stránce řešení potíží s KCD.

Pokud budete pokračovat chybu, přejděte k počítači, kde je konektor nainstalovaný, otevřete prohlížeč a se pokoušejí připojit interní adresa URL pro aplikaci. Tento konektor funguje jako jiného klienta ze stejného počítače. Pokud se nelze připojit aplikace, zjistěte, proč není schopen dostanou do aplikace, nebo použít konektor na serveru, který má aplikace přístup k tomuto počítači.

Pokud dosáhnete aplikace z tohoto počítače mají vyhledávat problémy nebo chyby se konektor sám. Zobrazí se některé běžné chyby v [Poradce při potížích dokumentu](application-proxy-troubleshoot.md#connector-errors). Můžete se také podívat na protokoly konektoru a identifikovat chyby přímo. Mnohé z našich chybové zprávy mohli sdílet více konkrétní doporučení pro opravy. Zjistěte, jak zobrazit protokoly, najdete v článku [naši dokumentaci konektory](application-proxy-connectors.md#under-the-hood).

## <a name="next-steps"></a>Další postup
[Principy konektorů Proxy aplikací Azure AD](application-proxy-connectors.md)
