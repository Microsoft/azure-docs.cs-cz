---
title: 'Azure AD Connect: Výrazů deklarativního zřizování | Microsoft Docs'
description: Vysvětluje výrazů deklarativního zřizování.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: e3ea53c8-3801-4acf-a297-0fb9bb1bf11d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 12d7e997d82c0ac15d1c81b92491e0a16f4c214e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593669"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Synchronizace Azure AD Connect: Principy výrazů deklarativní zřizování
Synchronizace Azure AD Connect je založený na deklarativní zřizování poprvé dostupné ve verzi produktu Forefront Identity Manager 2010. Umožňuje implementovat obchodní logiku integrace kompletní identity bez nutnosti napsat zkompilovaný kód.

Nedílnou součást vámi vyžádaných deklarativní zřizování je výraz jazyk použitý v toky atributů. Použitý jazyk je podmnožinou Microsoft® Visual Basic for Applications (VBA). Tento jazyk slouží v aplikaci Microsoft Office a uživatelé s prostředím jazyka VBScript také rozpozná ho. Výraz jazyka deklarativní zřizování je jenom pomocí funkce a není strukturovaných jazyk. Neexistují žádné metody nebo příkazy. Na toku express programu místo toho jsou vnořené funkce.

Další podrobnosti najdete v tématu [Vítá vás Visual Basic pro aplikace referenční příručka jazyka pro Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Atributy jsou silného typu. Funkce přijímá pouze atributy správného typu. Je také malá a velká písmena. Názvy funkcí a názvy atributů musí mít správná velká a malá písmena nebo dojde k chybě.

## <a name="language-definitions-and-identifiers"></a>Jazyk definic a identifikátory
* Funkce mít název, za nímž následují argumenty v závorce: %{FunctionName/ (argument 1, argument N).
* Atributy jsou určeny hranaté závorky: [attributeName]
* Parametry jsou určeny znaky procenta: % ParameterName %
* Řetězcové konstanty jsou uzavřeny do uvozovek: například "Contoso" (Poznámka: musíte použít rovné uvozovky "" a není inteligentní uvozovky "")
* Číselné hodnoty jsou vyjádřené bez uvozovek a očekává se decimal. Hexadecimální hodnoty mají předponu & H. Například 98052 & HFF
* Logické hodnoty jsou vyjádřeny pomocí konstant: True, False.
* Předdefinované konstanty a literály jsou vyjádřeny se pouze jejich název: NULL, Line FEED, IgnoreThisFlow

### <a name="functions"></a>Functions
Chcete-li povolit možnost transformace hodnot atributů deklarativní zřizování používá mnoho funkcí. Tyto funkce mohou být vnořené, takže výsledek z jednoho funkce je předán v jiné funkci.

`Function1(Function2(Function3()))`

Úplný seznam funkcí najdete v [funkce odkaz](active-directory-aadconnectsync-functions-reference.md).

### <a name="parameters"></a>Parametry
Parametr je definována pomocí konektoru nebo pomocí správce pomocí prostředí PowerShell. Parametry obvykle obsahují hodnoty, které se liší na systému, například název domény uživatele nachází v. Tyto parametry můžete použít v toky atributů.

Konektor služby Active Directory k dispozici následující parametry pro příchozí pravidla synchronizace:

| Název parametru | Poznámka |
| --- | --- |
| Domain.Netbios |Formát pro rozhraní NetBIOS domény aktuálně importována, například FABRIKAMSALES |
| Domain.FQDN |Plně kvalifikovaný název domény formát domény aktuálně importována, například sales.fabrikam.com |
| Domain.LDAP |Formát LDAP domény aktuálně importována, například řadič domény = prodej, DC = fabrikam, DC = com |
| Forest.Netbios |Formát názvu doménové struktury aktuálně importována, například FABRIKAMCORP pro rozhraní NetBIOS |
| Forest.FQDN |Formát názvu doménové struktury aktuálně importována, třeba fabrikam.com plně kvalifikovaný název domény |
| Forest.LDAP |LDAP formát názvu doménové struktury aktuálně importována, například DC = fabrikam, DC = com |

Systém poskytuje následující parametr, který se použije k získání identifikátor konektoru aktuálně spuštěna:  
`Connector.ID`

Tady je příklad, který naplní atribut úložiště metaverse domény s názvem netbios domény, kde se uživatel nachází:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operátory
Můžete použít následující operátory:

* **Porovnání**: <, < =, <>, =, >, > =
* **Matematika**: +, -, \*, -
* **Řetězec**: & (zřetězení)
* **Logické**: & & (a), || (nebo)
* **Pořadí vyhodnocení**:)

Operátory se vyhodnocují zleva doprava a se stejnou prioritou vyhodnocení. To znamená \* (násobitel), nebude hodnocen před - (odčítání). 2\*(5 + 3) není stejný jako 2\*5 + 3. Chcete-li změnit pořadí vyhodnocení při zleva na pořadí správné vyhodnocení není vhodné se používají hranatých závorek ().

## <a name="multi-valued-attributes"></a>Více hodnot atributů
Funkce mohou pracovat s jednou hodnotou i více hodnot atributů. Pro více hodnot atributů funkce funguje v každé hodnotě a platí stejnou funkci ke každé hodnotě.

Příklad:  
`Trim([proxyAddresses])` Proveďte operace Trim každé hodnoty v atributu proxyAddress.  
`Word([proxyAddresses],1,"@") & "@contoso.com"` Pro každou hodnotu s @-sign, nahraďte doméně s @contoso.com.  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])` Vyhledejte adresu SIP a odebere ji z hodnot.

## <a name="next-steps"></a>Další postup
* Další informace o konfiguraci modelu v [Principy deklarativní zřizování](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* Najdete v tématu Jak deklarativní zřizování je použité out-of-box v [Principy výchozí konfigurace](active-directory-aadconnectsync-understanding-default-configuration.md).
* Informace o tom, praktické změnit pomocí deklarativní zřizování v [jak provést změnu výchozí konfigurace](active-directory-aadconnectsync-change-the-configuration.md).

**Témata s přehledem**

* [Synchronizace Azure AD Connect: pochopení a přizpůsobení synchronizace](active-directory-aadconnectsync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md)

**Témata odkazů**

* [Synchronizace Azure AD Connect: odkaz na funkce](active-directory-aadconnectsync-functions-reference.md)

