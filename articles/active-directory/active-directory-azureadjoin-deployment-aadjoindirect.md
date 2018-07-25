---
title: Scénáře použití a aspekty nasazení pro připojení ke službě Azure AD | Dokumentace Microsoftu
description: Vysvětluje, jak správci můžou nastavit připojení k Azure AD pro své koncové uživatele (zaměstnanci, studenty, další uživatelé). Také popisuje různé reálné scénáře pro pomocí Azure AD Join.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
tags: azure-classic-portal
ms.component: devices
ms.assetid: 81d4461e-21c8-4fdd-9076-0e4991979f62
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: markvi
ms.openlocfilehash: 29480390e6854dcedeaf8f06c078ed2e4ed2b94d
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223003"
---
# <a name="usage-scenarios-and-deployment-considerations-for-azure-ad-join"></a>Scénáře použití a aspekty nasazení pro službu Azure AD Join
## <a name="usage-scenarios-for-azure-ad-join"></a>Scénáře použití pro službu Azure AD Join
### <a name="scenario-1-businesses-largely-in-the-cloud"></a>Scénář 1: Firmy do značné míry v cloudu
Azure Active Directory Join (Azure AD Join) vám může hodit aktuálně provoz a Správa identit pro vaše podnikání v cloudu nebo se brzy přesouvají do cloudu. Můžete použít účet, který jste vytvořili ve službě Azure AD pro přihlášení k Windows 10. Prostřednictvím [prostředí prvního spuštění procesu (FRX)](active-directory-azureadjoin-user-frx.md), nebo díky připojení ke službě Azure AD z [nabídky nastavení](active-directory-azureadjoin-user-upgrade.md), uživatelé můžou své počítače připojit k Azure AD.  Vaši uživatelé budou moct využívat také (jednotné přihlašování SSO) přístup ke cloudovým prostředkům, jako je Office 365, ve svém prohlížeči nebo v aplikacích Office.

### <a name="scenario-2-educational-institutions"></a>Scénář 2: Vzdělávací instituce
Vzdělávací instituce obvykle mají dva typy uživatele: vyučujícím a studentům. Členové fakulty, kteří jsou považovány za dlouhodobější členy vaší organizace. Vytváření místních účtů pro ně je žádoucí. Ale studentů budou shorter-term členy vaší organizace a lze je spravovat své účty ve službě Azure AD. To znamená, že adresář škálování může doručit bez vyžádání do cloudu namísto uložené místně. Také znamená, že studenti budou moci přihlásit do Windows pomocí jejich účtů služby Azure AD a získat přístup k prostředkům Office 365 v aplikacích Office.

### <a name="scenario-3-retail-businesses"></a>Scénář 3: Firmám maloobchodního prodeje
Maloobchodní firem sezónním zaměstnancům a dlouhodobé zaměstnanci. Obvykle vytvoříte místních účtů a používat počítače připojené k doméně pro dlouhodobější zaměstnance na plný úvazek. Ale shorter-term členy vaší organizace se sezónním zaměstnancům, a je třeba spravovat své účty, kde uživatelské licence může být snadněji přesouvat. Při vytváření své uživatelské účty v cloudu s využitím licence Office 365, tito uživatelé získali výhody přihlášení k Windows a Office aplikací pomocí účtu služby Azure AD, zatímco udržovat větší flexibilitu s jejich licencí po opuštění.

### <a name="scenario-4-additional-scenarios"></a>Scénář 4: Další scénáře
I o výhodách jsme probírali výše, můžete těžit z mají vaši uživatelé připojit svoje zařízení do služby Azure AD z důvodu zjednodušené prostředí spojovacího, Správa efektivní zařízení, registrace správu automatické mobilních zařízení a jednotné přihlašování do služby Azure AD a místní prostředky.  

## <a name="deployment-considerations-for-azure-ad-join"></a>Důležité informace o nasazení pro službu Azure AD Join
### <a name="enable-your-users-to-join-a-company-owned-device-directly-to-azure-ad"></a>Umožní vašim uživatelům připojovat zařízení vlastněných společností přímo do Azure AD
Podniky můžete zadat účty jenom cloudu do partnerské firmy a organizace. Tito partneři můžou potom snadný přístup k podnikovým aplikacím a prostředkům s jednotným přihlašováním. Tento scénář se vztahuje na uživatele, kteří přístup k prostředkům především v cloudu, např. Office 365 nebo platformám SaaS aplikací, které jsou závislé na službě Azure AD pro ověřování.

### <a name="prerequisites"></a>Požadavky
**Na úrovni rozlehlé sítě (správce)**

* Předplatné Azure s Azure Active Directory  

**Na úrovni uživatele**

* Windows 10 (verze Professional a Enterprise)

### <a name="administrator-tasks"></a>Úlohy správce
* [Nastavení registrace zařízení](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Uživatelské úkoly
* [Nastavení nového zařízení s Windows 10 s využitím Azure AD během instalace](active-directory-azureadjoin-user-frx.md)
* [Nastavení zařízení s Windows 10 s využitím Azure AD z nabídky nastavení](active-directory-azureadjoin-user-upgrade.md)
* [Připojení osobního zařízení Windows 10 pro vaši organizaci](active-directory-azureadjoin-personal-device.md)

## <a name="enable-byod-in-your-organization-for-windows-10"></a>Přineste si vlastní ve vaší organizaci pro Windows 10
Můžete nastavit uživatele a zaměstnanci používat jejich osobní Windows zařízení (BYOD) přístup k podnikovým aplikacím a prostředkům. Uživatelé mohou přidat své účty služby Azure AD (pracovní nebo školní účty) do osobního zařízení Windows pro přístup k prostředkům v podobě zabezpečení a dodržování.

### <a name="prerequisites"></a>Požadavky
**Na úrovni rozlehlé sítě (správce)**

* Předplatné Azure AD

**Na úrovni uživatele**

* Windows 10 (verze Professional a Enterprise)

### <a name="administrator-tasks"></a>Úlohy správce
* [Nastavení registrace zařízení](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Uživatelské úkoly
* [Připojení osobního zařízení Windows 10 pro vaši organizaci](active-directory-azureadjoin-personal-device.md)

## <a name="additional-information"></a>Další informace
* [Windows 10 pro firmy: Možnosti, jak používat zařízení pro práci](active-directory-azureadjoin-windows10-devices-overview.md)
* [Rozšíření možností cloudu u zařízení s Windows 10 prostřednictvím služby Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Ověřování identit bez hesel pomocí Microsoft Passportu](active-directory-azureadjoin-passport.md)
* [Další informace o scénářích použití pro službu Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Připojení zařízení k doméně služby Azure AD ve Windows 10 – ukázky z praxe](active-directory-azureadjoin-devices-group-policy.md)
* [Nastavení služby Azure AD Join](active-directory-azureadjoin-setup.md)

