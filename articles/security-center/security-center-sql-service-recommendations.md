---
title: Doporučení pro úložiště & dat – Azure Security Center
description: Tento dokument řeší doporučení v Azure Security Center, které vám pomohou chránit vaše data a služby Azure SQL a zůstat v souladu se zásadami zabezpečení.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 74ed55e1d460495bfa8d3d4c00bd37bb7f05260e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552861"
---
# <a name="protect-azure-data-and-storage-services"></a>Ochrana dat a služeb úložiště Azure
Když Azure Security Center identifikuje potenciální slabá místa zabezpečení, vytvoří doporučení, která vás provedou procesem konfigurace potřebných ovládacích prvků pro posílení a ochranu vašich prostředků.

Tento článek vysvětluje **stránku Zabezpečení dat v** části zabezpečení prostředků v Centru zabezpečení.

Úplný seznam doporučení, která se mohou zobrazit na této stránce, najdete v [tématu Doporučení pro data a úložiště](recommendations-reference.md#recs-datastorage).


## <a name="view-your-data-security-information"></a>Zobrazení informací o zabezpečení dat

1. V části **Hygiena zabezpečení prostředků** klikněte na **položku Data and storage resources**.

    ![Prostředky úložiště & dat](./media/security-center-monitoring/click-data.png)

    Otevře se stránka **Zabezpečení dat** s doporučeními pro datové prostředky.

    [![Datové prostředky](./media/security-center-monitoring/sql-overview.png)](./media/security-center-monitoring/sql-overview.png#lightbox)

    Na této stránce můžete provést následující:

    * Klikněte na kartu **Přehled** obsahuje seznam všech doporučení datových zdrojů, která mají být opravena. 
    * Klikněte na jednotlivé karty a zobrazte doporučení podle typu zdroje.

    > [!NOTE]
    > Další informace o šifrování úložiště najdete v [tématu šifrování Azure Storage pro data v klidovém stavu](../storage/common/storage-service-encryption.md).


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Náprava doporučení pro datový prostředek

1. Na kterékoli z karet zdrojů klikněte na zdroj. Otevře se informační stránka s doporučeními, která mají být opravena.

    ![Informace o zdrojích](./media/security-center-monitoring/sql-recommendations.png)

2. Klikněte na doporučení. Otevře se stránka Doporučení a zobrazí **kroky nápravy** k implementaci doporučení.

   ![Kroky nápravy](./media/security-center-monitoring/remediate1.png)

3. Klikněte na **Provést akci**. Zobrazí se stránka nastavení prostředků.

    ![Povolit doporučení](./media/security-center-monitoring/remediate2.png)

4. Postupujte podle **kroků nápravy** a klepněte na tlačítko **Uložit**.


## <a name="next-steps"></a>Další kroky

Další informace o doporučeních, která platí pro jiné typy prostředků Azure, najdete v následujících tématech:

* [Úplný referenční seznam doporučení zabezpečení Azure Security Center](recommendations-reference.md)
* [Ochrana počítačů a aplikací ve službě Azure Security Center](security-center-virtual-machine-protection.md)
* [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)