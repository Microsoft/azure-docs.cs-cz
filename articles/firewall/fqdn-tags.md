---
title: Přehled značek plně kvalifikovaný název domény pro Brána Firewall služby Azure
description: Další informace o značkách plně kvalifikovaný název domény v Brána Firewall služby Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 4/22/2019
ms.author: victorh
ms.openlocfilehash: 740b0ac505edfff1f703c2831ec5608e72851610
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149763"
---
# <a name="fqdn-tags-overview"></a>Přehled značek plně kvalifikovaný název domény

Značku plně kvalifikovaný název domény představuje skupinu plně kvalifikované názvy domény (FQDN) přidružené k dobře známým službám Microsoft. V pravidlech aplikace můžete použít značku plně kvalifikovaný název domény Pokud chcete povolit požadované odchozí síťové přenosy přes bránu firewall.

Například pokud chcete ručně povolit aktualizace Windows síťové přenosy přes bránu firewall, musíte vytvořit víc pravidel aplikace za dokumentaci společnosti Microsoft. Pomocí značek plně kvalifikovaný název domény, můžete vytvořit pravidlo aplikace, zahrňte **aktualizací Windows** označit a nyní síťového provozu do koncových bodů může probíhat přes bránu firewall Microsoft Windows Update.

Nelze vytvořit vlastní značky plně kvalifikovaný název domény, ani můžete můžete určit, které jsou ve značce zahrnuté plně kvalifikovaných názvů domény. Microsoft spravuje plně kvalifikované názvy domény zahrnuté ve značce plně kvalifikovaný název domény a aktualizuje označit jako změnu plně kvalifikovaných názvů domény. 

<!--- screenshot of application rule with a FQDN tag.-->

V následující tabulce jsou uvedeny aktuální značky plně kvalifikovaný název domény, která vám pomůže. Společnost Microsoft udržuje tyto značky a můžete očekávat, že k příležitostně přidávat další značky.

## <a name="current-fqdn-tags"></a>Aktuální značky plně kvalifikovaný název domény

|Plně kvalifikovaný název domény značky  |Popis  |
|---------|---------|
|Windows Update     |Povolí odchozí přístup ke službě Microsoft Update, jak je popsáno v [konfigurace brány Firewall pro aktualizace softwaru](https://technet.microsoft.com/library/bb693717.aspx).|
|Program Diagnostika|Povolí odchozí přístup ke všem [koncových bodů Windows diagnostiky](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Microsoft Active Protection Service (MAPS)|Povolí odchozí přístup k [MAPY](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|App Service Environment (ASE)|Povolí odchozí přístup k platformě provoz služby ASE. Tato značka nezahrnuje zákaznické úložiště a SQL koncové body vytvořené ve službě ASE. Ty by měla být povolená přes [koncové body služby](../virtual-network/tutorial-restrict-network-access-to-resources.md) nebo přidat ručně.<br><br>Další informace o integraci se službou ASE Brána Firewall služby Azure najdete v tématu [používat jenom služby App Service Environment](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Azure Backup|Povolí odchozí přístup ke službám Azure Backup.|
|Azure HDInsight<br>(Preview)|Povolí odchozí přístup pro provoz platformě HDInsight. Tato značka nezahrnuje zákaznické úložiště nebo SQL provoz z HDInsight. Tyto akce jsou povolené prostřednictvím [koncové body služby](../virtual-network/tutorial-restrict-network-access-to-resources.md) nebo přidat ručně.|

> [!NOTE]
> Při výběru značek plně kvalifikovaný název domény v pravidle aplikace, portu protokolu: pole musí být nastavené na **https**.

## <a name="next-steps"></a>Další postup

Zjistěte, jak nasadit Azure, najdete v článku [kurzu: Nasazení a konfiguraci brány Firewall Azure pomocí webu Azure portal](tutorial-firewall-deploy-portal.md).