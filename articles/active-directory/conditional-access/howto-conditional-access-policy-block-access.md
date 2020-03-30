---
title: Podmíněný přístup – blokový přístup – služba Azure Active Directory
description: Vytvoření vlastní zásady podmíněného přístupu
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb,
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdbf4f352bc7d6874cab9c12adf5f1717d4b8f9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295725"
---
# <a name="conditional-access-block-access"></a>Podmíněný přístup: Blokovat přístup

Pro organizace s přístupem konzervativní migrace do cloudu je blok ována všechny zásady, které lze použít. 

> [!CAUTION]
> Nesprávnou konfiguraci zásad bloku může vést k tomu, že organizace budou uzamčeny z portálu Azure.

Politiky, jako jsou tyto, mohou mít nezamýšlené vedlejší účinky. Správné testování a validace jsou životně důležité před povolením. Správci by měli při provádění změn využívat nástroje, jako je [režim pouze pro funkci Podmíněný přístup](concept-conditional-access-report-only.md) a nástroj Co když v [podmíněném přístupu.](what-if-tool.md)

## <a name="user-exclusions"></a>Vyloučení uživatelů

Zásady podmíněného přístupu jsou výkonné nástroje, doporučujeme z vašich zásad vyloučit následující účty:

* **Nouzové přístup** nebo **break-glass** účty, aby se zabránilo uzamčení účtu pro celý klient. V nepravděpodobném scénáři jsou všichni správci uzamčeni z vašeho tenanta, váš účet pro správu nouzového přístupu lze použít k přihlášení do klienta podniknout kroky k obnovení přístupu.
   * Další informace najdete v článku [Správa účtů pro nouzový přístup ve službě Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Účty služeb** a **instanční objekty**, jako je například účet synchronizace připojení Azure AD Connect. Účty služeb jsou neinteraktivní účty, které nejsou vázány na žádného konkrétního uživatele. Obvykle jsou používány back-endovými službami a umožňují programový přístup k aplikacím. Účty služeb by měly být vyloučeny, protože vícefaktorové řízení nelze dokončit programově.
   * Pokud má vaše organizace tyto účty v provozu ve skriptech nebo kódu, zvažte jejich nahrazení [spravovanými identitami](../managed-identities-azure-resources/overview.md). Jako dočasné řešení můžete tyto konkrétní účty vyloučit ze zásady směrného plánu.

## <a name="create-a-conditional-access-policy"></a>Vytvoření zásad podmíněného přístupu

Následující kroky vám pomohou vytvořit zásady podmíněného přístupu, které zablokují přístup ke všem aplikacím kromě [Office 365,](concept-conditional-access-cloud-apps.md#office-365-preview) pokud uživatelé nejsou v důvěryhodné síti. Tyto zásady jsou uvedeny do [režimu pouze sestavy](howto-conditional-access-report-only.md) ke spuštění, takže správci mohou určit dopad, který budou mít na stávající uživatele. Pokud správci jsou spokojeni s tím, že zásady platí tak, jak hodlají, mohou je přepnout **na Zapnuto**.

První zásady blokují přístup ke všem aplikacím kromě aplikací Office 365, pokud nejsou na důvěryhodném místě.

1. Přihlaste se k **portálu Azure** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte na azure **active directory** > **zabezpečení** > podmíněný**přístup**.
1. Vyberte **nové zásady**.
1. Pojmenujte svou zásadu. Doporučujeme organizacím vytvořit smysluplný standard pro názvy svých zásad.
1. V části **Přiřazení** vyberte **Uživatelé a skupiny**.
   1. V části **Zahrnout**vyberte **možnost Všichni uživatelé**.
   1. V **části Vyloučit**vyberte Možnost **Uživatelé a skupiny** a zvolte nouzové přístupové nebo nerozbitné účty vaší organizace. 
   1. Vyberte **Done** (Hotovo).
1. V části **Cloudové aplikace nebo akce**vyberte následující možnosti:
   1. V části **Zahrnout**vyberte **Všechny cloudové aplikace**.
   1. V části **Vyloučit**vyberte **Office 365 (náhled),** vyberte **Vybrat**a pak vyberte **Hotovo**.
1. Za **podmínek**:
   1. V **podmínkách** > **Umístění**.
      1. Nastavit **konfiguraci** na **ano**
      1. V části **Zahrnout**vyberte **libovolné umístění**.
      1. V části **Vyloučit**vyberte **Všechna důvěryhodná umístění**.
      1. Vyberte **Done** (Hotovo).
   1. V části **Klientské aplikace (Preview)** nastavte **Možnost Konfigurovat** na **Ano**a vyberte **Hotovo** **a**hotovo .
1. V části **Access controls** > **Grant**vyberte **Blokovat přístup**a pak vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost Povolit zásady** pouze pro **hlášení**.
1. Chcete-li vytvořit, vyberte **vytvořit,** chcete-li povolit zásady.

Druhá zásada je vytvořena níže, která vyžaduje vícefaktorové ověřování nebo kompatibilní zařízení pro uživatele Office 365.

1. Vyberte **nové zásady**.
1. Pojmenujte svou zásadu. Doporučujeme organizacím vytvořit smysluplný standard pro názvy svých zásad.
1. V části **Přiřazení** vyberte **Uživatelé a skupiny**.
   1. V části **Zahrnout**vyberte **možnost Všichni uživatelé**.
   1. V **části Vyloučit**vyberte Možnost **Uživatelé a skupiny** a zvolte nouzové přístupové nebo nerozbitné účty vaší organizace. 
   1. Vyberte **Done** (Hotovo).
1. V části **Cloudové aplikace nebo akce** > **Zahrnout**vyberte **Vybrat aplikace**, zvolte Office **365 (náhled)** a vyberte **Vybrat**a pak **Hotovo**.
1. V části **Access controls** > **Grant**vyberte **Udělit přístup**.
   1. Vyberte **Vyžadovat vícefaktorové ověřování** a **Vyžadovat, aby zařízení bylo označeno jako vyhovující,** **vyberte Vybrat**.
   1. Ujistěte se, že je **vybrány všechny vybrané ovládací prvky.**
   1. Vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost Povolit zásady** pouze pro **hlášení**.
1. Chcete-li vytvořit, vyberte **vytvořit,** chcete-li povolit zásady.

## <a name="next-steps"></a>Další kroky

[Podmíněné přístupové běžné zásady](concept-conditional-access-policy-common.md)

[Určení dopadu pomocí režimu pouze pro sestavu podmíněného přístupu](howto-conditional-access-report-only.md)

[Simulovat chování přihlášení pomocí nástroje Co-li podmíněného přístupu](troubleshoot-conditional-access-what-if.md)
