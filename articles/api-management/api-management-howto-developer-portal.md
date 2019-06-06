---
title: Používat a přizpůsobovat na novém portálu pro vývojáře – Azure API Management | Dokumentace Microsoftu
description: Naučte se používat nový portál pro vývojáře ve službě API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2019
ms.author: apimpm
ms.openlocfilehash: deb3fdf3069aaad4982d71806c209fe516e3c3d6
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743572"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>Používat a přizpůsobovat nový portál pro vývojáře ve službě Azure API Management

V tomto článku se dozvíte, jak získat přístup k nový portál pro vývojáře Azure API Management. Provede vás prostřednictvím prostředí vizuální editor – přidání a úpravy obsahu – i přizpůsobení vzhledu webu.

![Nový portál pro vývojáře API Management](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a>Požadavky

- Projděte si následující rychlý start: [Vytvoření instance Azure API Management](get-started-create-service-instance.md).
- Import a publikování instance Azure API Management. Další informace najdete v tématu [Import a publikování](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> Nový portál pro vývojáře je aktuálně ve verzi preview.

## <a name="managed-and-self-hosted-versions"></a>Verze spravované a v místním prostředí

Můžete vytvářet portálu pro vývojáře dvěma způsoby:

- **Spravovaná verze** – úpravy a přizpůsobení portálu integrovaná do vaší instance služby API Management a přístupné přes adresu URL `<your-api-management-instance-name>.developer.azure-api.net`.
- **Verze v místním prostředí** – nasazení a hostování na vlastním portálu mimo instance služby API Management. Tento přístup umožňuje upravit na portálu základu kódu a rozšířit zadaná základní funkce. Podrobnosti a pokyny najdete [úložiště GitHub se zdrojovým kódem portálu][1].

## <a name="access-the-managed-version-of-the-portal"></a>Přístup ke spravované verze portálu

Podle následujících pokynů pro přístup k spravovaná verze na portálu.

1. Přejděte k vaší instanci služby API Management na webu Azure Portal.
1. Klikněte na **nový portál pro vývojáře (Náhled)** tlačítko v horním navigačním panelu. Otevře se nová karta prohlížeče správy verzí portálu. Pokud získáváte přístup k portálu poprvé, bude možné automaticky zřizovat výchozí obsah.

## <a name="edit-and-customize-the-managed-version-of-the-portal"></a>Úpravy a přizpůsobení spravovaná verze na portálu

Ve videu níže vám ukážeme, jak upravovat obsah na portálu, přizpůsobit vzhled webu a publikovat změny.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="next-steps"></a>Další postup

Další informace o novém portálu pro vývojáře:

- [Úložiště GitHub se zdrojovým kódem][1]
- [Pokyny k hostování na vlastním portálu][2]
- [Veřejný plán projektu][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects