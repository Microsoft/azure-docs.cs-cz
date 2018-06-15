---
title: Azure AD Connect Health - data o stavu služby není až datum výstraha | Microsoft Docs
description: Tento dokument popisuje příčinu "data o stavu služby není aktuální" výstrahy a řešení potíží s ho.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: 45e88320a64770239c8f322212e12ca7826cd0da
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2018
ms.locfileid: "29715007"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Stav služby data nejsou aktuální výstrahy

## <a name="overview"></a>Přehled
<li>Azure AD Connect Health generuje novou výstrahu data, když ho neobdrží datových bodů ze serveru pro dvě hodiny. Název výstrahy je **data služby stavu nejsou aktuální**. </li>
<li>**Upozornění** výstrahy stavu aktivuje Connect Health neobdrží částečné datové prvky odeslaných ze serveru dvě hodiny. Stav upozornění neaktivuje e-mailová oznámení pro správce klienta. </li>
<li>**Chyba** výstrahy stavu aktivuje Connect Health neobdrží žádné datové prvky odeslaných ze serveru dvě hodiny. Chyba stavu výstrah aktivuje e-mailová oznámení pro správce klienta. </li>

>[!IMPORTANT] 
> Tato výstraha následuje Connect Health [zásad uchovávání dat](active-directory-aadconnect-health-gdpr.md#data-retention-policy)

## <a name="troubleshooting-steps"></a>Řešení potíží 
* Nezapomeňte si projít a splňovat [v části požadavky na](active-directory-aadconnect-health-agent-install.md#requirements).
* Použití [nástroj pro testování připojení](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) chcete zjistit problémy s připojením.


## <a name="next-steps"></a>Další postup
* [Azure AD Connect Health – nejčastější dotazy](active-directory-aadconnect-health-faq.md)
