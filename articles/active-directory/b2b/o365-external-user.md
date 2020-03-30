---
title: Externí sdílení Office 365 a spolupráce B2B – Azure AD
description: Popisuje sdílení prostředků s externími partnery pomocí Spolupráce O365 a Azure Active Directory B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/24/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d70aed6fbe0f09ea6284f913c88186ecf94e297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272274"
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Externí sdílení Office 365 a spolupráce Azure Active Directory B2B

Externí sdílení v Office 365 (OneDrive, SharePoint Online, Sjednocené skupiny atd.) a Azure Active Directory (Azure AD) B2B spolupráce jsou technicky totéž. Všechna externí sdílení (kromě OneDrivu/SharePointu Online), včetně hostů ve skupinách Office 365, už ke sdílení používá pozvánka ke spolupráci Azure AD B2B.

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Jak se Azure AD B2B liší od externího sdílení v SharePointu Online?

OneDrive/SharePoint Online má samostatného správce pozvánek. Podpora externího sdílení na OneDrivu/SharePointu Online začala ještě předtím, než Azure AD vyvinulo svou podporu. V průběhu času se externímu sdílení OneDrivu/SharePointu Online nahromadilo několik funkcí a mnoho milionů uživatelů, kteří používají vestavěný vzor sdílení produktu. Existují však některé drobné rozdíly mezi fungováním externího sdílení OneDrivu/SharePointu Online a fungování mandaty Azure AD B2B. Další informace o externím sdílení OneDrivu/SharePointu Online najdete v [přehledu externího sdílení](https://docs.microsoft.com/sharepoint/external-sharing-overview). Proces se obecně liší od Azure AD B2B těmito způsoby:

- OneDrive/SharePoint Online přidá uživatele do adresáře poté, co uživatelé uplatní pozvánky. Takže před uplatněním se uživateli na portálu Azure AD nezobrazí. Pokud jiný web mezitím zve uživatele, vygeneruje se nová pozvánka. Ale při použití spolupráce Azure AD B2B, uživatelé jsou přidány okamžitě na pozvání tak, aby se zobrazí všude.

- Možnosti využití na OneDrivu/SharePointu Online vypadají jinak než ve spolupráci s Azure AD B2B. Když uživatel pozvánku uplatní, budou prostředí vypadat stejně.

- Pozvaní uživatelé Azure AD B2B pro spolupráci můžou být vybráni z dialogových oken sdílení onedrivu nebo SharePointu Online. Pozvaní uživatelé OneDrivu/SharePointu Online se po uplatnění pozvánky taky zobrazili ve službě Azure AD.

- Licenční požadavky se liší. U každé placené licence Azure AD můžete až 5 uživatelům typu Host povolit přístup k placeným funkcím Azure AD. Další informace o licencování najdete v [tématu Licencování Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/licensing-guidance) a ["Co je externí uživatel?" v přehledu externího sdílení SharePointu Online](https://docs.microsoft.com/sharepoint/external-sharing-overview#what-happens-when-users-share).

Pokud chcete spravovat externí sdílení na OneDrivu/SharePointu Online pomocí spolupráce Azure AD B2B, nastavte nastavení externího sdílení OneDrivu/SharePointu Online na **Povolit sdílení jenom s externími uživateli, kteří už v adresáři vaší organizace existují**. Uživatelé mohou přejít na externě sdílené weby a vybírat si od externích spolupracovníků, které správce přidal. Správce může přidat externí spolupracovníky prostřednictvím rozhraní API pozvání ke spolupráci B2B.


![Nastavení externího sdílení OneDrivu/SharePointu Online](media/o365-external-user/odsp-sharing-setting.png)

Po povolení externího sdílení je možnost vyhledávat existující uživatele typu Host v výběru lidí sharepointu Online (SPO) ve výchozím nastavení vypnutá tak, aby odpovídala staršímu chování.

Tuto funkci můžete povolit pomocí nastavení ShowPeoplePickerSuggestionsForGuestUsers na úrovni klienta a kolekce webů. Funkci můžete nastavit pomocí rutin Set-SPOTenant a Set-SPOSite, které umožňují členům prohledávat všechny existující uživatele typu Host v adresáři. Změny v oboru klienta nemají vliv na již zřízené weby SPO.

## <a name="next-steps"></a>Další kroky

* [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
* [Přidání uživatele spolupráce B2B do role](add-guest-to-role.md)
* [Pozvánky na spolupráci b2B delegáta](delegate-invitations.md)
* [Dynamické skupiny a b2B spolupráce](use-dynamic-groups.md)
* [Poradce při potížích se spoluprací služby Azure Active Directory B2B](troubleshoot.md)
