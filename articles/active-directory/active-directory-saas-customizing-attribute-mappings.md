---
title: Přizpůsobení mapování atributů Azure AD | Microsoft Docs
description: Zjistěte, co mapování atributů pro aplikace SaaS ve službě Azure Active Directory se, jak je vyřešit obchodních potřeb můžete upravit.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 549e0b8c-87ce-4c9b-b487-b7bf0155dc77
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2018
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7dad9f3e688c43de3eabd430bf5618ad4632ca3d
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035804"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Přizpůsobení mapování atributů zřizování pro aplikace SaaS ve službě Azure Active Directory uživatelů
Microsoft Azure AD poskytuje podporu pro zřizování uživatelů pro aplikace SaaS jiných výrobců jako Salesforce, Google Apps a dalších. Pokud máte zřizování uživatelů pro aplikace SaaS třetích stran povoleno, portálu Azure prvky jeho hodnot atributů v podobě mapování atributů.

Existuje předem nakonfigurovaná sada atributů a mapování atributů mezi objekty uživatele Azure AD a každá aplikace SaaS uživatelské objekty. Některé aplikace spravovat jiné typy objektů, kromě uživatele, například skupin. <br> 
 Mapování atributů výchozí můžete přizpůsobit podle obchodních potřeb. To znamená, můžete změnit nebo odstranit existující mapování atributů nebo vytvořte nové mapování atributů.
 
## <a name="editing-user-attribute-mappings"></a>Úpravy atribut mapování uživatelů

Na portálu Azure AD, dostanete tuto funkci kliknutím **mapování** konfigurace v **zřizování** v **spravovat** části  **Podniková aplikace**.


![Salesforce][5] 

Kliknutím **mapování** konfigurace, otevře se související **mapování atributů** obrazovky. Nejsou mapování atributů vyžadované aplikací SaaS fungovat správně. Pro povinné atributy **odstranit** funkce není dostupná.


![Salesforce][6]  

V předchozím příkladu můžete uvidíte, že **uživatelské jméno** se zobrazí v atributu spravovaného objektu v Salesforce **userPrincipalName** hodnota propojené objekt služby Azure Active Directory.

Můžete upravit existující **mapování atributů** kliknutím mapování. Tím se otevře **Upravit atribut** obrazovky.

![Salesforce][7]  


### <a name="understanding-attribute-mapping-types"></a>Principy typů mapování atributů
S mapování atributů řídit, jak jsou naplněny atributy v aplikaci SaaS jiných výrobců. Existují čtyři typy různých mapování podporované:

* **Přímé** – atribut target naplněný hodnotu atributu propojeného objektu ve službě Azure AD.
* **Konstantní** – cílový atribut je naplněna konkrétní řetězec, který jste zadali.
* **Výraz** -cílový atribut je vyplněný, na základě výsledku skriptu jako výraz. 
  Další informace najdete v tématu [zápis výrazy pro mapování atributů v Azure Active Directory](active-directory-saas-writing-expressions-for-attribute-mappings.md).
* **Žádný** -cílový atribut je ponechán beze změny. Ale pokud cílový atribut je někdy prázdná, je naplněna s výchozí hodnotou, který určíte.

Kromě tyto čtyři základní typy vlastní mapování atributů podporují koncept volitelný **výchozí** přiřazení hodnoty. Přiřazení hodnoty výchozí zajistí, že atribut target je vyplněný s hodnotou, když není ani jedna hodnota ve službě Azure AD ani v cílovém objektu. Nejběžnější konfigurace je nechte pole prázdné.


### <a name="understanding-attribute-mapping-properties"></a>Principy vlastnosti mapování atributů

V předchozí části které již byly zavedeny pro vlastnost type mapování atributů.
Kromě tato vlastnost mapování atributů také podporují následující atributy:

