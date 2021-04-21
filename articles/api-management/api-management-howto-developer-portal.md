---
title: Přehled portálu pro vývojáře v Azure API Management
titleSuffix: Azure API Management
description: Přečtěte si o portálu pro vývojáře v API Management – přizpůsobitelný web, ve kterém můžou uživatelé rozhraní API prozkoumat vaše rozhraní API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3f5da5ed6c828278eb25a9fa4fa5cfb90e16f8f4
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815954"
---
# <a name="overview-of-the-developer-portal"></a>Přehled portálu pro vývojáře

Portál pro vývojáře je automaticky generovaný plně přizpůsobitelný web s dokumentací vašich rozhraní API. Je tam, kde můžou příjemci rozhraní API zjišťovat vaše rozhraní API, učit se, jak je používat, žádat o přístup a vyzkoušet si je.

Jak je uvedeno v tomto článku, můžete přizpůsobit a roztáhnout portál pro vývojáře pro konkrétní scénáře. 

![Portál pro vývojáře API Management](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="migration-from-the-legacy-portal"></a>Migrace ze starší verze portálu

> [!IMPORTANT]
> Starší verze portálu pro vývojáře je nyní zastaralá a bude dostávat pouze aktualizace zabezpečení. Můžete ho i nadále používat, jak je obvyklé, do svého vyřazení v říjnu 2023, když se odebere ze všech API Management Services.

Migrace na nový portál pro vývojáře je popsaná v [článku věnovaném vyhrazené dokumentaci](developer-portal-deprecated-migration.md).

## <a name="customization-and-styling"></a>Přizpůsobení a stylování

Portál pro vývojáře se dá přizpůsobit a vytvořit jeho stylem prostřednictvím integrovaného a přetahování pomocí vizuálního editoru. Další podrobnosti najdete v [tomto kurzu](api-management-howto-developer-portal-customize.md) .

## <a name="extensibility"></a><a name="managed-vs-self-hosted"></a> Rozšiřitelnost

Vaše služba API Management obsahuje integrovaný, stále aktuální, **spravovaný** portál pro vývojáře. K němu můžete přistupovat z rozhraní Azure Portal.

Pokud ho potřebujete zvětšit pomocí vlastní logiky, která není předem podporovaná, můžete změnit jeho základ kódu. Základ kódu na portálu je [k dispozici v úložišti GitHubu](https://github.com/Azure/api-management-developer-portal). Můžete například implementovat nový widget, který se integruje se systémem podpory třetí strany. Při implementaci nových funkcí můžete vybrat jednu z následujících možností:

- **Vlastní hostování** výsledného portálu mimo vaši službu API Management. Po samoobslužném hostování portálu se stanete jeho údržbou a zodpovídáte za jeho upgrady. Pomocná podpora Azure je omezená jenom na [základní nastavení portálů pro samoobslužné hostování](developer-portal-self-host.md).
- Otevřete žádost o přijetí změn pro tým API Management, aby se do základu kódu **spravovaného** portálu sloučila nová funkce.

Podrobnosti a pokyny k rozšíření najdete v [úložišti GitHubu](https://github.com/Azure/api-management-developer-portal) a v kurzu [implementace widgetu](developer-portal-implement-widgets.md). Kurz pro [přizpůsobení spravovaného portálu](api-management-howto-developer-portal-customize.md) vás provede pomocí panelu pro správu portálu, který je běžně pro **spravované** a **samoobslužné** verze.


## <a name="next-steps"></a>Další kroky

Další informace o novém portálu pro vývojáře:

- [Přístup k portálu spravovaného vývojáře a jeho přizpůsobení](api-management-howto-developer-portal-customize.md)
- [Nastavení samoobslužné verze portálu](developer-portal-self-host.md)
- [Implementace vlastního widgetu](developer-portal-implement-widgets.md)

Procházet Další prostředky:

- [Úložiště GitHub se zdrojovým kódem](https://github.com/Azure/api-management-developer-portal)
- [Nejčastější dotazy týkající se portálu pro vývojáře](developer-portal-faq.md)
