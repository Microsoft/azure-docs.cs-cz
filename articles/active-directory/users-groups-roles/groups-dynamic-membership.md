---
title: Pravidla pro dynamicky vymožené členství ve skupinách – Azure AD | Dokumenty společnosti Microsoft
description: Jak vytvořit pravidla členství pro automatické vyplnění skupin a odkaz na pravidlo.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/27/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6f8237ac13744e56baa8551f8cced12b2785a48
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114743"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>Pravidla dynamického členství pro skupiny ve službě Azure Active Directory

Ve službě Azure Active Directory (Azure AD) můžete vytvořit komplexní pravidla založená na atributech, která umožní dynamická členství pro skupiny. Dynamické členství ve skupinách snižuje administrativní režii při přidávání a odebírání uživatelů. Tento článek podrobně popisuje vlastnosti a syntaxi pro vytvoření dynamických pravidel členství pro uživatele nebo zařízení. Pravidlo pro dynamické členství můžete nastavit pro skupiny zabezpečení nebo pro skupiny Office 365.

Při změně některých atributů uživatele nebo zařízení systém vyhodnotí všechna pravidla dynamické skupiny v adresáři a zjistí, zda by změna spustila přidání nebo odebrání libovolné skupiny. Pokud uživatel nebo zařízení splňuje pravidlo ve skupině, jsou přidány jako člen této skupiny. Pokud již nesplňují pravidlo, jsou odebrány. Nemůžete ručně přidat nebo odebrat člena dynamické skupiny.

- Můžete vytvořit dynamickou skupinu pro zařízení nebo pro uživatele, ale nemůžete vytvořit pravidlo, které obsahuje uživatele i zařízení.
- Skupinu zařízení nelze vytvořit na základě atributů vlastníků zařízení. Pravidla členství v zařízení mohou odkazovat pouze na atributy zařízení.

> [!NOTE]
> Tato funkce vyžaduje licenci Azure AD Premium P1 pro každého jedinečného uživatele, který je členem jedné nebo více dynamických skupin. Není nutné přiřazovat licence uživatelům, aby byli členy dynamických skupin, ale musíte mít minimální počet licencí v tenantovi, aby pokryli všechny tyto uživatele. Například pokud jste měli celkem 1 000 jedinečných uživatelů ve všech dynamických skupinách ve vašem tenantovi, budete potřebovat alespoň 1 000 licencí pro Azure AD Premium P1 ke splnění licenčního požadavku.
> Pro zařízení, která jsou členy dynamické skupiny zařízení, není vyžadována žádná licence.

## <a name="rule-builder-in-the-azure-portal"></a>Tvůrce pravidel na webu Azure Portal

Azure AD poskytuje tvůrce pravidel pro rychlejší vytváření a aktualizaci důležitých pravidel. Tvůrce pravidel podporuje konstrukci až pět výrazů. Tvůrce pravidel usnadňuje vytvoření pravidla s několika jednoduchými výrazy, ale nelze jej použít k reprodukci každého pravidla. Pokud tvůrce pravidel nepodporuje pravidlo, které chcete vytvořit, můžete použít textové pole.

Zde jsou některé příklady pokročilých pravidel nebo syntaxe, pro které doporučujeme vytvořit pomocí textového pole:

