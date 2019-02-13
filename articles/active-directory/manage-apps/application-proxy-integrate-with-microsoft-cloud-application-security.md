---
title: Integrace místních aplikací pomocí Cloud App Security – Azure Active Directory | Dokumentace Microsoftu
description: Konfigurace místní aplikace v Azure Active Directory pro práci s Cloud App Security (MCAS). Použít MCAS Conditional Access App Control k monitorování a řízení relace v reálném čase podle zásady podmíněného přístupu. Tyto zásady můžete použít k místním aplikacím, které používají Proxy aplikací v Azure Active Directory (Azure AD).
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: celested
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21c197dace3088ceb89270b7e3e8a1b91e9c8ebc
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56176556"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>Konfigurace aplikací v reálném čase přístup k monitorování pomocí Microsoft Cloud App Security a Azure Active Directory
Nakonfigurujte místní aplikace v Azure Active Directory (Azure AD) pro použití Microsoft Cloud App Security (MCAS) pro monitorování v reálném čase. MCAS používá Conditional Access App Control k monitorování a řízení relace v reálném čase podle zásady podmíněného přístupu. Tyto zásady můžete použít k místním aplikacím, které používají Proxy aplikací v Azure Active Directory (Azure AD).

Tady je několik příkladů typů zásad, které můžete vytvořit pomocí MCAS:

- Blokovat nebo chránit stahování citlivé dokumenty na nespravovaných zařízeních.
- Sledování uživatelů s vysokým rizikem přihlásit k aplikacím a protokolovat jejich akce z v rámci relace. Pomocí těchto informací můžete analyzovat chování uživatelů a zjistěte, jak aplikovat zásady relace.
- Pomocí klientských certifikátů nebo dodržování předpisů zařízením zablokovat přístup pro konkrétní aplikace z nespravovaných zařízení.
- Omezte uživatelské relace z jiných podnikových sítích. Omezený přístup můžete rozdat uživatelům přístup k aplikaci z vnějšku podnikové sítě. Například tento omezený přístup můžete zablokovat uživatele stahování citlivé dokumenty.

Další informace najdete v tématu [ochrana aplikací pomocí Microsoft Cloud App Security Conditional Access App Control](/cloud-app-security/proxy-intro-aad).

## <a name="requirements"></a>Požadavky

Licence:

- Licence EMS E5, nebo 
- Azure Active Directory Premium P1 a samostatné MCAS.

Místní aplikace:

- Místní aplikace musí používat protokol Kerberos omezené delegování (KCD)

Konfigurace Proxy aplikací:

- Konfigurace služby Azure AD používat Proxy aplikací, včetně přípravy prostředí a instalaci konektoru Proxy aplikací. Podívejte se kurz [přidat místní aplikace pro vzdálený přístup prostřednictvím Proxy aplikace ve službě Azure AD](application-proxy-add-on-premises-application.md). 

## <a name="add-on-premises-application-to-azure-ad"></a>Přidat místní aplikace do služby Azure AD

Přidáte místní aplikace do služby Azure AD. Rychlý start, naleznete v tématu [přidat místní aplikace do služby Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). Při přidávání aplikace, nezapomeňte nastavit následující dvě nastavení **přidat místní aplikace** okno:

- **Předběžné ověření**: Zadejte **Azure Active Directory**.
- **Překládat adresy URL v těle žádosti**: Zvolte **Ano**.

Tato dvě nastavení jsou požadovány pro aplikace pro spolupráci s MCAS.

## <a name="test-the-on-premises-application"></a>Test místní aplikace

Po přidání aplikace do služby Azure AD, postupujte podle kroků v [testování aplikace](application-proxy-add-on-premises-application.md#test-the-application) pro přidání uživatele pro testování a testování přihlašování. 

## <a name="deploy-conditional-access-app-control"></a>Nasazení Conditional Access App Control

Nakonfigurovat svoji aplikaci pomocí podmíněného řízení přístupu aplikace, postupujte podle pokynů v [nasazení řízení aplikací podmíněného přístupu pro aplikace Azure AD](/cloud-app-security/proxy-deployment-aad).


## <a name="test-conditional-access-app-control"></a>Test Conditional Access App Control

Chcete-li otestovat nasazení aplikací Azure AD pomocí podmíněného řízení přístupu aplikace, postupujte podle pokynů v [testovací nasazení pro aplikace Azure AD](/cloud-app-security/proxy-deployment-aad).





