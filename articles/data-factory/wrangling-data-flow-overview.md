---
title: Tahání datové toky v Azure Data Factory | Microsoft Docs
description: Přehled toků dat tahání v Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 05119adfa5b254297fd656854a027ede05c5ef26
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518577"
---
# <a name="what-are-wrangling-data-flows"></a>Co jsou toky dat tahání?

Organizace musí provést přípravu a tahání dat, aby bylo možné přesně analyzovat složitá data, která se budou dál rozšiřovat každý den. Příprava dat je povinná, aby organizace mohli používat data v různých obchodních procesech a zkrátit čas na hodnotu.

Tahání datové toky v Azure Data Factory umožňují iterativní přípravu dat bez kódu na úrovni cloudu. Tahání datové toky se integrují s [Power Query online](https://docs.microsoft.com/power-query/) a zpřístupňují Power Query M funkcí pro uživatele Data Factory.

Tok dat tahání transformuje M vygenerované online editorem hybridních webových Power Query aplikací do kódu Spark pro provádění škálování cloudu.

Toky dat tahání jsou zvláště užitečné pro inženýry dat nebo "občany pro datové integrátory".

## <a name="use-cases"></a>Případy použití

### <a name="fast-interactive-data-exploration-and-preparation"></a>Rychlé interaktivní zkoumání a Příprava dat

Více datových techniků a integrátorů dat občana může interaktivně prozkoumat a připravit datové sady v cloudovém měřítku. Díky nárůstu objemu, rozmanitosti a rychlosti dat v datových lakůch potřebují uživatelé účinný způsob, jak prozkoumat a připravit datové sady. Například může být nutné vytvořit datovou sadu, která obsahuje demografické údaje o zákaznících pro nové zákazníky od 2017. Nejste mapováni na známý cíl. Zkoumáte, tahání a připravujete datové sady, abyste splnili požadavky před jejich publikováním v Lake. Toky dat tahání se často používají pro méně formální analytické scénáře. Datové sady připravenou se dají použít k provádění transformací a operací strojového učení.

### <a name="code-free-agile-data-preparation"></a>Agilní Příprava dat bez kódu

Integrátory dat občana stráví více než 60% času hledáním a přípravou dat. Hledají si ho jako bezplatný kód pro zlepšení provozní produktivity. Umožnění rozšíření, tvaru a publikování dat občanům, kteří mají k disPower Query v rámci škálovatelné nástroje, jako je online, výrazně zlepšuje produktivitu. Tahání data flow v Azure Data Factory umožňuje známé Power Query online editoru hybridních webových aplikací, které umožňují integrátorům dat občanů rychle opravovat chyby, standardizovat data a vytvářet vysoce kvalitní data pro podporu obchodních rozhodnutí.

### <a name="data-validation"></a>Ověření dat

Vizuálně naskenujte vaše data způsobem bez kódu, abyste mohli odebrat jakékoli odlehlé hodnoty, anomálie a odpovídat na obrazec pro rychlou analýzu.

## <a name="next-steps"></a>Další kroky

Naučte se [vytvářet tahání tok dat](wrangling-data-flow-tutorial.md).