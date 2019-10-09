---
title: Podmíněný přístup – blokování starších verzí ověřování – Azure Active Directory
description: Vytvoření vlastních zásad podmíněného přístupu pro blokování starších protokolů ověřování
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ef8ab4b24d223f372ae3704b00ba6ff090fb039
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169991"
---
# <a name="conditional-access-block-legacy-authentication"></a>Podmíněný přístup: blokovat starší verze ověřování

V důsledku zvýšeného rizika spojeného se staršími protokoly pro ověřování společnost Microsoft doporučuje, aby organizace zablokovaly žádosti o ověření pomocí těchto protokolů a vyžadovala moderní ověřování.

## <a name="create-a-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu

Následující kroky vám pomůžou vytvořit zásady podmíněného přístupu, které blokují požadavky na starší verzi ověřování.

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte na **Azure Active Directory** **podmíněný přístup** > .
1. Vyberte **nové zásady**.
1. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
1. V části **přiřazení**vyberte **Uživatelé a skupiny** .
   1. V části **Zahrnout**vyberte **Všichni uživatelé**.
   1. V části **vyloučit**vyberte **Uživatelé a skupiny** a zvolte všechny účty, které musí udržovat možnost používat starší verze ověřování. 
   1. Vyberte **Hotovo**.
1. V části **cloudové aplikace nebo akce**@no__t-1 zadejte **všechny cloudové aplikace**.
   1. Pokud musíte z vaší zásady vyloučit konkrétní aplikace, můžete je vybrat z karty **vyloučit** v části **Vybrat vyloučené cloudové aplikace** a zvolit **Vybrat**.
   1. Vyberte **Hotovo**.
1. V části **podmínky**@no__t**klientských aplikacích – 1 (Preview)** nastavte **Konfigurovat** na **Ano**.
   1. Zaškrtněte políčka **mobilní aplikace a desktopové klienty** > **ostatní klienti**.
   2. Vyberte **Hotovo**.
1. V části **řízení přístupu**@no__t**udělení**-1 vyberte **blokovat přístup**.
   1. Vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost povolit zásadu** na **zapnuto**.
1. Vyberte **vytvořit** a vytvořte tak, aby se zásady povolily.

## <a name="next-steps"></a>Další kroky

[Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

[Simulace chování při přihlašování pomocí nástroje pro What If podmíněného přístupu](troubleshoot-conditional-access-what-if.md)
