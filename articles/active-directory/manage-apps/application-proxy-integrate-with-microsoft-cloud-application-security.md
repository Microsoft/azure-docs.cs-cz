---
title: Integrace místních aplikací s Cloud App Security – Azure AD
description: Nakonfigurujte místní aplikaci v Azure Active Directory tak, aby fungovala s Microsoft Cloud App Security (MCAS). Pomocí Řízení podmíněného přístupu k aplikacím MCAS můžete monitorovat a řídit relace v reálném čase na základě zásad podmíněného přístupu. Tyto zásady můžete použít u místních aplikací, které používají proxy aplikace v Azure Active Directory (Azure AD).
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 12/19/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 889168782acabaafa4200a8ebd097e431a68ede3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84764566"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>Konfigurace monitorování přístupu aplikace v reálném čase pomocí Microsoft Cloud App Security a Azure Active Directory
Nakonfigurujte místní aplikaci v Azure Active Directory (Azure AD), aby používala Microsoft Cloud App Security (MCAS) pro sledování v reálném čase. MCAS používá Řízení podmíněného přístupu k aplikacím k monitorování a řízení relací v reálném čase na základě zásad podmíněného přístupu. Tyto zásady můžete použít u místních aplikací, které používají proxy aplikace v Azure Active Directory (Azure AD).

Tady je několik příkladů typů zásad, které můžete vytvořit pomocí MCAS:

- Blokovat nebo chránit stahování citlivých dokumentů na nespravovaných zařízeních.
- Monitorujte, kdy se vysoce rizikové uživatelé přihlašují k aplikacím a následně protokolují své akce v rámci relace. Pomocí těchto informací můžete analyzovat chování uživatelů a určit, jak se mají zásady relace použít.
- Použijte klientské certifikáty nebo dodržování předpisů zařízením k zablokování přístupu ke konkrétním aplikacím z nespravovaných zařízení.
- Omezení uživatelských relací z jiných než firemních sítí. Uživatelům, kteří přistupují k aplikaci, můžete udělit omezený přístup mimo vaši podnikovou síť. Tento omezený přístup může například uživateli blokovat stahování citlivých dokumentů.

Další informace najdete v tématu [Ochrana aplikací pomocí Microsoft Cloud App Security řízení podmíněného přístupu k aplikacím](/cloud-app-security/proxy-intro-aad).

## <a name="requirements"></a>Požadavky

Průkaz

- Licence EMS E5 nebo 
- Azure Active Directory Premium P1 a MCAS samostatně.

Místní aplikace:

- Místní aplikace musí používat omezené delegování protokolu Kerberos (KCD).

Konfigurovat proxy aplikace:

- Nakonfigurujte službu Azure AD tak, aby používala proxy aplikace, včetně přípravy prostředí a instalace konektoru proxy aplikací. Kurz najdete v tématu [Přidání místních aplikací pro vzdálený přístup prostřednictvím proxy aplikací v Azure AD](application-proxy-add-on-premises-application.md). 

## <a name="add-on-premises-application-to-azure-ad"></a>Přidání místní aplikace do Azure AD

Přidejte místní aplikaci do Azure AD. Informace o rychlém startu najdete v tématu [Přidání místní aplikace do služby Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). Při přidávání aplikace nezapomeňte v okně **Přidat místní aplikaci** nastavit následující dvě nastavení:

- **Předběžné ověření**: zadejte **Azure Active Directory**.
- **Přeložit adresy URL v těle aplikace**: vyberte **Ano**.

Tato dvě nastavení jsou nutná, aby aplikace fungovala s MCAS.

## <a name="test-the-on-premises-application"></a>Testování místní aplikace

Po přidání aplikace do služby Azure AD použijte kroky v části [test aplikace](application-proxy-add-on-premises-application.md#test-the-application) a přidejte uživatele k testování a otestujte přihlášení. 

## <a name="deploy-conditional-access-app-control"></a>Nasazení Řízení podmíněného přístupu k aplikacím

Chcete-li nakonfigurovat aplikaci pomocí řízení aplikací podmíněného přístupu, postupujte podle pokynů v části [nasazení řízení aplikací podmíněného přístupu pro aplikace Azure AD](/cloud-app-security/proxy-deployment-aad).


## <a name="test-conditional-access-app-control"></a>Řízení podmíněného přístupu k aplikacím testu

Pokud chcete otestovat nasazení aplikací Azure AD pomocí řízení aplikací podmíněného přístupu, postupujte podle pokynů v části [testování nasazení pro aplikace Azure AD](/cloud-app-security/proxy-deployment-aad).





