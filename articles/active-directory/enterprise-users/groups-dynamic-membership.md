---
title: Pravidla pro dynamicky vyplněné členství ve skupinách – Azure AD | Microsoft Docs
description: Jak vytvořit pravidla členství pro automatické naplňování skupin a odkaz na pravidlo.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf5a06cf906084e3f87d5f56748476a26587ff17
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2020
ms.locfileid: "95490727"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>Pravidla dynamického členství pro skupiny v Azure Active Directory

V Azure Active Directory (Azure AD) můžete vytvářet složitá pravidla založená na atributech, která umožní dynamické členství pro skupiny. Dynamické členství ve skupinách snižuje administrativní režii při přidávání a odebírání uživatelů. Tento článek podrobně popisuje vlastnosti a syntaxi pro vytváření pravidel dynamického členství pro uživatele nebo zařízení. Můžete nastavit pravidlo pro dynamické členství ve skupinách zabezpečení nebo Microsoft 365ch skupinách.

Když se změní kterýkoli atribut uživatele nebo zařízení, systém vyhodnotí všechna dynamická pravidla skupiny v adresáři, aby zjistil, jestli by změna aktivovala nebo odebrala nějakou skupinu. Pokud uživatel nebo zařízení splňuje pravidlo pro skupinu, přidají se jako členové této skupiny. Pokud už pravidla nevyhovují, odeberou se. Nemůžete ručně přidat nebo odebrat člena dynamické skupiny.

- Můžete vytvořit dynamickou skupinu pro zařízení nebo pro uživatele, ale nemůžete vytvořit pravidlo, které bude obsahovat uživatele i zařízení.
- Nemůžete vytvořit skupinu zařízení na základě atributů vlastníků zařízení. Pravidla členství v zařízeních můžou odkazovat jenom na atributy zařízení.

> [!NOTE]
> Tato funkce vyžaduje licenci Azure AD Premium P1 pro každého jedinečného uživatele, který je členem jedné nebo více dynamických skupin. Nemusíte přiřazovat licence uživatelům, aby byli členy dynamických skupin, ale musíte mít minimální počet licencí v organizaci Azure AD, abyste pokryli všechny takové uživatele. Pokud byste například měli celkem 1 000 jedinečných uživatelů ve všech dynamických skupinách ve vaší organizaci, budete potřebovat alespoň 1 000 licencí Azure AD Premium P1, aby splnily požadavky na licenci.
> Pro zařízení, která jsou členem dynamické skupiny zařízení, není nutná žádná licence.

## <a name="rule-builder-in-the-azure-portal"></a>Tvůrce pravidel v Azure Portal

Azure AD poskytuje tvůrci pravidel pro rychlejší vytváření a aktualizaci důležitých pravidel. Tvůrce pravidel podporuje konstrukci až pěti výrazů. Tvůrce pravidel usnadňuje vytvoření pravidla s několika jednoduchými výrazy, ale nelze ho použít k reprodukování všech pravidel. Pokud tvůrce pravidel nepodporuje pravidlo, které chcete vytvořit, můžete použít textové pole.

Tady jsou některé příklady pokročilých pravidel nebo syntaxe, pro které doporučujeme sestavit pomocí textového pole:

