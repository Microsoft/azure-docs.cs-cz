---
title: Žádní uživatelé se nezřizují k aplikaci Galerie Azure AD | Dokumentace Microsoftu
description: Když se povolí, uživatelé, kterým čelí řešení běžných potíží služby Azure AD Galerie aplikací, které jste nakonfigurovali pro zřizování uživatelů pomocí Azure AD
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
ms.date: 05/04/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 813dc8686edc94bdaad8067a3395251a91051cb3
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44356394"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Žádní uživatelé se nezřizují k aplikaci Galerie Azure AD

Jakmile automatické zřizování není nakonfigurovaná pro aplikaci (včetně ověření, že jsou platné přihlašovací údaje aplikace do služby Azure AD pro připojení k aplikaci k dispozici). Pak uživatele a/nebo skupiny jsou zřízené do aplikace a se určuje podle následujících akcí:

-   Které uživatelé a skupiny byly **přiřazené** do aplikace. Další informace o přiřazení najdete v tématu [přiřadit podnikové aplikace v Azure Active Directory uživatele nebo skupinu](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

-   Určuje, jestli **mapování atributů** jsou povolené a nakonfigurované pro synchronizaci platné atributy ze služby Azure AD do aplikace. Další informace o mapování atributů najdete v tématu [přizpůsobení zřizování atribut mapování uživatele pro aplikace SaaS ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings).

-   Zda je **filtr oborů** k dispozici, který je filtrování uživatelů podle konkrétních atributů. Další informace o filtry oborů, naleznete v tématu [zřizování aplikací na základě atributů s filtry oborů](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

Při sledování se zřizuje uživatele, najdete v protokolech auditování ve službě Azure AD a vyhledejte položky protokolu pro konkrétního uživatele.

Zřizování protokolů auditu je možný na webu Azure Portal, **Azure Active Directory &gt; podnikové aplikace &gt; \[název aplikace\] &gt; protokoly auditu** kartu. Filtrovat protokoly **zřizování účtů** kategorie zobrazíte jen zřizování události pro tuto aplikaci. Můžete hledat uživatele na základě "Odpovídající ID", která byla konfigurována pro ně mapování atributů. Například, pokud jste nakonfigurovali "hlavní název uživatele" nebo "e-mailovou adresu" jako odpovídající atribut na straně služby Azure AD a uživatel není zřizování má hodnotu "audrey@contoso.com". Pak vyhledejte v protokolech auditu "audrey@contoso.com" a kontrola pak vrácených záznamů.

Zřizování audit protokoluje záznamu všechny operace provedené službě zřizování, včetně dotazování služby Azure AD pro přiřazené uživatele, kteří jsou v oboru pro zřizování, dotazování cílové aplikace existenci těchto uživatelů, porovnání uživatelské objekty mezi systémem. Pak přidejte, aktualizovat nebo zakázat uživatelský účet v cílovém systému založené na porovnání.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Obecné problémových oblastí pomocí zřizování vzít v úvahu

Níže je seznam obecné problémových oblastí, které můžete zobrazit další podrobnosti Pokud máte představu, kde začít.

* [Služba zřizování se nezobrazí spuštění](#provisioning-service-does-not-appear-to-start)
* [Protokoly auditu Řekněme, že uživatelé jsou vynechány a není zřízený, i když jsou přiřazeny](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Služba zřizování se nezobrazí spuštění

Pokud jste nastavili **stavu zřizování** bude **na** v **Azure Active Directory &gt; podnikové aplikace &gt; \[název_aplikace\] &gt;Zřizování** části webu Azure portal. Ale žádné jiné podrobnosti o stavu jsou uvedeny na této stránce po následné znovu načte, je pravděpodobné, že služba běží, ale nebyla dokončena ještě počáteční synchronizaci. Zkontrolujte **protokoly auditu** popsáno výše, a určit, jaké operace provádí služba, a pokud nejsou žádné chyby.

>[!NOTE]
>Počáteční synchronizace může trvat 20 minut i několik hodin, v závislosti na velikosti adresáře služby Azure AD a počet uživatelů v oboru pro zřizování. Následné synchronizuje po počáteční synchronizaci jsou rychlejší, zřizovací služba ukládá vodoznaky, představující stav obou systémů po počáteční synchronizaci. To zlepšuje výkon následné synchronizace.
>
>

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Protokoly auditu Řekněme, že uživatelé jsou vynechány a není zřízený, i když jsou přiřazeny

Když uživatel zobrazí jako "vynecháno" z protokolů auditu, je velmi důležité, abyste Rozšířené podrobnosti najdete ve zprávě protokolu a zjistěte jeho důvod. Níže jsou uvedeny běžné důvody a jejich řešení:

-   **Filtr oborů není nakonfigurovaná** **, který je odfiltrováním uživatele založené na hodnotě atributu**. Další informace o filtry oborů, naleznete v tématu <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

-   **Uživatel je "není oprávněn efektivně".** Pokud se zobrazí určité chybové zprávě, bude to, že dojde k nějakému problému s záznamu přiřazení uživatele uložených ve službě Azure AD. Chcete-li vyřešit tento problém, rušit přiřazení uživatele (nebo skupiny) z aplikace a znovu přiřadit. Další informace o přiřazení najdete v tématu <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

-   **Požadovaný atribut nebyl nalezen nebo není vyplněný pro uživatele.** Důležité vzít v úvahu při nastavování zřizování se ke kontrole a nakonfigurujte mapování atributů a pracovních postupů, které definují, které uživatel (nebo skupiny) vlastnosti toku ze služby Azure AD do aplikace. Jedná se o vlastnost "odpovídající", který použije k jednoznačné identifikaci a odpovídající uživatelům nebo skupinám mezi těmito dvěma systémy. Další informace o tomto procesu důležité, naleznete v tématu [přizpůsobení zřizování atribut mapování uživatele pro aplikace SaaS ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings).

  * **Mapování pro skupiny atributů:** zřizování název skupiny a podrobnosti o skupině, kromě členů, pokud se podporuje u některých aplikací. Můžete povolit nebo zakázat tuto funkci povolením nebo zakázáním **mapování** pro objekty skupiny je znázorněno **zřizování** kartu. Pokud je zapnutá zřizování skupin, nezapomeňte zkontrolovat mapování atributů k zajištění, že se že příslušné pole se používá pro "Odpovídající ID". To může být zobrazovaný název nebo e-mailu alias), jak skupině a jejích členů nelze zřídit Pokud odpovídající vlastnost je prázdná nebo není vyplněný pro skupinu ve službě Azure AD.

## <a name="next-steps"></a>Další postup
[Synchronizace Azure AD Connect: Principy deklarativní zřizování](../connect/active-directory-aadconnectsync-understanding-declarative-provisioning.md)

