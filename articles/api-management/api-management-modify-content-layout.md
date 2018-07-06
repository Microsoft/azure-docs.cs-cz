---
title: Úprava obsahu stránek na portálu pro vývojáře ve službě Azure API Management | Dokumentace Microsoftu
description: Naučte se upravovat obsah stránek na portálu pro vývojáře ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: vlvinogr
editor: ''
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/09/2017
ms.author: vlvinogr
ms.openlocfilehash: 4a372b54ace87816b9e7cccbd38466331b5ebfc7
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437592"
---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>Úprava obsahu a rozložení stránek na portálu pro vývojáře ve službě Azure API Management
Existují tři základní způsoby, kterými jde přizpůsobit portál pro vývojáře ve službě Azure API Management:

* [Úprava obsahu statických stránek a elementů rozložení stránek][modify-content-layout] (vysvětlení obsahuje tento průvodce)
* [Aktualizace stylů použitých pro elementy stránek napříč portálem pro vývojáře][customize-styles]
* [Úprava šablon použitých pro stránky generované portálem][portal-templates] (například dokumentace rozhraní API, produkty, ověřování uživatelů atd.)

## <a name="page-structure"></a>Struktura stránek portálu pro vývojáře

Portál pro vývojáře je založený na systému CMS (Content Management System). Rozložení jednotlivých stránek je založené na základě sady malých elementů stránek, které se označují jako widgety:

![Struktura stránek portálu pro vývojáře][api-management-customization-widget-structure]

Všechny widgety se dají upravovat.
* Základní obsah specifický pro každou jednotlivou stránku se nachází ve widgetu Contents. Úprava stránky znamená úpravy obsahu tohoto widgetu.
* Všechny prvky rozložení stránky jsou obsažené v ostatních widgetech. Změny provedené v těchto widgetech se použijí pro všechny stránky. Označujeme je jako widgety rozložení.

Při běžných úpravách stránek se často mění jenom widget Content, který má pro každou jednotlivou stránku jiný obsah.

## <a name="modify-layout-widget"></a>Změně obsahu widgetu rozložení

Portál pro vývojáře je přístupný z webu Azure Portal.

1. Klikněte na **Portál pro vývojáře** na panelu nástrojů vaší instance služby API Management.
2. Pokud chcete upravit obsah widgetů, klikněte na ikonu se dvěma štětci v nabídce portálu **Vývojář** na levé straně.
3. Pokud chcete upravit obsah záhlaví, v seznamu na levé straně se posuňte do části **Záhlaví**.

    Widgety můžete upravovat v rámci polí.
4. Až budete připraveni publikovat provedené změny, klikněte na **Publikovat** v dolní části stránky.

Nové záhlaví by se teď mělo zobrazovat na každé stránce portálu pro vývojáře.

## <a name="next-steps"></a>Další kroky
* [Aktualizace stylů použitých pro elementy stránek napříč portálem pro vývojáře][customize-styles]
* [Úprava šablon použitých pro stránky generované portálem][portal-templates] (například dokumentace rozhraní API, produkty, ověřování uživatelů atd.)

[Structure of developer portal pages]: #page-structure
[Modifying the contents of a layout widget]: #modify-layout-widget
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customization-widget-structure]: ./media/api-management-modify-content-layout/portal-widget-structure.png
[api-management-management-console]: ./media/api-management-modify-content-layout/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-modify-content-layout/api-management-widgets-header.png
[api-management-customization-manage-content]: ./media/api-management-modify-content-layout/api-management-customization-manage-content.png
