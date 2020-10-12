---
title: Zabezpečení PaaSch mobilních aplikací pro webové &
titleSuffix: Azure App Service
description: 'Přečtěte si o Azure App Service osvědčených postupech zabezpečení pro zabezpečení webových a mobilních aplikací PaaS. '
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
ms.openlocfilehash: fc273c2e6e27c191b4407ffae86a4ba227ee642b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87131034"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Osvědčené postupy pro zabezpečení webových a mobilních aplikací PaaS pomocí Azure App Service

V tomto článku se zabýváme kolekcí [Azure App Service](/azure/app-service/overview) osvědčených postupů zabezpečení pro zabezpečení webových a mobilních aplikací PaaS. Tyto osvědčené postupy se odvozují z našich zkušeností s Azure a zkušeností zákazníků, jako je sami.

Azure App Service je nabídka typu platforma jako služba (PaaS), která umožňuje vytvářet webové a mobilní aplikace pro jakoukoli platformu nebo zařízení a připojovat se k datům kdekoli, v cloudu i v místním prostředí. App Service zahrnují webové a mobilní funkce, které byly dříve dodávány samostatně jako Azure websites a Azure Mobile Services. Obsahuje také nové možnosti pro automatizaci obchodních procesů a hostování cloudových rozhraní API. Jako jediná integrovaná služba App Service přináší bohatou sadu možností pro webové, mobilní a integrační scénáře.

## <a name="authenticate-through-azure-active-directory-ad"></a>Ověřování prostřednictvím Azure Active Directory (AD)
App Service poskytuje službě OAuth 2,0 pro vašeho poskytovatele identity. OAuth 2,0 se zaměřuje na jednoduchost vývojáře v klientských počítačích a poskytuje konkrétní autorizační toky pro webové aplikace, desktopové aplikace a mobilní telefony. Azure AD používá OAuth 2,0, který vám umožní autorizovat přístup k mobilním a webovým aplikacím. Další informace najdete v tématu [ověřování a autorizace v Azure App Service](../../app-service/overview-authentication-authorization.md).

## <a name="restrict-access-based-on-role"></a>Omezení přístupu na základě role
Omezení přístupu je nezbytné pro organizace, které chtějí vyhovět zásadám zabezpečení pro přístup k datům. Řízení přístupu na základě role (RBAC) můžete použít k přiřazení oprávnění uživatelům, skupinám a aplikacím v určitém oboru, jako je třeba znát a nejnižší principy zabezpečení oprávnění. Další informace o tom, jak udělit uživatelům přístup k aplikacím, najdete v tématu [co je řízení přístupu na základě role Azure (Azure RBAC)](/azure/role-based-access-control/overview).

## <a name="protect-your-keys"></a>Ochrana klíčů
Nezáleží na tom, jaký je dobrý způsob zabezpečení při ztrátě klíčů předplatného. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Pomocí Key Vault můžete šifrovat klíče a tajné klíče (například ověřovací klíče, klíče účtu úložiště, šifrovací klíče dat). Soubory PFX a hesla) pomocí klíčů chráněných moduly hardwarového zabezpečení (HSM). Pro zvýšené bezpečí můžete klíče importovat nebo generovat v modulech HSM. Pomocí Key Vault můžete také spravovat certifikáty TLS s automatickým obnovením. Další informace najdete v tématu [co je Azure Key Vault](../../key-vault/general/overview.md) .

## <a name="restrict-incoming-source-ip-addresses"></a>Omezit příchozí IP adresy příchozích dat
[Prostředí App Service](../../app-service/environment/intro.md) mají funkci Integrace virtuální sítě, která vám pomůže omezit příchozí IP adresy příchozích dat prostřednictvím skupin zabezpečení sítě (skupin zabezpečení sítě). Pokud nejste obeznámeni s virtuálními sítěmi Azure (virtuální sítě), jedná se o funkci, která umožňuje umístit spoustu prostředků Azure do sítě, která není Internet, směrovat do sítě, ke které ovládáte přístup. Další informace najdete v tématu [integrace aplikace s Virtual Network Azure](../../app-service/web-sites-integrate-with-vnet.md).

Pro App Service ve Windows můžete také dynamicky omezit IP adresy tím, že nakonfigurujete web.config. Další informace najdete v tématu [dynamické zabezpečení protokolu IP](/iis/configuration/system.webServer/security/dynamicIpSecurity/).


## <a name="next-steps"></a>Další kroky
Tento článek vás zavedl do kolekce App Service osvědčené postupy zabezpečení pro zabezpečení webových a mobilních aplikací PaaS. Další informace o zabezpečení nasazení PaaS najdete v těchto tématech:

- [Zabezpečení nasazení PaaS](paas-deployments.md)
- [Zabezpečení databází PaaS v Azure](paas-applications-using-sql.md)
