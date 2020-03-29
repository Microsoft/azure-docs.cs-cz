---
title: 'Azure AD Connect: Deklarativní výrazy zřizování | Dokumenty společnosti Microsoft'
description: Vysvětluje deklarativní zřizování výrazy.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: e3ea53c8-3801-4acf-a297-0fb9bb1bf11d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdc7c9dba49bf37db1f039d43b0450c65884c74b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60245504"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Synchronizace azure a připojení služby Azure AD: principy výrazů deklarativního zřizování
Synchronizace Azure AD Connect vychází z deklarativního zřizování, které bylo poprvé zavedeno ve Správci identit forefront 2010. Umožňuje implementovat obchodní logiku integrace úplné identity bez nutnosti psát kompilovaný kód.

Podstatnou součástí deklarativního zřizování je jazyk výrazu používaný v tocích atributů. Použitý jazyk je podmnožinou microsoft® Visual Basic® pro aplikace (VBA). Tento jazyk se používá v sadě Microsoft Office a uživatelé se zkušenostmi s VBScript emituje také. Deklarativní zřizování výraz jazyka používá pouze funkce a není strukturovaný jazyk. Neexistují žádné metody nebo příkazy. Funkce jsou místo toho vnořeny pro expresiní toku programu.

Další podrobnosti [najdete v tématu Vítá vás odkaz na jazyk jazyka Visual Basic for Applications pro Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Atributy jsou silně zadali. Funkce přijímá pouze atributy správného typu. To je také malá a velká písmena. Názvy funkcí i názvy atributů musí mít vlastní velikost písmen nebo je vyvolána chyba.

## <a name="language-definitions-and-identifiers"></a>Definice a identifikátory jazyků
* Funkce mají název následovaný argumenty v závorkách: FunctionName(argument 1, argument N).
* Atributy jsou označeny hranami: [attributeName]
* Parametry jsou označeny znaménky procent: %ParameterName%
* Řetězcové konstanty jsou obklopeny uvozovkami: Například "Contoso" (Poznámka: musí používat rovné uvozovky "" a ne inteligentní uvozovky "")
* Číselné hodnoty jsou vyjádřeny bez uvozovek a očekává se, že budou desítkové. Šestnáctkové hodnoty jsou předponou &H. Například 98052, &HFF
* Logické hodnoty jsou vyjádřeny konstantami: True, False.
* Vestavěné konstanty a literály jsou vyjádřeny pouze jejich názvem: NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Funkce
Deklarativní zřizování používá mnoho funkcí k povolení možnosti transformace hodnot atributů. Tyto funkce mohou být vnořeny tak, aby výsledek z jedné funkce byl předán do jiné funkce.

`Function1(Function2(Function3()))`

Kompletní seznam funkcí naleznete v [odkazu na funkci](reference-connect-sync-functions-reference.md).

### <a name="parameters"></a>Parametry
Parametr je definován konektorem nebo správcem používajícím prostředí PowerShell. Parametry obvykle obsahují hodnoty, které se liší od systému k systému, například název domény, ve které se uživatel nachází. Tyto parametry lze použít v tocích atributů.

Konektor služby Active Directory poskytl následující parametry pro pravidla příchozí synchronizace:

| Název parametru | Poznámka |
| --- | --- |
| Doména.Netbios |Netbios formát domény, která je právě importována, například FABRIKAMSALES |
| doména.FQDN |Formát FQDN importované domény, například sales.fabrikam.com |
| Doména.LDAP |Formát LDAP importované domény, například DC=sales,DC=fabrikam,DC=com |
| Forest.Netbios |Netbios formát názvu doménové struktury, který je právě importován, například FABRIKAMCORP |
| Doménové struktury.FQDN |Formát FQDN aktuálně importovaného názvu doménové struktury, například fabrikam.com |
| Doménová struktura.LDAP |Formát LDAP aktuálně importovaného názvu doménové struktury, například DC=fabrikam,DC=com |

Systém poskytuje následující parametr, který se používá k získání identifikátoru aktuálně spuštěného konektoru:  
`Connector.ID`

Zde je příklad, který naplní doménu atributu metaverse názvem netbios domény, ve které je uživatel umístěn:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operátory
Lze použít následující operátory:

* **Porovnání**: <, <=, <>, =, >, >=
* **Matematika**: +, \*-, , -
* **Řetězec**: & (zřetězit)
* **Logické**: && (a), || (nebo)
* **Pořadí hodnocení**: ( )

Operátory jsou hodnoceny zleva doprava a mají stejnou prioritu hodnocení. To znamená, \* že (multiplikátor) není vyhodnocen před - (odčítání). 2\*(5+3) není stejný\*jako 2 5+3. Závorky ( ) se používají ke změně pořadí hodnocení, když pořadí hodnocení zleva doprava není vhodné.

## <a name="multi-valued-attributes"></a>Atributy s více hodnotami
Funkce mohou pracovat s atributy s jednou i s více hodnotami. Pro atributy s více hodnotami funkce pracuje přes každou hodnotu a použije stejnou funkci pro každou hodnotu.

Například:  
`Trim([proxyAddresses])`Proveďte Trim každé hodnoty v proxyAddress atribut.  
`Word([proxyAddresses],1,"@") & "@contoso.com"`Pro každou hodnotu @-signs , @contoso.comnahraďte doménu .  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])`Vyhledejte SIP-adresu a odeberte ji z hodnot.

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o modelu konfigurace v [principu deklarativní zřizování](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Podívejte se, jak se používá deklarativní zřizování out-of-box v [principu výchozí konfigurace](concept-azure-ad-connect-sync-default-configuration.md).
* Podívejte se, jak provést praktickou změnu pomocí deklarativnízři v [jak provést změnu výchozí konfigurace](how-to-connect-sync-change-the-configuration.md).

**Přehledná témata**

* [Synchronizace služby Azure AD Connect: Principy a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)

**Referenční témata**

* [Synchronizace azure apřipojení: odkaz na funkce](reference-connect-sync-functions-reference.md)

