---
title: Zabezpečený provoz určený k soukromým koncovým bodům ve službě Azure Virtual WAN
description: Naučte se používat Síťová pravidla a pravidla aplikací k zabezpečení provozu určeného do privátních koncových bodů ve službě Azure Virtual WAN.
services: firewall-manager
author: jocortems
ms.service: firewall-manager
ms.topic: how-to
ms.date: 04/02/2021
ms.author: jocorte
ms.openlocfilehash: 7322bab635d398fc7a5335546ba6fef327ff24b2
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259349"
---
# <a name="secure-traffic-destined-to-private-endpoints-in-azure-virtual-wan"></a>Zabezpečený provoz určený k soukromým koncovým bodům ve službě Azure Virtual WAN

> [!NOTE]
> Tento článek se týká jenom zabezpečeného virtuálního rozbočovače. Pokud chcete zkontrolovat provoz určený k soukromým koncovým bodům pomocí Azure Firewall ve virtuální síti rozbočovače, přečtěte si téma [použití Azure firewall pro kontrolu provozu určeného pro soukromý koncový bod](../private-link/inspect-traffic-with-azure-firewall.md).

[Soukromý koncový bod Azure](../private-link/private-endpoint-overview.md) je základní stavební blok pro [privátní propojení Azure](../private-link/private-link-overview.md). Privátní koncové body umožňují prostředkům Azure nasazeným ve virtuální síti komunikovat soukromě s prostředky privátního propojení.

Soukromé koncové body umožňují prostředkům přístup ke službě privátního propojení nasazené ve virtuální síti. Přístup k privátnímu koncovému bodu prostřednictvím partnerského vztahu virtuální sítě a místních síťových připojení rozšiřuje připojení.

Možná budete muset filtrovat provoz od klientů místně nebo v Azure určených pro služby vystavené prostřednictvím privátních koncových bodů ve virtuální síti připojené k síti WAN. Tento článek vás provede tímto úkolem pomocí [zabezpečeného virtuálního centra](../firewall-manager/secured-virtual-hub.md) s [Azure firewall](../firewall/overview.md) jako poskytovatel zabezpečení.

Azure Firewall filtruje provoz pomocí kterékoli z následujících metod:

