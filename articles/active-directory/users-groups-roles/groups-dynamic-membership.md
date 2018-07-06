---
title: Pravidla členství v dynamické skupině založených na atributech ve službě Azure Active Directory | Dokumentace Microsoftu
description: Postup vytváření rozšířených pravidel, včetně členství v dynamické skupině podporované operátory pravidel výraz a parametry.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 07/05/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: a48dcff6eedc2aa6e8bb6cd5b0668af72259493b
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37869083"
---
# <a name="create-attribute-based-rules-for-dynamic-group-membership-in-azure-active-directory"></a>Vytvoření pravidel založených na atributech pro členství v dynamické skupině v Azure Active Directory
Ve službě Azure Active Directory (Azure AD) můžete vytvořit vlastní pravidla pro povolení komplexní založených na atributech dynamické členství ve skupinách. Tento článek podrobně popisuje atributy a syntaxe pro vytvoření pravidla dynamického členství pro uživatele nebo zařízení. Pravidlo pro dynamické členství můžete nastavit pro skupiny zabezpečení nebo pro skupiny Office 365.

Když se změní libovolné atributy uživatele nebo zařízení, systém vyhodnotí všechna dynamická pravidla skupin v adresáři a zjistěte, jestli tato změna se aktivuje všechny skupiny přidá nebo odebere. Pokud uživatel nebo zařízení splňuje pravidlo pro skupinu, se přidají jako člena této skupiny. Pokud již uživatel pravidlo, se odeberou.

> [!NOTE]
> Tato funkce vyžaduje licenci Azure AD Premium P1 pro každý jedinečný uživatel, který je členem jedné nebo více dynamických skupin. Není nutné k přiřazení licencí uživatelům pro ně být členem dynamické skupiny, ale minimální počet licencí musí mít v tenantovi k pokrytí všech těchto uživatelů. Například pokud máte celkem 1 000 jedinečných uživatelů v všech dynamických skupin ve vašem tenantovi, je třeba alespoň 1000 licencí pro Azure AD Premium P1 pro splnění požadavcích na licence.
>
> Můžete vytvořit dynamické skupiny zařízení nebo uživatelů, ale nelze vytvořit pravidlo, které obsahuje uživatele a zařízení.
> 
> V tomto okamžiku není možné vytvořit skupinu zařízení na základě atributů vlastnícího uživatele. Pravidla členství zařízení mohou odkazovat pouze na okamžité atributy zařízení objektů v adresáři.

