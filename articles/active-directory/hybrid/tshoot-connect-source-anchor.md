---
title: 'Azure AD Connect: Řešení problémů se zdrojovým ukotvením během instalace | Dokumenty společnosti Microsoft'
description: Toto téma obsahuje postup řešení problémů se zdrojovou kotvou během instalace.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: fac33a01afc2efc1ab06c4783c11f7a089bb6208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "62114150"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>Poradce při potížích s ukotvením zdroje během instalace
Tento článek vysvětluje různé problémy související se zdrojovou kotvou, ke kterým může dojít během instalace, a nabízí způsoby, jak tyto problémy vyřešit.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Neplatná zdrojová kotva ve službě Azure Active Directory

### <a name="custom-installation"></a>Vlastní instalace

Během vlastní instalace Azure AD Connect přečte zásady zdrojové kotvy z Azure Active Directory. Pokud zásady existují ve službě Azure Active Directory, Azure AD Connect použije stejné zásady, pokud je přepsána zákazníkem. Průvodce vás informuje, který atribut byl přečten. Průvodce navíc upozorní, pokud se pokusíte přepsat zásady ukotvení zdroj.

Během této operace čtení je možné, že zásady ukotvení zdrojve službě Azure Active Directory je neočekávané. V takovém případě Azure AD Connect neví, co zdroj kotvy použít a potřebuje ruční přepsání.</br>
![Neočekávané](media/tshoot-connect-source-anchor/source1.png)

Chcete-li tento problém vyřešit, můžete ručně přepsat zdrojovou kotvu výběrem konkrétního atributu. Pokračujte touto možností pouze v případě, že jste si jisti, který atribut vybrat. Pokud si nejste jisti, obraťte se na [podporu společnosti Microsoft](https://support.microsoft.com/contactus/) pro pokyny. Pokud změníte zásady ukotvení zdroje, může přerušit přidružení mezi místními uživateli a jejich přidruženými prostředky Azure.</br>
![Neočekávané](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>Expresní instalace
Během expresní instalace Azure AD Connect čte zásady zdrojové kotvy z Azure Active Directory. Pokud zásady existují ve službě Azure Active Directory, Azure AD Connect použije stejnou zásadu. Neexistuje žádná možnost provést ruční přepsání.

Během této operace čtení je možné, že zásady ukotvení zdrojve službě Azure Active Directory je neočekávané. V tomto případě Azure AD Connect neví, co by měla být zdrojová kotva.</br>
![Neočekávané](media/tshoot-connect-source-anchor/source3.png)

Chcete-li tento problém vyřešit, je třeba znovu nainstalovat pomocí vlastního režimu a ručně přepsat zdrojovou kotvu výběrem konkrétního atributu. Pokračujte touto možností pouze v případě, že jste si jisti, který atribut vybrat. Pokud si nejste jisti, obraťte se na [podporu společnosti Microsoft](https://support.microsoft.com/contactus/) pro pokyny. Pokud změníte zásady ukotvení zdroje, může přerušit přidružení mezi místními uživateli a jejich přidruženými prostředky Azure.

### <a name="invalid-source-anchor-in-sync-engine"></a>Neplatná zdrojová kotva v synchronizačním modulu
Během instalace je možné, že se Azure AD Connect pokusí nakonfigurovat synchronizační modul pomocí neplatné zdrojové kotvy. Tato operace je s největší pravděpodobností problém produktu a instalace Azure AD Connect se nezdaří. Obraťte se na [podporu společnosti Microsoft,](https://support.microsoft.com/contactus/) pokud spustíte v tomto problému.</br>
![Neočekávané](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).