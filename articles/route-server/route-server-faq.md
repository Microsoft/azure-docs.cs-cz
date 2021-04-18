---
title: Nejčastější dotazy týkající se serveru Azure Route
description: Získejte odpovědi na nejčastější dotazy týkající se Azure Route serveru.
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 04/16/2021
ms.author: duau
ms.openlocfilehash: 0bbe16fb63a4546b4b4745df16074f6a4b0cb26b
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599532"
---
# <a name="azure-route-server-preview-faq"></a>Azure Route Server (Preview) – nejčastější dotazy

> [!IMPORTANT]
> Služba Azure Route Server (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-is-azure-route-server"></a>Co je Azure Route Server?

Azure Route Server je plně spravovaná služba, která umožňuje snadno spravovat směrování mezi virtuálním síťovým zařízením (síťové virtuální zařízení) a vaší virtuální sítí.

### <a name="is-azure-route-server-just-a-vm"></a>Je jenom Azure Route Server jenom s virtuálním počítačem?

No. Azure Route Server je služba navržená s vysokou dostupností. Pokud je nasazená v oblasti Azure, která podporuje [zóny dostupnosti](../availability-zones/az-overview.md), bude mít redundanci na úrovni zóny.

### <a name="how-many-route-servers-can-i-create-in-a-virtual-network"></a>Kolik směrovacích serverů je možné vytvořit ve virtuální síti?

Ve virtuální síti můžete vytvořit jenom jeden směrovací server. Musí být nasazena v určené podsíti s názvem *RouteServerSubnet*.

### <a name="does-azure-route-server-support-vnet-peering"></a>Podporuje Azure Route Server partnerský vztah virtuálních sítí?

Ano. Pokud nasadíte virtuální síť, která je hostitelem serveru tras Azure, do jiné virtuální sítě a povolíte použít vzdálenou bránu v této virtuální síti, Azure Route Server se seznámí s adresními prostory této virtuální sítě a pošle je do všech síťová virtuální zařízení partnerských uzlů. Bude také naprogramovat trasy z síťová virtuální zařízení do směrovací tabulky virtuálních počítačů ve virtuální síti s partnerským vztahem. 


### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>Jaké směrovací protokoly podporuje Azure Route Server?

Server směrování Azure podporuje pouze Border Gateway Protocol (BGP). Vaše síťové virtuální zařízení musí podporovat externí protokol BGP s více segmenty, protože budete muset nasadit server směrování Azure v vyhrazené podsíti ve vaší virtuální síti. [ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) , které si zvolíte, se musí lišit od jednoho serveru Azure Route, který se používá při konfiguraci protokolu BGP v síťové virtuální zařízení.

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>Směruje Server Azure Route Server přenos dat mezi síťové virtuální zařízení a můjmi virtuálními počítači?

No. Server tras Azure vyměňuje pouze trasy protokolu BGP s vaším síťové virtuální zařízení. Přenos dat směřuje přímo z síťové virtuální zařízení do cílového virtuálního počítače a přímo z virtuálního počítače do síťové virtuální zařízení.

### <a name="does-azure-route-server-store-customer-data"></a>Ukládá Azure Route Server data o zákaznících?
No. Server tras Azure vyměňuje pouze trasy protokolu BGP s vaším síťové virtuální zařízení a pak je šíří do vaší virtuální sítě.

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-how-does-it-handle-them"></a>Pokud Azure Route Server dostane stejnou trasu z více než jednoho síťové virtuální zařízení, jak je zpracovává?

Pokud má trasa stejnou cestu jako délka cesty, server tras Azure bude naprogramovat více kopií trasy, z nichž každý má jiný segment směrování, na virtuální počítače ve virtuální síti. Když virtuální počítače odesílají provoz do cíle této trasy, hostitelé virtuálních počítačů provede Equal-Cost směrování s více cestami (ECMP). Pokud ale jedna síťové virtuální zařízení odešle trasu kratší jako délku cesty než jiné síťová virtuální zařízení, server tras Azure bude naprogramovat pouze trasu s dalším segmentem směrování nastaveným na tento síťové virtuální zařízení do virtuálních počítačů ve virtuální síti.

### <a name="does-azure-route-server-preserve-the-bgp-communities-of-the-route-it-receives"></a>Zachovává Server Azure Route komunity protokolu BGP trasy, kterou přijímá?

Ano, Azure Route server šíří trasu s komunitami protokolu BGP tak, jak je.

### <a name="what-autonomous-system-numbers-asns-can-i-use"></a>Jaká čísla autonomního systému (čísla ASN) můžu použít?

Ve virtuálním síťovém zařízení můžete použít vlastní veřejný čísla ASN nebo privátní čísla ASN. Nemůžete použít rozsahy rezervované pro Azure nebo IANA.
Následující čísla ASN jsou vyhrazené pro Azure nebo IANA:

* Čísla ASN rezervované pro Azure:
    * Veřejná ASN: 8074, 8075, 12076
    * Soukromá ASN: 65515, 65517, 65518, 65519, 65520
* Čísla ASN [vyhrazený organizací IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml):
    * 23456, 64496-64511, 65535-65551

### <a name="can-i-use-32-bit-4-byte-asns"></a>Můžu použít 32-bit (4 bajty) čísla ASN?

Ne, směrovací server Azure podporuje jenom 16 bitů (2 bajty) čísla ASN.

## <a name="route-server-limits"></a><a name = "limitations"></a>Omezení serveru tras

Server Azure Route má následující omezení (na jedno nasazení).

| Prostředek | Omezení |
|----------|-------|
| Počet podporovaných partnerských uzlů protokolu BGP | 8 |
| Počet tras, které může každý partnerský uzel protokolu BGP inzerovat službě Azure Route Server | 200 |
| Počet tras, které může server Azure Route inzerovat službě ExpressRoute nebo VPN Gateway | 200 |

Pokud vaše síťové virtuální zařízení inzeruje více tras, než je limit, relace protokolu BGP se vynechá. Pokud k tomu dojde u brány a směrovacího serveru Azure, ztratíte připojení z vaší místní sítě k Azure. Další informace najdete v tématu [Diagnostika problému s směrováním virtuálních počítačů Azure](../virtual-network/diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [nakonfigurovat server směrování Azure](quickstart-configure-route-server-powershell.md).
