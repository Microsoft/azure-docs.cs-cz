---
title: Co je spolupráce B2B v Azure Active Directory? | Dokumenty Microsoft
description: Spolupráce B2B v Azure Active Directory podporuje přístup uživatelů typu host, abyste mohli s externími partnery bezpečně sdílet prostředky a spolupracovat s nimi.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 09/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.openlocfilehash: d8aecb4dd8c680c876cf43f6c0bcbe1a4f911f41
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55082108"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>Co je přístup uživatelů typu host při spolupráci B2B v Azure Active Directory?

Při spolupráci B2B (business-to-business) v Azure Active Directory (Azure AD) můžete bezpečně sdílet aplikace a služby společnosti s uživateli typu host z libovolné organizace a současně si zachovat kontrolu nad vlastními podnikovými daty. Spolupracujte bezpečně s velkými i malými externími partnery, i když nemají Azure AD ani oddělení IT. Díky jednoduchému postupu založenému na pozvánce a jejím uplatnění můžou partneři, kteří přistupují k vašim firemním prostředkům, používat vlastní přihlašovací údaje. Vývojáři můžou v rozhraní API pro spolupráci B2B v Azure AD přizpůsobit postup pozvání nebo napsat aplikace, jako jsou portály pro samoobslužnou registraci.

Podívejte se na video, ve kterém se dozvíte, jak bezpečně spolupracovat s uživateli typu host tím, že je pozvete, aby k přihlášení do vašich podnikových aplikací a služeb používali vlastní identity.

Následující video nabízí praktický přehled.

>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

## <a name="collaborate-with-any-partner-using-their-identities"></a>Spolupráce s partnery s využitím jejich vlastních identit
Při spolupráci B2B v Azure AD partner používá ke správě identit vlastní řešení. Vaší organizaci tak v této souvislosti nevznikají další režijní náklady. 
- Partner používá vlastní identity a přihlašovací údaje, a nepotřebuje k tomu Azure AD. 
- Nemusíte spravovat externí účty ani hesla. 
- Nemusíte synchronizovat účty ani spravovat jejich životní cyklus.  

![Přidání člena](media/what-is-b2b/add-member.png)

## <a name="invite-guest-users-with-a-simple-invitation-and-redemption-process"></a>Pozvání uživatelů typu host prostřednictvím jednoduché pozvánky a jejího uplatnění
Uživatelé typu host používají při přihlášení k vašim aplikacím a službám vlastní pracovní, školní nebo sociální identitu. Pokud uživatel typu host nemá účet Microsoft ani účet Azure AD, vytvoří se jim po uplatnění pozvánky. 
- K pozvání uživatelů typu host použijte e-mailovou identitu, kterou si sami vyberou.
- Pošlete jim přímo odkaz na aplikaci nebo jim pošlete pozvánku do vlastního přístupového panelu uživatele typu host. 
- Uživatelé typu host provedou při přihlášení a uplatnění pozvánky několik jednoduchých kroků.

![Přístupový panel](media/what-is-b2b/consentscreen.png)

## <a name="use-policies-to-securely-share-your-apps-and-services"></a>Použití zásad při bezpečném sdílení aplikací a služeb
K ochraně firemního obsahu můžete použít zásady autorizace. Zásady podmíněného přístupu, jako je vícefaktorové ověřování, je možné vynutit:
- Na úrovni tenanta
- Na úrovni aplikace
- Pro konkrétní uživatele typu host, abyste chránili firemní aplikace a data

![Přidání uživatelů typu host](media/what-is-b2b/tutorial-mfa-policy-2.png)


## <a name="easily-add-guest-users-in-the-azure-ad-portal"></a>Jednoduché přidání uživatelů typu host na webu Azure Portal

Jako správce můžete do své organizace na webu Azure Portal jednoduše přidat uživatele typu host.
- V Azure AD můžete vytvořit nového uživatele typu host podobně, jako byste přidávali nového uživatele.
- Uživateli typu host ihned přijde přizpůsobená pozvánka, ze které se může přihlásit ke svému přístupovému panelu.
- Uživatele typu host můžete v adresáři přiřazovat k aplikacím nebo skupinám.  

![Přidání uživatelů typu host](media/what-is-b2b/adding-b2b-users-admin.png)

## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>Nechte vlastníky aplikací a skupin spravovat vlastní uživatele typu host

Správu uživatelů typu host můžete delegovat na vlastníky aplikací, aby do libovolné aplikace, kterou chtějí sdílet, mohli přímo přidávat uživatele typu host. Přitom nezáleží, jestli je aplikace od Microsoftu. 
 - Správci nastaví samoobslužnou správu aplikace a skupiny.
 - Uživatelé, kteří nejsou správci, použijí k přidání uživatelů typu host do aplikací nebo skupin [přístupový panel](https://myapps.microsoft.com).

![Přidání uživatelů typu host](media/what-is-b2b/access-panel-manage-app.png)

## <a name="use-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Použití rozhraní API a ukázkového kódu k jednoduchému připojení vytvořených aplikací

Připojte externí partnery tak, jak to vyhovuje potřebám vaší organizace.
- K přizpůsobení připojovacího prostředí můžete použít [rozhraní API s pozváním ke spolupráci B2B](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation), včetně vytvoření samoobslužných přihlašovacích portálů. 
- Použití ukázkového kódu, poskytujeme pro samoobslužný portál [na Githubu](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

![Registrační portál](media/what-is-b2b/sign-up-portal.png)

## <a name="next-steps"></a>Další postup

- [Pokyny k licencování spolupráce B2B v Azure AD](licensing-guidance.md)
- [Přidání uživatelů typu host na portálu pro spolupráci B2B](add-users-administrator.md)
- [Vysvětlení postupu při uplatnění pozvánky](redemption-experience.md)
- A jako vždy se můžete spojit s naším produktovým týmem a sdělit mu své připomínky, diskutovat nebo posílat své návrhy prostřednictvím [technické komunity Microsoftu](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
