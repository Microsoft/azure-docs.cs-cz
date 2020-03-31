---
title: Podmíněný přístup – vyžadovat kompatibilní zařízení – Azure Active Directory
description: Vytvoření vlastní zásady podmíněného přístupu, které vyžadují kompatibilní zařízení
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
ms.openlocfilehash: cc6bd486c1e8338eaf875c7026764c80d49e2f05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295208"
---
# <a name="conditional-access-require-compliant-devices"></a>Podmíněný přístup: Vyžadovat kompatibilní zařízení

Organizace, které nasadily Microsoft Intune, můžou informace vrácené ze svých zařízení použít k identifikaci zařízení, která splňují požadavky na dodržování předpisů, jako jsou:

* Vyžadování kódu PIN k odemknutí
* Vyžadování šifrování zařízení
* Vyžadování minimální nebo maximální verze operačního systému
* Vyžadování zařízení není jailbroken nebo zakořeněné

Tyto informace o dodržování zásad se předávají do služby Azure AD, kde podmíněný přístup může rozhodovat o udělení nebo zablokování přístupu k prostředkům. Další informace o zásadách dodržování předpisů zařízení najdete v článku [Nastavení pravidel pro zařízení, která umožňují přístup k prostředkům ve vaší organizaci pomocí Intune.](/intune/protect/device-compliance-get-started)

## <a name="create-a-conditional-access-policy"></a>Vytvoření zásad podmíněného přístupu

Následující kroky vám pomohou vytvořit zásady podmíněného přístupu, které vyžadují, aby zařízení, která přistupují k prostředkům, byla označena jako kompatibilní se zásadami dodržování předpisů Intune vaší organizace.

1. Přihlaste se k **portálu Azure** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte na azure **active directory** > **zabezpečení** > podmíněný**přístup**.
1. Vyberte **nové zásady**.
1. Pojmenujte svou zásadu. Doporučujeme organizacím vytvořit smysluplný standard pro názvy svých zásad.
1. V části **Přiřazení**vyberte **Možnost I Uživatelé a skupiny.**
   1. V části **Zahrnout**vyberte **možnost Všichni uživatelé**.
   1. V **části Vyloučit**vyberte Možnost **Uživatelé a skupiny** a zvolte nouzové přístupové nebo nerozbitné účty vaší organizace. 
   1. Vyberte **Done** (Hotovo).
1. V části **Cloudové aplikace nebo akce** > **Zahrnout**vyberte **Všechny cloudové aplikace**.
   1. Pokud musíte ze zásad vyloučit konkrétní aplikace, můžete je vybrat z karty **Vyloučit** v části **Vybrat vyloučené cloudové aplikace** a zvolit **Vybrat**.
   1. Vyberte **Done** (Hotovo).
1. V **části Podmínky** > **Klientské aplikace (Preview)** nastavte **Configure** to **Yes**a vyberte **Hotovo**.
1. V části **Access controls** > **Grant**vyberte **Vyžadovat, aby zařízení bylo označeno jako vyhovující**.
   1. Vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost Povolit zásady** **na Zapnuto**.
1. Chcete-li vytvořit, vyberte **vytvořit,** chcete-li povolit zásady.

> [!NOTE]
> Nová zařízení můžete do Intune zaregistrovat, i když vyberete **Vyžadovat, aby zařízení bylo označeno jako kompatibilní** pro **všechny uživatele** a **všechny cloudové aplikace,** pomocí výše uvedených kroků. **Vyžadovat, aby zařízení bylo označeno jako kompatibilní** ovládací prvek, registraci Intune neblokuje. 

### <a name="known-behavior"></a>Známé chování

Ve Windows 7, iOS, Android, macOS a některé webové prohlížeče třetích stran Azure AD identifikuje zařízení pomocí klientského certifikátu, který se zřídí, když je zařízení registrované ve službě Azure AD. Při prvním přihlášení uživatele prostřednictvím prohlížeče je uživatel vyzván k výběru certifikátu. Koncový uživatel musí vybrat tento certifikát, aby mohl pokračovat v používání prohlížeče.

## <a name="next-steps"></a>Další kroky

[Podmíněné přístupové běžné zásady](concept-conditional-access-policy-common.md)

[Určení dopadu pomocí režimu pouze pro sestavu podmíněného přístupu](howto-conditional-access-report-only.md)

[Simulovat chování přihlášení pomocí nástroje Co-li podmíněného přístupu](troubleshoot-conditional-access-what-if.md)

[Zásady dodržování předpisů zařízení fungují ve službě Azure AD](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
