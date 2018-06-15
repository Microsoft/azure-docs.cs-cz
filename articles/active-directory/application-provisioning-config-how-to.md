---
title: Postup konfigurace zřizování uživatelů k aplikaci Galerie Azure AD | Microsoft Docs
description: Jak můžete snadno konfigurovat bohaté uživatelský účet zajišťování a rušení zajištění aplikací již uveden v galerii aplikací Azure AD
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: da7050edae282c90c4f92c588ce8fe501ccba50d
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2018
ms.locfileid: "29464421"
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>Postup konfigurace zřizování uživatelů k aplikaci Galerie Azure AD

*Zřizování účtu uživatele* je úkon, vytváření, aktualizaci nebo zakázání záznamů účet uživatele v úložišti profil místního uživatele aplikace. Většina cloudu a aplikace SaaS úložiště rolí uživatelů a oprávnění v profilu úložiště vlastní místní uživatele a přítomnost takové uživatelský záznam v jejich místní úložiště je *požadované* pro jednotné přihlašování a přístup k práci.

Na portálu Azure **zřizování** klikněte v levém navigačním podokně pro podnikové aplikace zobrazuje, jaké zřizování režimy jsou podporovány pro tuto aplikaci. To může být jednu ze dvou hodnot:

## <a name="configuring-an-application-for-manual-provisioning"></a>Konfigurace aplikace pro ruční zřizování

*Ruční* zřizování znamená, že uživatelské účty musí být vytvořen ručně pomocí metody poskytované tuto aplikaci. To může znamenat přihlašování portálu pro správu pro tuto aplikaci a přidání uživatelů pomocí webového uživatelského rozhraní. Nebo může být odesílání tabulku s podrobnostmi účet uživatele, používá mechanismus poskytované tuto aplikaci. Vyhledejte v dokumentaci aplikace, nebo se obraťte na vývojáři aplikace k určení, že wat mechanismy jsou dostupné.

Pokud ručně režimu jen pro zobrazí pro danou aplikaci, znamená to, že žádné automatické Azure AD zřizování konektor zatím nebyla vytvořena pro aplikaci. Nebo znamená to, že aplikace nepodporuje rozhraní API správy požadovaného uživatele, na kterém k vytvoření konektoru služby Automatické zřizování.

Pokud chcete požádat o podporu pro automatické zřizování pro danou aplikaci, můžete vyplnit žádosti o pomocí [Azure Active Directory aplikace požadavky](https://aka.ms/aadapprequest).

## <a name="configuring-an-application-for-automatic-provisioning"></a>Konfigurace aplikace pro automatické zřizování

*Automatické* znamená, že byla vyvinuta Azure AD zřizování konektor pro tuto aplikaci. Další informace o Azure AD zřizování služby a jak to funguje, najdete v části [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning).

Další informace o tom, jak zřídit konkrétních uživatelů a skupin k aplikaci najdete v tématu [Správa zřizování účtu uživatele pro podnikové aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning).

Skutečné kroky nutné k povolení a konfigurace automatické zřizování se liší v závislosti na aplikaci.

>[!NOTE]
>Měli byste začít tak, že instalační program kurzu najdete konkrétní nastavení zřizování pro aplikace a následující ty kroky při konfiguraci aplikace a Azure AD k vytvoření zřizování připojení. 
>
>

Kurzy aplikace naleznete na adrese [seznamu kurzy o tom, jak integrovat SaaS aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

Důležité vzít v úvahu při nastavování zřizování být zkontrolujte a nakonfigurujte mapování atributů a pracovních postupů, které definují, které uživatele (nebo skupiny) vlastnosti toku z Azure AD k aplikaci. To zahrnuje nastavení "odpovídající vlastnost", které použít k jednoznačné identifikaci a odpovídají uživatele nebo skupiny mezi těmito dvěma systémy. Další informace o tomto důležité procesu.

## <a name="next-steps"></a>Další postup
[Přizpůsobení mapování atributů zřizování pro aplikace SaaS ve službě Azure Active Directory uživatelů](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

