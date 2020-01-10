---
title: Jak nakonfigurovat zřizování uživatelů pro aplikaci Galerie Azure AD
description: Jak rychle nakonfigurovat zřizování a rušení uživatelských účtů v aplikacích, které jsou už uvedené v galerii aplikací Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: 8aa8265733128ed3b2ad5ddf16dcebb122b0f054
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443425"
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>Jak nakonfigurovat zřizování uživatelů pro aplikaci Galerie Azure AD

*Zřizování uživatelských účtů* je proces vytváření, aktualizace nebo zakázání záznamů uživatelských účtů v úložišti profilů místního uživatele aplikace. Většina cloudových a SaaS aplikací ukládá role uživatelů a oprávnění do vlastního místního úložiště profilů uživatele a k získání jednotného přihlašování se *vyžaduje* přítomnost takového záznamu uživatele v místním úložišti uživatele a přístup k práci.

V Azure Portal karta **zřizování** v levém navigačním podokně pro podnikovou aplikaci zobrazuje, jaké režimy zřizování jsou pro tuto aplikaci podporovány. Může to být jedna ze dvou hodnot:

## <a name="configuring-an-application-for-manual-provisioning"></a>Konfigurace aplikace pro ruční zřizování

*Ruční* zřizování znamená, že uživatelské účty se musí vytvořit ručně pomocí metod poskytovaných aplikací. To může znamenat přihlášení k portálu pro správu pro danou aplikaci a přidání uživatelů pomocí webového uživatelského rozhraní. Můžete také nahrát tabulku s podrobnostmi o uživatelském účtu pomocí mechanismu poskytovaného touto aplikací. Projděte si dokumentaci poskytovanou aplikací nebo se obraťte na vývojáře aplikace a zjistěte, jaké mechanismy jsou k dispozici.

Pokud *je* jediným režimem zobrazeným pro danou aplikaci, znamená to, že pro tuto aplikaci ještě není k dispozici žádný automatický konektor pro zřizování Azure AD. Nebo to znamená, že aplikace nepodporuje požadavky rozhraní API pro správu uživatelů od Microsoftu, které se používají k sestavení konektoru pro automatizované zřizování.

Pokud chcete požádat o podporu automatického zřizování pro danou aplikaci, můžete žádost vyplnit pomocí [Azure Active Directory požadavků aplikace](https://aka.ms/aadapprequest).

## <a name="configuring-an-application-for-automatic-provisioning"></a>Konfigurace aplikace pro Automatické zřizování

*Automaticky* znamená, že se pro tuto aplikaci vyvinul konektor zřizování služby Azure AD. Další informace o službě zřizování Azure AD a o tom, jak funguje, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning).

Další informace o tom, jak zřídit konkrétní uživatele a skupiny pro aplikaci, najdete v tématu [Správa zřizování uživatelských účtů pro podnikové aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning).

Skutečný postup potřebný k povolení a konfiguraci automatického zřizování se liší v závislosti na aplikaci.

> [!NOTE]
> Měli byste začít tak, že si vyhledáte kurz pro instalaci, který je specifický pro nastavení vaší aplikace, a podle těchto kroků nakonfigurovat aplikaci a službu Azure AD tak, aby vytvořila zřizovací připojení. 

Kurzy aplikací najdete v tématu [Seznam kurzů, jak integrovat aplikace SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

Při nastavování zřizování je důležité vzít v úvahu, že je potřeba zkontrolovat a nakonfigurovat mapování atributů a pracovní postupy, které definují, který uživatel (nebo skupinu) z Azure AD do aplikace přesměruje. To zahrnuje nastavení "odpovídajících vlastností", která se používá k jednoznačné identifikaci uživatelů nebo skupin mezi oběma systémy a jejich porovnání. Další informace o mapování atributů najdete v tomto odkazu v části *Další kroky* .

## <a name="next-steps"></a>Další kroky
[Přizpůsobení mapování atributů zřizování uživatelů pro SaaS aplikace v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

