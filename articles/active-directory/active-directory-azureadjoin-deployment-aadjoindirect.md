---
title: Scénáře využití a aspekty nasazení pro připojení ke službě Azure AD | Microsoft Docs
description: Vysvětluje, jak mohou správci nastavit službu Azure AD Join pro své koncové uživatele (zaměstnanci, studenty, ostatní uživatelé). Popisuje také různé reálných scénářů pro použití připojení ke službě Azure AD.
services: active-directory
documentationcenter: ''
author: femila
manager: mtillman
editor: ''
tags: azure-classic-portal
ms.assetid: 81d4461e-21c8-4fdd-9076-0e4991979f62
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.openlocfilehash: 173ad6f07699ca6bfa534dedc053663bba571382
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2017
ms.locfileid: "26602428"
---
# <a name="usage-scenarios-and-deployment-considerations-for-azure-ad-join"></a>Scénáře využití a aspekty nasazení pro Azure AD Join
## <a name="usage-scenarios-for-azure-ad-join"></a>Scénáře použití pro připojení ke službě Azure AD
### <a name="scenario-1-businesses-largely-in-the-cloud"></a>Scénář 1: Podnikům do značné míry v cloudu
Azure Active Directory Join (Azure AD Join) vám může hodit Pokud aktuálně fungovat a Správa identit pro vaši firmu v cloudu nebo se brzy přechodu do cloudu. Můžete použít účet, který jste vytvořili ve službě Azure AD pro přihlášení k Windows 10. Prostřednictvím [během prvního spuštění prostředí procesu (FRX)](active-directory-azureadjoin-user-frx.md), nebo díky připojení ke službě Azure AD z [nabídky nastavení](active-directory-azureadjoin-user-upgrade.md), uživatelé se můžete zapojit své počítače ke službě Azure AD.  Uživatelé mohou také získejte (jeden přihlašování SSO) přístup k prostředkům cloudu třeba Office 365 ve svém prohlížeči zakázanou nebo v aplikacích Office.

### <a name="scenario-2-educational-institutions"></a>Scénář 2: Vzdělávací instituce
Vzdělávací instituce obvykle mají dva typy uživatelů: zaměstnance školy a studenti. Zaměstnance školy členové jsou považovány za dlouhodobější členy organizace. Vytváření místních účtů pro ně je žádoucí. Ale studenti, kteří jsou členy shorter-term organizace a je možné spravovat své účty ve službě Azure AD. To znamená, že můžete directory škálování vloží do cloudu namísto uloženého místně. Taky to znamená, že studenti, kteří budou moci přihlásit do systému Windows pomocí svých účtů Azure AD a získat přístup k prostředkům v Office 365 v aplikacích Office.

### <a name="scenario-3-retail-businesses"></a>Scénář 3: Prodejní firmách
Prodejní firmy mají sezónních pracovníků a dlouhodobé zaměstnanci. Obvykle vytvoříte místní účty a připojený k doméně počítače použít pro dlouhodobější zaměstnance na plný úvazek. Ale sezónních pracovníků shorter-term členy organizace, a je třeba spravovat své účty, kde uživatelské licence můžete snadno přesouvat. Když vytvoříte své uživatelské účty v cloudu s licencemi Office 365, těmto uživatelům získat výhody přihlášení k Windows a Office aplikace pomocí účtu Azure AD, při zachování větší flexibilitu s jejich licencí po opustí.

### <a name="scenario-4-additional-scenarios"></a>Scénář 4: Další scénáře
Společně s výhody už jsme probírali výše, můžete využívat museli uživatelé kvůli zjednodušená spojující prostředí, efektivní správu zařízení, registrace správu automatické mobilních zařízení a jednotné přihlašování do služby Azure AD připojit svá zařízení do služby Azure AD a místní prostředky.  

## <a name="deployment-considerations-for-azure-ad-join"></a>Informace o nasazení pro Azure AD Join
### <a name="enable-your-users-to-join-a-company-owned-device-directly-to-azure-ad"></a>Povolit uživatelům připojení k zařízení ve vlastnictví společnosti přímo do služby Azure AD
Podniky můžou poskytnout jen cloudové účty partnerských společností a organizace. Těchto partnerů můžete pak snadný přístup k firemním aplikacím a prostředkům pomocí jednotného přihlašování. Tento scénář se vztahuje na uživatele, kteří přístup k prostředkům především v cloudu, jako je například Office 365 nebo SaaS aplikací, které jsou závislé na službě Azure AD pro ověřování.

### <a name="prerequisites"></a>Požadavky
**Na úrovni organizace (správce)**

* Předplatné Azure s Azure Active Directory  

**Na úrovni uživatele**

* Windows 10 (edice Professional a Enterprise)

### <a name="administrator-tasks"></a>Úlohy správce
* [Nastavení registrace zařízení](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Úlohy uživatele
* [Nastavit nové zařízení Windows 10 s Azure AD během instalace](active-directory-azureadjoin-user-frx.md)
* [Nastavení Windows 10 zařízení s Azure AD v nabídce nastavení](active-directory-azureadjoin-user-upgrade.md)
* [Připojení osobního zařízení Windows 10 pro vaši organizaci](active-directory-azureadjoin-personal-device.md)

## <a name="enable-byod-in-your-organization-for-windows-10"></a>Povolit model BYOD ve vaší organizaci pro Windows 10
Můžete nastavit uživatele a zaměstnanci používat svá osobní zařízení se systémem Windows (BYOD) přístup k podnikovým aplikacím a prostředkům. Vaši uživatelé můžete přidat osobní zařízení se systémem Windows pro přístup k prostředkům způsobem zabezpečení a dodržování své účty Azure AD (pracovní nebo školní účty).

### <a name="prerequisites"></a>Požadavky
**Na úrovni organizace (správce)**

* Předplatné Azure AD

**Na úrovni uživatele**

* Windows 10 (edice Professional a Enterprise)

### <a name="administrator-tasks"></a>Úlohy správce
* [Nastavení registrace zařízení](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Úlohy uživatele
* [Připojení osobního zařízení Windows 10 pro vaši organizaci](active-directory-azureadjoin-personal-device.md)

## <a name="additional-information"></a>Další informace
* [Windows 10 pro firmy: Možnosti, jak používat zařízení pro práci](active-directory-azureadjoin-windows10-devices-overview.md)
* [Rozšíření možností cloudu u zařízení s Windows 10 prostřednictvím služby Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Ověřování identit bez hesel pomocí Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Další informace o scénářích použití pro službu Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Připojení zařízení k doméně služby Azure AD ve Windows 10 – ukázky z praxe](active-directory-azureadjoin-devices-group-policy.md)
* [Nastavení služby Azure AD Join](active-directory-azureadjoin-setup.md)

