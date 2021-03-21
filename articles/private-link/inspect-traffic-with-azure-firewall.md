---
title: Použití Azure Firewall ke kontrole provozu určeného pro soukromý koncový bod
titleSuffix: Azure Private Link
description: Zjistěte, jak můžete kontrolovat provoz směřující do privátního koncového bodu pomocí Azure Firewall.
services: private-link
author: jocortems
ms.service: private-link
ms.topic: how-to
ms.date: 09/02/2020
ms.author: allensu
ms.openlocfilehash: 3ed349616ae6456913c19bb073f6e9ea28e7d549
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575128"
---
# <a name="use-azure-firewall-to-inspect-traffic-destined-to-a-private-endpoint"></a>Použití Azure Firewall ke kontrole provozu určeného pro soukromý koncový bod

Soukromý koncový bod Azure je základní stavební blok pro privátní propojení Azure. Privátní koncové body umožňují prostředkům Azure nasazeným ve virtuální síti komunikovat soukromě s prostředky privátního propojení.

Soukromé koncové body umožňují prostředkům přístup ke službě privátního propojení nasazené ve virtuální síti. Přístup k privátnímu koncovému bodu prostřednictvím partnerského vztahu virtuální sítě a místních síťových připojení rozšiřuje připojení.

Možná budete muset zkontrolovat nebo blokovat provoz od klientů až po služby, které jsou zpřístupněny prostřednictvím soukromých koncových bodů. Tuto kontrolu dokončete pomocí [Azure firewall](../firewall/overview.md) nebo síťového virtuálního zařízení třetí strany.

Platí následující omezení:

* Skupiny zabezpečení sítě (skupin zabezpečení sítě) se nevztahují na soukromé koncové body.
* Trasy definované uživatelem (UDR) se nevztahují na soukromé koncové body.
* Jedna směrovací tabulka může být připojená k podsíti.
* Směrovací tabulka podporuje až 400 tras.

Azure Firewall filtruje provoz pomocí těchto:

