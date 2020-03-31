---
title: Zabezpečení webových & mobilní aplikace PaaS
titleSuffix: Azure App Service
description: 'Přečtěte si o doporučených postupech zabezpečení služby Azure App Service pro zabezpečení webových a mobilních aplikací PaaS. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2019
ms.author: terrylan
ms.openlocfilehash: c3f3c7fbaa043a03b70ab770c06e493716c70daf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77500284"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Doporučené postupy pro zabezpečení webových a mobilních aplikací PaaS pomocí služby Azure App Service

V tomto článku se zabýváme kolekcí doporučených postupů zabezpečení [služby Azure App Service](/azure/app-service/overview) pro zabezpečení webových a mobilních aplikací PaaS. Tyto osvědčené postupy jsou odvozeny z našich zkušeností s Azure a zkušeností zákazníků, jako jste vy.

Azure App Service je platforma jako služba (PaaS), která vám umožní vytvářet webové a mobilní aplikace pro jakoukoli platformu nebo zařízení a připojit se k datům kdekoli, v cloudu nebo místně. Služba App Service zahrnuje webové a mobilní funkce, které byly dříve doručovány samostatně jako Weby Azure a Mobilní služby Azure. Obsahuje také nové možnosti pro automatizaci obchodních procesů a hostování cloudových rozhraní API. Jako jediná integrovaná služba přináší app service bohatou sadu funkcí pro webové, mobilní a integrační scénáře.

## <a name="authenticate-through-azure-active-directory-ad"></a>Ověření prostřednictvím služby Azure Active Directory (AD)
Služba App Service poskytuje službu OAuth 2.0 pro vašeho poskytovatele identity. OAuth 2.0 se zaměřuje na jednoduchost vývojářů klientů a zároveň poskytuje specifické autorizační toky pro webové aplikace, desktopové aplikace a mobilní telefony. Azure AD používá OAuth 2.0, který vám umožní autorizovat přístup k mobilním a webovým aplikacím. Další informace najdete [v tématu Ověřování a autorizace ve službě Azure App Service](../../app-service/overview-authentication-authorization.md).

## <a name="restrict-access-based-on-role"></a>Omezení přístupu na základě role
Omezení přístupu je nezbytné pro organizace, které chtějí vynutit zásady zabezpečení pro přístup k datům. Pomocí řízení přístupu na základě rolí (RBAC) můžete přiřadit oprávnění uživatelům, skupinám a aplikacím v určitém oboru, jako je například potřeba znát a nejméně oprávnění zásadzabezpečení. Další informace o udělení přístupu uživatelům k aplikacím naleznete v tématu [Co je řízení přístupu založené na rolích](/azure/role-based-access-control/overview).

## <a name="protect-your-keys"></a>Chraňte své klíče
Nezáleží na tom, jak dobré je vaše zabezpečení, pokud ztratíte klíče předplatného. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Pomocí trezoru klíčů můžete šifrovat klíče a tajné klíče (například ověřovací klíče, klíče účtu úložiště, šifrovací klíče dat, . PFX soubory a hesla) pomocí klíčů, které jsou chráněny moduly hardwarového zabezpečení (HSM). Pro zvýšené bezpečí můžete klíče importovat nebo generovat v modulech HSM. Trezor klíčů můžete také použít ke správě certifikátů TLS s automatickým obnovením. Další informace najdete v [tématu Co je Azure Key Vault.](../../key-vault/key-vault-overview.md)

## <a name="restrict-incoming-source-ip-addresses"></a>Omezení příchozích zdrojových ADRES IP
[Prostředí služby App Service má](../../app-service/environment/intro.md) funkci integrace virtuální sítě, která vám pomůže omezit příchozí zdrojové IP adresy prostřednictvím skupin zabezpečení sítě (NSGs). Pokud nejste obeznámeni s virtuálními sítěmi Azure (VNET), je to funkce, která vám umožní umístit mnoho prostředků Azure v síti, která není internet, směrovatelná, ke které řídíte přístup. Další informace najdete v [tématu Integrace aplikace s virtuální sítí Azure](../../app-service/web-sites-integrate-with-vnet.md).

V případě služby App Service v systému Windows můžete také dynamicky omezit adresy IP konfigurací souboru web.config. Další informace naleznete [v tématu Dynamic IP Security](/iis/configuration/system.webServer/security/dynamicIpSecurity/).


## <a name="next-steps"></a>Další kroky
Tento článek vás seznámil s kolekcí doporučených postupů zabezpečení služby App Service pro zabezpečení webových a mobilních aplikací PaaS. Další informace o zabezpečení nasazení PaaS najdete v tématu:

- [Zabezpečení nasazení PaaS](paas-deployments.md)
- [Zabezpečení databází PaaS v Azure](paas-applications-using-sql.md)
