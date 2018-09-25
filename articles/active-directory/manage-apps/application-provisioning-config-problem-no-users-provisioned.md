---
title: Žádní uživatelé se nezřizují k aplikaci Galerie Azure AD | Dokumentace Microsoftu
description: Jak řešit běžné problémy, kterým čelí, když se uživatelům povolí, jste nakonfigurovali pro zřizování uživatelů pomocí Azure AD aplikace Galerie Azure AD
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
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 3874e6ff6586726577a2c89e3cf45bbd3343b821
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47040682"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Žádní uživatelé se nezřizují k aplikaci Galerie Azure AD
Po dokončení automatické zřizování konfigurace pro aplikaci (včetně ověření, že jsou platné přihlašovací údaje aplikace do služby Azure AD pro připojení k aplikaci k dispozici), potom uživatele a/nebo skupiny připravené k aplikaci. Zřizování se určuje podle následujících akcí:

-   Které uživatelé a skupiny byly **přiřazené** do aplikace. Další informace o přiřazení najdete v tématu [přiřadit podnikové aplikace v Azure Active Directory uživatele nebo skupinu](assign-user-or-group-access-portal.md).
-   Určuje, jestli **mapování atributů** jsou povolené a nakonfigurované pro synchronizaci platné atributy ze služby Azure AD do aplikace. Další informace o mapování atributů najdete v tématu [přizpůsobení zřizování atribut mapování uživatele pro aplikace SaaS ve službě Azure Active Directory](customize-application-attributes.md).
-   Zda je **filtr oborů** k dispozici, který je filtrování uživatelů podle konkrétních atributů. Další informace o filtry oborů, naleznete v tématu [zřizování aplikací na základě atributů s filtry oborů](define-conditional-rules-for-provisioning-user-accounts.md).
  
Pokud zjistíte, že uživatelé se zřizuje, najdete v protokolech auditování ve službě Azure AD. Hledat položky protokolu pro konkrétního uživatele.

Zřizování protokolů auditu je možný na webu Azure Portal, **Azure Active Directory &gt; podnikové aplikace &gt; \[název aplikace\] &gt; protokoly auditu** kartu. Filtrovat protokoly **zřizování účtů** kategorie zobrazíte jen zřizování události pro tuto aplikaci. Můžete hledat uživatele na základě "Odpovídající ID", která byla konfigurována pro ně mapování atributů. Například, pokud jste nakonfigurovali "hlavní název uživatele" nebo "e-mailovou adresu" jako odpovídající atribut na straně služby Azure AD a uživatel není zřizování má hodnotu "audrey@contoso.com", potom hledání v protokolech auditu "audrey@contoso.com" a zkontrolovat položky Vrátí.

Zřizování audit protokoluje záznamu všechny operace provedené službě zřizování, včetně dotazování služby Azure AD pro přiřazené uživatele, kteří jsou v oboru pro zřizování, dotazování cílové aplikace existenci těchto uživatelů, porovnání uživatelské objekty mezi systémem. Pak přidejte, aktualizovat nebo zakázat uživatelský účet v cílovém systému založené na porovnání.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Obecné problémových oblastí pomocí zřizování vzít v úvahu
Níže je seznam obecné problémových oblastí, které můžete zobrazit další podrobnosti Pokud máte představu, kde začít.

- [Služba zřizování se nezobrazí spuštění](#provisioning-service-does-not-appear-to-start)
- [Protokoly auditu Řekněme, že uživatelé jsou vynechány a není zřízený, i když jsou přiřazeny](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Služba zřizování se nezobrazí spuštění
Pokud jste nastavili **stavu zřizování** bude **na** v **Azure Active Directory &gt; podnikové aplikace &gt; \[název_aplikace\] &gt;Zřizování** části webu Azure portal. Ale žádné jiné podrobnosti o stavu jsou uvedeny na této stránce po následné znovu načte, je pravděpodobné, že služba běží, ale nebyla dokončena ještě počáteční synchronizaci. Zkontrolujte **protokoly auditu** popsáno výše, a určit, jaké operace provádí služba, a pokud nejsou žádné chyby.

>[!NOTE]
>Počáteční synchronizace může trvat 20 minut i několik hodin, v závislosti na velikosti adresáře služby Azure AD a počet uživatelů v oboru pro zřizování. Následné synchronizuje po počáteční synchronizaci jsou rychlejší, zřizovací služba ukládá vodoznaky, představující stav obou systémů po počáteční synchronizaci. Počáteční synchronizace zlepšuje výkon následné synchronizace.
>


## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Protokoly auditu Řekněme, že uživatelé jsou vynechány a není zřízený, i když jsou přiřazeny

Když uživatel zobrazí jako "vynecháno" z protokolů auditu, je potřeba Rozšířené podrobnosti najdete ve zprávě protokolu a zjistěte jeho důvod. Níže jsou uvedeny běžné důvody a jejich řešení:

- **Filtr oborů není nakonfigurovaná** **, který je odfiltrováním uživatele založené na hodnotě atributu**. Další informace o filtry oborů, naleznete v tématu [filtry oborů](define-conditional-rules-for-provisioning-user-accounts.md).
- **Uživatel je "není oprávněn efektivně".** Pokud se zobrazí určité chybové zprávě, bude to, že dojde k nějakému problému s záznamu přiřazení uživatele uložených ve službě Azure AD. Chcete-li vyřešit tento problém, zrušení přiřazení uživatele (nebo skupiny) z aplikace a znovu přiřadit. Další informace o přiřazení najdete v tématu [přiřadit přístup uživatele nebo skupinu](assign-user-or-group-access-portal.md).
- **Požadovaný atribut nebyl nalezen nebo není vyplněný pro uživatele.** Důležité vzít v úvahu při nastavování zřizování je ke kontrole a nakonfigurujte mapování atributů a pracovních postupů, které definují, které uživatel (nebo skupiny) vlastnosti toku ze služby Azure AD do aplikace. Tato konfigurace zahrnuje nastavení "odpovídající vlastnost", který slouží k jednoznačné identifikaci a odpovídající uživatelům nebo skupinám mezi těmito dvěma systémy. Další informace o tomto procesu důležité, naleznete v tématu [přizpůsobení zřizování atribut mapování uživatele pro aplikace SaaS ve službě Azure Active Directory](customize-application-attributes.md).
- **Mapování pro skupiny atributů:** zřizování název skupiny a podrobnosti o skupině, kromě členů, pokud se podporuje u některých aplikací. Můžete povolit nebo zakázat tuto funkci povolením nebo zakázáním **mapování** pro objekty skupiny je znázorněno **zřizování** kartu. Pokud je zapnutá zřizování skupin, nezapomeňte zkontrolovat mapování atributů k zajištění, že se že příslušné pole se používá pro "Odpovídající ID". Odpovídající ID může být zobrazovaný název nebo e-mailu alias. Skupiny a její členy nebyly zřízeny, pokud je odpovídající vlastnost prázdná nebo není vyplněný pro skupinu ve službě Azure AD.

## <a name="next-steps"></a>Další postup

[Synchronizace Azure AD Connect: Principy deklarativní zřizování](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
