---
title: Řešení potíží s Azure Active Directory aktivity chyby balíčku obsahu protokolů | Dokumentace Microsoftu
description: Poskytuje seznam chybových zpráv balíčku obsahu aktivit Azure Active Directory a kroky a opravte je.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 461c61fdd38fe8801a71c1ac34cd3e7114838207
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54825353"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Řešení potíží s Azure Active Directory aktivity chyby balíčku obsahu protokolů 

|  |
|--|
|V současné době balíček obsahu Azure AD Power BI používá rozhraní Azure AD Graph API k načtení dat z vašeho tenanta Azure AD. V důsledku toho můžete zaznamenat určitý nesoulad mezi daty, která jsou k dispozici v balíčku obsahu, a daty načtenými pomocí [rozhraní Microsoft Graph API pro generování sestav](concept-reporting-api.md). |
|  |

Při práci s balíčkem obsahu Power BI pro Azure Active Directory (Azure AD), je možné, že narazíte na následující chyby: 

- [Aktualizace se nezdařila.](troubleshoot-content-pack.md#refresh-failed) 
- [Nepovedlo se aktualizovat přihlašovací údaje ke zdroji dat](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Import dat trvá moc dlouho](troubleshoot-content-pack.md#importing-of-data-is-taking-too-long) 

Tento článek obsahuje informace o možných příčinách a jak tyto chyby opravit.
 
## <a name="refresh-failed"></a>Aktualizace se nezdařila 
 
**Jak se zobrazí tato chyba**: E-mailu z Power BI nebo stavu selhání v historii aktualizace. 


| Příčina | K vyřešení |
| ---   | ---        |
| Aktualizujte chyby, které chyby může být způsobena přihlašovacích údajů uživatele, připojení k balíčku obsahu byly obnovit, ale není aktualizovaná. v nastavení připojení balíčku obsahu. | V Power BI, vyhledejte sadu dat odpovídající panel protokolů aktivity služby Azure AD (**protokoly aktivit Azure Active Directory**), zvolte naplánovat aktualizaci a pak zadejte svoje přihlašovací údaje Azure AD. |
| Obnovení může selhat kvůli problémům s daty v základní balíčku obsahu. | [Lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Nepovedlo se aktualizovat přihlašovací údaje ke zdroji dat 
 
**Jak se zobrazí tato chyba**: V Power BI, když se připojíte k balíčku obsahu protokolů aktivit Azure AD. 

| Příčina | K vyřešení |
| ---   | ---        |
| Je připojující se uživatel není globální správce nebo čtenáře zabezpečení nebo správce zabezpečení. | Použijte účet, který je globálním správcem nebo Čtenář zabezpečení nebo správce zabezpečení pro přístup k obsahu sady. |
| Váš tenant není Premium tenanta nebo nemá alespoň jeden uživatel s licencí Premium souboru. | [Lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 


## <a name="data-import-is-too-slow"></a>Import dat je pomalý 
 
**Jak se zobrazí tato chyba**: V Power BI připojíte balíček obsahu, proces importu dat spustí Příprava řídicí panel aktivit Azure AD protokoly. Zobrazí se tato zpráva: **Importují se data...**  bez jakékoli další krok.  

| Příčina | K vyřešení |
| ---   | ---        |
| V závislosti na velikosti tenanta tento krok může trvat pár minut až 30 minut. | Pokud zprávu nezmění na zobrazení řídicího panelu do jedné hodiny, [lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Další postup

* [Nainstalujte sadu contect Power BI pro sestavy Azure AD](quickstart-install-power-bi-content-pack.md).
* [Použití Power BI content pack pro službu Azure AD zprávy k vizualizaci dat](howto-power-bi-content-pack.md)
* [Získání podpory pro Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md)
