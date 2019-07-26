---
title: Vykázat Automatické zřizování uživatelských účtů do aplikací SaaS | Microsoft Docs
description: Zjistěte, jak kontrolovat stav automatických úloh zřizování uživatelských účtů a jak řešit problémy zřizování jednotlivých uživatelů.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: fda7654ca2d825ae4112dd06021c7e83ed6867cd
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381259"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Kurz: Vytváření sestav o automatickém zřizování uživatelských účtů

Azure Active Directory (Azure AD) obsahuje [službu zřizování uživatelských účtů](user-provisioning.md) , která pomáhá automatizovat zřizování uživatelských účtů v aplikacích SaaS a dalších systémech za účelem komplexní správy životního cyklu identity. Azure AD podporuje předem integrované konektory zřizování uživatelů pro všechny aplikace a systémy v části "Doporučené" v [galerii aplikací Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured).

Tento článek popisuje, jak kontrolovat stav zřizovacích úloh po jejich nastavení a jak řešit problémy zřizování jednotlivých uživatelů a skupin.

## <a name="overview"></a>Přehled

Zřizovací konektory se nastavují a konfigurují pomocí [Azure Portal](https://portal.azure.com), a to podle [uvedené dokumentace](../saas-apps/tutorial-list.md) pro podporovanou aplikaci. Po nakonfigurování a spuštění lze úlohy zřizování ohlásit pomocí jedné ze dvou metod:

* **Azure Portal** – Tento článek popisuje, jak načíst informace o sestavě z [Azure Portal](https://portal.azure.com), která poskytuje souhrnnou sestavu zřizování i podrobné protokoly auditu pro danou aplikaci.
* **Audit API** – Azure Active Directory taky poskytuje rozhraní API pro audit, které umožňuje programové načtení podrobných protokolů auditu zřizování. Dokumentaci specifickou pro použití tohoto rozhraní API najdete v tématu [referenční informace k rozhraní api Azure Active Directory audit](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) . I když tento článek nezahrnuje konkrétně použití rozhraní API, podrobně popisuje typy událostí zřizování, které jsou zaznamenány v protokolu auditu.

### <a name="definitions"></a>Definice

Tento článek používá následující výrazy, které jsou definovány níže:

* **Zdrojový systém** – úložiště uživatelů, ze kterých se služba zřizování Azure AD synchronizuje. Azure Active Directory je zdrojovým systémem většiny předintegrovaných zřizovacích konektorů, ale vyskytly se nějaké výjimky (příklad: Synchronizace příchozích do Workday).
* **Cílový systém** – úložiště uživatelů, se kterými se služba zřizování Azure AD synchronizuje. Obvykle se jedná o aplikaci SaaS (příklady: Salesforce, ServiceNow, G Suite, Dropbox pro firmy), ale v některých případech se může jednat o místní systém, jako je například služba Active Directory (příklad: Příchozí synchronizace v Workday do služby Active Directory).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Získávání sestav zřizování z Azure Portal

Chcete-li získat informace o zřizovacích sestavách pro danou aplikaci, Začněte spuštěním [Azure Portal](https://portal.azure.com) a přejděte do podnikové aplikace, pro kterou je zřizování nakonfigurováno. Pokud například zřizujete uživatele na zvýšení oprávnění LinkedInu, navigační cesta k aplikaci je následující:

**Azure Active Directory > podnikové aplikace > všech aplikacích > zvýšení úrovně LinkedInu**

Odtud můžete přistupovat ke zprávě souhrn zřizování i k protokolům pro zřizování, jak je popsáno níže.

## <a name="provisioning-summary-report"></a>Souhrnná sestava zřizování

Sestava Souhrn zřizování se zobrazuje na kartě **zřizování** pro danou aplikaci. Najdete ho v části **Podrobnosti o synchronizaci** pod **nastavením**a poskytuje tyto informace:

* Celkový počet uživatelů a skupin, které byly synchronizovány a které jsou aktuálně v rozsahu pro zřizování mezi zdrojovým systémem a cílovým systémem.
* Čas posledního spuštění synchronizace. K synchronizaci obvykle dochází každých 20-40 minut po dokončení [počáteční synchronizace](user-provisioning.md#what-happens-during-provisioning) .
* Bez ohledu na to, zda byla dokončena [počáteční synchronizace](user-provisioning.md#what-happens-during-provisioning) .
* Bez ohledu na to, jestli je proces zřizování umístěný do karantény, a jaký je důvod stavu karantény (například selhání komunikace s cílovým systémem z důvodu neplatných přihlašovacích údajů správce).

Zpráva Shrnutí zřizování by měla být prvním místem, kde správci hledají provozní stav úlohy zřizování.

 ![Souhrnná sestava](./media/check-status-user-account-provisioning/summary_report.PNG)

## <a name="provisioning-audit-logs"></a>Zřizování protokolů auditu

Všechny aktivity prováděné službou zřizování se zaznamenávají v protokolech auditu Azure AD, které se dají zobrazit na kartě **protokoly auditu** v kategorii **zřizování účtů** . Typy událostí protokolovaných aktivit zahrnují:

* **Události importu** – událost import se zaznamenává pokaždé, když služba zřizování služby Azure AD načte informace o jednotlivých uživatelích nebo skupinách ze zdrojového nebo cílového systému. Během synchronizace se uživatelé načtou ze zdrojového systému jako první a výsledky se nahrály jako události import. Porovnávací ID načtených uživatelů se pak dotazují na cílový systém, aby zkontrolovala, jestli existují, a výsledky se zaznamenávají taky jako události import. Tyto události zaznamenávají všechny namapované atributy uživatelů a jejich hodnoty, které služba zřizování služby Azure AD zjistila v době události.
* **Události synchronizačního pravidla** – tyto události obsahují zprávy o výsledcích pravidel mapování atributů a všech nakonfigurovaných filtrů oborů po importu a vyhodnocení uživatelských dat ze zdrojového a cílového systému. Například pokud se uživatel ve zdrojovém systému považuje za v oboru pro zřizování a v cílovém systému se považuje za neexistující, zaznamená to, že uživatel bude v cílovém systému zřízen.
* **Události exportu** – událost exportu se zaznamenává pokaždé, když služba zřizování služby Azure AD zapisuje uživatelský účet nebo objekt skupiny do cílového systému. Tyto události zaznamenávají všechny atributy uživatelů a jejich hodnoty, které byly zapsány službou zřizování služby Azure AD v okamžiku události. Pokud došlo k chybě při zápisu uživatelského účtu nebo objektu skupiny do cílového systému, zobrazí se zde.
* **Zpracování událostí v úschově** – proces escrows nastane, když služba zřizování narazí na selhání při pokusu o operaci a začne opakovat operaci v případě back-Time. Událost v úschově se zaznamenává při každém opakování operace zřizování.

Při prohlížení událostí zřizování pro jednotlivé uživatele se události běžně vyskytují v tomto pořadí:

1. Událost importu: Uživatel je načten ze zdrojového systému.
1. Událost importu: Cílový systém je dotazován na kontrolu existence načteného uživatele.
1. Událost synchronizačního pravidla: Uživatelská data ze zdrojových a cílových systémů jsou vyhodnocována proti nakonfigurovaným pravidlům mapování atributů a filtrům rozsahu, které určují, jakou akci by měla být provedena.
1. Událost exportu: Pokud se u události synchronizačního pravidla určí, že by měla být provedena akce (přidat, aktualizovat, odstranit), výsledky akce se zaznamenávají do události exportu.

   ![Příklad: Stránka protokolu auditu zobrazující aktivity a stav](./media/check-status-user-account-provisioning/audit_logs.PNG)

### <a name="looking-up-provisioning-events-for-a-specific-user"></a>Hledání událostí zřizování pro konkrétního uživatele

Nejběžnějším případem při zřizování protokolů auditu je kontrola stavu zřizování jednotlivých uživatelských účtů. Vyhledání posledních událostí zřizování pro konkrétního uživatele:

1. Přejít do části **protokoly auditu** .
1. V nabídce **kategorie** vyberte **zřizování účtů**.
1. V nabídce **rozsah dat** vyberte rozsah dat, který chcete vyhledat.
1. Na panelu **hledání** zadejte ID uživatele, které chcete vyhledat. Formát hodnoty ID by měl odpovídat všem, které jste vybrali jako primární odpovídající ID v konfiguraci mapování atributů (například userPrincipalName nebo číslo ID zaměstnance). Požadovaná hodnota ID se zobrazí ve sloupci cíl (y).
1. Pro hledání stiskněte klávesu ENTER. Nejprve se vrátí poslední události zřizování.
1. Pokud jsou vráceny události, poznamenejte si typy aktivit a zda byly úspěšné nebo neúspěšné. Pokud se nevrátí žádné výsledky, znamená to, že uživatel buď neexistuje, nebo nebyl dosud zjištěn procesem zřizování, pokud nebyla úplná synchronizace dosud dokončena.
1. Kliknutím na jednotlivé události zobrazíte rozšířené podrobnosti, včetně všech vlastností uživatele, které byly načteny, vyhodnocovány nebo zapsány jako součást události.

Ukázku použití protokolů auditu najdete níže v následujícím videu. Protokoly auditu se zobrazí kolem značky 5:30:

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

### <a name="tips-for-viewing-the-provisioning-audit-logs"></a>Tipy pro zobrazení protokolů auditu zřizování

Pro lepší čitelnost v Azure Portal vyberte tlačítko **sloupce** a zvolte tyto sloupce:

* **Date** – zobrazí datum, kdy došlo k události.
* **Cíl (y)** – zobrazuje název aplikace a ID uživatele, které jsou předměty události.
* **Activity** – typ aktivity, jak je popsáno výše.
* **Stav** – bez ohledu na to, zda byla událost úspěšná nebo ne.
* **Důvod stavu** – souhrn toho, co se stalo v události zřizování.

## <a name="troubleshooting"></a>Řešení potíží

Sestava souhrnu zřizování a protokoly auditu hrají klíčovou roli pomáhající správcům při řešení potíží s různými problémy zřizování uživatelských účtů.

Pokyny k řešení potíží s automatickým zřizováním uživatelů najdete v tématu problémy s [konfigurací a zřizováním uživatelů do aplikace](application-provisioning-config-problem.md).

## <a name="additional-resources"></a>Další prostředky

* [Správa zřizování uživatelských účtů pro podnikové aplikace](configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](what-is-single-sign-on.md)
