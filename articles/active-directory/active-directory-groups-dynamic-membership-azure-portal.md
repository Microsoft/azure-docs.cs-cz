---
title: Na základě atributů dynamické členství ve skupině v Azure Active Directory | Microsoft Docs
description: Postup vytváření rozšířených pravidel, včetně členství dynamické skupiny podporované výraz pravidlo operátory a parametry.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.assetid: fb434cc2-9a91-4ebf-9753-dd81e289787e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/30/2018
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.openlocfilehash: a4ed9ddabe19406fa694992f29cf529b491438c0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="create-attribute-based-rules-for-dynamic-group-membership-in-azure-active-directory"></a>Vytvořit pravidla založená na atributu pro dynamické členství ve skupině v Azure Active Directory
V Azure Active Directory (Azure AD) můžete vytvořit rozšířených pravidel povolit komplexní založená na atributu dynamické členství skupiny. Tento článek podrobně atributy a syntaxe k vytvoření pravidla dynamické členství pro uživatele nebo zařízení. Pravidlo pro dynamické členství můžete nastavit pro skupiny zabezpečení nebo pro skupiny Office 365.

Pokud žádné atributy uživatele nebo zařízení změnit, systém vyhodnotí všechna pravidla dynamické skupiny v adresáři, abyste zjistili, zda by změna aktivovat libovolnou skupinu přidá nebo odebere. Pokud na uživatele nebo zařízení splňuje pravidlo ve skupině, přidají se jako člena této skupiny. Pokud se už splňovat pravidla, se odeberou.

> [!NOTE]
> Tato funkce vyžaduje pro každého člena uživatel přidán do alespoň jednu skupinu dynamické licenci Azure AD Premium P1. Není to povinné ve skutečnosti přiřaďte licence uživatelům pro ně mají být členy v dynamických skupin, ale musíte mít minimální počet licencí v klientovi nepokrývají všechny tyto uživatele. Například: Pokud máte celkem 1 000 jedinečných uživatelů, kteří ve všech dynamických skupin ve vašem klientovi, musíte mít alespoň 1 000 licencí pro Azure AD Premium P1 nebo vyšší, aby splňovala požadavky licence.
>
> Můžete vytvořit skupinu dynamické pro zařízení nebo uživatelů, ale nelze vytvořit pravidlo, které obsahuje uživatele a zařízení.
> 
> V tuto chvíli není možné vytvořit skupinu zařízení na základě atributů vlastnícím uživatele. Pravidla členství zařízení může odkazovat pouze na okamžitou atributy zařízení objektů v adresáři.

