---
title: Verze v Azure API Management | Microsoft Docs
description: Přečtěte si o konceptu verzí v Azure API Management.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 02/10/2021
ms.author: jodowns
ms.custom: fasttrack-new
ms.openlocfilehash: 55951f288314d92cf5057e7d5c1e988f65cb3e14
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040375"
---
# <a name="versions-in-azure-api-management"></a>Verze v Azure API Management

Verze umožňují vývojářům prezentovat skupiny souvisejících rozhraní API. Můžete použít verze ke bezpečnému zpracování nejnovějších změn v rozhraní API. Klienti si můžou zvolit novou verzi rozhraní API, když budou připraveni, zatímco stávající klienti budou dál používat starší verzi. Verze jsou odlišeny pomocí identifikátoru verze (což je libovolná hodnota řetězce, kterou zvolíte), a schéma správy verzí umožňuje klientům určit, kterou verzi rozhraní API chtějí použít.

Pro většinu účelů je možné každou verzi rozhraní API považovat za vlastní nezávislé rozhraní API. Dvě různé verze rozhraní API mohou mít různé sady operací a různé zásady.

S verzemi můžete:

- Publikování více verzí rozhraní API ve stejnou dobu.
- K rozlišení mezi verzemi použijte cestu, řetězec dotazu nebo hlavičku.
- Použijte libovolnou řetězcovou hodnotu, kterou chcete identifikovat, což může být číslo, datum nebo název.
- Na portálu pro vývojáře si zobrazte verze rozhraní API seskupené dohromady.
- Vezměte existující (nestandardní) rozhraní API a vytvořte novou verzi, aniž by došlo k přerušení stávajících klientů.

[Začněte s verzemi podle našeho návodu.](./api-management-get-started-publish-versions.md)

## <a name="versioning-schemes"></a>Schémata správy verzí

Různí vývojáři rozhraní API mají různé požadavky na správu verzí. Azure API Management nepředepisuje jediný přístup k verzím, ale poskytuje několik možností.

### <a name="path-based-versioning"></a>Správa verzí na základě cest

Při použití schématu správy verzí cesty musí být identifikátor verze zahrnut v cestě URL pro všechny požadavky rozhraní API.

Například `https://apis.contoso.com/products/v1` a `https://apis.contoso.com/products/v2` může odkazovat na stejné `products` rozhraní API, ale na verze `v1` a `v2` v uvedeném pořadí.

Formát adresy URL požadavku rozhraní API při použití správy verzí na základě cest je: `https://{yourDomain}/{apiName}/{versionIdentifier}/{operationId}` .

### <a name="header-based-versioning"></a>Správa verzí na základě hlaviček

Při použití schématu verze hlavičky musí být identifikátor verze zahrnut v hlavičce požadavku HTTP pro všechny požadavky rozhraní API. Můžete zadat název hlavičky požadavku HTTP.

Můžete například vytvořit vlastní hlavičku s názvem `Api-Version` a klienti můžou zadat `v1` nebo `v2` v hodnotě této hlavičky.

### <a name="query-string-based-versioning"></a>Správa verzí na základě řetězce dotazu

Při použití schématu správy verzí řetězce dotazu musí být identifikátor verze obsažen v parametru řetězce dotazu pro všechny požadavky rozhraní API. Můžete zadat název parametru řetězce dotazu.

Formát adresy URL požadavku rozhraní API při použití správy verzí založené na řetězci dotazu je: `https://{yourDomain}/{apiName}/{operationId}?{queryStringParameterName}={versionIdentifier}` .

Například `https://apis.contoso.com/products?api-version=v1` a `https://apis.contoso.com/products/api-version=v2` může odkazovat na stejné `products` rozhraní API, ale na verze `v1` a `v2` v uvedeném pořadí.

## <a name="original-versions"></a>Původní verze

Pokud přidáte verzi do rozhraní API bez verze, `Original` bude automaticky vytvořena verze a bude reagovat na výchozí adresu URL bez zadaného identifikátoru verze. `Original`Verze zajišťuje, že proces přidávání verze neruší žádné existující volající. Pokud vytvoříte nové rozhraní API s verzemi povolenými na začátku, verze se `Original` nevytvoří.

## <a name="how-versions-are-represented"></a>Jak jsou zastoupeny verze

Azure API Management udržuje prostředek nazvaný *sadu verzí*, který představuje sadu verzí pro jedno logické rozhraní API. Sada verzí obsahuje zobrazovaný název rozhraní API pro správu verzí a [schéma správy verzí](#versioning-schemes) , které se používá k přímému zpracování požadavků na zadané verze.

Každá verze rozhraní API se udržuje jako vlastní prostředek rozhraní API, který je pak přidružený k sadě verzí. Sada verzí může obsahovat rozhraní API s různými operacemi nebo zásadami. Mezi verzemi v sadě můžete významně měnit.

Azure Portal vytvoří pro vás sady verzí. Můžete upravit název a popis sady verzí v Azure Portal.

Sady verzí můžete zobrazit a spravovat přímo pomocí [Azure CLI](/cli/azure/apim/api/versionset), [Azure PowerShell](/powershell/module/az.apimanagement/#api-management), [Správce prostředků šablon](/azure/templates/microsoft.apimanagement/service/apiversionsets)nebo [rozhraní API pro Azure Resource Manager](/rest/api/apimanagement/2020-06-01-preview/apiversionset).

### <a name="migrating-a-non-versioned-api-to-a-versioned-api"></a>Migrace rozhraní API bez správy verzí na rozhraní API s verzí

Když použijete Azure Portal k povolení správy verzí pro existující rozhraní API, provedou se v prostředcích API Management tyto změny:

 * Vytvoří se nová sada verzí.
 * Existující verze je udržovaná a [nakonfigurovaná jako `Original` verze rozhraní API](#original-versions). Rozhraní API je propojeno se sadou verzí, ale nevyžaduje zadání identifikátoru verze.
 * Nová verze je vytvořená jako nové rozhraní API a je propojená se sadou verzí. Toto nové rozhraní API musí být dostupné pomocí schématu správy verzí a identifikátoru.

## <a name="versions-and-revisions"></a>Verze a revize

Verze a revize jsou odlišné funkce. Každá verze může mít více revizí, podobně jako rozhraní API bez verzí. Můžete použít revize bez použití verze nebo jiného způsobu kolem. Obvykle se verze používají k oddělení verzí rozhraní API s zásadními změnami, zatímco revize lze použít pro méně závažné a nepodstatné změny v rozhraní API.

Pokud zjistíte, že vaše revize má zásadní změny, nebo pokud chcete, aby se vaše revize nastavila na verzi beta/testu, můžete z revize vytvořit verzi. Pomocí Azure Portal klikněte na možnost vytvořit verzi z revize v místní nabídce revize na kartě Revize.

## <a name="developer-portal"></a>Portál pro vývojáře

[Portál pro vývojáře](./api-management-howto-developer-portal.md) zobrazuje každou verzi rozhraní API samostatně.

![Portál pro vývojáře API Management zobrazení seznamu verzí rozhraní API](media/api-management-versions/portal-list.png)

V podrobnostech rozhraní API se zobrazí také seznam všech verzí rozhraní API. `Original`Verze se zobrazí bez identifikátoru verze.

![API Management portálu pro vývojáře zobrazující podrobnosti rozhraní API a seznam verzí pro toto rozhraní API](media/api-management-versions/portal-details.png)

> [!TIP]
> Verze rozhraní API se musí do produktu přidat dřív, než se budou zobrazovat na portálu pro vývojáře.
