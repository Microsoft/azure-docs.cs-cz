---
title: Pravidla členství v dynamické skupině automatické odkazovat ve službě Azure Active Directory | Dokumentace Microsoftu
description: Jak vytvořit pravidla členství pro skupiny a odkaz se automaticky vyplní.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/20/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: c3121f8b303d9f82ed949d598a942906d0d24f7e
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041019"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>Pravidla dynamického členství pro skupiny ve službě Azure Active Directory

Ve službě Azure Active Directory (Azure AD) můžete vytvořit komplexní pravidla založená na atributu umožňující dynamickým členstvím ve skupinách. Členství v dynamické skupině snižuje administrativní režii potřebnou ke přidávání a odebírání uživatelů. Tento článek podrobně popisuje vlastnosti a syntaxe pro vytvoření pravidla dynamického členství pro uživatele nebo zařízení. Pravidlo pro dynamické členství můžete nastavit pro skupiny zabezpečení nebo pro skupiny Office 365.

Když se změní libovolné atributy uživatele nebo zařízení, systém vyhodnotí všechna dynamická pravidla skupin v adresáři a zjistěte, jestli tato změna se aktivuje všechny skupiny přidá nebo odebere. Pokud uživatel nebo zařízení splňuje pravidlo pro skupinu, se přidají jako člena této skupiny. Pokud již uživatel pravidlo, se odeberou. Nemůžete ručně přidat nebo odebrat členem dynamické skupiny.

* Můžete vytvořit dynamické skupiny zařízení nebo uživatelů, ale nelze vytvořit pravidlo, které obsahuje uživatele a zařízení.
* Nelze vytvořit skupinu zařízení na základě atributů zařízení vlastníků. Pravidla členství zařízení mohou odkazovat pouze na atributy zařízení.

> [!NOTE]
> Tato funkce vyžaduje licenci Azure AD Premium P1 pro každý jedinečný uživatel, který je členem jedné nebo více dynamických skupin. Není nutné k přiřazení licencí uživatelům pro ně být členem dynamické skupiny, ale minimální počet licencí musí mít v tenantovi k pokrytí všech těchto uživatelů. Například pokud máte celkem 1 000 jedinečných uživatelů v všech dynamických skupin ve vašem tenantovi, je třeba alespoň 1000 licencí pro Azure AD Premium P1 pro splnění požadavcích na licence.
>

## <a name="constructing-the-body-of-a-membership-rule"></a>Vytváření tělo pravidla členství

Pravidlo členství, které naplňuje skupinu uživatelů nebo zařízení automaticky se binární výraz, jehož výsledkem výsledek true nebo false. Jsou tři části jednoduché pravidlo:

* Vlastnost
* Operátor
* Hodnota

Pořadí částí v rámci výrazu jsou důležité k zamezení chyby syntaxe.

### <a name="rules-with-a-single-expression"></a>Pravidla s jeden výraz

Jeden výraz je nejjednodušší forma pravidla členství a pouze má tři části uvedených výše. Pravidlo s jeden výraz vypadá podobně jako tento: `Property Operator Value`, kde je název objekt.vlastnost syntaxe pro vlastnost.

Následuje příklad pravidla správně členství s jeden výraz:

```
user.department -eq "Sales"
```

Závorky jsou nepovinné pro jeden výraz. Celková délka obsahu pravidla členství může mít maximálně 2048 znaků.

## <a name="supported-properties"></a>Podporovaných vlastností

Existují tři typy vlastností, které lze použít k vytvoření pravidla členství.

* Logická hodnota
* Řetězec
* Kolekce řetězců

Toto jsou vlastnosti uživatele, které vám umožní vytvořit jeden výraz.

### <a name="properties-of-type-boolean"></a>Vlastnosti typu boolean

| Vlastnosti | Povolené hodnoty | Využití |
| --- | --- | --- |
| accountEnabled |Hodnota TRUE, false |true – eq user.accountEnabled |
| dirSyncEnabled |Hodnota TRUE, false |true – eq user.dirSyncEnabled |

### <a name="properties-of-type-string"></a>Vlastnosti typu řetězec

