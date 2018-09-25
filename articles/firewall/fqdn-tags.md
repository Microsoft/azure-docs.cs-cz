---
title: Přehled značek plně kvalifikovaný název domény pro Brána Firewall služby Azure
description: Další informace o značkách plně kvalifikovaný název domény v Brána Firewall služby Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 536c0915cae17aa6f4201c62eae5f5b077805274
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46999473"
---
# <a name="fqdn-tags-overview"></a>Přehled značek plně kvalifikovaný název domény

Plně kvalifikovaný název domény značka představuje skupinu plně kvalifikované názvy domény (FQDN) přidružené k dobře známým službám Microsoft. Značka plně kvalifikovaný název domény můžete použít v pravidlech aplikace Pokud chcete povolit požadované odchozí síťové přenosy přes bránu firewall.

>[!NOTE]
>Funkce značky plně kvalifikovaný název domény je pouze aktuálně dostupné v Azure Powershellu a REST.

Například pokud chcete ručně povolit aktualizace Windows síťové přenosy přes bránu firewall, musíte vytvořit víc pravidel aplikace za dokumentaci společnosti Microsoft. Pomocí značek plně kvalifikovaný název domény, můžete vytvořit pravidlo aplikace, zahrňte **aktualizací Windows** označit a nyní síťového provozu do koncových bodů může probíhat přes bránu firewall Microsoft Windows Update.

Nelze vytvořit vlastní značky plně kvalifikovaný název domény, ani můžete můžete určit, které jsou ve značce zahrnuté plně kvalifikovaných názvů domény. Microsoft spravuje plně kvalifikované názvy domény zahrnuté ve značce plně kvalifikovaný název domény a aktualizuje označit jako změnu plně kvalifikovaných názvů domény. 

<!--- screenshot of application rule with a FQDN tag.-->

V následující tabulce jsou uvedeny aktuální značky plně kvalifikovaný název domény, která vám pomůže. Společnost Microsoft udržuje tyto značky a můžete očekávat, že k příležitostně přidávat další značky.

|Plně kvalifikovaný název domény značky  |Popis  |
|---------|---------|
|Windows Update     |Povolí odchozí přístup ke službě Microsoft Update, jak je popsáno v [konfigurace brány Firewall pro aktualizace softwaru](https://technet.microsoft.com/library/bb693717.aspx).|
|Program Diagnostika|Povolí odchozí přístup ke všem [koncových bodů Windows diagnostiky](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Microsoft Active Protection Service (MAPS)|Povolí odchozí přístup k [MAPY](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|App Service Environment (ASE)|Povolí odchozí přístup k platformě provoz služby ASE. Tato značka nezahrnuje zákaznické úložiště a SQL koncové body vytvořené ve službě ASE. Ty by měla být povolená přes [koncové body služby](../virtual-network/tutorial-restrict-network-access-to-resources.md) nebo přidat ručně.|
|Azure Backup|Povolí odchozí přístup ke službám Azure Backup.

## <a name="next-steps"></a>Další postup

Zjistěte, jak nasadit Azure, najdete v článku [kurz: nasazení a konfiguraci brány Firewall Azure pomocí webu Azure portal](tutorial-firewall-deploy-portal.md).