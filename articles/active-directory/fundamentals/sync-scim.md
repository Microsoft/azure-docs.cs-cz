---
title: SCIM synchronizaci s Azure Active Directory
description: Pokyny pro architekturu k dosažení SCIM synchronizace s Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b95aac504bc6ee72c353faecad25384e2dc90840
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96172414"
---
# <a name="scim-synchronization-with-azure-active-directory"></a>SCIM synchronizaci s Azure Active Directory

Systém pro správu identit mezi doménami (SCIM) je otevřený standardní protokol pro automatizaci výměny informací o identitě uživatelů mezi doménami identity a systémy IT. SCIM zajišťuje, že zaměstnanci přidaní do systému HCM (Human kapitálu Management) mají automaticky účty vytvořené ve službě Azure Active Directory (Azure AD) nebo Windows Server Active Directory. Atributy a profily uživatelů se synchronizují mezi těmito dvěma systémy a aktualizuje odebrání uživatelů na základě změny stavu nebo role uživatele.

SCIM je standardizovaná definice dvou koncových bodů: koncový bod "/Users" a koncový bod/groups. K vytváření, aktualizaci a odstraňování objektů využívá běžné operace REST. Používá také předdefinované schéma pro běžné atributy, jako je název skupiny, uživatelské jméno, křestní jméno, příjmení a e-mail. Aplikace, které nabízejí SCIM 2,0 REST API můžou snížit nebo eliminovat přehlednost práce s proprietárními rozhraními API pro správu uživatelů a. Například kterýkoli klient, který odpovídá SCIM, může vytvořit nový uživatel zadáním příspěvku HTTP objektu JSON do koncového bodu/Users. Místo toho, aby pro stejné základní akce bylo nutné trochu odlišné rozhraní API, můžou aplikace, které odpovídají standardu SCIM, okamžitě využít výhod existujících klientů, nástrojů a kódu. 

## <a name="use-when"></a>Použijte, když: 

Chcete automaticky zřídit informace o uživateli ze systému HCM do služby Azure AD a Windows Server Active Directory a v případě potřeby cílové systémy. 

![Diagram architektury](./media/authentication-patterns/scim-auth.png)


## <a name="components-of-system"></a>Součásti systému 

* **HCM System**: aplikace a technologie, které umožňují proces správy lidských zdrojů a postupy, které podporují a automatizují procesy pro personál v životním cyklu zaměstnanců. 

* **Služba zřizování Azure AD**: používá protokol SCIM 2,0 pro Automatické zřizování. Služba se připojí ke koncovému bodu SCIM pro aplikaci a pomocí schématu uživatelského objektu SCIM a rozhraní REST API automatizuje zřizování a zrušení zřizování uživatelů a skupin.  

* **Azure AD**: úložiště uživatelů používané ke správě životního cyklu identit a jejich nároků. 

* **Cílový systém**: aplikace nebo systém, který má koncový bod SCIM a spolupracuje s zřizováním Azure AD za účelem umožnění automatického zřizování uživatelů a skupin.  

## <a name="implement-scim-with-azure-ad"></a>Implementace SCIM s využitím Azure AD 

* [Jak zřizování funguje v Azure AD ](../app-provisioning/how-provisioning-works.md)

* [Správa zřizování uživatelských účtů pro podnikové aplikace v Azure Portal ](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [Vytvoření koncového bodu SCIM a konfigurace zřizování uživatelů pomocí Azure AD  ](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [Dodržování předpisů protokolu SCIM 2,0 ve službě Azure AD Provisioning](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md)