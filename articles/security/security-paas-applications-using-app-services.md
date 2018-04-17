---
title: Zabezpečení PaaS webové a mobilní aplikace pomocí služby Azure App Service | Microsoft Docs
description: " Další informace o zabezpečení služby Azure App Service osvědčené postupy pro zabezpečení vašich PaaS webové a mobilní aplikace. "
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
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: d2e606fe23a3a6eb9d2310b0932ccec8fcfc518c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Zabezpečení PaaS webové a mobilní aplikace pomocí služby Azure App Service

V tomto článku probereme kolekce [Azure App Service](https://azure.microsoft.com/services/app-service/) osvědčené postupy zabezpečení pro zabezpečení vašich PaaS webové a mobilní aplikace. Tyto doporučené postupy jsou odvozeny od našich zkušeností s Azure a prostředí zákazníků jako sami.

## <a name="azure-app-service"></a>Azure App Service
[Aplikační služba Azure](../app-service/app-service-web-overview.md) je PaaS, nabídka, která umožňuje vytvářet webových a mobilních aplikací pro všechny platformy a zařízení a připojte se k datům kdekoliv a v cloudu nebo místně. Služby App Service zahrnuje webové a mobilní funkce, které byly dříve nabízeli samostatně jako weby Azure a Azure Mobile Services. Obsahuje také nové možnosti pro automatizaci obchodních procesů a hostování cloudových rozhraní API. Jako jediná integrovaná služba App Service přináší bohatou sadu funkcí pro webové, mobilní a integrační scénáře.

## <a name="best-practices"></a>Osvědčené postupy

Při používání služby App Service, postupujte podle těchto osvědčené postupy:

- [Ověřování pomocí služby Azure Active Directory (AD)](../app-service/app-service-authentication-overview.md). Služba App Service poskytuje službu OAuth 2.0 pro zprostředkovatele identity. OAuth 2.0 se zaměřuje na jednoduchost vývojáře klienta při současném poskytování toky konkrétní autorizace pro webové aplikace, aplikací klasické pracovní plochy a mobilních telefonů. Azure AD umožňují autorizovat přístup k mobilní a webových aplikací pomocí OAuth 2.0.
- Omezení přístupu na základě potřeba znát a principy zabezpečení nejnižší oprávnění. Omezení přístupu je nutné pro organizace, které chcete vynutit zásady zabezpečení pro přístup k datům. Řízení přístupu na základě rolí (RBAC) slouží k přiřazení oprávnění pro uživatele, skupiny a aplikace v určité oboru. Další informace o uživatelům udělen přístup k aplikacím, najdete v části [Začínáme se správou přístupu](../role-based-access-control/overview.md).
- Chrání vaše klíče. Nezávisle na tom, jak dobrý zabezpečení je při ztrátě klíče pro předplatné. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Pomocí Key Vault můžete šifrovat klíče a tajné klíče (např. ověřovací klíče, klíče účtu úložiště, šifrovací klíče dat, soubory PFX a hesla) pomocí klíčů chráněných moduly hardwarového zabezpečení (HSM). Pro zvýšené bezpečí můžete klíče importovat nebo generovat v modulech HSM. V tématu [Azure Key Vault](../key-vault/key-vault-whatis.md) Další informace. Key Vault můžete také spravovat certifikáty protokolu TLS s automatického obnovení.
- Omezte příchozí zdrojové IP adresy. [Služba App Service Environment](../app-service/environment/intro.md) má funkce integrace virtuální sítě, která pomáhá omezit příchozí zdrojové IP adresy pomocí skupin zabezpečení sítě (Nsg). Pokud jste obeznámeni s virtuální sítí Azure (virtuální sítě), toto je funkce, která umožňuje umístit mnoho vašich prostředků Azure v Internetu jiných výrobců, směrovatelné síti, která můžete řídit přístup ke. Další informace najdete v tématu [aplikace integrovat Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md).

## <a name="next-steps"></a>Další postup
Tento článek je zavedená do kolekce služby App Service osvědčené postupy zabezpečení pro zabezpečení vašich PaaS webové a mobilní aplikace. Další informace o zabezpečení vašich PaaS nasazení najdete v tématu:

- [Zabezpečení nasazení PaaS](security-paas-deployments.md)
- [Zabezpečení PaaS webové a mobilní aplikace pomocí Azure SQL Database a SQL Data Warehouse](security-paas-applications-using-sql.md)
