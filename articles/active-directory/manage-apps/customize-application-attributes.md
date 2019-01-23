---
title: Přizpůsobení mapování atributů Azure AD | Dokumentace Microsoftu
description: Zjistěte, jaké mapování atributů pro aplikace SaaS ve službě Azure Active Directory se, jak můžete upravit, tak, aby řeší obchodní potřeby.
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: barbkess
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ead99afaf0399c060424d310d8f4011b2a50aad2
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54473720"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Přizpůsobení mapování atributů zřizování pro aplikace SaaS ve službě Azure Active Directory uživatelů
Microsoft Azure AD poskytuje podporu pro zřizování uživatelů pro aplikace SaaS třetích stran, jako je například Salesforce, Google Apps a jiná. Pokud máte zřizování uživatelů pro aplikace SaaS třetích stran povoleno, řídí na webu Azure portal jeho hodnoty atributů ve formě mapování atributů.

Je předem nakonfigurované sady atributů a atributů mapování mezi objekty uživatele služby Azure AD a příslušné aplikace SaaS uživatelské objekty. Některé aplikace spravovat další typy objektů kromě uživatelů, jako jsou skupiny. <br> 
 Přizpůsobení mapování atributů výchozí podle vašich obchodních potřeb. To znamená, že můžete změnit nebo odstranit existující mapování atributů nebo vytvořit nové mapování atributů.
 
## <a name="editing-user-attribute-mappings"></a>Úpravy uživatele – mapování atributů

Na portálu Azure AD, dostanete tuto funkci kliknutím **mapování** konfiguraci v části **zřizování** v **spravovat** část  **Podniková aplikace**.


![Salesforce](./media/customize-application-attributes/21.png) 

Kliknutím **mapování** konfigurace, otevře se související **mapování atributů** obrazovky. Existují – mapování atributů, které vyžaduje aplikaci SaaS, aby správně fungoval. Pro požadované atributy **odstranit** funkce není k dispozici.


![Salesforce](./media/customize-application-attributes/22.png)

V předchozím příkladu vidíte, že **uživatelské jméno** atribut spravovaný objekt v Salesforce se vyplní **userPrincipalName** hodnotu odkazovaný objekt služby Azure Active Directory.

Můžete upravit existující **mapování atributů** kliknutím mapování. Tím se otevře **Upravit atribut** obrazovky.

![Salesforce](./media/customize-application-attributes/23.png)


### <a name="understanding-attribute-mapping-types"></a>Principy typů mapování atributů
S mapování atributů můžete řídit, jak se atributy vyplní v aplikaci SaaS třetí strany. Existují čtyři jiné typy mapování podporovány:

* **Přímé** – cílový atribut se vyplní hodnotou atributu propojeného objektu ve službě Azure AD.
* **Konstantní** – cílový atribut se vyplní konkrétní řetězec, který jste zadali.
* **Výraz** – cílový atribut se vyplní na základě výsledku výrazu jako skript. 
  Další informace najdete v tématu [zápis výrazů pro mapování atributů ve službě Azure Active Directory](functions-for-customizing-application-data.md).
* **Žádný** – cílový atribut je ponecháno bez úprav. Nicméně pokud cílový atribut je někdy prázdný, otevře se s výchozí hodnotu, která zadáte.

Kromě tyto čtyři základní typy, vlastní mapování atributů podporují koncept volitelně **výchozí** přiřazení hodnoty. Výchozí hodnota přiřazení zajistí, že cílový atribut je vyplněný s hodnotou, když ve službě Azure AD ani na cílový objekt není ani jedna hodnota. Nejběžnější konfigurací je toto pole nechat prázdné.


### <a name="understanding-attribute-mapping-properties"></a>Principy vlastnosti mapování atributů

V předchozí části jste již byly zavedeny mapování atributů typu vlastnosti.
Kromě této vlastnosti mapování atributů podporují také následující atributy:

