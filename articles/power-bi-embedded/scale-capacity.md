---
title: Škálovat vaše Power BI Embedded kapacita | Microsoft Docs
description: Tento článek vás provede postup škálování Power BI Embedded kapacity v Microsoft Azure.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/19/2018
ms.author: maghan
ms.openlocfilehash: a089d34b3f49188f3037ad57aab4d7bcc8c660f2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31408281"
---
# <a name="scale-your-power-bi-embedded-capacity"></a>Škálovat vaše Power BI Embedded kapacita

Tento článek vás provede postup škálování Power BI Embedded kapacity v Microsoft Azure. Škálování můžete zvýšit nebo snížit velikost vašeho kapacity.

Předpokladem je, že jste vytvořili Power BI Embedded kapacity. Pokud máte není, najdete v části [vytvořit Power BI na Embedded kapacity na portálu Azure](create-capacity.md) začít pracovat.

> [!NOTE]
> Škálování operace může trvat o několik minut. Během této doby nebudou dostupné kapacity. Vložený obsah se pravděpodobně nepodaří načíst.

## <a name="scale-a-capacity"></a>Škálování kapacitu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Vyberte **všechny služby** > **Power BI Embedded** zobrazíte vaší kapacity.

    ![Všechny služby v rámci portálu Azure](media/scale-capacity/azure-portal-more-services.png)

3. Vyberte kapacitu, kterou chcete škálovat.

    ![Power BI Embedded kapacity seznamu v rámci portálu Azure](media/scale-capacity/azure-portal-capacity-list.png)

4. Vyberte **cenová úroveň** pod **škálování** v rámci vaší kapacity.

    ![Cenová úroveň možnosti v části škálování](media/scale-capacity/azure-portal-scale-pricing-tier.png)

    Aktuální cenová úroveň je označen modře.

    ![Aktuální cenová úroveň uvedených v modrá](media/scale-capacity/azure-portal-current-tier.png)

5. Chcete-li škálovat nahoru nebo dolů, vyberte novou vrstvu přesunout do. Výběrem novou vrstvu umístí přerušovanou modrý obrys ve výběru. Vyberte **vyberte** škálování do nové vrstvy.

    ![Vyberte novou vrstvu](media/scale-capacity/azure-portal-select-new-tier.png)

    Škálování vaše kapacita může trvat minutu nebo dvě k dokončení.

6. Zkontrolujte vaše vrstvy zobrazením karta Přehled. Aktuální cenová úroveň je uveden.

    ![Potvrďte aktuální úroveň](media/scale-capacity/azure-portal-confirm-tier.png)

## <a name="next-steps"></a>Další postup

Pozastavení nebo spustit vaše kapacita, najdete v části [pozastavit a spusťte vaše Power BI Embedded kapacita v portálu Azure](pause-start.md).

Pokud chcete začít, vkládání obsah Power BI v rámci vaší aplikace, najdete v části [postup vložení vaše řídicí panely Power BI, sestavy a dlaždice](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Chcete se ještě na něco zeptat? [Zkuste požádat komunitě Power BI](http://community.powerbi.com/)
