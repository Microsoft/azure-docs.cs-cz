---
title: Podmíněný přístup – vyžadovat vícefaktorové povolení pro správu Azure – Azure Active Directory
description: Vytvoření vlastní zásady podmíněného přístupu, které vyžadují vícefaktorové ověřování pro úlohy správy Azure
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b92d833e6f32821ad907ff966771bbba8bbb77ce
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755180"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>Podmíněný přístup: Vyžadovat vícefaktorové povolení pro správu Azure

Organizace používají celou řadu služeb Azure a spravují je z nástrojů založených na Azure Resource Manageru, jako jsou:

* portál Azure
* Azure PowerShell
* Azure CLI

Tyto nástroje mohou poskytovat vysoce privilegovaný přístup k prostředkům, které mohou změnit konfiguraci celého předplatného, nastavení služby a fakturaci předplatného. K ochraně těchto privilegovaných prostředků společnost Microsoft doporučuje vyžadovat vícefaktorové ověřování pro všechny uživatele, kteří přistupují k těmto prostředkům.

## <a name="user-exclusions"></a>Vyloučení uživatelů

Zásady podmíněného přístupu jsou výkonné nástroje, doporučujeme z vašich zásad vyloučit následující účty:

* **Nouzové přístup** nebo **break-glass** účty, aby se zabránilo uzamčení účtu pro celý klient. V nepravděpodobném scénáři jsou všichni správci uzamčeni z vašeho tenanta, váš účet pro správu nouzového přístupu lze použít k přihlášení do klienta podniknout kroky k obnovení přístupu.
   * Další informace najdete v článku [Správa účtů pro nouzový přístup ve službě Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Účty služeb** a **instanční objekty**, jako je například účet synchronizace připojení Azure AD Connect. Účty služeb jsou neinteraktivní účty, které nejsou vázány na žádného konkrétního uživatele. Obvykle se používají back-endové služby, které umožňují programový přístup k aplikacím, ale také se používají k přihlášení k systémům pro administrativní účely. Účty služeb, jako jsou tyto by měly být vyloučeny, protože vícefaktorové nelze dokončit programově. Volání ze strany instančních objektů nejsou blokovány podmíněný montovna.
   * Pokud má vaše organizace tyto účty v provozu ve skriptech nebo kódu, zvažte jejich nahrazení [spravovanými identitami](../managed-identities-azure-resources/overview.md). Jako dočasné řešení můžete tyto konkrétní účty vyloučit ze zásady směrného plánu.

## <a name="create-a-conditional-access-policy"></a>Vytvoření zásad podmíněného přístupu

Následující kroky pomohou vytvořit zásadu podmíněného přístupu, která bude vyžadovat, aby tyto přiřazené role správy prováděly vícefaktorové ověřování.

1. Přihlaste se k **portálu Azure** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte na azure **active directory** > **zabezpečení** > podmíněný**přístup**.
1. Vyberte **nové zásady**.
1. Pojmenujte svou zásadu. Doporučujeme organizacím vytvořit smysluplný standard pro názvy svých zásad.
1. V části **Přiřazení**vyberte **Možnost I Uživatelé a skupiny.**
   1. V části **Zahrnout**vyberte **možnost Všichni uživatelé**.
   1. V **části Vyloučit**vyberte Možnost **Uživatelé a skupiny** a zvolte nouzové přístupové nebo nerozbitné účty vaší organizace. 
   1. Vyberte **Done** (Hotovo).
1. V části **Cloudové aplikace nebo akce** > **Zahrnout**vyberte **Vybrat aplikace**, zvolte Microsoft Azure **Management**a vyberte **Vybrat** pak **Hotovo**.
1. V **části Podmínky** > **Klientské aplikace (Preview)** nastavte **Configure** to **Yes**a vyberte **Hotovo**.
1. V části **Access controls** > **Grant**vyberte **Udělit přístup**, Vyžadovat **vícefaktorové ověřování**a vybrat **vybrat**.
1. Potvrďte nastavení a nastavte **možnost Povolit zásady** **na Zapnuto**.
1. Chcete-li vytvořit, vyberte **vytvořit,** chcete-li povolit zásady.

## <a name="next-steps"></a>Další kroky

[Podmíněné přístupové běžné zásady](concept-conditional-access-policy-common.md)

[Určení dopadu pomocí režimu pouze pro sestavu podmíněného přístupu](howto-conditional-access-report-only.md)

[Simulovat chování přihlášení pomocí nástroje Co-li podmíněného přístupu](troubleshoot-conditional-access-what-if.md)
