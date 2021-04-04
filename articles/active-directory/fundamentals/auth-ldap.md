---
title: Ověřování protokolu LDAP pomocí Azure Active Directory
description: Pokyny pro architekturu k dosažení ověřování LDAP pomocí Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f209d394e1a0c2c4ddde9cbf8df2704647e2822a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168708"
---
# <a name="ldap-authentication-with-azure-active-directory"></a>Ověřování protokolu LDAP pomocí Azure Active Directory

Protokol LDAP (Lightweight Directory Access Protocol) je aplikační protokol pro práci s různými adresářovými službami. Adresářové služby, jako je služba Active Directory, [informace o uživatelích a účtech](https://www.dnsstuff.com/active-directory-service-accounts)a informace o zabezpečení, jako jsou hesla. Služba pak umožní sdílení informací s ostatními zařízeními v síti. Podnikové aplikace, jako je e-mail, správci vztahů se zákazníky (CRMs) a software pro lidské zdroje (HR), můžou k ověřování, přístupu a hledání informací používat protokol LDAP. 

Azure Active Directory (Azure AD) Tento vzor podporuje přes Azure AD Domain Services (služba AD DS). Umožňuje organizacím, které přijímají strategii zaměřené na Cloud, modernizovat své prostředí tím, že přesouvá své místní prostředky LDAP do cloudu. Okamžité výhody budou: 

* Integrace se službou Azure AD. Přidání uživatelů a skupin nebo změny atributů u jejich objektů se automaticky synchronizují z vašeho tenanta Azure AD a služba AD DS. Změny objektů v místní službě Active Directory se synchronizují do Azure AD a pak se služba AD DS.

* Zjednodušte operace. Omezuje nutnost ručně uchovávat a opravovat místní infrastruktury. 

* Spolehlivosti. Získáte spravované a vysoce dostupné služby. 

## <a name="use-when"></a>Kdy použít

Pro aplikaci nebo službu je nutné, aby používala ověřování protokolu LDAP.

![Diagram architektury](./media/authentication-patterns/ldap-auth.png)

## <a name="components-of-system"></a>Součásti systému

* **Uživatel**: přístup k aplikacím závislým na protokolu LDAP prostřednictvím prohlížeče.

* **Webový prohlížeč**: rozhraní, se kterým uživatel pracuje, aby bylo možné získat přístup k externí adrese URL aplikace.

* **Virtual Network**: privátní síť v Azure, přes kterou může starší verze aplikace využívat služby LDAP. 

* **Starší verze aplikací**: úlohy aplikací nebo serverů, které vyžadují protokol LDAP nasazený ve virtuální síti v Azure, nebo které mají na služba AD DS IP adresy instancí prostřednictvím síťových tras. 

* **Azure AD**: synchronizuje informace o identitě z místního adresáře organizace prostřednictvím Azure AD Connect.

* **Azure AD Domain Services (služba AD DS)**: provádí jednosměrnou synchronizaci z Azure AD, která poskytuje přístup k centrální skupině uživatelů, skupin a přihlašovacích údajů. Instance služba AD DS je přiřazena k virtuální síti. Aplikace, služby a virtuální počítače v Azure, které se připojují k virtuální síti přiřazené k služba AD DS, můžou používat běžné služba AD DS funkce, jako je LDAP, připojení k doméně, zásady skupiny, Kerberos a ověřování NTLM.
   > [!NOTE]
   >  V prostředích, kde organizace nemůže synchronizovat hodnoty hash hesel nebo se uživatelé přihlásí pomocí čipových karet, doporučujeme, abyste v služba AD DS používali doménovou strukturu prostředků. 

* **Azure AD Connect**: Nástroj pro synchronizaci informací o místních identitách, který se má Microsoft Azure AD. Průvodce nasazením a s asistencí vám pomůžou nakonfigurovat požadavky a součásti potřebné pro připojení, včetně synchronizace a přihlášení ze služby Active Directory do Azure AD. 

* **Active Directory**: Adresářová služba, která ukládá [místní informace o identitě, jako jsou informace o uživatelích a účtech](https://www.dnsstuff.com/active-directory-service-accounts), a informace o zabezpečení, jako jsou hesla.

## <a name="implement-ldap-authentication-with-azure-ad"></a>Implementace ověřování LDAP pomocí Azure AD

* [Vytvoření a konfigurace instance služby Azure služba AD DS](../../active-directory-domain-services/tutorial-create-instance.md) 

* [Konfigurace virtuální sítě pro instanci Azure služba AD DS](../../active-directory-domain-services/tutorial-configure-networking.md) 

* [Konfigurace protokol Secure LDAP pro spravovanou doménu Azure služba AD DS](../../active-directory-domain-services/tutorial-configure-ldaps.md) 

* [Vytvoření odchozího vztahu důvěryhodnosti doménové struktury k místní doméně v Azure služba AD DS](../../active-directory-domain-services/tutorial-create-forest-trust.md)

