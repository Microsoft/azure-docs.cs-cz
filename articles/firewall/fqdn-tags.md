---
title: Přehled značek FQDN pro Azure Firewall
description: Značka Plně kvalifikovaný název domény představuje skupinu plně kvalifikovaných názvů domén (FQDN) přidružených ke známým službám společnosti Microsoft.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 6396f8292a4c54f7fce237439f37c3e8156d59e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74169037"
---
# <a name="fqdn-tags-overview"></a>Přehled značek FQDN

Značka Plně kvalifikovaný název domény představuje skupinu plně kvalifikovaných názvů domén (FQDN) přidružených ke známým službám společnosti Microsoft. Značku FQDN můžete použít v pravidlech aplikace a povolit požadovaný odchozí síťový provoz přes bránu firewall.

Chcete-li například ručně povolit síťový provoz služby Windows Update prostřednictvím brány firewall, je třeba vytvořit více pravidel aplikace podle dokumentace společnosti Microsoft. Pomocí značek FQDN můžete vytvořit pravidlo aplikace, zahrnout značku **Aktualizace systému Windows** a nyní může síťový provoz koncových bodů služby Microsoft Windows Update protékat bránou firewall.

Nemůžete vytvořit vlastní značky VYVesten, ani nemůžete určit, které soubory ReQDN jsou zahrnuty do značky. Společnost Microsoft spravuje hlavní soubory kvedu, které jsou zahrnuty ve značce FQDN, a aktualizuje ji při změně v oblasti fqdn. 

<!--- screenshot of application rule with a FQDN tag.-->

V následující tabulce jsou uvedeny aktuální značky vykreslování k vykazování, které můžete použít. Společnost Microsoft tyto značky spravuje a můžete očekávat, že budou pravidelně přidávány další značky.

## <a name="current-fqdn-tags"></a>Aktuální značky FQDN

|Značka FQDN  |Popis  |
|---------|---------|
|Windows Update     |Povolit odchozí přístup k webu Microsoft Update, jak je popsáno v [části Konfigurace brány firewall pro aktualizace softwaru](https://technet.microsoft.com/library/bb693717.aspx).|
|Program Diagnostika|Povolit odchozí přístup ke všem [koncovým bodům diagnostiky systému Windows](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Služba Microsoft Active Protection Service (MAPS)|Povolit odchozí přístup ke [službě MAPS](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|Prostředí služby App Service (ASE)|Umožňuje odchozí přístup k provozu platformy ASE. Tato značka nezahrnuje koncové body úložiště specifické pro zákazníka a koncové body SQL vytvořené službou ASE. Ty by měly být povoleny prostřednictvím [koncových bodů služby](../virtual-network/tutorial-restrict-network-access-to-resources.md) nebo přidány ručně.<br><br>Další informace o integraci brány Azure Firewall se službou ASE najdete v [tématu Uzamčení prostředí služby App Service](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Azure Backup|Umožňuje odchozí přístup ke službám Azure Backup.|
|Azure HDInsight|Umožňuje odchozí přístup pro provoz na platformě HDInsight. Tato značka se nevztahuje na úložiště specifické pro zákazníka nebo sql provoz z HDInsight. Povolte tyto pomocí [koncových bodů služby](../virtual-network/tutorial-restrict-network-access-to-resources.md) nebo je přidejte ručně.|

> [!NOTE]
> Při výběru značky FQDN v pravidle aplikace musí být pole protocol:port nastaveno na **https**.

## <a name="next-steps"></a>Další kroky

Informace o nasazení brány Azure Firewall najdete [v tématu Nasazení a konfigurace brány Azure firewall pomocí webu Azure Portal](tutorial-firewall-deploy-portal.md).
