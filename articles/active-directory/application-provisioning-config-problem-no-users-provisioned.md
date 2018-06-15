---
title: Žádní uživatelé jsou se zřídí k aplikaci Galerie Azure AD | Microsoft Docs
description: Postup řešení běžných problémů s potýkají při nevidíte uživatelé zobrazovaných v na Azure AD Application Gallery jste nakonfigurovali pro zřizování uživatelů s Azure AD
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
ms.date: 05/04/2017
ms.author: asteen
ms.openlocfilehash: a36d60b8915ae0b46226bb2127829a8f1767daba
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2017
ms.locfileid: "26616289"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Žádní uživatelé jsou se zřídí k aplikaci Galerie Azure AD

Jakmile automatické zřizování byl nakonfigurován pro aplikace (včetně ověřování, zda jsou platná aplikaci přihlašovacích údajů zadaných do služby Azure AD se připojit k aplikaci). Potom uživatelů nebo skupin se zřizují do aplikace a je určen podle následujících akcí:

-   Které uživatelé a skupiny byly **přiřazené** do aplikace. Další informace o přiřazení najdete v tématu [přiřadit uživatele nebo skupinu enterprise aplikace v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

-   Zda **mapování atributů** jsou povolené a nakonfigurované pro synchronizaci platné atributy z Azure AD do aplikace. Další informace o mapování atributů najdete v tématu [přizpůsobení zřizování atribut mapování uživatelů pro aplikace SaaS ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings).

-   Zda je **oboru filtru** přítomen, který je filtrování uživatelů na základě konkrétní atribut hodnot. Další informace o filtry oborů najdete v tématu [zřizování aplikace na základě atributů s filtry oborů](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

Při sledování, zda uživatelé nejsou se zřídí, projděte si protokoly auditu ve službě Azure AD a vyhledejte položky protokolu pro konkrétního uživatele.

Zřizování protokolů auditu na portálu Azure v přístupné **Azure Active Directory &gt; podnikové aplikace &gt; \[název aplikace\] &gt; protokolech auditování** kartě. Filtrovat protokoly **zřizování účtu** kategorii zobrazíte jen zřizování události pro tuto aplikaci. Můžete vyhledat uživatele podle "Odpovídající ID" nakonfigurovaný pro ně v mapování atributů. Například, pokud jste nakonfigurovali "hlavní název uživatele" nebo "e-mailovou adresu" jako odpovídající atribut na straně Azure AD a uživatel není zřizování má hodnotu "audrey@contoso.com". Pak vyhledejte v protokolech auditu "audrey@contoso.com" a kontrola a vrácena žádná položka.

Protokoly zřizování auditu záznam všechny operace provádí zřizování služby, včetně dotazování Azure AD pro přiřazené uživatele, které jsou v oboru pro zřizování, dotazování cílové aplikace existenci uživatelům, porovnávání uživatelských objektů mezi systémem. Potom přidání, aktualizace nebo zakázat účet uživatele v cílovém systému podle porovnání.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Obecné problémových oblastí se zřizováním vzít v úvahu

Níže je seznam obecné problémových oblastí, které můžete rozbalit Pokud máte představu o kde začít.

* [Zřizování služby nezobrazí spuštění](#provisioning-service-does-not-appear-to-start)
* [Protokoly auditu, že uživatelé jsou přeskočeny a není zajišťováno, i když jsou přiřazeny](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Zřizování služby nezobrazí spuštění

Pokud nastavíte **Stav zřizování** být **na** v **Azure Active Directory &gt; podnikové aplikace &gt; \[název aplikace\] &gt;zřizování** části portálu Azure. Ale žádné další podrobnosti o stavu jsou zobrazena na této stránce po následné znovu načte, je pravděpodobné, že služba běží, avšak nedokončil ještě počáteční synchronizaci. Zkontrolujte **protokoly auditu** popsáno výše, a určit, jakým operacím služby provádí, a pokud nejsou žádné chyby.

>[!NOTE]
>Počáteční synchronizace může trvat od 20 minut několik hodin v závislosti na velikosti adresáře služby Azure AD a počet uživatelů v oboru pro zřizování. Následné synchronizace po počáteční synchronizace je rychlejší, jako službu zřizování ukládá vodoznaky, které představují stav obou systémů po počáteční synchronizaci. To zvyšuje výkon následné synchronizace.
>
>

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Protokoly auditu, že uživatelé jsou přeskočeny a není zajišťováno i když jsou přiřazeny

Když uživatel se zobrazí jako "přeskočen" v protokolech auditu, je velmi důležité Číst rozšířené podrobnosti v protokolu zprávy a určete důvod. Níže jsou uvedeny běžné příčiny a řešení:

-   **Byl nakonfigurován oboru filtru** **, je filtrování uživatele podle hodnota atributu**. Další informace o filtry oborů najdete v tématu <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

-   **Uživatel je "není oprávněn efektivně".** Pokud se zobrazí tato konkrétní chybová zpráva, je to, protože došlo k potížím s uživatelský záznam přiřazení uložené ve službě Azure AD. Opravte tento problém, zrušte přiřazení uživatele (nebo skupiny) z aplikace a znovu přiřadit. Další informace o přiřazení najdete v tématu <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

-   **Požadovaný atribut nebyl nalezen nebo není vyplněná pro uživatele.** Důležité vzít v úvahu při nastavování zřizování být zkontrolujte a nakonfigurujte mapování atributů a pracovních postupů, které definují, které uživatele (nebo skupiny) vlastnosti toku z Azure AD k aplikaci. To zahrnuje nastavení "odpovídající vlastnost", které použít k jednoznačné identifikaci a odpovídají uživatele nebo skupiny mezi těmito dvěma systémy. Další informace o tomto procesu důležité najdete v tématu [přizpůsobení zřizování atribut mapování uživatelů pro aplikace SaaS ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings).

  * **Mapování pro skupiny atributů:** zřizování název skupiny a údaje skupiny, kromě členy, pokud pro některé aplikace podporován. Můžete povolit nebo zakázat tuto funkci povolením nebo zakázáním **mapování** pro objekty skupiny ukazuje **zřizování** kartě. Pokud je povoleno zřizování skupiny, nezapomeňte si projít mapování atributů k zajištění, že na odpovídající pole je používána pro "Odpovídající ID". Může to být alias zobrazovaný název nebo e-mailu), protože skupiny a její členy nelze zřídit Pokud odpovídající vlastnost je prázdná nebo není vyplněná skupiny ve službě Azure AD.

## <a name="next-steps"></a>Další kroky
[Synchronizace Azure AD Connect: Principy deklarativní zřizování](active-directory-aadconnectsync-understanding-declarative-provisioning.md)

