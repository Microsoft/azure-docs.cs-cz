---
title: 'Kurz: nasazení a konfigurace Azure Firewall v hybridní síti pomocí Azure Portal'
description: V tomto kurzu se naučíte, jak nasadit a nakonfigurovat Azure Firewall pomocí Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 11/17/2020
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 86e27c190b269763d8dd2f562a207b3f2020da29
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051067"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-the-azure-portal"></a>Kurz: nasazení a konfigurace Azure Firewall v hybridní síti pomocí Azure Portal

Když připojíte místní síť k virtuální síti Azure a vytvoříte hybridní síť, bude mít možnost řídit přístup k síťovým prostředkům Azure důležitou součást celkového plánu zabezpečení.

S využitím služby Azure Firewall můžete řídit síťový přístup v hybridní síti pomocí pravidel, která definují povolený a zakázaný síťový provoz.

Pro tento kurz vytvoříte tři virtuální sítě:

- **VNet-hub** – brána firewall je v této virtuální síti.
- **VNet-paprsek** – virtuální síť paprsků představuje zatížení, které je umístěné v Azure.
- **VNet-OnPrem** -místní virtuální síť představuje místní síť. Ve skutečném nasazení je možné ho připojit buď pomocí sítě VPN, nebo připojení ExpressRoute. Pro zjednodušení tento kurz používá připojení brány VPN a k reprezentaci místní sítě se používá virtuální síť, která je umístěná v Azure.

