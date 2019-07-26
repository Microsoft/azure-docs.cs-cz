---
title: Zjištěné chyby zabezpečení Azure Active Directory Identity Protection
description: Přehled chyb zabezpečení zjištěných aplikací Azure Active Directory Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 446e2ef33b714afe06f24f3dbfc46bc6a42790b8
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335155"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Zjištěné chyby zabezpečení Azure Active Directory Identity Protection

Chyby zabezpečení jsou slabiny v prostředí, které může útočník zneužít. Doporučujeme správcům vyřešit tato ohrožení zabezpečení, aby vylepšili stav zabezpečení jejich organizace.

![Ohrožení zabezpečení hlášené službou Identity Protection](./media/vulnerabilities/identity-protection-vulnerabilities.png)

Následující části vám poskytnou přehled o chybách zabezpečení hlášených ochranou identity.

## <a name="multi-factor-authentication-registration-not-configured"></a>Registrace Multi-Factor Authentication není nakonfigurovaná.

Tato chyba zabezpečení pomáhá zhodnotit nasazení služby Azure Multi-Factor Authentication ve vaší organizaci.

Azure Multi-Factor Authentication poskytuje druhou úroveň zabezpečení pro ověřování uživatelů. Pomáhá chránit přístup k datům a aplikacím a současně splňuje požadavky uživatelů na jednoduchý proces přihlašování. Azure Multi-Factor Authentication umožňuje snadno používat možnosti ověřování, jako je:

* Telefonní hovor
* Zpráva SMS
* Oznámení mobilní aplikace
* Ověřovací kód jednorázového hesla

Doporučujeme, abyste pro přihlášení uživatelů vyžadovali službu Azure Multi-Factor Authentication. Multi-Factor Authentication hraje klíčovou roli v zásadách podmíněného přístupu na základě rizik, které jsou dostupné prostřednictvím Identity Protection.

Další informace najdete v tématu [Co je Azure Multi-Factor Authentication?](../authentication/multi-factor-authentication.md)

## <a name="unmanaged-cloud-apps"></a>Nespravované cloudové aplikace

Tato chyba zabezpečení pomáhá identifikovat nespravované cloudové aplikace ve vaší organizaci.

Zaměstnanci IT často neznají všechny cloudové aplikace ve své organizaci. Můžete snadno zjistit, proč by správci měli obavy o neoprávněný přístup k firemním datům, může dojít k úniku dat a dalším bezpečnostním rizikům.

Doporučujeme nasazovat Cloud Discovery pro zjišťování nespravovaných cloudových aplikací a ke správě těchto aplikací pomocí Azure Active Directory.

Další informace najdete v tématu [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="security-alerts-from-privileged-identity-management"></a>Výstrahy zabezpečení z Privileged Identity Managementu

Tato chyba zabezpečení pomáhá zjišťovat a řešit výstrahy týkající se privilegovaných identit ve vaší organizaci.  

Aby mohli uživatelé provádět privilegované operace, organizace potřebují udělit uživatelům dočasný nebo trvalý privilegovaný přístup v prostředcích Azure AD, Azure nebo Office 365 nebo v jiných aplikacích SaaS. Každý z těchto privilegovaných uživatelů zvyšuje plochu útoku vaší organizace. Tato chyba zabezpečení pomáhá identifikovat uživatele s zbytečným privilegovaným přístupem a přijmout vhodná opatření ke snížení nebo odstranění rizika, které představují.

Doporučujeme organizacím používat Azure AD Privileged Identity Management ke správě, řízení a monitorování privilegovaných identit v Azure AD a dalších Microsoft online služby jako je třeba sada Office 365 nebo Microsoft Intune.

Další informace najdete v tématu [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md).

## <a name="see-also"></a>Viz také:

[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
