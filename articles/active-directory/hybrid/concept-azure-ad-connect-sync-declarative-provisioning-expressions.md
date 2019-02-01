---
title: 'Azure AD Connect: Deklarativní zřizování výrazů | Dokumentace Microsoftu'
description: Vysvětluje výrazů deklarativního zřizování.
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
ms.openlocfilehash: a18b80677a67960a0b0c323280264bff2ffa8371
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55489468"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Synchronizace Azure AD Connect: Principy výrazů deklarativního zřizování
Synchronizace Azure AD Connect navazuje na deklarativní zřizování poprvé byla představena v produktu Forefront Identity Manager 2010. Umožňuje implementovat obchodní logiku integrace naprosto bez nutnosti psát zkompilovaný kód.

Nedílnou součást deklarativní zřizování je jazyk výrazů používaných pro toky atributů. Jazyk používaný je podmnožinou Microsoft® Visual Basic for Applications (VBA). Tento jazyk se používá v aplikaci Microsoft Office a uživatelé s prostředím VBScript bude také rozpoznají. Výraz jazyka deklarativní zřizování používá pouze funkce a strukturované jazyk není. Neexistují žádné metody nebo příkazy. Funkce jsou místo toho vnořit do toku výslovné programu.

Další podrobnosti najdete v tématu [Vítá vás Visual Basic pro referenční dokumentace jazyka aplikace pro Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Atributy jsou silného typu. Funkce přijímá pouze atributy nesprávného typu. Také je velká a malá písmena. Názvy funkcí a názvy atributů musí mít správná velká a malá písmena nebo dojde k chybě.

## <a name="language-definitions-and-identifiers"></a>Jazyk definice a identifikátory.
* Funkce mají název, za nímž následuje argumenty v závorkách: FunctionName (argument 1, argument N).
* Atributy jsou určeny hranaté závorky: [attributeName]
* Parametry jsou označeny podle procenta: ParameterName %
* Řetězcové konstanty jsou uzavřeny v uvozovkách: Například "Contoso" (Poznámka: musíte použít rovné uvozovky "" a není inteligentní uvozovky "")
* Číselné hodnoty jsou vyjádřené bez uvozovek a být desítkové. Šestnáctkové hodnoty mají předponu & H. Například 98052 & HFF
* Logické hodnoty jsou vyjádřeny pomocí konstant: Hodnota TRUE, False.
* Předdefinované konstanty a literály jsou vyjádřeny včetně pouze jejich jména: Hodnota NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Functions
Deklarativní zřizování využívá mnoho funkcí k zajištění možnost transformovat hodnoty atributů. Tyto funkce mohou být vnořené, takže výsledkem jedna funkce je předáno do jiné funkce.

`Function1(Function2(Function3()))`

Úplný seznam funkcí najdete v [funkce odkaz](reference-connect-sync-functions-reference.md).

### <a name="parameters"></a>Parametry
Definování získá parametr konektor nebo správce pomocí prostředí PowerShell. Parametry obvykle obsahují hodnoty, které se liší mezi různými systémy, třeba název domény uživatele se nachází v. Tyto parametry můžete použít v toky atributů.

Konektor služby Active Directory k dispozici následující parametry pro příchozí pravidla synchronizace:

| Název parametru | Poznámka |
| --- | --- |
| Domain.Netbios |Formátu NetBIOS domény aktuálně importována, například FABRIKAMSALES |
| Domain.FQDN |Plně kvalifikovaný název domény formát domény aktuálně importována, například sales.fabrikam.com |
| Domain.LDAP |Formát protokolu LDAP import aktuální, například Řadiči domény = prodej, DC = fabrikam, DC = com |
| Forest.Netbios |NetBIOS formát názvu doménové struktury import aktuální, například FABRIKAMCORP |
| Forest.FQDN |Plně kvalifikovaný název domény formát názvu doménové struktury import aktuální, třeba fabrikam.com |
| Forest.LDAP |LDAP formát názvu doménové struktury import aktuální, například DC = fabrikam, DC = com |

Systém poskytuje následující parametr, který se používá ke stahování identifikátor konektoru aktuálně spuštěné:  
`Connector.ID`

Tady je příklad, který naplní domény atribut úložiště metaverze s názvem netbios domény, ve kterém se uživatel zdržuje:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operátory
Můžete použít následující operátory:

* **Porovnání**: <, < =, <>, =, >, > =
* **Matematické**: +, -, \*, -
* **Řetězec**: & (zřetězení)
* **Logické**: & & (a), || (nebo)
* **Pořadí vyhodnocení**:)

Operátory jsou vyhodnocovány zleva doprava a mají stejnou prioritu hodnocení. To znamená \* (multiplikátor) není vyhodnocen před - (odčítání). 2\*(5 + 3) není stejný jako 2\*5 + 3. Chcete-li změnit pořadí vyhodnocování zleva doprava evaluationorder není vhodné se používají závorky ().

## <a name="multi-valued-attributes"></a>Více jednohodnotových atributů
Funkce může pracovat na atributy jednohodnotové i více Vážíme si toho. Pro více jednohodnotových atributů funkce funguje každá hodnota a má stejnou funkci se vztahuje ke každé hodnotě.

Příklad:  
`Trim([proxyAddresses])` Proveďte uvolnění dočasné paměti každé hodnoty v atribut proxyAddress.  
`Word([proxyAddresses],1,"@") & "@contoso.com"` Pro každou hodnotu s @-sign, nahraďte domény s @contoso.com.  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])` Hledat adresy SIP a odebere ji z hodnot.

## <a name="next-steps"></a>Další postup
* Další informace o konfiguraci modelu v [Principy deklarativní zřizování](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Viz jak deklarativní zřizování je použité out-of-box v [Principy výchozí konfigurace](concept-azure-ad-connect-sync-default-configuration.md).
* Informace o tom, aby praktické změnu pomocí deklarativní zřizování v [jak provést změnu výchozí konfigurace](how-to-connect-sync-change-the-configuration.md).

**Témata s přehledem**

* [Synchronizace Azure AD Connect: Pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)

**Referenční témata**

* [Synchronizace Azure AD Connect: Reference k funkcím](reference-connect-sync-functions-reference.md)