- **Zdrojový atribut** -atribut uživatele ze zdrojového systému (Příklad: Azure Active Directory).
- **Atribut target** – atribut uživatele v cílovém systému (Příklad: ServiceNow).
- **Objekty používající tento atribut odpovídat** – jestli toto mapování se má použít k jednoznačné identifikaci uživatele mezi zdrojovým a cílovým systémem. To se obvykle nastavuje na atribut userPrincipalName nebo e-mailu ve službě Azure AD, která se obvykle mapuje na pole uživatelské jméno v cílové aplikace.
- **Odpovídající prioritu** – více odpovídající atributy lze nastavit. Pokud existuje více vyhodnocují se v pořadí podle tohoto pole. Jakmile je nalezena shoda, žádné další odpovídající atributy vyhodnocují.
- **Použít toto mapování**
    - **Vždy** – použít toto mapování na obou vytvoření uživatele a aktualizovat akce
    - **Pouze během vytváření** -použít toto mapování pouze na akcí vytvoření uživatele


## <a name="editing-group-attribute-mappings"></a>Úprava skupiny-mapování atributů

Vybraný počet aplikací, jako je například ServiceNow, pole a Google Apps, podporují možnost pro zřízení objektů skupiny kromě uživatelských objektů. Objekty skupiny může obsahovat vlastnosti skupiny, jako je například zobrazované názvy a e-mailu aliasy, kromě členy skupiny.

![ServiceNow][8]  

Zřizování skupiny můžete volitelně můžete povolit nebo zakázat tak, že vyberete mapování skupin v rámci **mapování**a nastavení **povoleno** na požadovanou možnost v **mapováníatributů** obrazovky.

Atributy zřízené v rámci skupiny objektů můžete přizpůsobit stejným způsobem jako uživatelské objekty, popsané. 

>[!TIP]
>Zřizování objektů skupiny (vlastnosti a členy) je odlišné koncept z [přiřazení skupiny](manage-apps/assign-user-or-group-access-portal.md) k aplikaci. Je možné přiřadit skupinu k aplikaci, ale pouze zřízení uživatelských objektů obsažených ve skupině. K používání skupin v přiřazení není potřeba zřizování úplné skupiny objektů.


## <a name="editing-the-list-of-supported-attributes"></a>Úpravy seznam podporovaných atributů

Uživatelské atributy podporované pro danou aplikaci jsou předem nakonfigurovaná. Rozhraní API správy uživatelů většinu aplikací nepodporují zjišťování schématu, proto není schopen dynamicky generovat seznam podporovaných atributů volání do aplikace Azure AD zřizováním služby. 

Ale některé aplikace podporují vlastní atributy. Aby zřizování služby Azure AD mohli číst a zapisovat do vlastních atributů, je třeba zadat jejich definice do Azure pomocí portálu **zobrazit rozšířené možnosti** políčko v dolní části  **Mapování atributů** obrazovky.

Aplikace a systémy, které podporují přizpůsobení seznamu atributů patří:

