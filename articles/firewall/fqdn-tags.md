---
title: Přehled značek plně kvalifikovaný název domény pro Brána Firewall služby Azure
description: Další informace o značkách plně kvalifikovaný název domény v Brána Firewall služby Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/1/2018
ms.author: victorh
ms.openlocfilehash: 897ea3856516b5429ffb770164f863d71e7ae0dd
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419006"
---
# <a name="fqdn-tags-overview"></a>Přehled značek plně kvalifikovaný název domény

Značku plně kvalifikovaný název domény představuje skupinu plně kvalifikované názvy domény (FQDN) přidružené k dobře známým službám Microsoft. V pravidlech aplikace můžete použít značku plně kvalifikovaný název domény Pokud chcete povolit požadované odchozí síťové přenosy přes bránu firewall.

Například pokud chcete ručně povolit aktualizace Windows síťové přenosy přes bránu firewall, musíte vytvořit víc pravidel aplikace za dokumentaci společnosti Microsoft. Pomocí značek plně kvalifikovaný název domény, můžete vytvořit pravidlo aplikace, zahrňte **aktualizací Windows** označit a nyní síťového provozu do koncových bodů může probíhat přes bránu firewall Microsoft Windows Update.

Nelze vytvořit vlastní značky plně kvalifikovaný název domény, ani můžete můžete určit, které jsou ve značce zahrnuté plně kvalifikovaných názvů domény. Microsoft spravuje plně kvalifikované názvy domény zahrnuté ve značce plně kvalifikovaný název domény a aktualizuje označit jako změnu plně kvalifikovaných názvů domény. 

<!--- screenshot of application rule with a FQDN tag.-->

V následující tabulce jsou uvedeny aktuální značky plně kvalifikovaný název domény, která vám pomůže. Společnost Microsoft udržuje tyto značky a můžete očekávat, že k příležitostně přidávat další značky.

|Plně kvalifikovaný název domény značky  |Popis  |
|---------|---------|
|Windows Update     |Povolí odchozí přístup ke službě Microsoft Update, jak je popsáno v [konfigurace brány Firewall pro aktualizace softwaru](https://technet.microsoft.com/library/bb693717.aspx).|
|Program Diagnostika|Povolí odchozí přístup ke všem [koncových bodů Windows diagnostiky](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Microsoft Active Protection Service (MAPS)|Povolí odchozí přístup k [MAPY](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|App Service Environment (ASE)|Povolí odchozí přístup k platformě provoz služby ASE. Tato značka nezahrnuje zákaznické úložiště a SQL koncové body vytvořené ve službě ASE. Ty by měla být povolená přes [koncové body služby](../virtual-network/tutorial-restrict-network-access-to-resources.md) nebo přidat ručně.<br><br>Další informace o integraci se službou ASE Brána Firewall služby Azure najdete v tématu [používat jenom služby App Service Environment](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Azure Backup|Povolí odchozí přístup ke službám Azure Backup.

> [!NOTE]
> Při výběru značek plně kvalifikovaný název domény v pravidle aplikace, portu protokolu: pole musí být nastavené na **https**.

## <a name="next-steps"></a>Další postup

Zjistěte, jak nasadit Azure, najdete v článku [kurz: nasazení a konfiguraci brány Firewall Azure pomocí webu Azure portal](tutorial-firewall-deploy-portal.md).