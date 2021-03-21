---
title: Nastavení žádosti o změnu pro balíček pro přístup ve správě nároků Azure AD – Azure Active Directory
description: Přečtěte si, jak změnit nastavení žádosti pro balíček přístupu v Azure Active Directory správě nároků.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/16/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b6bc00af8d54c8748dd82b934974282e0e8da0e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102426956"
---
# <a name="change-request-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Nastavení žádosti o změnu pro balíček přístupu v Azure AD – Správa nároků

Jako správce balíčků přístupu můžete kdykoli změnit uživatele, kteří můžou požádat o přístup k balíčku, úpravou zásady nebo přidáním nové zásady. Tento článek popisuje, jak změnit nastavení žádosti pro existující balíček přístupu.

## <a name="choose-between-one-or-multiple-policies"></a>Vyberte jednu nebo víc zásad.

Způsob, jakým určíte, kdo může žádat o přístup k balíčku, je zásadou. Před vytvořením nové zásady nebo úpravou existující zásady v přístupovém balíčku musíte určit, kolik zásad má balíček pro přístup potřebovat. 

Když vytvoříte balíček pro přístup, zadáte nastavení žádosti, které vytvoří zásadu. Většina přístupových balíčků bude mít jednu zásadu, ale jeden balíček přístupu může mít víc zásad. Vytvořili jste několik zásad pro balíček pro přístup, pokud chcete, aby bylo možné udělit různým sadám uživatelů přiřazení s různými nastaveními požadavků a schvalování. 

Například jedna zásada se nedá použít k přiřazení interních a externích uživatelů ke stejnému balíčku přístupu. Ve stejném přístupovém balíčku ale můžete vytvořit dvě zásady, jednu pro interní uživatele a jednu pro externí uživatele. Pokud je k dispozici více zásad, které platí pro uživatele, budou v době žádosti vyzváni k výběru zásady, ke kterým se mají přiřadit. Následující diagram znázorňuje balíček přístupu se dvěma zásadami.