* Salesforce
* ServiceNow
* WORKDAY
* Azure Active Directory ([Azure AD Graph API výchozí atributy](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) a jsou podporovány vlastní adresář rozšíření)
* Aplikace, které podporují [SCIM 2.0](https://tools.ietf.org/html/rfc7643), kde atributy definované v [základní schématu](https://tools.ietf.org/html/rfc7643) třeba přidat

>[!NOTE]
>Úpravy seznam podporovaných atributů, které doporučujeme pouze pro správce, kteří přizpůsobili schéma aplikací a systémů a mít první ruky o tom, jak byly definovány jejich vlastních atributů. To může být nutné znalost rozhraní API a vývojáři nástroje poskytované subsystémem pro aplikace nebo systému. 

![Editor][9]  

Při úpravě seznam podporovaných atributů, jsou k dispozici následující vlastnosti:

* **Název** -systému název atributu, jak je definováno ve schématu cílový objekt. 
* **Typ** -typu dat atribut ukládá definovaným ve schématu cílový objekt. To může být jeden z následujících akcí:
   * *Binární* -atribut obsahuje binární data.
   * *Logická hodnota* -atributu obsahuje hodnotu True nebo False.
   * *Data a času* -atribut obsahuje řetězec datum.
   * *Celé číslo* -atribut obsahuje celé číslo.
   * *Referenční dokumentace* -atribut obsahuje ID, které odkazuje na hodnotu v další tabulce v cílové aplikaci.
   * *Řetězec* -atribut obsahuje textového řetězce. 
* **Primární klíč?** -Atribut, jestli je definován jako pole primárního klíče ve schématu cílový objekt.
* **Vyžaduje?** – Jestli atribut je potřeba zadat v cílové aplikace nebo systému.
* **Vícehodnotový?** -, Jestli atribut podporuje více hodnot.
* **Přesný případ?** -Hodnoty atributů, jestli se vyhodnocují způsobem, malá a velká písmena.
* **Rozhraní API výraz** -nepoužívejte, pokud pokyn k tomu v dokumentaci pro konkrétní zřizování konektor (například Workday).
* **Odkazovaný objekt atribut** – Pokud se jedná o atribut typu odkaz, pak tato nabídka umožňuje vybrat tabulky a atributů v cílové aplikaci, která obsahuje hodnotu přidruženou atribut. Například pokud máte atribut s názvem "Oddělení", jejíž uložené hodnoty odkazuje na objektu do samostatné tabulky "Oddělení", vyberete "Departments.Name". Všimněte si, že referenční tabulky a pole Primární ID podporované pro danou aplikaci jsou předem nakonfigurované a aktuálně nelze upravovat pomocí portálu Azure, ale můžete upravit pomocí [rozhraní Graph API](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

Pokud chcete přidat nový atribut, přejděte na konec seznamu podporovaných atributů, vyplňte pole pomocí zadané vstupy a vyberte **přidat atribut**. Vyberte **Uložit** po dokončení přidávání atributy. Pak bude muset znovu načíst **zřizování** kartu pro nové atributy k dispozici v editoru mapování atributů.

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Obnovit výchozí atributy a mapování atributů

Byste potřebovali začít znovu a resetování existující mapování zpátky na jejich výchozí stav, můžete vybrat **obnovit výchozí mapování** zaškrtněte políčko a uložte konfiguraci. Nastaví tato všechna mapování, protože pokud aplikace právě byla přidána ke klientovi Azure AD z Galerie aplikace. 

Výběrem této možnosti bude efektivně vynutit opětovnou synchronizaci všech uživatelů, při zřizování služby. 

>[!IMPORTANT]
>Důrazně doporučujeme, **Stav zřizování** nastavit na **vypnout** před vyvoláním tuto možnost.


## <a name="what-you-should-know"></a>Důležité informace

* Microsoft Azure AD poskytuje efektivní implementaci procesu synchronizace. V prostředí inicializovaného jsou zpracovány pouze objekty, které vyžadují aktualizace při synchronizační cyklus. 

* Aktualizace mapování atributů má vliv na výkon synchronizační cyklus. Aktualizace konfigurace mapování atributů vyžaduje všechny spravované objekty do znovu vyhodnocena. 

* Je součástí osvědčeného postupu udrželi počet po sobě jdoucích změn na vaše mapování atributů minimálně.


## <a name="next-steps"></a>Další postup

* [Automatizovat uživatele zřízení nebo zrušení zřízení k aplikacím SaaS](active-directory-saas-app-provisioning.md)
* [Zapisují se výrazy pro mapování atributů](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtry pro zřizování uživatelů oborů](active-directory-saas-scoping-filters.md)
* [Zapnutí automatického zřizování uživatelů a skupin ze služby Azure Active Directory do aplikací pomocí SCIM](manage-apps/use-scim-to-provision-users-and-groups.md)
* [Seznam kurzů k integraci aplikací SaaS](saas-apps/tutorial-list.md)

<!--Image references-->
[5]: ./media/active-directory-saas-customizing-attribute-mappings/21.png
[6]: ./media/active-directory-saas-customizing-attribute-mappings/22.png
[7]: ./media/active-directory-saas-customizing-attribute-mappings/23.png
[8]: ./media/active-directory-saas-customizing-attribute-mappings/24.png
[9]: ./media/active-directory-saas-customizing-attribute-mappings/25.PNG

