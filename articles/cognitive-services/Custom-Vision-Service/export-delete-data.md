---
title: Export nebo odstranit data v vlastní vize, kognitivní služeb Azure | Microsoft Docs
description: Zjistěte, jak k exportu nebo odstranit data v vize vlastní.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/25/2018
ms.author: v-jaswel
ms.openlocfilehash: 49fc49b3181f56c23167cfbae18911e7db441f8c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343697"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Export nebo odstranit uživatelská data ve vlastní vize

Obsahu moderátora shromažďuje data uživatele pro provoz služby, ale zákazníci máte plnou kontrolu nad zobrazení nebo odstranění svá data pomocí služby vize vlastní [školení API](https://go.microsoft.com/fwlink/?linkid=865446).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Další informace o tom, jak exportovat a odstranit uživatelská data ve vlastní vize najdete v následující tabulce.

| Data | Operace exportu | Operace odstranění |
| ---- | ---------------- | ---------------- |
| Informace o účtu (předplatné klíče) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Odstraňte pomocí portálu Azure (předplatných Azure). Nebo pomocí tlačítka "Odstranit váš účet" na stránce nastavení CustomVision.ai (předplatné účet Microsoft) |
| Podrobnosti o iterace | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Detaily výkonu iterace | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Seznam iterací | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projekty a podrobností projektu | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) a [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) |
| Obrázek značky | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) a [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) |
| Image | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (poskytuje identifikátor uri pro stažení bitové kopie) a [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (poskytuje identifikátor uri pro stažení bitové kopie) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) |
| Exportovaný modely | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Odstranit po odstranění účtu |
