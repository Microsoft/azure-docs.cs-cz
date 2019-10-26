---
title: Řízení přístupu pro externí uživatele ve správě nároků Azure AD (Preview) – Azure Active Directory
description: Přečtěte si o nastaveních, která můžete zadat pro řízení přístupu externích uživatelů v Azure Active Directory správě nároků.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3794f409b2cdc11373dc330099e5ff93d65a2a1
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934389"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management-preview"></a>Řízení přístupu pro externí uživatele ve správě nároků Azure AD (Preview)

> [!IMPORTANT]
> Správa opravňujících k Azure Active Directory (Azure AD) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Správa nároků Azure AD využívá [Azure AD Business-to-Business (B2B)](../b2b/what-is-b2b.md) a spolupracuje s lidmi mimo vaši organizaci v jiném adresáři. Pomocí Azure AD B2B se externí uživatelé ověřují do svého domovského adresáře, ale mají v adresáři reprezentace. Reprezentace v adresáři umožňuje uživateli přiřadit přístup k vašim prostředkům.

Tento článek popisuje nastavení, která můžete zadat pro řízení přístupu pro externí uživatele.

## <a name="how-entitlement-management-can-help"></a>Jak může správa nároků pomáhat

Pokud používáte prostředí pozvánky [B2B Azure AD](../b2b/what-is-b2b.md) , musíte už znát e-mailové adresy externích uživatelů typu Host, které chcete uvést do svého adresáře prostředků a pracovat s. To funguje skvěle, když pracujete na kratším nebo krátkodobém projektu a už znáte všechny účastníky, ale to je těžké spravovat, pokud máte spoustu uživatelů, se kterými chcete pracovat, nebo pokud se účastníci v průběhu času změnili.  Můžete například pracovat s jinou organizací a mít jeden kontaktní bod s touto organizací, ale v průběhu času budou mít přístup i další uživatelé z této organizace.

Díky správě nároků můžete definovat zásadu, která uživatelům ze zadaných organizací umožní získat přístup k balíčku pro přístup sami. Můžete určit, jestli se vyžaduje schválení, a datum vypršení platnosti pro přístup. Pokud se vyžaduje schválení, můžete taky pozvat jednoho nebo více uživatelů z externí organizace do svého adresáře a označit je jako schvalovatele – protože budou mít pravděpodobně jistotu, které externí uživatelé z jejich organizace potřebují přístup. Po nakonfigurování přístupového balíčku můžete odeslat odkaz na kontaktní osobu (Sponzor) v externí organizaci. Tento kontakt může sdílet s ostatními uživateli v externí organizaci a může použít tento odkaz k vyžádání přístupového balíčku. Tento odkaz můžou použít i uživatelé z této organizace, kteří už byli pozváni do vašeho adresáře.

Pokud je žádost schválena, Správa oprávnění zřídí uživatele, který má nezbytný přístup, který může zahrnovat pozvání uživatele, pokud ještě nejsou ve vašem adresáři. Azure AD vytvoří pro ně automaticky účet hosta B2B. Všimněte si, že správce mohl dříve omezit, které organizace jsou povolené pro spolupráci, nastavením [seznamu povolených nebo zakázaných aplikací B2B](../b2b/allow-deny-list.md) povolit nebo zablokovat pozvánky jiným organizacím.  Pokud uživatel není povolený seznamem povolených nebo blokovaných uživatelů, pak nebude pozván.

Vzhledem k tomu, že nechcete, aby byl externí uživatel přístupný k poslednímu typu navždy, zadejte v zásadách datum vypršení platnosti, například 180 dní. Pokud jejich přístup není rozšířený, Správa oprávnění po 180 dnů odebere veškerý přístup spojený s tímto balíčkem přístupu. Ve výchozím nastavení platí, že pokud uživatel, který byl pozván prostřednictvím správy oprávnění, nemá žádné jiné přiřazení balíčků přístupu, pak když ztratí své poslední přiřazení, účet Guest se zablokuje přihlášení po dobu 30 dnů a následně se odebere. Tím se zabrání šíření zbytečných účtů. Jak je popsáno v následujících částech, tato nastavení lze konfigurovat.

## <a name="how-access-works-for-external-users"></a>Jak funguje přístup pro externí uživatele

Následující diagram a kroky poskytují přehled o tom, jak mají externí uživatelé udělen přístup k balíčku přístupu.

