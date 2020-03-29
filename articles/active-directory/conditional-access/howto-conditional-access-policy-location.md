---
title: Podmíněný přístup – blokování přístupu podle umístění – služba Azure Active Directory
description: Vytvoření vlastní zásady podmíněného přístupu pro blokování přístupu k prostředkům podle umístění IP
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
ms.openlocfilehash: 34b29ceadaaf85e69d1214039fa1b563ed21a77d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295186"
---
# <a name="conditional-access-block-access-by-location"></a>Podmíněný přístup: Blokování přístupu podle umístění

S podmínkou umístění v podmíněném přístupu můžete řídit přístup ke cloudovým aplikacím na základě umístění uživatele v síti. Podmínka umístění se běžně používá k zablokování přístupu ze zemí, kde vaše organizace ví, že provoz by neměl pocházet.

## <a name="define-locations"></a>Definování umístění

1. Přihlaste se k **portálu Azure** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte na **Azure Active Directory** > **Zabezpečení** > **podmíněného přístupu** > **pojmenované umístění**.
1. Zvolte **Nové umístění**.
1. Uveďte název své polohy.
1. Pokud znáte konkrétní externě přístupné rozsahy adres IPv4, které tvoří dané umístění nebo **země/oblasti**, **zvolte rozsahy IP** adres .
   1. Zadejte **rozsahy IP adres** nebo vyberte země nebo **oblasti** pro zadané místo.
      * Pokud zvolíte Země nebo oblasti, můžete volitelně zahrnout neznámé oblasti.
1. Zvolte **Uložit**

Další informace o podmínce umístění v podmíněném přístupu najdete v článku [Jaká je podmínka umístění v podmíněném přístupu služby Azure Active Directory.](location-condition.md)

## <a name="create-a-conditional-access-policy"></a>Vytvoření zásad podmíněného přístupu

1. Přihlaste se k **portálu Azure** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte na azure **active directory** > **zabezpečení** > podmíněný**přístup**.
1. Vyberte **nové zásady**.
1. Pojmenujte svou zásadu. Doporučujeme organizacím vytvořit smysluplný standard pro názvy svých zásad.
1. V části **Přiřazení**vyberte **Možnost I Uživatelé a skupiny.**
   1. V části **Zahrnout**vyberte **možnost Všichni uživatelé**.
   1. Vyberte **Done** (Hotovo).
1. V části **Cloudové aplikace nebo akce** > **Zahrnout**vyberte **Všechny cloudové aplikace**a vyberte **Hotovo**.
1. V **podmínkách** > **Umístění**.
   1. Nastavit **konfiguraci** na **ano**
   1. **Zahrnout** **vybraná vybraná umístění**
   1. Vyberte blokované umístění, které jste pro vaši organizaci vytvořili.
   1. Klepněte na **tlačítko Vybrat** > **hotovo** > **Done**.
1. V **části Podmínky** > **Klientské aplikace (Preview)** nastavte **Configure** to **Yes**a vyberte **Hotovo**.
1. V části > **Blok** **ovládacích prvků přístupu**vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost Povolit zásady** **na Zapnuto**.
1. Chcete-li vytvořit, vyberte **vytvořit,** chcete-li povolit zásady.

## <a name="next-steps"></a>Další kroky

[Podmíněné přístupové běžné zásady](concept-conditional-access-policy-common.md)

[Určení dopadu pomocí režimu pouze pro sestavu podmíněného přístupu](howto-conditional-access-report-only.md)

[Simulovat chování přihlášení pomocí nástroje Co-li podmíněného přístupu](troubleshoot-conditional-access-what-if.md)
