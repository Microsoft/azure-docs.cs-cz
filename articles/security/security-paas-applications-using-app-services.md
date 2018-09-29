---
title: Zabezpečení PaaS webových a mobilních aplikací pomocí služby Azure App Service | Dokumentace Microsoftu
description: 'Další informace o zabezpečení služby Azure App Service osvědčené postupy pro zabezpečení vašich webových a mobilních aplikací PaaS. '
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: 99f706a3b24991e7f5a3473d40a568971b71a979
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451982"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Osvědčené postupy pro zabezpečení PaaS webových a mobilních aplikací pomocí služby Azure App Service

V tomto článku se podíváme na kolekci [služby Azure App Service](../app-service/app-service-web-overview.md) osvědčené postupy zabezpečení pro zabezpečení vašich webových a mobilních aplikací PaaS. Tyto osvědčené postupy jsou odvozeny z našich zkušenostech s Azure a prostředí zákazníků, jako sami.

Azure App Service je nabídka platform-as-a-service (PaaS), která umožňuje vytvářet webové a mobilní aplikace pro jakoukoliv platformu nebo zařízení a připojení k datům bez ohledu na, v cloudu nebo místně. App Service zahrnuje webové a mobilní funkce, které byly dříve nabízeli samostatně jako weby Azure a Azure Mobile Services. Obsahuje také nové možnosti pro automatizaci obchodních procesů a hostování cloudových rozhraní API. Jako jediná integrovaná služba App Service přináší bohatou sadu možností pro webové, mobilní a integrační scénáře.

## <a name="authenticate-through-azure-active-directory-ad"></a>Ověřování pomocí služby Azure Active Directory (AD)
Služba App Service poskytuje službu OAuth 2.0 pro zprostředkovatele identity. OAuth 2.0, zaměřuje na jednoduchost klienta pro vývojáře poskytují konkrétní povolení tocích pro webové aplikace, aplikací klasické pracovní plochy a mobilní telefony. Azure AD používá OAuth 2.0 k umožnění autorizace přístupu k mobilních a webových aplikací. Další informace najdete v tématu [ověřování a autorizace ve službě Azure App Service](../app-service/app-service-authentication-overview.md).

## <a name="restrict-access-based-on-role"></a>Omezení přístupu na základě role 
Omezení přístupu je nezbytné pro organizace, které chcete vynutit zásady zabezpečení pro přístup k datům. Řízení přístupu na základě role (RBAC) můžete přiřadit oprávnění pro uživatele, skupiny nebo aplikace v určitém rozsahu, jako je například potřeba znát a principů zabezpečení nejnižších oprávnění. Další informace o uživatelům udělíte přístup k aplikacím, najdete v článku [co je řízení přístupu na základě rolí](../role-based-access-control/overview.md).

## <a name="protect-your-keys"></a>Chrání vaše klíče
Nezáleží na jak kvalitní je zabezpečení při ztrátě klíče předplatného. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Se službou Key Vault můžete šifrovat klíče a tajné klíče (např. ověřovací klíče, klíče účtu úložiště, šifrovací klíče dat. Soubory PFX a hesla) pomocí klíčů chráněných moduly hardwarového zabezpečení (HSM). Pro zvýšené bezpečí můžete klíče importovat nebo generovat v modulech HSM. Key Vault můžete také spravovat své certifikáty protokolu TLS se automatické obnovení. Zobrazit [co je Azure Key Vault](../key-vault/key-vault-whatis.md) Další informace. 

## <a name="restrict-incoming-source-ip-addresses"></a>Omezit příchozí zdrojové IP adresy
[App Service Environment](../app-service/environment/intro.md) má funkci integrace virtuální sítě, která pomáhá omezit příchozí zdrojové IP adresy pomocí skupiny zabezpečení sítě (Nsg). Pokud nejste obeznámeni s Azure Virtual Networks (Vnet), jedná se o funkci, která umožňuje umístit řadu prostředků Azure v Internetu jiných výrobců, směrovatelné síti, které řídí přístup k. Další informace najdete v tématu [integrujte svou aplikaci s Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md).

## <a name="next-steps"></a>Další postup
V tomto článku jste se seznámili s kolekce osvědčené postupy zabezpečení služby App Service pro zabezpečení vašich webových a mobilních aplikací PaaS. Další informace o zabezpečení nasazení PaaS najdete v tématu:

- [Zabezpečení nasazení PaaS](security-paas-deployments.md)
- [Zabezpečení databáze PaaS v Azure](security-paas-applications-using-sql.md)