- Pravidlo s více než pěti výrazy
- Pravidlo Přímé sestavy
- Nastavení [priority operátora](groups-dynamic-membership.md#operator-precedence)
- [Pravidla se složitými výrazy](groups-dynamic-membership.md#rules-with-complex-expressions); například`(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Tvůrce pravidel nemusí být schopen zobrazit některá pravidla vytvořená v textovém poli. Může se zobrazit zpráva, když tvůrce pravidel není schopen zobrazit pravidlo. Tvůrce pravidel žádným způsobem nezmění podporovanou syntaxi, ověření nebo zpracování pravidel dynamické skupiny.

Další podrobné pokyny naleznete v tématu [Vytvoření nebo aktualizace dynamické skupiny](groups-create-rule.md).

![Přidání pravidla členství pro dynamickou skupinu](./media/groups-dynamic-membership/update-dynamic-group-rule.png)

### <a name="rule-syntax-for-a-single-expression"></a>Syntaxe pravidla pro jeden výraz

Jeden výraz je nejjednodušší forma pravidla členství a má pouze tři výše uvedené části. Pravidlo s jedním výrazem vypadá `Property Operator Value`podobně jako toto: , kde syntaxe vlastnosti je název object.property.

Následuje příklad správně sestaveného pravidla členství s jedním výrazem:

```
user.department -eq "Sales"
```

Závorky jsou volitelné pro jeden výraz. Celková délka těla pravidla členství nesmí překročit 2048 znaků.

## <a name="constructing-the-body-of-a-membership-rule"></a>Vytvoření těla pravidla členství

Pravidlo členství, které automaticky naplňuje skupinu uživateli nebo zařízeními, je binární výraz, jehož výsledkem je skutečný nebo nepravdivý výsledek. Tři části jednoduchého pravidla jsou:

- Vlastnost
- Operátor
- Hodnota

Pořadí částí ve výrazu jsou důležité, aby se zabránilo chybám syntaxe.

## <a name="supported-properties"></a>Podporované vlastnosti

Existují tři typy vlastností, které lze použít k vytvoření pravidla členství.

- Logická hodnota
- Řetězec
- Kolekce řetězců

Následují vlastnosti uživatele, které můžete použít k vytvoření jednoho výrazu.

### <a name="properties-of-type-boolean"></a>Vlastnosti typu logické

| Vlastnosti | Povolené hodnoty | Využití |
| --- | --- | --- |
| účetPovolený |pravda false |user.accountEnabled -eq true |
| dirSyncEnabled |pravda false |user.dirSyncEnabled -eq true |

### <a name="properties-of-type-string"></a>Vlastnosti řetězce typu

| Vlastnosti | Povolené hodnoty | Využití |
| --- | --- | --- |
| city |Libovolná hodnota řetězce nebo *hodnota null* |(user.city -eq "value") |
| country |Libovolná hodnota řetězce nebo *hodnota null* |(user.country -eq "value") |
| Companyname | Libovolná hodnota řetězce nebo *hodnota null* | (user.companyName -eq "value") |
| Oddělení |Libovolná hodnota řetězce nebo *hodnota null* |(user.department -eq "hodnota") |
| displayName |Libovolná hodnota řetězce |(user.displayName -eq "value") |
| Employeeid |Libovolná hodnota řetězce |(user.employeeId -eq "value")<br>(user.employeeId -ne *null*) |
| facsimileČíslo telefonu |Libovolná hodnota řetězce nebo *hodnota null* |(user.facsimileTelephoneNumber -eq "hodnota") |
| givenName |Libovolná hodnota řetězce nebo *hodnota null* |(user.givenName -eq "value") |
| název úlohy |Libovolná hodnota řetězce nebo *hodnota null* |(user.jobTitle -eq "value") |
| pošta |Libovolná hodnota řetězce nebo *null* (adresa SMTP uživatele) |(user.mail -eq "value") |
| mailNickName |Libovolná hodnota řetězce (poštovní alias uživatele) |(user.mailNickName -eq "value") |
| mobil |Libovolná hodnota řetězce nebo *hodnota null* |(user.mobile -eq "hodnota") |
| Objectid |Identifikátor GUID objektu uživatele |(user.objectId -eq "1111111-1111-1111-1111-1111-111111111111111") |
| onPremisesSecurityIdentifier | Místní identifikátor zabezpečení (SID) pro uživatele, kteří byli synchronizováni z místního do cloudu. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-11111111111-111111111-11111111-11111111-1111111111-11111111-11111111-11111111-11111111-111111111-11111111-111111111-1111111111-1111111111-1111111111-1111111-1111111-111 |
| zásady hesel |Žádný DisableStrongPassword ZakázatPasswordExpiration Expiration DisablePasswordExpirationExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Libovolná hodnota řetězce nebo *hodnota null* |(user.physicalDeliveryOfficeName -eq "value") |
| Postalcode |Libovolná hodnota řetězce nebo *hodnota null* |(user.postalCode -eq "value") |
| preferredLanguage |Kód ISO 639-1 |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |Libovolná hodnota řetězce nebo *hodnota null* |(user.sipProxyAddress -eq "value") |
| state |Libovolná hodnota řetězce nebo *hodnota null* |(user.state -eq "value") |
| Streetaddress |Libovolná hodnota řetězce nebo *hodnota null* |(user.streetAddress -eq "value") |
| surname |Libovolná hodnota řetězce nebo *hodnota null* |(user.surname -eq "value") |
| phoneČíslo |Libovolná hodnota řetězce nebo *hodnota null* |(user.telephoneNumber -eq "value") |
| usageUmístění |Dva kódy země s písmeny |(user.usageLocation -eq "US") |
| userPrincipalName (Hlavní název uživatele) |Libovolná hodnota řetězce |(user.userPrincipalName -eqalias@domain" ") |
| userType |člen host *null* |(user.userType -eq "Člen") |

### <a name="properties-of-type-string-collection"></a>Vlastnosti kolekce typů řetězců

| Vlastnosti | Povolené hodnoty | Využití |
| --- | --- | --- |
| otherMails |Libovolná hodnota řetězce |(user.otherMails -obsahujealias@domain" ") |
| proxyAddresses |SMTP: alias@domain smtp:alias@domain |(user.proxyAddresses -obsahuje "SMTP: alias@domain") |

Vlastnosti používané pro pravidla zařízení naleznete v [tématu Pravidla pro zařízení](#rules-for-devices).

## <a name="supported-expression-operators"></a>Podporované operátory výrazů

V následující tabulce jsou uvedeny všechny podporované operátory a jejich syntaxe pro jeden výraz. Operátory lze použít s předponou pomlčky (-) nebo bez ní.

| Operátor | Syntaxe |
| --- | --- |
| Není rovno |-ne |
| Rovná se |-eq |
| Nezačíná |-notStartsWith |
| Začíná |-startsWith |
| Neobsahuje |-notContains |
| Contains |-obsahuje |
| Neshoduje se |-notMatch |
| Shoda |-zápas |
| V | -v |
| Není v | -notIn |

### <a name="using-the--in-and--notin-operators"></a>Použití operátorů -in a -notIn

Pokud chcete porovnat hodnotu atributu uživatele s řadou různých hodnot, můžete použít operátory -in nebo -notIn. Pomocí symbolů závorek "[" a "]" začínte a ukončujseznam hodnot.

 V následujícím příkladu výraz vyhodnotí true, pokud hodnota user.department rovná některé z hodnot v seznamu:

```
   user.department -in ["50001","50002","50003","50005","50006","50007","50008","50016","50020","50024","50038","50039","51100"]
```


### <a name="using-the--match-operator"></a>Použití operátoru -match 
Operátor **-match** se používá pro porovnávání libovolného regulárního výrazu. Příklady:

```
user.displayName -match "Da.*"   
```
Da, Dav, David vyhodnotit na true, aDa vyhodnotí na false.

```
user.displayName -match ".*vid"
```
David hodnotí jako pravda, Da hodnotí jako nepravdivé.

## <a name="supported-values"></a>Podporované hodnoty

Hodnoty použité ve výrazu se mohou skládat z několika typů, včetně:

* Řetězce
* Logická – pravda, nepravda
* Čísla
* Pole – číselné pole, pole řetězců

Při zadávání hodnoty ve výrazu je důležité použít správnou syntaxi, aby se zabránilo chybám. Některé popisy syntaxe jsou:

* Dvojité uvozovky jsou volitelné, pokud hodnota je řetězec.
* Operace řetězce a regulárnívýraz nejsou rozlišena.
* Pokud hodnota řetězce obsahuje dvojité uvozovky, \` obě uvozovky by měly \`být\`uvozeny pomocí znaku, například user.department -eq "Prodej " je správná syntaxe, když "Prodej" je hodnota.
* Můžete také provádět kontroly Null, pomocí null `user.department -eq null`jako hodnotu, například .

### <a name="use-of-null-values"></a>Použití hodnot Null

Chcete-li zadat hodnotu null v pravidle, můžete použít *hodnotu null.* 

* Při porovnávání *hodnoty null* ve výrazu použijte -eq nebo -ne.
* Uvozovky kolem slova *null* použijte pouze v případě, že chcete, aby bylo interpretováno jako hodnota řetězce literálu.
* Operátor -not nelze použít jako srovnávací operátor pro null. Pokud jej použijete, zobrazí se chyba, zda používáte hodnotu null nebo $null.

Správný způsob, jak odkazovat na hodnotu null, je následující:

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>Pravidla s více výrazy

Pravidlo členství ve skupině se může skládat z více než jednoho výrazu připojeného logickými operátory -and, -or a -not. Logické operátory lze také použít v kombinaci. 

Následují příklady správně vytvořených pravidel členství s více výrazy:

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>Priorita operátorů

Všechny operátory jsou uvedeny níže v pořadí podle priority od nejvyšší po nejnižší. Operátory na stejném řádku mají stejnou prioritu:

```
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
-not
-and
-or
-any -all
```

Následuje příklad priority operátoru, kde jsou pro uživatele vyhodnocovány dva výrazy:

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

Závorky jsou potřeba pouze v případě, že priorita nesplňuje vaše požadavky. Pokud například chcete, aby oddělení bylo vyhodnoceno jako první, následující ukazuje, jak lze závorky použít k určení pořadí:

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>Pravidla se složitými výrazy

Pravidlo členství se může skládat ze složitých výrazů, kde vlastnosti, operátory a hodnoty nabírají složitější formuláře. Výrazy jsou považovány za složité, pokud jsou splněny některé z následujících hodnot:

* Vlastnost se skládá z kolekce hodnot; konkrétně vícehodnotové nemovitosti
* Výrazy používají -any a -all operátory
* Hodnota výrazu může být sama o sobě jeden nebo více výrazů.

## <a name="multi-value-properties"></a>Vlastnosti s více hodnotami

Vlastnosti s více hodnotami jsou kolekce objektů stejného typu. Lze je použít k vytvoření pravidel členství pomocí -any a -all logické operátory.

| Vlastnosti | Hodnoty | Využití |
| --- | --- | --- |
| přiřazené plány | Každý objekt v kolekci zpřístupňuje následující vlastnosti řetězce: capabilityStatus, service, servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |
| proxyAddresses| SMTP: alias@domain smtp:alias@domain | (user.proxyAddresses -any\_ ( -contains "contoso")) |

### <a name="using-the--any-and--all-operators"></a>Použití -any a -all operátorů

Můžete použít -all a -all operátory použít podmínku pro jednu nebo všechny položky v kolekci, v uvedeném pořadí.

* -any (splněno, pokud alespoň jedna položka v kolekci odpovídá podmínce)
* -all (splněno, když všechny položky v kolekci odpovídají podmínce)

#### <a name="example-1"></a>Příklad 1

assignedPlans je vlastnost s více hodnotami, která obsahuje seznam všech plánů služeb přiřazených uživateli. Následující výraz vybere uživatele, kteří mají plán služeb Exchange Online (plán 2) (jako hodnotu GUID), který je také ve stavu Enabled:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

Pravidlo, jako je toto, lze použít k seskupení všech uživatelů, pro které je povolena funkce Office 365 (nebo jiné služby Microsoft Online Service). Potom můžete použít sadu zásad pro skupinu.

#### <a name="example-2"></a>Příklad 2

Následující výraz vybere všechny uživatele, kteří mají jakýkoli plán služeb, který je přidružen ke službě Intune (identifikovaný názvem služby "SCO"):

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

### <a name="using-the-underscore-_-syntax"></a>Použití syntaxe\_podtržítek ( )

Syntaxe\_podtržítka ( ) odpovídá výskytům určité hodnoty v jedné z vlastností kolekce řetězců s více hodnotami a přidejte uživatele nebo zařízení do dynamické skupiny. Používá se s -any nebo -all operátory.

Zde je příklad použití podtržítko (\_) v pravidle pro přidání členů na základě user.proxyAddress (funguje to též pro user.otherMails). Toto pravidlo přidá do skupiny všechny uživatele s adresou proxy, která obsahuje "contoso".

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>Další vlastnosti a společná pravidla

### <a name="create-a-direct-reports-rule"></a>Vytvoření pravidla "Přímé sestavy"

Můžete vytvořit skupinu obsahující všechny přímé sestavy manažera. Když se v budoucnu změní přímé zprávy manažera, členství ve skupině se automaticky upraví.

Pravidlo přímých sestav je vyrobeno pomocí následující syntaxe:

```
Direct Reports for "{objectID_of_manager}"
```

Zde je příklad platného pravidla, kde "62e19b97-8b3d-4d4a-a106-4ce66896a863" je objektID správce:

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

Následující tipy vám mohou pomoci správně používat pravidlo.

- **ID správce** je ID objektu správce. Najdete ji v **profilu**manažera .
- Aby pravidlo fungovalo, ujistěte se, že je vlastnost **Manager** správně nastavena pro uživatele ve vašem tenantovi. Aktuální hodnotu můžete zkontrolovat v **profilu**uživatele .
- Toto pravidlo podporuje pouze přímé sestavy vedoucího. Jinými slovy, nelze vytvořit skupinu s přímými sestavami vedoucího *a* jejich sestavami.
- Toto pravidlo nelze kombinovat s jinými pravidly členství.

### <a name="create-an-all-users-rule"></a>Vytvoření pravidla "Všichni uživatelé"

Můžete vytvořit skupinu obsahující všechny uživatele v rámci klienta pomocí pravidla členství. Když jsou uživatelé přidáni nebo odebráni z tenanta v budoucnu, členství skupiny se automaticky upraví.

Pravidlo "Všichni uživatelé" je konstruováno pomocí jednoho výrazu pomocí operátoru -ne a hodnoty null. Toto pravidlo přidá uživatele typu Host B2B i členské uživatele do skupiny.

```
user.objectId -ne null
```
Pokud chcete, aby vaše skupina vyloučila uživatele typu Host a zahrnula jenom členy vašeho tenanta, můžete použít následující syntaxi:

```
(user.objectId -ne null) -and (user.userType -eq "Member")
```

### <a name="create-an-all-devices-rule"></a>Vytvoření pravidla "Všechna zařízení"

Můžete vytvořit skupinu obsahující všechna zařízení v rámci klienta pomocí pravidla členství. Když jsou zařízení přidána nebo odebrána z tenanta v budoucnu, členství ve skupině se automaticky upraví.

Pravidlo "Všechna zařízení" je konstruováno pomocí jednoho výrazu pomocí operátoru -ne a hodnoty null:

```
device.objectId -ne null
```

## <a name="extension-properties-and-custom-extension-properties"></a>Vlastnosti rozšíření a vlastní vlastnosti rozšíření

Atributy rozšíření a vlastní vlastnosti rozšíření jsou podporovány jako vlastnosti řetězce v pravidlech dynamického členství. [Atributy rozšíření](https://docs.microsoft.com/graph/api/resources/onpremisesextensionattributes?view=graph-rest-1.0) jsou synchronizovány z místního okna serveru AD a převzít formát "ExtensionAttributeX", kde X se rovná 1 - 15. Tady je příklad pravidla, které používá atribut rozšíření jako vlastnost:

```
(user.extensionAttribute15 -eq "Marketing")
```

[Vlastní vlastnosti rozšíření](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-directory-extensions) jsou synchronizovány z místní služby Windows Server AD nebo `user.extension_[GUID]_[Attribute]`z připojené aplikace SaaS a jsou ve formátu aplikace , kde:

* [GUID] je jedinečný identifikátor ve službě Azure AD pro aplikaci, která vytvořila vlastnost ve službě Azure AD
* [Attribute] je název vlastnosti, jak byla vytvořena

Příkladem pravidla, které používá vlastní vlastnost rozšíření, je:

```
user.extension_c272a57b722d4eb29bfe327874ae79cb_OfficeNumber -eq "123"
```

Název vlastní vlastnosti lze nalézt v adresáři dotazem na vlastnost uživatele pomocí průzkumníka graphu a vyhledáním názvu vlastnosti. Nyní můžete také vybrat možnost Získat odkaz **na vlastní vlastnosti rozšíření** v nástroji tvůrce pravidel dynamické skupiny uživatelů, chcete-li zadat jedinečné ID aplikace a získat úplný seznam vlastních vlastností rozšíření, které se mají použít při vytváření pravidla dynamického členství. Tento seznam lze také aktualizovat získat všechny nové vlastní vlastnosti rozšíření pro tuto aplikaci.

## <a name="rules-for-devices"></a>Pravidla pro zařízení

Můžete také vytvořit pravidlo, které vybere objekty zařízení pro členství ve skupině. Jako členy skupiny nemůžete mít uživatele i zařízení. 

> [!NOTE]
> Atribut **organizationalUnit** již není uveden a neměl by být používán. Tento řetězec je nastaven Intune v konkrétních případech, ale není rozpoznán Azure AD, takže žádná zařízení jsou přidány do skupin na základě tohoto atributu.

> [!NOTE]
> systémové popisky je atribut jen pro čtení, který nelze nastavit pomocí Intune.
>
> Pro Windows 10 je správný formát atributu deviceOSVersion následující: (device.deviceOSVersion -eq "10.0.17763"). Formátování lze ověřit pomocí rutiny Get-MsolDevice PowerShell.

Lze použít následující atributy zařízení.

 Atribut zařízení  | Hodnoty | Příklad
 ----- | ----- | ----------------
 účetPovolený | pravda false | (device.accountEnabled -eq true)
 displayName | libovolná hodnota řetězce |(device.displayName -eq "Rob iPhone")
 deviceOSType | libovolná hodnota řetězce | (device.deviceOSType -eq "iPad") -or (device.deviceOSType -eq "iPhone")<br>(device.deviceOSType -obsahuje "AndroidEnterprise")<br>(device.deviceOSType -eq "AndroidForWork")
 deviceOSVersion | libovolná hodnota řetězce | (device.deviceOSVersion -eq "9.1")
 deviceKategorie | platný název kategorie zařízení | (device.deviceCategory -eq "BYOD")
 zařízeníVýrobce | libovolná hodnota řetězce | (device.deviceManufacturer -eq "Samsung")
 model zařízení | libovolná hodnota řetězce | (device.deviceModel -eq "iPad Air")
 deviceOwnership | Osobní, Společnost, Neznámý | (device.deviceOwnership -eq "Společnost")
 enrollmentProfileName | Profil registrace zařízení Apple, registrace zařízení – identifikátory podnikových zařízení (Android – kiosk) nebo název profilu Windows Autopilot | (device.enrollmentProfileName -eq "DEP iPhone")
 isRooted | pravda false | (device.isRooted -eq true)
 managementType | MDM (pro mobilní zařízení)<br>PC (pro počítače spravované agentem Intune PC) | (device.managementType -eq "MDM")
 deviceId | platné ID zařízení Azure AD | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 Objectid | platné ID objektu Azure AD |  (device.objectId -eq "76ad43c9-32c5-45e8-a272-7b58b58f596d")
 devicePhysicalIds | libovolnou hodnotu řetězce používanou autopilotem, například všechna zařízení Autopilot, OrderID nebo PurchaseOrderID  | (device.devicePhysicalID -any _ -obsahuje "[ZTDId]") (device.devicePhysicalIds -any _ -eq "[OrderID]:179887111881") (device.devicePhysicalIds -any _ -eq "[PurchaseOrderId]:76222342342")
 systémové štítky | libovolný řetězec odpovídající vlastnosti zařízení Intune pro značkování zařízení Modern Workplace | (device.systemLabels -obsahuje "M365Managed")

> [!Note]  
> Pro deviceOwnership při vytváření dynamických skupin pro zařízení je třeba nastavit hodnotu rovnou "Společnost". V Intune je vlastnictví zařízení reprezentováno jako Firemní. Další podrobnosti naleznete v [typech ownertypes.](https://docs.microsoft.com/intune/reports-ref-devices#ownertypes) 

## <a name="next-steps"></a>Další kroky

Tyto články obsahují další informace o skupinách ve službě Azure Active Directory.

- [Zobrazení existujících skupin](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Vytvoření nové skupiny a přidání členů](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Správa nastavení skupiny](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Správa členství ve skupině](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Správa dynamických pravidel pro uživatele ve skupině](groups-create-rule.md)
