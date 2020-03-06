---
title: Azure Government web, mobilní zařízení a rozhraní API | Microsoft Docs
description: To poskytuje srovnání funkcí a pokynů pro vývoj aplikací pro Azure Government
services: azure-government
cloud: gov
documentationcenter: ''
author: gsacavdm
manager: pathuff
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/22/2019
ms.author: gsacavdm
ms.openlocfilehash: 15c6936b7a8c319c4ddef86eddc47546966782b4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396339"
---
# <a name="azure-government-web--mobile"></a>Azure Government Web a mobilní zařízení

Tento článek popisuje webové a mobilní služby pro prostředí Azure Government, popis variací funkcí, které se liší od veřejné verze, a také všechny požadavky na konkrétní prostředí.

## <a name="azure-cognitive-search"></a>Azure Cognitive Search

[Azure kognitivní hledání](https://docs.microsoft.com/azure/search/) je všeobecně k dispozici v Azure Government. Pro zkoumání funkcí vyhledávání pomocí veřejných dat státní správy navštivte web pro [vyhledávání obsahu a inteligentní](https://documentsearch.azurewebsites.net/home/) informace vyberte datovou sadu "Soudní dvůr pro odvolání oblasti 1" a pak zvolte jednu z možností ukázky.

Funkce hledání, které byly široce přijaty v aplikacích pro vyhledávání pro státní správu, zahrnují [dovednosti v rozpoznávání](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro), užitečné pro extrakci struktury a informací z velkých nerozlišených textových dokumentů.

Základní syntaxe dotazů, formulující dotazy pro hledání velkého množství obsahu, je relevantní také pro vývojáře aplikací. Azure Kognitivní hledání podporuje dvě syntaxe: [Jednoduchá](https://docs.microsoft.com/azure/search/query-simple-syntax) a [plná](https://docs.microsoft.com/azure/search/query-lucene-syntax). Můžete zkontrolovat [Příklady výrazů dotazů](https://docs.microsoft.com/azure/search/search-query-simple-examples) pro orientaci.

### <a name="variations"></a>Odlišnost
Koncový bod pro služby vyhledávání vytvořený v Azure Government je následující:

| Typ služby | Veřejné Azure | Azure Government |
| ------------ | ------------ | ---------------- |
| Služba Azure Kognitivní hledání |\*. search.windows.net |\*. search.windows.us|

Všechny všeobecně dostupné a ukázkové funkce ve veřejném cloudu jsou dostupné taky v Azure Government.

### <a name="considerations"></a>Požadavky

Následující informace identifikují Azure Government hranici pro Azure Kognitivní hledání:

| Povolená řízená/řízená data | Regulovaná/řízená data nejsou povolena. |
| ----------------------------------- | --------------------------------------- |
| Všechna data uložená a zpracovaná v Azure Kognitivní hledání můžou obsahovat data regulovaná Azure Government. | Metadata Azure Kognitivní hledání nesmí obsahovat data řízená exportem. Tato metadata zahrnují všechna konfigurační data zadaná při vytváření a údržbě služby. Nezadávejte regulovaná a řízená data do následujících polí: název předplatného, skupiny prostředků, název služby, názvy prostředků, značky prostředků nebo libovolný objekt, který zadáte, nebo popište v Kognitivní hledání (indexy, indexery, zdroje dat, mapy synonym a dovednosti). Nezahrnujte citlivá data do hlaviček protokolu HTTP odeslaných do REST API žádosti o přijetí změn v řetězcích hledání nebo dotaz odeslaných jako součást rozhraní API.|

## <a name="app-services"></a>App Services
### <a name="variations"></a>Odlišnost
Azure App Services je všeobecně k dispozici v Azure Government.

Adresa pro aplikace Azure App Service vytvořené v Azure Government se liší od aplikací vytvořených ve veřejném cloudu:

| Typ služby | Veřejné Azure | Azure Government |
| --- | --- | --- |
| App Service |\*. azurewebsites.net |\*. azurewebsites.us|
| ID instančního objektu| abfa0a7c-a6b6-4736-8310-5855508787cd | 6a02c803-dafd-4136-b4c3-5a6f318b4714 |

Některé funkce App Service dostupné ve veřejném cloudu nejsou ještě dostupné v Azure Government:

- Diagnostikovat a řešit problémy
- Nasazení
    - Možnosti nasazení: k dispozici je jenom místní úložiště Git a externí úložiště.
    - Centrum nasazení
- Nastavení
    - Application Insights
- Vývojové nástroje
    - Test výkonnosti
    - Průzkumník prostředků
    - Ladění PHP
- Podpora řešení potíží &
    - App Service Advisor
- Certifikáty App Service


### <a name="considerations"></a>Požadavky
Následující informace identifikují Azure Government hranici pro App Service:

| Povolená řízená/řízená data | Regulovaná/řízená data nejsou povolena. |
| --- | --- |
| Vložená data, uložená a zpracovaná v rámci Azure App Service mohou obsahovat data řízená exportem. Binární soubory běžící v rámci Azure App Service. Statické ověřovací objekty, jako jsou hesla a PIN kódy SmartCard pro přístup k součástem platformy Azure. Soukromé klíče certifikátů, které se používají ke správě komponent platformy Azure. Připojovací řetězce SQL. Další informace o zabezpečení/tajné kódy, jako jsou certifikáty, šifrovací klíče, hlavní klíče a klíče úložiště uložené ve službách Azure. |Metadata neumožňují obsahovat data řízená exportem. Tato metadata zahrnují všechna konfigurační data zadaná při vytváření a údržbě Azure App Service. Nezadávejte regulovaná nebo řízená data do následujících polí: skupiny prostředků, názvy prostředků, značky prostředků|

## <a name="api-management"></a>API Management
Podrobnosti o této službě a o tom, jak ji používat, najdete v [dokumentaci k Azure API Management](../api-management/index.yml).

### <a name="variations"></a>Odlišnost

Služba Azure API Management je všeobecně dostupná v Azure Government. Funkce, které nejsou aktuálně k dispozici ve službě API Management Service pro Azure Government jsou:

- Integrace Azure AD B2C 
    - Integrace s Azure AD B2C není k dispozici v Azure Government 

Adresy URL pro přístup k Azure API Management v Azure Government se liší:

| Typ služby | Veřejné Azure | Azure Government |
| --- | --- | --- |
|Brána API Management| \*. azure-api.net| \*. azure-api.us|
|Portál API Management | \*. portal.azure-api.net |\*. portal.azure-api.us| 
|Správa API Management| \*. management.azure-api.net |\*. management.azure-api.us|

### <a name="considerations"></a>Požadavky
Následující informace identifikují Azure Government hranici pro službu Azure API Management Service:

| Povolená řízená/řízená data | Regulovaná/řízená data nejsou povolena. |
| --- | --- |
|Všechna data uložená a zpracovaná ve službě Azure API Management Service můžou obsahovat Azure Government regulovaná data.|Metadata služby Azure API Management nesmí obsahovat data řízená exportem. Nezadávejte regulovaná nebo řízená data do následujících polí: API Management název služby, název předplatného, skupiny prostředků a značky prostředků.|

## <a name="next-steps"></a>Další kroky
Další informace a aktualizace získáte po přihlášení k odběru [blogu Microsoft Azure Government.](https://blogs.msdn.microsoft.com/azuregov/)

