---
title: Vlastní ovládací prvky v podmíněném přístupu Azure AD
description: Zjistěte, jak fungují vlastní ovládací prvky v podmíněném přístupu Služby Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/18/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8c149279a755eb186a3fdc7891e9b511d18c7f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050539"
---
# <a name="custom-controls-preview"></a>Vlastní ovládací prvky (náhled)

Vlastní ovládací prvky jsou funkce náhledu služby Azure Active Directory. Při použití vlastních ovládacích prvků jsou uživatelé přesměrováni na kompatibilní službu, aby splňovali požadavky na ověřování mimo službu Azure Active Directory. Aby byl tento ovládací prvek splněn, prohlížeč uživatele je přesměrován na externí službu, provede požadované ověřování a pak bude přesměrován zpět do služby Azure Active Directory. Azure Active Directory ověří odpověď a pokud byl uživatel úspěšně ověřen nebo ověřen, uživatel pokračuje v toku podmíněného přístupu.

> [!NOTE]
> Další informace o změnách, které plánujeme v možnosti vlastní hod, naleznete v únoru 2020 [Co je nová aktualizace](../fundamentals/whats-new.md#upcoming-changes-to-custom-controls).

## <a name="creating-custom-controls"></a>Vytváření vlastních ovládacích prvků

Vlastní ovládací prvky fungují s omezenou sadou schválených poskytovatelů ověřování. Chcete-li vytvořit vlastní ovládací prvek, měli byste nejprve kontaktovat zprostředkovatele, který chcete využít. Každý poskytovatel, který není od společnosti Microsoft, má svůj vlastní proces a požadavky na registraci, přihlášení k odběru nebo jiné vytvoření služby a označení, že chcete integrovat s podmíněným přístupem. V tomto okamžiku vám poskytovatel poskytne blok dat ve formátu JSON. Tato data umožňují zprostředkovateli a podmíněnému přístupu spolupracovat pro vašeho tenanta, vytvoří nový ovládací prvek a definuje, jak podmíněný přístup může zjistit, zda vaši uživatelé úspěšně provedli ověření s poskytovatelem.

Zkopírujte data JSON a vložte je do souvisejícího textového pole. Neprovávejte žádné změny json, pokud explicitně pochopit změny, které provádíte. Provedení jakékoli změny by mohlo přerušit spojení mezi poskytovatelem a společností Microsoft a potenciálně uzamknout vás a vaše uživatele z vašich účtů.

Možnost vytvořit vlastní ovládací prvek je v části **Spravovat** na stránce **Podmíněný přístup.**

![Řízení](./media/controls/82.png)

Klepnutím na **tlačítko Nový vlastní ovládací prvek**otevřete okno s textovým polem pro data JSON ovládacího prvku.  

![Řízení](./media/controls/81.png)

## <a name="deleting-custom-controls"></a>Odstranění vlastních ovládacích prvků

Chcete-li odstranit vlastní ovládací prvek, musíte nejprve zajistit, že není používán v žádné zásady podmíněného přístupu. Po dokončení:

1. Přejít do seznamu Vlastní ovládací prvky
1. Klepněte...  
1. Vyberte **Odstranit**.

## <a name="editing-custom-controls"></a>Úpravy vlastních ovládacích prvků

Chcete-li upravit vlastní ovládací prvek, je nutné odstranit aktuální ovládací prvek a vytvořit nový ovládací prvek s aktualizovanými informacemi.

## <a name="known-limitations"></a>Známá omezení

Vlastní ovládací prvky nelze použít s automatizací identity protection, která vyžaduje azure multi-factor authentication, samoobslužné resetování hesla Azure AD (SSPR), splňující požadavky na deklaraci vícefaktorového ověřování nebo ke zvýšení rolí v privilegovaných Správce identit (PIM).

## <a name="next-steps"></a>Další kroky

- [Podmíněné přístupové běžné zásady](concept-conditional-access-policy-common.md)

- [Režim pouze sestav](concept-conditional-access-report-only.md)

- [Simulovat chování přihlášení pomocí nástroje Co-li podmíněného přístupu](troubleshoot-conditional-access-what-if.md)