- **Zdrojový atribut** – atribut uživatele ze zdrojového systému (Příklad: Azure Active Directory).
- **Cílový atribut** – atribut uživatele v cílovém systému (Příklad: ServiceNow).
- **Párování objektů pomocí tohoto atributu** – jestli má toto mapování použít k jednoznačné identifikaci uživatele mezi zdrojovým a cílovým systémem. To se obvykle nastavuje na atribut userPrincipalName nebo e-mailu ve službě Azure AD, která se obvykle mapuje na pole uživatelské jméno v cílové aplikaci.
- **Priorita porovnávání** – více shodné atributy lze nastavit. Když je více, jsou vyhodnocovány v pořadí určeném parametrem toto pole. Jakmile se najde shoda, žádné další odpovídající atributy jsou vyhodnocovány.
- **Použít toto mapování**
    - **Vždy** – použít toto mapování na obou vytvoření uživatele a aktualizovat akce
    - **Jenom během vytváření** -použít toto mapování jenom na akce vytvoření uživatele


## <a name="editing-group-attribute-mappings"></a>Úpravy skupiny – mapování atributů

Vybraný počet aplikací, jako je například Google Apps, ServiceNow a pole podporují možnost zřizování skupiny objektů kromě objektů uživatelů. Objekty skupiny může obsahovat vlastnosti skupiny, jako je zobrazované názvy a aliasy, kromě členy skupiny e-mailu.

![ServiceNow](./media/customize-application-attributes/24.png)

Skupinové zřizování může být volitelně povolit nebo zakázat tak, že vyberete mapování skupin v rámci **mapování**a nastavení **povoleno** na požadovanou možnost v **mapováníatributů** obrazovky.

Atributy zřízeným jako součást skupiny objektů je možné přizpůsobit stejným způsobem jako objekty User, je popsáno výše. 

>[!TIP]
>Zřizování skupiny objektů (vlastností a členy) je koncept odlišné od [přiřazení skupin](assign-user-or-group-access-portal.md) k aplikaci. Je možné přiřadit skupiny k aplikaci, ale pouze zřídit uživatelské objekty obsažených ve skupině. Zřizování objekty celé skupiny není vyžadováno pro použití v přiřazení skupiny.


## <a name="editing-the-list-of-supported-attributes"></a>Seznam podporovaných atributů pro úpravy

Atributy uživatele pro danou aplikaci podporována jsou předem nakonfigurované. Rozhraní API správy uživatelů většina aplikací nepodporují zjišťování schématu, proto není možné dynamicky generovat seznam podporovaných atributů tak, že volání aplikace služba zřizování Azure AD. 

Nicméně některé aplikace podporují vlastní atributy. Aby služba zřizování Azure AD bude moct číst a zapisovat do vlastních atributů, třeba na webu Azure portal pomocí zadat jejich definice **zobrazit pokročilé možnosti** zaškrtávací políčko v dolní části  **Mapování atributů** obrazovky.

Aplikace a systémy, které podporují přizpůsobení seznamu atributů patří:

