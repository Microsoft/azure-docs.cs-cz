---
title: 'Azure AD Connect: výrazy deklarativního zřizování | Microsoft Docs'
description: Vysvětluje deklarativní zřizovací výrazy.
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
ms.openlocfilehash: 02490839a9e35695ae2e8b3f750e139ad7413aa4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89280209"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Azure AD Connect synchronizace: principy deklarativních zřizovacích výrazů
Azure AD Connect synchronizovat buildy na deklarativní zřizování poprvé představené v Forefront Identity Manageru 2010. Umožňuje implementovat kompletní obchodní logiku pro integraci identit bez nutnosti psaní zkompilovaného kódu.

Důležitou součástí deklarativního zřizování je jazyk výrazů, který se používá v tocích atributů. Použitý jazyk je podmnožinou sady Microsoft® Visual Basic® for Applications (VBA). Tento jazyk se používá v systém Microsoft Office a uživatelé, kteří mají zkušenosti s jazykem VBScript, ho také rozpoznají. Jazyk deklarativního zřizování výrazů používá pouze funkce a není strukturovaným jazykem. Neexistují žádné metody ani příkazy. Funkce jsou místo toho vnořené do programu Express Flow.

Další podrobnosti najdete v tématu [Vítá vás jazyk Visual Basic for Application Reference k jazyku pro Office 2013](/office/vba/api/overview/language-reference).

Atributy jsou silného typu. Funkce přijímá pouze atributy správného typu. Rozlišuje také velká a malá písmena. Názvy funkcí i názvy atributů musí mít správné velikosti písmen nebo je vyvolána chyba.

## <a name="language-definitions-and-identifiers"></a>Jazykové definice a identifikátory
* Funkce mají název následovaný argumenty v závorkách: název funkce (argument 1, argument N).
* Atributy jsou označeny hranatými závorkami: [attributeName]
* Parametry jsou identifikovány pomocí znaků procenta:% ParameterName%
* Řetězcové konstanty jsou obklopené uvozovkami: například "contoso" (Poznámka: musí používat rovné uvozovky "" a ne typografické uvozovky "")
* Číselné hodnoty se vyjadřují bez uvozovek a očekávají se jako desetinné číslo. Šestnáctkové hodnoty mají předponu &H. Například 98052 &HFF
* Logické hodnoty jsou vyjádřeny konstantami: true, false.
* Předdefinované konstanty a literály jsou vyjádřeny pouze pomocí jejich názvu: NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Functions
Deklarativní zřizování používá mnoho funkcí k umožnění možnosti transformace hodnot atributů. Tyto funkce mohou být vnořené, takže výsledek z jedné funkce je předán do jiné funkce.

`Function1(Function2(Function3()))`

Úplný seznam funkcí najdete v [odkazu na funkci](reference-connect-sync-functions-reference.md).

### <a name="parameters"></a>Parametry
Parametr je definován buď pomocí konektoru, nebo správcem pomocí prostředí PowerShell. Parametry obvykle obsahují hodnoty, které se liší od systému k systému, například název domény, ve které je uživatel umístěný. Tyto parametry lze použít v tocích atributů.

Konektor služby Active Directory, který poskytuje následující parametry pro pravidla příchozí synchronizace:

| Název parametru | Komentář |
| --- | --- |
| Doména. NetBIOS |Formát rozhraní NetBIOS aktuálně importované domény, například FABRIKAMSALES |
| Doména. FQDN |Plně kvalifikovaný název domény aktuálně importované domény, například sales.fabrikam.com |
| Doména. LDAP |Formát LDAP aktuálně importované domény, například DC = Sales, DC = Fabrikam, DC = com |
| Doménová struktura. NetBIOS |Formát NetBIOS názvu doménové struktury, který se právě importuje, například FABRIKAMCORP |
| Doménová struktura. plně kvalifikovaný název domény |Formát plně kvalifikovaného názvu domény, který se právě importuje, například fabrikam.com |
| Doménová struktura. LDAP |Formát LDAP pro aktuálně importovaný název doménové struktury, například DC = Fabrikam, DC = com |

Systém poskytuje následující parametr, který slouží k získání identifikátoru aktuálně běžícího konektoru:  
`Connector.ID`

Tady je příklad, který naplní doménu atributu Metaverse názvem domény pro rozhraní NetBIOS, kde se nachází uživatel:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operátory
Můžete použít následující operátory:

* **Porovnání**: <, <=,  <>, =, >, >=
* **Matematické**: +,-, \* ,-
* **Řetězec**: & (zřetězení)
* **Logický**:  &&  (a), | | ani
* **Pořadí vyhodnocování**: ()

Operátory jsou vyhodnoceny zleva doprava a mají stejnou prioritu hodnocení. To znamená, že \* (násobitel) není vyhodnocen před-(odčítání). 2 \* (5 + 3) není totéž jako 2 \* 5 + 3. Hranaté závorky () se používají ke změně pořadí vyhodnocování v případě, že levé a pravé pořadí vyhodnocení není vhodné.

## <a name="multi-valued-attributes"></a>Vícehodnotových atributů
Funkce mohou fungovat jak v atributu s jednou hodnotou, tak i s více hodnotami. U vícehodnotových atributů funkce funguje v každé hodnotě a použije stejnou funkci na každou hodnotu.

Například:  
`Trim([proxyAddresses])` Proveďte oříznutí všech hodnot v atributu proxyAddress.  
`Word([proxyAddresses],1,"@") & "@contoso.com"` U každé hodnoty s hodnotou @-sign nahraďte doménu hodnotou @contoso.com .  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])` Vyhledejte adresu SIP a odeberte ji z hodnot.

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o modelu konfigurace v tématu [Principy deklarativního zřizování](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Podívejte se, jak se v tématu [Principy výchozí konfigurace](concept-azure-ad-connect-sync-default-configuration.md)používá deklarativní zřizování.
* Informace o tom, jak provést praktickou změnu pomocí deklarativního zajišťování, najdete v tématu [Postup provedení změny ve výchozí konfiguraci](how-to-connect-sync-change-the-configuration.md).

**Témata s přehledem**

* [Azure AD Connect synchronizace: pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)

**Referenční témata**

* [Azure AD Connect Sync: Reference k funkcím](reference-connect-sync-functions-reference.md)