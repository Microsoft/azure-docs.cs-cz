---
title: Vytváření sestav v Azure Active Directory automatické zřizování uživatelských účtů pro aplikace SaaS | Dokumentace Microsoftu
description: Zjistěte, jak zkontrolovat stav automatické uživatelský účet zřizování úloh a řešení potíží s zřizování jednotlivým uživatelům.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: celested
ms.reviewer: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a6d1684c4bc0031978fb5e76548a3112b0f1ef2
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56206986"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Kurz: Vytváření sestav o zřizování automatické uživatelských účtů


Azure Active Directory zahrnuje [uživatelský účet služby zřizování](user-provisioning.md) , která pomáhá automatizovat zřizování zrušení zřizování uživatelských účtů v aplikacích SaaS a jinými systémy pro účely životního cyklu identity začátku do konce Správa. Azure AD podporuje zřizování pro všechny aplikace a systémy v části "Doporučené" konektory předem integrované uživatelů [Galerie aplikací Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured).

Tento článek popisuje, jak zkontrolovat stav zajišťování úlohy poté, co byly nastaveny a řešení potíží s zřizování jednotlivých uživatelů a skupin.

## <a name="overview"></a>Přehled

Zřizování konektory jsou nastavit a nakonfigurovat pomocí [webu Azure portal](https://portal.azure.com), pomocí následujících [poskytuje dokumentaci](../saas-apps/tutorial-list.md) pro podporované aplikace. Po nakonfigurování a spuštění, zřizování úlohy můžete reportovány pomocí jedné ze dvou způsobů:

* **Portál pro správu Azure** – Tento článek popisuje primárně načítání informací o sestavy z [webu Azure portal](https://portal.azure.com), poskytující zřizování souhrnnou sestavu i zřizování podrobné protokoly auditu pro dané aplikace.

* **Audit rozhraní API** – Azure Active Directory také poskytuje rozhraní API s auditu, která umožňuje programový načítání podrobných protokolů auditu zřizování. Zobrazit [auditování Azure Active Directory reference k rozhraní API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) pro dokumentaci k použití tohoto rozhraní API. Přestože tento článek nepopisuje konkrétně jak používat rozhraní API, podrobnosti typy zřizování události, které se zaznamenávají do protokolu auditu.

### <a name="definitions"></a>Definice

Tento článek používá následující podmínky, definovaná níže:

* **Zdrojový systém** -úložišti uživatelů, pro které služba zřizování Azure AD se synchronizuje z. Azure Active Directory je zdrojový systém pro většinu předem integrovaných konektorů, zřizování, ale existují některé výjimky (Příklad: WORKDAY synchronizace příchozích dat).

* **Cíl systému** -úložišti uživatelů, které se synchronizuje služba zřizování Azure AD. Toto je obvykle aplikace SaaS (příklady: Salesforce, ServiceNow, Google Apps, Dropbox for Business), ale v některých případech může být místnímu systému, jako je Active Directory (Příklad: WORKDAY synchronizace příchozích dat do služby Active Directory).


## <a name="getting-provisioning-reports-from-the-azure-management-portal"></a>Načítání, vytváření sestav z portálu pro správu Azure

Získat zřízení informací sestavy pro danou aplikaci, začněte tím, že spouštění [portál pro správu Azure](https://portal.azure.com) a procházení pro podnikové aplikace, pro který je nakonfigurovaný zřizování. Například pokud jsou zřizování uživatelů k rozvoji Linkedinem, je navigační cestu do podrobností o aplikaci:

**Azure Active Directory > podnikové aplikace > všechny aplikace > rozvoji Linkedinem**

Z tohoto místa souhrnnou sestavu zřizování a zřizování protokolů auditu můžete přistupovat, jak je popsáno níže.


## <a name="provisioning-summary-report"></a>Zřizování souhrnnou sestavu

Zřizování Souhrnná sestava se nezobrazuje **zřizování** kartu pro danou aplikaci. Nachází se ve **podrobnosti synchronizace** části pod **nastavení**a obsahuje následující informace:

* Celkový počet uživatelů a / skupiny, které byly synchronizované a jsou nyní v oboru pro zřizování mezi systémem zdrojového a cílového systému.

* Čas poslední synchronizace byla spuštěna. Synchronizace každých 20 – 40 minut, obvykle dochází po [počáteční synchronizace](user-provisioning.md#what-happens-during-provisioning) byla dokončena.

* Určuje, jestli [počáteční synchronizace](user-provisioning.md#what-happens-during-provisioning) bylo dokončeno.

* Určuje, jestli během procesu zřizování se nachází v umístění do karantény a Důvod stavu karantény je (například selhání ke komunikaci s cílový systém z důvodu neplatné správce přihlašovacích údajů).

Zřizování Souhrnná sestava by měla být první pohled správci místě můžete zkontrolovat na provozní stav úlohy zřizování.

 ![Souhrnná sestava](./media/check-status-user-account-provisioning/summary_report.PNG)

## <a name="provisioning-audit-logs"></a>Zřizování protokolů auditu
Všechny aktivity prováděné zřizovací služby se zaznamenávají do protokolů auditu Azure AD, které lze zobrazit v **protokoly auditu** kartu **zřizování účtů** kategorie. Typy událostí protokolu aktivit patří:

* **Importovat události** – pokaždé, když služba zřizování Azure AD načte informace o jednotlivých uživatelů nebo skupin ze systému zdrojového nebo cílového systému se zaznamená událost "import". Při synchronizaci uživatelů se načítají ze zdrojového systému nejprve s výsledky zaznamenán jako "import" události. Odpovídající ID načtený uživatelů jsou následně dotázán proti cílovém systému a zkontrolujte, zda existují, se také jako "import" události zaznamenané výsledky. Tyto události zaznamenat všechny mapované uživatelské atributy a jejich hodnoty, které se zjistily službou Azure AD služby zřizování v době události. 

* **Synchronizační pravidlo události** – tyto události sestav o výsledcích pravidel mapování atributů a žádné nakonfigurované filtry oborů po importu a vyhodnocují na základě zdrojové a cílové systémy dat uživatele. Například pokud je uživatel v systému zdrojového zjistí, že v oboru pro zřizování a považuje neexistuje v cílovém systému, pak tato událost zaznamená, který uživatel se zřídí v cílovém systému. 

* **Exportovat události** – pokaždé, když služba zřizování Azure AD zapíše objekt uživatele účtu nebo skupiny na cílovém systému se zaznamená událost "export". Tyto události zaznamenat všechny atributy uživatele a jejich hodnoty, které byly vytvořeny ve službě Azure AD služby zřizování v době události. Pokud došlo k chybě při zápisu objektu účet nebo skupinu uživatele do cílového systému, zobrazí se tady.

* **Zpracování událostí v úschově** – proces escrows dojít, když dojde k chybě při pokusu o operaci služby zřizování a začne a zkuste operaci zopakovat na regresní interval času. Zřizování operace pokaždé, když se zaznamená událost "od".

Při prohlížení zřizování události pro jednotlivé uživatele, obvykle dojde k událostem v tomto pořadí:

1. Importovat události: Uživatel je načten ze zdrojového systému.

2. Importovat události: Cílový systém vyzván k provedení kontroly existence uživatele.

3. Synchronizační pravidlo událost: Uživatelská data ze zdrojové a cílové systémy vyhodnocují nakonfigurované mapování atributů pravidla a filtry oborů k určení, jaké akce, pokud existuje, je třeba provést.

4. Export událostí: Pokud synchronizační pravidlo událost závisí, by měl být akce provést (přidání, aktualizace, odstranění), pak výsledky akce jsou zaznamenány v události exportu.

![Vytváří se testovací uživatele služby Azure AD](./media/check-status-user-account-provisioning/audit_logs.PNG)


### <a name="looking-up-provisioning-events-for-a-specific-user"></a>Vyhledávání zřizování události pro konkrétního uživatele

Nejběžnější případ použití pro zřizování protokoly auditu se ke kontrole stavu zřizování jednotlivý uživatelský účet. K vyhledání poslední zřizování události pro konkrétního uživatele:

1. Přejděte **protokoly auditu** oddílu.

2. Z **kategorie** nabídce vyberte možnost **zřizování účtů**.

3. V **rozsah** nabídce vyberte rozsah dat, kterou chcete vyhledat.

4. V **hledání** panelu, zadejte uživatelské ID uživatele, kterou chcete vyhledat. Formát ID hodnoty musí odpovídat cokoli, co jste vybrali jako primární odpovídající ID v konfiguraci mapování atributů (například userPrincipalName zdravotní nebo zaměstnanecká identifikační číslo). Hodnota ID, vyžaduje se nebude zobrazovat ve sloupci cíle.

5. Stisknutím klávesy Enter spustíte vyhledávání. Nejnovější události zřizování bude vrácena jako první.

6. Události jsou vráceny, Všimněte si typy aktivit a zda úspěšné nebo neúspěšné. Pokud se nezobrazí žádné výsledky, pak ho znamená, že uživatel buď neexistuje, nebo nebyl dosud byl zjištěn během procesu zřizování Pokud zatím není dokončený úplná synchronizace.

7. Kliknutím na jednotlivé události, chcete-li zobrazit podrobnosti, včetně všech vlastnosti uživatele, které byly načteny, vyhodnotí nebo zapsat jako součást události.

Ukázku, jak používat protokoly auditu podívejte se na video, které jsou níže. Protokoly auditu se zobrazí kolem 5:30 označení:

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

### <a name="tips-for-viewing-the-provisioning-audit-logs"></a>Tipy pro zobrazení zřizování protokolů auditu

Nejlepší čitelnost na webu Azure Portal, vyberte **sloupce** tlačítko a vyberte tyto sloupce:

* **Datum** – zobrazuje datum, kdy došlo k události.
* **Cíle** – zobrazuje aplikaci název a uživatelské ID, které jsou témata události.
* **Aktivita** – typ aktivity, jak je popsáno výše.
* **Stav** – ať už událost byla úspěšná, či nikoli.
* **Důvod stavu** – souhrn co se dělo ve zřizování události.


## <a name="troubleshooting"></a>Řešení potíží

Zřizování souhrnné sestavy a auditu protokoly sami hrát klíčovou roli pomáhá řešit problémy se zřizování různých uživatelský účet správce.

Založené na scénářích informace o odstraňování potíží automatické zřizování uživatelů najdete v tématu [problémy konfigurace a zřizování uživatelů pro aplikaci](application-provisioning-config-problem.md).


## <a name="additional-resources"></a>Další prostředky

* [Správa zřizování uživatelských účtů pro podnikové aplikace](configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](what-is-single-sign-on.md)