![Brána firewall v hybridní síti](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Deklarování proměnných
> * Vytvoření virtuální sítě centra firewallu
> * Vytvoření virtuální sítě paprsků
> * Vytvoření místní virtuální sítě
> * Konfigurace a nasazení brány firewall
> * Vytvoření a propojení bran VPN
> * Vytvoření partnerského vztahu mezi virtuálními sítěmi hub a paprsek
> * Vytvoření tras
> * Vytvoření virtuálních počítačů
> * Testování brány firewall

Pokud chcete použít Azure PowerShell k provedení tohoto postupu, přečtěte si téma [nasazení a konfigurace Azure firewall v hybridní síti pomocí Azure PowerShell](tutorial-hybrid-ps.md).

## <a name="prerequisites"></a>Požadavky

Hybridní síť používá model architektury hvězdicové a hvězdicové ke směrování provozu mezi Azure virtuální sítě a místními sítěmi. Architektura centra a paprsků má následující požadavky:

- Nastavte **AllowGatewayTransit** při VNet-Hub partnerských vztahů na VNet-paprsek. V architektuře sítě s rozbočovačem a paprsky umožňuje přenos brány virtuální sítě rozbočovače sdílení brány VPN v centru místo nasazení bran VPN v každé virtuální síti paprsků. 

   Trasy k virtuálním sítím připojeným bránou nebo místním sítím se navíc automaticky rozšíří do směrovacích tabulek pro partnerské virtuální sítě s použitím přenosu brány. Další informace najdete v tématu [Konfigurace přenosu brány VPN pro partnerský vztah virtuálních sítí](../vpn-gateway/vpn-gateway-peering-gateway-transit.md).

- Nastavte **useremotegateways nastavený** , když se VNet-Spoke partnerských vztahů k virtuálnímu centru VNET. Pokud je nastavená taky **useremotegateways nastavený** a **AllowGatewayTransit** ve vzdáleném partnerském vztahu, virtuální síť paprsků používá pro přenos brány vzdálenou virtuální síť.
- Chcete-li směrovat přenos podsítě paprsků přes bránu firewall centra, můžete použít trasu definovanou uživatelem (UDR), která odkazuje na bránu firewall s možností **šíření tras brány virtuální sítě** zakázané. Možnost zakázat **šíření tras brány virtuální sítě** zabraňuje distribuci tras do podsítí paprsků. Tím se zabrání tomu, aby se naučily trasy z konfliktu s vaším UDR. Pokud chcete zajistit, aby **šíření tras brány virtuální sítě** bylo povolené, nezapomeňte v bráně firewall definovat konkrétní trasy pro přepsání těch, které jsou publikované z místního počítače přes protokol BGP.
- Nakonfigurujte UDR v podsíti brány centra, která odkazuje na IP adresu brány firewall jako na další segment směrování sítí paprsků. V Azure Firewall podsíti se nevyžadují žádné UDR, protože se učí trasy od protokolu BGP.

Postup vytvoření těchto tras najdete v části [Vytvoření pravidel](#create-the-routes) v tomto kurzu.

>[!NOTE]
>Služba Azure Firewall musí mít přímé připojení k internetu. Pokud vaše AzureFirewallSubnet zjišťuje výchozí trasu k místní síti přes protokol BGP, musíte tuto hodnotu přepsat hodnotou 0.0.0.0/0 UDR s hodnotou **typem** nastavenou jako **Internet** pro udržování přímého připojení k Internetu.
>
>Azure Firewall lze nakonfigurovat pro podporu vynuceného tunelování. Další informace najdete v tématu [Azure firewall vynucené tunelování](forced-tunneling.md).

>[!NOTE]
>Provoz mezi přímo rovnocenným virtuální sítě je směrován přímo, i když jako výchozí bránu UDR body Azure Firewall. Aby bylo možné odeslat podsíť do brány firewall v tomto scénáři, musí UDR v obou podsítích explicitně obsahovat předponu sítě cílové podsítě.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-the-firewall-hub-virtual-network"></a>Vytvoření virtuální sítě centra firewallu

Nejdřív vytvořte skupinu prostředků, která bude obsahovat prostředky pro tento kurz:

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Na domovské stránce Azure Portal vyberte **skupiny prostředků**  >  **Přidat**.
3. V části **Předplatné** vyberte své předplatné.
1. Jako **název skupiny prostředků** zadejte **FW-Hybrid-test**.
2. V **oblasti oblast** vyberte **(US) východní USA**. Všechny prostředky, které vytvoříte později, musí být ve stejném umístění.
3. Vyberte **Zkontrolovat a vytvořit**.
4. Vyberte **Vytvořit**.

Nyní vytvořte virtuální síť:

> [!NOTE]
> Velikost podsítě AzureFirewallSubnet je/26. Další informace o velikosti podsítě najdete v tématu [Azure firewall Nejčastější dotazy](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

1. Na domovské stránce Azure Portal vyberte **vytvořit prostředek**.
2. V části **sítě** vyberte **virtuální síť**.
1. Vyberte **Vytvořit**.
1. Jako **skupinu prostředků** vyberte **FW-Hybrid-test**.
1. Jako **název** zadejte **VNet-hub**.
1. Vyberte **Další: IP adresy**.
1. V případě **adresního prostoru IPv4** odstraňte výchozí adresu a zadejte **10.5.0.0/16**.
1. V části **název podsítě** vyberte **Přidat podsíť**.
1. Jako **název podsítě** zadejte **AzureFirewallSubnet**. Brána firewall bude v této podsíti a název podsítě **musí** být AzureFirewallSubnet.
1. Jako **Rozsah adres podsítě** zadejte **10.5.0.0/26**. 
1. Vyberte **Add** (Přidat).
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**.

## <a name="create-the-spoke-virtual-network"></a>Vytvoření virtuální sítě paprsků

1. Na domovské stránce Azure Portal vyberte **vytvořit prostředek**.
2. V článku **sítě** vyberte **virtuální síť**.
7. Jako **skupinu prostředků** vyberte **FW-Hybrid-test**.
1. Jako **název** zadejte **VNet-paprsek**.
2. V **oblasti oblast** vyberte **(US) východní USA**.
3. Vyberte **Další: IP adresy**.
4. V případě **adresního prostoru IPv4** odstraňte výchozí adresu a zadejte **10.6.0.0/16**.
6. V části **název podsítě** vyberte **Přidat podsíť**.
7. Jako **název podsítě** zadejte **sn-zatížení**.
8. Jako **Rozsah adres podsítě** zadejte **10.6.0.0/24**. 
9. Vyberte **Add** (Přidat).
10. Vyberte **Zkontrolovat a vytvořit**.
11. Vyberte **Vytvořit**.

## <a name="create-the-on-premises-virtual-network"></a>Vytvoření místní virtuální sítě

1. Na domovské stránce Azure Portal vyberte **vytvořit prostředek**.
2. V článku **sítě** vyberte **virtuální síť**.
7. Jako **skupinu prostředků** vyberte **FW-Hybrid-test**.
1. Jako **název** zadejte **VNet-OnPrem**.
2. V **oblasti oblast** vyberte **(US) východní USA**.
3. Vybrat **Další: IP adresy**
4. V případě **adresního prostoru IPv4** odstraňte výchozí adresu a zadejte **192.168.0.0/16**.
5. V části **název podsítě** vyberte **Přidat podsíť**.
7. Jako **název podsítě** zadejte **sn-Corp**.
8. Jako **Rozsah adres podsítě** zadejte **192.168.1.0/24**. 
9. Vyberte **Add** (Přidat).
10. Vyberte **Zkontrolovat a vytvořit**.
11. Vyberte **Vytvořit**.

Nyní vytvořte druhou podsíť pro bránu.

1. Na stránce **VNet-OnPrem** vyberte **podsítě**.
2. Vyberte **+ podsíť**.
3. Jako **název** zadejte **GatewaySubnet**.
4. Pro **Rozsah adres podsítě** typu **192.168.2.0/24**.
5. Vyberte **OK**.

## <a name="configure-and-deploy-the-firewall"></a>Konfigurace a nasazení brány firewall

Teď nasaďte bránu firewall do virtuální sítě centra brány firewall.

1. Na domovské stránce Azure Portal vyberte **vytvořit prostředek**.
2. V levém sloupci vyberte **sítě** a vyhledejte a pak vyberte **firewall**.
4. Na stránce **Vytvoření brány firewall** nakonfigurujte bránu firewall podle následující tabulky:

   |Nastavení  |Hodnota  |
   |---------|---------|
   |Předplatné     |\<your subscription\>|
   |Skupina prostředků     |**FW – Hybrid-test** |
   |Název     |**AzFW01**|
   |Oblast     |**East US**|
   |Volba virtuální sítě     |**Použít existující**:<br> **Virtuální síť – centrum**|
   |Veřejná IP adresa     |Přidat nové: <br>**FW-PIP** |

5. Vyberte **Zkontrolovat a vytvořit**.
6. Zkontrolujte souhrn a pak vyberte **vytvořit** a vytvořte bránu firewall.

   Nasazení bude trvat několik minut.
7. Po dokončení nasazení přejdete do skupiny prostředků **FW-Hybrid-test** a vyberete bránu **AzFW01** firewall.
8. Poznamenejte si privátní IP adresu. Budete ji potřebovat později při vytváření výchozí trasy.

### <a name="configure-network-rules"></a>Konfigurace pravidel sítě

Nejdřív přidejte síťové pravidlo, které povolí webový provoz.

1. Na stránce **AzFW01** vyberte **pravidla**.
2. Vyberte kartu **kolekce pravidel sítě** .
3. Vyberte **přidat kolekci pravidel sítě**.
4. Jako **název** zadejte **RCNet01**.
5. Jako **Priorita** zadejte **100**.
6. V části **Akce** vyberte **Povolit**.
6. Do pole **pravidla** zadejte  **AllowWeb**.
7. V části **Protokol** vyberte **TCP**.
8. Jako **typ zdroje** vyberte **IP adresa**.
9. Jako **zdroj** zadejte **192.168.1.0/24**.
10. Jako **typ cíle** vyberte **IP adresa**.
11. Pro **cílovou adresu** zadejte **10.6.0.0/16** .
12. V případě **cílových portů** zadejte **80**.

Nyní přidejte pravidlo povolující provoz protokolu RDP.

Na řádku druhý pravidlo zadejte následující informace:

1. **Název** zadejte **AllowRDP**.
2. V části **Protokol** vyberte **TCP**.
3. Jako **typ zdroje** vyberte **IP adresa**.
4. Jako **zdroj** zadejte **192.168.1.0/24**.
5. Jako **typ cíle** vyberte **IP adresa**.
6. Pro **cílovou adresu** zadejte **10.6.0.0/16** .
7. V případě **cílových portů** zadejte **3389**.
8. Vyberte **Add** (Přidat).

## <a name="create-and-connect-the-vpn-gateways"></a>Vytvoření a propojení bran VPN

Rozbočovač a místní virtuální sítě se připojují prostřednictvím bran VPN.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Vytvoření brány VPN pro virtuální síť centrální sítě

Nyní vytvořte bránu VPN pro virtuální síť centrální sítě. Konfigurace sítě na síť vyžadují RouteBased VpnType. Vytvoření brány VPN může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové položce brány VPN.

1. Na domovské stránce Azure Portal vyberte **vytvořit prostředek**.
2. Do textového pole Hledat zadejte **bránu virtuální sítě**.
3. Vyberte **Brána virtuální sítě** a vyberte **vytvořit**.
4. Jako **název** zadejte **GS-hub**.
5. V poli **oblast** vyberte stejnou oblast, kterou jste použili dříve.
6. Jako **typ brány** vyberte **VPN**.
7. Jako **typ sítě VPN** vyberte **směrování založené na trasách**.
8. V případě **SKU** vyberte **základní**.
9. V případě služby **Virtual Network** vyberte **VNet-hub**.
10. Pro **veřejnou IP adresu** vyberte **vytvořit novou** a jako název zadejte **VNet-hub-GS-PIP** .
11. Potvrďte zbývající výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**.
12. Zkontrolujte konfiguraci a pak vyberte **vytvořit**.

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Vytvoření brány VPN pro místní virtuální síť

Teď vytvořte bránu VPN pro místní virtuální síť. Konfigurace sítě na síť vyžadují RouteBased VpnType. Vytvoření brány VPN může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové položce brány VPN.

1. Na domovské stránce Azure Portal vyberte **vytvořit prostředek**.
2. Do textového pole Hledat zadejte **bránu virtuální sítě** a stiskněte klávesu **ENTER**.
3. Vyberte **Brána virtuální sítě** a vyberte **vytvořit**.
4. Jako **název** zadejte **GS-OnPrem**.
5. V poli **oblast** vyberte stejnou oblast, kterou jste použili dříve.
6. Jako **typ brány** vyberte **VPN**.
7. Jako **typ sítě VPN** vyberte **směrování založené na trasách**.
8. V případě **SKU** vyberte **základní**.
9. V případě služby **Virtual Network** vyberte **VNet-OnPrem**.
10. Pro **veřejnou IP adresu** vyberte **vytvořit novou** a jako název zadejte **VNet-OnPrem-GS-PIP** .
11. Potvrďte zbývající výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**.
12. Zkontrolujte konfiguraci a pak vyberte **vytvořit**.

### <a name="create-the-vpn-connections"></a>Vytvoření připojení VPN

Nyní můžete vytvořit připojení VPN mezi centrem a místními bránami.

V tomto kroku vytvoříte připojení z virtuální sítě rozbočovače k místní virtuální síti. Zobrazí se sdílený klíč uváděný v příkladech. Pro sdílený klíč můžete použít vlastní hodnoty. Důležité je, že se sdílený klíč pro obě připojení musí shodovat. Vytvoření připojení může nějakou dobu trvat.

1. Otevřete skupinu prostředků- **Hybrid-test FW** a vyberte bránu **GS-hub** .
2. V levém sloupci vyberte **připojení** .
3. Vyberte **Add** (Přidat).
4. Název připojení, typ **hub-to-OnPrem**.
5. Pro **Typ připojení** vyberte **VNet-to-VNet** .
6. Pro **druhou bránu virtuální sítě** vyberte **GS-OnPrem**.
7. Pro **sdílený klíč (PSK)** zadejte **AzureA1b2C3**.
8. Vyberte **OK**.

Vytvořte připojení k virtuální síti z místního prostředí k rozbočovači. Tento krok je podobný předchozímu, s tím rozdílem, že vytvoříte připojení z VNet-Onprem k rozbočovači VNet. Ověřte, že se sdílené klíče shodují. Připojení se vytvoří během několika minut.

1. Otevřete skupinu prostředků " **Hybrid-test FW** " a vyberte bránu **GS-OnPrem** .
2. V levém sloupci vyberte **připojení** .
3. Vyberte **Add** (Přidat).
4. Do pole název připojení zadejte **OnPrem-to-hub**.
5. Pro **Typ připojení** vyberte **VNet-to-VNet** .
6. Pro **druhou bránu virtuální sítě** vyberte **GS-hub**.
7. Pro **sdílený klíč (PSK)** zadejte **AzureA1b2C3**.
8. Vyberte **OK**.


#### <a name="verify-the-connection"></a>Ověření připojení

Po asi pěti minutách se musí **připojit** stav obou připojení.

![Připojení brány](media/tutorial-hybrid-portal/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Vytvoření partnerského vztahu mezi virtuálními sítěmi hub a paprsek

Nyní můžete vytvořit partnerský vztah mezi virtuálními sítěmi hub a paprsek.

1. Otevřete skupinu prostředků " **Hybrid-test FW** " a vyberte virtuální síť **centra VNet** .
2. V levém sloupci vyberte **partnerské vztahy**.
3. Vyberte **Add** (Přidat).
4. V rámci **této virtuální sítě**:
 
   
   |Název nastavení  |Hodnota  |
   |---------|---------|
   |Název propojení partnerského vztahu| HubtoSpoke|
   |Provoz do vzdálené virtuální sítě|   Povoleno (výchozí)      |
   |Přenos předaných ze vzdálené virtuální sítě    |   Povoleno (výchozí)      |
   |Brána virtuální sítě     |  Použít bránu této virtuální sítě       |
    
5. V části **Vzdálená virtuální síť**:

   |Název nastavení  |Hodnota  |
   |---------|---------|
   |Název propojení partnerského vztahu | SpoketoHub|
   |Model nasazení virtuální sítě| Resource Manager|
   |Předplatné|\<your subscription\>|
   |Virtuální síť| VNet-Spoke
   |Provoz do vzdálené virtuální sítě     |   Povoleno (výchozí)      |
   |Přenos předaných ze vzdálené virtuální sítě    |   Povoleno (výchozí)      |
   |Brána virtuální sítě     |  Použít bránu vzdálené virtuální sítě       |

5. Vyberte **Add** (Přidat).

   :::image type="content" source="media/tutorial-hybrid-portal/firewall-peering.png" alt-text="Partnerský vztah virtuální sítě":::

## <a name="create-the-routes"></a>Vytvoření tras

Dále vytvořte několik tras:

- Trasa z podsítě brány rozbočovače do podsítě paprsku přes IP adresu brány firewall
- Výchozí trasa z podsítě paprsku přes IP adresu brány firewall

1. Na domovské stránce Azure Portal vyberte **vytvořit prostředek**.
2. Do textového pole Hledat zadejte **Směrování Table** a stiskněte klávesu **ENTER**.
3. Vyberte **směrovací tabulku**.
4. Vyberte **Vytvořit**.
6. Vyberte položku **FW-Hybrid-test** pro skupinu prostředků.
8. V poli **oblast** vyberte stejné umístění, které jste použili dříve.
1. Jako název zadejte **udr-hub-paprsek**.
9. Vyberte **Zkontrolovat a vytvořit**.
10. Vyberte **Vytvořit**.
11. Po vytvoření směrovací tabulky vyberte ji a otevřete stránku směrovací tabulka.
12. V levém sloupci vyberte možnost **trasy** .
13. Vyberte **Add** (Přidat).
14. Jako název trasy zadejte **ToSpoke**.
15. Jako předponu adresy zadejte **10.6.0.0/16**.
16. V poli Typ dalšího segmentu směrování vyberte **virtuální zařízení**.
17. Do pole adresa dalšího směrování zadejte privátní IP adresu brány firewall, kterou jste si poznamenali dříve.
18. Vyberte **OK**.

Nyní připojte trasu k podsíti.

1. Na stránce **udr-hub-paprsek-Routes** vyberte **podsítě**.
2. Vyberte **přidružit**.
3. V části **virtuální síť** vyberte **VNet-hub**.
1. V části **podsíť** vyberte **GatewaySubnet**.
2. Vyberte **OK**.

Nyní vytvořte výchozí trasu z podsítě paprsků.

1. Na domovské stránce Azure Portal vyberte **vytvořit prostředek**.
2. Do textového pole Hledat zadejte **Směrování Table** a stiskněte klávesu **ENTER**.
3. Vyberte **směrovací tabulku**.
5. Vyberte **Vytvořit**.
7. Vyberte položku **FW-Hybrid-test** pro skupinu prostředků.
8. V poli **oblast** vyberte stejné umístění, které jste použili dříve.
1. Jako název zadejte **udr-DG**.
4. V případě **trasy rozšíření** pro přenos brány vyberte možnost **ne**.
5. Vyberte **Zkontrolovat a vytvořit**.
6. Vyberte **Vytvořit**.
7. Po vytvoření směrovací tabulky vyberte ji a otevřete stránku směrovací tabulka.
8. V levém sloupci vyberte možnost **trasy** .
9. Vyberte **Add** (Přidat).
10. Jako název trasy zadejte **ToHub**.
11. Jako předponu adresy zadejte **0.0.0.0/0**.
12. V poli Typ dalšího segmentu směrování vyberte **virtuální zařízení**.
13. Do pole adresa dalšího směrování zadejte privátní IP adresu brány firewall, kterou jste si poznamenali dříve.
14. Vyberte **OK**.

Nyní připojte trasu k podsíti.

1. Na stránce **udr-DG-Routes** vyberte **podsítě**.
2. Vyberte **přidružit**.
3. V části **virtuální síť** vyberte **VNet-paprsek**.
1. V části **podsíť** vyberte **sériové – zatížení**.
2. Vyberte **OK**.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Teď vytvořte úlohu paprsků a místní virtuální počítače a umístěte je do příslušných podsítí.

### <a name="create-the-workload-virtual-machine"></a>Vytvoření virtuálního počítače úloh

Vytvořte virtuální počítač ve virtuální síti paprsků a spusťte službu IIS bez veřejné IP adresy.

1. Na domovské stránce Azure Portal vyberte **vytvořit prostředek**.
2. V části **Oblíbené** vyberte **Windows Server 2016 Datacenter**.
3. Zadejte pro virtuální počítač tyto hodnoty:
    - **Skupina prostředků** – vyberte **FW-Hybrid-test**.
    - **Název virtuálního počítače**: *VM-paprsek-01*.
    - **Oblast se** stejnou oblastí, kterou jste použili dříve.
    - **Uživatelské jméno**: \<type a user name\> .
    - **Heslo**: \<type a password\>
4. U **veřejných příchozích portů** vyberte **Povolit vybrané porty** a pak vyberte **http (80)** a **RDP (3389)** .
4. Vyberte **Další: disky**.
5. Přijměte výchozí hodnoty a vyberte **Další: sítě**.
6. Vyberte **VNet-hvězdicové** pro virtuální síť a podsíť je **sn-zatížení**.
7. V případě **veřejné IP adresy** vyberte **žádné**. 
9. Vyberte **Další: Správa**.
10. V případě **diagnostiky spouštění** vyberte **Zakázat**.
11. Vyberte **zkontrolovat + vytvořit**, zkontrolujte nastavení na stránce Souhrn a pak vyberte **vytvořit**.

### <a name="install-iis"></a>Instalace služby IIS

1. Z Azure Portal otevřete Cloud Shell a ujistěte se, že je nastavené na **PowerShell**.
2. Spuštěním následujícího příkazu nainstalujte službu IIS na virtuálním počítači a v případě potřeby změňte umístění:

   ```azurepowershell-interactive
   Set-AzVMExtension `
           -ResourceGroupName FW-Hybrid-Test `
           -ExtensionName IIS `
           -VMName VM-Spoke-01 `
           -Publisher Microsoft.Compute `
           -ExtensionType CustomScriptExtension `
           -TypeHandlerVersion 1.4 `
           -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell      Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
           -Location EastUS
   ```

### <a name="create-the-on-premises-virtual-machine"></a>Vytvoření místního virtuálního počítače

Toto je virtuální počítač, který používáte k připojení pomocí vzdálené plochy k veřejné IP adrese. Odtud se pak pomocí brány firewall připojíte k místnímu serveru.

1. Na domovské stránce Azure Portal vyberte **vytvořit prostředek**.
2. V části **Oblíbené** vyberte **Windows Server 2016 Datacenter**.
3. Zadejte pro virtuální počítač tyto hodnoty:
    - **Skupina prostředků** – vyberte existující a pak vyberte **FW-Hybrid-test**.
    - **Název**  -  virtuálního počítače *VM-OnPrem*.
    - **Oblast se** stejnou oblastí, kterou jste použili dříve.
    - **Uživatelské jméno**: \<type a user name\> .
    - **Heslo**: \<type a user password\> .
7. Pro **veřejné příchozí porty** vyberte **Povolit vybrané porty** a pak vyberte **RDP (3389)** .
4. Vyberte **Další: disky**.
5. Přijměte výchozí hodnoty a vyberte **Další: sítě**.
6. Vyberte **VNet-OnPrem** pro virtuální síť a podsíť je **sn-Corp**.
8. Vyberte **Další: Správa**.
10. V případě **diagnostiky spouštění** vyberte **Zakázat**.
10. Vyberte **zkontrolovat + vytvořit**, zkontrolujte nastavení na stránce Souhrn a pak vyberte **vytvořit**.

## <a name="test-the-firewall"></a>Testování brány firewall

1. Nejdřív si poznamenejte privátní IP adresu virtuálního počítače **VM-paprsek-01** .

2. Na webu Azure Portal se připojte k virtuálnímu počítači **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Otevřete webový prohlížeč na **virtuálním počítači-OnPrem** a přejděte na http:// \<VM-spoke-01 private IP\> .

   Měla by se zobrazit webová stránka **VM-paprsek-01** webová stránka ![ VM-paprsk-01.](media/tutorial-hybrid-portal/VM-Spoke-01-web.png)

4. Z virtuálního počítače **VM-OnPrem** otevřete vzdálenou plochu **virtuálního počítače-paprsek-01** na privátní IP adrese.

   Připojení by mělo být úspěšné a mělo by být možné se přihlásit.

Takže teď ověříte, že pravidla brány firewall fungují:

<!---- You can ping the server on the spoke VNet.--->
- Webový server můžete procházet ve virtuální síti paprsků.
- Pomocí protokolu RDP se můžete připojit k serveru ve virtuální síti paprsků.

Dále změňte akci kolekce pravidel sítě brány firewall na **Odepřít**, abyste ověřili, že pravidla brány firewall fungují podle očekávání.

1. Vyberte bránu firewall **AzFW01** .
2. Vyberte **pravidla**.
3. Vyberte kartu **kolekce pravidel sítě** a vyberte kolekci pravidel **RCNet01** .
4. V případě **Akce** vyberte **Odepřít**.
5. Vyberte **Uložit**.

Před testováním změněných pravidel ukončete všechna existující připojení vzdálené plochy. Teď znovu spusťte testy. Tentokrát by všechny měly selhat.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Prostředky brány firewall si můžete ponechat pro další kurz, nebo můžete odstraněním skupiny prostředků **FW-Hybrid-Test** odstranit všechny prostředky související z bránou firewall, pokud už je nepotřebujete.

## <a name="next-steps"></a>Další kroky

Dál můžete pokračovat monitorováním protokolů brány Azure Firewall.

> [!div class="nextstepaction"]
> [Kurz: Monitorování protokolů brány Azure Firewall](./firewall-diagnostics.md)