* Salesforce
* ServiceNow
* Pracovní den
* Azure Active Directory ([Azure AD Graph API výchozí atributy](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) a jsou podporovány vlastní adresáře rozšíření)
* Aplikace, které podporují [SCIM 2.0](https://tools.ietf.org/html/rfc7643), kde atributy definované ve [základní schéma](https://tools.ietf.org/html/rfc7643) je potřeba přidat

>[!NOTE]
>Úpravy v seznamu podporovaných atributů se doporučuje jenom pro správce, kteří si přizpůsobili schématu jejich aplikací a systémů a znalosti zblízka jak jejich vlastní atributy definované. V některých případech to vyžaduje znalost nástroje rozhraní API a vývojáři aplikace nebo systému k dispozici. 

![Editor](./media/customize-application-attributes/25.png) 

Při úpravě seznam podporovaných atributů, jsou k dispozici následující vlastnosti:

* **Název** -systémový název atributu, jak je definováno ve schématu cílový objekt. 
* **Typ** – typ dat ukládá atributu, jak je definováno ve schématu cílový objekt. Může to být jedna z následujících akcí:
   * *Binární* – atribut obsahuje binární data.
   * *Logická* – atribut obsahuje hodnotu True nebo False.
   * *Datum a čas* – atribut obsahuje řetězec data.
   * *Celé číslo* – atribut obsahuje celé číslo.
   * *Referenční dokumentace* – atribut obsahuje ID, která odkazuje hodnotu uloženou v druhé tabulce v cílové aplikaci.
   * *Řetězec* – atribut obsahuje textový řetězec. 
* **Primární klíč?** – Jestli atribut je definován jako pole primárního klíče ve schématu cílový objekt.
* **Vyžaduje?** – Jestli atribut je potřeba dosazeny cílové aplikace nebo systému.
* **Vícehodnotový?** – Jestli atribut podporuje více hodnot.
* **Rozlišovat velikost písmen?** – Jestli hodnoty atributů jsou vyhodnocovány tak velká a malá písmena.
* **Výraz rozhraní API** – nepoužívejte, pokud jste nedostali pokyny k tomu dokumentaci pro konkrétní zřizování konektor (například Workday).
* **Odkazovaný atribut objektu** – Pokud je atribut typu odkaz, pak tato nabídka umožňuje vybrat tabulky a atributů v cílové aplikaci, která obsahuje hodnotu přidruženou k atributu. Například pokud máte atribut s názvem "Oddělení", jehož uložená hodnota odkazuje na objekt v samostatné tabulce "Oddělení", vyberete "Departments.Name". Všimněte si, že referenční tabulky a pole primárního ID podporované pro danou aplikaci předem nakonfigurovaným aktuálně nejde upravovat pomocí webu Azure portal, ale lze upravovat pomocí [rozhraní Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

Chcete-li přidat nový atribut, přejděte na konec seznamu podporovaných atributů, vyplňte pole pomocí zadané vstupy a vyberte **přidat atribut**. Vyberte **Uložit** po dokončení přidávání atributů. Pak bude potřeba znovu načíst **zřizování** kartu pro nové atributy, které se budou k dispozici v editoru mapování atributů.

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Obnovení výchozí atributy a mapování atributů

Budete chtít začít znovu a obnovit existující mapování zpět do výchozího stavu, můžete vybrat **obnovit výchozí mapování** zaškrtněte políčko a uložte konfiguraci. Tím se nastaví všechny mapování jakoby aplikace právě byla přidána do svého tenanta Azure AD z Galerie aplikací. 

Tato možnost vynutí efektivně opětovnou synchronizaci všech uživatelů je spuštěna služba zřizování. 

>[!IMPORTANT]
>Důrazně doporučujeme **Stav zřizování** nastavit na **vypnout** před vyvoláním této možnosti.


## <a name="what-you-should-know"></a>Co byste měli vědět

* Microsoft Azure AD poskytuje efektivní provádění procesu synchronizace. V prostředí inicializována pouze objekty, které vyžadují aktualizace jsou zpracovány při synchronizační cyklus. 

* Aktualizace mapování atributů má vliv na výkon synchronizační cyklus. Aktualizace konfigurace mapování atributů, vyžaduje se znovu vyhodnotit všechny spravované objekty. 

* Je součástí osvědčeného udrželi počet po sobě jdoucích změn na vaší mapování atributů minimálně.


## <a name="next-steps"></a>Další postup

* [Automatizace uživatele zřizování a jeho rušení pro aplikace SaaS](user-provisioning.md)
* [Zápis výrazů pro mapování atributů](functions-for-customizing-application-data.md)
* [Filtry oborů pro zřizování uživatelů](define-conditional-rules-for-provisioning-user-accounts.md)
* [Zapnutí automatického zřizování uživatelů a skupin ze služby Azure Active Directory do aplikací pomocí SCIM](use-scim-to-provision-users-and-groups.md)
* [Seznam kurzů o integraci aplikací SaaS](../saas-apps/tutorial-list.md)


