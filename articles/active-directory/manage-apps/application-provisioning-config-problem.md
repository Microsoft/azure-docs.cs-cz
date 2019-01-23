---
title: Potíže s konfigurací zřizování uživatelů pro aplikaci Galerie Azure AD | Dokumentace Microsoftu
description: Jak řešit běžné problémy, kterým čelí při konfiguraci zřizování uživatelů pro aplikace již uvedená v galerii aplikací Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 340a7dfdf9698019eeb2c96dc56411afb7a78f50
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54471986"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Potíže s konfigurací zřizování uživatelů pro aplikaci Galerie Azure AD

Konfigurace [automatické zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) pro aplikaci (Pokud je podporuje), vyžaduje, aby konkrétní pokyny platí Příprava aplikací pro automatické zřizování. Potom můžete na webu Azure portal ke konfiguraci služby zřizování pro synchronizaci uživatelských účtů do aplikace.

Byste měli vždy začít hledáním kurz nastavení specifické pro nastavení zřizování pro vaši aplikaci. Pak postupujte podle těchto kroků ke konfiguraci aplikace a služby Azure AD k vytvoření zřizování připojení. Seznam kurzů aplikace najdete v [seznam kurzů o integraci aplikací typu SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

## <a name="how-to-see-if-provisioning-is-working"></a>Tom, jak zjistit, zda pracuje zřizování 

Po nakonfigurování služby lze většinu přehled o fungování služby rozlišovat ze dvou míst:

-   **Protokoly auditu** – protokoly zřizování auditu záznamu všechny operace prováděné zřizovací služba, včetně dotazování služby Azure AD pro přiřazené uživatele, kteří jsou v oboru pro zřizování. Dotaz na cílové aplikace existenci tito uživatelé porovnání uživatelské objekty mezi systémem. Pak přidejte, aktualizovat nebo zakázat uživatelský účet v cílovém systému založené na porovnání. Zřizování protokolů auditu je možný na webu Azure Portal, **Azure Active Directory &gt; podnikové aplikace &gt; \[název aplikace\] &gt; protokoly auditu** kartu. Filtrovat protokoly **zřizování účtů** kategorie zobrazíte jen zřizování události pro tuto aplikaci.

-   **Stav – zřízení** souhrn, poslední zřizování spuštění pro danou aplikaci si můžete prohlédnout ve **Azure Active Directory &gt; podnikové aplikace &gt; \[název_aplikace\] &gt;Zřizování** části, v dolní části obrazovky v části Nastavení služby. Tento oddíl shrnuje, kolik uživatelů (a/nebo skupiny) se momentálně synchronizuje mezi těmito dvěma systémy, a pokud nejsou žádné chyby. Podrobnosti o chybě se v protokolech auditování. Všimněte si, že stav zřizování nebudou vyplní počkal jeden úplný počáteční synchronizace mezi službami Azure AD a aplikace.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Obecné problémových oblastí pomocí zřizování vzít v úvahu

Níže je seznam obecné problémových oblastí, které můžete zobrazit další podrobnosti Pokud máte představu, kde začít.

* [Služba zřizování se nezobrazí spuštění](#provisioning-service-does-not-appear-to-start)
* [Nelze uložit konfiguraci z důvodu přihlašovacích údajů aplikace nefunguje](#can’t-save-configuration-due-to-app-credentials-not-working)
* [Protokoly auditu Řekněme, že uživatelé jsou "vynecháno" a není zřízený, i když jsou přiřazeny](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Služba zřizování se nezobrazí spuštění

Pokud jste nastavili **stavu zřizování** bude **na** v **Azure Active Directory &gt; podnikové aplikace &gt; \[název_aplikace\] &gt;Zřizování** části webu Azure portal. Ale na této stránce jsou uvedeny žádné další podrobnosti o stavu po následné znovu načte. Je pravděpodobné, že služba běží, ale nebyla dokončena ještě počáteční synchronizaci. Zkontrolujte **protokoly auditu** popsáno výše, a určit, jaké operace provádí služba, a pokud nejsou žádné chyby.

>[!NOTE]
>Počáteční synchronizace může trvat 20 minut i několik hodin, v závislosti na velikosti adresáře služby Azure AD a počet uživatelů v oboru pro zřizování. Následné synchronizuje po počáteční synchronizaci být rychlejší, zřizovací služba ukládá vodoznaky, představující stav obou systémů po počáteční synchronizaci, zvýšení výkonu následné synchronizace.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Nelze uložit konfiguraci z důvodu přihlašovacích údajů aplikace nefunguje

V pořadí pro zřizování pro práci Azure AD vyžaduje platné přihlašovací údaje, které umožňují připojení k rozhraní API poskytuje tuto aplikaci pro správu uživatelů. Pokud tyto přihlašovací údaje, nebudou fungovat, nebo si nejste jisti wat, ve které je, přečtěte si kurz pro nastavení této aplikace, je popsáno výše.

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Protokoly auditu Řekněme, že uživatelé jsou vynechány a není zřízený, i když jsou přiřazeny

Když uživatel zobrazí jako "vynecháno" z protokolů auditu, je velmi důležité, abyste Rozšířené podrobnosti najdete ve zprávě protokolu a zjistěte jeho důvod. Níže jsou uvedeny běžné důvody a jejich řešení:

-   **Filtr oborů není nakonfigurovaná** **, který je odfiltrováním uživatele založené na hodnotě atributu**. Další informace o filtry oborů, naleznete v tématu <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

-   **Uživatel je "není oprávněn efektivně".** Pokud se zobrazí určité chybové zprávě, bude to, že dojde k nějakému problému s záznamu přiřazení uživatele uložených ve službě Azure AD. Chcete-li vyřešit tento problém, rušit přiřazení uživatele (nebo skupiny) z aplikace a znovu přiřadit. Další informace o přiřazení najdete v tématu <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

-   **Požadovaný atribut nebyl nalezen nebo není vyplněný pro uživatele.** Důležité vzít v úvahu při nastavování zřizování se ke kontrole a nakonfigurujte mapování atributů a pracovních postupů, které definují, které uživatel (nebo skupiny) vlastnosti toku ze služby Azure AD do aplikace. Jedná se o vlastnost "odpovídající", který použije k jednoznačné identifikaci a odpovídající uživatelům nebo skupinám mezi těmito dvěma systémy. Další informace o tomto procesu důležité, naleznete v tématu <https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings>.

   * **Mapování atributů pro skupiny:** Zřizování název skupiny a podrobnosti o skupině, kromě členů, pokud se podporuje u některých aplikací. Můžete povolit nebo zakázat tuto funkci povolením nebo zakázáním **mapování** pro objekty skupiny je znázorněno **zřizování** kartu. Pokud je zapnutá zřizování skupin, nezapomeňte zkontrolovat mapování atributů k zajištění, že se že příslušné pole se používá pro "Odpovídající ID". To může být zobrazovaný název nebo e-mailu alias), jak skupině a jejích členů nelze zřídit Pokud odpovídající vlastnost je prázdná nebo není vyplněný pro skupinu ve službě Azure AD.

## <a name="next-steps"></a>Další postup
[Automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](user-provisioning.md)