- Pravidlo s více než pěti výrazy
- Pravidlo přímých sestav
- Nastavení [priority operátoru](#operator-precedence)
- [Pravidla se složitými výrazy](#rules-with-complex-expressions); například `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Tvůrce pravidel nemusí být schopný zobrazit některá pravidla vytvořená v textovém poli. Když tvůrce pravidel nemůže zobrazit pravidlo, může se zobrazit zpráva. Tvůrce pravidel nemění podporovanou syntaxi, ověřování ani zpracování pravidel dynamických skupin jakýmkoli způsobem.

Další podrobné pokyny najdete v tématu [Vytvoření nebo aktualizace dynamické skupiny](groups-create-rule.md).

![Přidat pravidlo členství pro dynamickou skupinu](./media/groups-dynamic-membership/update-dynamic-group-rule.png)

### <a name="rule-syntax-for-a-single-expression"></a>Syntaxe pravidla pro jeden výraz

Jediným výrazem je nejjednodušší forma pravidla členství a má jenom tři části uvedené výše. Pravidlo s jedním výrazem vypadá podobně jako v tomto příkladu: `Property Operator Value` , kde syntaxe pro vlastnost je název Object. Property.

Následuje příklad správného vytvořeného pravidla členství s jedním výrazem:

```
user.department -eq "Sales"
```

Kulaté závorky jsou volitelné pro jeden výraz. Celková délka těla pravidla členství nesmí překročit 2048 znaků.

## <a name="constructing-the-body-of-a-membership-rule"></a>Sestavování těla pravidla členství

Pravidlo členství, které automaticky naplní skupinu uživateli nebo zařízeními, je binární výraz, jehož výsledkem je výsledek true nebo false. Mezi tři části jednoduchého pravidla patří:

- Vlastnost
- Operátor
- Hodnota

Pořadí částí v rámci výrazu je důležité, aby nedocházelo k chybám syntaxe.

## <a name="supported-properties"></a>Podporované vlastnosti

Existují tři typy vlastností, které lze použít k vytvoření pravidla členství.

- Logická hodnota
- Řetězec
- Kolekce řetězců

Níže jsou uvedené vlastnosti uživatele, které můžete použít k vytvoření jednoho výrazu.

### <a name="properties-of-type-boolean"></a>Vlastnosti typu Boolean

| Vlastnosti | Povolené hodnoty | Využití |
| --- | --- | --- |
| accountEnabled |true false |User. accountEnabled-EQ true |
| Nastavení dirsyncenabled |true false |User. nastavení dirsyncenabled-EQ true |

### <a name="properties-of-type-string"></a>Vlastnosti typu String

| Vlastnosti | Povolené hodnoty | Využití |
| --- | --- | --- |
| city |Libovolná hodnota řetězce nebo hodnota *null* |(User. City-EQ "hodnota") |
| country |Libovolná hodnota řetězce nebo hodnota *null* |(User. Country-EQ "value") |
| Společnosti | Libovolná hodnota řetězce nebo hodnota *null* | (User. companyName-EQ "value") |
| Oddělení |Libovolná hodnota řetězce nebo hodnota *null* |(User. Department-EQ "hodnota") |
| displayName |Libovolná hodnota řetězce |(User. DisplayName-EQ "value") |
| Zaměstnance |Libovolná hodnota řetězce |(User. employeeId-EQ "value")<br>(User. ČísloZaměstnance-ne *null*) |
| facsimileTelephoneNumber |Libovolná hodnota řetězce nebo hodnota *null* |(User. facsimileTelephoneNumber-EQ "value") |
| givenName |Libovolná hodnota řetězce nebo hodnota *null* |(User. Value-EQ "value") |
| jobTitle |Libovolná hodnota řetězce nebo hodnota *null* |(User. jobTitle-EQ "value") |
| pošta |Libovolná hodnota řetězce nebo hodnota *null* (adresa SMTP uživatele) |(User. mail-EQ "value") |
| mailNickName |Libovolná hodnota řetězce (e-mailový alias uživatele) |(User. mailNickName-EQ "value") |
| mobil |Libovolná hodnota řetězce nebo hodnota *null* |(User. Mobile-EQ "value") |
| Objektu |Identifikátor GUID objektu uživatele |(User. objectId-EQ "11111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | Místní identifikátor zabezpečení (SID) pro uživatele, kteří byli synchronizováni z místního prostředí do cloudu. |(User. onPremisesSecurityIdentifier-EQ "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |Žádné DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(User. passwordPolicies-EQ "DisableStrongPassword") |
| physicalDeliveryOfficeName |Libovolná hodnota řetězce nebo hodnota *null* |(User. physicalDeliveryOfficeName-EQ "value") |
| Ovládacím |Libovolná hodnota řetězce nebo hodnota *null* |(User. postalCode-EQ "hodnota") |
| preferredLanguage |Kód ISO 639-1 |(User. preferredLanguage-EQ "en-US") |
| sipProxyAddress |Libovolná hodnota řetězce nebo hodnota *null* |(User. sipProxyAddress-EQ "value") |
| state |Libovolná hodnota řetězce nebo hodnota *null* |(User. State-EQ "value") |
| streetAddress |Libovolná hodnota řetězce nebo hodnota *null* |(User. streetAddress-EQ "value") |
| surname |Libovolná hodnota řetězce nebo hodnota *null* |(User. příjmení-EQ "hodnota") |
| telephoneNumber |Libovolná hodnota řetězce nebo hodnota *null* |(User. telephoneNumber-EQ "value") |
| usageLocation |Dva směrové číslo země/oblasti |(User. usageLocation-EQ "US") |
| userPrincipalName (Hlavní název uživatele) |Libovolná hodnota řetězce |(User. userPrincipalName-EQ " alias@domain ") |
| userType |člen typu host *null* |(User. userType-EQ "Member") |

### <a name="properties-of-type-string-collection"></a>Vlastnosti kolekce řetězců typu

| Vlastnosti | Povolené hodnoty | Využití |
| --- | --- | --- |
| otherMails |Libovolná hodnota řetězce |(User. otherMails-Contains " alias@domain ") |
| proxyAddresses |SMTP: alias@domain SMTP: alias@domain |(User. proxyAddresses-obsahuje "SMTP: alias@domain ") |

Vlastnosti používané pro pravidla zařízení najdete v tématu [pravidla pro zařízení](#rules-for-devices).

## <a name="supported-expression-operators"></a>Podporované operátory výrazů

V následující tabulce jsou uvedeny všechny podporované operátory a jejich syntaxe pro jeden výraz. Operátory lze použít s předponou spojovníku (-) nebo bez ní.

| Operátor | Syntaxe |
| --- | --- |
| Nerovná se |-Ne |
| Je rovno |– EQ |
| Nezačíná na |-notStartsWith |
| Začíná na |– startsWith |
| Neobsahuje |-notContains |
| Contains |-obsahuje |
| Neodpovídá |-notMatch |
| Shoda |– shoda |
| V | -in |
| Není v | -notIn |

### <a name="using-the--in-and--notin-operators"></a>Používání operátorů-in a-notIn

Pokud chcete porovnat hodnotu atributu uživatele s řadou různých hodnot, můžete použít operátory-in nebo-notIn. K zahájení a ukončení seznamu hodnot použijte symboly závorek "[" a "]".

 V následujícím příkladu se výraz vyhodnotí jako true, pokud se hodnota User. Department rovná libovolné hodnotě v seznamu:

```
   user.department -in ["50001","50002","50003","50005","50006","50007","50008","50016","50020","50024","50038","50039","51100"]
```


### <a name="using-the--match-operator"></a>Použití operátoru-Match 
Operátor **-Match** se používá pro porovnávání libovolného regulárního výrazu. Příklady:

```
user.displayName -match "Da.*"   
```
Hodnota da, dav, David vyhodnocena jako true, hodnota aDa je vyhodnocena jako false.

```
user.displayName -match ".*vid"
```
David se vyhodnotí jako true, da se vyhodnotí jako false.

## <a name="supported-values"></a>Podporované hodnoty

Hodnoty použité ve výrazu mohou sestávat z několika typů, včetně:

* Řetězce
* Logická hodnota – true, false
* Čísla
* Pole – číselné pole, pole řetězců

Při zadávání hodnoty v rámci výrazu je důležité použít správnou syntaxi, aby nedocházelo k chybám. Zde jsou některé tipy syntaxe:

* Dvojité uvozovky jsou volitelné, pokud hodnota není řetězec.
* Operace s řetězci String a Regex nerozlišují velká a malá písmena.
* Pokud řetězcová hodnota obsahuje dvojité uvozovky, obě nabídky by měly být uvozeny \` znakem, například User. Department-EQ \` "Sales \` ", jedná se o správnou syntaxi, pokud je hodnota "Sales".
* Můžete také provádět kontroly null a jako hodnotu použít null, například `user.department -eq null` .

### <a name="use-of-null-values"></a>Použití hodnot null

Chcete-li v pravidle zadat hodnotu null, můžete použít hodnotu *null* . 

* Při porovnávání hodnoty *null* ve výrazu použijte hodnotu-EQ nebo-ne.
* Použijte uvozovky kolem slova *null* pouze v případě, že je chcete interpretovat jako řetězcovou hodnotu literálu.
* Operátor-NOT nelze použít jako srovnávací operátor pro hodnotu null. Pokud ho použijete, zobrazí se chyba, ať už použijete hodnotu null nebo $null.

Správný způsob, jak odkazovat na hodnotu null, je následující:

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>Pravidla s více výrazy

Pravidlo členství ve skupině se může skládat z více než jednoho jednoho výrazu připojeného logickými operátory-a,-nebo a not. Logické operátory lze také použít v kombinaci. 

Následují příklady správně konstruovaných pravidel členství s více výrazy:

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>Priorita operátorů

Všechny operátory jsou uvedeny níže v pořadí podle priority od nejvyšší po nejnižší. Operátory na stejném řádku mají stejnou přednost:

```
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
-not
-and
-or
-any -all
```

Následuje příklad přednosti operátoru, kdy je pro uživatele vyhodnocovány dva výrazy:

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

Závorky jsou nutné pouze v případě, že priorita nesplňuje vaše požadavky. Například pokud chcete, aby oddělení vyhodnotilo jako první, následující ukazuje, jak lze pomocí závorek určit pořadí:

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>Pravidla se složitými výrazy

Pravidlo členství se může skládat ze složitých výrazů, kde vlastnosti, operátory a hodnoty přebírají složitější formy. Výrazy jsou považovány za složité, pokud jsou splněny některé z následujících hodnot:

* Vlastnost se skládá z kolekce hodnot; konkrétně vlastnosti s více hodnotami
* Výrazy používají operátory-any a-All.
* Hodnota výrazu může být jeden nebo více výrazů.

## <a name="multi-value-properties"></a>Vlastnosti s více hodnotami

Vlastnosti s více hodnotami jsou kolekce objektů stejného typu. Je možné je použít k vytvoření pravidel členství pomocí logických operátorů-any a-All.

| Vlastnosti | Hodnoty | Využití |
| --- | --- | --- |
| assignedPlans | Každý objekt v kolekci zpřístupňuje následující řetězcové vlastnosti: capabilityStatus, Service, servicePlanId |User. assignedPlans-any (assignedPlan. servicePlanId-any (.-EQ "efb87545-963c-4e0d-99df-69c6916d9eb0"-a assignedPlan. capabilityStatus-EQ "Enabled") |
| proxyAddresses| SMTP: alias@domain SMTP: alias@domain | (User. proxyAddresses-any ( \_ -obsahuje "contoso")) |

### <a name="using-the--any-and--all-operators"></a>Použití operátorů-any a-All

K použití podmínky pro jednu nebo všechny položky v kolekci můžete použít operátory-any a-All.

* -Any (splněno, když alespoň jedna položka v kolekci odpovídá podmínce)
* -All (splněné, když se všechny položky v kolekci shodují s podmínkou)

#### <a name="example-1"></a>Příklad 1

assignedPlans je vlastnost s více hodnotami, která obsahuje seznam všech plánů služeb přiřazených uživateli. Následující výraz vybere uživatele, kteří mají plán služby Exchange Online (plán 2) (jako hodnotu GUID), který je také v povoleném stavu:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

Pravidlo, jako je tato vlastnost, lze použít k seskupení všech uživatelů, pro které je povolena funkce Microsoft 365 (nebo jiné online služby Microsoftu). Pro skupinu pak můžete použít sadu zásad.

#### <a name="example-2"></a>Příklad 2

Následující výraz vybere všechny uživatele, kteří mají libovolný plán služby, který je přidružený ke službě Intune (identifikovaný názvem služby SCO):

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

### <a name="using-the-underscore-_-syntax"></a>Použití syntaxe podtržítka ( \_ )

Syntaxe podtržítka ( \_ ) se shoduje s výskytem konkrétní hodnoty v jedné z vlastností kolekce řetězců s více hodnotami, aby bylo možné přidat uživatele nebo zařízení do dynamické skupiny. Používá se u operátorů-any nebo-ALL.

Tady je příklad použití podtržítka ( \_ ) v pravidle pro přidání členů na základě User. ProxyAddress (to funguje stejně pro User. otherMails). Toto pravidlo přidá libovolného uživatele s adresou proxy, který ve skupině obsahuje "contoso".

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>Další vlastnosti a společná pravidla

### <a name="create-a-direct-reports-rule"></a>Vytvoření pravidla "přímé sestavy"

Můžete vytvořit skupinu obsahující všechny přímé sestavy manažera. Když se v budoucnu změní přímá sestavování portálu, členství ve skupině se upraví automaticky.

Pravidlo přímých sestav je vytvořené pomocí následující syntaxe:

```
Direct Reports for "{objectID_of_manager}"
```

Tady je příklad platného pravidla, kde "62e19b97-8b3d-4d4a-A106-4ce66896a863" je objectID správce:

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

Následující tipy vám pomůžou pravidlo používat správně.

- **ID správce** je ID objektu správce. Najdete ho v **profilu** správce.
- Aby pravidlo fungovalo, ujistěte se, že je vlastnost **Manager** nastavena správně pro uživatele ve vaší organizaci. Aktuální hodnotu můžete ověřit v **profilu** uživatele.
- Toto pravidlo podporuje pouze přímé sestavy vedoucího správce. Jinými slovy, nemůžete vytvořit skupinu s přímými sestavami manažera *a* jejich sestavami.
- Toto pravidlo nelze kombinovat s jinými pravidly členství.

### <a name="create-an-all-users-rule"></a>Vytvoří pravidlo pro všechny uživatele.

Můžete vytvořit skupinu obsahující všechny uživatele v rámci organizace s použitím pravidla členství. Když se uživatelé v budoucnu přidají nebo odeberou z organizace, členství ve skupině se upraví automaticky.

Pravidlo "Všichni uživatelé" je tvořeno pomocí jednoduchého výrazu s použitím operátoru-ne a hodnoty null. Toto pravidlo přidá uživatele typu Guest B2B i členské uživatele do skupiny.

```
user.objectId -ne null
```
Pokud chcete, aby skupina vyloučila uživatele typu Host a zahrnovala pouze členy vaší organizace, můžete použít následující syntaxi:

```
(user.objectId -ne null) -and (user.userType -eq "Member")
```

### <a name="create-an-all-devices-rule"></a>Vytvořit pravidlo pro všechna zařízení

Můžete vytvořit skupinu obsahující všechna zařízení v organizaci pomocí pravidla členství. Když se zařízení v budoucnu přidají nebo odeberou z organizace, členství ve skupině se automaticky upraví.

Pravidlo všechna zařízení je vytvořené pomocí jednoho výrazu s použitím operátoru-ne a hodnoty null:

```
device.objectId -ne null
```

## <a name="extension-properties-and-custom-extension-properties"></a>Vlastnosti rozšíření a vlastnosti vlastního rozšíření

Atributy rozšíření a vlastnosti vlastního rozšíření jsou podporovány jako vlastnosti řetězce v dynamických pravidlech členství. [Atributy rozšíření](/graph/api/resources/onpremisesextensionattributes?view=graph-rest-1.0) se synchronizují z místního systému Windows Server AD a převezmou formát "ExtensionAttributeX", kde X se rovná 1-15. Tady je příklad pravidla, které používá atribut rozšíření jako vlastnost:

```
(user.extensionAttribute15 -eq "Marketing")
```

[Vlastní vlastnosti rozšíření](../hybrid/how-to-connect-sync-feature-directory-extensions.md) se synchronizují z místní služby Windows Server AD nebo z připojené aplikace SaaS a mají formát `user.extension_[GUID]_[Attribute]` , kde:

* [GUID] je jedinečný identifikátor ve službě Azure AD pro aplikaci, která vytvořila vlastnost ve službě Azure AD.
* [Attribute] je název vlastnosti, jak byla vytvořena.

Příkladem pravidla, které používá vlastní vlastnost rozšíření:

```
user.extension_c272a57b722d4eb29bfe327874ae79cb_OfficeNumber -eq "123"
```

Název vlastní vlastnosti lze najít v adresáři dotazem na vlastnost uživatele pomocí Průzkumníka graphu a vyhledáním názvu vlastnosti. V Tvůrci pravidel dynamické skupiny uživatelů teď můžete vybrat odkaz **získat vlastní rozšíření vlastností** a zadat jedinečný identifikátor aplikace a získat úplný seznam vlastností vlastního rozšíření, které se použijí při vytváření dynamického pravidla členství. Tento seznam je také možné aktualizovat, aby získal všechny nové vlastnosti rozšíření pro danou aplikaci.

## <a name="rules-for-devices"></a>Pravidla pro zařízení

Můžete také vytvořit pravidlo, které vybere objekty zařízení pro členství ve skupině. Nemůžete mít uživatele i zařízení jako členy skupiny. 

> [!NOTE]
> Atribut **organizationalUnit** již není uveden a neměl by být použit. Tento řetězec je v Intune nastavený v určitých případech, ale Azure AD ho nerozpoznal, takže se do skupin na základě tohoto atributu nepřidala žádná zařízení.

> [!NOTE]
> systemlabels je atribut jen pro čtení, který nelze nastavit pomocí Intune.
>
> Ve Windows 10 je správný formát atributu deviceOSVersion následující: (Device. deviceOSVersion-EQ "10.0.17763"). Formátování lze ověřit pomocí rutiny prostředí PowerShell Get-MsolDevice.

Je možné použít následující atributy zařízení.

 Atribut zařízení  | Hodnoty | Příklad
 ----- | ----- | ----------------
 accountEnabled | true false | (Device. accountEnabled-EQ true)
 displayName | libovolná hodnota řetězce |(Device. DisplayName-EQ "Rob iPhone")
 deviceOSType | libovolná hodnota řetězce | (Device. deviceOSType-EQ "iPad")-nebo (zařízení. deviceOSType-EQ "iPhone")<br>(Device. deviceOSType-obsahuje "AndroidEnterprise")<br>(Device. deviceOSType-EQ "AndroidForWork")
 deviceOSVersion | libovolná hodnota řetězce | (Device. deviceOSVersion-EQ "9,1")
 deviceCategory | platný název kategorie zařízení | (Device. deviceCategory-EQ "BYOD")
 deviceManufacturer | libovolná hodnota řetězce | (Device. deviceManufacturer-EQ "Samsung")
 deviceModel | libovolná hodnota řetězce | (Device. deviceModel-EQ "iPad Air")
 deviceOwnership | Osobní, společnost, neznámá | (Device. deviceOwnership-EQ "společnost")
 enrollmentProfileName | Název profilu registrace zařízení Apple, název profilu registrace vyhrazeného zařízení s Androidem Enterprise, nebo název profilu Windows autopilot | (zařízení. enrollmentProfileName-EQ "DEP – iPhone")
 s kořenem | true false | (Device.-rooted-EQ true)
 managementType | MDM (pro mobilní zařízení)<br>POČÍTAČ (pro počítače spravované agentem Intune pro počítače) | (Device. managementType-EQ "MDM")
 deviceId | platné ID zařízení Azure AD | (Device. deviceId-EQ "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 Objektu | platné ID objektu Azure AD |  (Device. objectId-EQ "76ad43c9-32c5-45e8-a272-7b58b58f596d")
 devicePhysicalIds | libovolná hodnota řetězce používaná autopilotem, například všechna zařízení s autopilotem, ČísloObjednávky nebo PurchaseOrderID  | (Device. devicePhysicalIDs-any _-obsahuje "[ZTDId]") (Device. devicePhysicalIds-any _-EQ "[ČísloObjednávky]: 179887111881") (Device. devicePhysicalIds-any _-EQ "[PurchaseOrderId]: 76222342342")
 systemLabels | libovolný řetězec odpovídající vlastnosti zařízení Intune pro označování moderních zařízení na pracovišti | (device.systemLabels-obsahuje "M365Managed")

> [!Note]  
> Pro deviceOwnership při vytváření dynamických skupin pro zařízení musíte nastavit hodnotu rovnou "společnost". V Intune se vlastnictví zařízení prezentuje jako firemní. Další podrobnosti najdete v tématu [OwnerTypes](/intune/reports-ref-devices#ownertypes) . 

## <a name="next-steps"></a>Další kroky

Tyto články poskytují další informace o skupinách v Azure Active Directory.

- [Zobrazení existujících skupin](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Vytvoření nové skupiny a přidání členů](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Správa nastavení skupiny](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Správa členství ve skupině](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Správa dynamických pravidel pro uživatele ve skupině](groups-create-rule.md)