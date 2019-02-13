---
title: Postup konfigurace zřizování uživatelů pro aplikaci Galerie Azure AD | Dokumentace Microsoftu
description: Jak můžete snadno konfigurovat bohaté uživatelský účet zřizování a jeho rušení pro aplikace již uveden v galerii aplikací Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 028c93e1d250606ccf1516e9393bf61140961831
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56174788"
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>Postup konfigurace zřizování uživatelů pro aplikaci Galerie Azure AD

*Zřizování uživatelských účtů* je proces vytváření, aktualizace nebo zakázání záznamů uživatelského účtu v aplikaci na místním úložišti profilu uživatele. Většina aplikací SaaS a cloudových ukládání rolí uživatelů a oprávnění v jejich vlastním místním úložišti profilu uživatele a přítomnost takový záznam uživatele ve svém místním úložišti je *požadované* pro jednotné přihlašování a přístupu k práci.

Na webu Azure Portal **zřizování** kartu v levém navigačním podokně pro podnikové aplikace zobrazí, co režim zřizování jsou podporovány pro tuto aplikaci. Může to být jedna ze dvou hodnot:

## <a name="configuring-an-application-for-manual-provisioning"></a>Konfigurace aplikace pro ruční zřizování

*Ruční* zřizování znamená, že uživatelské účty musí být vytvořeny ručně pomocí metody, které poskytuje tuto aplikaci. To může znamenat přihlášením na portál pro správu pro tuto aplikaci a přidání uživatelů pomocí webového uživatelského rozhraní. Nebo může být nahrání tabulky s podrobnou účet uživatele, použití mechanismu dostupném v příslušné aplikaci. Nahlédněte do dokumentace aplikace nebo sdělí vývojář aplikace k určení, že wat mechanismy jsou k dispozici.

Pokud ručně je jediný režim pro danou aplikaci, znamená to, že žádné automatické zřizování konektoru AD Azure zatím se nevytvořilo pro aplikaci. Nebo to znamená, že aplikace nepodporuje rozhraní API správy požadovaného uživatele, na kterém chcete vytvořit automatizované zřizování konektor.

Pokud chcete požádat o podporu pro automatické zřizování pro danou aplikaci, můžete zadat žádost o prostřednictvím [požadavků Azure Active Directory aplikací](https://aka.ms/aadapprequest).

## <a name="configuring-an-application-for-automatic-provisioning"></a>Konfigurace aplikace pro automatické zřizování

*Automatické* znamená, že pro tuto aplikaci jsme vyvinuli zřizování konektor Azure AD. Další informace o zřizování služby a jak funguje Azure AD najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning).

Další informace o tom, jak zřídit konkrétních uživatelů a skupin k aplikaci najdete v tématu [správě zřizování uživatelských účtů pro podnikové aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning).

Skutečné kroky nutné k povolení a konfigurace automatické zřizování se liší v závislosti na aplikaci.

>[!NOTE]
>Měli byste začít hledáním kurz nastavení specifické pro nastavení zřizování pro vaši aplikaci a následující tyto kroky konfigurace aplikace a služby Azure AD k vytvoření zřizování připojení. 
>
>

Kurzy aplikací lze nalézt v [seznam kurzů o integraci aplikací typu SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

Důležité vzít v úvahu při nastavování zřizování se ke kontrole a nakonfigurujte mapování atributů a pracovních postupů, které definují, které uživatel (nebo skupiny) vlastnosti toku ze služby Azure AD do aplikace. Jedná se o vlastnost "odpovídající", který použije k jednoznačné identifikaci a odpovídající uživatelům nebo skupinám mezi těmito dvěma systémy. Další informace o tomto procesu důležité.

## <a name="next-steps"></a>Další postup
[Přizpůsobení mapování atributů zřizování pro aplikace SaaS ve službě Azure Active Directory uživatelů](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

