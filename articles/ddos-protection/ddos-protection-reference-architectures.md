---
title: Azure DDoS Protection referenční architektury
description: Naučte se referenční architektury Azure DDoS Protection.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: b74ebf332790fd9a08840c8c76d99e2b014dac43
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103075"
---
# <a name="ddos-protection-reference-architectures"></a>DDoS Protection referenční architektury

DDoS Protection Standard je určený [pro služby, které jsou nasazené ve virtuální síti](../virtual-network/virtual-network-for-azure-services.md). Pro ostatní služby platí výchozí služba DDoS Protection Basic. Následující referenční architektury jsou uspořádané podle scénářů a jsou seskupeny pomocí schémat architektury.

## <a name="virtual-machine-windowslinux-workloads"></a>Úlohy virtuálních počítačů (Windows/Linux)

### <a name="application-running-on-load-balanced-vms"></a>Aplikace spuštěná na virtuálních počítačích s vyrovnáváním zatížení

Tato referenční architektura ukazuje sadu osvědčených postupů pro spuštění několika virtuálních počítačů s Windows ve škálované sadě za nástrojem pro vyrovnávání zatížení, aby se zlepšila dostupnost a škálovatelnost. Tuto architekturu je možné použít pro jakékoli bezstavové úlohy, jako je například webový server.

![Diagram referenční architektury pro aplikaci spuštěnou na virtuálních počítačích s vyrovnáváním zatížení](./media/ddos-best-practices/image-9.png)

V této architektuře jsou úlohy distribuované napříč několika instancemi virtuálního počítače. V nástroji pro vyrovnávání zatížení je k virtuálním počítačům distribuována jedna veřejná IP adresa a Internetová data. Ve virtuální síti nástroje pro vyrovnávání zatížení Azure (Internet) je povolený DDoS Protection Standard, ke kterému je přidružená veřejná IP adresa.

Nástroj pro vyrovnávání zatížení distribuuje příchozí internetové požadavky do instancí virtuálních počítačů. Virtual Machine Scale Sets umožňuje, aby byl počet virtuálních počítačů v ručním nebo na základě předdefinovaných pravidel škálované nebo automaticky rozložen. To je důležité, pokud je prostředek pod útokem DDoS. Další informace o této referenční architektuře najdete v [tomto článku](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

### <a name="application-running-on-windows-n-tier"></a>Aplikace běžící na N-vrstvách Windows

N-vrstvou architekturu je možné implementovat mnoha způsoby. Následující diagram znázorňuje typickou webovou aplikaci na třech úrovních. Tato architektura je založena na článku [spuštění virtuálních počítačů s vyrovnáváním zatížení pro zajištění škálovatelnosti a dostupnosti](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). Webové a obchodní vrstvy používají virtuální počítače s vyrovnáváním zatížení.

![Diagram referenční architektury pro aplikaci běžící na N-vrstvách Windows](./media/ddos-best-practices/image-10.png)

V této architektuře je ve virtuální síti povolená DDoS Protection Standard. Všechny veřejné IP adresy ve virtuální síti získají DDoS ochranu pro vrstvu 3 a 4. V případě ochrany vrstvy 7 nasaďte Application Gateway v SKU WAF. Další informace o této referenční architektuře najdete v [tomto článku](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

> [!NOTE]
> Scénáře, ve kterých je jeden virtuální počítač spuštěný za veřejnou IP adresou, se nepodporují.

### <a name="paas-web-application"></a>Webová aplikace PaaS

Tato referenční architektura ukazuje spuštění aplikace Azure App Service v jedné oblasti. Tato architektura ukazuje sadu osvědčených postupů pro webovou aplikaci, která používá [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) a [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
Pohotovostní oblast je nastavená pro scénáře převzetí služeb při selhání.

![Diagram referenční architektury pro webovou aplikaci v PaaS](./media/ddos-best-practices/image-11.png)

Azure Traffic Manager směruje příchozí žádosti do Application Gateway v jedné z oblastí. Během normálních operací směruje požadavky na Application Gateway v aktivní oblasti. Pokud tato oblast nebude k dispozici, Traffic Manager převezme Application Gateway v pohotovostní oblasti.

Veškerý provoz z Internetu určeného do webové aplikace je směrován do [Application Gateway veřejné IP adresy](../application-gateway/application-gateway-web-app-overview.md) prostřednictvím Traffic Manager. V tomto scénáři se služba App Service (samotná webová aplikace) přímo netýká a chrání ji Application Gateway. 

Doporučujeme, abyste nakonfigurovali Application Gateway WAF SKU (režim prevence), která vám umožní chránit proti útokům vrstvy 7 (HTTP/HTTPS/WebSocket). Kromě toho jsou webové aplikace nakonfigurované tak, aby [přijímaly jenom přenosy z Application Gateway](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) IP adresy.

Další informace o této referenční architektuře najdete v [tomto článku](/azure/architecture/reference-architectures/app-service-web-app/multi-region).

## <a name="protecting-on-premises-resources"></a>Ochrana místních prostředků

K ochraně místních prostředků můžete využít škálování, kapacitu a efektivitu Azure DDoS Protection Standard, a to díky hostování veřejné IP adresy v Azure a přesměrování provozu na back-end Server do místního prostředí.

![Ochrana premch prostředků](./media/reference-architectures/ddos-on-prem.png)

Pokud máte webovou aplikaci, která přijímá provoz z Internetu, můžete hostovat webovou aplikaci za Application Gateway a chránit ji pomocí WAF proti webovým útokům vrstvy 7, jako je například injektáže SQL a slowloris. Back-endové zdroje vaší aplikace budou v místním prostředí, které je připojené přes síť VPN. 

Back-endové prostředky v místním prostředí nebudou zpřístupněny veřejnému Internetu. K Internetu se zveřejňuje jenom veřejná IP adresa AppGW/WAF a název DNS vaší aplikace se mapuje na tuto veřejnou IP adresu. 

Pokud je ve virtuální síti povolená služba DDoS Protection Standard, která obsahuje AppGW/WAF, DDoS Protection Standard bude chránit vaše aplikace tím, že bude zmírnit špatný provoz a bude do vaší aplikace směrovat předpokládaný čistý provoz. 

V tomto [článku](https://docs.microsoft.com/azure/azure-vmware/protect-azure-vmware-solution-with-application-gateway) se dozvíte, jak můžete použít DDoS Protection Standard vedle Application Gateway k ochraně webové aplikace běžící na řešení Azure VMware.

## <a name="mitigation-for-non-web-paas-services"></a>Zmírnění rizik pro jiné než webové služby PaaS Services

### <a name="hdinsight-on-azure"></a>HDInsight v Azure

Tato referenční architektura ukazuje konfiguraci DDoS Protection standard pro [cluster Azure HDInsight](../hdinsight/index.yml). Ujistěte se, že je cluster HDInsight propojený s virtuální sítí a že DDoS Protection je ve virtuální síti povolený.

![Podokna HDInsight a rozšířená nastavení s nastavením virtuální sítě](./media/ddos-best-practices/image-12.png)

![Výběr pro povolení DDoS Protection](./media/ddos-best-practices/image-13.png)

V této architektuře je provoz určený pro cluster HDInsight z Internetu směrován do veřejné IP adresy přidružené k nástroji pro vyrovnávání zatížení brány HDInsight. Nástroj pro vyrovnávání zatížení brány pak pošle provoz na hlavní uzly nebo pracovní uzly přímo. Vzhledem k tomu, že je ve virtuální síti HDInsight povolený DDoS Protection Standard, všechny veřejné IP adresy ve virtuální síti získají DDoS ochranu pro vrstvu 3 a 4. Tuto referenční architekturu je možné kombinovat s referenčními architekturami N-vrstvých a více oblastí.

Další informace o této referenční architektuře najdete v tématu věnovaném [rozšiřování Azure HDInsight pomocí dokumentace k azure Virtual Network](../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .


> [!NOTE]
> Azure App Service Environment pro PowerApps nebo API Management ve virtuální síti s veřejnou IP adresou není nativně podporovaná.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit plán DDoS Protection](manage-ddos-protection.md).