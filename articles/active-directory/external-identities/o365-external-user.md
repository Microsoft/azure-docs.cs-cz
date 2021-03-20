---
title: Microsoft 365 externí sdílení a spolupráce B2B – Azure AD
description: Popisuje sdílení prostředků s externími partnery pomocí Microsoft 365 a Azure Active Directory spolupráci B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: d003008ea5b0d2591574f6f488b0145ee6f08a5e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100008124"
---
# <a name="microsoft-365-external-sharing-and-azure-active-directory-azure-ad-b2b-collaboration"></a>Spolupráce s Microsoft 365 externích sdílení a Azure Active Directory (Azure AD) B2B

V rámci spolupráce Azure AD B2B i Microsoft 365 externí sdílení (OneDrive, SharePoint Online, sjednocené skupiny atd.) se pomocí Azure AD B2B ověřují externí uživatelé.

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Jak se Azure AD B2B liší od externího sdílení v SharePointu Online?

OneDrive nebo SharePoint Online má samostatného správce pozvánk. Podpora pro externí sdílení na OneDrivu nebo SharePointu Online se spustila dřív, než Azure AD vyvinula podporu. V průběhu času externí sdílení OneDrivu nebo SharePointu Online narostlo několik funkcí a spousta milionů uživatelů, kteří používají model sdílení daného produktu v rámci sestavení. Existuje však několik drobných rozdílů mezi tím, jak funguje externí sdílení OneDrive/SharePoint Online a jak funguje spolupráce Azure AD B2B. Další informace o externím sdílení OneDrivu nebo SharePointu Online najdete v [přehledu externího](/sharepoint/external-sharing-overview)sdílení. Tento proces se obecně liší od Azure AD B2B v těchto ohledech:

- OneDrive nebo SharePoint Online přidá uživatele do adresáře po uplatnění svých pozvání uživateli. Takže před uplatněním se uživatel na portálu Azure AD nezobrazuje. Pokud již jiný web pozve uživatele do té doby, je vygenerována nová pozvánka. Pokud ale používáte spolupráci B2B Azure AD, uživatelé se hned přidají na pozvánku, aby se zobrazovaly všude.

- Prostředí pro uplatnění na OneDrivu nebo SharePointu Online vypadá jinak než ve spolupráci s B2B Azure AD. Jakmile uživatel uplatní pozvánku, bude vypadat podobně.

- Spolupráce Azure AD B2B pozvaní uživatelů může být převzata z dialogových oken OneDrive nebo SharePoint Online pro sdílení. Pozvaní uživatelé OneDrivu nebo SharePointu Online se také zobrazí ve službě Azure AD po uplatnění svých pozvání.

- Licenční požadavky se liší. Další informace o licencování najdete v tématu věnovaném [licencování externích identit Azure AD](./external-identities-pricing.md) a [k externímu sdílení SharePointu Online](/sharepoint/external-sharing-overview).
Pokud chcete spravovat externí sdílení ve OneDrivu nebo SharePointu Online pomocí spolupráce B2B Azure AD, nastavte nastavení externího sdílení OneDrive/SharePoint Online tak, aby **bylo možné sdílet jenom s externími uživateli, kteří už existují v adresáři vaší organizace**. Uživatelé můžou přejít na externě sdílené weby a vybrat externí spolupracovníky, které správce přidal. Správce může přidat externí spolupracovníky prostřednictvím rozhraní API pozvánky pro spolupráci B2B.


![Nastavení externího sdílení OneDrivu nebo SharePointu](media/o365-external-user/odsp-sharing-setting.png)

Po povolení externího sdílení je možnost hledání stávajících uživatelů typu Host ve výběru osob v SharePointu Online (SPO) ve výchozím nastavení VYPNUTá, aby odpovídala staršímu chování.

Tuto funkci můžete povolit pomocí nastavení ' ShowPeoplePickerSuggestionsForGuestUsers ' na úrovni tenanta a kolekce webů. Funkci můžete nastavit pomocí rutin Set-SPOTenant a Set-SPOSite, které umožní členům Hledat v adresáři všechny existující uživatele typu Host. Změny v oboru tenanta neovlivňují již zřízené SPO lokality.

## <a name="next-steps"></a>Další kroky

* [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
* [Přidání uživatele spolupráce B2B do role](add-guest-to-role.md)
* [Delegovat pozvánky pro spolupráci B2B](delegate-invitations.md)
* [Dynamické skupiny a spolupráce B2B](use-dynamic-groups.md)
* [Řešení potíží s Azure Active Directory spolupráce B2B](troubleshoot.md)
