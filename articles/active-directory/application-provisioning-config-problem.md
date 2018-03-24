---
title: Problém konfiguraci zřizování uživatelů k aplikaci Galerie Azure AD | Microsoft Docs
description: Postup řešení běžných problémů s potýkají při konfiguraci zřizování uživatelů na aplikaci již uveden v galerii aplikací Azure AD
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
ms.openlocfilehash: 9fcfee9cbbeb01422e7d9e92d90b908e054f96da
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Problém konfiguraci zřizování uživatelů k aplikaci Galerie Azure AD

Konfigurace [zřizování automatické uživatelů](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) pro aplikaci (Pokud je podporuje), vyžaduje, aby konkrétní pokyny platí Příprava aplikací pro automatické zřizování. Potom můžete portál Azure ke konfiguraci zřizování služby synchronizace uživatelských účtů do aplikace.

Vždy byste měli začít hledání kurzu instalace specifické pro vytvoření zřizování pro vaši aplikaci. Potom postupujte podle těchto kroků konfigurace aplikace a Azure AD k vytvoření zřizování připojení. Seznam kurzů aplikace najdete na [seznamu kurzy o tom, jak integrovat SaaS aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

## <a name="how-to-see-if-provisioning-is-working"></a>Jak zjistit, zda pracuje zřizování 

Po nakonfigurování služby lze většinu přehledy operaci služby rozlišovat ze dvou míst:

-   **Protokoly auditu** – protokoly zřizování auditu záznam všech operací prováděných zřizování službou, včetně dotazování Azure AD pro přiřazené uživatele, kteří jsou v oboru pro zřizování. Dotaz na cílové aplikace existenci uživatelům porovnávání uživatelských objektů mezi systémem. Potom přidání, aktualizace nebo zakázat účet uživatele v cílovém systému podle porovnání. Zřizování protokolů auditu na portálu Azure v přístupné **Azure Active Directory &gt; podnikové aplikace &gt; \[název aplikace\] &gt; protokolech auditování** kartě. Filtrovat protokoly **zřizování účtu** kategorii zobrazíte jen zřizování události pro tuto aplikaci.

-   **Stav – zřízení** souhrn poslední zřizování spusťte pro danou aplikaci si můžete prohlédnout ve **Azure Active Directory &gt; podnikové aplikace &gt; \[název aplikace\] &gt;Zřizování** části, v dolní části obrazovky v části Nastavení služby. Tento oddíl shrnuje, kolik uživatelů (nebo skupin) jsou nyní synchronizovány mezi těmito dvěma systémy, a pokud nejsou žádné chyby. Podrobnosti o chybě se v protokolech auditu. Všimněte si, že stav zřizování nesmí být naplněny až do dokončení jeden úplné počáteční synchronizaci mezi službou Azure AD a aplikace.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Obecné problémových oblastí se zřizováním vzít v úvahu

Níže je seznam obecné problémových oblastí, které můžete rozbalit Pokud máte představu o kde začít.

* [Zřizování služby nezobrazí spuštění](#provisioning-service-does-not-appear-to-start)
* [Nelze uložit konfiguraci z důvodu přihlašovací údaje aplikace nepracuje](#can’t-save-configuration-due-to-app-credentials-not-working)
* [Protokoly auditu, že uživatelé jsou "přeskočen" a není zajišťováno, i když jsou přiřazeny](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Zřizování služby nezobrazí spuštění

Pokud nastavíte **Stav zřizování** být **na** v **Azure Active Directory &gt; podnikové aplikace &gt; \[název aplikace\] &gt;zřizování** části portálu Azure. Žádné další podrobnosti o stavu se ale zobrazují na této stránce po následné znovu načte. Je pravděpodobné, že služba běží, avšak nedokončil ještě počáteční synchronizaci. Zkontrolujte **protokoly auditu** popsáno výše, a určit, jakým operacím služby provádí, a pokud nejsou žádné chyby.

>[!NOTE]
>Počáteční synchronizace může trvat od 20 minut několik hodin v závislosti na velikosti adresáře služby Azure AD a počet uživatelů v oboru pro zřizování. Následné synchronizace po počáteční synchronizace být rychlejší jako službu zřizování ukládá vodoznaky, které představují stav obou systémů po počáteční synchronizaci, zvýšení výkonu následné synchronizace.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Nelze uložit konfiguraci z důvodu přihlašovací údaje aplikace nepracuje

Aby zřizování pracovat Azure AD vyžaduje platné přihlašovací údaje, které umožňují připojení k rozhraní API poskytované aplikaci pro správu uživatelů. Pokud tyto přihlašovací údaje nefungují, nebo neznáte wat je, přečtěte si kurz pro nastavení této aplikace a popsané.

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Protokoly auditu, že uživatelé jsou přeskočeny a není zajišťováno i když jsou přiřazeny

Když uživatel se zobrazí jako "přeskočen" v protokolech auditu, je velmi důležité Číst rozšířené podrobnosti v protokolu zprávy a určete důvod. Níže jsou uvedeny běžné příčiny a řešení:

-   **Byl nakonfigurován oboru filtru** **, je filtrování uživatele podle hodnota atributu**. Další informace o filtry oborů najdete v tématu <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

-   **Uživatel je "není oprávněn efektivně".** Pokud se zobrazí tato konkrétní chybová zpráva, je to, protože došlo k potížím s uživatelský záznam přiřazení uložené ve službě Azure AD. Opravte tento problém, zrušte přiřazení uživatele (nebo skupiny) z aplikace a znovu přiřadit. Další informace o přiřazení najdete v tématu <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

-   **Požadovaný atribut nebyl nalezen nebo není vyplněná pro uživatele.** Důležité vzít v úvahu při nastavování zřizování být zkontrolujte a nakonfigurujte mapování atributů a pracovních postupů, které definují, které uživatele (nebo skupiny) vlastnosti toku z Azure AD k aplikaci. To zahrnuje nastavení "odpovídající vlastnost", které použít k jednoznačné identifikaci a odpovídají uživatele nebo skupiny mezi těmito dvěma systémy. Další informace o tomto procesu důležité najdete v tématu <https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings>.

   * **Mapování pro skupiny atributů:** zřizování název skupiny a údaje skupiny, kromě členy, pokud pro některé aplikace podporován. Můžete povolit nebo zakázat tuto funkci povolením nebo zakázáním **mapování** pro objekty skupiny ukazuje **zřizování** kartě. Pokud je povoleno zřizování skupiny, nezapomeňte si projít mapování atributů k zajištění, že na odpovídající pole je používána pro "Odpovídající ID". Může to být alias zobrazovaný název nebo e-mailu), protože skupiny a její členy nelze zřídit Pokud odpovídající vlastnost je prázdná nebo není vyplněná skupiny ve službě Azure AD.

## <a name="next-steps"></a>Další postup
[Automatizovat uživatele zajišťování a rušení zajištění pro aplikace SaaS ve službě Azure Active Directory](active-directory-saas-app-provisioning.md)