![Diagram znázorňující životní cyklus externích uživatelů](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. [Přidáváte připojenou organizaci](entitlement-management-organization.md) pro adresář nebo doménu služby Azure AD, se kterým chcete spolupracovat.

1. Ve svém adresáři vytvoříte balíček pro přístup, který obsahuje zásady [pro uživatele, kteří nejsou ve vašem adresáři](entitlement-management-access-package-create.md#for-users-not-in-your-directory).

1. Pošlete odkaz na [portál pro přístup](entitlement-management-access-package-settings.md) k vašemu kontaktu v externí organizaci, kterou můžou sdílet s uživateli, aby si vyžádali balíček pro přístup.

1. Externí uživatel (v tomto příkladu**žadatel a** ) používá odkaz Můj portál přístupu k [vyžádání přístupu](entitlement-management-request-access.md) k balíčku přístupu. Způsob přihlášení uživatele závisí na typu ověřování adresáře nebo domény definované v připojené organizaci.

1. Schvalovatel [žádost schválí](entitlement-management-request-approve.md) (nebo se žádost automaticky schválí).

1. Požadavek přejde do [stavu doručování](entitlement-management-process.md).

1. Pomocí procesu pozvání B2B se ve vašem adresáři vytvoří uživatelský účet hosta (v tomto příkladu**žadatel a (host)** ). Pokud je definovaný seznam [povolených nebo zakázaných](../b2b/allow-deny-list.md) seznamů, použije se nastavení list.

1. Uživatel typu host má přiřazený přístup ke všem prostředkům v balíčku pro přístup. Může trvat nějakou dobu, než se změny provedou v Azure AD a dalších online službách Microsoftu nebo připojených aplikacích SaaS. Další informace najdete v tématu [Změna použití změn](entitlement-management-access-package-resources.md#when-changes-are-applied).

1. Externí uživatel obdrží e-mail s oznámením, že byl [dodán](entitlement-management-process.md)jejich přístup.

1. Chcete-li získat přístup k prostředkům, může externí uživatel kliknout na odkaz v e-mailu nebo se pokusit o přístup k libovolnému prostředku adresáře přímo za účelem dokončení procesu pozvánky.

1. V závislosti na nastavení zásad vyprší platnost přiřazení balíčku přístupu pro externího uživatele a odebrání přístupu externího uživatele.

1. V závislosti na životním cyklu nastavení externích uživatelů platí, že když externí uživatel už nemá žádná přiřazení balíčků přístupu, externímu uživateli se zablokuje přihlášení a účet uživatele hosta se odebere z vašeho adresáře.

## <a name="manage-the-lifecycle-of-external-users"></a>Správa životního cyklu externích uživatelů

Můžete vybrat, co se stane, když externí uživatel, který byl pozván do vašeho adresáře prostřednictvím schválené žádosti balíčku přístupu, už nemá žádná přiřazení balíčků přístupu. K tomu může dojít, když uživatel odstane všechna přiřazení balíčků přístupu nebo poslední přiřazení balíčku pro přístup vyprší. Ve výchozím nastavení platí, že když externí uživatel už nemá žádné přiřazení balíčku přístupu, zablokuje se přihlášení ke svému adresáři. Po 30 dnech se jejich uživatelský účet hosta odebere z vašeho adresáře.

**Požadovaná role:** Globální správce nebo Správce uživatelů

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V levé nabídce v části **Správa nároků** klikněte na **Nastavení**.

1. Klikněte na **Upravit**.

    ![Nastavení pro správu životního cyklu externích uživatelů](./media/entitlement-management-external-users/settings-external-users.png)

1. V části **Spravovat životní cyklus externích uživatelů** vyberte různá nastavení pro externí uživatele.

1. Když externí uživatel ztratí své poslední přiřazení ke všem balíčkům přístupu, pokud je chcete zablokovat, aby se přihlašovat k tomuto adresáři, nastavte **blokovat externímu uživateli přístup k tomuto adresáři** na **Ano**.

    > [!NOTE]
    > Pokud se uživateli zablokuje přihlášení k tomuto adresáři, uživatel nebude moct znovu požádat o přístup k balíčku nebo požádat o další přístup v tomto adresáři. Nekonfigurujte blokování v přihlašování, pokud budou následně potřebovat požádat o přístup k jiným balíčkům přístupu.

1. Když externí uživatel ztratí své poslední přiřazení ke všem balíčkům přístupu, pokud chcete odebrat svůj uživatelský účet hosta v tomto adresáři, nastavte **Odebrat externí uživatele** na **Ano**.

    > [!NOTE]
    > Správa nároků odebírá jenom účty, které byly pozvány prostřednictvím správy nároků. Také si všimněte, že uživatel bude zablokován přihlášení a odebrán z tohoto adresáře i v případě, že byl tento uživatel přidán do prostředků v tomto adresáři, které nepoužívaly přístup k přiřazení balíčků. Pokud se host nachází v tomto adresáři před přijetím přiřazení přístupových balíčků, zůstane. Pokud ale Host byl přizván prostřednictvím přiřazení balíčku přístupu a po jeho pozvaní byl také přiřazen k webu OneDrivu pro firmy nebo SharePointu Online, bude stále odebrán.

1. Pokud chcete odebrat uživatelský účet hosta v tomto adresáři, můžete nastavit počet dní, než se odebere. Pokud chcete odebrat uživatelský účet hosta, jakmile ztratí poslední přiřazení na všechny balíčky pro přístup, nastavte **počet dní před odebráním externího uživatele z tohoto adresáře** na **hodnotu 0**.

1. Klikněte na **Uložit**.

## <a name="enable-a-catalog-for-external-users"></a>Povolení katalogu pro externí uživatele

Při vytváření [nového katalogu](entitlement-management-catalog-create.md)je k dispozici nastavení umožňující uživatelům z externích adresářů vyžadovat přístup k balíčkům v katalogu. Pokud nechcete, aby externí uživatelé měli oprávnění k vyžádání balíčků přístupu v katalogu, nastavte možnost **povoleno pro externí uživatele** na **ne**.

**Požadovaná role:** Globální správce, Správce uživatelů nebo vlastník katalogu

![Podokno nového katalogu](./media/entitlement-management-shared/new-catalog.png)

Toto nastavení můžete změnit i po vytvoření katalogu.

![Upravit nastavení katalogu](./media/entitlement-management-shared/catalog-edit.png)

## <a name="next-steps"></a>Další kroky

- [Přidat připojenou organizaci](entitlement-management-organization.md)
- [Pro uživatele, kteří nejsou ve vašem adresáři](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [Vytvoření a správa katalogu prostředků](entitlement-management-catalog-create.md)
