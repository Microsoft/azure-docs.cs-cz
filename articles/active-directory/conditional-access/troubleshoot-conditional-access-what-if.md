---
title: Poradce při potížích s podmíněným přístupem pomocí nástroje Co-li – Azure Active Directory
description: Kde zjistit, jaké zásady podmíněného přístupu byly použity a proč
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 07/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 661afc08c76c6cde61b02a29a55b4a8bec932e21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73175796"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>Poradce při potížích s podmíněným přístupem pomocí nástroje Co-li

[Nástroj Co Když](what-if-tool.md) v podmíněném přístupu je výkonný, když se snažíte pochopit, proč byla nebo nebyla použita zásada pro uživatele za určitého okolností nebo zda by zásada platila ve známém stavu.

Nástroj Co if se nachází na **webu Azure Portal** > **Azure Active Directory** > **Conditional Access** > **What If**.

![Nástroj Co když podmíněného přístupu ve výchozím stavu](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> Nástroj Co když aktuálně nevyhodnocuje zásady v režimu pouze pro sestavu.

## <a name="gathering-information"></a>Shromažďování informací

Nástroj Co když **vyžaduje,** aby uživatel mohl začít. 

Následující další informace jsou volitelné, ale pomohou zúžit rozsah pro konkrétní případy.

* Cloudové aplikace nebo akce
* IP adresa 
* Země
* Platforma zařízení
* Klientské aplikace (náhled)
* Stav zařízení (náhled) 
* Riziko přihlášení

Tyto informace lze získat od uživatele, jejich zařízení nebo protokolu přihlášení Azure AD.

## <a name="generating-results"></a>Generování výsledků

Zadejte kritéria shromážděná v předchozí části a vyberte **možnost Co když,** chcete-li generovat seznam výsledků. 

V libovolném okamžiku můžete vybrat **Obnovit,** chcete-li vymazat všechny vstupní kritéria a vrátit se do výchozího stavu.

## <a name="evaluating-results"></a>Hodnocení výsledků

### <a name="policies-that-will-apply"></a>Zásady, které budou platit

V tomto seznamu se zobrazí, které zásady podmíněného přístupu by se za daných podmínek použily. Seznam bude obsahovat ovládací prvky grantu i relace, které platí. Mezi příklady patří vyžadování vícefaktorového ověřování pro přístup k určité aplikaci.

### <a name="policies-that-will-not-apply"></a>Zásady, které nebudou platit

V tomto seznamu se zobrazí zásady podmíněného přístupu, které by se nepoužily, pokud by byly použity podmínky. Seznam bude obsahovat všechny zásady a důvod, proč se nevztahují. Příklady zahrnují uživatele a skupiny, které mohou být vyloučeny ze zásady.

## <a name="use-case"></a>Případ použití

Mnoho organizací vytváří zásady založené na umístěních v síti, povoluje důvěryhodná umístění a blokuje umístění, kde by neměl opomíjet přístup.

Chcete-li ověřit, že konfigurace byla provedena vhodně, správce může použít nástroj Co if napodobovat přístup, z umístění, které by mělo být povoleno a z umístění, které by mělo být odepřeno.

![Nástroj Co Když zobrazující výsledky pomocí nástroje Blokovat přístup](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)

V tomto případě by uživateli byl zablokován přístup k jakékoli cloudové aplikaci na jejich cestě do Severní Koreje, protože Contoso zablokoval přístup z tohoto umístění.

Tento test by mohl být rozšířen tak, aby zahrnoval další datové body pro zúžení oboru.

## <a name="next-steps"></a>Další kroky

* [Co je podmíněný přístup?](overview.md)
* [Co je Azure Active Directory Identity Protection?](../identity-protection/overview-v2.md)
* [Co je identita zařízení?](../devices/overview.md)
* [Jak to funguje: Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)