| Vlastnosti | Povolené hodnoty | Využití |
| --- | --- | --- |
| city |Některé řetězcová hodnota nebo *null* |(user.city - eq "value") |
| Země |Některé řetězcová hodnota nebo *null* |(user.country - eq "value") |
| Firma | Některé řetězcová hodnota nebo *null* | (user.companyName - eq "value") |
| Oddělení |Některé řetězcová hodnota nebo *null* |(user.department - eq "value") |
| displayName |Libovolnou hodnotou řetězce |(user.displayName - eq "value") |
| employeeId |Libovolnou hodnotou řetězce |(user.employeeId - eq "value")<br>(user.employeeId - ne *null*) |
| facsimileTelephoneNumber |Některé řetězcová hodnota nebo *null* |(user.facsimileTelephoneNumber - eq "value") |
| givenName |Některé řetězcová hodnota nebo *null* |(user.givenName - eq "value") |
| pracovní funkce |Některé řetězcová hodnota nebo *null* |(user.jobTitle - eq "value") |
| e-mailu |Některé řetězcová hodnota nebo *null* (adresa SMTP uživatele) |(user.mail - eq "value") |
| mailNickName |Libovolnou hodnotou řetězce (poštovní alias uživatele) |(user.mailNickName - eq "value") |
| Mobilní zařízení |Některé řetězcová hodnota nebo *null* |(user.mobile - eq "value") |
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
| userPrincipalName (Hlavní název uživatele) |Libovolnou hodnotou řetězce |(user.userPrincipalName -eq "alias@domain") |
| UserType |člen hosta *null* |(user.userType - eq "Člen") |

### <a name="properties-of-type-string-collection"></a>Vlastnosti kolekce typu řetězec

