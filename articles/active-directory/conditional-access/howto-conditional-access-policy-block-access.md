---
title: Podmíněný přístup – blokový přístup – Azure Active Directory
description: Vytvoření vlastních zásad podmíněného přístupu pro
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb,
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e0801daa5bf83889be87987d440e377287b5ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92366186"
---
# <a name="conditional-access-block-access"></a>Podmíněný přístup: blokovat přístup

Pro organizace s konzervativním přístupem k migraci do cloudu je možnost blokovat všechny zásady možnost, kterou lze použít. 

> [!CAUTION]
> Nespravovaná konfigurace zásad blokování může vést k zamčení organizací, které se Azure Portal.

Tyto zásady můžou mít nezamýšlené vedlejší účinky. Před povolením je vhodné testování a ověřování důležité. Při provádění změn by správci měli používat nástroje, jako je [režim pouze pro vytváření sestav podmíněného přístupu](concept-conditional-access-report-only.md) a [Nástroj what if v podmíněném přístupu](what-if-tool.md) .

## <a name="user-exclusions"></a>Vyloučení uživatelů

Zásady podmíněného přístupu jsou výkonné nástroje. doporučujeme, abyste z zásad vyloučili následující účty:

* **Nouzový přístup** nebo účty pro **přerušení** , které zabrání uzamknutí účtu na úrovni tenanta. V nepravděpodobném scénáři jsou všichni správci zamčeni z vašeho tenanta, účet pro správu pro nouzový přístup se dá použít k přihlášení k tenantovi a přijímá kroky pro obnovení přístupu.
   * Další informace najdete v článku [Správa účtů pro nouzový přístup ve službě Azure AD](../roles/security-emergency-access.md).
* **Účty služeb** a **instanční objekty**, jako je například účet Azure AD Connect Sync. Účty služeb jsou neinteraktivní účty, které nejsou vázány na konkrétního uživatele. Obvykle jsou používány back-endové služby, které umožňují programový přístup k aplikacím, ale používají se také pro přihlášení k systémům pro účely správy. Účty služby by měly být vyloučené, protože MFA nelze dokončit programově. Podmíněný přístup neblokuje volání prováděná instančními objekty.
   * Pokud má vaše organizace tyto účty používané ve skriptech nebo v kódu, zvažte jejich nahrazení pomocí [spravovaných identit](../managed-identities-azure-resources/overview.md). Jako dočasné řešení můžete tyto konkrétní účty z základní zásady vyloučit.

## <a name="create-a-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu

Následující kroky vám pomůžou vytvořit zásady podmíněného přístupu, které budou blokovat přístup ke všem aplikacím kromě [Office 365](concept-conditional-access-cloud-apps.md#office-365) , pokud uživatelé nejsou v důvěryhodné síti. Tyto zásady jsou uváděné do [režimu pouze](howto-conditional-access-insights-reporting.md) pro spouštění sestav, aby správci mohli určit dopad, který budou mít u stávajících uživatelů. Když mají správci možnost si být jistí, že se zásady použijí podle svých záměrů, můžou je přepnout na **zapnuto**.

První zásada blokuje přístup ke všem aplikacím s výjimkou Microsoft 365ch aplikací, pokud nejsou v důvěryhodném umístění.

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Vyhledejte **Azure Active Directory**  >    >  **podmíněný přístup** zabezpečení.
1. Vyberte **nové zásady**.
1. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
1. V části **Přiřazení** vyberte **Uživatelé a skupiny**.
   1. V části **Zahrnout** vyberte **Všichni uživatelé**.
   1. V části **vyloučit** vyberte **Uživatelé a skupiny** a zvolte účty pro nouzový přístup nebo rozklad vaší organizace. 
   1. Vyberte **Hotovo**.
1. V části **cloudové aplikace nebo akce** vyberte následující možnosti:
   1. V části **Zahrnout** vyberte **všechny cloudové aplikace**.
   1. V části **vyloučit** vyberte možnost **Office 365**, vyberte **možnost vybrat** a pak vyberte možnost **Hotovo**.
1. V části **podmínky**:
   1. V části  >  **umístění** podmínek.
      1. Nastavte **Konfigurovat** na **Ano** .
      1. V části **Zahrnout** vyberte **libovolné umístění**.
      1. V části **vyloučit** vyberte **všechna důvěryhodná umístění**.
      1. Vyberte **Hotovo**.
   1. V části **klientské aplikace (Preview)** nastavte **Konfigurovat** na **Ano** a vyberte **Hotovo** a **Hotovo**.
1. V části **řízení přístupu**  >  **udělení** přístupu vyberte **blokovat přístup** a pak vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost povolit zásady** na **pouze sestavy**.
1. Vyberte **vytvořit** a vytvořte tak, aby se zásady povolily.

Níže je vytvořena druhá zásada, která vyžaduje vícefaktorové ověřování nebo zařízení vyhovující předpisům pro uživatele Microsoft 365.

1. Vyberte **nové zásady**.
1. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
1. V části **Přiřazení** vyberte **Uživatelé a skupiny**.
   1. V části **Zahrnout** vyberte **Všichni uživatelé**.
   1. V části **vyloučit** vyberte **Uživatelé a skupiny** a zvolte účty pro nouzový přístup nebo rozklad vaší organizace. 
   1. Vyberte **Hotovo**.
1. V části **cloudové aplikace nebo akce**  >  vyberte **vybrat aplikace**, zvolte **Office 365** a pak vyberte **Vybrat** a pak **Hotovo**.
1. V části **řízení přístupu**  >  **udělení** přístupu vyberte **udělit přístup**.
   1. Vyberte možnost **požadovat vícefaktorové ověřování** a **vyžadovat, aby zařízení byla označena jako vyhovující** vyberte vybrat. 
   1. Ujistěte se, že je vybrána možnost **vyžadovat všechny vybrané ovládací prvky** .
   1. Vyberte **Vybrat**.
1. Potvrďte nastavení a nastavte **možnost povolit zásady** na **pouze sestavy**.
1. Vyberte **vytvořit** a vytvořte tak, aby se zásady povolily.

## <a name="next-steps"></a>Další kroky

[Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

[Určení dopadu pomocí režimu pouze sestavy podmíněného přístupu](howto-conditional-access-insights-reporting.md)

[Simulace chování při přihlašování pomocí nástroje pro What If podmíněného přístupu](troubleshoot-conditional-access-what-if.md)