## <a name="to-create-an-advanced-rule"></a>Vytvoření pokročilé pravidla
1. Přihlaste se k [centra pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který je globální správce nebo správce účtu uživatele.
2. Vyberte **uživatelů a skupin**.
3. Vyberte **všechny skupiny**a vyberte **nové skupiny**.

   ![Přidat nové skupiny](./media/active-directory-groups-dynamic-membership-azure-portal/new-group-creation.png)

4. Na **skupiny** okno, zadejte název a popis nové skupiny. Vyberte **typ členství** buď **dynamické uživatele** nebo **dynamické zařízení**, v závislosti na tom, jestli chcete vytvořit pravidlo pro uživatele nebo zařízení a potom vyberte **Přidat dynamické dotazu**. Můžete použít k vytvoření jednoduché pravidlo Tvůrce pravidlo, nebo můžete zapsat rozšířené pravidlo sami. Tento článek obsahuje další informace o dostupné atributy uživatele a zařízení, jakož i Příklady rozšířených pravidel.

   ![Přidat dynamické pravidlo členství](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)

5. Po vytvoření pravidla, vyberte **přidat dotazu** v dolní části okna.
6. Vyberte **vytvořit** na **skupiny** okno vytvořit skupinu.

> [!TIP]
> Vytvoření skupiny může selhat, pokud byl rozšířené pravidlo, které jste zadali nesprávný. Oznámení se zobrazí v pravém dolním rohu portálu; obsahuje vysvětlení, proč nelze přijmout pravidlo v systému. Přečtěte si ho pečlivě, abyste pochopili, jak bude nutné upravit pravidlo pro zahájení platnosti.

## <a name="constructing-the-body-of-an-advanced-rule"></a>Vytváření textu rozšířené pravidlo
Pokročilé pravidlo, které můžete vytvořit pro dynamické členství ve skupinách je v podstatě binárního výrazu, který se skládá ze tří částí a výsledkem výsledek true nebo false. Jsou tři části:

* Levý parametr
* Binární operátor
* Pravé konstanta

Dokončení rozšířeného pravidla vypadá podobně jako tento: (leftParameter binaryOperator "RightConstant"), kde jsou volitelné pro celý výraz binární otevírání a kulaté závorky, dvojité uvozovky jsou volitelné taky jen vyžaduje pro správné konstanta Pokud je řetězec a syntaxe pro levý parametr je user.property. Rozšířené pravidlo se může skládat z více než jeden binární výrazy oddělených- a- nebo a - není logické operátory.

Následují příklady správně strukturovaný pokročilé pravidla:
```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```
Úplný seznam podporovaných parametrů a operátory výraz pravidlo najdete v níže uvedených částech. Pro atributy používané pro pravidla zařízení, najdete v části [pomocí atributů k vytvoření pravidel pro objekty zařízení](#using-attributes-to-create-rules-for-device-objects).

Celková délka textu rozšířeného pravidla nesmí překročit hodnotu 2048 znaků.

> [!NOTE]
> Operace řetězec a regex se nerozlišují malá a velká písmena. Můžete také provést kontroly Null pomocí *null* jako konstanta, například user.department - eq *null*.
> Řetězce obsahující uvozovky "by měly být ukončeny pomocí, například znak user.department - eq \`"Prodej".

## <a name="supported-expression-rule-operators"></a>Podporované výraz pravidlo operátory
V následující tabulce jsou uvedeny všechny operátory pravidlo podporované výrazu a jejich syntaxi, který se má použít v těle rozšířeného pravidla:

| Operátor | Syntaxe |
| --- | --- |
| Nerovná se |-ne |
| Je rovno |-eq |
| Není začíná |-notStartsWith |
| Začíná |-startsWith |
| Neobsahuje |-notContains |
| Contains |-obsahuje |
| Neodpovídá |-notMatch |
| Shoda |-odpovídat |
| V | -v |
| Není ve | -notIn |

## <a name="operator-precedence"></a>Priorita operátorů

Níže jsou uvedeny všechny operátory za prioritu z nižší na vyšší. Operátory na stejném řádku jsou ve stejné prioritu:
````
-any -all
-or
-and
-not
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
````
Všechny operátory lze použít s nebo bez předpony pomlčkou. Závorky jsou potřeba jenom v případě, že přednost nesplňuje vaše požadavky.
Příklad:
```
   user.department –eq "Marketing" –and user.country –eq "US"
```
je ekvivalentní:
```
   (user.department –eq "Marketing") –and (user.country –eq "US")
```
## <a name="using-the--in-and--notin-operators"></a>Pomocí-v a notIn – operátory

Pokud chcete porovnat hodnotu atributu uživatele proti počet různých hodnot můžete použít-v nebo - notIn operátory. Tady je příklad použití-v operátoru:
```
    user.department -In [ "50001", "50002", "50003", “50005”, “50006”, “50007”, “50008”, “50016”, “50020”, “50024”, “50038”, “50039”, “51100” ]
```
Všimněte si použití "[" a "]" na začátku a konci seznamu hodnot. Tato podmínka vyhodnocena jako True hodnota rovná user.department jedna z hodnot v seznamu.


## <a name="query-error-remediation"></a>Náprava chyby dotazu
Následující tabulka uvádí běžných chyb a opravte je

| Chyba analýzy dotazu | Chyba použití | Opravené využití |
| --- | --- | --- |
| Chyba: Atribut není podporován. |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>Ujistěte se, že atribut je na [podporované seznam vlastností](#supported-properties). |
| Chyba: Operátor není podporován pro atribut. |(user.accountEnabled – obsahuje hodnotu PRAVDA) |(user.accountEnabled -eq true)<br/><br/>Operátor použitý nepodporuje pro typ vlastnosti (v tomto příkladu – obsahuje nelze použít v typu logická hodnota). Použijte správný operátory pro vlastnosti typu. |
| Chyba: Chyba při kompilaci dotazu. |1. (user.department - eq "Prodej") (user.department - eq "Marketing")<br/><br/>2. (user.userPrincipalName -match "*@domain.ext") |1. Operátor nebyl nalezen. Pomocí - nebo - nebo dvě připojení predikáty<br/><br/>(user.department - eq "Prodej")- nebo (user.department - eq "Marketing")<br/><br/>2. Chyba v regulárním výrazu použít s - odpovídat<br/><br/>(user.userPrincipalName-shodovat s ". *@domain.ext"), případně: (user.userPrincipalName-shodovat s "@domain.ext$")|

## <a name="supported-properties"></a>Podporovaných vlastností
Tady jsou všechny vlastnosti uživatele, které můžete použít v pokročilé pravidla:

### <a name="properties-of-type-boolean"></a>Vlastnosti typu logická hodnota
Povolené operátory

* -eq
* -ne

| Vlastnosti | Povolené hodnoty | Využití |
| --- | --- | --- |
| accountEnabled |Hodnota TRUE, false |user.accountEnabled -eq true |
| dirSyncEnabled |Hodnota TRUE, false |user.dirSyncEnabled -eq true |

### <a name="properties-of-type-string"></a>Vlastnosti typu řetězec
Povolené operátory

* -eq
* -ne
* -notStartsWith
* -StartsWith
* -obsahuje
* -notContains
* -odpovídat
* -notMatch
* -v
* -notIn

| Vlastnosti | Povolené hodnoty | Využití |
| --- | --- | --- |
| city |Žádné hodnota typu řetězec nebo *hodnotu null.* |(user.city - eq "value") |
| Země |Žádné hodnota typu řetězec nebo *hodnotu null.* |(User.Country. - eq "value") |
| companyName | Žádné hodnota typu řetězec nebo *hodnotu null.* | (user.companyName -eq "value") |
| Oddělení |Žádné hodnota typu řetězec nebo *hodnotu null.* |(user.department -eq "value") |
| displayName |Libovolnou hodnotu řetězce |(user.displayName -eq "value") |
| číslo zaměstnance |Libovolnou hodnotu řetězce |(user.employeeId - eq "value")<br>(user.employeeId - ne *null*) |
| facsimileTelephoneNumber |Žádné hodnota typu řetězec nebo *hodnotu null.* |(user.facsimileTelephoneNumber -eq "value") |
| givenName |Žádné hodnota typu řetězec nebo *hodnotu null.* |(user.givenName -eq "value") |
| pracovní funkce |Žádné hodnota typu řetězec nebo *hodnotu null.* |(user.jobTitle -eq "value") |
| mail |Žádné hodnota typu řetězec nebo *null* (adresa SMTP uživatele) |(user.mail - eq "value") |
| mailNickName |Libovolnou hodnotu řetězce (e-mailu alias uživatele) |(user.mailNickName -eq "value") |
| mobilní |Žádné hodnota typu řetězec nebo *hodnotu null.* |(user.mobile - eq "value") |
| objectId |GUID objektu uživatele |(user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | Místní identifikátor zabezpečení (SID) pro uživatele, kteří se synchronizovaly z místní do cloudu. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |Žádný DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies - eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Žádné hodnota typu řetězec nebo *hodnotu null.* |(user.physicalDeliveryOfficeName - eq "value") |
| PSČ |Žádné hodnota typu řetězec nebo *hodnotu null.* |(user.postalCode - eq "value") |
| preferredLanguage |Kód ISO 639-1 |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |Žádné hodnota typu řetězec nebo *hodnotu null.* |(user.sipProxyAddress -eq "value") |
| state |Žádné hodnota typu řetězec nebo *hodnotu null.* |(user.state - eq "value") |
| StreetAddress |Žádné hodnota typu řetězec nebo *hodnotu null.* |(user.streetAddress - eq "value") |
| surname |Žádné hodnota typu řetězec nebo *hodnotu null.* |(user.surname -eq "value") |
| telephoneNumber |Žádné hodnota typu řetězec nebo *hodnotu null.* |(user.telephoneNumber - eq "value") |
| usageLocation |Dva písmeny směrové číslo země |(user.usageLocation - eq "US") |
| userPrincipalName |Libovolnou hodnotu řetězce |(user.userPrincipalName -eq "alias@domain") |
| userType |člen hosta *hodnotu null.* |(user.userType - eq "Člen") |

### <a name="properties-of-type-string-collection"></a>Vlastnosti typu řetězec kolekce
Povolené operátory

* -obsahuje
* -notContains

| Vlastnosti | Povolené hodnoty | Využití |
| --- | --- | --- |
| otherMails |Libovolnou hodnotu řetězce |(user.otherMails – obsahuje "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses – obsahuje "SMTP: alias@domain") |

## <a name="multi-value-properties"></a>Vícehodnotový vlastnosti
Povolené operátory

* -všechny (uspokojit, když minimálně jedna položka v kolekci odpovídá podmínku)
* -všechny (uspokojit, když všechny položky v kolekci splňují podmínku)

| Vlastnosti | Hodnoty | Využití |
| --- | --- | --- |
| assignedPlans |Každý objekt v kolekci zpřístupní následující vlastnosti řetězce: capabilityStatus, služby, servicePlanId |user.assignedPlans-všechny (assignedPlan.servicePlanId - eq "efb87545-963c-4e0d-99df-69c6916d9eb0"- a assignedPlan.capabilityStatus - eq "Povoleno") |

Vícehodnotový vlastnosti jsou kolekce objektů stejného typu. Můžete použít – všechny a - všechny operátory použít podmínku, na jeden nebo všechny položky v kolekci, v uvedeném pořadí. Příklad:

assignedPlans je vícehodnotový vlastnost, která uvádí všechny plány služby, které jsou přiřazeny uživateli. Níže výraz bude vyberte uživatele, kteří mají Exchange Online (plán 2) plán služeb, který je sám ve stavu povoleno:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

(Identifikátor Guid identifikuje tarifu Exchange Online (plán 2).)

> [!NOTE]
> To je užitečné, pokud chcete identifikovat všechny uživatele, pro kterého Office 365 (nebo jinou službu Microsoft Online) povoleno schopnosti, třeba pro jejich sadu zásad.

Následující výraz vybere všechny uživatele, kteří mají plán žádné služby, která souvisí se službou Intune (identifikovaný název služby "SCO"):
```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

## <a name="use-of-null-values"></a>Použití hodnoty Null

Chcete-li zadat hodnotu null v pravidle, můžete použít *null* hodnotu. Dejte pozor, abyste použít uvozovky, do kterých slovo *null* – v takovém případě bude interpretovat jako hodnotu řetězcového literálu. -Není operátor nelze použít jako srovnávací operátor pro hodnotu null. Pokud chcete použít, ať už používáte hodnotu null nebo $null dojde k chybě. Místo toho použijte - eq nebo - ne. Správný způsob, jak odkazovat na hodnotu null je následující:
```
   user.mail –ne $null
```

## <a name="extension-attributes-and-custom-attributes"></a>Atributy rozšíření a vlastní atributy
Atributy rozšíření a vlastní atributy jsou podporovány v pravidlech dynamické členství.

Rozšíření atributy jsou synchronizované z místní okno Server AD a proveďte formát "ExtensionAttributeX", kde X je rovno 1 až 15.
Jedná se například pravidla, které používá atribut rozšíření
```
(user.extensionAttribute15 -eq "Marketing")
```
Vlastní atributy se synchronizují z místního systému Windows Server AD nebo z připojených aplikací SaaS a formát "user.extension_[GUID]\__ [atribut]", kde [identifikátor GUID] je jedinečný identifikátor v AAD aplikace, která vytvoří atribut v AAD a [atribut] je název atributu, který byl vytvořen.
Je například pravidla, které používá vlastní atribut
```
user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  
```
Název vlastního atributu naleznete v adresáři dotazováním uživatele je atribut pomocí Průzkumníka grafu a vyhledávání pro název atributu.

## <a name="direct-reports-rule"></a>Pravidlo "Přímé podřízené"
Můžete vytvořit skupina obsahující všechny přímé podřízené manažera. Pokud v budoucnu změnit manažera přímé podřízené skupiny členství se upraví, automaticky.

> [!NOTE]
> 1. Pravidla pro práci, zajistěte, aby **Manager ID** vlastnost je správně nastavena na uživatele ve vašem klientovi. Aktuální hodnota pro uživatele můžete zkontrolovat na jejich **kartu profil**.
> 2. Toto pravidlo podporuje pouze **přímé** sestavy. Aktuálně není možné vytvořit skupinu pro vnořené hierarchii; například skupina, která zahrnuje přímé podřízené a jejich sestavy.
> 3. Toto pravidlo nelze kombinovat s jinými pravidly Upřesnit.

**Konfigurace skupiny**

1. Postupujte podle kroků 1 až 5 z oddílu [k vytvoření rozšířeného pravidla](#to-create-the-advanced-rule)a vyberte **typ členství** z **dynamické uživatele**.
2. Na **pravidla dynamické členství** okno, zadejte pravidlo s následující syntaxí:

    *Přímé podřízené pro "{objectID_of_manager}"*

    Příklad platné pravidlo:
```
                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```
    where “62e19b97-8b3d-4d4a-a106-4ce66896a863” is the objectID of the manager. The object ID can be found on manager's **Profile tab**.
3. Po uložení pravidlo, všichni uživatelé se zadanou hodnotou Manager ID přidá do skupiny.

## <a name="using-attributes-to-create-rules-for-device-objects"></a>Vytváření pravidel pro objekty zařízení pomocí atributů
Můžete také vytvořit pravidlo, které vybere objekty zařízení pro členství ve skupině. Můžete použít následující atributy zařízení.

 Atribut zařízení  | Hodnoty | Příklad:
 ----- | ----- | ----------------
 accountEnabled | Hodnota TRUE, false | (device.accountEnabled - eq true)
 displayName | Libovolnou hodnotu řetězce |(device.displayName -eq "Rob Iphone”)
 deviceOSType | Libovolnou hodnotu řetězce | (device.deviceOSType - eq "iPad")- nebo (device.deviceOSType - eq "iPhone")
 deviceOSVersion | Libovolnou hodnotu řetězce | (device.OSVersion -eq "9.1")
 deviceCategory | Název kategorie platný zařízení | (device.deviceCategory - eq "BYOD")
 DeviceManufacturer | Libovolnou hodnotu řetězce | (device.deviceManufacturer -eq "Samsung")
 deviceModel | Libovolnou hodnotu řetězce | (device.deviceModel - eq "iPad vzduchem")
 deviceOwnership | Osobní společnosti, neznámý | (device.deviceOwnership - eq "Společnost")
 domainName | Libovolnou hodnotu řetězce | (device.domainName - eq "contoso.com")
 enrollmentProfileName | Název profilu registrace zařízení Apple | (device.enrollmentProfileName - eq "DEP Iphony")
 isRooted | Hodnota TRUE, false | (device.isRooted - eq true)
 managementType | MDM (pro mobilní zařízení)<br>PC (pro počítače spravované agentem počítačů s Intune) | (device.managementType - eq "MDM")
 OrganizationalUnit | libovolnou hodnotu řetězce odpovídající názvu organizační jednotky ve služby Active Directory místní nastavení | (device.organizationalUnit - eq "Počítačů USA")
 deviceId | platné ID zařízení služby Azure AD | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | ID objektu platný Azure AD |  (device.objectId -eq 76ad43c9-32c5-45e8-a272-7b58b58f596d")



## <a name="changing-dynamic-membership-to-static-and-vice-versa"></a>Změna dynamické členství na statické a naopak
Je možné změnit, jak se spravuje členství ve skupině. To je užitečné, pokud chcete zachovat stejný název skupiny a ID v systému, takže všechny existující odkazy na skupiny musí být stále platné; Vytvoření nové skupiny by vyžadovaly aktualizace tyto odkazy.

Aktualizovali jsme do centra pro správu Azure AD přidat podporu této funkce. Nyní zákazníci můžete převést existující skupiny z dynamické členství přiřazené členství a naopak prostřednictvím centra pro správu Azure AD nebo rutiny prostředí PowerShell, jak je uvedeno níže.

> [!WARNING]
> Při změně existující statická skupina pro skupinu dynamické, všechny stávající členy se odeberou ze skupiny, a pak se zpracují pravidlo členství pro přidání nové členy. Pokud skupině se používá k řízení přístupu k aplikacím nebo prostředkům, původní členy může ztratit přístup, dokud plně zpracování pravidla členství se.
>
> Doporučujeme, abyste otestovali pravidla členství v předem a ujistěte se, že nové členství ve skupině je podle očekávání.

### <a name="using-azure-ad-admin-center-to-change-membership-management-on-a-group"></a>Chcete-li změnit správy členství ve skupině pomocí centra pro správu Azure AD 

1. Přihlaste se k [centra pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který je globální správce nebo správce účtu uživatele ve vašem klientovi.
2. Vyberte **skupiny**.
3. Z **všechny skupiny** seznamu, otevřete skupinu, kterou chcete změnit.
4. Vyberte **vlastnosti**.
5. Na **vlastnosti** stránce skupiny, vyberte **typ členství** přiřazeno (statické), dynamické uživatele nebo dynamické zařízení, v závislosti na vaší požadované členské typu. Pro dynamické členství můžete Tvůrce pravidla vyberte možnosti pro jednoduché pravidlo nebo Zapisovat rozšířené pravidlo sami. 

Následující kroky jsou například změny skupiny ze statické na dynamické členství ve skupině uživatelů. 

1. Na **vlastnosti** stránky pro vybrané skupiny, vyberte **typ členství** z **dynamické uživatele**, pak vyberte možnost Ano v dialogovém okně vysvětlením změny do skupiny členství pokračujte. 
  
   ![Vyberte typ členství dynamické uživatele](./media/active-directory-groups-dynamic-membership-azure-portal/select-group-to-convert.png)
  
2. Vyberte **přidat dynamické dotazu**a pak zadejte pravidlo.
  
   ![Zadejte pravidla](./media/active-directory-groups-dynamic-membership-azure-portal/enter-rule.png)
  
3. Po vytvoření pravidla, vyberte **přidat dotazu** v dolní části stránky.
4. Vyberte **Uložit** na **vlastnosti** stránce skupiny a uložit provedené změny. **Typ členství** skupiny se ihned projeví v seznamu skupiny.

> [!TIP]
> Skupiny převod může selhat, pokud byl rozšířené pravidlo, které jste zadali nesprávný. V pravém dolním rohu portálu, který obsahuje vysvětlení, proč nelze přijmout, pravidlo v systému se zobrazí oznámení. Přečtěte si ho pečlivě, abyste pochopili, jak můžete nastavit pravidlo, aby platný.

### <a name="using-powershell-to-change-membership-management-on-a-group"></a>Chcete-li změnit správy členství ve skupině pomocí prostředí PowerShell

> [!NOTE]
> Chcete-li změnit vlastnosti dynamické skupiny budete muset použít rutiny z **verze preview** [Azure AD PowerShell verze 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Můžete nainstalovat z verze preview [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

Tady je příklad funkcí, které přepínač na existující skupinu pro správu členství. V tomto příkladu je pozor správně pracovat s vlastnost GroupTypes a zároveň zachovat všechny hodnoty, které se nevztahují ke dynamické členství.

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
Chcete-li skupinu statické:
```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```
Chcete-li skupinu dynamické:
```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```
## <a name="next-steps"></a>Další postup
Tyto články poskytují další informace o skupin v Azure Active Directory.

* [Zobrazení existujících skupin](active-directory-groups-view-azure-portal.md)
* [Vytvoření nové skupiny a přidávání členů](active-directory-groups-create-azure-portal.md)
* [Spravovat nastavení skupiny](active-directory-groups-settings-azure-portal.md)
* [Správa členství ve skupině](active-directory-groups-membership-azure-portal.md)
* [Dynamická pravidla pro uživatele ve skupině pro správu](active-directory-groups-dynamic-membership-azure-portal.md)
