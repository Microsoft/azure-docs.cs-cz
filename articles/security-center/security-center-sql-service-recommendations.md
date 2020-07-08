---
title: Doporučení pro úložiště dat & – Azure Security Center
description: Tento dokument popisuje doporučení v Azure Security Center, která vám pomůžou chránit vaše data a službu Azure SQL a zůstat v souladu se zásadami zabezpečení.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "75552861"
---
# <a name="protect-azure-data-and-storage-services"></a>Ochrana Azure data a služeb úložiště
Když Azure Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří doporučení, která vás provedou procesem konfigurace potřebných ovládacích prvků k posílení a ochraně vašich prostředků.

Tento článek vysvětluje **stránku zabezpečení dat** v části Security Center zabezpečení prostředků v tématu.

Úplný seznam doporučení, která se vám můžou zobrazit na této stránce, najdete v tématu [doporučení pro data a úložiště](recommendations-reference.md#recs-datastorage).


## <a name="view-your-data-security-information"></a>Zobrazit informace o zabezpečení dat

1. V části **hygiena zabezpečení prostředků** klikněte na **data a prostředky úložiště**.

    ![Prostředky úložiště dat &](./media/security-center-monitoring/click-data.png)

    Otevře se stránka **zabezpečení dat** s doporučeními pro datové prostředky.

    [![Datové prostředky](./media/security-center-monitoring/sql-overview.png)](./media/security-center-monitoring/sql-overview.png#lightbox)

    Na této stránce můžete provést následující:

    * Klikněte na kartu **Přehled** se seznamem všech doporučení k datovým prostředkům, která se mají opravit. 
    * Klikněte na jednotlivé karty a Prohlédněte si doporučení podle typu prostředku.

    > [!NOTE]
    > Další informace o šifrování úložiště najdete v tématu [Azure Storage Encryption for data v klidovém umístění](../storage/common/storage-service-encryption.md).


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Náprava doporučení pro datový prostředek

1. Ze všech karet prostředků klikněte na prostředek. Otevře se stránka s informacemi o doporučeních, která se mají opravit.

    ![Informace o prostředku](./media/security-center-monitoring/sql-recommendations.png)

2. Klikněte na doporučení. Otevře se stránka s doporučením a zobrazí se **kroky pro nápravu** , které implementují doporučení.

   ![Postup odstranění problému](./media/security-center-monitoring/remediate1.png)

3. Klikněte na **provést akci**. Zobrazí se stránka nastavení prostředků.

    ![Povolit doporučení](./media/security-center-monitoring/remediate2.png)

4. Postupujte podle **kroků pro nápravu** a klikněte na **Uložit**.


## <a name="next-steps"></a>Další kroky

Další informace o doporučeních, která se vztahují na jiné typy prostředků Azure, najdete v následujících tématech:

* [Úplný seznam odkazů na doporučení zabezpečení Azure Security Center](recommendations-reference.md)
* [Ochrana počítačů a aplikací ve službě Azure Security Center](security-center-virtual-machine-protection.md)
* [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)