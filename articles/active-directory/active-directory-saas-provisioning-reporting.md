---
title: Vytváření sestav v Azure Active Directory zřizování účtu automatické uživatelů pro aplikace SaaS | Microsoft Docs
description: Zjistěte, jak zkontrolovat stav zřizování úlohy automatické uživatelského účtu a řešení potíží s zřizování jednotlivé uživatele.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: asmalser-msft
ms.openlocfilehash: 5011dfbe496472e21a85dee9fa4901dad429a984
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37031725"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Kurz: Generování sestav na uživatele automatické zřizování účtu


Azure Active Directory zahrnuje [uživatelský účet zřizování služby](active-directory-saas-app-provisioning.md) která pomáhá automatizovat zřizování zrušte zřizování uživatelské účty v aplikace SaaS a dalšími systémy, pro účely identity začátku do konce cyklu Správa. Azure AD podporuje zřizování konektory pro všechny aplikace a systémy v části "Doporučený" předem integrovaných uživatelů [galerii aplikací Azure AD](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured).

Tento článek popisuje, jak zkontrolovat stav zajišťování úlohy po byly nastaveny a řešení potíží s zřizování jednotlivých uživatelů a skupin.

## <a name="overview"></a>Přehled

Zřizování konektory jsou nastavit a konfigurovat pomocí [portál Azure](https://portal.azure.com), pomocí následujících [poskytuje dokumentaci](saas-apps/tutorial-list.md) pro daná podporovaná aplikace. Jakmile nakonfigurovaný a spuštěný, mohou být zřizování úlohy oznámeny na pomocí jedné ze dvou způsobů:

* **Portál pro správu Azure** – Tento článek popisuje především načítání informací ze sestavy z [portál Azure](https://portal.azure.com), který poskytuje protokoly pro auditu zřizování souhrnnou sestavu jak podrobné zřizování dané aplikaci.

* **Audit rozhraní API** – Azure Active Directory také poskytuje rozhraní API auditu, který umožňuje programový načtení podrobné zřizování protokoly auditu. V tématu [auditování Azure Active Directory referenční dokumentace rozhraní API](active-directory-reporting-api-audit-reference.md) pro dokumentaci týkající se použití toto rozhraní API. Když tento článek nepopisuje používání rozhraní API konkrétně, podrobnosti typy zřizování události, které se zaznamenávají v protokolu auditu.

### <a name="definitions"></a>Definice

Tento článek používá následující podmínky, definovaná níže:

* **Zdroj systému** -úložiště uživatelů, které provádí synchronizaci ze služby Azure AD zřizováním služby. Azure Active Directory je zdrojovém systému pro většinu předem integrovaných zřizování konektory, ale existují některé výjimky (Příklad: Workday příchozí synchronizace).

* **Cíl systému** -úložiště uživatelů, kteří zřizování služby Azure AD synchronizuje k. To je obvykle SaaS aplikace (příklady: Salesforce, ServiceNow, Google Apps, Dropbox pro firmy), ale v některých případech může být v místním systému, jako je Active Directory (Příklad: Workday příchozí synchronizace služby Active Directory).


## <a name="getting-provisioning-reports-from-the-azure-management-portal"></a>Získávání zřizování sestavy z portálu správy Azure

Chcete-li získat zřizování informací sestavy pro danou aplikaci, spusťte spuštěním [portál pro správu Azure](https://portal.azure.com) a procházení pro podnikové aplikace, pro který je nakonfigurovaný zřizování. Například pokud zřizujete uživatelům LinkedIn zvýšení oprávnění, je cesta navigace k podrobností o aplikaci:

**Azure Active Directory > podnikové aplikace, které > všechny aplikace > LinkedIn zvýšení oprávnění**

Tady souhrnnou sestavu zřizování a zřizování protokoly auditu jsou k dispozici, i popsané dole.


## <a name="provisioning-summary-report"></a>Zřizování souhrnnou sestavu

Zřizování souhrnnou sestavu se zobrazí na **zřizování** kartě pro danou aplikaci. Nachází se ve **podrobnosti synchronizace** části pod **nastavení**a obsahuje následující informace:

* Celkový počet uživatelů a / skupiny, byly synchronizované a jsou momentálně v oboru pro zřizování mezi systémem zdrojovém a cílovém systému

* Čas poslední synchronizace byla spuštěna. Synchronizace většinou dochází každých 20 40 minut po [počáteční synchronizace](active-directory-saas-app-provisioning.md#what-happens-during-provisioning) byla dokončena.

* Jestli [počáteční synchronizace](active-directory-saas-app-provisioning.md#what-happens-during-provisioning) byla dokončena

* Proces zřizování, jestli má byly umístěny do karantény a Důvod stavu umístění do karantény je (například selhání ke komunikaci s cílovým systémem z důvodu neplatné správce přihlašovacích údajů)

Zřizování souhrnnou sestavu musí být první vzhledu místní správci zkontrolovat na provozní stav úlohy zřizování.

 ![Souhrnná sestava](./media/active-directory-saas-provisioning-reporting/summary_report.PNG)

## <a name="provisioning-audit-logs"></a>Protokoly auditu a zřizování
Všechny aktivity provedené zřizování služby se zaznamenávají do protokolů auditu Azure AD, které lze zobrazit v **protokoly auditu** v části **zřizování účtu** kategorie. Typy událostí protokolu aktivit patří:

* **Import události** – událost "import" se zaznamenává pokaždé, když zřizování služby Azure AD načte informace o jednotlivé uživatele nebo skupinu ze zdrojového systému nebo cílového systému. Při synchronizaci uživatelů se načítají ze zdrojového systému nejprve s výsledky zaznamenávají jako "import" události. Odpovídající ID načtené uživatelů jsou potom dotaz proti cílovém systému, aby zaškrtněte, pokud existují, s výsledky taky zaznamená jako "importovat" události. Tyto události zaznamenejte všechny atributy mapovat uživatele a jejich hodnoty, které se zobrazily službou Azure AD zřizování služby v době události. 

* **Události pravidlo synchronizace** – tyto události sestav o výsledcích pravidel mapování atributů a žádné nakonfigurované oboru filtrů, po importu a vyhodnocují na základě zdrojové a cílové systémy dat uživatele. Například pokud je uživatel ve zdrojovém systému zjistí, že se v oboru pro zřizování a zjistí, že neexistuje v cílovém systému, pak tato událost zaznamenává, který se zřídí uživatele v cílovém systému. 

* **Export události** -"export" událost se zaznamená pokaždé, když zřizování služby Azure AD zapíše objekt uživatelského účtu nebo skupiny do cílového systému. Tyto události zaznamenejte všechny atributy uživatelů a jejich hodnoty, které byly napsané pomocí Azure AD zřizování služby v době události. Pokud došlo k chybě při zápisu objektu účet nebo skupinu uživatele do cílového systému, zobrazí se zde.

* **Zpracování událostí úschově** -escrows proces dojít, když dojde k chybě při pokusu o operaci zřizování služby a začne opakujte operaci na back vypnout interval času. Operace zřizování vyřazenou pokaždé, když se zaznamená událost "úschově".

Při pohledu zřizování události pro jednotlivé uživatele, dochází k události obvykle v tomto pořadí:

1. Import událostí: uživatel se načítají ze zdrojového systému.

2. Import událostí: cílovém systému je dotazován Zkontrolujte existenci načtené uživatele.

3. Událost pravidlo synchronizace: uživatelská data ze zdrojové a cílové systémy jsou vyhodnotit na základě nakonfigurovaná pravidla mapování atributů a oboru filtrů určit, jaké akce, pokud existuje, je třeba provést.

4. Export událostí: Pokud událost pravidlo synchronizace závisí, že má být akce provést (přidání, aktualizaci, odstranění), pak výsledky akce se zaznamenávají v události Export.

![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-provisioning-reporting/audit_logs.PNG)


### <a name="looking-up-provisioning-events-for-a-specific-user"></a>Vyhledávání zřizování události pro konkrétního uživatele

Nejběžnější případ použití pro zřizování protokoly auditu je zkontrolovat stav zřizování individuální uživatelský účet. K vyhledání poslední události zřizování pro konkrétního uživatele:

1. Přejděte na **protokoly auditu** části.

2. Z **kategorie** nabídce vyberte možnost **zřizování účtu**.

3. V **rozsah** nabídce vyberte rozsah dat, kterou chcete vyhledat,

4. V **vyhledávání** panel, zadejte ID uživatele chcete hledat uživatele. Formát hodnoty ID by měl odpovídat ať jste vybrali jako primární odpovídající ID v konfiguraci mapování atributů (například userPrincipalName nebo ID číslo zaměstnance). Na požadovanou hodnotu ID se nebude zobrazovat ve sloupci cíle (cílů).

5. Stiskněte klávesu Enter pro vyhledávání. Zřizování nejaktuálnějších událostí, bude vrácena jako první.

6. Pokud jsou vráceny události, poznamenejte si typy aktivit a jestli byla úspěšná nebo neúspěšná. Pokud se žádné výsledky, pak znamená uživatel buď neexistuje, nebo nebylo dosud byl zjištěn procesu zřizování Pokud zatím není dokončený úplnou synchronizaci.

7. Kliknutím na jednotlivé události zobrazíte Rozšířené podrobnosti, včetně všech vlastnosti uživatele, které byly načteny, vyhodnotí nebo zapsat jako součást události.

Ukázka o tom, jak pomocí protokolů auditování najdete v níže uvedené video. Protokoly auditu jsou prezentované přibližně 5:30 označit:

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

### <a name="tips-for-viewing-the-provisioning-audit-logs"></a>Tipy pro zobrazení zřizování protokoly auditu

Pro nejlepší čitelnost na portálu Azure, vyberte **sloupce** tlačítko a vyberte tyto sloupce:

* **Datum** – zobrazuje datum, kdy došlo k události.
* **Cíle (cílů)** – zobrazuje aplikaci název a uživatelské ID, které jsou témata události.
* **Aktivita** – typ aktivity, jak je popsáno výše.
* **Stav** – ať událost byla úspěšná, nebo ne.
* **Důvod stavu** – souhrn co se stalo v zřizování události.


## <a name="troubleshooting"></a>Řešení potíží

Zřizování souhrnné sestavy a auditu protokoly hrát klíčovou roli pomáhá správci řešení potíží s různými uživatelský účet zřizování problémy.

Na základě scénáře pokyny, jak řešit zřizování automatické uživatelů najdete v tématu [problémy konfigurace a zřizování uživatelů k aplikaci](active-directory-application-provisioning-content-map.md).


## <a name="additional-resources"></a>Další prostředky

* [Správa uživatelů zřizování účtu pro podnikové aplikace](manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
