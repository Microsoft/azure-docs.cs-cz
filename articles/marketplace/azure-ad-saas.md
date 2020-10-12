---
title: Azure Active Directory a transakční nabídky SaaS na komerčním webu Marketplace
description: Přečtěte si, jak Azure Active Directory funguje s SaaS nabídkami, které nabízí Microsoft Commercial Marketplace.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 5a09105dac89f3dc241140f16f3d4be72cc97493
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89483622"
---
# <a name="azure-ad-and-transactable-saas-offers-in-the-commercial-marketplace"></a>Nabídky Azure AD a SaaS s podporou transakcí na komerčním webu Marketplace

Cloudová služba pro správu identit a přístupu Microsoftu, [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD), pomáhá uživatelům přihlašovat se k interním a externím prostředkům a přistupovat k nim. V rámci komerčního tržiště Microsoftu zpřístupňuje služba Azure AD SaaS, které nabízí snazší a bezpečnější pro všechny, včetně vydavatelů, nákupčích a uživatelů. S Azure AD můžou vydavatelé automatizovat zřizování uživatelů s aplikacemi SaaS (software jako služba) a kupující samy si můžou tyto zřízené uživatele spravovat. 

[Jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (SSO) Azure AD poskytuje zabezpečení a pohodlí, když se uživatelé přihlásí k aplikacím v Azure AD. Rychlejší zapojení a optimalizovaná prostředí také inspirovat kupující a důvěru uživatelů od velmi první interakce s aplikací SaaS vydavatele. To představuje pozitivní dojem, který sestaví viditelnost a doporučuje opakování firmy.

Podle pokynů v tomto článku vám pomůžete certifikovat nabídku SaaS na komerčním webu Marketplace. Pokud chcete získat další informace o certifikaci, přečtěte si podrobné [zásady certifikace komerčního tržiště](https://aka.ms/commercial-marketplace-certification-policies#100-general), včetně těch, které jsou [specifické pro SaaS](https://aka.ms/commercial-marketplace-certification-policies#1000-software-as-a-service-saas).

## <a name="before-you-begin"></a>Než začnete

Když [vytvoříte nabídku SaaS](./partner-center-portal/create-new-saas-offer.md) v partnerském centru, zvolíte ze sady specifických možností výpisu, která se zobrazí v seznamu nabídek. Vaše volba určuje, jak se vaše nabídka používá na komerčním webu Marketplace. Nabídky prodávané prostřednictvím Microsoftu se nazývají transakční nabídky. Zákazníkům účtujeme vaše jménem všechny nabídky s podporou transakcí. Pokud se rozhodnete prodávat prostřednictvím Microsoftu a máte za vás transakce s hostováním v USA (možnost **Ano** ), pak jste se rozhodli vytvořit nabídku s podporou transakcí a tento článek je určený pro vás. Doporučujeme si ho přečíst v celém rozsahu.

Pokud se rozhodnete, že nabídku zobrazíte jenom prostřednictvím komerčního tržiště a nezávisle na tom **, že** nezávisle na tom, jak se budou k vaší nabídce pracovat, máte k dispozici tři možnosti, jak budou mít potenciální zákazníci přístup k vaší nabídce: získat hned (zdarma), bezplatnou zkušební verzi Pokud vyberete **získat hned (zdarma)** nebo **bezplatnou zkušební verzi**, Tento článek není pro vás. Místo toho si přečtěte další informace [v tématu sestavení cílové stránky bezplatné nebo zkušební SaaS nabídky na komerčním webu Marketplace](./azure-ad-free-or-trial-landing-page.md) . Pokud vyberete **kontakt mi**, neexistují žádné přímé zodpovědnosti vydavatele. Pokračujte v vytváření vaší nabídky v partnerském centru.

## <a name="how-azure-ad-works-with-the-commercial-marketplace-for-saas-offers"></a>Jak Azure AD funguje s komerčním Marketplace pro nabídky SaaS

Azure AD umožňuje bezproblémové nakupování, plnění a správu komerčních řešení na webu Marketplace. Obrázek 1 znázorňuje způsob, jakým Vydavatel, kupující a uživatel si můžou koupit a aktivovat předplatné. Také ukazuje, jak zákazníci používají a spravují aplikace SaaS, které získají z komerčního tržiště. Pro účely tohoto obrázku je kupujícím uživatel aplikace SaaS, který iniciuje nákup z komerčního tržiště.

Jak je znázorněno na obrázku 1, když nákupčí vybere vaši nabídku, zahájí řetěz pracovních postupů, které zahrnují nákup, předplatné a správu uživatelů. V rámci tohoto řetězce je Vydavatel zodpovědný za určité požadavky, přičemž společnost Microsoft poskytuje podporu na klíčových místech.

***Obrázek 1: použití Azure AD pro nabídky SaaS na komerčním webu Marketplace***

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow.png" alt-text="Znázorňuje postupy správy nákupu, správy předplatného a volitelného procesu správy uživatelů.":::

Následující části obsahují podrobné informace o požadavcích na jednotlivé kroky procesu.

## <a name="process-steps-for-purchase-management"></a>Kroky procesu pro správu nákupu

Na tomto obrázku je znázorněno čtyři kroky procesu pro správu nákupu.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-1-4.png" alt-text="Znázorňuje postupy správy nákupu, správy předplatného a volitelného procesu správy uživatelů.":::

Tato tabulka poskytuje podrobnosti o krocích procesu správy nákupu.

| Krok procesu | Akce vydavatele | Doporučené nebo vyžadované pro vydavatele |
| ------------ | ------------- | ------------- |
| 1. nákupčí se přihlásí k komerčnímu tržišti pomocí své identity ID Azure a vybere SaaS nabídku. | Není vyžadována žádná akce vydavatele. | Nelze použít |
| 2. po zakoupení kupující vybere **konfigurovat účet** v Azure Marketplace nebo **nakonfigurovat nyní** v AppSource, který nasměruje kupující na úvodní stránku vydavatele pro tuto nabídku. Kupující musí být schopný se přihlašovat k aplikaci SaaS vydavatele pomocí jednotného přihlašování služby Azure AD a musí vyžadovat jenom minimální souhlas, který nevyžaduje schválení správcem Azure AD. | Navrhněte [cílovou stránku](azure-ad-transactable-saas-landing-page.md) nabídky tak, aby získala uživatele s identitou Azure AD nebo účet Microsoft (MSA), a usnadňuje jakékoli další zřizování nebo nastavení, které je potřeba. | Vyžadováno |
| 3. Vydavatel požaduje informace o nákupu z rozhraní API pro plnění SaaS. | Pomocí [přístupového tokenu](./partner-center-portal/pc-saas-registration.md) generovaného z ID aplikace cílové stránky [zavolejte koncový bod](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) , který načte konkrétní informace o nákupu. | Vyžadováno |
| 4. prostřednictvím služby Azure AD a rozhraní Microsoft Graph API shromažďuje Vydavatel údaje o společnosti a uživatelích požadovaných ke zřízení kupujícího v aplikaci SaaS vydavatele.  | Rozložíte uživatelský token Azure AD tak, aby našli jméno a e-mail, nebo [volejte rozhraní Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api) a pomocí delegovaných oprávnění [načetli informace](https://docs.microsoft.com/graph/api/user-get) o přihlášeném uživateli. | Vyžadováno |
||||

## <a name="process-steps-for-subscription-management"></a>Kroky procesu pro správu předplatného

Tento obrázek znázorňuje dva kroky procesu pro správu předplatného.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-5-6.png" alt-text="Znázorňuje postupy správy nákupu, správy předplatného a volitelného procesu správy uživatelů.":::

Tato tabulka obsahuje podrobné informace o krocích procesu správy předplatného.

| Krok procesu | Akce vydavatele | Doporučené nebo vyžadované pro vydavatele |
| ------------ | ------------- | ------------- |
| 5. Vydavatel spravuje odběr aplikace SaaS prostřednictvím rozhraní API pro plnění SaaS. | Pomocí [rozhraní API pro plnění SaaS](./partner-center-portal/pc-saas-fulfillment-api-v2.md)zpracujte změny předplatného a další úlohy správy.<br><br>Tento krok vyžaduje přístupový token, jak je popsáno v kroku 3 procesu. | Vyžadováno |
| 6. Pokud používáte ceny účtované podle objemu dat, vydavatel vygeneruje události využití do rozhraní API služby měření. | Pokud vaše aplikace SaaS nabízí fakturaci na základě využití, vyžádejte si oznámení o využití prostřednictvím [rozhraní API služby měření na webu Marketplace](./partner-center-portal/marketplace-metering-service-apis.md).<br><br>Tento krok vyžaduje přístupový token, jak je popsáno v kroku 3. | Vyžadováno pro měření |
||||

## <a name="process-steps-for-user-management"></a>Kroky procesu pro správu uživatelů

Na tomto obrázku jsou tři kroky procesu správy uživatelů.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-7-9.png" alt-text="Znázorňuje postupy správy nákupu, správy předplatného a volitelného procesu správy uživatelů.":::

Kroky procesu 7 až 9 jsou volitelné kroky procesu správy uživatelů. Poskytují další výhody pro vydavatele, kteří podporují jednotné přihlašování Azure AD. Tato tabulka obsahuje podrobné informace o krocích procesu správy uživatelů.

| Krok procesu | Akce vydavatele | Doporučené nebo vyžadované pro vydavatele |
| ------------ | ------------- | ------------- |
| 7. správci Azure AD na společnosti kupujícího můžou volitelně spravovat přístup pro uživatele a skupiny přes Azure AD. | Žádná akce vydavatele není nutná, pokud je služba Azure AD SSO nastavena pro uživatele (krok 9). | Nelze použít |
| 8. služba zřizování Azure AD komunikuje se změnami mezi Azure AD a aplikací SaaS vydavatele. | [Implementujte koncový bod SCIM](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups) pro příjem aktualizací ze služby Azure AD při přidávání a odebírání uživatelů. | Doporučeno |
| 9. Jakmile je aplikace oprávněná a zřízená, uživatelé z společnosti kupujícího můžou použít jednotné přihlašování Azure AD k přihlášení k aplikaci SaaS vydavatele. | [Pomocí služby Azure AD SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) můžete uživatelům povolit, aby se jednou přihlásili pomocí jednoho účtu do aplikace SaaS vydavatele. | Doporučeno |
||||

## <a name="next-steps"></a>Další kroky

- [Sestavení cílové stránky pro SaaS nabídku s podporou transakcí na komerčním webu Marketplace](azure-ad-transactable-saas-landing-page.md)
- [Sestavení cílové stránky bezplatné nebo zkušební SaaS nabídky na komerčním webu Marketplace](azure-ad-free-or-trial-landing-page.md)
- [Jak vytvořit nabídku SaaS na komerčním webu Marketplace](create-new-saas-offer.md)
