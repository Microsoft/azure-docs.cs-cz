---
title: Zařízení připojená k fungování jednotného přihlašování k místním prostředkům v Azure AD | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat hybridní zařízení připojená k Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2018
ms.author: markvi
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c42a2240bd5b242634cbb255bf717f11610f063
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56161587"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>Zařízení připojená k fungování jednotného přihlašování k místním prostředkům v Azure AD

Není pravděpodobně překvapením, že zařízení připojené k doméně Azure Active Directory (Azure AD) poskytuje možnosti jednotného přihlašování (SSO) vašeho tenanta cloudových aplikací. Pokud vaše prostředí služby v místním Active Directory (AD), můžete rozšířit možnosti jednotného přihlašování na těchto zařízeních k němu.

Tento článek vysvětluje, jak to funguje.

## <a name="how-it-works"></a>Jak to funguje 

Protože je potřeba zapamatovat jedním jedno uživatelské jméno a heslo, jednotné přihlašování zjednodušuje přístup k vašim prostředkům a zvyšuje zabezpečení vašeho prostředí. Zařízení připojené k doméně Azure AD uživatelé již mají Jednotným přihlašováním do cloudových aplikací ve vašem prostředí. Pokud je vaše prostředí Azure AD a místní AD, budete pravděpodobně chtít zvětšit rozsah prostředí jednotného přihlašování k místní řádku obchodní (LOB) aplikace, sdílené složky a tiskárny.  


Podpora k zařízením Azure AD, které jsou připojené k nemají žádné informace o místní prostředí AD vzhledem k tomu, že nejsou připojené k němu. Ale může poskytovat další informace o místní AD do těchto zařízení se službou Azure AD Connect.
Prostředí, ve kterém jsou obě, kterým je Azure AD a místní AD, je také známá má hybridní prostředí. Pokud máte hybridní prostředí, je pravděpodobné, že už máte Azure AD Connect, které jsou nasazené na synchronizovat informace o místních identit do cloudu. Jako součást procesu synchronizace Azure AD Connect synchronizuje místní informace o doméně do Azure AD. Když se uživatel přihlásí ke službě Azure AD připojené zařízení v hybridním prostředí:

1. Azure AD pošle že název domény místní uživatel je členem skupiny zpět do zařízení. 

2. Služba úřad místní zabezpečení povolí ověřování protokolu Kerberos na zařízení.

Při pokus o přístup k prostředku v doméně místní uživatele, zařízení:

1. Informace o doméně používá k vyhledání řadič domény (DC). 

2. Odešle místní domény informace a přihlašovací údaje uživatele najít řadič domény získat uživatel ověřený.

3. Přijímá protokolu Kerberos [udělování lístků (TGT)](https://docs.microsoft.com/windows/desktop/secauthn/ticket-granting-tickets) , který se používá pro přístup k prostředkům připojených k AD.

Všechny aplikace, které jsou nakonfigurované pro **ověření integrované Windows** bezproblémově získat jednotné přihlašování, když se uživatel pokusí o přístup k nim.  

Windows Hello pro firmy vyžaduje další konfiguraci k povolení přihlášení SSO s místními ze zařízení připojeného k Azure AD. Další informace najdete v tématu [konfiguraci Azure AD připojené zařízení pro On-premises jednotné přihlašování v pomocí Windows Hello pro firmy](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base). 

## <a name="what-you-get"></a>Co získáte

S jednotným Přihlašováním připojené zařízení, které můžete v Azure AD: 

- Přístup k cestě UNC na členský server AD

- Přístup k AD člen webový server nakonfigurován pro integrovaný režim Windows zabezpečení 



Pokud chcete spravovat místní AD ze zařízení s Windows nainstalovat [vzdálenou správu serveru nástroje pro Windows 10](https://www.microsoft.com/en-us/download/details.aspx?id=45520).

Můžete použít:

- Uživatelé služby Active Directory počítače (ADUC) snap-in a k povolení správy všech objektů AD. Ale budete muset zadat doménu, kterou chcete připojit k ručně.

- Modul snap-in DHCP pro správu serveru DHCP připojená k AD. Ale možná muset zadat název serveru DHCP nebo adresu.

 
## <a name="what-you-should-know"></a>Co byste měli vědět

Možná budete muset upravit vaše [filtrování podle domén](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) ve službě Azure AD Connect k zajištění, že se synchronizují data o požadovaných domén.

Aplikace a prostředky, které jsou závislé na službě Active Directory ověřování počítače nebudou fungovat, protože zařízení připojená k Azure AD nemáte počítačového objektu ve službě AD. 

Soubory nelze sdílet s dalšími uživateli v Azure zařízení připojená k AD.

## <a name="next-steps"></a>Další postup

Další informace najdete v tématu [co je Správa zařízení ve službě Azure Active Directory?](overview.md) 
