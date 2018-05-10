---
title: Externí sdílení Office 365 a Azure Active Directory s B2B spolupráci | Microsoft Docs
description: Popisuje, sdílení prostředků s externími partnery pomocí spolupráce O365 a Azure Active Directory s B2B.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/24/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 4421e6ca2bdce721c360f2701b3693024a694eb6
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Externí sdílení Office 365 a spolupráce Azure Active Directory s B2B

Externí sdílení v Office 365 (OneDrive, SharePoint Online, Unified skupin atd.) a spolupráce B2B Azure Active Directory (Azure AD) jsou technicky samé. Všechny externí sdílení (kromě OneDrive nebo SharePoint Online), včetně hostů v skupiny Office 365, už používá pozvánku spolupráce Azure AD B2B rozhraní API pro sdílení.

OneDrive nebo SharePoint Online má správce samostatné pozvánku. Podpora pro externí sdílení v OneDrive nebo SharePoint Online spuštěna před jeho podporu vyvinul Azure AD. V čase OneDrive nebo SharePoint Online externí sdílení rozlišeny několik funkcí a mnoha milionů uživatelů, kteří používají produktu je v vytvořená sdílení vzor. Existují však určité jemně rozdíly mezi OneDrive nebo SharePoint Online externí sdílení jak funguje a jak funguje spolupráce Azure AD B2B:

- OneDrive nebo SharePoint Online přidá uživatele do adresáře po uživatelé mají uplatněn svoje pozvánky. Ano před uplatnění, nevidíte uživatele na portálu Azure AD. Pokud jiný web vyzývá uživatel do té doby, vygeneruje se nová pozvánka. Však při použití spolupráce Azure AD B2B, uživatelé jsou okamžitě přidány na pozvánku, aby zobrazovala až everywhere.

- Uplatnění dojde v OneDrive nebo SharePoint Online vypadá jinak než dojde v spolupráce Azure AD B2B. Jakmile uživatel uplatňuje pozvánku, vypadat podobně zkušenosti.

- Azure AD s B2B spolupráce pozvánku, uživatelé mohou být zachyceny z OneDrive nebo SharePoint Online sdílení dialogová okna. OneDrive nebo SharePoint Online pozvat uživatele také zobrazí v Azure AD poté, co se uplatnit své pozvánky.

- Chcete-li spravovat externí sdílení v OneDrive nebo SharePoint Online pomocí spolupráce Azure AD B2B, nastavte OneDrive nebo SharePoint Online externí sdílení nastavení **pouze povolit sdílení s externími uživateli již v adresáři**. Uživatelé přejít na externě sdílené weby a vyberte z externími spolupracovníky přidané správce. Správce můžete přidat externí spolupracovníky prostřednictvím pozvánku spolupráce B2B rozhraní API.

![OneDrive nebo SharePoint Online externí sdílení nastavení](media/active-directory-b2b-o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>Další postup

* [Co je spolupráce B2B ve službě Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Přidání uživatele spolupráce B2B k roli](active-directory-b2b-add-guest-to-role.md)
* [Delegovat pozvánek spolupráce B2B](active-directory-b2b-delegate-invitations.md)
* [Dynamické skupiny a spolupráci B2B](active-directory-b2b-dynamic-groups.md)