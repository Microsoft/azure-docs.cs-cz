---
title: Problém s konfigurací zřizování uživatelů do aplikace Azure AD Gallery
description: Jak řešit běžné problémy, kterým čelí při konfiguraci zřizování uživatelů k aplikaci, která je již uvedena v Galerii aplikací Azure AD
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
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3955b96e4a7edfc79a229d927523bdd4473409d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522760"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Problém s konfigurací zřizování uživatelů do aplikace Azure AD Gallery

Konfigurace [automatického zřizování uživatelů](user-provisioning.md) pro aplikaci (pokud je podporována) vyžaduje, aby byly dodrženy konkrétní pokyny k přípravě aplikace pro automatické zřizování. Pak můžete použít portál Azure ke konfiguraci zřizovací služby pro synchronizaci uživatelských účtů s aplikací.

Vždy byste měli začít tím, že najdete kurz nastavení specifický pro nastavení zřizování pro vaši aplikaci. Potom postupujte podle těchto kroků a nakonfigurujte aplikaci i Azure AD k vytvoření zřizovacího připojení. Seznam výukových programů aplikací najdete v [seznamu výukových programů o integraci aplikací SaaS s Azure Active Directory](../saas-apps/tutorial-list.md).

## <a name="how-to-see-if-provisioning-is-working"></a>Jak zjistit, jestli zřizování funguje 

Jakmile je služba nakonfigurována, většina přehledů o provozu služby lze získat ze dvou míst:

-   **Zřizování protokoly (preview)** – [protokoly zřizování zaznamenat](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) všechny operace prováděné zřizovací služby, včetně dotazování Azure AD pro přiřazené uživatele, které jsou v oboru pro zřizování. Dotaz cílové aplikace pro existenci těchto uživatelů, porovnání objektů uživatele mezi systémem. Potom přidejte, aktualizujte nebo zakažte uživatelský účet v cílovém systému na základě porovnání. K protokolům zřizování na webu Azure Portal můžete přistupovat tak, že v části **Aktivita** **vyberete protokoly zřizování** aplikací **Azure Active Directory** &gt; **Enterprise Provisioning** &gt; (preview).

-   **Aktuální stav –** Souhrn posledního spuštění zřizování pro danou aplikaci najdete v části **Azure Active &gt; Directory Enterprise Apps &gt; \[Application Name Name\] &gt;Name Provisioning** v dolní části obrazovky v nastavení služby. Aktuální stav část ukazuje, zda zřizování cyklus začal zřizování uživatelských účtů. Můžete sledovat průběh cyklu, zjistit, kolik uživatelů a skupin bylo zřízeno, a zjistit, kolik rolí se vytvoří. Pokud dojde k chybám, podrobnosti naleznete v [Provisioning protokoly (.. /reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Obecné problémové oblasti s poskytováním, které je třeba zvážit

Níže je uveden seznam obecných problémových oblastí, do kterých můžete přejít, pokud máte představu o tom, kde začít.

* [Zdá se, že služba zřizování se nespustí](#provisioning-service-does-not-appear-to-start)
* Nelze uložit konfiguraci z důvodu nefunguje pověření aplikace
* [Protokoly zřizování říkají, že uživatelé jsou "přeskočeni" a nejsou zřízeni, i když jsou přiřazeni](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Zdá se, že služba zřizování se nespustí

Pokud nastavíte **stav zřizování,** aby **byl zapnutý** v části Azure Active **Directory &gt; &gt; \[Enterprise Apps Application Name\] &gt;Nameprovisioning** na webu Azure Portal. Po následném opětovném načtení se však na této stránce nezobrazí žádné další podrobnosti o stavu. Je pravděpodobné, že služba je spuštěna, ale ještě nedokončila počáteční cyklus. Zkontrolujte **protokoly zřizování** popsané výše k určení, jaké operace služba provádí a pokud existují nějaké chyby.

>[!NOTE]
>Počáteční cyklus může trvat od 20 minut do několika hodin, v závislosti na velikosti adresáře Azure AD a počet uživatelů v oboru pro zřizování. Následné synchronizace po počátečním cyklu být rychlejší, jako zřizovací služba ukládá vodoznaky, které představují stav obou systémů po počátečnícyklus, zlepšení výkonu následné synchronizace.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Nelze uložit konfiguraci z důvodu nefunguje pověření aplikace

Aby zřizování fungovalo, Azure AD vyžaduje platná pověření, která mu umožňují připojení k rozhraní API pro správu uživatelů poskytovaného totou aplikací. Pokud tato pověření nefungují nebo nevíte, co jsou, přečtěte si kurz pro nastavení této aplikace, popsaný výše.

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Protokoly zřizování říkají, že uživatelé jsou přeskočeni a nejsou zřízeni, i když jsou přiřazeni

Když se uživatel zobrazí jako "přeskočeno" v protokolech zřizování, je velmi důležité přečíst rozšířené podrobnosti ve zprávě protokolu k určení důvodu. Níže jsou uvedeny běžné důvody a usnesení:

- **Byl nakonfigurován filtr oborů,** **který filtruje uživatele na základě hodnoty atributu**. Další informace naleznete [v tématu Zřizování aplikací založených na atributech pomocí filtrů oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

- **Uživatel "není účinně oprávněn".** Pokud se zobrazí tato konkrétní chybová zpráva, je to proto, že došlo k potížím se záznamem přiřazení uživatele uloženým ve službě Azure AD. Chcete-li tento problém vyřešit, odřaďte uživatele (nebo skupinu) z aplikace a znovu jej přiřaďte. Další informace najdete [v tématu Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md).

- **Požadovaný atribut chybí nebo není naplněn pro uživatele.** Důležitou věcí, kterou je třeba zvážit při nastavování zřizování, je kontrola a konfigurace mapování atributů a pracovních postupů, které definují, které vlastnosti uživatele (nebo skupiny) toku z Azure AD do aplikace. To zahrnuje nastavení "odpovídající vlastnost", které se používají k jednoznačné identifikaci a sladění uživatelů nebo skupin mezi dvěma systémy. Další informace o tomto důležitém procesu naleznete v [tématu Přizpůsobení mapování atributů zřizování uživatelů](../app-provisioning/customize-application-attributes.md).

  * **Mapování atributů pro skupiny:** Zřizování názvu skupiny a podrobnosti skupiny, kromě členů, pokud je podporovánpro některé aplikace. Tuto funkci můžete povolit nebo zakázat povolením nebo zakázáním mapování **pro** objekty skupiny zobrazené na kartě **Zřizování.** Pokud zřizování skupiny je povolena, nezapomeňte zkontrolovat mapování atributů, aby zajistily příslušné pole se používá pro "odpovídající ID". Může se jedná o zobrazovaný název nebo e-mailový alias), protože skupina a její členové se nezřídí, pokud je odpovídající vlastnost prázdná nebo není naplněna pro skupinu ve službě Azure AD.

## <a name="next-steps"></a>Další kroky
[Automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](user-provisioning.md)
