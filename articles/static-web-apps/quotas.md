---
title: Kvóty ve službě Azure static Web Apps Preview
description: Další informace o kvótách přidružených ke službě Azure static Web Apps Preview
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 6fa70eba5875c32d41fa1cfc8daf9b1cdf8f19df
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "83599989"
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
| Velikost aplikace                    | 100 MB           |
| Předprodukční prostředí | 1                |
| Vlastní domény              | 1                |
| Autorizace<br><br>S vlastními rolemi a pravidly směrování | Max. 25 pozvaných a přiřazených rolí koncovým uživatelům |
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
