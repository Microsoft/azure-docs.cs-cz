---
title: Přizpůsobování mapování atributů Azure AD | Microsoft Docs
description: Přečtěte si, jaké mapování atributů pro aplikace SaaS v Azure Active Directory způsob jejich úprav, abyste mohli řešit své obchodní potřeby.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2020
ms.author: kenwith
ms.openlocfilehash: aaafd3833c63458f8403dfeb3620d76f631315b5
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92899190"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Přizpůsobení uživatelského zřizování atributů – mapování pro SaaS aplikace v Azure Active Directory

Microsoft Azure AD poskytuje podporu pro zřizování uživatelů pro aplikace SaaS třetích stran, jako jsou Salesforce, G Suite a další. Pokud povolíte zřizování uživatelů pro aplikaci SaaS třetí strany, Azure Portal řídí její hodnoty atributu prostřednictvím mapování atributů.

K dispozici je předem nakonfigurovaná sada atributů a mapování atributů mezi uživatelskými objekty Azure AD a všemi uživatelskými objekty aplikace v SaaS. Některé aplikace spravují jiné typy objektů spolu s uživateli, jako jsou skupiny.

Výchozí mapování atributů můžete přizpůsobit podle potřeb vaší firmy. Takže můžete změnit nebo odstranit existující mapování atributů nebo vytvořit nová mapování atributů.

## <a name="editing-user-attribute-mappings"></a>Úpravy atributu uživatele – mapování

Pomocí těchto kroků můžete získat přístup k funkci **mapování** zřizování uživatelů:

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com).
1. V levém podokně vyberte **podnikové aplikace** . Zobrazí se seznam všech nakonfigurovaných aplikací, včetně aplikací přidaných z galerie.
1. Vyberte libovolnou aplikaci a načtěte její podokno Správa aplikací, kde můžete zobrazit sestavy a spravovat nastavení aplikace.
1. Vyberte **zřizování** pro správu nastavení zřizování uživatelských účtů pro vybranou aplikaci.
1. Rozbalením **mapování** můžete zobrazit a upravit atributy uživatele, které budou tok mezi Azure AD a cílovou aplikací. Pokud cílová aplikace tuto aplikaci podporuje, Tato část vám umožní volitelně nakonfigurovat zřizování skupin a uživatelských účtů.

   ![Použití mapování k zobrazení a úpravě atributů uživatele](./media/customize-application-attributes/21.png)

1. Vyberte konfiguraci **mapování** , aby se otevřela obrazovka **mapování souvisejících atributů** . Některá mapování atributů vyžaduje aplikace SaaS, aby fungovala správně. U vyžadovaných atributů není dostupná funkce **Odstranit** .

   ![Použití mapování atributů ke konfiguraci mapování atributů pro aplikace](./media/customize-application-attributes/22.png)

   Na tomto snímku obrazovky vidíte, že atribut **username** spravovaného objektu v Salesforce se naplní hodnotou **userPrincipalName** odkazovaného objektu Azure Active Directory.

1. Vyberte existující **mapování atributů** a otevřete tak obrazovku **Upravit atribut** . Tady můžete upravit atributy uživatele, které se budou přesměrovat mezi Azure AD a cílovou aplikací.

   ![Pro úpravu atributů uživatele použijte úpravu atributu](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Porozumění typům mapování atributů

Pomocí mapování atributů můžete řídit způsob naplnění atributů v aplikaci SaaS třetí strany.
Podporovány jsou čtyři různé typy mapování:

- **Direct** – cílový atribut se naplní hodnotou atributu propojeného objektu ve službě Azure AD.
- **Constant** – cílový atribut je naplněný konkrétním řetězcem, který jste zadali.
- **Výraz** – cílový atribut je vyplněný na základě výsledku výrazu podobného skriptu.
  Další informace najdete v tématu [zápis výrazů pro Attribute-Mappings v Azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md).
- **None** – cílový atribut zůstane beze změny. Nicméně pokud cílový atribut je někdy prázdný, je vyplněný výchozí hodnotou, kterou určíte.

Spolu s těmito čtyřmi základními typy jsou vlastní mapování atributů podporují koncept volitelného přiřazení **výchozí** hodnoty. Výchozí přiřazení hodnoty zajišťuje, že cílový atribut je naplněný hodnotou, pokud neexistuje hodnota ve službě Azure AD nebo v cílovém objektu. Nejběžnější konfigurací je ponecháno toto prázdné.

### <a name="understanding-attribute-mapping-properties"></a>Porozumění vlastnostem mapování atributů

V předchozí části jste již zavedli vlastnost typu mapování atributů.
Spolu s touto vlastností také mapování atributů podporuje následující atributy:

- **Zdrojový atribut** – atribut uživatele ze zdrojového systému (příklad: Azure Active Directory).
- **Cílový atribut** – atribut uživatele v cílovém systému (příklad: ServiceNow).
- **Výchozí hodnota, pokud je null (volitelné)** – hodnota, která se předává cílovému systému, pokud má zdrojový atribut hodnotu null. Tato hodnota se zřídí jenom při vytvoření uživatele. Při aktualizaci stávajícího uživatele nebude zajištěna výchozí hodnota, pokud je null. Pokud například chcete zřídit všechny existující uživatele v cílovém systému s určitou funkcí (Pokud má ve zdrojovém systému hodnotu null), můžete použít následující [výraz](../app-provisioning/functions-for-customizing-application-data.md): Switch (přítomen ([jobtitle]), "DefaultValue", "true", [jobtitle]). Nezapomeňte nahradit "výchozí hodnotu" s tím, co byste chtěli ve zdrojovém systému zřídit, když má hodnotu null. 
- **Porovnává objekty pomocí tohoto atributu** – určuje, jestli se má toto mapování použít k jednoznačné identifikaci uživatelů mezi zdrojovým a cílovým systémem. Obvykle se nastavuje u atributu userPrincipalName nebo mail v Azure AD, který je obvykle namapován na pole username v cílové aplikaci.
- **Priorita porovnání** – lze nastavit více vyhovujících atributů. Pokud existuje více, vyhodnotí se v pořadí definovaném tímto polem. Jakmile se najde shoda, nevyhodnocují se žádné další odpovídající atributy. I když můžete nastavit libovolný počet vyhovujících atributů, je vhodné zvážit, zda atributy, které používáte jako vyhovující atributy, jsou skutečně jedinečné a je nutné, aby odpovídaly atributům. Obecně zákazníci mají ve své konfiguraci 1 nebo 2 odpovídající atributy. 
- **Použít toto mapování**
  - **Vždy** – použít toto mapování na akce vytvoření a aktualizace uživatele
  - **Pouze během vytváření** – použít toto mapování pouze při akcích vytvoření uživatele.

## <a name="matching-users-in-the-source-and-target--systems"></a>Porovnání uživatelů se zdrojovými a cílovými systémy
Služba zřizování Azure AD se dá nasadit ve scénářích "bezserverová" (kde se uživatelé neukončí v cílovém systému) a ve scénářích "brownfield" (kde už uživatelé v cílovém systému existují). Pro podporu obou scénářů používá zřizovací služba koncept vyhovujících atributů. Odpovídající atributy umožňují určit, jak jednoznačně identifikovat uživatele ve zdroji a jak se bude shodovat s uživatelem v cíli. V rámci plánování nasazení Identifikujte atribut, který se dá použít k jednoznačné identifikaci uživatele ve zdrojovém a cílovém systému. Co je potřeba vzít v vědomí:

- **Párové atributy by měly být jedinečné:** Zákazníci často používají atributy, jako je například userPrincipalName, mail nebo ID objektu, jako shodný atribut.
- **Jako párové atributy lze použít více atributů:** Můžete definovat více atributů, které mají být vyhodnoceny při shodě uživatelů a pořadí, ve kterém jsou vyhodnocovány (definované jako priorita pro porovnání v uživatelském rozhraní). Pokud například definujete tři atributy jako odpovídající atributy a uživatel se jednoznačně shoduje po vyhodnocení prvních dvou atributů, služba nebude vyhodnocovat třetí atribut. Služba vyhodnotí odpovídající atributy v zadaném pořadí a zastaví hodnocení, když se najde shoda.  
- **Hodnota ve zdroji a cíli nemusí přesně odpovídat:** Hodnota v cíli může být jednoduchou funkcí hodnoty ve zdroji. Takže jeden může mít atribut emailAddress ve zdroji a userPrincipalName v cíli a musí odpovídat funkci atributu emailAddress, který nahrazuje některé znaky nějakou konstantní hodnotou.  
- **Porovnání na základě kombinace atributů není podporováno:** Většina aplikací nepodporuje dotazování na základě dvou vlastností. Proto není možné se shodovat na základě kombinace atributů. Je možné vyhodnotit jednu vlastnost po druhém.
- **Všichni uživatelé musí mít hodnotu aspoň u jednoho odpovídajícího atributu:** Pokud definujete jeden odpovídající atribut, všichni uživatelé musí mít hodnotu pro tento atribut ve zdrojovém systému. Pokud například definujete atribut userPrincipalName jako atribut Matching, všichni uživatelé musí mít hodnotu userPrincipalName. Pokud definujete více atributů odpovídajícího (např. extensionAttribute1 a mail), ne všichni uživatelé musí mít stejný shodný atribut. Jeden uživatel může mít extensionAttribute1, ale ne poštu, zatímco jiný uživatel může mít poštu, ale ne extensionAttribute1. 
- **Cílová aplikace musí podporovat filtrování u odpovídajícího atributu:** Vývojáři aplikací umožňují filtrování pro podmnožinu atributů v rozhraní API uživatele nebo skupiny. Pro aplikace v galerii zajišťujeme, aby bylo mapování výchozího atributu pro atribut, který podporuje rozhraní API cílové aplikace. Při změně výchozího atributu odpovídajícího pro cílovou aplikaci zkontrolujte v dokumentaci k rozhraní API třetí strany, zda lze atribut filtrovat.  

## <a name="editing-group-attribute-mappings"></a>Úprava atributu skupiny – mapování

Vybraný počet aplikací, například ServiceNow, box a G Suite, podporuje možnost zřídit objekty skupiny a uživatelské objekty. Skupinové objekty můžou obsahovat vlastnosti skupiny, jako jsou zobrazovaná jména a e-mailové aliasy, spolu se členy skupiny.

![Příklad ukazuje ServiceNow se zřízené objekty skupiny a uživatele.](./media/customize-application-attributes/24.png)

Zřizování skupin se dá volitelně zapnout nebo vypnout tak, že v části **mapování** vyberete mapování skupiny a na obrazovce **mapování atributů** **povolíte** požadované možnosti.

Atributy zřízené jako součást objektů skupiny lze přizpůsobit stejným způsobem jako objekty uživatele popsané dříve. 

> [!TIP]
> Zřizování skupinových objektů (vlastností a členů) je jedinečný koncept z [přiřazování skupin](../manage-apps/assign-user-or-group-access-portal.md) do aplikace. Je možné přiřadit skupinu k aplikaci, ale pouze zřídit objekty uživatele obsažené ve skupině. Zřizování úplných objektů skupiny není vyžadováno pro používání skupin v přiřazeních.

## <a name="editing-the-list-of-supported-attributes"></a>Úpravy seznamu podporovaných atributů

Atributy uživatele podporované pro danou aplikaci jsou předem nakonfigurované. Většina rozhraní API pro správu uživatelů aplikace nepodporuje zjišťování schématu. Proto služba zřizování Azure AD nemůže dynamicky generovat seznam podporovaných atributů tím, že provádí volání do aplikace.

Některé aplikace ale podporují vlastní atributy a služba zřizování Azure AD může číst a zapisovat do vlastních atributů. Pokud chcete do Azure Portal zadat jejich definice, zaškrtněte políčko **Zobrazit pokročilé možnosti** v dolní části obrazovky **mapování atributů** a potom vyberte **Upravit seznam atributů pro** vaši aplikaci.

Mezi aplikace a systémy, které podporují přizpůsobení seznamu atributů, patří:

- Salesforce
- ServiceNow
- Pracovní den do Azure Active Directory služby Active Directory/pracovní den
- SuccessFactors se ke službě Active Directory/SuccessFactors pro Azure Active Directory
- Azure Active Directory (podporují se[výchozí atributy služby Azure AD Graph API](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#user-entity) a vlastní rozšíření adresáře)
- Aplikace, které podporují [SCIM 2,0](https://tools.ietf.org/html/rfc7643), ve kterých je potřeba přidat atributy definované ve [schématu Core](https://tools.ietf.org/html/rfc7643)
- Pro Azure Active Directory zpětný zápis do Workday nebo SuccessFactors se podporuje aktualizace relevantních metadat pro podporované atributy (XPATH a JSONPath), ale není podporováno přidávání nových atributů Workday nebo SuccessFactors nad rámec těch, které jsou zahrnuté ve výchozím schématu.


> [!NOTE]
> Úpravy seznamu podporovaných atributů se doporučují jenom správcům, kteří přizpůsobili schéma svých aplikací a systémů, a mají první znalosti o tom, jak jsou vlastní atributy definované. To někdy vyžaduje znalost rozhraní API a vývojářských nástrojů poskytovaných aplikací nebo systémem.

Při úpravách seznamu podporovaných atributů jsou k dispozici následující vlastnosti:

- **Název** – systémový název atributu, jak je definovaný ve schématu cílového objektu.
- **Typ** – typ dat, který atributy ukládají, jak je definováno ve schématu cílového objektu, což může být jeden z následujících typů:
  - *Binární* atribut obsahuje binární data.
  - Atribut *Boolean* -Attribute obsahuje hodnotu true nebo false.
  - *Hodnota DateTime* -atribut obsahuje řetězec data.
  - *Celočíselný* atribut obsahuje celé číslo.
  - *Odkaz* -atribut obsahuje ID, které odkazuje na hodnotu uloženou v jiné tabulce cílové aplikace.
  - *Řetězcový*  atribut obsahuje textový řetězec.
- **Primární klíč?** – Určuje, zda je atribut definován jako pole primárního klíče ve schématu cílového objektu.
- **Požadovanou?** – Určuje, zda je atribut požadován k naplnění cílové aplikace nebo systému.
- **Více hodnot?** – Zda atribut podporuje více hodnot.
- **Přesný případ?** – Zda jsou hodnoty atributů vyhodnocovány způsobem, který rozlišuje velká a malá písmena.
- **Výraz rozhraní API** – nepoužívejte, pokud k tomu nejste vyzváni v dokumentaci pro konkrétní zřizovací konektor (například Workday).
- **Odkazovaný atribut objektu** – Pokud se jedná o atribut typu odkazu, pak v této nabídce můžete vybrat tabulku a atribut v cílové aplikaci, která obsahuje hodnotu přidruženou k atributu. Například pokud máte atribut s názvem "Department", jehož uložená hodnota odkazuje na objekt v samostatné tabulce "oddělení", vyberte "Departments.Name". Referenční tabulky a pole primárních ID podporovaná pro danou aplikaci jsou předem nakonfigurované a aktuálně se nedají upravovat pomocí Azure Portal, ale dají se upravit pomocí [rozhraní Microsoft Graph API](/graph/api/resources/synchronization-configure-with-custom-target-attributes).

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>Zřízení vlastního rozšíření atributu pro aplikaci vyhovující SCIM
SCIM RFC definuje základní schéma uživatelů a skupin a zároveň umožňuje rozšíření schématu, aby splňovala potřeby vaší aplikace. Přidání vlastního atributu do aplikace SCIM:
   1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com), vyberte **podnikové aplikace** , vyberte svou aplikaci a pak vyberte **zřizování** .
   2. V části **mapování** vyberte objekt (uživatele nebo skupinu), pro který chcete přidat vlastní atribut.
   3. V dolní části stránky vyberte **Zobrazit upřesňující možnosti** .
   4. Vyberte možnost **Upravit seznam atributů pro AppName** .
   5. V dolní části seznamu atributů zadejte informace o vlastním atributu v zadaných polích. Pak vyberte **Přidat atribut** .

Pro SCIM aplikace musí název atributu odpovídat vzoru, který je znázorněn v následujícím příkladu. "CustomExtensionName" a "CustomAttribute" je možné přizpůsobit podle požadavků vaší aplikace, například: urn: IETF: param: SCIM: schémata: rozšíření: CustomExtensionName: 2.0: uživatel: Atribut CustomAttribute 

Tyto pokyny platí pouze pro aplikace s podporou SCIM. Aplikace jako ServiceNow a Salesforce nejsou integrovány se službou Azure AD pomocí SCIM, a proto při přidávání vlastního atributu nevyžadují tento konkrétní obor názvů.

Vlastní atributy nemůžou být referenční atributy ani atributy s více hodnotami. Vlastní atributy rozšíření s více hodnotami se aktuálně podporují jenom pro aplikace v galerii.  
 
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
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User": {
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


## <a name="provisioning-a-role-to-a-scim-app"></a>Zřízení role pro aplikaci SCIM
Pomocí následujících kroků můžete zřídit role pro uživatele aplikace. Všimněte si, že popis níže je specifický pro vlastní SCIM aplikace. Pro aplikace Galerie, jako jsou Salesforce a ServiceNow, použijte předdefinované mapování rolí. Odrážky níže popisují, jak transformovat atribut AppRoleAssignments na formát, který aplikace očekává.

- Mapování appRoleAssignment ve službě Azure AD na roli v aplikaci vyžaduje, abyste atribut transformoval pomocí [výrazu](../app-provisioning/functions-for-customizing-application-data.md). Atribut appRoleAssignment **by neměl být namapován přímo** na atribut role bez použití výrazu k analýze podrobností role. 

- **SingleAppRoleAssignment** 
  - **Kdy použít:** Pomocí výrazu SingleAppRoleAssignment můžete zřídit jednu roli pro uživatele a určit primární roli. 
  - **Postup konfigurace:** Pomocí kroků popsaných výše přejděte na stránku mapování atributů a použijte výraz SingleAppRoleAssignment pro mapování na atribut role. Existují tři atributy role, ze kterých si můžete vybrat: (role [primární EQ "true"]. Display, role [primární EQ "true]. Type a role [Primary EQ" true "]. Value). Můžete se rozhodnout zahrnout do mapování všechny atributy role nebo všechny. Pokud chcete zahrnout více než jeden, stačí přidat nové mapování a zahrnout ho jako cílový atribut.  
  
  ![Přidat SingleAppRoleAssignment](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **Co je potřeba zvážit**
    - Ujistěte se, že uživateli není přiřazeno více rolí. Nemůžeme zaručit, která role se zřídí.
    
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
  
- **AppRoleAssignmentsComplex** 
  - **Kdy použít:** Pomocí výrazu AppRoleAssignmentsComplex můžete zřídit více rolí pro uživatele. 
  - **Postup konfigurace:** Upravte seznam podporovaných atributů, jak je popsáno výše, aby zahrnovaly nový atribut pro role: 
  
    ![Přidání rolí](./media/customize-application-attributes/add-roles.png)<br>

    Pak použijte výraz AppRoleAssignmentsComplex k namapování na atribut vlastní role, jak je znázorněno na následujícím obrázku:

    ![Přidat AppRoleAssignmentsComplex](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **Co je potřeba zvážit**
    - Všechny role se zřídí jako primární = false.
    - PŘÍSPĚVEK obsahuje typ role. Žádost o opravu neobsahuje typ. Pracujeme na posílání typu v žádosti POST i PATCH.
    
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
Některé atributy jako phoneNumbers a e-maily jsou atributy s více hodnotami, kde možná budete muset zadat různé typy telefonních čísel nebo e-mailů. Použijte níže uvedený výraz pro vícehodnotové atributy. Umožňuje zadat typ atributu a mapovat ho na odpovídající atribut uživatele Azure AD pro tuto hodnotu. 

* phoneNumbers[type eq "work"].value
* phoneNumbers[type eq "mobile"].value
* phoneNumbers [typ EQ "fax"]. hodnota

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

Pokud potřebujete začít znovu a obnovit stávající mapování zpátky do výchozího stavu, můžete zaškrtnout políčko **Obnovit výchozí mapování** a uložit konfiguraci. Tím se nastaví všechna mapování a filtry oborů, jako by byla aplikace jenom do tenanta Azure AD přidaná z Galerie aplikací.

Výběrem této možnosti efektivně vynutíte opětovnou synchronizaci všech uživatelů, když je služba zřizování spuštěná.

> [!IMPORTANT]
> Před vyvoláním této možnosti důrazně doporučujeme, aby byl **stav zřizování** nastavený na **vypnuto** .

## <a name="what-you-should-know"></a>Co byste měli vědět

- Microsoft Azure AD poskytuje efektivní implementaci procesu synchronizace. V inicializovaném prostředí jsou během synchronizačního cyklu zpracovávány pouze objekty vyžadující aktualizace.
- Aktualizace mapování atributů má vliv na výkon synchronizačního cyklu. Aktualizace konfigurace mapování atributů vyžaduje opětovné vyhodnocení všech spravovaných objektů.
- Doporučeným postupem je udržet počet po sobě jdoucích změn na mapování atributů minimálně.
- Přidání atributu Photo, který se má zřídit pro aplikaci, není dnes podporované, protože nemůžete zadat formát pro synchronizaci fotografie. Můžete požádat o funkci na [hlas uživatele](https://feedback.azure.com/forums/169401-azure-active-directory) .
- Atribut IsSoftDeleted je často součástí výchozího mapování pro aplikaci. IsSoftdeleted může být true v jednom ze čtyř scénářů (uživatel je mimo rozsah z důvodu nepřiřazené k aplikaci, uživatel je mimo rozsah, protože nesplňuje filtr oboru, uživatel byl v Azure AD předem odstraněn nebo vlastnost AccountEnabled je pro uživatele nastavená na hodnotu false). Nedoporučuje se odebrat atribut IsSoftDeleted z mapování atributů.
- Služba zřizování Azure AD nepodporuje zřizování hodnot null.
- Primární klíč, obvykle "ID", by neměl být zahrnut jako cílový atribut v mapování atributů. 
- Atribut role obvykle musí být namapován pomocí výrazu místo přímého mapování. Další podrobnosti o mapování rolí najdete v části výše. 
- I když můžete zakázat skupiny z mapování, zákaz uživatelů není podporován. 

## <a name="next-steps"></a>Další kroky

- [Automatizace zřizování a rušení zřizování uživatelů pro aplikace SaaS](user-provisioning.md)
- [Zápis výrazů pro mapování atributů](functions-for-customizing-application-data.md)
- [Filtry oborů pro zřizování uživatelů](define-conditional-rules-for-provisioning-user-accounts.md)
- [Pomocí SCIM zapněte automatické zřizování uživatelů a skupin ze služby Azure Active Directory do aplikací](use-scim-to-provision-users-and-groups.md)
- [Seznam kurzů o integraci aplikací SaaS](../saas-apps/tutorial-list.md)