| Vlastnosti | Povolené hodnoty | Využití |
| --- | --- | --- |
| otherMails |Libovolnou hodnotou řetězce |(user.otherMails – obsahuje "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses – obsahuje "SMTP: alias@domain") |

Vlastnosti používané pro pravidla zařízení, najdete v části [pravidla pro zařízení](#rules-for-devices).

## <a name="supported-operators"></a>Podporované operátory

V následující tabulce jsou uvedeny všechny podporované operátory a jejich syntaxi pro jeden výraz. Operátory lze používat s nebo bez předpony spojovníku (-).

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

### <a name="using-the--in-and--notin-operators"></a>Pomocí-v a notIn – operátory

Pokud chcete porovnat hodnotu atributu uživatele s celou řadou různých hodnot můžete použít-v nebo - notIn operátory. Použít závorky symboly "[" a "]" begin a end seznam hodnot.

 V následujícím příkladu se výraz vyhodnotí jako true Pokud je hodnota user.department jakákoliv hodnota v seznamu:

```
   user.department -In ["50001","50002","50003",“50005”,“50006”,“50007”,“50008”,“50016”,“50020”,“50024”,“50038”,“50039”,“51100”]
```

## <a name="supported-values"></a>Podporované hodnoty

Hodnoty použité ve výrazu se může skládat z několika typů, včetně:

* Řetězce
* Boolean – true, false
* Čísla
* Pole – číselné pole, pole řetězců

Při zadání hodnoty v rámci výrazu je potřeba použít správnou syntaxi. aby nedocházelo k chybám. Jsou některé tipy syntaxi:

* Dvojité uvozovky jsou volitelné, pokud hodnota není řetězec.
* Operace řetězec a regulární výraz se nerozlišují malá a velká písmena.
* Řetězcová hodnota obsahuje uvozovky, obě nabídky by měla být uzavřena \` znak, například user.department - eq \`"Sales\`" je lze patřičnou syntaxi, pokud je hodnota "Prodeje".
* Můžete také provádět kontrola hodnot Null, pomocí null jako hodnotu, například `user.department -eq null`.

### <a name="use-of-null-values"></a>Použití hodnoty Null

Chcete-li zadat hodnotu null v pravidle, můžete použít *null* hodnotu. 

* Použití - eq nebo - ne při porovnávání *null* hodnotu ve výrazu.
* Použijte uvozovky kolem slovo *null* pouze v případě, že chcete, aby interpretovat jako hodnotu řetězcového literálu.
* -Není operátor nelze použít jako srovnávací operátor pro hodnotu null. Když ho používáte, ať už používáte hodnotu null nebo $null dojde k chybě.

Správný způsob, jak odkazovat na hodnotu null je následujícím způsobem:

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>Pravidla s více výrazy

Pravidla členství ve skupině se může skládat z více než jeden výraz jedné připojené prostřednictvím- a- nebo a – nejsou logické operátory. Logické operátory lze také použít v kombinaci. 

Následují příklady pravidel členství správně s více výrazy:

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>Priorita operátorů

Všechny operátory jsou uvedeny níže v pořadí podle priority od nejvyšší k nejnižší. Operátory na stejném řádku mají stejnou prioritu:

```
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
-not
-and
-or
-any -all
```

Následuje příklad priorita operátorů kde dvou výrazů jsou vyhodnocujeme, jestli uživatel:

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

Závorky jsou potřeba jenom v případě, že priorita nesplňuje vaše požadavky. Pokud chcete, aby oddělení vyčíslen první, následujícím příkladu je použití závorek k určení pořadí:

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>Pravidla s složité výrazy

Pravidla členství se může skládat z kde provádět vlastnosti, operátory a hodnoty na složitější formulářů složité výrazy. Výrazy jsou považovány za komplexní, když je splněna některá z následujících akcí:

* Vlastnost se skládá z kolekce hodnot. Konkrétně více Vážíme si toho vlastnosti
* Použití výrazů-všechny a - všechny operátory
* Vlastní hodnota výrazu může být jeden nebo více výrazů

## <a name="multi-value-properties"></a>Vícehodnotový vlastnosti

Vícehodnotový vlastnosti jsou kolekce objektů stejného typu. Slouží k vytvoření pravidla členství pomocí-všechny a - všechny logické operátory.

| Vlastnosti | Hodnoty | Využití |
| --- | --- | --- |
| assignedPlans | Každý objekt v kolekci zveřejňuje následující vlastnosti řetězce: capabilityStatus, služby, servicePlanId |user.assignedPlans – všechny (assignedPlan.servicePlanId - eq "efb87545-963c-4e0d-99df-69c6916d9eb0"- a assignedPlan.capabilityStatus - eq "Povoleno") |
| proxyAddresses| SMTP: alias@domain smtp: alias@domain | (user.proxyAddresses – všechny (\_ – obsahuje "contoso")) |

### <a name="using-the--any-and--all-operators"></a>Pomocí-všechny a - všechny operátory

Můžete použít – všechny - všechny operátory a použít podmínku pro jednu nebo všechny položky v kolekci, v uvedeném pořadí.

* -žádný (splněn pokud alespoň jedna položka v kolekci odpovídá podmínce)
* -všechny (splněn, jestliže splňují podmínku všechny položky v kolekci)

#### <a name="example-1"></a>Příklad 1

assignedPlans je vlastnost více hodnotami, který obsahuje seznam všech plánu služeb přiřazeném pro uživatele. Následující výraz zvolí uživatelé, kteří mají Exchange Online (plán 2) plán služeb (jako hodnota identifikátoru GUID), který je také ve stavu Enabled:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

Do skupiny všichni uživatelé, pro kterého je možné pravidla, jako je například tento služby Office 365 (nebo jiné služby Microsoft Online Service) je funkce povolená. Můžete pak použít pomocí sady zásad do skupiny.

#### <a name="example-2"></a>Příklad 2

Následující výraz vybere všechny uživatele, kteří mají všechny plán služeb, která je přidružená ke službě Intune (identifikovaný podle názvu služby "SCO"):

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

### <a name="using-the-underscore--syntax"></a>Pomocí podtržítka (\_) syntaxe

Podtržítko (\_) syntaxe odpovídá výskytů určité hodnoty v jednom z vlastnosti kolekce s více hodnotami řetězce pro přidání uživatelů nebo zařízení do dynamické skupiny. Se použije s parametrem- nebo - všemi operátory.

Tady je příklad použití podtržítko (\_) v pravidlu pro přidání členů podle user.proxyAddress (to funguje stejně v případě user.otherMails). Toto pravidlo se přidá každý uživatel s adresa proxy serveru, který obsahuje "contoso" do skupiny.

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>Další vlastnosti a běžných pravidel

### <a name="create-a-direct-reports-rule"></a>Vytvořit pravidlo "Přímé podřízené"

Můžete vytvořit skupina obsahující všechny přímých podřízených manažera. Při v budoucnu změnit manažera přímé podřízené skupiny členství se upraví automaticky.

Přímí podřízení pravidlo je vytvořená pomocí následující syntaxe:

```
Direct Reports for "{objectID_of_manager}"
```

Tady je příklad platná pravidla, kde "62e19b97-8b3d-4d4a-a106-4ce66896a863" je objectID sady manager:

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

Následující tipy vám umožňují použít pravidlo správně.

* **ID správce** je ID objektu správce. Nachází se v nástroji Správce **profilu**.
* Pravidla pro práci, ujistěte se, že **správce** vlastnosti jsou správně nastavena pro uživatele ve vašem tenantovi. Můžete zkontrolovat aktuální hodnotu v uživatele **profilu**.
* Toto pravidlo podporuje pouze přímé podřízené manažera. Jinými slovy, nelze vytvořit skupinu s manažera přímé podřízené *a* své sestavy.
* Toto pravidlo nelze kombinovat s jinými pravidly členství.

### <a name="create-an-all-users-rule"></a>Vytvoření pravidla "Všichni uživatelé"

Můžete vytvořit skupinu obsahující všechny uživatele v tenantovi pomocí pravidla členství. Při přidávání nebo v budoucnu odebere z tenanta uživatelů, skupiny členství se automaticky upraví.

Pravidlo "Všechny uživatele" je vytvořen pomocí jediný výraz, ne – operátor a hodnotu null. Toto pravidlo přidává ke skupině uživatelů typu Host B2B, stejně jako členské uživatele.

```
user.objectid -ne null
```

### <a name="create-an-all-devices-rule"></a>Vytvoření pravidla "Všechna zařízení"

Můžete vytvořit skupina, která obsahuje všechna zařízení v rámci tenanta pomocí pravidla členství. Při přidávání nebo v budoucnu odebere z klienta zařízení se automaticky upraví členství skupiny.

Pravidlo "Všechna zařízení" je vytvořen pomocí jediný výraz, ne – operátor a hodnotu null:

```
device.objectid -ne null
```

### <a name="extension-properties-and-custom-extension-properties"></a>Rozšíření vlastností a vlastností vlastního rozšíření

Atributy rozšíření a vlastní extenson vlastnosti jsou podporovány jako řetězec vlastnosti pravidla dynamického členství. Atributy rozšíření jsou synchronizované z místní Windows Server AD a využijte formát "ExtensionAttributeX", kde X je rovno 1 – 15. Tady je příklad pravidla, která používá jako vlastnost atributu rozšíření:

```
(user.extensionAttribute15 -eq "Marketing")
```

Vlastnosti vlastní rozšíření jsou synchronizované z místní Windows Server AD nebo z připojených aplikací SaaS a jsou formátu `user.extension_[GUID]__[Attribute]`, kde:

* [Identifikátor GUID] je jedinečný identifikátor v adresáři AAD aplikace, který vytvořil vlastnost ve službě Azure AD
* [Attribute] je název vlastnosti, protože byla vytvořena

Je například pravidlo, které používá rozšíření vlastních vlastností:

```
user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber -eq "123"
```

Název vlastní vlastnosti najdete v adresáři zadáním dotazu na vlastnosti pomocí Graph Exploreru a vyhledávání pro název vlastnosti.

## <a name="rules-for-devices"></a>Pravidla pro zařízení

Můžete také vytvořit pravidlo, které vybere objekty zařízení pro členství ve skupině. Uživatelé a zařízení nemůže mít jako členy skupiny. Můžete použít následující atributy zařízení.

 Atribut zařízení  | Hodnoty | Příklad:
 ----- | ----- | ----------------
 accountEnabled | Hodnota TRUE, false | (device.accountEnabled - eq true)
 displayName | Libovolnou hodnotou řetězce |(device.displayName - eq "Rob Iphone")
 deviceOSType | Libovolnou hodnotou řetězce | (device.deviceOSType - eq "iPad")- nebo (device.deviceOSType - eq "iPhone")
 deviceOSVersion | Libovolnou hodnotou řetězce | (zařízení. OSVersion - eq "9.1")
 deviceCategory | Název kategorie platné zařízení | (device.deviceCategory - eq "BYOD")
 deviceManufacturer | Libovolnou hodnotou řetězce | (device.deviceManufacturer - eq "Samsung")
 deviceModel | Libovolnou hodnotou řetězce | (device.deviceModel - eq "iPad Air")
 deviceOwnership | Osobní, společnosti, neznámé | (device.deviceOwnership - eq "Company")
 domainName | Libovolnou hodnotou řetězce | (device.domainName - eq "contoso.com")
 enrollmentProfileName | Název profilu profil registrace zařízení Apple nebo Windows Autopilot | (device.enrollmentProfileName - eq "DEP Iphony")
 isRooted | Hodnota TRUE, false | (device.isRooted - eq true)
 managementType | MDM (pro mobilní zařízení)<br>PC (pro počítače spravované pomocí agenta Intune pro počítače) | (device.managementType - eq "MDM")
 organizationalUnit | libovolnou hodnotu řetězce odpovídající název organizační jednotky nastavuje v místním Active Directory | (device.organizationalUnit - eq "Počítačů USA")
 deviceId | platné ID zařízení Azure AD | (device.deviceId - eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 ID objektu | ID objektu platný Azure AD |  (device.objectId -eq 76ad43c9-32c5-45e8-a272-7b58b58f596d")

## <a name="next-steps"></a>Další postup

Tyto články poskytují další informace o skupinách ve službě Azure Active Directory.

* [Zobrazení existujících skupin](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Vytvoření nové skupiny a přidání členů](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Správa nastavení skupiny](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Správa členství ve skupině](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Správa dynamických pravidel pro uživatele ve skupině](groups-dynamic-membership.md)
