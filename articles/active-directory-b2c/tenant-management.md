---
title: Správa Azure Active Directory B2C
titleSuffix: Azure Active Directory B2C
description: Naučte se spravovat tenanta Azure Active Directory B2C. Seznamte se s funkcemi Azure AD, které jsou podporované v Azure AD B2C, jak používat role správců ke správě prostředků a jak přidat pracovní účty a uživatele typu Host do tenanta Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c3ea17a4f6dc2fb5134c6ceb1ae37d25e0881365
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715423"
---
# <a name="manage-your-azure-active-directory-b2c-tenant"></a>Správa tenanta Azure Active Directory B2C

V Azure Active Directory B2C (Azure AD B2C) tenant představuje adresář uživatelů spotřebitelů. Každý tenant Azure AD B2C je jedinečný a oddělený od ostatních tenantů Azure AD B2C. Tenant Azure AD B2C je jiný než tenant Azure Active Directory, který už možná máte. V tomto článku se dozvíte, jak spravovat klienta Azure AD B2C.

## <a name="supported-azure-ad-features"></a>Podporované funkce služby Azure AD

Azure AD B2C spoléhá na platformu Azure AD. Ve vašem tenantovi Azure AD B2C můžete použít následující funkce služby Azure AD.

|Funkce  |Azure AD  | Azure AD B2C |
|---------|---------|---------|
| [Skupiny](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) | Skupiny je možné použít ke správě účtů pro správu a uživatele.| Skupiny je možné použít ke správě účtů pro správu. [Uživatelské účty](user-overview.md#consumer-user) nepodporují skupiny. |
| [Pozvání k externím identitám hostů](../active-directory//external-identities/add-users-administrator.md)| Můžete pozvat uživatele typu Host a nakonfigurovat funkce externích identit, jako je například federace a přihlášení pomocí účtů Facebook a Google. | Můžete pozvat jenom účet Microsoft nebo uživatele Azure AD jako hosta pro vašeho tenanta Azure AD pro přístup k aplikacím nebo správě tenantů. U [zákaznických účtů](user-overview.md#consumer-user)můžete pomocí Azure AD B2C uživatelských toků a vlastních zásad spravovat uživatele a registrovat se nebo přihlašovat pomocí externích zprostředkovatelů identity, jako je Google nebo Facebook. |
| [Role a správci](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)| Plně podporováno pro účty pro správu a uživatele. | Role se u [uživatelských účtů](user-overview.md#consumer-user)nepodporují. Uživatelské účty nemají přístup k žádným prostředkům Azure.|
| [Vlastní názvy domén](../active-directory/roles/permissions-reference.md#) |  Vlastní domény Azure AD můžete použít jenom pro účty správců. | [Účty příjemců](user-overview.md#consumer-user) se můžou přihlásit pomocí uživatelského jména, telefonního čísla nebo libovolné e-mailové adresy. V adresách URL pro přesměrování můžete použít [vlastní domény](custom-domain.md) .|
| [Podmíněný přístup](../active-directory/roles/permissions-reference.md#) | Plně podporováno pro účty pro správu a uživatele. | Podmnožina funkcí podmíněného přístupu služby Azure AD je podporovaná u uživatelských [účtů](user-overview.md#consumer-user) , které se naučí nakonfigurovat Azure AD B2C [vlastní domény](conditional-access-user-flow.md).|

## <a name="other-azure-resources-in-your-tenant"></a>Další prostředky Azure ve vašem tenantovi

V tenantovi Azure AD B2C nemůžete zřídit další prostředky Azure, jako jsou virtuální počítače, Azure Web Apps nebo Azure Functions. Tyto prostředky musíte vytvořit v tenantovi Azure AD.

## <a name="azure-ad-b2c-accounts-overview"></a>Přehled účtů Azure AD B2C

V Azure AD B2C tenantovi se dají vytvořit tyto typy účtů:

V tenantovi Azure AD B2C existuje několik typů účtů, které jsou popsány v [přehledu uživatelských účtů v Azure Active Directory B2C](user-overview.md) článku.

- **Pracovní účet** – pracovní účet má přístup k prostředkům v tenantovi a s rolí správce může spravovat klienty.
- **Účet Guest** – účet hosta může být jenom účet Microsoft nebo uživatel Azure Active Directory, který se dá použít pro přístup k aplikacím nebo správě tenantů.
- **Účet příjemce** – účet příjemce se používá uživatelem aplikací, které jste zaregistrovali v Azure AD B2C.

Podrobnosti o těchto typech účtů najdete v tématu [Přehled uživatelských účtů v Azure Active Directory B2C](user-overview.md). Každý uživatel, který bude přiřazený ke správě vašeho tenanta Azure AD B2C, musí mít uživatelský účet Azure AD, aby mohl přistupovat ke službám souvisejícím s Azure. Takového uživatele můžete přidat [vytvořením účtu](#add-an-administrator-work-account) (pracovní účet) ve vašem tenantovi Azure AD B2C nebo [jejich pozváním](#invite-an-administrator-guest-account) do svého tenanta Azure AD B2C jako uživatel typu Host.

## <a name="use-roles-to-control-resource-access"></a>Řízení přístupu k prostředkům pomocí rolí

Při plánování strategie řízení přístupu je nejvhodnější přiřadit uživatelům nejnižší privilegované role vyžadované pro přístup k prostředkům. Následující tabulka popisuje primární prostředky v tenantovi Azure AD B2C a nejvhodnější administrativní role pro uživatele, kteří je spravují.

|Prostředek  |Popis  |Role  |
|---------|---------|---------|
|[Registrace aplikací](tutorial-register-applications.md) | Vytvářejte a spravujte všechny aspekty svých webových, mobilních a nativních registrací aplikací v rámci Azure AD B2C.|[Správce aplikace](../active-directory/roles/permissions-reference.md#global-administrator)|
|[Zprostředkovatelé identity](add-identity-provider.md)| Nakonfigurujte [místního zprostředkovatele identity](identity-provider-local.md) a externí poskytovatele identity v sociálních sítích nebo organizacích. | [Správce externích zprostředkovatelů identity](../active-directory/roles/permissions-reference.md#external-identity-provider-administrator)|
|[Konektory rozhraní API](add-api-connector.md)| Integrací uživatelských toků s webovými rozhraními API můžete přizpůsobit uživatelské prostředí a integraci s externími systémy.|[Správce atributů toku uživatele externího ID](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Branding společnosti](customize-ui.md#configure-company-branding)| Přizpůsobení stránek toku uživatele.| [Globální správce](../active-directory/roles/permissions-reference.md#global-administrator)|
|[Atributy uživatele](user-flow-custom-attributes.md)| Přidejte nebo odstraňte vlastní atributy dostupné všem uživatelským tokům.| [Správce atributů toku uživatele externího ID](../active-directory/roles/permissions-reference.md#external-id-user-flow-attribute-administrator)|
|Správa uživatelů| Správa uživatelských [účtů](manage-users-portal.md) a účtů pro správu, jak je popsáno v tomto článku.| [Správce uživatele](../active-directory/roles/permissions-reference.md#user-administrator)|
|Role a správci| Umožňuje spravovat přiřazení rolí v adresáři Azure AD B2C. Vytváření a Správa skupin, které je možné přiřadit k rolím Azure AD B2C. |[Globální správce](../active-directory/roles/permissions-reference.md#global-administrator), [správce privilegovaných rolí](../active-directory/roles/permissions-reference.md#privileged-role-administrator)|
|[Toky uživatelů](user-flow-overview.md)|Pro rychlou konfiguraci a povolování běžných úloh identity, jako je registrace, přihlašování a úpravy profilu.| [Správce atributů toku uživatele externího ID](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Vlastní zásady](user-flow-overview.md)| Vytváření, čtení, aktualizace a odstraňování všech vlastních zásad v Azure AD B2C.| [Správce zásad IEF B2C](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)|
|[Klíče zásad](policy-keys-overview.md)|Přidejte a spravujte šifrovací klíče pro podepisování a ověřování tokenů, tajných kódů klienta, certifikátů a hesel používaných ve vlastních zásadách.|[B2C IEF, správce sady klíčů](../active-directory/roles/permissions-reference.md#b2c-ief-keyset-administrator)|


## <a name="add-an-administrator-work-account"></a>Přidat správce (pracovní účet)

K vytvoření nového účtu správce použijte následující postup:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) s oprávněními správce globálního správce nebo privilegované role.
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V části **služby Azure** vyberte **Azure AD B2C**. Nebo pomocí vyhledávacího pole vyhledejte a vyberte **Azure AD B2C**.
1. V části **Spravovat** vyberte **Uživatelé**.
1. Vyberte **Nový uživatel**.
1. Na stránce **uživatel** zadejte informace pro tohoto uživatele:

   - **Název:** Povinná hodnota. Křestní jméno a příjmení nového uživatele. Například *Marie Parker*.
   - **Uživatelské jméno**. Povinná hodnota. Uživatelské jméno nového uživatele. Například, `mary@contoso.com`.
     Část domény uživatelského jména musí používat počáteční výchozí název domény ( *\<yourdomainname> . onmicrosoft.com*).
   - **Skupiny**. Volitelně můžete přidat uživatele do jedné nebo více existujících skupin. Uživatele můžete také přidat do skupin později. 
   - **Role adresáře**: Pokud pro uživatele požadujete oprávnění správce Azure AD, můžete je přidat do role Azure AD. Můžete přiřadit uživatele jako globální správce nebo jednu nebo více rolí s omezeným správcem v Azure AD. Další informace o přiřazování rolí najdete v tématu [použití rolí k řízení přístupu k prostředkům](#use-roles-to-control-resource-access).
   - **Informace o úloze**: tady můžete přidat další informace o uživateli nebo to udělat později. 

1. Zkopírujte automaticky vygenerované heslo, které jste zadali v poli **heslo** . Toto heslo budete muset zadat pro uživatele, aby se přihlásil poprvé.
1. Vyberte **Vytvořit**.

Uživatel je vytvořen a přidán do vašeho tenanta Azure AD B2C. Je vhodnější, aby byl pro vašeho tenanta Azure AD B2C přidaných role globálního správce nativně aspoň jeden pracovní účet. Tento účet se dá považovat za účet pro dělení na sklo.

## <a name="invite-an-administrator-guest-account"></a>Pozvání správce (účet hosta)

Můžete taky pozvat nového uživatele typu Host a spravovat svého tenanta. Účet hosta je upřednostňovanou možností, když má vaše organizace taky Azure AD, protože životní cyklus této identity je možné spravovat externě. 

Pokud chcete uživatele pozvat, postupujte podle těchto kroků:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) s oprávněními správce globálního správce nebo privilegované role.
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V části **služby Azure** vyberte **Azure AD B2C**. Nebo pomocí vyhledávacího pole vyhledejte a vyberte **Azure AD B2C**.
1. V části **Spravovat** vyberte **Uživatelé**.
1. Vyberte **nový účet Guest**.
1. Na stránce **uživatel** zadejte informace pro tohoto uživatele:

   - **Název:** Povinná hodnota. Křestní jméno a příjmení nového uživatele. Například *Marie Parker*.
   - **E-mailová adresa** Povinná hodnota. E-mailová adresa uživatele, kterou chcete pozvat. Například, `mary@contoso.com`.   
   - **Osobní zpráva**: přidáte osobní zprávu, která bude obsažena v e-mailu s pozvánkou.
   - **Skupiny**. Volitelně můžete přidat uživatele do jedné nebo více existujících skupin. Uživatele můžete také přidat do skupin později.
   - **Role adresáře**: Pokud pro uživatele požadujete oprávnění správce Azure AD, můžete je přidat do role Azure AD. Můžete přiřadit uživatele jako globální správce nebo jednu nebo více rolí s omezeným správcem v Azure AD. Další informace o přiřazování rolí najdete v tématu [použití rolí k řízení přístupu k prostředkům](#use-roles-to-control-resource-access).
   - **Informace o úloze**: tady můžete přidat další informace o uživateli nebo to udělat později.

1. Vyberte **Vytvořit**.

Uživateli se pošle e-mail s pozvánkou. Uživatel musí přijmout pozvánku, aby se mohl přihlásit.

### <a name="resend-the-invitation-email"></a>Poslat znovu e-mail s pozvánkou

Pokud host neobdržel e-mail s pozvánkou nebo vypršela jeho platnost, můžete pozvánku odeslat znovu. Jako alternativu k e-mailu s pozvánkou můžete dát hostovi přímý odkaz na přijetí pozvánky. Pokud chcete znovu odeslat pozvánku a získat přímý odkaz:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V části **služby Azure** vyberte **Azure AD B2C**. Nebo pomocí vyhledávacího pole vyhledejte a vyberte **Azure AD B2C**.
1. V části **Spravovat** vyberte **Uživatelé**.
1. Vyhledejte a vyberte uživatele, na kterého chcete pozvánku poslat znovu.
1. V **uživateli |** Na stránce profil v části **Identita** vyberte **(spravovat)**.
    
    ![Snímek obrazovky ukazující opětovné odeslání e-mailu s pozvánkou na účet Guest](./media/tenant-management/guest-account-resend-invite.png)

1. V případě **opětovného odeslání pozvánky** vyberte **Ano**. Když jste si **jisti, že chcete znovu odeslat pozvánku?** zobrazí se vyberte **Ano**.
1. Azure AD B2C odešle pozvánku. Můžete také zkopírovat adresu URL pozvánky a poskytnout ji přímo k hostovi.
    
    ![Snímek obrazovky s informacemi o tom, jak získat adresu URL pozvánky](./media/tenant-management/guest-account-invitation-url.png)  
 
## <a name="add-a-role-assignment"></a>Přidat přiřazení role

Roli můžete přiřadit při [vytváření uživatele](#add-an-administrator-work-account) nebo [pozvání uživatele typu Host](#invite-an-administrator-guest-account). Můžete přidat roli, změnit roli nebo odebrat roli pro uživatele:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) s oprávněními správce globálního správce nebo privilegované role.
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V části **služby Azure** vyberte **Azure AD B2C**. Nebo pomocí vyhledávacího pole vyhledejte a vyberte **Azure AD B2C**.
1. V části **Spravovat** vyberte **Uživatelé**.
1. Vyberte uživatele, pro kterého chcete změnit role. Pak vyberte **přiřazené role**.
1. Vyberte **Přidat přiřazení**, vyberte roli, kterou chcete přiřadit (například *správce aplikace*), a pak zvolte **Přidat**.

## <a name="remove-a-role-assignment"></a>Odebrání přiřazení role

Pokud potřebujete odebrat přiřazení role z uživatele, postupujte podle těchto kroků:

1. Vyberte **Azure AD B2C**, vyberte **uživatele** a pak vyhledejte a vyberte uživatele.
1. Vyberte **přiřazené role**. Vyberte roli, kterou chcete odebrat, například *Správce aplikací*, a pak vyberte **odebrat přiřazení**.

## <a name="review-administrator-account-role-assignments"></a>Kontrola přiřazení rolí účtu správce

V rámci procesu auditování obvykle zkontrolujete, kteří uživatelé jsou přiřazeni k určitým rolím v adresáři Azure AD B2C. Pomocí následujících kroků můžete auditovat uživatele, kteří jsou aktuálně přiřazeni privilegovaným rolím.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) s oprávněními správce globálního správce nebo privilegované role.
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V části **služby Azure** vyberte **Azure AD B2C**. Nebo pomocí vyhledávacího pole vyhledejte a vyberte **Azure AD B2C**.
1. V části **Spravovat** vyberte **role a správci**.
1. Vyberte roli, jako je například **globální správce**. **Role | Stránka přiřazení** obsahuje uživatele s touto rolí.

## <a name="delete-an-administrator-account"></a>Odstranit účet správce

Pokud chcete odstranit existujícího uživatele, musíte mít přiřazenou roli *globálního správce* . Globální Správci mohou odstranit libovolného uživatele, včetně jiných správců. *Správci uživatelů* mohou odstranit libovolného uživatele bez oprávnění správce.

1. V adresáři Azure AD B2C vyberte možnost **Uživatelé** a potom vyberte uživatele, kterého chcete odstranit.
1. Vyberte **Odstranit** a potvrďte odstranění potvrzením kliknutím na **Ano** .

Uživatel se odstraní a už se nezobrazí na stránce **Uživatelé – všichni uživatelé** . Uživatel se může na stránce **Odstraněná uživatelé** zobrazit během následujících 30 dnů a může se v této době obnovit. Další informace o obnovení uživatele najdete v tématu [obnovení nebo odebrání nedávno odstraněného uživatele pomocí Azure Active Directory](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="protect-administrative-accounts"></a>Ochrana účtů pro správu

Doporučuje se chránit všechny účty správců pomocí služby Multi-Factor Authentication (MFA) pro zvýšení zabezpečení. MFA je proces ověření identity během přihlašování, který uživateli vyzve k získání další formy identifikace, jako je ověřovací kód na svém mobilním zařízení nebo požadavek ve Microsoft Authenticator aplikaci.

![Metody ověřování používané na přihlašovacím snímku obrazovky](./media/tenant-management/sing-in-with-multi-factor-authentication.png)

Můžete povolit [výchozí nastavení zabezpečení Azure AD](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) , které vynutí použití vícefaktorového ověřování pro všechny účty pro správu.



## <a name="next-steps"></a>Další kroky

- [Vytvoření tenanta Azure Active Directory B2C na webu Azure Portal](tutorial-create-tenant.md)

