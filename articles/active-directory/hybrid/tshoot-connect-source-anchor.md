---
title: 'Azure AD Connect: řešení potíží se zdrojovým kotvou během instalace | Microsoft Docs'
description: Toto téma popisuje kroky pro řešení potíží se zdrojovým kotvou během instalace.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: cb82eb2d8e23daec0ddb8856b713c1aa051f25c5
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2020
ms.locfileid: "85355943"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>Řešení potíží se zdrojovým kotvou během instalace
Tento článek popisuje různé problémy související se zdrojovým ukotvením, ke kterým může dojít během instalace, a nabízí způsoby, jak tyto problémy vyřešit.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Neplatný zdroj kotvy v Azure Active Directory

### <a name="custom-installation"></a>Vlastní instalace

Při vlastní instalaci Azure AD Connect přečte zásadu zdrojového ukotvení z Azure Active Directory. Pokud zásady existují v Azure Active Directory, Azure AD Connect uplatní stejné zásady, pokud je nepřepíše zákazník. Průvodce vás informuje o tom, který atribut byl načten. Kromě toho Průvodce zobrazí upozornění, pokud se pokusíte přepsat zásadu zdrojového ukotvení.

Během této operace čtení je možné, že zásada zdrojového ukotvení v Azure Active Directory není očekávaná. V takovém případě Azure AD Connect neví, co zdrojové ukotvení použít a potřebuje ruční přepsání.</br>
![neočekávané](media/tshoot-connect-source-anchor/source1.png)

Chcete-li tento problém vyřešit, můžete ručně přepsat zdrojové kotvy výběrem konkrétního atributu. Pokračujte s touto možností pouze v případě, že jste si jisti, který atribut chcete vybrat. Pokud si nejste jistí, kontaktujte [podporu Microsoftu](https://support.microsoft.com/contactus/) , kde najdete pokyny. Pokud změníte zásadu zdrojového ukotvení, může to narušit přidružení mezi místními uživateli a jejich přidruženými prostředky Azure.</br>
![neočekávané](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>Expresní instalace
Při expresní instalaci Azure AD Connect čte zásadu zdrojového ukotvení z Azure Active Directory. Pokud zásada existuje v Azure Active Directory, Azure AD Connect použije stejné zásady. Neexistuje žádný způsob, jak provést ruční přepsání.

Během této operace čtení je možné, že zásada zdrojového ukotvení v Azure Active Directory není očekávaná. V takovém případě Azure AD Connect neví, co by mělo být zdrojové ukotvení.</br>
![neočekávané](media/tshoot-connect-source-anchor/source3.png)

Chcete-li tento problém vyřešit, je nutné znovu nainstalovat pomocí vlastního režimu a ručně přepsat zdrojové kotvy výběrem konkrétního atributu. Pokračujte s touto možností pouze v případě, že jste si jisti, který atribut chcete vybrat. Pokud si nejste jistí, kontaktujte [podporu Microsoftu](https://support.microsoft.com/contactus/) , kde najdete pokyny. Pokud změníte zásadu zdrojového ukotvení, může to narušit přidružení mezi místními uživateli a jejich přidruženými prostředky Azure.

### <a name="invalid-source-anchor-in-sync-engine"></a>Neplatná kotva zdroje v synchronizačním modulu
Během instalace je možné Azure AD Connect se pokusí nakonfigurovat synchronizační modul pomocí neplatné zdrojové kotvy. Tato operace je pravděpodobně problémem s produktem a instalace Azure AD Connect se nezdaří. Pokud s tímto problémem spustíte, obraťte se na [podporu Microsoftu](https://support.microsoft.com/contactus/) .</br>
![neočekávané](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
