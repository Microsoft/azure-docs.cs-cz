---
title: Integrace místních aplikací s cloudovým zabezpečením aplikací – Azure AD
description: Nakonfigurujte místní aplikaci ve službě Azure Active Directory tak, aby fungovala se zabezpečením aplikací Microsoft Cloud App Security (MCAS). Pomocí řízení aplikací podmíněného přístupu MCAS můžete monitorovat a řídit relace v reálném čase na základě zásad podmíněného přístupu. Tyto zásady můžete použít pro místní aplikace, které používají proxy aplikace ve službě Azure Active Directory (Azure AD).
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb97f9dd87277215a5d4708d3a6f49564c490204
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275492"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>Konfigurace monitorování přístupu k aplikacím v reálném čase pomocí Microsoft Cloud App Security a Azure Active Directory
Nakonfigurujte místní aplikaci ve službě Azure Active Directory (Azure AD) tak, aby používala Microsoft Cloud App Security (MCAS) pro monitorování v reálném čase. MCAS používá řízení aplikací podmíněného přístupu ke sledování a řízení relací v reálném čase na základě zásad podmíněného přístupu. Tyto zásady můžete použít pro místní aplikace, které používají proxy aplikace ve službě Azure Active Directory (Azure AD).

Zde je několik příkladů typů zásad, které můžete vytvořit pomocí funkce MCAS:

- Blokovat nebo chránit stahování citlivých dokumentů na nespravovaných zařízeních.
- Sledujte, kdy se vysoce rizikoví uživatelé přihlašují k aplikacím, a pak přihlaste své akce z relace. Pomocí těchto informací můžete analyzovat chování uživatelů a určit způsob použití zásad relace.
- Pomocí klientských certifikátů nebo dodržování předpisů zařízení zablokujte přístup k určitým aplikacím z nespravovaných zařízení.
- Omezte uživatelské relace z nepodnikových sítí. Uživatelům, kteří přistupují k aplikaci mimo podnikovou síť, můžete udělit omezený přístup. Tento omezený přístup může například uživateli zabránit ve stahování citlivých dokumentů.

Další informace najdete [v tématu Ochrana aplikací pomocí řízení aplikací podmíněného přístupu služby Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad).

## <a name="requirements"></a>Požadavky

Licence:

- licence EMS E5, nebo 
- Azure Active Directory Premium P1 a MCAS samostatné.

Místní aplikace:

- Místní aplikace musí používat delegování s omezeným i protokolem Kerberos (KCD).

Konfigurace proxy aplikace:

- Nakonfigurujte Azure AD tak, aby používala proxy aplikace, včetně přípravy prostředí a instalace konektoru proxy aplikace. Kurz najdete v [tématu Přidání místních aplikací pro vzdálený přístup prostřednictvím proxy aplikace ve službě Azure AD](application-proxy-add-on-premises-application.md). 

## <a name="add-on-premises-application-to-azure-ad"></a>Přidání místní aplikace do Azure AD

Přidejte místní aplikaci do Služby Azure AD. Úvodní příručka najdete [v tématu Přidání místní aplikace do Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). Při přidávání aplikace nezapomeňte nastavit následující dvě nastavení v okně **Přidat místní aplikaci:**

- **Předběžné ověření:** Zadejte **službu Azure Active Directory**.
- **Přeložit adresy URL v těle aplikace**: Zvolte **ano**.

Tato dvě nastavení jsou vyžadována pro aplikaci pracovat s MCAS.

## <a name="test-the-on-premises-application"></a>Testování místní aplikace

Po přidání aplikace do Azure AD, použijte kroky v [test aplikace](application-proxy-add-on-premises-application.md#test-the-application) přidat uživatele pro testování a otestujte přihlášení. 

## <a name="deploy-conditional-access-app-control"></a>Nasazení řízení aplikací podmíněného přístupu

Chcete-li aplikaci nakonfigurovat pomocí řízení aplikací podmíněného přístupu, postupujte podle pokynů v části [Nasazení řízení aplikací podmíněného přístupu pro aplikace Azure AD](/cloud-app-security/proxy-deployment-aad).


## <a name="test-conditional-access-app-control"></a>Testování řízení aplikací podmíněného přístupu

Chcete-li otestovat nasazení aplikací Azure AD s řízením aplikací podmíněného přístupu, postupujte podle pokynů v [části Testování nasazení pro aplikace Azure AD](/cloud-app-security/proxy-deployment-aad).





