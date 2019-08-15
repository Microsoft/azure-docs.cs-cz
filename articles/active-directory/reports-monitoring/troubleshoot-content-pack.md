---
title: Řešení potíží s chybami balíčku obsahu v protokolu aktivit Azure Active Directory | Microsoft Docs
description: Poskytuje seznam chybových zpráv balíčku obsahu Azure Active Directory aktivity a postup jejich opravy.
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 06/07/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54a0a5b5306414eb50a1928ec8a1854f56055681
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68987898"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Řešení potíží s chybami balíčku obsahu v protokolu aktivit Azure Active Directory 

|  |
|--|
|V současné době balíček obsahu Azure AD Power BI používá rozhraní Azure AD Graph API k načtení dat z vašeho tenanta Azure AD. V důsledku toho můžete zaznamenat určitý nesoulad mezi daty, která jsou k dispozici v balíčku obsahu, a daty načtenými pomocí [rozhraní Microsoft Graph API pro generování sestav](concept-reporting-api.md). |
|  |

Při práci s balíčkem obsahu Power BI pro Azure Active Directory (Azure AD) je možné spustit následující chyby: 

- [Neúspěšná aktualizace](troubleshoot-content-pack.md#refresh-failed) 
- [Nepovedlo se aktualizovat přihlašovací údaje zdroje dat.](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Import dat trvá příliš dlouho.](#data-import-is-too-slow) 

V tomto článku najdete informace o možných příčinách a o tom, jak tyto chyby opravit.
 
## <a name="refresh-failed"></a>Aktualizace se nezdařila 
 
**Jak je tato chyba Surface**: Odeslání e-mailu z Power BI nebo neúspěšného stavu v historii aktualizace. 


| Příčina | Jak opravit |
| ---   | ---        |
| Chyby při selhání aktualizace se můžou způsobovat při resetování přihlašovacích údajů uživatelů, kteří se připojili k balíčku obsahu, ale ne aktualizovat v nastavení připojení balíčku obsahu. | V Power BI Najděte datovou sadu odpovídající řídicímu panelu protokoly aktivit Azure AD (**protokoly aktivit Azure Active Directory**), zvolte naplánovat aktualizaci a zadejte svoje přihlašovací údaje služby Azure AD. |
| Aktualizace může selhat kvůli rozsáhlým datovým sadám. | Balíček obsahu Azure AD s Power BI v současné době podporuje pouze malé datové sady (méně než 500, 00 řádků) kvůli omezením týkajícím se časových limitů v rámci služby Power BI. Pokud dojde k chybám při omezování nebo pokud se aktualizace nepovede kvůli problémům s časovým limitem, může to být způsobeno tím, že se pokoušíte načíst velkou datovou sadu. Zkraťte časové období v dotazu a zkuste to znovu.|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Nepovedlo se aktualizovat přihlašovací údaje zdroje dat. 
 
**Jak je tato chyba Surface**: Když se v Power BI připojíte k balíčku obsahu protokol aktivit služby Azure AD. 

| Příčina | Jak opravit |
| ---   | ---        |
| Připojující se uživatel není globálním správcem nebo čtenářem zabezpečení nebo správcem zabezpečení. | Pro přístup k balíčkům obsahu použijte účet, který je buď globálním správcem, nebo čtenářem zabezpečení, nebo správcem zabezpečení. |
| Váš tenant není tenant úrovně Premium nebo nemá aspoň jednoho uživatele se souborem licence Premium. | [Soubor a lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 


## <a name="data-import-is-too-slow"></a>Import dat je příliš pomalý. 
 
**Jak je tato chyba Surface**: Po připojení balíčku obsahu v Power BI začne proces importu dat připravovat váš řídicí panel pro protokoly aktivit služby Azure AD. Zobrazí se tato zpráva: **Importují se data...** bez dalšího postupu.  

| Příčina | Jak opravit |
| ---   | ---        |
| V závislosti na velikosti vašeho tenanta může tento krok trvat několik minut až 30 minut. | Pokud se zpráva nezměnila tak, aby zobrazovala řídicí panel během celé hodiny, požádejte [o pomoc lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Další postup

* [Nainstalujte Power BI balíčku obsahu pro sestavy služby Azure AD](quickstart-install-power-bi-content-pack.md).
* [Použití Power BI balíčku obsahu pro sestavy služby Azure AD k vizualizaci dat](howto-power-bi-content-pack.md)
* [Získání podpory pro Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md)