* [Plně kvalifikovaný název domény v síťových pravidlech](../firewall/fqdn-filtering-network-rules.md) pro protokoly TCP a UDP
* [Plně kvalifikovaný název domény v pravidlech použití](../firewall/features.md#application-fqdn-filtering-rules) pro http, https a MSSQL. 

Většina služeb vystavených v rámci soukromých koncových bodů používá protokol HTTPS. Při používání Azure SQL se doporučuje použít pravidla použití aplikací přes pravidla sítě.

> [!NOTE]
> Filtrování plně kvalifikovaného názvu domény SQL je podporováno pouze v [režimu proxy serveru](../azure-sql/database/connectivity-architecture.md#connection-policy) (port 1433). Režim **proxy** může mít za následek větší latenci v porovnání s *přesměrování*. Pokud chcete pokračovat v používání režimu přesměrování, který je ve výchozím nastavení pro klienty připojující se v rámci Azure, můžete filtrovat přístup pomocí plně kvalifikovaného názvu domény v pravidlech sítě brány firewall.

## <a name="scenario-1-hub-and-spoke-architecture---dedicated-virtual-network-for-private-endpoints"></a>Scénář 1: virtuální síť vyhrazená pro architekturu centra a paprsků pro privátní koncové body

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/hub-and-spoke.png" alt-text="Vyhrazená Virtual Network pro privátní koncové body" border="true":::

Tento scénář je nejrozšířenější architektura pro soukromou připojení k více službám Azure pomocí privátních koncových bodů. Trasa, která odkazuje na adresní prostor sítě, ve kterém jsou nasazené privátní koncové body. Tato konfigurace snižuje administrativní režii a brání v běhu do limitu 400 tras.

Připojením z klientské virtuální sítě k Azure Firewall ve virtuální síti rozbočovače se budou účtovat poplatky, pokud jsou virtuální sítě v partnerském vztahu.

Další informace o nákladech týkajících se připojení k virtuálním sítím s partnerským vztahem najdete v části Nejčastější dotazy stránky s [cenami](https://azure.microsoft.com/pricing/details/private-link/) .

>[!NOTE]
> Tento scénář je možné implementovat pomocí pravidel síťové virtuální zařízení nebo Azure Firewallch síťových pravidel jiných výrobců, nikoli pravidel aplikací.

## <a name="scenario-2-hub-and-spoke-architecture---shared-virtual-network-for-private-endpoints-and-virtual-machines"></a>Scénář 2: sdílená virtuální síť architektury centra a paprsků pro privátní koncové body a virtuální počítače

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/shared-spoke.png" alt-text="Soukromé koncové body a Virtual Machines ve stejné Virtual Network" border="true":::

Tento scénář je implementován v těchto případech:

* Pro privátní koncové body není možné mít vyhrazenou virtuální síť.

* Pokud se ve virtuální síti zveřejňuje jenom několik služeb pomocí privátních koncových bodů

Virtuální počítače budou mít systémové trasy/32, které odkazují na každý privátní koncový bod. Jedna trasa na soukromý koncový bod je nakonfigurovaná pro směrování provozu prostřednictvím Azure Firewall. 

Administrativní režijní náklady na údržbu směrovací tabulky se zvyšují jako služby, které jsou ve virtuální síti přístupné. Také se zvyšuje možnost zvýšení limitu trasy.

V závislosti na celkové architektuře je možné spustit limit 400 tras. Pokud je to možné, doporučuje se použít scénář 1.

Připojením z klientské virtuální sítě k Azure Firewall ve virtuální síti rozbočovače se budou účtovat poplatky, pokud jsou virtuální sítě v partnerském vztahu.

Další informace o nákladech týkajících se připojení k virtuálním sítím s partnerským vztahem najdete v části Nejčastější dotazy stránky s [cenami](https://azure.microsoft.com/pricing/details/private-link/) .

>[!NOTE]
> Tento scénář je možné implementovat pomocí pravidel síťové virtuální zařízení nebo Azure Firewallch síťových pravidel jiných výrobců, nikoli pravidel aplikací.

## <a name="scenario-3-single-virtual-network"></a>Scénář 3: jedna virtuální síť

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/single-vnet.png" alt-text="Jedna virtuální síť" border="true":::

K implementaci dochází k nějakým omezením: migrace do architektury hub a paprsků není možná. Platí stejné požadavky jako ve scénáři 2. V tomto scénáři se poplatky za partnerské vztahy virtuálních sítí nevztahují.

>[!NOTE]
> Pokud chcete tento scénář implementovat pomocí síťové virtuální zařízení nebo Azure Firewall třetí strany, musí se pravidla sítě místo pravidel aplikací vyžadovat pro přenos dat z provozu do privátních koncových bodů. Jinak komunikace mezi virtuálními počítači a soukromými koncovými body selže.

## <a name="scenario-4-on-premises-traffic-to-private-endpoints"></a>Scénář 4: místní provoz do privátních koncových bodů

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/on-premises.png" alt-text="Místní provoz do privátních koncových bodů" border="true":::

Tuto architekturu je možné implementovat, pokud jste nakonfigurovali připojení k místní síti pomocí těchto možností: 

* [ExpressRoute](..\expressroute\expressroute-introduction.md)
* [Síť k síti VPN](../vpn-gateway/tutorial-site-to-site-portal.md) 

Pokud požadavky na zabezpečení vyžadují, aby byly přenosy dat klientů do služeb vystavených prostřednictvím privátních koncových bodů směrovány přes bezpečnostní zařízení, nasaďte tento scénář.

Platí stejné požadavky jako ve scénáři 2 výše. V tomto scénáři se neúčtují poplatky za partnerské vztahy virtuálních sítí. Další informace o tom, jak nakonfigurovat servery DNS tak, aby umožňovaly místním úlohám přístup k privátním koncovým bodům, najdete v tématu [místní úlohy pomocí služby DNS pro přeposílání](./private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder).

>[!NOTE]
> Pokud chcete tento scénář implementovat pomocí síťové virtuální zařízení nebo Azure Firewall třetí strany, musí se pravidla sítě místo pravidel aplikací vyžadovat pro přenos dat z provozu do privátních koncových bodů. Jinak komunikace mezi virtuálními počítači a soukromými koncovými body selže.

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure.
* Pracovní prostor služby Log Analytics.  

Pokud ve svém předplatném nemáte nějaké předplatné, můžete si v [Azure Portal vytvořit pracovní prostor Log Analytics](../azure-monitor/logs/quick-create-workspace.md) .


## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

V této části vytvoříte virtuální síť a podsíť pro hostování virtuálního počítače používaného pro přístup k vašemu prostředku privátního propojení. Jako ukázkovou službu se používá databáze SQL Azure.

## <a name="virtual-networks-and-parameters"></a>Virtuální sítě a parametry

Vytvořte tři virtuální sítě a jejich odpovídající podsítě pro:

* Obsahuje Azure Firewall sloužící k omezení komunikace mezi virtuálním počítačem a privátním koncovým bodem.
* Hostovat virtuální počítač, který se používá pro přístup k prostředku privátního propojení.
* Hostování privátního koncového bodu.

V krocích níže nahraďte následující parametry:

### <a name="azure-firewall-network"></a>Azure Firewall sítě
| Parametr                   | Hodnota                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myAzFwVNet          |
| **\<region-name>**          | Středojižní USA      |
| **\<IPv4-address-space>**   | 10.0.0.0/16          |
| **\<subnet-name>**          | AzureFirewallSubnet        |
| **\<subnet-address-range>** | 10.0.0.0/24          |

### <a name="virtual-machine-network"></a>Síť virtuálního počítače
| Parametr                   | Hodnota                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVMVNet          |
| **\<region-name>**          | Středojižní USA      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | Podsíť vmsubnet      |
| **\<subnet-address-range>** | 10.1.0.0/24          |

### <a name="private-endpoint-network"></a>Síť privátního koncového bodu
| Parametr                   | Hodnota                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myPEVNet         |
| **\<region-name>**          | Středojižní USA      |
| **\<IPv4-address-space>**   | 10.2.0.0/16          |
| **\<subnet-name>**          | PrivateEndpointSubnet    |        |
| **\<subnet-address-range>** | 10.2.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

10. Opakováním kroků 1 až 9 vytvořte virtuální sítě pro hostování virtuálních počítačů a prostředků privátního koncového bodu.

### <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

1. V levé horní části obrazovky Azure Portal vyberte **vytvořit**  >    >  **virtuální počítač** Compute.

2. V nástroji **vytvořit virtuální počítač základy** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Tuto skupinu prostředků jste vytvořili v předchozí části.  |
    | **Podrobnosti o instancích** |  |
    | Název virtuálního počítače | Zadejte **myVM**. |
    | Oblast | Vyberte **(US) Střed USA – jih**. |
    | Možnosti dostupnosti | Nechte výchozí nastavení **bez nutnosti redundance infrastruktury**. |
    | Image | Vyberte **Ubuntu Server 18,04 LTS-Gen1**. |
    | Velikost | Vyberte **Standard_B2s**. |
    | **Účet správce** |  |
    | Typ ověřování | Vyberte **heslo**. |
    | Uživatelské jméno | Zadejte uživatelské jméno, které si zvolíte. |
    | Heslo | Zadejte libovolné heslo. Heslo musí mít délku aspoň 12 znaků a musí splňovat [definované požadavky na složitost](../virtual-machines/linux/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potvrdit heslo | Zadejte znovu heslo. |
    | **Pravidla portů pro příchozí spojení** |  |
    | Veřejné příchozí porty | Vyberte **Žádná**. |
    |||

3. Vyberte **Další: disky**.

4. V okně **Vytvořit virtuální počítač – Disky** nechte vybrané výchozí hodnoty a vyberte **Další: Sítě**.

5. V nástroji **vytvořit virtuální počítač – síť** vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Virtuální síť | Vyberte **myVMVNet**.  |
    | Podsíť | Vyberte **podsíť VMSubnet (10.1.0.0/24)**.|
    | Veřejná IP adresa | Ponechte výchozí **(New) myVm-IP**. |
    | Veřejné příchozí porty | Vyberte možnost **Povolení vybraných portů**. |
    | Vyberte příchozí porty | Vyberte **SSH**.|
    ||

6. Vyberte **Zkontrolovat a vytvořit**. Budete přesměrováni na stránku **Zkontrolovat a vytvořit**, kde Azure ověří konfiguraci.

7. Jakmile se zobrazí zpráva **Ověření proběhlo úspěšně**, vyberte **Vytvořit**.

## <a name="deploy-the-firewall"></a>Nasazení brány firewall

1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**.

2. Do vyhledávacího pole zadejte **firewall** a stiskněte klávesu **ENTER**.

3. Vyberte **firewall** a pak vyberte **vytvořit**.

4. Na stránce **Vytvoření brány firewall** nakonfigurujte bránu firewall podle následující tabulky:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**.  |
    | **Podrobnosti o instancích** |  |
    | Name | Zadejte **myAzureFirewall**. |
    | Oblast | Vyberte **střed USA – jih**. |
    | Zóna dostupnosti | Nechejte výchozí nastavení **žádné**. |
    | Volba virtuální sítě    |    Vyberte **použít existující**.    |
    | Virtuální síť    |    Vyberte **myAzFwVNet**.    |
    | Veřejná IP adresa    |    Vyberte **Přidat nové** a v názvu zadejte **myFirewall-IP**.    |
    | Vynucené tunelování    | Nechte výchozí nastavení **zakázané**.    |
    |||
5. Vyberte **Zkontrolovat a vytvořit**. Budete přesměrováni na stránku **Zkontrolovat a vytvořit**, kde Azure ověří konfiguraci.

6. Jakmile se zobrazí zpráva **Ověření proběhlo úspěšně**, vyberte **Vytvořit**.

## <a name="enable-firewall-logs"></a>Povolit protokoly brány firewall

V této části povolíte protokoly v bráně firewall.

1. V Azure Portal vyberte **všechny prostředky** v nabídce na levé straně.

2. V seznamu prostředků vyberte bránu firewall **myAzureFirewall** .

3. V části **monitorování** v nastavení brány firewall vyberte **nastavení diagnostiky** .

4. V nastavení diagnostiky vyberte **+ Přidat nastavení diagnostiky** .

5. V **nastavení diagnostiky** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název nastavení diagnostiky | Zadejte **myDiagSetting**. |
    | Podrobnosti kategorie | |
    | protokolu | Vyberte **AzureFirewallApplicationRule** a **AzureFirewallNetworkRule**. |
    | Podrobnosti o cíli | Vyberte **odeslat Log Analytics**. |
    | Předplatné | Vyberte své předplatné. |
    | Pracovní prostor služby Log Analytics | Vyberte pracovní prostor Log Analytics. |

6. Vyberte **Uložit**.

## <a name="create-azure-sql-database"></a>Vytvoření databáze SQL Azure

V této části vytvoříte privátní SQL Database.

1. V levé horní části obrazovky Azure Portal vyberte **vytvořit**  >  **databáze** prostředků  >  **SQL Database**.

2. V **SQL Database základy** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Tuto skupinu prostředků jste vytvořili v předchozí části.|
    | **Podrobnosti databáze** |  |
    | Název databáze  | Zadejte **MyDatabase**.  |
    | Server | Vyberte **vytvořit nové** a zadejte informace níže.    |
    | Název serveru | Zadejte **mydbserver**. Pokud se tento název povede, zadejte jedinečný název.   |
    | Přihlášení správce serveru | Zadejte název, který chcete vybrat. |
    | Heslo    |    Zadejte libovolné heslo.    |
    | Potvrdit heslo | Znovu zadejte heslo.    |
    | Umístění    | Vyberte **(US) Střed USA – jih**.    |
    | Chcete použít elastický fond SQL    | Ponechte výchozí hodnotu **ne**. |
    | Výpočty + úložiště | Ponechte výchozí **pro obecné účely Gen5, 2 virtuální jádra, 32 GB úložiště**. |
    |||

3. Vyberte **Zkontrolovat a vytvořit**. Budete přesměrováni na stránku **Zkontrolovat a vytvořit**, kde Azure ověří konfiguraci.

4. Jakmile se zobrazí zpráva **Ověření proběhlo úspěšně**, vyberte **Vytvořit**.

## <a name="create-private-endpoint"></a>Vytvořit privátní koncový bod

V této části vytvoříte privátní koncový bod pro Azure SQL Database v předchozí části.

1. V Azure Portal vyberte **všechny prostředky** v nabídce na levé straně.

2. V seznamu služeb vyberte Azure SQL Server **mydbserver** .  Pokud jste použili jiný název serveru, vyberte tento název.

3. V nastavení serveru vyberte v části **zabezpečení** možnost **připojení privátního koncového bodu** .

4. Vyberte **+ soukromý koncový bod**.

5. V části **Vytvoření privátního koncového bodu** zadejte nebo vyberte tyto informace na kartě **základy** :

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. |
    | **Podrobnosti o instancích** | |
    | Name | Zadejte **SQLPrivateEndpoint**. |
    | Oblast | Vyberte **(US) Střed USA – jih.** |

6. Vyberte kartu **prostředek** nebo v dolní části stránky vyberte položku **Další: prostředek** .

7. Na kartě **prostředek** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Způsob připojení | **V adresáři vyberte připojit k prostředku Azure**. |
    | Předplatné | Vyberte své předplatné. |
    | Typ prostředku | Vyberte **Microsoft. SQL/servery**. |
    | Prostředek | Vyberte **mydbserver** nebo název serveru, který jste vytvořili v předchozím kroku.
    | Cílový dílčí prostředek | Vyberte **sqlServer**. |

8. Vyberte kartu **Konfigurace** nebo vyberte **Další: Konfigurace** v dolní části stránky.

9. Na kartě **Konfigurace** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Sítě** | |
    | Virtuální síť | Vyberte **myPEVnet**. |
    | Podsíť | Vyberte **PrivateEndpointSubnet**. |
    | **Integrace Privátní DNS** | |
    | Integrovat s privátní zónou DNS | Vyberte **Ano**. |
    | Předplatné | Vyberte své předplatné. |
    | Privátní zóny DNS | Ponechte výchozí **privatelink.Database.Windows.NET**. |

10. Vyberte kartu **Revize + vytvořit** nebo v dolní části stránky vyberte **zkontrolovat + vytvořit** .

11. Vyberte **Vytvořit**.

12. Po vytvoření koncového bodu vyberte v části **zabezpečení** možnost **brány firewall a virtuální sítě** .

13. V poli **brány firewall a virtuální sítě** vyberte **Ano** a **Povolte službám a prostředkům Azure přístup k tomuto serveru**.

14. Vyberte **Uložit**.

## <a name="connect-the-virtual-networks-using-virtual-network-peering"></a>Připojení virtuálních sítí pomocí partnerského vztahu virtuálních sítí

V této části propojíme virtuální sítě **myVMVNet** a **myPEVNet** s **myAzFwVNet** pomocí partnerského vztahu. Mezi **myVMVNet** a **myPEVNet** nebude přímé připojení.

1. Na panelu hledání na portálu zadejte **myAzFwVNet**.

2. V nabídce **Nastavení** vyberte **partnerské vztahy** a vyberte **+ Přidat**.

3. V části **Přidat partnerský vztah** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název partnerského vztahu z myAzFwVNet do vzdálené virtuální sítě | Zadejte **myAzFwVNet-to-myVMVNet**. |
    | **Podrobnosti o partnerském vztahu** |  |
    | Model nasazení virtuální sítě  | Ponechte výchozí **Správce prostředků**.  |
    | Nevím ID prostředku | Tuto možnost nechte nezaškrtnutou.    |
    | Předplatné | Vyberte své předplatné.    |
    | Virtuální síť | Vyberte **myVMVNet**. |
    | Název partnerského vztahu ze vzdálené virtuální sítě do myAzFwVNet    |    Zadejte **myVMVNet-to-myAzFwVNet**.    |
    | **Konfigurace** | |
    | **Konfigurace nastavení přístupu k virtuální síti** | |
    | Umožňuje přístup k virtuální síti z myAzFwVNet do vzdálené virtuální sítě. | Ponechte výchozí **povolenou** možnost.    |
    | Povolení přístupu k virtuální síti ze vzdálené virtuální sítě do myAzFwVNet    | Ponechte výchozí **povolenou** možnost.    |
    | **Konfigurace nastavení předávaných přenosů** | |
    | Povolení přesměrovaného provozu ze vzdálené virtuální sítě do myAzFwVNet    | Vyberte **Povoleno**. |
    | Povolení přesměrovaného provozu z myAzFwVNet do vzdálené virtuální sítě | Vyberte **Povoleno**. |
    | **Konfigurace nastavení přenosu brány** | |
    | Povolení přenosu brány | Tuto možnost nechte nezaškrtnutou. |
    |||

4. Vyberte **OK**.

5. Vyberte **+ Přidat**.

6. V části **Přidat partnerský vztah** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název partnerského vztahu z myAzFwVNet do vzdálené virtuální sítě | Zadejte **myAzFwVNet-to-myPEVNet**. |
    | **Podrobnosti o partnerském vztahu** |  |
    | Model nasazení virtuální sítě  | Ponechte výchozí **Správce prostředků**.  |
    | Nevím ID prostředku | Tuto možnost nechte nezaškrtnutou.    |
    | Předplatné | Vyberte své předplatné.    |
    | Virtuální síť | Vyberte **myPEVNet**. |
    | Název partnerského vztahu ze vzdálené virtuální sítě do myAzFwVNet    |    Zadejte **myPEVNet-to-myAzFwVNet**.    |
    | **Konfigurace** | |
    | **Konfigurace nastavení přístupu k virtuální síti** | |
    | Umožňuje přístup k virtuální síti z myAzFwVNet do vzdálené virtuální sítě. | Ponechte výchozí **povolenou** možnost.    |
    | Povolení přístupu k virtuální síti ze vzdálené virtuální sítě do myAzFwVNet    | Ponechte výchozí **povolenou** možnost.    |
    | **Konfigurace nastavení předávaných přenosů** | |
    | Povolení přesměrovaného provozu ze vzdálené virtuální sítě do myAzFwVNet    | Vyberte **Povoleno**. |
    | Povolení přesměrovaného provozu z myAzFwVNet do vzdálené virtuální sítě | Vyberte **Povoleno**. |
    | **Konfigurace nastavení přenosu brány** | |
    | Povolení přenosu brány | Tuto možnost nechte nezaškrtnutou. |

7. Vyberte **OK**.

## <a name="link-the-virtual-networks-to-the-private-dns-zone"></a>Propojení virtuálních sítí s privátní zónou DNS

V této části propojíme virtuální sítě **myVMVNet** a **myAzFwVNet** s privátní zónou DNS **privatelink.Database.Windows.NET** . Tato zóna byla vytvořena při vytváření privátního koncového bodu. 

Aby mohl virtuální počítač a brána firewall přeložit plně kvalifikovaný název domény databáze na jeho adresu privátního koncového bodu, vyžaduje se odkaz. Služba Virtual Network **myPEVNet** se automaticky propojili při vytvoření privátního koncového bodu.

>[!NOTE]
>Pokud virtuální sítě virtuálních počítačů a brány firewall nepřipojíte k privátní zóně DNS, bude virtuální počítač i brána firewall stále schopna vyřešit SQL Server plně kvalifikovaný název domény. Budou přeloženy na veřejnou IP adresu.

1. Na panelu hledání na portálu zadejte **privatelink. Database**.

2. Ve výsledcích hledání vyberte **privatelink.Database.Windows.NET** .

3. V části **Nastavení** vyberte **odkazy virtuální sítě** .

4. Vybrat **+ Přidat**

5. V části **Přidat virtuální síť** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název odkazu | Zadejte **Link-to-myVMVNet**. |
    | **Podrobnosti virtuální sítě** |  |
    | Nevím s ID prostředku virtuální sítě  | Tuto možnost nechte nezaškrtnutou.  |
    | Předplatné | Vyberte své předplatné.    |
    | Virtuální síť | Vyberte **myVMVNet**. |
    | **ROZŠÍŘENÉHO** | |
    | Povolit automatickou registraci | Tuto možnost nechte nezaškrtnutou.    |


6. Vyberte **OK**.

## <a name="configure-an-application-rule-with-sql-fqdn-in-azure-firewall"></a>Konfigurace pravidla aplikace s plně kvalifikovaným názvem domény SQL v Azure Firewall

V této části nakonfigurujte pravidlo aplikace, které umožní komunikaci mezi **myVM** a privátním koncovým bodem pro SQL Server **mydbserver.Database.Windows.NET**. 

Toto pravidlo umožňuje komunikaci přes bránu firewall, kterou jsme vytvořili v předchozích krocích.

1. Na panelu hledání na portálu zadejte **myAzureFirewall**.

2. Ve výsledcích hledání vyberte **myAzureFirewall** .

3. V části **Nastavení** v přehledu **myAzureFirewall** vyberte **pravidla** .

4. Vyberte kartu **kolekce pravidel aplikace** .

5. Vyberte **+ přidat kolekci pravidel aplikace**.

6. V části **přidat kolekci pravidel aplikace** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte **SQLPrivateEndpoint**. |
    | Priorita | Zadejte **100**. |
    | Akce | Zadejte **Allow**. |
    | **Pravidla** |  |
    | **Značky plně kvalifikovaných názvů domén** | |
    | Name  | Ponechte prázdné.  |
    | Typ zdroje | Ponechte výchozí **IP adresu**.    |
    | Zdroj | Ponechte prázdné. |
    | Značky plně kvalifikovaných názvů domén | Ponechte vybrané výchozí nastavení **0**. |
    | **Cílové plně kvalifikované názvy domén** | |
    | Name | Zadejte **SQLPrivateEndpoint**.    |
    | Typ zdroje | Ponechte výchozí **IP adresu**. |
    | Zdroj | Zadejte **10.1.0.0/16**. |
    | Protokol: port | Zadejte **MSSQL: 1433**. |
    | Cílové plně kvalifikované názvy domén | Zadejte **mydbserver.Database.Windows.NET**. |
    |||

7. Vyberte **Přidat**.

## <a name="route-traffic-between-the-virtual-machine-and-private-endpoint-through-azure-firewall"></a>Směrování provozu mezi virtuálním počítačem a soukromým koncovým bodem prostřednictvím Azure Firewall

Nevytvořili jsme partnerský vztah virtuální sítě přímo mezi virtuálními sítěmi **myVMVNet** a **myPEVNet**. **MyVM** virtuálního počítače nemá trasu k privátnímu koncovému bodu, který jsme vytvořili. 

V této části vytvoříme směrovací tabulku s vlastní trasou. 

Trasa odesílá provoz z podsítě **myVM** do adresního prostoru služby Virtual Network **myPEVNet** prostřednictvím Azure firewall.

1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**.

2. Do vyhledávacího pole zadejte **směrovací tabulku** a stiskněte klávesu **ENTER**.

3. Vyberte **směrovací tabulka** a pak vyberte **vytvořit**.

4. Na stránce **vytvořit směrovací tabulku** použijte ke konfiguraci směrovací tabulky následující tabulku:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Podrobnosti o projektu** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**.  |
    | **Podrobnosti o instancích** |  |
    | Oblast | Vyberte **střed USA – jih**. |
    | Name | Zadejte **VMsubnet-to-AzureFirewall**. |
    | Šíření tras brány | Vyberte **Ne**. |

5. Vyberte **Zkontrolovat a vytvořit**. Budete přesměrováni na stránku **Zkontrolovat a vytvořit**, kde Azure ověří konfiguraci.

6. Jakmile se zobrazí zpráva **Ověření proběhlo úspěšně**, vyberte **Vytvořit**.

7. Po dokončení nasazení vyberte **Přejít k prostředku**.

8. V části **Nastavení** vyberte **trasy** .

9. Vyberte **+ Přidat**.

10. Na stránce **Přidat trasu** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název trasy | Zadejte **myVMsubnet-to-privateendpoint**. |
    | Předpona adresy | Zadejte **10.2.0.0/16**.  |
    | Typ dalšího přesměrování | Vyberte **Virtuální zařízení**. |
    | Adresa dalšího segmentu | Zadejte **10.0.0.4**. |

11. Vyberte **OK**.

12. V části **Nastavení** vyberte **podsítě** .

13. Vyberte **+ přidružit**.

14. Na stránce **přidružit podsíť** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Virtuální síť | Vyberte **myVMVNet**. |
    | Podsíť | Vyberte **podsíť VMSubnet**.  |

15. Vyberte **OK**.

## <a name="connect-to-the-virtual-machine-from-your-client-computer"></a>Připojení k virtuálnímu počítači z klientského počítače

Připojte se k virtuálnímu počítači **myVm** z Internetu následujícím způsobem:

1. Na panelu hledání na portálu zadejte **myVm-IP**.

2. Ve výsledcích hledání vyberte **myVM-IP** .

3. Zkopírujte nebo zapište hodnotu v části **IP adresa**.

4. Pokud používáte Windows 10, spusťte následující příkaz pomocí PowerShellu. U ostatních verzí klientů Windows použijte klienta SSH [, jako je například výstup](https://www.putty.org/):

* Položku **username** nahraďte uživatelským jménem správce, které jste zadali při vytváření virtuálního počítače.

* Adresu **IPaddress** nahraďte IP adresou z předchozího kroku.

    ```bash
    ssh username@IPaddress
    ```

5. Zadejte heslo, které jste definovali při vytváření **myVm**

## <a name="access-sql-server-privately-from-the-virtual-machine"></a>Přístup k SQL Server soukromě z virtuálního počítače

V této části se soukromě připojíte k SQL Database pomocí privátního koncového bodu.

1. Zadejte `nslookup mydbserver.database.windows.net`.
    
    Zobrazí se zpráva podobná následující:

    ```bash
    Server:         127.0.0.53
    Address:        127.0.0.53#53

    Non-authoritative answer:
    mydbserver.database.windows.net       canonical name = mydbserver.privatelink.database.windows.net.
    Name:   mydbserver.privatelink.database.windows.net
    Address: 10.2.0.4
    ```

2. Nainstalovat [SQL Server nástroje příkazového řádku](/sql/linux/quickstart-install-connect-ubuntu?view=sql-server-ver15#tools).

3. Spusťte následující příkaz pro připojení k SQL Server. Použijte Správce serveru a heslo, které jste definovali při vytváření SQL Server v předchozích krocích.

* Nahraďte **\<ServerAdmin>** uživatelským jménem správce, které jste zadali během vytváření SQL serveru.

* Nahraďte **\<YourPassword>** heslem správce, které jste zadali během vytváření SQL serveru.

    ```bash
    sqlcmd -S mydbserver.database.windows.net -U '<ServerAdmin>' -P '<YourPassword>'
    ```
4. Po úspěšném přihlášení se zobrazí příkazový řádek SQL. Přejděte na **konec** a ukončete nástroj **Sqlcmd** .

5. Ukončete připojení k **myVM** , a to tak, že zadáte **Exit**.

## <a name="validate-the-traffic-in-azure-firewall-logs"></a>Ověření provozu v protokolech Azure Firewall

1. V Azure Portal vyberte **všechny prostředky** a vyberte pracovní prostor Log Analytics.

2. V části **Obecné** na stránce Log Analytics pracovní prostor vyberte **protokoly** .

3. Vyberte **tlačítko modrý začátek.**

4. V okně **Ukázkové dotazy** vyberte v části **všechny dotazy** možnost **brány firewall** .

5. V části **data protokolu pravidla aplikace** vyberte tlačítko **Spustit** .

6. V poli výstup dotazu protokolu ověřte, že **mydbserver.Database.Windows.NET** je uvedený v části **plně kvalifikovaný název domény** a **SQLPrivateEndpoint** je uveden v části **RuleCollection**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s použitím prostředků hotovi, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Do **vyhledávacího** pole v horní části portálu zadejte **myResourceGroup** a ve výsledcích hledání vyberte **myResourceGroup** .

1. Vyberte **Odstranit skupinu prostředků**.

1. Zadejte **myResourceGroup** pro **typ název skupiny prostředků** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto článku jste prozkoumali různé scénáře, pomocí kterých můžete omezit provoz mezi virtuálním počítačem a soukromým koncovým bodem pomocí Azure Firewall. 

Připojili jste se k virtuálnímu počítači a bezpečně komunikovali databázi prostřednictvím Azure Firewall pomocí privátního odkazu.

Další informace o privátním koncovém bodu najdete v tématu [co je privátní koncový bod Azure](private-endpoint-overview.md).