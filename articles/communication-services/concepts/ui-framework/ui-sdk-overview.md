---
title: Přehled architektury uživatelského rozhraní služby Azure Communication Services
titleSuffix: An Azure Communication Services conceptual document
description: Informace o architektuře uživatelského rozhraní služby Azure Communication Services
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 47a32815ded5809edfde856a38c69ec7c6fd6fdf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493352"
---
# <a name="azure-communication-services-ui-framework"></a>Architektura uživatelského rozhraní služby Azure Communication Services

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

:::image type="content" source="../media/ui-framework/pre-and-custom-composite.png" alt-text="Porovnání mezi předem vytvořenými a vlastními složenými hodnotami":::

Architektura uživatelského rozhraní služby Azure Communication Services usnadňuje vytváření moderních komunikačních prostředí pro uživatele. Poskytuje knihovnu součástí uživatelského rozhraní připraveného pro produkční prostředí, které můžete přetáhnout do aplikací:

- **Složené komponenty** – tyto komponenty jsou řešení pro klíč, která implementují běžné komunikační scénáře. Můžete rychle přidat volání videa nebo chatovací prostředí do svých aplikací. Složené jsou otevřené zdrojové komponenty sestavené pomocí základních komponent.
- **Základní komponenty** – tyto komponenty jsou Open Source stavební bloky, které umožňují vytvářet vlastní komunikační prostředí. Komponenty jsou nabízeny pro možnosti volání i chat, které lze kombinovat s prostředím pro sestavování. 

Tyto klientské knihovny uživatelského rozhraní využívají [jazyk a prostředky společnosti Microsoft pro návrh Fluent](https://developer.microsoft.com/fluentui/) . ROZHRANÍ Fluent poskytuje základní vrstvu pro rozhraní uživatelského rozhraní, které bylo v rámci produktů Microsoftu testováno na více systémů.

## <a name="differentiating-components-and-composites"></a>**Odlišení komponent a složených prvků**

**Základní komponenty** jsou postavené na základních knihovnách klienta služby Azure Communication Services a implementují základní akce, jako je například inicializace základních klientských knihoven, vykreslování videa a poskytnutí uživatelských ovládacích prvků pro ztlumení a vypnutí videa atd. Tyto **základní komponenty** můžete použít k sestavení vlastních rozložení pomocí předem sestavených komponent pro komunikaci připravené pro produkční prostředí.

:::image type="content" source="../media/ui-framework/component-overview.png" alt-text="Přehled součásti pro rozhraní uživatelského rozhraní":::

**Složené součásti** kombinují více **základních komponent** , aby bylo možné vytvořit úplnější komunikační prostředí. Tyto komponenty vyšší úrovně se dají snadno integrovat do existující aplikace, aby se vynechal plně fledge komunikační prostředí, aniž by se musela sestavovat úplně od začátku. Vývojáři se můžou soustředit na sestavování okolního prostředí a flowu, které jsou ve svých aplikacích vhodné, a mít složitost komunikace se složenými komponentami.

:::image type="content" source="../media/ui-framework/composite-overview.png" alt-text="Přehled kompozitní architektury uživatelského rozhraní":::

## <a name="what-ui-framework-is-best-for-my-project"></a>Jaké jsou architektury uživatelského rozhraní nejvhodnější pro můj projekt?

Porozumění těmto požadavkům vám pomůže vybrat správnou klientskou knihovnu:

- **Kolik přizpůsobení si přejete?** Klientské knihovny pro komunikaci Azure nemají uživatelské rozhraní a jsou navržené tak, abyste mohli vytvořit libovolné požadované uživatelské prostředí. Komponenty architektury uživatelského rozhraní poskytují prostředky uživatelského rozhraní na náklady na omezené přizpůsobení.
- **Požadujete funkce schůzky?** Systém schůzek obsahuje několik jedinečných možností, které nejsou aktuálně k dispozici v základních knihovnách klienta služby Azure Communication Services, například rozmazaných a vystouplých pozadí.
- **Jaké platformy cílíte?** Různé platformy mají různé možnosti.

Podrobnosti o dostupnosti funkcí v různých sadách [SDK uživatelského rozhraní jsou k dispozici zde](ui-sdk-features.md), ale klíčové obchody jsou shrnuty níže.

|Klientská knihovna/sada SDK|Složitost implementace|    Možnost přizpůsobení|  Videohovory| Chat| [Týmy Interop](./../teams-interop.md)
|---|---|---|---|---|---|---|
|Složené součásti|Nízká|Nízká|✔|✔|✕
|Základní komponenty|Střední|Střední|✔|✔|✕
|Základní klientské knihovny|Vysoká|Vysoká|✔|✔ |✔

## <a name="cost"></a>Náklady

Použití platforem Azure UI nemá žádné dodatečné náklady na Azure ani měření. Platíte jenom za využití základní služby, a to za použití stejného volání, chatu a měřičů veřejné sítě.

## <a name="supported-use-cases"></a>Podporované případy použití

Videohovory

- Připojit se k volání služby Azure Communication Services pomocí ID skupiny

Prostřednictvím

- Zapojit se do chatu komunikačních služeb Azure s ID vlákna

## <a name="supported-identities"></a>Podporované identity:

Pro inicializaci architektury uživatelského rozhraní a ověření pro službu se vyžaduje identita služby Azure Communication Services. Další informace o ověřování najdete v tématu [ověřování](../authentication.md) a [přístupové tokeny](../../quickstarts/access-tokens.md) .


## <a name="recommended-architecture"></a>Doporučená architektura 

:::image type="content" source="../media/ui-framework/framework-architecture.png" alt-text="Architektura rozhraní .NET Framework doporučená pro architekturu klient-server ":::

Složené a základní komponenty se inicializují pomocí přístupového tokenu služby Azure Communication Services. Přístupové tokeny by se měly získávat z komunikačních služeb Azure prostřednictvím důvěryhodné služby, kterou spravujete. Další informace najdete v tématu [rychlý Start: vytvoření přístupových tokenů](../../quickstarts/access-tokens.md) a [služby Trusted Service v kurzu](../../tutorials/trusted-service-tutorial.md) .

Tyto klientské knihovny také vyžadují kontext pro volání nebo chat, ke kterým se připojí. Podobně jako přístupové tokeny uživatelů by tento kontext měl být šířen klientům prostřednictvím vlastní důvěryhodné služby. Následující seznam shrnuje funkce správy inicializace a prostředků, které je třeba zprovoznění.

| Povinnosti contoso                                 | Odpovědnosti architektury uživatelského rozhraní                         |
|----------------------------------------------------------|-----------------------------------------------------------------|
| Poskytnutí přístupového tokenu z Azure                    | Předání prostřednictvím daného přístupového tokenu k inicializaci komponent        |
| Zadat funkci aktualizace                                 | Aktualizace přístupového tokenu pomocí funkce poskytnuté vývojářem          |
| Načíst/předat informace o připojení pro volání nebo chat          | Předání prostřednictvím volání a informací o chatu k inicializaci komponent |
| Načtení nebo předání informací o uživateli pro libovolný vlastní datový model | Předání vlastních datových modelů komponentám k vykreslování          |
