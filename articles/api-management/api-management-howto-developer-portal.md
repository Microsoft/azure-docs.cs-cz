---
title: Přístup a přizpůsobení nového portálu pro vývojáře – Azure API Management | Microsoft Docs
description: Naučte se používat nový portál pro vývojáře v API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/12/2019
ms.author: apimpm
ms.openlocfilehash: da75ca43a2576e3214d4b67f9eb61c7bad3bd5cc
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073515"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>Přístup k novému portálu pro vývojáře v Azure API Management a jeho přizpůsobení

V tomto článku se dozvíte, jak získat přístup k novému portálu pro vývojáře Azure API Management. Provede vás pomocí prostředí vizuálního editoru – přidávání a úpravy obsahu a také přizpůsobení vzhledu webu.

![Nový portál pro vývojáře API Management](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a> Požadavky

- Dokončete následující rychlý Start: [Vytvořte instanci služby Azure API Management](get-started-create-service-instance.md).
- Import a publikování instance služby Azure API Management. Další informace najdete v tématu [Import a publikování](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> Nový portál pro vývojáře je aktuálně ve verzi Preview.

## <a name="managed-vs-self-hosted"></a>Spravované a samostatně hostované verze

Portál pro vývojáře můžete vytvořit dvěma způsoby:

- **Spravovaná verze** – úpravou a přizpůsobením portálu, který je součástí vaší instance API Management a je přístupný prostřednictvím adresy URL `<your-api-management-instance-name>.developer.azure-api.net`.
- **Samoobslužná verze** – nasazením a vlastním hostováním portálu mimo instanci API Management. Tento přístup umožňuje upravit základ kódu na portálu a zvětšit poskytované základní funkce. Podrobnosti a pokyny najdete v [úložišti GitHub se zdrojovým kódem portálu][1].

## <a name="managed-access"></a>Přístup ke spravované verzi portálu

Použijte následující postup, chcete-li získat přístup ke spravované verzi portálu.

1. V Azure Portal přejít na instanci služby API Management.
1. V horním navigačním panelu klikněte na tlačítko **nový portál pro vývojáře (Preview)** . Otevře se nová karta prohlížeče s verzí pro správu portálu. Pokud k portálu přistupujete poprvé, bude automaticky zřízen výchozí obsah.

## <a name="managed-tutorial"></a>Úprava a přizpůsobení spravované verze portálu

V níže uvedeném videu jsme ukázali, jak upravit obsah portálu, přizpůsobit vzhled webu a publikovat změny.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="faq"></a>Nejčastější dotazy

V této části odpovíme na běžné otázky týkající se nového portálu pro vývojáře, který má obecnou povahu. Otázky specifické pro samostatnou verzi najdete [v části wiki v úložišti GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-content-from-the-old-developer-portal-to-the-new-one"></a>Jak můžu migrovat obsah z původního portálu pro vývojáře na nový?

Nemůžete. Portály jsou nekompatibilní.

### <a name="when-will-the-portal-become-generally-available"></a>Kdy bude portál všeobecně dostupný?

Portál je momentálně ve verzi Preview a bude obecně dostupný na konci kalendářního roku (2019). Verze Preview by se neměla používat pro produkční účely.

### <a name="will-the-old-portal-be-deprecated"></a>Bude starý portál zastaralý?

Ano, tato akce bude zastaralá, jakmile bude nová verze všeobecně dostupná. Pokud máte obavy, vyvolejte je [ve vyhrazeném problému GitHubu](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>Má nový portál všechny funkce starého portálu?

Cílem obecné dostupnosti je poskytnout na starém portálu paritu funkcí založenou na scénářích. V takovém případě nemusí být ve verzi Preview vybrané implementované funkce.

Výjimkou jsou *aplikace* a *problémy* z původního portálu, které na novém portálu nebudou k dispozici. Pokud ve starém portálu používáte *problémy* a potřebujete je v novém, pošlete komentář do [vyhrazeného problému GitHubu](https://github.com/Azure/api-management-developer-portal/issues/122).

### <a name="ive-found-bugs-andor-id-like-to-request-a-feature"></a>Zjistili jsem chyby nebo jsem chtěl požádat o funkci.

Výborně! Můžete nám poskytnout zpětnou vazbu, odeslat žádost o funkci nebo zaslat zprávu o chybě prostřednictvím [oddílu s úložištěm GitHubu](https://github.com/Azure/api-management-developer-portal/issues). I když tam máte, vážíme si také vašich připomínek k problémům označeným `community` popiskem.

### <a name="i-want-to-move-the-content-of-the-new-portal-between-environments-how-can-i-do-that-and-do-i-need-to-go-with-the-self-hosted-version"></a>Chci přesunout obsah nového portálu mezi prostředími. Jak to můžu udělat a potřebuji, abyste se mohli dostat k místní verzi?

Můžete tak učinit v obou verzích portálu spravovaných i v místním prostředí. Nový portál pro vývojáře podporuje extrakci obsahu prostřednictvím rozhraní API pro správu vaší API Management služby. Rozhraní API jsou zdokumentována [v části wiki úložiště GitHub](https://github.com/Azure/api-management-developer-portal/wiki/). Napsali jsme také [skript](https://github.com/Azure/api-management-developer-portal/blob/master/scripts/migrate.bat), který vám může pomáhat začít.

Pořád pracujeme na tom, jak tento proces zarovnává se sadou API Management DevOps Resource Kit.

### <a name="how-can-i-select-a-layout-when-creating-a-new-page"></a>Jak můžu vybrat *rozložení* při vytváření nové *stránky*?

*Rozložení* se použije na stránku podle odpovídající šablony URL k adrese URL *stránky* . Například *rozložení* s `/wiki/*` šablonou URL má být použito na `/wiki/` každou *stránku* s segmentem: `/wiki/getting-started`, `/wiki/styles`a tak dále.

### <a name="why-doesnt-the-interactive-developer-console-work"></a>Proč nefunguje interaktivní Konzola pro vývojáře?

Je nejspíš v souvislosti s CORS. Interaktivní konzola vytvoří požadavek rozhraní API na straně klienta z prohlížeče. Problém CORS můžete vyřešit tak, že do svých rozhraní API přidáte [zásadu CORS](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS) . Všechny parametry můžete zadat ručně (například jako zdroj jako https://contoso.com) nebo použít zástupnou `*` hodnotu).

## <a name="next-steps"></a>Další postup

Další informace o novém portálu pro vývojáře:

- [Úložiště GitHub se zdrojovým kódem][1]
- [Pokyny pro samoobslužné hostování portálu][2]
- [Veřejný plán projektu][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects