---
title: Azure AD Connect Health a ochrana osobních údajů uživatele | Microsoft Docs
description: Tento dokument popisuje ochraně osobních údajů uživatelů s Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: billmath
ms.openlocfilehash: bf26e91308cfec0dc8ede20e683919b5764a4868
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>Ochrana osobních údajů uživatelů a Azure AD Connect Health 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Tento článek se zabývá Azure AD Connect Health a uživatele o ochraně osobních údajů.  Informace o Azure AD Connect a uživatele o ochraně osobních údajů najdete v článku [zde](../../active-directory/connect/active-directory-aadconnect-gdpr.md).

## <a name="user-privacy-classification"></a>Klasifikace ochrany osobních údajů uživatele
Azure AD Connect Health, které patří do **procesor dat** kategorie klasifikace GDPR. Jako procesor dat kanál poskytuje služby zpracování dat služby a klíče partnery a koncoví uživatelé. Azure AD Connect Health negeneruje uživatelská data a nemá žádnou nezávislé kontrolu nad jaké osobní údaje se shromažďují a jak se používají. Načtení dat, agregace, analýzu a vytváření sestav v Azure AD Connect Health jsou založeny na existující místní data. 

## <a name="data-retention-policy"></a>Zásady uchovávání dat
Azure AD Connect Health není generování sestav, provádět analýzy nebo nabízejí přehled za 30 dní. Azure AD Connect Health proto není ukládání, zpracovat nebo zachování všech dat za 30 dní. Tento návrh je kompatibilní s předpisy GDPR, Microsoft o ochraně osobních údajů předpisy a zásad uchovávání dat služby Azure AD. 

Servery s aktivní **data služby stavu nejsou aktuální** **chyba** výstrahy pro více než 30 dní po sobě jdoucích naznačují, že žádná data dosáhl Connect Health během této časové rozpětí. Tyto servery budou zakázaná a není uvedené na portálu Connect Health. Chcete-li znovu povolit servery, je nutné odinstalovat a [přeinstalujte agenta stavu](active-directory-aadconnect-health-agent-install.md). Upozorňujeme, že to neplatí pro **upozornění** s výstrahy stejného typu. Upozornění označují, že částečná data chybí od serveru, který se zobrazí výstraha, pro. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Zakázání shromažďování dat a monitorování v Azure AD Connect Health
Azure AD Connect Health umožňuje zastavit shromažďování dat pro každý monitorovaných server nebo instanci monitorované služby. Například můžete zastavit shromažďování dat pro jednotlivé servery služby AD FS (Active Directory Federation Services), které jsou monitorovány pomocí Azure AD Connect Health. Můžete také zastavit shromažďování dat pro instanci služby AD FS, který je monitorován pomocí Azure AD Connect Health. Pokud se rozhodnete tak učinit, odpovídající servery se odstraní z portálu Azure AD Connect Health po zastavení shromažďování dat. 

>[!IMPORTANT]
> Potřebujete oprávnění globální správce Azure AD nebo roli Přispěvatel v RBAC odstranění monitorované servery z Azure AD Connect Health.
>
> Odebrání serveru nebo instance služby z Azure AD Connect Health je reverzibilního akce. 

### <a name="what-to-expect"></a>Co můžete očekávat?
Když zastavíte shromažďování dat a monitorování pro jednotlivé monitorovaném serveru nebo instanci monitorované služby, vezměte na vědomí následující:

- Při odstranění instance monitorované služby instance se odebere ze seznamu monitorování služby Azure AD Connect Health na portálu. 
- Při odstranění monitorovaných server nebo instanci monitorované služby agenta stavu se neodinstalují nebo odebrat z vašich serverů. Agent stavu je nakonfigurován tak, aby posílat data do Azure AD Connect Health. Budete muset ručně odinstalovat agenta stavu na dříve monitorované servery.
- Pokud jste agenta stavu neodinstalovali před provedením tohoto kroku, může na serverech vztahujících se k agentovi stavu objevit události chyb.
- Všechna data patřící k instanci monitorované služby se odstraní podle zásad uchovávání dat Microsoft Azure.

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Zakázání shromažďování dat a monitorování pro monitorovaném serveru
V tématu [postup odebrání serveru z Azure AD Connect Health](active-directory-aadconnect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Zakázat monitorování pro instanci monitorované služby a shromažďování dat
V tématu [postup odebrání instance služby Azure AD Connect Health](active-directory-aadconnect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).


## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Opětovné povolení shromažďování dat a monitorování v Azure AD Connect Health
Chcete-li znovu povolte sledování v Azure AD Connect Health pro dříve odstraněné monitorované služby, je nutné odinstalovat a [přeinstalujte agenta stavu](active-directory-aadconnect-health-agent-install.md) ve všech serverech.


## <a name="next-steps"></a>Další postup
* [Zkontrolujte nastavení zásad Microsoft Privacy na Centrum zabezpečení](https://www.microsoft.com/trustcenter)
* [Azure AD Connect a ochraně osobních údajů uživatelů](../../active-directory/connect/active-directory-aadconnect-gdpr.md)

