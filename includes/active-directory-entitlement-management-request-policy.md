---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 11/11/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: c73a62b2d5feeae42a5ea35c88073dd5fcc0d78c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77192872"
---
## <a name="for-users-in-your-directory"></a>Pro uživatele ve vašem adresáři

Pokud chcete uživatelům ve vašem adresáři povolit, aby mohli požádat o tento přístupový balíček, postupujte takto. Při definování zásad požadavků můžete určit jednotlivé uživatele nebo častěji skupiny uživatelů. Vaše organizace může mít například skupinu, například **Všichni zaměstnanci**.  Pokud je tato skupina přidána do zásad pro uživatele, kteří mohou požádat o přístup, může o přístup požádat každý člen této skupiny.

1. V části **Uživatelé, kteří mohou požádat o přístup,** klepněte **ve svém adresáři**na tlačítko Pro uživatele .

    Vyberete-li tuto možnost, zobrazí se nové možnosti, které dále upřesní, kdo ve vašem adresáři může požadovat tento přístupový balíček.

    ![Přístupový balíček – požadavky – pro uživatele ve vašem adresáři](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Vyberte jednu z následujících možností:

    |  |  |
    | --- | --- |
    | **Konkrétní uživatelé a skupiny** | Tuto možnost zvolte, pokud chcete, aby mohli tento přístupový balíček požadovat pouze uživatelé a skupiny ve vašem adresáři, které zadáte. |
    | **Všichni členové (kromě hostů)** | Tuto možnost zvolte, pokud chcete, aby všichni členové ve vašem adresáři mohli požadovat tento přístupový balíček. Tato možnost nezahrnuje žádné uživatele typu Host, které jste do adresáře pozvali. |
    | **Všichni uživatelé (včetně hostů)** | Tuto možnost zvolte, pokud chcete, aby všichni členové a uživatelé typu Host ve vašem adresáři mohli požadovat tento přístupový balíček. |

    Uživatelé typu Host odkazují na externí uživatele, kteří byli pozváni do vašeho adresáře pomocí [služby Azure AD B2B](../articles/active-directory/b2b/what-is-b2b.md). Další informace o rozdílech mezi členskými uživateli a uživateli typu Host najdete [v tématu Jaká jsou výchozí uživatelská oprávnění ve službě Azure Active Directory?](../articles/active-directory/fundamentals/users-default-permissions.md).

1. Pokud jste vybrali **Konkrétní uživatele a skupiny**, klepněte na **tlačítko Přidat uživatele a skupiny**.

1. V podokně Vybrat uživatele a skupiny vyberte uživatele a skupiny, které chcete přidat.

    ![Přístupový balíček – požadavky – výběr uživatelů a skupin](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. Kliknutím na **Vybrat** přidáte uživatele a skupiny.

1. Přeskočte dolů do části [Schválení.](#approval)

## <a name="for-users-not-in-your-directory"></a>Pro uživatele, kteří nejsou ve vašem adresáři

 **Uživatelé, kteří nejsou ve vašem adresáři,** odkazují na uživatele, kteří jsou v jiném adresáři nebo doméně Azure AD. Tito uživatelé pravděpodobně ještě nebyli pozváni do vašeho adresáře. Adresáře Azure AD musí být nakonfigurované tak, aby umožňovaly pozvánky v **omezeních spolupráce**. Další informace naleznete [v tématu Povolení externí spolupráce B2B a správa toho, kdo může pozvat hosty](../articles/active-directory/b2b/delegate-invitations.md).

> [!NOTE]
> Uživatelský účet typu Host bude vytvořen pro uživatele, který ještě není ve vašem adresáři, jehož požadavek je schválen nebo automaticky schválen. Host bude pozván, ale neobdrží e-mail s pozvánkou. Místo toho obdrží e-mail při doručení přiřazení balíčku přístupu. Ve výchozím nastavení, později, když tento uživatel typu Host již nemá žádné přiřazení balíčků přístupu, protože jejich poslední přiřazení vypršelo nebo bylo zrušeno, bude tento uživatelský účet hosta zablokován z přihlášení a následně odstraněn. Pokud chcete, aby uživatelé typu Host zůstali ve vašem adresáři neomezeně dlouho, i když nemají žádná přiřazení balíčků přístupu, můžete změnit nastavení konfigurace správy nároků. Další informace o objektu uživatele typu Host naleznete v [tématu Vlastnosti uživatele spolupráce služby Azure Active Directory B2B](../articles/active-directory/b2b/user-properties.md).

Pokud chcete uživatelům, kteří nejsou ve vašem adresáři, povolit, aby tento přístupový balíček požadovali, postupujte takto:

1. V části **Uživatelé, kteří mohou požádat o přístup,** klepněte na tlačítko **Pro uživatele, kteří nejsou ve vašem adresáři**.

    Když vyberete tuto možnost, zobrazí se nové možnosti.

    ![Přístupový balíček – požadavky – pro uživatele, kteří nejsou ve vašem adresáři](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Vyberte jednu z následujících možností:

    |  |  |
    | --- | --- |
    | **Konkrétní propojené organizace** | Tuto možnost vyberte, pokud chcete vybrat ze seznamu organizací, které dříve přidal správce. Tento přístupový balíček mohou požádat všichni uživatelé z vybraných organizací. |
    | **Všechny propojené organizace** | Tuto možnost zvolte, pokud mohou všichni uživatelé ze všech připojených organizací požádat o tento přístupový balíček. |
    | **Všichni uživatelé (všechny připojené organizace + noví externí uživatelé)** | Tuto možnost zvolte, pokud všichni uživatelé ze všech připojených organizací mohou požadovat tento přístupový balíček a že nastavení seznamu B2B povolit nebo odepřít by mělo mít přednost pro všechny nové externí uživatele. |

    Připojená organizace je externí adresář nebo doména Azure AD, se kterou máte vztah.

1. Pokud jste vybrali **Konkrétní připojené organizace**, klikněte na Přidat **adresáře** a vyberte ze seznamu připojených organizací, které dříve přidal správce.

1. Zadejte název nebo název domény, který chcete vyhledat pro dříve připojenou organizaci.

    ![Přístupový balíček – požadavky – výběr adresářů](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Pokud organizace, se kterou chcete spolupracovat, není v seznamu, můžete požádat správce, aby ji přidal jako připojenou organizaci. Další informace naleznete [v tématu Přidání připojené organizace](../articles/active-directory/governance/entitlement-management-organization.md).

1. Po výběru všech připojených organizací klikněte na **Vybrat**.

    > [!NOTE]
    > Všichni uživatelé z vybraných připojených organizací budou moci požádat o tento přístupový balíček. To zahrnuje uživatele ve službě Azure AD ze všech subdomén přidružených k organizaci, pokud tyto domény nejsou blokovány seznamem povolených nebo odepřených azure B2B. Další informace naleznete v [tématu Povolit nebo blokovat pozvánky uživatelům B2B z konkrétních organizací](../articles/active-directory/b2b/allow-deny-list.md).

1. Přeskočte dolů do části [Schválení.](#approval)

## <a name="none-administrator-direct-assignments-only"></a>Žádné (pouze přiřazení přímo správce)

Pokud chcete obejít žádosti o přístup a povolit správcům přímé přiřazení konkrétních uživatelů k tomuto balíčku přístupu, postupujte takto. Uživatelé nebudou muset požadovat přístupový balíček. Stále můžete nastavit nastavení životního cyklu, ale neexistují žádné nastavení požadavků.

1. V části **Uživatelé, kteří mohou požádat o přístup,** klepněte na **položku Žádný (pouze přiřazení přímo u správce**.

    ![Přístupový balíček – požadavky – pouze žádná přímá přiřazení správce](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    Po vytvoření balíčku přístupu můžete přímo přiřadit konkrétní interní a externí uživatele k balíčku přístupu. Pokud zadáte externího uživatele, bude ve vašem adresáři vytvořen uživatelský účet typu Host. Informace o přímém přiřazení uživatele naleznete v tématu [Zobrazení, přidání a odebrání přiřazení pro přístupový balíček](../articles/active-directory/governance/entitlement-management-access-package-assignments.md).

1. Přejděte dolů k části [Povolit požadavky.](#enable-requests)

## <a name="approval"></a>Schválení

V části Schválení určíte, zda je vyžadováno schválení, když uživatelé požadují tento přístupový balíček. Nastavení schválení funguje následujícím způsobem:

- Pouze jeden z vybraných schvalovatelů nebo záložní schvalovatelé musí schválit žádost o jednofázové schválení. 
- Žádost o dvoufázové schválení musí schválit pouze jeden z vybraných schvalovatelů z každé fáze.
- Schvalovatel může být manažer, interní sponzor nebo externí sponzor v závislosti na tom, kdo zásady řídí přístup.
- Schválení od každého vybraného schvalovatele není vyžadováno pro jednofázové nebo dvoustupňové schválení.
- Rozhodnutí o schválení je založeno na tom, který schvalovatel nejprve zkontroluje žádost.

Ukázku toho, jak přidat schvalovatele do zásad požadavků, nahlédli následující video:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

Ukázku toho, jak přidat vícestupňové schválení do zásad požadavků, nahlédli následující video:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]

Chcete-li zadat nastavení schválení pro požadavky na přístupový balíček, postupujte takto:

1. Chcete-li vyžadovat schválení požadavků od vybraných uživatelů, nastavte přepínač **Vyžadovat schválení** na **Ano**. Chcete-li mít požadavky automaticky schváleny, nastavte přepínač na **ne**.

1. Chcete-li požadovat, aby uživatelé poskytli odůvodnění pro vyžádání přístupového balíčku, nastavte přepínač **Vyžadovat zarovnání žádosti** na **Ano**.
    
1. Nyní určete, zda požadavky budou vyžadovat jednofázové nebo dvoufázové schválení. Nastavte přepínač **Kolik fází** na **1** pro jednostupňové schválení nebo nastavte přepínač na **2** pro dvoustupňové schválení.

    ![Přístupový balíček – požadavky – nastavení schválení](./media/active-directory-entitlement-management-request-policy/approval.png)

Pomocí následujících kroků přidejte schvalovatele po výběru, kolik fází požadujete: 

### <a name="single-stage-approval"></a>Jednostupňové schválení

1. Přidejte **prvního schvalovatele**:
    
    Pokud je zásada nastavena tak, aby řídila přístup uživatelů ve vašem adresáři, můžete vybrat **Správce jako schvalovatele**. Nebo přidejte konkrétního uživatele kliknutím na **Přidat schvalovatele** po výběru výběru konkrétních schvalovatelů z rozevírací nabídky.
    
    ![Přístupový balíček – požadavky – pro uživatele v adresáři – první schvalovatel](./media/active-directory-entitlement-management-request-policy/approval-single-stage-first-approver-manager.png)

    Pokud je tato zásada nastavena tak, aby řídila přístup uživatelů, kteří nejsou ve vašem adresáři, můžete vybrat **externího sponzora** nebo **interního sponzora**. Nebo přidejte konkrétního uživatele kliknutím na **Přidat schvalovatele** nebo skupiny v části Zvolit konkrétní schvalovatele.
    
    ![Přístupový balíček – požadavky – pro uživatele mimo adresář – první schvalovatel](./media/active-directory-entitlement-management-request-policy/out-directory-first-approver.png)
    
1. Pokud jste jako první ho schvalovatel vybrali **Správce,** klikněte na **Přidat záložní položku** a vyberte jednoho nebo více uživatelů nebo skupin ve vašem adresáři jako záložního schvalovatele. Záložní schvalovatelé obdrží žádost, pokud správa oprávnění nemůže najít správce pro uživatele požadující přístup.

    Správce je nalezen podle správy oprávnění pomocí atributu **Manager.** Atribut je v profilu uživatele ve službě Azure AD. Další informace najdete [v tématu Přidání nebo aktualizace informací o profilu uživatele pomocí služby Azure Active Directory](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

1. Pokud jste vybrali **Vybrat konkrétní schvalovatele**, klikněte na **Přidat schvalovatele** a vyberte jednoho nebo více uživatelů nebo skupin ve vašem adresáři jako schvalovatelů.

1. V poli **podle rozhodnutí musí být provedena v kolik dní?**, uveďte počet dní, které schvalovatel musí zkontrolovat žádost o tento přístup balíček.

    Pokud žádost nebude v této lhůtě schválena, bude automaticky zamítnuta. Uživatel bude muset odeslat další požadavek na přístupový balíček.

1. Chcete-li vyžadovat, aby schvalovatelé poskytli odůvodnění svého rozhodnutí, nastavte na řadě Vyžadovat zarovnání schvalovatele na **ano**.

    Odůvodnění je viditelné pro ostatní schvalovatele a žadatel.

### <a name="2-stage-approval-preview"></a>Dvoufázové schválení (náhled)

Pokud jste vybrali dvoufázové schválení, budete muset přidat druhého schvalovatele.

1. Přidejte **druhého schvalovatele**: 
    
    Pokud jsou uživatelé ve vašem adresáři, přidejte konkrétního uživatele jako druhého schvalovatele kliknutím na **Přidat schvalovatele** v části Zvolit konkrétní schvalovatele.

    ![Přístupový balíček – požadavky – pro uživatele v adresáři – druhý schvalovatel](./media/active-directory-entitlement-management-request-policy/in-directory-second-approver.png)

    Pokud uživatelé nejsou ve vašem adresáři, vyberte jako druhého schvalovatele **interního sponzora** nebo **externího sponzora.** Po výběru schvalovatele přidejte záložní schvalovatele.

    ![Přístupový balíček – požadavky – pro uživatele mimo adresář – druhý schvalovatel](./media/active-directory-entitlement-management-request-policy/out-directory-second-approver.png) 

1. Zadejte počet dní, po které musí druhý schvalovatel schválit žádost v kolonce **rozhodnutí, která musí být podána v kolika dnech?**. 

1. Nastavte přepínač Vyžadovat zarovnání schvalovatele na **Ano** nebo **Ne**.

### <a name="alternate-approvers"></a>Alternativní schvalovatelé

Můžete určit alternativní schvalovatelé, podobně jako určení první a druhé schvalovatelů, kteří mohou schvalovat žádosti. Alternativní schvalovatelé vám pomohou zajistit, aby byly žádosti schváleny nebo zamítnuty před vypršením jejich platnosti (časový limit). Můžete vypsat alternativní schvalovatele prvního schvalovatele a druhého schvalovatele pro dvoufázové schválení. 

Určením alternativních schvalovatelů v případě, že první nebo druhý schvalovatel nemohl schválit nebo zamítnout žádost, bude čekající žádost předána alternativním schvalovatelům podle plánu předávání, který jste zadali během nastavení zásad. Obdrží e-mail ke schválení nebo zamítnutí nevyřízené žádosti.

Po předání žádosti alternativním schvalovatelům mohou první nebo druhý schvalovatel žádost stále schválit nebo zamítnout. Alternativní schvalovatelé používají stejný web Můj přístup ke schválení nebo zamítnutí nevyřízené žádosti.

Můžeme uvést lidi nebo skupiny lidí, kteří mají být schvalovatelé a alternativní schvalovatelé. Ujistěte se, že jste seznam různých sad lidí, které mají být první, druhý a alternativní schvalovatelů.
Pokud jste například uvedli Alici a Boba jako prvního schvalovatele, uveďte jako alternativní schvalovatele Carol a Davea. Pomocí následujících kroků přidejte alternativní schvalovatele do balíčku přístupu:

1. V části První schvalovatel, Druhý schvalovatel nebo obojí klikněte na **Zobrazit upřesňující nastavení požadavku**.

    ![Přístupový balíček – zásady – zobrazení nastavení upřesňujících požadavků](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. Nastavit: **Pokud nebyla provedena žádná akce, přepněte přepošlete alternativním schvalovatelům** na **Ano**.

1. Klikněte na **Přidat alternativní schvalovatele** a ze seznamu vyberte alternativní schvalovatele.

    ![Přístupový balíček – zásady – přidání alternativních schvalovatelů](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

1. V poli **Forward to alternate approver (y) po počtu dní** uveďte počet dní, po které mají schvalovatelé schválit nebo zamítnout žádost. Pokud žádní schvalovatelé neschválili nebo nezamítli požadavek před trváním žádosti, vyprší platnost žádosti (časový limit) a uživatel bude muset odeslat další požadavek na přístupový balíček. 

    Žádosti lze předat alternativním schvalovatelům pouze den poté, co doba trvání žádosti dosáhne poločasu. V tomto příkladu je doba trvání požadavku 14 dní. Takže doba trvání žádosti dosáhne poločasu v den 7. Takže žádost nemůže být předána dříve než den 8. Požadavky také nelze předat dál v poslední den trvání požadavku. Takže v příkladu nejnovější požadavek může být předán je den 13.

## <a name="enable-requests"></a>Povolit požadavky

1. Pokud chcete, aby byl přístupový balíček okamžitě k dispozici uživatelům v zásadách požadavku, přesuňte přepínač Povolit na **Ano**.

    Vždy jej můžete povolit v budoucnu po dokončení vytváření přístupového balíčku.

    Pokud jste vybrali **možnost Žádné (pouze přiřazení přímo správce)** a nastavili jste možnost **Ne**, nemohou správci tento přístupový balíček přímo přiřadit.

    ![Přístupový balíček – zásady – povolit nastavení zásad](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Klikněte na **Další**.