## <a name="to-create-an-advanced-rule"></a>Chcete-li vytvořit rozšířené pravidlo
1. Přihlaste se k [centrum pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který je globální správce nebo správce uživatelských účtů.
2. Vyberte **uživatelů a skupin**.
3. Vyberte **všechny skupiny**a vyberte **novou skupinu**.

   ![Přidat novou skupinu](./media/groups-dynamic-membership/new-group-creation.png)

4. Na **skupiny** okně zadejte název a popis nové skupiny. Vyberte **typ členství** buď **dynamický uživatel** nebo **dynamické zařízení**, v závislosti na tom, jestli chcete vytvořit pravidlo pro uživatele nebo zařízení a pak vyberte **Přidat dynamický dotaz**. Můžete použít Tvůrce pravidlo k vytvoření jednoduché pravidlo nebo rozšířené pravidlo napsat sami. Tento článek obsahuje další informace o dostupných atributů uživatelů a zařízení, stejně jako příklady rozšířených pravidel.

   ![Přidat dynamické pravidlo členství](./media/groups-dynamic-membership/add-dynamic-group-rule.png)

5. Po vytvoření pravidla, vyberte **přidat dotaz** v dolní části okna.
6. Vyberte **vytvořit** na **skupiny** okno pro vytvoření skupiny.

> [!TIP]
> Vytvoření skupiny selže, pokud pravidlo, které jste zadali, byl nesprávně vytvořený nebo není platný. Oznámení se zobrazí v pravém dolním rohu portálu, který obsahuje vysvětlení, proč nebylo možné zpracovat pravidlo. Přečtěte si ho pečlivě, abyste pochopili, jak je potřeba upravit pravidlo, aby byl platný.

## <a name="status-of-the-dynamic-rule"></a>Stav dynamické pravidlo

Zobrazí se zpracování stavu a datum poslední aktualizace na stránce s přehledem pro vaši skupinu pro dynamické členství.
  
  ![Zobrazení stavu dynamické skupiny](./media/groups-dynamic-membership/group-status.png)


Tyto stavové zprávy lze zobrazit pro **členství zpracování** stavu:
* **Vyhodnocení**: byla přijata změnu skupiny a aktualizace se právě vyhodnocována.
* **Zpracování**: aktualizace se právě zpracovává.
* **Dokončena aktualizace**: bylo dokončeno zpracování a provedli všechny použitelné aktualizace.
* **Chyba zpracování**: došlo k chybě při vyhodnocování pravidel členství a nešlo ho dokončit zpracování.
* **Pozastavená aktualizace**: aktualizace byla pozastavena správcem pravidlo dynamického členství. MembershipRuleProcessingState nastavená na "Pozastaveno".

Tyto stavové zprávy lze zobrazit pro **členství poslední aktualizace** stavu:
* &lt;**Datum a čas**&gt;: poslední čas aktualizace členství.
* **V průběhu**: momentálně probíhají aktualizace.
* **Neznámý**: Nelze získat čas poslední aktualizace. Může být příčinou nově vytvářené skupiny.

Pokud dojde k chybě při zpracování pravidla členství pro konkrétní skupinu, upozornění se zobrazí v horní **stránka s přehledem** pro skupinu. Pokud ne čekající členství v dynamické aktualizace lze zpracovat pro všechny skupiny v rámci tenanta pro další pak 24 hodin, upozornění se zobrazí v horní **všechny skupiny**.

![zpracování chybová zpráva](./media/groups-dynamic-membership/processing-error.png)

## <a name="constructing-the-body-of-an-advanced-rule"></a>Tělo rozšířené pravidlo vytváření
Pokročilé pravidlo, které můžete vytvořit pro dynamické členství pro skupiny je v podstatě binární výraz, který se skládá ze tří částí a výsledkem výsledek true nebo false. Jsou tři části:

* Levý parametr
* Binární operátor
* Správné – konstanta

Kompletní rozšířené pravidlo vypadá podobně jako tento: (leftParameter binaryOperator "RightConstant"), kde levou a pravou závorkou jsou volitelné pro celý binární výraz, dvojité uvozovky jsou volitelné, pouze požadované pro správné – konstanta Když je řetězec a syntaxe pro parametr vlevo je user.property. Rozšířené pravidlo se může skládat z více než jeden binární výrazy oddělené- a- nebo a – nejsou logické operátory.

Následují příklady správně rozšířené pravidlo:
```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```
Úplný seznam podporovaných parametrů a operátory pravidel výrazu najdete v níže uvedených částech. Atributy použité pro pravidla zařízení, najdete v části [používání atributů k vytvoření pravidel pro objekty zařízení](#using-attributes-to-create-rules-for-device-objects).

Celková délka obsahu rozšířené pravidlo nemůže být delší než 2 048 znaků.

> [!NOTE]
> Operace řetězec a regulární výraz se nerozlišují malá a velká písmena. Můžete také provádět kontrola hodnot Null, pomocí *null* jako konstantu, například user.department - eq *null*.
> Řetězce, který obsahuje uvozovky "by měla být uzavřena" znak, například user.department - eq \`"Prodeje".

## <a name="supported-expression-rule-operators"></a>Operátory pravidel podporované výraz
V následující tabulce jsou uvedeny všechny operátory pravidel podporované výrazu a jejich syntaxi pro použití v těle rozšířené pravidlo:

| Operátor | Syntaxe |
| --- | --- |
| Nerovná se |-ne |
| Rovná se |-eq |
| Nemá na začátku |-notStartsWith |
| Začíná |startsWith – |
| Neobsahuje |-notContains |
| Contains |-obsahuje |
| Neodpovídá |-notMatch |
| Shoda |-odpovídat |
| V | -v |
| Ne v | -notIn |

## <a name="operator-precedence"></a>Priorita operátorů

Všechny operátory jsou uvedeny dole podle priority od dolní na vyšší. Operátory na stejném řádku jsou v stejnou prioritu:
````
-any -all
-or
-and
-not
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
````
Všechny operátory lze používat s nebo bez předpony spojovníkem. Závorky jsou potřeba jenom v případě, že priorita nesplňuje vaše požadavky.
Příklad:
```
   user.department –eq "Marketing" –and user.country –eq "US"
```
je ekvivalentní:
```
   (user.department –eq "Marketing") –and (user.country –eq "US")
```
## <a name="using-the--in-and--notin-operators"></a>Pomocí-v a notIn – operátory

Pokud chcete porovnat hodnotu atributu uživatele s celou řadou různých hodnot můžete použít-v nebo - notIn operátory. Tady je příklad použití / v operátora:
```
   user.department -In ["50001","50002","50003",“50005”,“50006”,“50007”,“50008”,“50016”,“50020”,“50024”,“50038”,“50039”,“51100”]
```
Všimněte si, "[" a "]" na začátku a konce seznamu hodnot. Tato podmínka vyhodnotí jako True, hodnota se rovná user.department jedna z hodnot v seznamu.


## <a name="query-error-remediation"></a>Náprava chyba dotazu
V následující tabulce jsou uvedeny běžné chyby a jak je opravit

| Chyba analýzy dotazu | Chyba využití | Opravené využití |
| --- | --- | --- |
| Chyba: Atribut není podporován. |(user.invalidProperty - eq "Value") |(user.department - eq "value")<br/><br/>Ujistěte se, že se o atribut [podporované seznam vlastností](#supported-properties). |
| Chyba: Operátor není podporován u atributu. |(user.accountEnabled – obsahuje hodnotu true) |(user.accountEnabled - eq true)<br/><br/>Operátor používá není podporován pro tento typ vlastnosti (v tomto příkladu – obsahuje nedá použít u typu boolean). Použijte správnou operátory pro tento typ vlastnosti. |
| Chyba: Chyba při kompilaci dotazu. |1. (user.department - eq "Prodej") (user.department - eq "Marketing")<br/><br/>2. (user.userPrincipalName-odpovídají "*@domain.ext") |1. Chybějící operátor. Pomocí parametru - a - nebo dvě spojení predikátů<br/><br/>(user.department - eq "Prodej")- nebo (user.department - eq "Marketing")<br/><br/>2. Chyba u regulární výraz používaný s parametrem - odpovídá<br/><br/>(user.userPrincipalName-odpovídají ". *@domain.ext"), případně: (user.userPrincipalName-odpovídají "\@domain.ext$")|

## <a name="supported-properties"></a>Podporovaných vlastností
Tady jsou všechny vlastnosti uživatele, které vám v rozšířené pravidlo:

### <a name="properties-of-type-boolean"></a>Vlastnosti typu boolean
Povolené operátory

* -eq
* -ne

| Vlastnosti | Povolené hodnoty | Využití |
| --- | --- | --- |
| accountEnabled |Hodnota TRUE, false |true – eq user.accountEnabled |
| dirSyncEnabled |Hodnota TRUE, false |true – eq user.dirSyncEnabled |

### <a name="properties-of-type-string"></a>Vlastnosti typu řetězec
Povolené operátory

* -eq
* -ne
* -notStartsWith
* StartsWith –
* -obsahuje
* -notContains
* -odpovídat
* -notMatch
* -v
* -notIn

| Vlastnosti | Povolené hodnoty | Využití |
| --- | --- | --- |
| city |Některé řetězcová hodnota nebo *null* |(user.city - eq "value") |
| země |Některé řetězcová hodnota nebo *null* |(user.country - eq "value") |
| Firma | Některé řetězcová hodnota nebo *null* | (user.companyName - eq "value") |
| oddělení |Některé řetězcová hodnota nebo *null* |(user.department - eq "value") |
| displayName |Libovolnou hodnotou řetězce |(user.displayName - eq "value") |
| employeeId |Libovolnou hodnotou řetězce |(user.employeeId - eq "value")<br>(user.employeeId - ne *null*) |
| facsimileTelephoneNumber |Některé řetězcová hodnota nebo *null* |(user.facsimileTelephoneNumber - eq "value") |
| givenName |Některé řetězcová hodnota nebo *null* |(user.givenName - eq "value") |
| pracovní funkce |Některé řetězcová hodnota nebo *null* |(user.jobTitle - eq "value") |
| e-mailu |Některé řetězcová hodnota nebo *null* (adresa SMTP uživatele) |(user.mail - eq "value") |
| mailNickName |Libovolnou hodnotou řetězce (poštovní alias uživatele) |(user.mailNickName - eq "value") |
| mobilní |Některé řetězcová hodnota nebo *null* |(user.mobile - eq "value") |
| ID objektu |Identifikátor GUID objektu uživatele |(user.objectId - eq "11111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | On-premises identifikátor zabezpečení (SID) pro uživatele, kteří byly synchronizované z místní do cloudu. |(user.onPremisesSecurityIdentifier - eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |Žádný DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies - eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Některé řetězcová hodnota nebo *null* |(user.physicalDeliveryOfficeName - eq "value") |
| PSČ |Některé řetězcová hodnota nebo *null* |(user.postalCode - eq "value") |
| preferredLanguage |Kód ISO 639-1 |(user.preferredLanguage - eq "en US") |
| sipProxyAddress |Některé řetězcová hodnota nebo *null* |(user.sipProxyAddress - eq "value") |
| state |Některé řetězcová hodnota nebo *null* |(user.state - eq "value") |
| streetAddress |Některé řetězcová hodnota nebo *null* |(user.streetAddress - eq "value") |
| Příjmení |Některé řetězcová hodnota nebo *null* |(user.surname - eq "value") |
| telephoneNumber |Některé řetězcová hodnota nebo *null* |(user.telephoneNumber - eq "value") |
| Místo využívání |Dvě lettered směrové číslo země |(user.usageLocation - eq "USA") |
| userPrincipalName |Libovolnou hodnotou řetězce |(user.userPrincipalName -eq "alias@domain") |
| userType |člen hosta *null* |(user.userType - eq "Člen") |

### <a name="properties-of-type-string-collection"></a>Vlastnosti kolekce typu řetězec
Povolené operátory

* -obsahuje
* -notContains

| Vlastnosti | Povolené hodnoty | Využití |
| --- | --- | --- |
| otherMails |Libovolnou hodnotou řetězce |(user.otherMails – obsahuje "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses – obsahuje "SMTP: alias@domain") |

## <a name="multi-value-properties"></a>Vícehodnotový vlastnosti
Povolené operátory

* -žádný (splněn pokud alespoň jedna položka v kolekci odpovídá podmínce)
* -všechny (splněn, jestliže splňují podmínku všechny položky v kolekci)

| Vlastnosti | Hodnoty | Využití |
| --- | --- | --- |
| assignedPlans |Každý objekt v kolekci zveřejňuje následující vlastnosti řetězce: capabilityStatus, služby, servicePlanId |user.assignedPlans – všechny (assignedPlan.servicePlanId - eq "efb87545-963c-4e0d-99df-69c6916d9eb0"- a assignedPlan.capabilityStatus - eq "Povoleno") |

Vícehodnotový vlastnosti jsou kolekce objektů stejného typu. Můžete použít – všechny - všechny operátory a použít podmínku pro jednu nebo všechny položky v kolekci, v uvedeném pořadí. Příklad:

assignedPlans je vlastnost více hodnotami, který obsahuje seznam všech plánu služeb přiřazeném pro uživatele. Následující výraz zvolí uživatelé, kteří mají Exchange Online (plán 2), který je také ve stavu Enabled plánu služby:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

(Identifikátor Guid identifikuje plánu služby Exchange Online (plán 2).)

> [!NOTE]
> To je užitečné, pokud chcete určit všichni uživatelé, pro kterého služby Office 365 (nebo jiné služby Microsoft Online Service) povolená funkce například cílit pomocí sady zásad.

Následující výraz vybere všechny uživatele, kteří mají všechny plán služeb, která je přidružená ke službě Intune (identifikovaný podle názvu služby "SCO"):
```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

## <a name="use-of-null-values"></a>Použití hodnoty Null

Chcete-li zadat hodnotu null v pravidle, můžete použít *null* hodnotu. Dejte pozor, abyste použijte uvozovky kolem slovo *null* – Pokud tak učiníte, se bude interpretovat jako hodnotu řetězcového literálu. -Není operátor nelze použít jako srovnávací operátor pro hodnotu null. Když ho používáte, ať už používáte hodnotu null nebo $null dojde k chybě. Místo toho použití - eq nebo - ne. Správný způsob, jak odkazovat na hodnotu null je následujícím způsobem:
```
   user.mail –ne $null
```

## <a name="extension-attributes-and-custom-attributes"></a>Atributy rozšíření a vlastní atributy
Atributy rozšíření a uživatelských atributů, které jsou podporovány v pravidla dynamického členství.

Atributy rozšíření jsou synchronizované z místní Windows Server AD a využijte formát "ExtensionAttributeX", kde X je rovno 1 – 15.
Jedná se například pravidlo, které používá atribut rozšíření
```
(user.extensionAttribute15 -eq "Marketing")
```
Uživatelských atributů, které jsou synchronizované z místní Windows Server AD nebo z připojených aplikací SaaS a formát "user.extension_[GUID]\__ [Attribute]", kde [identifikátor GUID] je jedinečný identifikátor v adresáři AAD pro aplikaci, kterou vytvořili atribut v AAD a [Attribute] je název atributu, který byl vytvořen.
Je například pravidla, která používá vlastní atribut
```
user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  
```
Vlastní název atributu najdete v adresáři zadáním dotazu na atribut pomocí Graph Exploreru a vyhledávání pro název atributu.

## <a name="direct-reports-rule"></a>Pravidlo "Přímé podřízené"
Můžete vytvořit skupina obsahující všechny přímých podřízených manažera. Pokud v budoucnu změnit manažera přímé podřízené skupiny členství automaticky upraven.

> [!NOTE]
> 1. Pravidla pro práci, ujistěte se, že **ID správce** je nastavena správně na uživatele ve vašem tenantovi. Aktuální hodnota pro uživatele můžete zkontrolovat na jejich **kartu profil**.
> 2. Toto pravidlo podporuje pouze **přímé** sestavy. Aktuálně není možné vytvořit skupinu pro vnořené hierarchie; například skupina, která zahrnuje přímé podřízené a jejich zprávy.
> 3. Toto pravidlo nelze kombinovat s jinými pravidly pokročilé.

**Postup konfigurace skupiny**

1. Postupujte podle kroků 1 – 5 z části [vytvořit rozšířené pravidlo](#to-create-the-advanced-rule)a vyberte **typ členství** z **dynamický uživatel**.
2. Na **pravidla dynamického členství** okně zadejte pravidlo s následující syntaxí:

    *Přímí podřízení pro "{objectID_of_manager}"*

    Příkladem platné pravidlo:
```
                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```
    where “62e19b97-8b3d-4d4a-a106-4ce66896a863” is the objectID of the manager. The object ID can be found on manager's **Profile tab**.
3. Po uložení pravidlo, všichni uživatelé se zadanou hodnotou ID správce se přidají do skupiny.

## <a name="using-attributes-to-create-rules-for-device-objects"></a>Používání atributů k vytvoření pravidel pro objekty zařízení
Můžete také vytvořit pravidlo, které vybere objekty zařízení pro členství ve skupině. Můžete použít následující atributy zařízení.

 Atribut zařízení  | Hodnoty | Příklad:
 ----- | ----- | ----------------
 accountEnabled | Hodnota TRUE, false | (device.accountEnabled - eq true)
 displayName | libovolnou hodnotou řetězce |(device.displayName - eq "Rob Iphone")
 deviceOSType | libovolnou hodnotou řetězce | (device.deviceOSType - eq "iPad")- nebo (device.deviceOSType - eq "iPhone")
 deviceOSVersion | libovolnou hodnotou řetězce | (zařízení. OSVersion - eq "9.1")
 deviceCategory | Název kategorie platné zařízení | (device.deviceCategory - eq "BYOD")
 deviceManufacturer | libovolnou hodnotou řetězce | (device.deviceManufacturer - eq "Samsung")
 deviceModel | libovolnou hodnotou řetězce | (device.deviceModel - eq "iPad Air")
 deviceOwnership | Osobní, společnosti, neznámé | (device.deviceOwnership - eq "Company")
 domainName | libovolnou hodnotou řetězce | (device.domainName - eq "contoso.com")
 enrollmentProfileName | Název profilu profil registrace zařízení Apple nebo Windows Autopilot | (device.enrollmentProfileName - eq "DEP Iphony")
 isRooted | Hodnota TRUE, false | (device.isRooted - eq true)
 managementType | MDM (pro mobilní zařízení)<br>PC (pro počítače spravované pomocí agenta Intune pro počítače) | (device.managementType - eq "MDM")
 organizationalUnit | libovolnou hodnotu řetězce odpovídající název organizační jednotky nastavuje v místním Active Directory | (device.organizationalUnit - eq "Počítačů USA")
 deviceId | platné ID zařízení Azure AD | (device.deviceId - eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 ID objektu | ID objektu platný Azure AD |  (device.objectId -eq 76ad43c9-32c5-45e8-a272-7b58b58f596d")



## <a name="changing-dynamic-membership-to-static-and-vice-versa"></a>Změna členství v dynamické na statickou a naopak
Je možné změnit, jak se spravuje členství ve skupině. To je užitečné, pokud chcete zachovat stejný název skupiny a ID sady, tak, aby byly všechny existující odkazy na skupiny stále platná. vytváří se nová skupina bude vyžadovat aktualizaci těchto odkazů.

Aktualizovali jsme Centrum pro správu Azure AD přidat podporu této funkce. Teď zákazníci můžou převést stávající skupiny z dynamického členství na přiřazené členství a naopak přes Centrum pro správu Azure AD nebo rutiny prostředí PowerShell, jak je znázorněno níže.

> [!WARNING]
> Při změně existující skupinu statické na dynamickou skupinu, všech stávajících členů se odebere ze skupiny, a pak se zpracuje pravidlo členství pro přidání nových členů. Skupiny se používá k řízení přístup k aplikacím a prostředkům, původní členy ztratit přístup až do pravidla členství budou plně zpracovány.
>
> Doporučujeme, abyste otestovali pravidla členství v předem abyste měli jistotu, že je nový členství ve skupině podle očekávání.

### <a name="using-azure-ad-admin-center-to-change-membership-management-on-a-group"></a>Chcete-li změnit Správa členství ve skupině pomocí centra pro správu Azure AD 

1. Přihlaste se k [centrum pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který je globální správce nebo správce uživatelských účtů ve vašem tenantovi.
2. Vyberte **skupiny**.
3. Z **všechny skupiny** seznamu, otevřete skupinu, kterou chcete změnit.
4. Vyberte **vlastnosti**.
5. Na **vlastnosti** stránce pro skupinu, vyberte **typ členství** přiřazeno (statické), dynamické uživatele nebo dynamické zařízení, v závislosti na typu požadovaného členství. Pro dynamické členství můžete použít Tvůrce pravidlo a vyberte možnosti pro jednoduché pravidlo nebo rozšířené pravidlo napsat sami. 

Následující postup je příklad změny skupiny ze statické na dynamické členství ve skupině uživatelů. 

1. Na **vlastnosti** stránky pro vybranou skupinu, vyberte **typ členství** z **dynamický uživatel**, vyberte Ano v dialogovém okně s vysvětlením změn do skupiny členství na pokračovat. 
  
   ![Vyberte typ členství v dynamické uživatele](./media/groups-dynamic-membership/select-group-to-convert.png)
  
2. Vyberte **Přidat dynamický dotaz**a pak zadejte pravidlo.
  
   ![Zadejte pravidla](./media/groups-dynamic-membership/enter-rule.png)
  
3. Po vytvoření pravidla, vyberte **přidat dotaz** v dolní části stránky.
4. Vyberte **Uložit** na **vlastnosti** stránce pro skupinu, kterou chcete uložit provedené změny. **Typ členství** skupiny okamžitě aktualizován v seznamu skupin.

> [!TIP]
> Převod skupin může selhat, pokud byl nesprávný rozšířené pravidlo, které jste zadali. Oznámení se zobrazí v pravém dolním rohu portálu, který obsahuje vysvětlení, proč nelze přijmout pravidlo v systému. Přečtěte si ho pečlivě, abyste pochopili, jak můžete nastavit pravidlo, aby byl platný.

### <a name="using-powershell-to-change-membership-management-on-a-group"></a>Chcete-li změnit Správa členství ve skupině pomocí Powershellu

> [!NOTE]
> Chcete-li změnit vlastnosti dynamické skupiny, budete muset použít rutiny z **ve verzi preview** [Azure AD PowerShell verze 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Můžete nainstalovat verzi preview z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

Tady je příklad funkce, které se přepnout na existující skupinu správy členství. V tomto příkladu je správně pracovat vlastnost GroupTypes a zachovat všechny hodnoty, které nesouvisí s dynamického členství věnovat pozornost.

```
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Chcete-li vytvořit statickou skupinu:
```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```
Chcete-li dynamické skupiny:
```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```
## <a name="next-steps"></a>Další postup
Tyto články poskytují další informace o skupinách ve službě Azure Active Directory.

* [Zobrazit existující skupiny](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Vytvoření nové skupiny a přidání členů](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Správa nastavení skupiny](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Správa členství ve skupině](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Správa dynamické pravidel pro uživatele ve skupině](groups-dynamic-membership.md)
