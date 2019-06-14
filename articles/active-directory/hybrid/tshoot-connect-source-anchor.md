---
title: 'Azure AD Connect: Řešení potíží s zdrojového ukotvení při instalaci | Dokumentace Microsoftu'
description: Toto téma popisuje kroky pro řešení potíží s zdrojové ukotvení během instalace.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "62114150"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>Řešení potíží zdrojového ukotvení během instalace
Tento článek vysvětluje rozdíly zdrojové ukotvení související problémy, které mohou nastat během instalace a nabídky způsoby, jak tyto problémy vyřešit.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Neplatné zdrojové ukotvení v Azure Active Directory

### <a name="custom-installation"></a>Vlastní instalace

Při vlastní instalaci služby Azure AD Connect načte zdrojové ukotvení zásady ze služby Azure Active Directory. Pokud tyto zásady existují ve službě Azure Active Directory, Azure AD Connect používá stejné zásady nepřepíšete příslušného zákazníka. Průvodce vás informuje, který atribut byl přečten. Kromě toho Průvodce vás bude varovat, pokud se pokusíte přepsat zásady zdrojové ukotvení.

Během této operace čtení je možné, že zdrojové ukotvení zásady v Azure Active Directory neočekávaný. V tomto případě Azure AD Connect neví, jaké zdrojové ukotvení použití a vyžaduje ruční přepsání.</br>
![unexpected](media/tshoot-connect-source-anchor/source1.png)

Chcete-li vyřešit tento problém, můžete přepsat ručně zdrojové ukotvení tak, že vyberete určitý atribut. Pokračovat s tímto parametrem a pouze v případě jste si jisti, jaké atributu k výběru. Pokud si nejste jistí, obraťte se na [podpory Microsoftu](https://support.microsoft.com/contactus/) pokyny. Pokud změníte zdrojové ukotvení zásady, může to přerušit spojení mezi vaší místní uživatele a jejich souvisejících prostředcích Azure.</br>
![unexpected](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>Expresní instalace
Při Expresní instalace služby Azure AD Connect načte zdrojové ukotvení zásady ze služby Azure Active Directory. Pokud tyto zásady existují ve službě Azure Active Directory, Azure AD Connect používá stejné zásady. Neexistuje žádná možnost provést ruční přepsání.

Během této operace čtení je možné, že zdrojové ukotvení zásady v Azure Active Directory neočekávaný. V tomto případě Azure AD Connect neví, co by měl být zdrojové ukotvení.</br>
![unexpected](media/tshoot-connect-source-anchor/source3.png)

Chcete-li vyřešit tento problém, budete muset znovu nainstalovat pomocí vlastního režimu a ručně přepsat tak, že vyberete konkrétní atribut zdrojového ukotvení. Pokračovat s tímto parametrem a pouze v případě jste si jisti, jaké atributu k výběru. Pokud si nejste jistí, obraťte se na [podpory Microsoftu](https://support.microsoft.com/contactus/) pokyny. Pokud změníte zdrojové ukotvení zásady, může to přerušit spojení mezi vaší místní uživatele a jejich souvisejících prostředcích Azure.

### <a name="invalid-source-anchor-in-sync-engine"></a>Neplatné zdrojové ukotvení v synchronizačního modulu
Během instalace, je možné Azure AD Connect se pokusí nakonfigurovat synchronizační modul pomocí neplatné zdrojové ukotvení. Tato operace je pravděpodobně problému s produktem a instalace služby Azure AD Connect se nezdaří. Kontakt [podpory Microsoftu](https://support.microsoft.com/contactus/) při spuštění těchto potíží.</br>
![unexpected](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).