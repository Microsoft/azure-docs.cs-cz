---
title: Správa tenantů na komerčním webu Marketplace – Azure Marketplace
description: Naučte se spravovat klienty pro program komerčního tržiště v partnerském centru.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.date: 04/07/2021
ms.openlocfilehash: 446792b26527126a4db99da14a2585c17cf8610c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108222"
---
# <a name="manage-tenants-in-the-commercial-marketplace"></a>Správa tenantů na komerčním webu Marketplace

**Příslušné role**

- Manažer

Tenant Azure Active Directory (AD), označovaný také jako *pracovní účet* v této dokumentaci, je reprezentace vaší organizace nastavená v Azure Portal a pomůže vám spravovat konkrétní instanci cloudových služeb Microsoftu pro interní a externí uživatele. Pokud se vaše organizace přihlásila k odběru cloudové služby Microsoftu, jako je Azure, Microsoft Intune nebo Microsoft 365, vytvořil se tenant Azure AD za vás.

Můžete nastavit více tenantů pro použití s partnerským centrem. Můžete to chtít udělat, pokud má vaše společnost více tenantů (například contoso.com, contoso.uk atd.), můžete propojit další klienty tady. Toto přidružení vám umožní přidávat a spravovat uživatele z dalších tenantů na účtu komerčního tržiště.

Každý uživatel s rolí správce v účtu partnerského centra bude mít možnost Přidat a odebrat klienty služby Azure AD z tohoto účtu.

## <a name="add-an-existing-tenant"></a>Přidat existujícího tenanta

K přidružení jiného tenanta Azure AD k vašemu účtu partnerského centra:

1. V pravém horním rohu partnerského centra vyberte **Nastavení**  >  **Nastavení účtu**.
1. V části **Profil organizace** vyberte **klienti**. Zobrazují se aktuální přidružení tenanta.
1. Na kartě **vývojář** vyberte **přidružit**.
1. Zadejte svoje přihlašovací údaje Azure AD pro tenanta, kterého chcete přidružit.
1. Zkontrolujte název organizace a domény pro vašeho tenanta Azure AD. Přidružení dokončíte výběrem **Potvrdit**.

Pokud je přidružení úspěšné, budete připraveni přidat a spravovat uživatele účtu v části Uživatelé v partnerském centru. Další informace o přidávání uživatelů najdete v tématu [Správa uživatelů](add-manage-users.md).

## <a name="create-a-new-tenant"></a>Vytvoření nového tenanta

Pokud chcete vytvořit značku nového tenanta Azure AD s vaším účtem partnerského centra:

1. V pravém horním rohu partnerského centra vyberte **Nastavení**  >  **Nastavení účtu**.
1. V části **Profil organizace** vyberte **klienti**. Zobrazují se aktuální přidružení tenanta.
1. Na kartě Vývojář vyberte **vytvořit**.
1. Zadejte informace o adresáři pro novou službu Azure AD:
    - **Název domény**: jedinečný název, který budeme používat pro vaši doménu Azure AD, společně s ". onmicrosoft.com". Pokud jste například zadali "example", vaše doména Azure AD by byla "example.onmicrosoft.com".
    - **Kontaktní e-mail**: e-mailová adresa, na které vám můžeme v případě potřeby kontaktovat váš účet.
    - **Informace o uživatelském účtu globálního správce**: jméno, příjmení, uživatelské jméno a heslo, které chcete použít pro nový účet globálního správce.
1. Vyberte vytvořit a potvrďte informace o nové doméně a účtu.
1. Přihlaste se pomocí svého nového uživatelského jména a hesla globálního správce služby Azure AD, abyste mohli začít [přidávat a spravovat uživatele](add-manage-users.md).

Další informace o vytváření nových klientů v rámci Azure Portal, a ne na portálu partnerského centra, najdete v článku [Vytvoření nového tenanta v Azure Active Directory](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

## <a name="remove-a-tenant"></a>Odebrání tenanta

Pokud chcete odebrat tenanta z účtu partnerského centra, Najděte jeho název na stránce **tenantů** (v **Nastavení účtu**) a pak vyberte **Odebrat**. Zobrazí se výzva k potvrzení, že chcete klienta odebrat. Až to uděláte, žádní uživatelé v tomto tenantovi se nebudou moct přihlašovat k účtu partnerského centra a všechna oprávnění, která jste nakonfigurovali pro tyto uživatele, se odeberou.

> [!TIP]
> Tenanta nemůžete odebrat, pokud jste aktuálně přihlášení k partnerskému centru pomocí účtu ve stejném tenantovi. Pokud chcete klienta odebrat, musíte se přihlásit k partnerskému centru jako **správce** pro jiného tenanta, který je k tomuto účtu přidružený. Pokud se k účtu přidruží jenom jeden tenant, dá se tento tenant odebrat jenom po přihlášení pomocí účet Microsoft, který účet otevřel.
