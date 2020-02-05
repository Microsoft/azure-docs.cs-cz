---
title: Podmíněný přístup – blokování starších verzí ověřování – Azure Active Directory
description: Vytvoření vlastních zásad podmíněného přístupu pro blokování starších protokolů ověřování
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0aca5019f4f7fca47195fb8fb821b1af1ae9ec77
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024241"
---
# <a name="conditional-access-block-legacy-authentication"></a>Podmíněný přístup: blokovat starší verze ověřování

V důsledku zvýšeného rizika spojeného se staršími protokoly pro ověřování společnost Microsoft doporučuje, aby organizace zablokovaly žádosti o ověření pomocí těchto protokolů a vyžadovala moderní ověřování.

## <a name="create-a-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu

Následující kroky vám pomůžou vytvořit zásady podmíněného přístupu, které blokují požadavky na starší verzi ověřování.

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte na **Azure Active Directory** > **zabezpečení** > **podmíněný přístup**.
1. Vyberte **nové zásady**.
1. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
1. V části **přiřazení**vyberte **Uživatelé a skupiny** .
   1. V části **Zahrnout**vyberte **Všichni uživatelé**.
   1. V části **vyloučit**vyberte **Uživatelé a skupiny** a zvolte všechny účty, které musí udržovat možnost používat starší verze ověřování. Musíte vyloučit aspoň jeden účet, abyste mohli zabránit jeho uzamknutí. Pokud nevylučujete žádný účet, nebudete moct vytvořit tuto zásadu.
   1. Vyberte **Done** (Hotovo).
1. V části **cloudové aplikace nebo akce** vyberte **všechny cloudové aplikace**.
   1. Vyberte **Done** (Hotovo).
1. V části **podmínky** > **klientské aplikace (Preview)** nastavte **Konfigurovat** na **Ano**.
   1. Zaškrtněte políčka **mobilní aplikace a desktopové klienty** > **jiných klientech**.
   1. Vyberte **Done** (Hotovo).
1. V části **řízení přístupu** > **udělení**vyberte **blokovat přístup**.
   1. Vyberte **vyberte**.
1. Potvrďte nastavení a nastavte **možnost povolit zásadu** na **zapnuto**.
1. Vyberte **vytvořit** a vytvořte tak, aby se zásady povolily.

## <a name="next-steps"></a>Další kroky

[Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

[Určení dopadu pomocí režimu pouze sestavy podmíněného přístupu](howto-conditional-access-report-only.md)

[Simulace chování při přihlašování pomocí nástroje pro What If podmíněného přístupu](troubleshoot-conditional-access-what-if.md)
