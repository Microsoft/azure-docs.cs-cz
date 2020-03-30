---
title: Podmíněný přístup – blokovat starší ověřování – Služba Azure Active Directory
description: Vytvoření vlastní zásady podmíněného přístupu pro blokování starších ověřovacích protokolů
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a868c8199ac34a498a280e2522d6b1e4c7ec370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295220"
---
# <a name="conditional-access-block-legacy-authentication"></a>Podmíněný přístup: Blokovat starší verze ověřování

Vzhledem ke zvýšenému riziku spojenému se staršími ověřovacími protokoly společnost Microsoft doporučuje organizacím blokovat požadavky na ověření pomocí těchto protokolů a vyžadovat moderní ověřování.

## <a name="create-a-conditional-access-policy"></a>Vytvoření zásad podmíněného přístupu

Následující kroky vám pomohou vytvořit zásady podmíněného přístupu pro blokování starších požadavků na ověření. Tato zásada je uvedena do [režimu pouze sestavy,](howto-conditional-access-report-only.md) aby se spustila, aby správci mohli určit dopad, který budou mít na stávající uživatele. Pokud správci jsou pohodlné, že zásady platí tak, jak mají v úmyslu, mohou přepnout **na on** nebo fázi nasazení přidáním konkrétní skupiny a vyloučení mj.

1. Přihlaste se k **portálu Azure** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte na azure **active directory** > **zabezpečení** > podmíněný**přístup**.
1. Vyberte **nové zásady**.
1. Pojmenujte svou zásadu. Doporučujeme organizacím vytvořit smysluplný standard pro názvy svých zásad.
1. V části **Přiřazení**vyberte **Možnost I Uživatelé a skupiny.**
   1. V části **Zahrnout**vyberte **možnost Všichni uživatelé**.
   1. V **části Vyloučit**vyberte Možnost **Uživatelé a skupiny** a zvolte všechny účty, které si musí zachovat možnost používat starší verze ověřování. Vylučte alespoň jeden účet, abyste zabránili uzamčení. Pokud nevyloučíte žádný účet, nebudete moci vytvořit tuto zásadu.
   1. Vyberte **Done** (Hotovo).
1. V části **Cloudové aplikace nebo akce**vyberte **Všechny cloudové aplikace**.
   1. Vyberte **Done** (Hotovo).
1. V **části Podmínky** > **Klientské aplikace (preview)** nastavte **nastavit konfigurovat** na **ano**.
   1. Zaškrtněte pouze políčka **Mobilní aplikace a klienti** > pro stolní počítače**Ostatní klienti**.
   1. Vyberte **Done** (Hotovo).
1. V části **Access controls** > **Grant**vyberte Blokovat **přístup**.
   1. Vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost Povolit zásady** pouze pro **hlášení**.
1. Chcete-li vytvořit, vyberte **vytvořit,** chcete-li povolit zásady.

## <a name="next-steps"></a>Další kroky

[Podmíněné přístupové běžné zásady](concept-conditional-access-policy-common.md)

[Určení dopadu pomocí režimu pouze pro sestavu podmíněného přístupu](howto-conditional-access-report-only.md)

[Simulovat chování přihlášení pomocí nástroje Co-li podmíněného přístupu](troubleshoot-conditional-access-what-if.md)
