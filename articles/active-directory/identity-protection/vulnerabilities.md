---
title: Chyb zabezpečení detekovaných službou Azure Active Directory Identity Protection
description: Přehled chyb zabezpečení detekovaných službou Azure Active Directory Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.date: 04/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: b481030c5d2d8e7d5e7061cdf256a202e08d6cbf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108790"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Chyb zabezpečení detekovaných službou Azure Active Directory Identity Protection

Ohrožení zabezpečení jsou slabá místa v prostředí, které může útočník zneužít. Doporučujeme, abyste správci těchto ohrožení a zlepšit stav zabezpečení svých organizací.

![Ohrožení hlášená Identity Protection](./media/vulnerabilities/identity-protection-vulnerabilities.png)

Následující části poskytují přehled o ohrožení hlášená Identity Protection.

## <a name="multi-factor-authentication-registration-not-configured"></a>Nenakonfigurovaná registrace vícefaktorového ověřování

Toto ohrožení zabezpečení pomáhá vyhodnotit nasazení služby Azure Multi-Factor Authentication ve vaší organizaci.

Azure Multi-Factor Authentication poskytuje druhou vrstvu zabezpečení pro ověřování uživatelů. To pomáhá chránit přístup k datům a aplikacím a současně plní požadavky uživatelů na jednoduchý přihlašovací proces. Azure Multi-Factor Authentication poskytuje snadno použitelný ověřování možností, jako jsou:

* Telefonní hovor
* Textová zpráva
* Oznámení mobilní aplikace
* Ověřování jednorázovým HESLEM ověřovací kód

Doporučujeme vyžadovat ověřování Azure Multi-Factor Authentication pro přihlášení uživatelů. Ověřování službou Multi-Factor Authentication hrají klíčovou roli na základě rizik zásady podmíněného přístupu k dispozici prostřednictvím služby Identity Protection.

Další informace najdete v tématu [Co je Azure Multi-Factor Authentication?](../authentication/multi-factor-authentication.md)

## <a name="unmanaged-cloud-apps"></a>Nespravované cloudové aplikace

Toto ohrožení zabezpečení pomáhá identifikovat nespravované cloudové aplikace ve vaší organizaci.

Pracovníci IT jsou často vědět o všech cloudových aplikací ve své organizaci. Je snadno zjistit, proč by správci máte obavy o neoprávněný přístup k podnikovým datům, úniku dat a další bezpečnostní rizika.

Doporučujeme, abyste nasazení Cloud Discovery ke zjišťování nespravované cloudové aplikace a ke správě těchto aplikací pomocí Azure Active Directory.

Další informace najdete v tématu [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="security-alerts-from-privileged-identity-management"></a>Výstrahy zabezpečení z Privileged Identity Managementu

Toto ohrožení zabezpečení pomáhá zjistit a řešit oznámení o privilegované identity ve vaší organizaci.  

Umožňuje uživatelům provádět privilegované operace organizace potřebují udělit dočasný nebo trvalý privilegovaný přístup uživatelům ve službě Azure AD, prostředky Azure nebo Office 365 nebo jiným aplikacím SaaS. Každá z těchto privilegovaní uživatelé zvyšuje prostor pro napadení vaší organizace. Toto ohrožení zabezpečení vám pomůže identifikovat uživatele s privilegovaným přístupem nepotřebné a přijmout vhodná opatření ke snížení nebo eliminaci, které představují riziko.

Doporučujeme organizace používá Azure AD Privileged Identity Management pro správu, řízení a monitorovat privilegované identity ve službě Azure AD a dalších online službách Microsoftu jako Office 365 nebo Microsoft Intune.

Další informace najdete v tématu [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md).

## <a name="see-also"></a>Další informace najdete v tématech

[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