* [Plně kvalifikovaný název domény v síťových pravidlech](../firewall/fqdn-filtering-network-rules.md) pro protokoly TCP a UDP
* [Plně kvalifikovaný název domény v pravidlech použití](../firewall/features.md#application-fqdn-filtering-rules) pro http, https a MSSQL.
* Zdrojová a cílová IP adresa, port a protokol pomocí [síťových pravidel](../firewall/features.md#network-traffic-filtering-rules)

Pro kontrolu provozu určeného do privátních koncových bodů použijte pravidla aplikací pro síťová pravidla.
Zabezpečený virtuální rozbočovač je spravován společností Microsoft a nemůže být propojen se [zónou privátní DNS](../dns/private-dns-privatednszone.md). To je nutné k překladu plně kvalifikovaného názvu domény [prostředku privátního propojení](../private-link/private-endpoint-overview.md#private-link-resource) na odpovídající IP adresu privátního koncového bodu.

Filtrování plně kvalifikovaného názvu domény SQL je podporováno pouze v [režimu proxy serveru](../azure-sql/database/connectivity-architecture.md#connection-policy) (port 1433). Režim *proxy* může mít za následek větší latenci v porovnání s *přesměrování*. Pokud chcete pokračovat v používání režimu přesměrování, který je ve výchozím nastavení pro klienty připojující se v rámci Azure, můžete filtrovat přístup pomocí plně kvalifikovaného názvu domény v pravidlech sítě brány firewall.

## <a name="filter-traffic-using-fqdn-in-network-and-application-rules"></a>Filtrování provozu pomocí plně kvalifikovaného názvu domény v pravidlech sítě a aplikace

Následující kroky umožňují Azure Firewall filtrování provozu pomocí plně kvalifikovaného názvu domény v pravidlech sítě a aplikace:

1. Nasaďte virtuální počítač [DNS pro přeposílání](../private-link/private-endpoint-dns.md#virtual-network-and-on-premises-workloads-using-a-dns-forwarder) ve virtuální síti připojené k zabezpečenému virtuálnímu rozbočovači, který je propojený s privátní DNS zónami hostujícím typy záznamů pro privátní koncové body.

2. Nakonfigurujte [vlastní nastavení DNS](../firewall/dns-settings.md#configure-custom-dns-servers---azure-portal) tak, aby odkazovalo na IP adresu serveru DNS pro přeposílání DNS, a povolte DNS proxy v zásadách brány firewall přidružených k Azure firewall nasazené v zabezpečeném virtuálním centru.

3. Nakonfigurujte [vlastní servery DNS](../virtual-network/manage-virtual-network.md#change-dns-servers) pro virtuální sítě připojené k zabezpečenému virtuálnímu rozbočovači tak, aby odkazovaly na privátní IP adresu přidruženou k Azure firewall nasazenému v zabezpečeném virtuálním centru.

4. Nakonfigurujte místní servery DNS tak, aby přenesly dotazy DNS na veřejné zóny DNS u privátních koncových bodů na privátní IP adresu přidruženou k Azure Firewall nasazené v zabezpečeném virtuálním centru.

5. Nakonfigurujte [pravidlo nebo pravidlo aplikace](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule) [podle potřeby v](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule) zásadách brány firewall přidružených k Azure firewall nasazené v zabezpečeném virtuálním rozbočovači s názvem FQDN *cílového typu* a jako *cíl* zadejte veřejný plně kvalifikovaný název domény prostředku privátního propojení.

6. Přejděte na *Zabezpečená virtuální centra* v zásadách brány firewall přidružených k Azure firewall nasazené v zabezpečeném virtuálním rozbočovači a vyberte zabezpečené virtuální centrum, kde se nakonfigurují filtrování přenosů určených pro soukromé koncové body.

7. Přejděte na **Konfigurace zabezpečení** a v části **privátní provoz** vyberte **Odeslat prostřednictvím Azure firewall** .

8. Chcete-li upravit předpony CIDR, které budou zkontrolovány prostřednictvím Azure Firewall v zabezpečeném virtuálním rozbočovači, vyberte **předpony privátních přenosů** a přidejte jednu/32 předponu pro každý privátní koncový bod následujícím způsobem:

   > [!IMPORTANT]
   > Pokud tyto předpony/32 nejsou nakonfigurované, přenosy určené k soukromým koncovým bodům budou obejít Azure Firewall.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="Konfigurace zabezpečení Správce brány firewall" border="true":::

Tyto kroky fungují pouze v případě, že jsou klienti a privátní koncové body nasazeny v různých virtuálních sítích připojených ke stejnému zabezpečenému virtuálnímu rozbočovači a pro místní klienty. Pokud jsou klienti a privátní koncové body nasazeny ve stejné virtuální síti, je nutné vytvořit trasy UDR s/32 pro privátní koncové body. Nakonfigurujte tyto trasy s **typem dalšího segmentu směrování**  nastaveným na **virtuální zařízení** a **adresu dalšího směrování** nastavenou na soukromou IP adresu Azure firewall nasazenou v zabezpečeném virtuálním centru. **Trasy pro rozšíření brány** musí být nastavené na **hodnotu Ano**.

Následující diagram znázorňuje toky DNS a přenos dat pro různé klienty pro připojení k privátnímu koncovému bodu nasazenému v Azure Virtual WAN:

:::image type="content" source="./media/private-link-inspection-secure-virtual-hub/private-link-inspection-virtual-wan-architecture.png" alt-text="Toky provozu" border="true":::

## <a name="troubleshooting"></a>Řešení potíží

Hlavní problémy, které mohou nastat při pokusu o filtrování provozu určeného do privátních koncových bodů prostřednictvím zabezpečeného virtuálního rozbočovače, jsou:

- Klienti se nemohou připojit k privátním koncovým bodům.

- Azure Firewall se přeskočí. Tento příznak lze ověřit nepřítomností položek protokolu sítě nebo pravidla aplikace v Azure Firewall.

Ve většině případů tyto problémy způsobují některé z následujících příčin:

- Nesprávné rozlišení názvů DNS

- Nesprávná konfigurace směrování

### <a name="incorrect-dns-name-resolution"></a>Nesprávné rozlišení názvů DNS

1. Ověřte, že servery DNS virtuální sítě jsou nastavené na *vlastní* a IP adresa je privátní ip adresa Azure firewall v zabezpečeném virtuálním centru.

   Rozhraní příkazového řádku Azure:

   ```azurecli-interactive
   az network vnet show --name <VNET Name> --resource-group <Resource Group Name> --query "dhcpOptions.dnsServers"
   ```
2. Ověřte, že klienti ve stejné virtuální síti, jako je virtuální počítač DNS pro přeposílání, můžou přeložit veřejný plně kvalifikovaný název domény privátního koncového bodu na odpovídající privátní IP adresu, a to přímo dotazem na virtuální počítač nakonfigurovaný jako DNS pro přeposílání

   Linux:

   ```bash
   dig @<DNS forwarder VM IP address> <Private endpoint public FQDN>
   ```
3. Zkontrolujte položky protokolu *AzureFirewallDNSProxy* Azure firewall a ověřte, že je možné přijímat a řešit dotazy DNS od klientů.

   ```kusto
   AzureDiagnostics
   | where Category contains "DNS"
   | where msg_s contains "database.windows.net"
   ```
4. Ověřte, že je povolený *proxy server DNS* a že je v zásadách brány firewall přidružených k Azure firewall v zabezpečeném virtuálním centru nakonfigurovaný *vlastní* server DNS odkazující na IP adresu IP adresy virtuálního počítače DNS pro přeposílání.

   Rozhraní příkazového řádku Azure:

   ```azurecli-interactive
   az network firewall policy show --name <Firewall Policy> --resource-group <Resource Group Name> --query dnsSettings
   ```

### <a name="incorrect-routing-configuration"></a>Nesprávná konfigurace směrování

1. Ověřte *konfiguraci zabezpečení* v zásadách brány firewall přidružených k Azure firewall nasazené v zabezpečeném virtuálním centru. Ujistěte se, že je sloupec **privátní přenos** zobrazený jako **zabezpečený Azure firewall** pro všechna připojení virtuální sítě a větve, pro které chcete filtrovat provoz.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-policy-private-traffic-configuration.png" alt-text="Privátní provoz zabezpečený pomocí Azure Firewall" border="true":::

2. Ověřte **konfiguraci zabezpečení** v zásadách brány firewall přidružených k Azure firewall nasazené v zabezpečeném virtuálním centru. Zajistěte, aby existovala položka/32 pro každou privátní IP adresu privátního koncového bodu, pro kterou chcete filtrovat provoz v rámci **předpon privátních přenosů**.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="Konfigurace zabezpečení Správce brány firewall – předpony privátních přenosů" border="true":::

3. V zabezpečeném virtuálním rozbočovači ve virtuální síti WAN zkontrolujte efektivní trasy pro směrovací tabulky přidružené k virtuálním sítím a připojením větví, pro které chcete filtrovat provoz. Zajistěte, aby byly pro každou soukromou IP adresu privátního koncového bodu k dispozici položky/32, pro které chcete filtrovat provoz.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/secured-virtual-hub-effective-routes.png" alt-text="Efektivní trasy zabezpečeného virtuálního centra" border="true":::

4. Zkontrolujte efektivní trasy síťových karet připojených k virtuálním počítačům nasazeným ve virtuálních sítích, pro které chcete filtrovat provoz. Zajistěte, aby byly pro každou soukromou IP adresu privátního koncového bodu k dispozici položky/32, pro které chcete filtrovat provoz.
 
   Rozhraní příkazového řádku Azure:

   ```azurecli-interactive
   az network nic show-effective-route-table --name <Network Interface Name> --resource-group <Resource Group Name> -o table
   ```
5. Zkontrolujte směrovací tabulky místních zařízení pro směrování. Ujistěte se, že jste se naučili adresní prostory virtuálních sítí, ve kterých jsou nasazené privátní koncové body.

   Azure Virtual WAN neinzeruje předpony nakonfigurované v rámci **předpon privátních přenosů** v **konfiguraci zabezpečení** zásad brány firewall místně. Očekává se, že se položky/32 nebudou zobrazovat ve směrovacích tabulkách vašich místních směrovacích zařízení.

6. Zkontrolujte protokoly Azure Firewall **AzureFirewallApplicationRule** a **AzureFirewallNetworkRule** . Zajistěte, aby byl provoz určený pro privátní koncové body zaznamenáván.

   Položky protokolu **AzureFirewallNetworkRule** neobsahují informace o plně kvalifikovaném názvu domény. Při kontrole síťových pravidel filtrovat podle IP adresy a portu

   Při filtrování provozu určeného pro veřejné koncové body [souborů Azure](../storage/files/storage-files-introduction.md) se položky protokolu **AzureFirewallNetworkRule** budou generovat jenom při prvním připojení klienta nebo ke sdílené složce. Azure Firewall negeneruje protokoly pro operace [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) pro soubory ve sdílené složce. Důvodem je to, že operace CRUD se přenesou přes trvalý kanál TCP, který se otevře při prvním připojení klienta ke sdílené složce nebo k jeho připojení.

   Příklad dotazu protokolu pro pravidlo aplikace:

   ```kusto
   AzureDiagnostics
   | where msg_s contains "database.windows.net"
   | where Category contains "ApplicationRule"
   ```
## <a name="next-steps"></a>Další kroky

- [Kontrola provozu směrovaného do privátního koncového bodu pomocí Azure Firewallu](../private-link/inspect-traffic-with-azure-firewall.md)
