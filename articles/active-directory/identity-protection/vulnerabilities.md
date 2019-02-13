---
title: Chyb zabezpečení detekovaných službou Azure Active Directory Identity Protection | Dokumentace Microsoftu
description: Přehled chyb zabezpečení detekovaných službou Azure Active Directory Identity Protection.
services: active-directory
keywords: cloud discovery, Správa aplikací, zabezpečení, rizika, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení služby Azure active directory identity protection
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: 92233a5b-cb34-4d28-88cc-d5d29c0f3256
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: markvi
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5968731981e9ba602a27b6b66a53e7b67e8fa7f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56187317"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Chyb zabezpečení detekovaných službou Azure Active Directory Identity Protection
Ohrožení zabezpečení jsou slabá místa ve vašem prostředí, která je někdo zneužije ze strany útočníka. Doporučujeme, abyste řešit tyto nedostatky zabezpečení a zlepšit stav zabezpečení vaší organizace a útočníkům zabránit v jejich využívání.


![ohrožení zabezpečení](./media/vulnerabilities/101.png "ohrožení zabezpečení")



Následující části poskytují přehled o ohrožení hlášená Identity Protection.

## <a name="multi-factor-authentication-registration-not-configured"></a>Nenakonfigurovaná registrace vícefaktorového ověřování
Toto ohrožení zabezpečení pomáhá řídit nastavení ověřování Azure Multi-Factor Authentication ve vaší organizaci. 

Ověřování Azure Multi-Factor Authentication poskytuje druhou vrstvu zabezpečení pro ověřování uživatelů. To pomáhá chránit přístup k datům a aplikacím a současně plní požadavky uživatelů na jednoduchý přihlašovací proces. Nabízí silné ověřování pomocí řady jednoduchých – telefonní hovor, textová zpráva nebo mobilní aplikace oznámení nebo ověřovací kód a třetích stran tokenů OATH.

Doporučujeme vyžadovat ověřování Azure Multi-Factor Authentication pro přihlášení uživatelů. Ověřování službou Multi-Factor Authentication hrají klíčovou roli v rámci zásad podmíněného přístupu na základě rizik k dispozici prostřednictvím služby Identity Protection.

Další informace najdete v tématu [Co je Azure Multi-Factor Authentication?](../authentication/multi-factor-authentication.md)

## <a name="unmanaged-cloud-apps"></a>Nespravované cloudové aplikace
Toto ohrožení zabezpečení pomáhá identifikovat nespravované cloudové aplikace ve vaší organizaci.

V moderních podnicích jsou často IT oddělením vědět o všech cloudových aplikací, které uživatelé v jejich organizaci používají ke své práci. Je snadno zjistit, proč by správci máte obavy o neoprávněný přístup k podnikovým datům, úniku dat a další bezpečnostní rizika. 

Doporučujeme k nasazení Cloud Discovery ke zjišťování nespravované cloudové aplikace a ke správě těchto aplikací pomocí Azure Active Directory.

Další informace najdete v tématu [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="security-alerts-from-privileged-identity-management"></a>Výstrahy zabezpečení z Privileged Identity Managementu
Toto ohrožení zabezpečení pomáhá zjistit a řešit oznámení o privilegované identity ve vaší organizaci.  

Umožňuje uživatelům provádět privilegované operace organizace potřebují udělit dočasný nebo trvalý privilegovaný přístup uživatelům ve službě Azure AD, prostředky Azure nebo Office 365 nebo jiným aplikacím SaaS. Každá z těchto privilegovaní uživatelé zvyšuje prostor pro napadení vaší organizace. Toto ohrožení zabezpečení vám pomůže identifikovat uživatele s privilegovaným přístupem nepotřebné a přijmout vhodná opatření ke snížení nebo eliminaci, které představují riziko. 

Doporučujeme vám, že vaše organizace používá Azure AD Privileged Identity Management pro správu, řízení a monitorování privilegované identity a jejich přístup k prostředkům ve službě Azure AD, jakož i dalších online službách Microsoftu jako Office 365 nebo Microsoft Intune.

Další informace najdete v tématu [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md). 

## <a name="see-also"></a>Další informace najdete v tématech

[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

