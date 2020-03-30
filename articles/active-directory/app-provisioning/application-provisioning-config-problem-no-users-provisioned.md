---
title: Do aplikace Azure AD Gallery se nezřají žádní uživatelé.
description: Jak řešit běžné problémy, kterým čelíte, když se nezobrazují uživatelé v aplikaci Azure AD Gallery, kterou jste nakonfigurovali pro zřizování uživatelů pomocí Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b60261d63e1bcb75aea9d2e8a6b74902520f391
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522913"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Do aplikace Azure AD Gallery se nezřají žádní uživatelé.
Po automatické zřizování byla nakonfigurována pro aplikaci (včetně ověření, že přihlašovací údaje aplikace poskytované Azure AD pro připojení k aplikaci jsou platné), pak uživatelé nebo skupiny jsou zřízeny do aplikace. Zřizování se určuje následujícími věcmi:

-   Kteří uživatelé a skupiny byly **přiřazeny** k aplikaci. Všimněte si, že zřizování vnořených skupin nebo skupin Office 365 není podporováno. Další informace o přiřazení najdete [v tématu Přiřazení uživatele nebo skupiny k podnikové aplikaci ve službě Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md).
-   Jestli jsou **mapování atributů** povolené a nakonfigurované tak, aby synchronizovaly platné atributy z Azure AD do aplikace. Další informace o mapování atributů najdete v [tématu Přizpůsobení mapování atributů zřizování uživatelů pro aplikace SaaS ve službě Azure Active Directory](customize-application-attributes.md).
-   Zda je či není k **dispozici filtr oborů,** který filtruje uživatele na základě hodnot určitých atributů. Další informace o filtrech oborů naleznete v [tématu Zřizování aplikací založených na atributech pomocí filtrů oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
  
Pokud zjistíte, že uživatelé nejsou zřizovány, podívejte se do [protokolů zřizování (preview)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) ve službě Azure AD. Vyhledejte položky protokolu pro konkrétního uživatele.

K protokolům zřizování na webu Azure Portal můžete přistupovat tak, že v části **Aktivita** **vyberete protokoly zřizování** aplikací **Azure Active Directory** &gt; **Enterprise Provisioning** &gt; (preview). Zřizování dat můžete prohledávat na základě jména uživatele nebo identifikátoru ve zdrojovém nebo cílovém systému. Podrobnosti naleznete v [tématu Zřizování protokolů (náhled)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Protokoly zřizování zaznamenávají všechny operace prováděné zřizovací službou, včetně dotazování služby Azure AD pro přiřazené uživatele, kteří jsou v oboru pro zřizování, dotazování na cílovou aplikaci pro existenci těchto uživatelů, porovnání uživatelských objektů mezi systému. Potom přidejte, aktualizujte nebo zakažte uživatelský účet v cílovém systému na základě porovnání.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Obecné problémové oblasti s zřizováním, které je třeba zvážit
Níže je uveden seznam obecných problémových oblastí, do kterých můžete přejít, pokud máte představu o tom, kde začít.

- [Zdá se, že služba zřizování se nespustí](#provisioning-service-does-not-appear-to-start)
- [Protokoly zřizování říkají, že uživatelé jsou přeskočeni a nejsou zřízeni, i když jsou přiřazeni](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Zdá se, že služba zřizování se nespustí
Pokud nastavíte **stav zřizování,** aby **byl zapnutý** v části Azure Active **Directory &gt; &gt; \[Enterprise Apps Application Name\] &gt;Nameprovisioning** na webu Azure Portal. Po následném opětovném načtení se však na této stránce nezobrazí žádné další podrobnosti o stavu, je pravděpodobné, že služba je spuštěna, ale ještě nedokončila počáteční cyklus. Zkontrolujte **protokoly zřizování (náhled)** popsané výše k určení, jaké operace služba provádí a pokud existují nějaké chyby.

>[!NOTE]
>Počáteční cyklus může trvat od 20 minut do několika hodin, v závislosti na velikosti adresáře Azure AD a počet uživatelů v oboru pro zřizování. Následné synchronizace po počátečnícyklus jsou rychlejší, jako zřizovací služba ukládá vodoznaky, které představují stav obou systémů po počátečnícyklus. Počáteční cyklus zlepšuje výkon následných synchronizací.
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Protokoly zřizování říkají, že uživatelé jsou přeskočeni a nejsou zřízeni, i když jsou přiřazeni

Když se uživatel zobrazí jako "přeskočeno" v protokolech zřizování, je důležité zkontrolovat **steps** kartu protokolu k určení důvodu. Níže jsou uvedeny běžné důvody a usnesení:

- **Byl nakonfigurován filtr oborů,** **který filtruje uživatele na základě hodnoty atributu**. Další informace o filtrech oborů naleznete v [tématu oborové filtry](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
- **Uživatel "není účinně oprávněn".** Pokud se zobrazí tato konkrétní chybová zpráva, je to proto, že došlo k potížím se záznamem přiřazení uživatele uloženým ve službě Azure AD. Chcete-li tento problém vyřešit, odřaďte uživatele (nebo skupinu) z aplikace a znovu jej přiřaďte. Další informace o přiřazení naleznete v tématu [Přiřazení přístupu k uživateli nebo skupině](../manage-apps/assign-user-or-group-access-portal.md).
- **Požadovaný atribut chybí nebo není naplněn pro uživatele.** Důležité při nastavování zřizování je třeba zkontrolovat a nakonfigurovat mapování atributů a pracovní postupy, které definují, které vlastnosti uživatele (nebo skupiny) toku z Azure AD do aplikace. Tato konfigurace zahrnuje nastavení "odpovídající vlastnost", která se používá k jednoznačné identifikaci a sladění uživatelů nebo skupin mezi dvěma systémy. Další informace o tomto důležitém procesu najdete [v tématu Přizpůsobení mapování atributů zřizování uživatelů pro aplikace SaaS ve službě Azure Active Directory](customize-application-attributes.md).
- **Mapování atributů pro skupiny:** Zřizování názvu skupiny a podrobnosti skupiny, kromě členů, pokud je podporovánpro některé aplikace. Tuto funkci můžete povolit nebo zakázat povolením nebo zakázáním mapování **pro** objekty skupiny zobrazené na kartě **Zřizování.** Pokud zřizování skupiny je povolena, nezapomeňte zkontrolovat mapování atributů, aby zajistily příslušné pole se používá pro "odpovídající ID". Odpovídající ID může být zobrazované jméno nebo e-mailový alias. Skupina a její členové nejsou zřízeny, pokud odpovídající vlastnost je prázdný nebo není naplněnpro skupinu ve službě Azure AD.

## <a name="next-steps"></a>Další kroky

[Synchronizace Azure AD Connect: Principy deklarativního zřizování](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
