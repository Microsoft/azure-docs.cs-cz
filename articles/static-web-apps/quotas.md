---
title: Kvóty ve službě Azure static Web Apps Preview
description: Další informace o kvótách přidružených ke službě Azure static Web Apps Preview
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: e3538e90a6dea69c703f56871fde86a18557a022
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106095165"
---
# <a name="quotas-in-azure-static-web-apps-preview"></a>Kvóty ve službě Azure static Web Apps Preview

Pro Azure static Web Apps ve verzi Preview existují následující kvóty.

> [!IMPORTANT]
> Statická Web Appsa Azure je ve verzi Public Preview a není určená pro použití v produkčním prostředí.

| Funkce                     | Bezplatný plán        |
|-----------------------------|------------------|
| Zahrnutá šířka pásma          | 100 GB za měsíc |
| Nadlimitní šířka pásma           | Neaktivní      |
| Aplikace na předplatné Azure | 10               |
| Velikost aplikace                    | 250 MB           |
| Předprodukční prostředí | 3                |
| Vlastní domény              | 1                |
| Autorizace (s vlastními rolemi a pravidly směrování) | Maximálně 25 koncových uživatelů, kteří můžou patřit do vlastních rolí |
| Azure Functions             | K dispozici        |
| SLA                         | Žádné             |

## <a name="github-storage"></a>Úložiště GitHub

Akce a balíčky GitHubu používají úložiště GitHub s vlastní sadou kvót. Když vytvoříte web Azure static Web Apps, GitHub uloží artefakty pro vaši lokalitu i po nasazení.

Další podrobnosti najdete v následujících zdrojích informací:

- [Správa akcí v prostoru úložiště](https://github.community/t5/GitHub-Actions/Managing-Actions-storage-space/td-p/38944)
- [O fakturaci akcí GitHubu](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/about-billing-for-github-actions#about-billing-for-github-actions)
- [Správa limitu útraty pro akce GitHubu](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/managing-your-spending-limit-for-github-actions)

## <a name="next-steps"></a>Další kroky

- [Přehled](overview.md)
