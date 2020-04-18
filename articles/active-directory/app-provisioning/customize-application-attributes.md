---
title: Přizpůsobení mapování atributů Azure AD | Dokumenty společnosti Microsoft
description: Zjistěte, jaká mapování atributů pro aplikace SaaS ve službě Azure Active Directory jsou způsob, jak je můžete upravit tak, aby řešily vaše obchodní potřeby.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfee19e9cfd1def71ebad82c2210ffc10146c896
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639747"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Přizpůsobení mapování atributů pro aplikace SaaS pro uživatele ve službě Azure Active Directory

Microsoft Azure AD poskytuje podporu pro zřizování uživatelů pro aplikace SaaS třetích stran, jako jsou Salesforce, G Suite a další. Pokud povolíte zřizování uživatelů pro aplikaci SaaS jiného výrobce, portál Azure řídí jeho hodnoty atributů prostřednictvím mapování atributů.

Existuje předem nakonfigurovaná sada atributů a mapování atributů mezi uživatelskými objekty Azure AD a uživatelskými objekty každé aplikace SaaS. Některé aplikace spravují jiné typy objektů spolu s uživateli, například Skupiny.

Výchozí mapování atributů můžete přizpůsobit podle vašich obchodních potřeb. Takže můžete změnit nebo odstranit existující mapování atributů nebo vytvořit nové mapování atributů.

## <a name="editing-user-attribute-mappings"></a>Úpravy mapování atributů uživatelů

Chcete-li získat přístup k funkci **Mapování** zřizování uživatelů, postupujte takto:

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com).
1. V levém podokně vyberte **podnikové aplikace.** Zobrazí se seznam všech nakonfigurovaných aplikací, včetně aplikací, které byly přidány z galerie.
1. Vyberte libovolnou aplikaci, ve které chcete načíst podokno správy aplikací, ve které můžete zobrazit sestavy a spravovat nastavení aplikace.
1. Vyberte **Zřizování,** chcete-li spravovat nastavení zřizování uživatelského účtu pro vybranou aplikaci.
1. Rozbalte **mapování** a zobrazte a upravte atributy uživatele, které točí mezi Azure AD a cílovou aplikací. Pokud to cílová aplikace podporuje, tato část umožňuje volitelně konfigurovat zřizování skupin a uživatelských účtů.

   ![Zobrazení a úpravy uživatelských atributů pomocí mapování](./media/customize-application-attributes/21.png)

1. Výběrem konfigurace **Mapování** otevřete související obrazovku **Mapování atributů.** Některé mapování atributů jsou vyžadovány aplikací SaaS, aby fungovaly správně. Pro požadované atributy funkce **Odstranit** není k dispozici.

   ![Konfigurace mapování atributů pro aplikace pomocí mapování atributů](./media/customize-application-attributes/22.png)

   Na tomto snímku obrazovky uvidíte, že atribut **Uživatelské jméno** spravovaného objektu v Salesforce je naplněn hodnotou **userPrincipalName** propojeného objektu Služby Active Directory Azure.

1. Vyberte existující **mapování atributů,** chcete-li otevřít obrazovku **Upravit atribut.** Tady můžete upravit atributy uživatele, které toku mezi Azure AD a cílové aplikace.

   ![Úpravy atributů uživatele pomocí atributu Upravit](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Principy typů mapování atributů

Pomocí mapování atributů můžete řídit, jak jsou atributy naplněny v aplikaci SaaS jiného výrobce.
Jsou podporovány čtyři různé typy mapování:

- **Direct** – cílový atribut je naplněn hodnotou atributu propojeného objektu ve službě Azure AD.
- **Konstanta** – cílový atribut je naplněn určitým řetězcem, který jste zadali.
- **Expression** - cílový atribut je naplněn na základě výsledku výrazu podobného skriptu.
  Další informace naleznete [v tématu Zápis výrazů pro mapování atributů ve službě Azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md).
- **Žádný** - cílový atribut zůstane nezměněn. Pokud je však cílový atribut někdy prázdný, je naplněn zadanou výchozí hodnotou.

Spolu s těmito čtyřmi základními typy vlastní mapování atributů podporují koncept volitelného **přiřazení výchozí** hodnoty. Výchozí přiřazení hodnoty zajišťuje, že cílový atribut je naplněn hodnotou, pokud není hodnota ve službě Azure AD nebo na cílovém objektu. Nejběžnější konfigurace je ponechat toto prázdné.

### <a name="understanding-attribute-mapping-properties"></a>Principy vlastností mapování atributů

V předchozí části jste již byli zavedeni do vlastnosti typu mapování atributů.
Spolu s touto vlastností mapování atributů také podporují následující atributy:

- **Atribut zdroje** – atribut uživatele ze zdrojového systému (příklad: Azure Active Directory).
- **Cílový atribut** – atribut uživatele v cílovém systému (příklad: ServiceNow).
- **Výchozí hodnota, pokud null (volitelné)** - Hodnota, která bude předána cílovému systému, pokud je zdrojový atribut null. Tato hodnota bude zřízena pouze při vytvoření uživatele. "Výchozí hodnota při null" nebude zřízena při aktualizaci existujícího uživatele. Pokud například chcete zřídit všechny existující uživatele v cílovém systému s určitou pracovní vlastností (pokud je ve zdrojovém systému null), můžete použít následující [výraz](../app-provisioning/functions-for-customizing-application-data.md): Switch(IsPresent([jobTitle]), "DefaultValue", "True", [jobTitle]). Ujistěte se, že nahradit "Výchozí hodnota" s tím, co chcete zřídit, když null ve zdrojovém systému. 
- **Shoda objektů pomocí tohoto atributu** – Zda toto mapování by měl y být použity k jednoznačné identifikaci uživatelů mezi zdrojové a cílové systémy. Obvykle se nastavuje na atribut userPrincipalName nebo mail ve službě Azure AD, který je obvykle mapován na pole uživatelského jména v cílové aplikaci.
- **Odpovídající priorita** – lze nastavit více odpovídajících atributů. Pokud existuje více, jsou vyhodnoceny v pořadí definovaném tímto polem. Jakmile je nalezena shoda, nejsou vyhodnoceny žádné další odpovídající atributy.
- **Použít toto mapování**
  - **Vždy** – toto mapování použijte na akcích vytváření a aktualizace uživatelů.
  - **Pouze během vytváření** – toto mapování použijte pouze u akcí vytvoření uživatele.

## <a name="matching-users-in-the-source-and-target--systems"></a>Párování uživatelů ve zdrojových a cílových systémech
Zřizování Služby Azure AD lze nasadit v obou "zelené louce" scénáře (kde uživatelé neukončí v cílovém systému) a "brownfield" scénáře (kde uživatelé již existují v cílovém systému). Pro podporu obou scénářů používá služba zřizování koncept odpovídajícíatributy. Odpovídající atributy umožňují určit, jak jednoznačně identifikovat uživatele ve zdroji a odpovídat uživateli v cíli. V rámci plánování nasazení identifikujte atribut, který lze jednoznačně identifikovat uživatele ve zdrojovém a cílovém systému. Co je třeba poznamenat:

- **Odpovídající atributy by měly být jedinečné:** Zákazníci často používají atributy jako userPrincipalName, mail nebo ID objektu jako odpovídající atribut.
- **Jako odpovídající atributy lze použít více atributů:** Můžete definovat více atributů, které mají být vyhodnoceny při porovnávání uživatelů a pořadí, ve kterém jsou vyhodnocovány (definované jako odpovídající prioritu v uživatelském prostředí). Pokud například definujete tři atributy jako odpovídající atributy a uživatel je jedinečně spárován po vyhodnocení prvních dvou atributů, služba nebude hodnotit třetí atribut. Služba vyhodnotí odpovídající atributy v zadaném pořadí a přestane vyhodnocovat, když je nalezena shoda.  
- **Hodnota ve zdroji a cíl nemusí přesně odpovídat:** Hodnota v cíli může být některé jednoduché funkce hodnoty ve zdroji. Tak jeden může mít emailAddress atribut ve zdroji a userPrincipalName v cíli a zápas podle funkce emailAddress atribut, který nahradí některé znaky s některé konstantní hodnotu.  
- **Párování založené na kombinaci atributů není podporováno:** Většina aplikací nepodporuje dotazování na základě dvou vlastností. Proto není možné shodovat na základě kombinace atributů. Je možné vyhodnotit jednotlivé vlastnosti na po druhé.
- **Všichni uživatelé musí mít hodnotu alespoň pro jeden odpovídající atribut:** Pokud definujete jeden odpovídající atribut, všichni uživatelé musí mít hodnotu pro tento atribut ve zdrojovém systému. Pokud například definujete userPrincipalName jako odpovídající atribut, všichni uživatelé musí mít userPrincipalName. Pokud definujete více odpovídající atribut (např. extensionAttribute1 a mail), ne všichni uživatelé musí mít stejný atribut odpovídající. Jeden uživatel může mít extensionAttribute1, ale ne poštu, zatímco jiný uživatel může mít poštu, ale žádné extensionAttribute1. 
- **Cílová aplikace musí podporovat filtrování odpovídajícího atributu:** Vývojáři aplikací umožňují filtrování podmnožiny atributů v rozhraní API pro uživatele nebo skupinu. Pro aplikace v galerii zajistíme, že výchozí mapování atributů je pro atribut, který rozhraní API cílové aplikace podporuje filtrování. Při změně výchozího atributu párování pro cílovou aplikaci zkontrolujte dokumentaci rozhraní API třetí strany, abyste se ujistili, že atribut lze filtrovat.  

## <a name="editing-group-attribute-mappings"></a>Úpravy mapování atributů skupiny

Vybraný počet aplikací, například ServiceNow, Box a G Suite, podporuje možnost zřizování objektů skupiny a objektů uživatele. Objekty skupiny mohou obsahovat vlastnosti skupiny, jako jsou zobrazované názvy a e-mailové aliasy, spolu se členy skupiny.

![Příklad ukazuje ServiceNow s zřízenými objekty Skupiny a Uživatele.](./media/customize-application-attributes/24.png)

Zřizování skupin lze volitelně povolit nebo zakázat výběrem mapování skupin v části Mapování a **nastavením Povoleno** na požadovanou možnost na obrazovce **Mapování atributů.** **Mappings**

Atributy zřízené jako součást objektů Skupiny lze přizpůsobit stejným způsobem jako objekty User, popsané výše. 

> [!TIP]
> Zřizování objektů skupiny (vlastnosti a členy) je odlišný koncept od [přiřazení skupin](../manage-apps/assign-user-or-group-access-portal.md) k aplikaci. Je možné přiřadit skupinu k aplikaci, ale pouze zřídit uživatelské objekty obsažené ve skupině. Zřizování úplných objektů skupiny není nutné používat skupiny v přiřazeních.

## <a name="editing-the-list-of-supported-attributes"></a>Úprava seznamu podporovaných atributů

Atributy uživatele podporované pro danou aplikaci jsou předem nakonfigurovány. Většina rozhraní API pro správu uživatelů aplikace nepodporuje zjišťování schématu. Takže služba zřizování Azure AD není schopna dynamicky generovat seznam podporovaných atributů voláním do aplikace.

Některé aplikace však podporují vlastní atributy a služba zřizování Azure AD můžete číst a zapisovat do vlastních atributů. Pokud chcete zadat jejich definice na portál Azure, zaškrtněte políčko **Zobrazit upřesňující možnosti** v dolní části obrazovky **Mapování atributů** a pak vyberte **Upravit seznam atributů pro** vaši aplikaci.

Mezi aplikace a systémy, které podporují přizpůsobení seznamu atributů, patří:

- Salesforce
- ServiceNow
- Workday
- Azure Active Directory (Microsoft[Graph REST API v1.0 reference](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0) a vlastní adresář rozšíření jsou podporovány)
- Aplikace, které podporují [SCIM 2.0](https://tools.ietf.org/html/rfc7643), kde je třeba přidat atributy definované v [základním schématu](https://tools.ietf.org/html/rfc7643)

> [!NOTE]
> Úprava seznamu podporovaných atributů se doporučuje pouze správcům, kteří přizpůsobili schéma svých aplikací a systémů a mají z první ruky znalosti o tom, jak byly definovány jejich vlastní atributy. To někdy vyžaduje znalost api a vývojářských nástrojů poskytovaných aplikací nebo systémem.

Při úpravách seznamu podporovaných atributů jsou k dispozici následující vlastnosti:

- **Název** - Název systému atributu, jak je definováno ve schématu cílového objektu.
- **Typ** - Typ dat, která atribut ukládá, jak je definováno ve schématu cílového objektu, což může být jeden z následujících typů:
  - *Binární* - Atribut obsahuje binární data.
  - *Logická hodnota* – atribut obsahuje hodnotu True nebo False.
  - *DateTime* - Atribut obsahuje řetězec data.
  - *Celé číslo* - Atribut obsahuje celé číslo.
  - *Odkaz* - Atribut obsahuje ID, které odkazuje na hodnotu uloženou v jiné tabulce v cílové aplikaci.
  - *Řetězec* - Atribut obsahuje textový řetězec.
- **Primární klíč?** - Určuje, zda je atribut definován jako pole primárního klíče ve schématu cílového objektu.
- **Požadované?** - Zda je nutné atribut vyplnit v cílové aplikaci nebo systému.
- **Multi-hodnota?** - Zda atribut podporuje více hodnot.
- **Přesný případ?** - Zda jsou hodnoty atributů vyhodnocovány způsobem rozlišujícím malá a velká písmena.
- **Výraz rozhraní API** – nepoužívejte, pokud k tomu není instruováno dokumentací pro konkrétní zřizovací konektor (například Workday).
- **Atribut odkazovaný objekt** – pokud se jedná o atribut typu Reference, tato nabídka umožňuje vybrat tabulku a atribut v cílové aplikaci, která obsahuje hodnotu přidruženou k atributu. Pokud máte například atribut s názvem "Oddělení", jehož uložená hodnota odkazuje na objekt v samostatné tabulce "Oddělení", vyberte možnost "Departments.Name". Referenční tabulky a primární pole ID podporovaná pro danou aplikaci jsou předem nakonfigurované a momentálně je nelze upravovat pomocí portálu Azure, ale lze je upravovat pomocí [rozhraní Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>Zřizování atributu vlastní rozšíření pro aplikaci kompatibilní s SCIM
Scim RFC definuje základní uživatelské a skupinové schéma a zároveň umožňuje rozšíření schématu tak, aby vyhovovalo potřebám vaší aplikace. Přidání vlastního atributu do aplikace SCIM:
   1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com), vyberte **Podnikové aplikace**, vyberte aplikaci a pak vyberte **Zřídit**.
   2. V části **Mapování**vyberte objekt (uživatele nebo skupinu), pro který chcete přidat vlastní atribut.
   3. V dolní části stránky vyberte **Zobrazit upřesňující možnosti**.
   4. Vyberte **Upravit seznam atributů pro Název aplikace**.
   5. V dolní části seznamu atributů zadejte informace o vlastním atributu do uvedených polí. Pak vyberte **Přidat atribut**.

Pro aplikace SCIM musí název atributu postupovat podle vzoru uvedeného v následujícím příkladu. "CustomExtensionName" a "CustomAttribute" lze přizpůsobit podle požadavků vaší aplikace, například:  
 * urn:ietf:params:scim:schémata:rozšíření:CustomExtensionName:2.0:Uživatel:Vlastní atribut 
 * urn:ietf:params:scim:schémata:rozšíření:2.0:CustomExtensionName:CustomAttribute  
 * urn:ietf:params:scim:schémata:rozšíření:CustomExtensionName:2.0:User.CustomAttributeName:value

Tyto pokyny platí pouze pro aplikace s podporou TECHNOLOGIE SCIM. Aplikace jako ServiceNow a Salesforce nejsou integrované s Azure AD pomocí SCIM, a proto nevyžadují tento konkrétní obor názvů při přidávání vlastního atributu.

Vlastní atributy nemohou být referenční atributy nebo atributy s více hodnotami. Vlastní atributy rozšíření s více hodnotami jsou aktuálně podporovány pouze pro aplikace v galerii.  
 
**Příklad reprezentace uživatele s atributem rozšíření:**

```json
   {
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "formatted":"Ms. Barbara J Jensen III",
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "employeeNumber": "701984",
     "costCenter": "4130",
     "organization": "Universal Studios",
     "division": "Theme Park",
     "department": "Tour Operations",
     "manager": {
       "value": "26118915-6090-4610-87e4-49d8ca9f808d",
       "$ref": "../Users/26118915-6090-4610-87e4-49d8ca9f808d",
       "displayName": "John Smith"
     }
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "CustomAttribute": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 }
```


## <a name="provisioning-a-role-to-a-scim-app"></a>Zřizování role do aplikace SCIM
Pomocí následujících kroků zřídíte role pro uživatele do vaší aplikace. Všimněte si, že níže uvedený popis je specifický pro vlastní aplikace SCIM. Pro aplikace galerie, jako je Salesforce a ServiceNow, použijte předdefinované mapování rolí. Níže uvedené odrážky popisují, jak transformovat AppRoleAssignments atribut ve formátu, který vaše aplikace očekává.

- Mapování appRoleAssignment ve službě Azure AD na roli ve vaší aplikaci vyžaduje, abyste atribut transformovali pomocí [výrazu](../app-provisioning/functions-for-customizing-application-data.md). Atribut appRoleAssignment **by neměl být mapován přímo** na atribut role bez použití výrazu k analýzě podrobností role. 

- **SingleAppRolePřiřazení** 
  - **Kdy použít:** Výraz SingleAppRoleAssignment slouží k zřízení jedné role pro uživatele a k určení primární role. 
  - **Jak konfigurovat:** Pomocí výše popsaných kroků přejděte na stránku mapování atributů a pomocí výrazu SingleAppRoleAssignment namapujte atribut rolí. Můžete si vybrat ze tří atributů role: (role[primární eq "True"].display, role[primární eq "True].type a role[primární eq "True"].value). Do mapování můžete zahrnout některé nebo všechny atributy role. Pokud chcete zahrnout více než jeden, stačí přidat nové mapování a zahrnout jej jako cílový atribut.  
  
  ![Přidat singleapproleassignment](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **Co je třeba zvážit**
    - Ujistěte se, že uživateli není přiřazeno více rolí. Nemůžeme zaručit, jaká role bude zřízena.
    
  - **Příklad výstupu** 

   ```json
    {
      "schemas": [
          "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": true,
               "type": "WindowsAzureActiveDirectoryRole",
               "value": "Admin"
         }
      ]
   }
   ```
  
- **AppRoleAssignmentsKomplexní** 
  - **Kdy použít:** Pomocí výrazu AppRoleAssignmentsComplex zřcužte více rolí pro uživatele. 
  - **Jak konfigurovat:** Upravte seznam podporovaných atributů, jak je popsáno výše, a zahrňte nový atribut pro role: 
  
    ![Přidání rolí](./media/customize-application-attributes/add-roles.png)<br>

    Potom pomocí výrazu AppRoleAssignmentsComplex namapujte na atribut vlastní role, jak je znázorněno na obrázku níže:

    ![Přidat AppRoleAssignmentsComplex](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **Co je třeba zvážit**
    - Všechny role budou zřízeny jako primární = false.
    - Post obsahuje typ role. Požadavek PATCH neobsahuje typ. Pracujeme na zaslání typu v obou POST a PATCH požadavků.
    
  - **Příklad výstupu** 
  
   ```json
   {
       "schemas": [
           "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "Admin",
               "value": "Admin"
         },
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "User",
             "value": "User"
         }
      ]
   }
   ```

  


## <a name="provisioning-a-multi-value-attribute"></a>Zřizování atributu s více hodnotami
Některé atributy, jako jsou telefonní čísla a e-maily, jsou atributy s více hodnotami, kde možná budete muset zadat různé typy telefonních čísel nebo e-mailů. Pro atributy s více hodnotami použijte níže uvedený výraz. Umožňuje zadat typ atributu a namapovat, že na odpovídající atribut uživatele Azure AD pro hodnotu. 

* phoneNumbers[zadejte eq "work"].value
* phoneNumbers[zadejte eq "mobile"].value
* phoneNumbers[zadejte eq "fax"].value

   ```json
   "phoneNumbers": [
       {
         "value": "555-555-5555",
         "type": "work"
      },
      {
         "value": "555-555-5555",
         "type": "mobile"
      },
      {
         "value": "555-555-5555",
         "type": "fax"
      }
   ]
   ```

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Obnovení výchozích atributů a mapování atributů

Pokud potřebujete začít znovu a obnovit stávající mapování zpět do výchozího stavu, můžete zaškrtnout políčko **Obnovit výchozí mapování** a uložit konfiguraci. Tím nastavíte všechna mapování a filtry oborů, jako by byla aplikace právě přidána do klienta Azure AD z galerie aplikací.

Výběr této možnosti účinně vynutí resynchronizaci všech uživatelů, když je spuštěna zřizovací služba.

> [!IMPORTANT]
> Důrazně doporučujeme, aby **stav zřizování** nastavit **na Vypnuto** před vyvoláním této možnosti.

## <a name="what-you-should-know"></a>Co byste měli vědět

- Microsoft Azure AD poskytuje efektivní implementaci procesu synchronizace. V inicializovaném prostředí jsou během cyklu synchronizace zpracovávány pouze objekty vyžadující aktualizace.
- Aktualizace mapování atributů má vliv na výkon cyklu synchronizace. Aktualizace konfigurace mapování atributů vyžaduje, aby byly znovu vyhodnoceny všechny spravované objekty.
- Doporučeným osvědčeným postupem je minimální počet po sobě jdoucích změn mapování atributů.
- Přidání atributu fotografie, který má být zřízen do aplikace, není dnes podporováno, protože nemůžete určit formát pro synchronizaci fotografie. Můžete požádat o tuto funkci na [uživatelském hlasu](https://feedback.azure.com/forums/169401-azure-active-directory)
- Atribut IsSoftDeleted je často součástí výchozímapování pro aplikaci. IsSoftdeleted může být pravda, v jednom ze čtyř scénářů (uživatel je mimo rozsah z důvodu, že je unassigned z aplikace, uživatel je mimo rozsah z důvodu není splnění filtru oborů, uživatel byl měkký odstraněn v Azure AD nebo vlastnost AccountEnabled je nastavena na false na uživatele). 
- Zřizování Azure AD nepodporuje zřizování nulových hodnot

## <a name="next-steps"></a>Další kroky

- [Automatizace zřizování a odzřízení uživatelů do aplikací SaaS](user-provisioning.md)
- [Zápis výrazů pro mapování atributů](../app-provisioning/functions-for-customizing-application-data.md)
- [Filtry oborů pro zřizování uživatelů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [Pomocí SCIM zapněte automatické zřizování uživatelů a skupin ze služby Azure Active Directory do aplikací](use-scim-to-provision-users-and-groups.md)
- [Seznam kurzů o integraci aplikací SaaS](../saas-apps/tutorial-list.md)
