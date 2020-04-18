---
title: Podmíněný přístup – vyžadovat vícefaktorové povolení pro všechny uživatele – Služba Azure Active Directory
description: Vytvoření vlastní zásady podmíněného přístupu, která vyžaduje, aby všichni uživatelé prováděli vícefaktorové ověřování
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
ms.openlocfilehash: 60d0ad0a1c0a1b4d13ce4d386df22406a8ab8e51
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617620"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>Podmíněný přístup: Vyžadovat vícefaktorové povolení pro všechny uživatele

Jak Alex Weinert, Adresář identity security na Microsoft, uvádí ve svém blogu [Váš Pa $ $word nezáleží](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984):

> Na vašem heslu nezáleží, ale MFA ano! Na základě našich studií je u vašeho účtu o více než 99,9 % menší pravděpodobnost ohrožení zabezpečení, pokud používáte vícefaktorové informace.

Pokyny v tomto článku pomohou vaší organizaci vytvořit vyvážené zásady vícefaktorové pomoci pro vaše prostředí.

## <a name="user-exclusions"></a>Vyloučení uživatelů

Zásady podmíněného přístupu jsou výkonné nástroje, doporučujeme z vašich zásad vyloučit následující účty:

* **Nouzové přístup** nebo **break-glass** účty, aby se zabránilo uzamčení účtu pro celý klient. V nepravděpodobném scénáři jsou všichni správci uzamčeni z vašeho tenanta, váš účet pro správu nouzového přístupu lze použít k přihlášení do klienta podniknout kroky k obnovení přístupu.
   * Další informace najdete v článku [Správa účtů pro nouzový přístup ve službě Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Účty služeb** a **instanční objekty**, jako je například účet synchronizace připojení Azure AD Connect. Účty služeb jsou neinteraktivní účty, které nejsou vázány na žádného konkrétního uživatele. Obvykle se používají back-endové služby, které umožňují programový přístup k aplikacím, ale také se používají k přihlášení k systémům pro administrativní účely. Účty služeb, jako jsou tyto by měly být vyloučeny, protože vícefaktorové nelze dokončit programově. Volání ze strany instančních objektů nejsou blokovány podmíněný montovna.
   * Pokud má vaše organizace tyto účty v provozu ve skriptech nebo kódu, zvažte jejich nahrazení [spravovanými identitami](../managed-identities-azure-resources/overview.md). Jako dočasné řešení můžete tyto konkrétní účty vyloučit ze zásady směrného plánu.

## <a name="application-exclusions"></a>Vyloučení aplikací

Organizace mohou mít mnoho cloudových aplikací v provozu. Ne všechny tyto aplikace mohou vyžadovat stejné zabezpečení. Například mzdy a docházky aplikace může vyžadovat Vícefaktorové, ale jídelna pravděpodobně není. Správci se mohou rozhodnout vyloučit určité aplikace ze svých zásad.

## <a name="create-a-conditional-access-policy"></a>Vytvoření zásad podmíněného přístupu

Následující kroky pomohou vytvořit zásady podmíněného přístupu, které budou vyžadovat, aby všichni uživatelé prováděli vícefaktorové ověřování.

1. Přihlaste se k **portálu Azure** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte na azure **active directory** > **zabezpečení** > podmíněný**přístup**.
1. Vyberte **nové zásady**.
1. Pojmenujte svou zásadu. Doporučujeme organizacím vytvořit smysluplný standard pro názvy svých zásad.
1. V části **Přiřazení**vyberte **Možnost I Uživatelé a skupiny.**
   1. V části **Zahrnout**vyberte **Možnost Všichni uživatelé.**
   1. V **části Vyloučit**vyberte Možnost **Uživatelé a skupiny** a zvolte nouzové přístupové nebo nerozbitné účty vaší organizace. 
   1. Vyberte **Done** (Hotovo).
1. V části **Cloudové aplikace nebo akce** > **Zahrnout**vyberte **Všechny cloudové aplikace**.
   1. V části **Vyloučit**vyberte všechny aplikace, které nevyžadují vícefaktorové ověřování.
1. V **části Podmínky** > **Klientské aplikace (Preview)** nastavte **Configure** to **Yes**a vyberte **Hotovo**.
1. V části **Access controls** > **Grant**vyberte **Udělit přístup**, Vyžadovat **vícefaktorové ověřování**a vybrat **vybrat**.
1. Potvrďte nastavení a nastavte **možnost Povolit zásady** **na Zapnuto**.
1. Chcete-li vytvořit, vyberte **vytvořit,** chcete-li povolit zásady.

### <a name="named-locations"></a>Pojmenovaná umístění

Organizace se mohou rozhodnout začlenit známá síťová umístění známá jako **Pojmenované umístění** do svých zásad podmíněného přístupu. Tato pojmenovaná umístění mohou zahrnovat důvěryhodné sítě IPv4, jako jsou sítě pro umístění v hlavní kanceláři. Další informace o konfiguraci pojmenovaných umístění najdete v článku [Jaká je podmínka umístění v podmíněném přístupu služby Azure Active Directory?](location-condition.md)

Ve výše uvedeném příkladu zásad může organizace zvolit, že nebude vyžadovat vícefaktorové ověřování, pokud přistupuje ke cloudové aplikaci ze své podnikové sítě. V tomto případě mohou přidat následující konfiguraci do zásady:

1. V části **Přiřazení**vyberte **možnost Místa podmínek** > **Locations**.
   1. Konfigurace **programu Ano**.
   1. Zahrnout **libovolné umístění**.
   1. Vyloučit **všechna důvěryhodná umístění**.
   1. Vyberte **Done** (Hotovo).
1. Vyberte **Done** (Hotovo).
1. **Uložte** změny zásad.

## <a name="next-steps"></a>Další kroky

[Podmíněné přístupové běžné zásady](concept-conditional-access-policy-common.md)

[Určení dopadu pomocí režimu pouze pro sestavu podmíněného přístupu](howto-conditional-access-report-only.md)

[Simulovat chování přihlášení pomocí nástroje Co-li podmíněného přístupu](troubleshoot-conditional-access-what-if.md)