![Více zásad v balíčku pro přístup](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

### <a name="how-many-policies-will-i-need"></a>Kolik zásad budu potřebovat?

| Scenario | Počet zásad |
| --- | --- |
| Chci, aby všichni uživatelé v adresáři měli stejné nastavení požadavků a schvalování pro balíček pro přístup | Jednu |
| Chci, aby všichni uživatelé v některých připojených organizacích mohli žádat o přístup k balíčku | Jednu |
| Chci uživatelům v adresáři a také uživatelům, kteří jsou mimo můj adresář, dovolit přístup k balíčku pro přístup | Několik |
| Chci pro některé uživatele zadat jiná nastavení schvalování | Několik |
| Chci, aby někteří uživatelé měli přístup k vypršení platnosti přiřazení balíčků, zatímco ostatní uživatelé můžou svůj přístup rozšíří. | Několik |

Informace o Logic prioritě, která se používá při použití více zásad, najdete v tématu [více zásad](entitlement-management-troubleshoot.md#multiple-policies
).

## <a name="open-an-existing-access-package-and-add-a-new-policy-of-request-settings"></a>Otevření existujícího balíčku pro přístup a přidání nové zásady nastavení žádostí

Pokud máte sadu uživatelů, kteří by měli mít různá nastavení požadavků a schvalování, budete pravděpodobně muset vytvořit novou zásadu. Pomocí těchto kroků můžete začít přidávat nové zásady do stávajícího přístupového balíčku:

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Klikněte na **zásady** a potom na **Přidat zásadu**.

1. Na kartě **základy** se začnete začínat. Zadejte název a popis zásady.

    ![Vytvořit zásadu s názvem a popisem](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. Kliknutím na **Další** otevřete kartu **žádosti** .

1. Změňte **uživatele, kteří můžou požadovat nastavení přístupu** . Pomocí kroků v následujících částech můžete změnit nastavení na jednu z následujících možností: 
    - [Pro uživatele ve vašem adresáři](#for-users-in-your-directory) 
    - [Pro uživatele, kteří nejsou ve vašem adresáři](#for-users-not-in-your-directory)
    - [Žádné (pouze přímé přiřazení správce)](#none-administrator-direct-assignments-only)

## <a name="for-users-in-your-directory"></a>Pro uživatele ve vašem adresáři

Postupujte podle těchto kroků, pokud chcete, aby uživatelé ve vašem adresáři mohli požadovat tento balíček přístupu. Při definování zásad žádosti můžete zadat jednotlivé uživatele nebo skupiny uživatelů, kteří jsou často. Vaše organizace například už může mít skupinu, jako je například **Všichni zaměstnanci**.  Pokud se tato skupina přidá v zásadách pro uživatele, kteří můžou požádat o přístup, pak může každý člen této skupiny požádat o přístup.

1. V části **Uživatelé, kteří můžou požádat o přístup** , klikněte na **uživatele v adresáři**.

    Když vyberete tuto možnost, zobrazí se nové možnosti, které vám pomůžou lépe upřesnit, kdo v adresáři může tento přístupový balíček požádat.

    ![Přístup k balíčku – požadavky – pro uživatele ve vašem adresáři](./media/entitlement-management-access-package-request-policy/for-users-in-your-directory.png)

1. Vyberte jednu z následujících možností:

    |  |  |
    | --- | --- |
    | **Konkrétní uživatelé a skupiny** | Tuto možnost vyberte, pokud chcete, aby se tento balíček přístupu mohl vyžádat jenom pro uživatele a skupiny v adresáři, který jste zadali. |
    | **Všichni členové (kromě hostů)** | Tuto možnost vyberte, pokud chcete, aby všichni členové členských uživatelů v adresáři mohli požádat o tento balíček přístupu. Tato možnost neobsahuje žádné uživatele typu Host, které jste mohli pozvat do svého adresáře. |
    | **Všichni uživatelé (včetně hostů)** | Tuto možnost vyberte, pokud chcete, aby se tento balíček přístupu mohl vyžádat všichni členové a uživatelé typu Host v adresáři. |

    Uživatelé typu Host odkazují na externí uživatele, kteří byli pozváni do vašeho adresáře pomocí [Azure AD B2B](../external-identities/what-is-b2b.md). Další informace o rozdílech mezi členskými uživateli a uživateli typu Host najdete v tématu [co jsou výchozí oprávnění uživatele v Azure Active Directory?](../fundamentals/users-default-permissions.md).

1. Pokud jste vybrali **konkrétní uživatele a skupiny**, klikněte na **Přidat uživatele a skupiny**.

1. V podokně vybrat uživatele a skupiny vyberte uživatele a skupiny, které chcete přidat.

    ![Přístup k balíčku – požadavky – výběr uživatelů a skupin](./media/entitlement-management-access-package-request-policy/select-users-groups.png)

1. Klikněte na **Vybrat** a přidejte uživatele a skupiny.

1. Pokud chcete vyžadovat schválení, použijte postup v části [Změna nastavení schvalování pro balíček přístupu ve službě Azure AD – Správa oprávnění](entitlement-management-access-package-approval-policy.md) ke konfiguraci nastavení schvalování.

1. Přejít do oddílu [Povolit žádosti](#enable-requests) .
 
## <a name="for-users-not-in-your-directory"></a>Pro uživatele, kteří nejsou ve vašem adresáři

 **Uživatelé, kteří nejsou v adresáři** , odkazují na uživatele, kteří jsou v jiném adresáři nebo adresáři služby Azure AD. Tito uživatelé možná nebyli přizváni do vašeho adresáře. Je potřeba nakonfigurovat adresáře Azure AD tak, aby umožňovaly pozvánky v **omezeních spolupráce**. Další informace najdete v tématu [Povolení externí spolupráce B2B a Správa toho, kdo může pozvat hosty](../external-identities/delegate-invitations.md).

> [!NOTE]
> Uživatelský účet hosta se vytvoří pro uživatele, který ještě není ve vašem adresáři, jehož žádost je schválená nebo automaticky schválená. Host bude pozván, ale neobdrží e-mail s pozvánkou. Místo toho obdrží e-mail po doručení jejich přiřazení balíčku přístupu. Ve výchozím nastavení platí, že pokud už uživatel typu host již nemá žádná přiřazení balíčku přístupu, protože jejich poslední přiřazení vypršela nebo byla zrušena, bude tento uživatelský účet hosta zablokován a následně odstraněn. Pokud chcete, aby uživatelé typu Host zůstali ve vašem adresáři po neomezenou dobu, a to i v případě, že nemají žádná přiřazení balíčků přístupu, můžete změnit nastavení pro konfiguraci správy nároků. Další informace o objektu uživatele hosta najdete v tématu [vlastnosti Azure Active Directoryho uživatele spolupráce B2B](../external-identities/user-properties.md).

Postupujte podle těchto kroků, pokud chcete uživatelům, kteří nejsou ve vašem adresáři, vyžadovat tento balíček přístupu:

1. V části **Uživatelé, kteří můžou požádat o přístup** , klikněte na uživatele, kteří **nejsou ve vašem adresáři**.

    Když vyberete tuto možnost, zobrazí se nové možnosti.

    ![Přístup k balíčku – požadavky – pro uživatele, kteří nejsou ve vašem adresáři](./media/entitlement-management-access-package-request-policy/for-users-not-in-your-directory.png)

1. Vyberte jednu z následujících možností:

    |  |  |
    | --- | --- |
    | **Konkrétní propojené organizace** | Tuto možnost vyberte, pokud chcete vybrat ze seznamu organizací, které jste přidali jako správce. Přístup k tomuto balíčku můžou vyžádat všichni uživatelé z vybraných organizací. |
    | **Všechny nakonfigurované připojené organizace** | Tuto možnost vyberte, pokud všichni uživatelé ze všech nakonfigurovaných připojených organizací můžou tento balíček přístupu požádat. Pouze uživatelé z konfigurovaných organizací můžou žádat o přístup k balíčkům, které se zobrazují uživatelům ze všech nakonfigurovaných organizací. |
    | **Všichni uživatelé (všechny připojené organizace a všichni noví externí uživatelé)** | Tuto možnost vyberte, pokud má některý uživatel na Internetu možnost požádat o tento balíček přístupu.  Pokud nepatří do připojené organizace ve vašem adresáři, při vyžádání balíčku se automaticky vytvoří připojená organizace. Automaticky vytvořená propojená organizace bude v **navrhovaném** stavu. Další informace o navrhovaných stavech najdete v tématu [Vlastnosti stavu propojených organizací](entitlement-management-organization.md#state-properties-of-connected-organizations). |

    Připojená organizace je externí adresář nebo doména služby Azure AD, ke kterým máte relaci.

1. Pokud jste vybrali **konkrétní připojené organizace**, klikněte na **Přidat adresáře** a vyberte si ze seznamu připojených organizací, které jste přidali jako správce.

1. Zadejte název nebo název domény, ve kterém chcete hledat dříve připojenou organizaci.

    ![Přístup k balíčku – požadavky – výběr adresářů](./media/entitlement-management-access-package-request-policy/select-directories.png)

    Pokud organizace, se kterou chcete spolupracovat, není v seznamu, můžete požádat správce, aby ho přidal jako připojenou organizaci. Další informace najdete v tématu [Přidání připojené organizace](entitlement-management-organization.md).

1. Po výběru všech připojených organizací klikněte na **Vybrat**.

    > [!NOTE]
    > Všichni uživatelé z vybraných připojených organizací budou moct požádat o tento balíček přístupu. To zahrnuje uživatele v Azure AD ze všech subdomén přidružených k organizaci, pokud tyto domény nejsou blokované seznamem povolených a zakázaných aplikací Azure B2B. Další informace najdete v tématu [Povolení nebo blokování pozvánek uživatelům B2B z konkrétních organizací](../external-identities/allow-deny-list.md).

1. Pokud chcete vyžadovat schválení, použijte postup v části [Změna nastavení schvalování pro balíček přístupu ve službě Azure AD – Správa oprávnění](entitlement-management-access-package-approval-policy.md) ke konfiguraci nastavení schvalování.
 
1. Přejít do oddílu [Povolit žádosti](#enable-requests) .

## <a name="none-administrator-direct-assignments-only"></a>Žádné (pouze přímé přiřazení správce)

Postupujte podle těchto kroků, pokud chcete vynechat žádosti o přístup a umožníte správcům, aby k tomuto balíčku přístupu přiřadili přímo konkrétní uživatele. Uživatelé nebudou muset vyžadovat balíček přístupu. Pořád můžete nastavit nastavení životního cyklu, ale neexistují žádná nastavení žádostí.

1. V části **Uživatelé, kteří můžou požádat o přístup** , klikněte na možnost **žádný (pouze přímá přiřazení správců**).

    ![Přístup k balíčku – požadavky – pouze přímá přiřazení správců](./media/entitlement-management-access-package-request-policy/none-admin-direct-assignments-only.png)

    Po vytvoření balíčku pro přístup můžete k balíčku pro přístup přímo přiřadit konkrétní interní a externí uživatele. Pokud zadáte externího uživatele, vytvoří se ve vašem adresáři uživatelský účet hosta. Informace o tom, jak přímo přiřazovat uživatele, najdete v tématu [zobrazení, přidání a odebrání přiřazení balíčku pro přístup](entitlement-management-access-package-assignments.md).

1. Přejděte do oddílu [Povolit žádosti](#enable-requests) .


## <a name="open-and-edit-an-existing-policy-of-request-settings"></a>Otevření a úprava existující zásady nastavení požadavků

Chcete-li změnit nastavení žádosti a schválení pro balíček pro přístup, je nutné otevřít odpovídající zásadu. Pomocí těchto kroků otevřete a upravte nastavení žádosti pro balíček pro přístup:

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Klikněte na **zásady** a potom klikněte na zásadu, kterou chcete upravit.

    V dolní části stránky se otevře podokno Podrobnosti o zásadách.

    ![Přístupový balíček – podokno podrobností zásad](./media/entitlement-management-shared/policy-details.png)

1. Zásadu upravíte kliknutím na **Upravit** .

    ![Přístup k balíčku – upravit zásadu](./media/entitlement-management-shared/policy-edit.png)

1. Kliknutím na kartu **žádosti** otevřete nastavení žádosti.

1. Podle pokynů v předchozích částech můžete podle potřeby změnit nastavení požadavku.

1. Přejít do oddílu [Povolit žádosti](#enable-requests) .

## <a name="enable-requests"></a>Povolit žádosti

1. Pokud chcete, aby byl balíček přístupu hned dostupný pro uživatele v zásadách žádosti, přesuňte přepínač Povolit na **Ano**.

    Po dokončení vytváření balíčku pro přístup ho můžete v budoucnu kdykoli povolit.

    Pokud jste vybrali **možnost žádné (pouze oprávnění správce)** a nastavíte možnost povolit na hodnotu **ne**, správci nebudou moci přiřadit tento balíček přístupu přímo.

    ![Přístup k balíčku – nastavení zásad – Povolit zásadu](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. Klikněte na **Next** (Další).

1. Pokud chcete, aby žadatelé při žádosti o přístup k balíčku pro přístup mohli poskytnout další informace, použijte postup v [nastavení změnit schválení a informace o žadateli (Preview) pro balíček přístupu ve službě Azure AD – Správa nároků](entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview) na konfiguraci informací o žadateli (Preview).

1. Nakonfigurujte nastavení životního cyklu.

1. Pokud upravujete zásadu, klikněte na **aktualizovat**. Pokud přidáváte novou zásadu, klikněte na **vytvořit**.

## <a name="next-steps"></a>Další kroky

- [Změna nastavení schválení balíčku pro přístup](entitlement-management-access-package-approval-policy.md)
- [Změnit nastavení životního cyklu pro balíček pro přístup](entitlement-management-access-package-lifecycle-policy.md)
- [Zobrazit žádosti pro balíček pro přístup](entitlement-management-access-package-requests.md)
