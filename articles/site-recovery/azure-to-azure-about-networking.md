---
title: O možnostech sítě v zotavení po havárii Azure do Azure pomocí Azure Site Recovery | Microsoft Docs
description: Poskytuje přehled sítě pro replikaci virtuálních počítačů Azure pomocí Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/26/2018
ms.author: sujayt
ms.openlocfilehash: 48be55632d9c1bece3f1a6e4f9ac12a68f9cb7ab
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="about-networking-in-azure-to-azure-replication"></a>O možnostech sítě v Azure do Azure replikace

>[!NOTE]
> Replikace obnovení lokality pro virtuální počítače Azure je aktuálně ve verzi preview.

Tento článek obsahuje síťové pokyny, když jste replikaci a obnovení virtuálních počítačů Azure z jedné oblasti do jiné, pomocí [Azure Site Recovery](site-recovery-overview.md). 

## <a name="before-you-start"></a>Než začnete

Zjistěte, jak Site Recovery poskytuje zotavení po havárii pro [tento scénář](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Typickou síťovou infrastrukturu

Následující diagram znázorňuje typické prostředí Azure pro aplikace spuštěné na virtuálních počítačích Azure:

![prostředí zákazníka](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Pokud používáte Azure ExpressRoute nebo VPN připojení z vaší místní sítě do Azure, prostředí vypadá takto:

![prostředí zákazníka](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Obvykle sítě jsou chráněny pomocí brány firewall a skupiny zabezpečení sítě (Nsg). Brány firewall pomocí adresy URL nebo povolených založenou na protokolu IP můžete řídit připojení k síti. Skupiny Nsg zadejte pravidla, která používají rozsahy IP adres k řízení připojení k síti.

>[!IMPORTANT]
> Pomocí ověřené proxy pro připojení k síti ovládací prvek není podporována službou Site Recovery a replikaci nejde povolit.


## <a name="outbound-connectivity-for-urls"></a>Odchozí připojení pro adresy URL

Pokud používáte proxy server brány firewall založená na adresu URL k řízení odchozí připojení, povolte tyto adresy URL obnovení lokality:


**Adresa URL** | **Podrobnosti**  
--- | ---
*.blob.core.windows.net | Vyžaduje, aby data je možné zapsat do mezipaměti účet úložiště v oblasti zdroje z virtuálního počítače.
login.microsoftonline.com | Vyžaduje se pro autorizaci a ověřování na adresy URL služby Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Vyžaduje, aby komunikace služby Site Recovery může dojít z virtuálního počítače.
*.servicebus.windows.net | Vyžaduje, aby data monitorování a Diagnostika Site Recovery je možné zapsat z virtuálního počítače.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Odchozí připojení pro rozsahy IP adres

Pokud používáte proxy služby založenou na protokolu IP brány firewall nebo pravidla NSG k řízení odchozí připojení, musí být povoleny tyto rozsahy IP.

- Všechny rozsahy IP adres, které odpovídají umístění zdroje.
    - Si můžete stáhnout [rozsahy IP adres](https://www.microsoft.com/download/confirmation.aspx?id=41653).
    - Budete muset povolit tyto adresy tak, aby data je možné zapsat do mezipaměti účet úložiště, z virtuálního počítače.
- Všechny rozsahy IP adres, které odpovídají Office 365 [ověřování a identita koncové body IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).
    - Pokud nové adresy se přidají do rozsahů Office 365 v budoucnu, musíte vytvořit nová pravidla NSG.
- Koncový bod služby Site Recovery IP adresy. Tyto jsou k dispozici v [souboru XML](https://aka.ms/site-recovery-public-ips)a závisí na cílové umístění.
-  Můžete [stáhnout a použít tento skript](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702), aby automaticky vytvoří požadované pravidla v této skupině. 
- Doporučujeme vytvořit požadované pravidla NSG na testovací skupina NSG a ověřte, že před vytvořením pravidel u produkčních NSG neexistují žádné problémy.
- Vytvořit požadovaný počet pravidel NSG, ujistěte se, že vaše předplatné je seznam povolených adres. Podporu kontaktní Azure o zvýšení limitu pravidla NSG v rámci vašeho předplatného.

Rozsahy IP adres jsou následující:

>
   **cíl** | **Obnovení lokality IP** |  **Site Recovery monitorování IP**
   --- | --- | ---
   Východní Asie | 52.175.17.132 | 13.94.47.61
   Jihovýchodní Asie | 52.187.58.193 | 13.76.179.223
   Střed Indie | 52.172.187.37 | 104.211.98.185
   Indie – jih | 52.172.46.220 | 104.211.224.190
   Střed USA – sever | 23.96.195.247 | 168.62.249.226
   Severní Evropa | 40.69.212.238 | 52.169.18.8
   Západní Evropa | 52.166.13.64 | 40.68.93.145
   Východ USA | 13.82.88.226 | 104.45.147.24
   Západní USA | 40.83.179.48 | 104.40.26.199
   Střed USA – jih | 13.84.148.14 | 104.210.146.250
   Střed USA | 40.69.144.231 | 52.165.34.144
   Východní USA 2 | 52.184.158.163 | 40.79.44.59
   Japonsko – východ | 52.185.150.140 | 138.91.1.105
   Japonsko – západ | 52.175.146.69 | 138.91.17.38
   Brazílie – jih | 191.234.185.172 | 23.97.97.36
   Austrálie – východ | 104.210.113.114 | 191.239.64.144
   Austrálie – jihovýchod | 13.70.159.158 | 191.239.160.45
   Střední Kanada | 52.228.36.192 | 40.85.226.62
   Východní Kanada | 52.229.125.98 | 40.86.225.142
   Západní střed USA | 52.161.20.168 | 13.78.149.209
   Západní USA 2 | 52.183.45.166 | 13.66.228.204
   Spojené království – západ | 51.141.3.203 | 51.141.14.113
   Spojené království – jih | 51.140.43.158 | 51.140.189.52
   Spojené království – jih 2 | 13.87.37.4| 13.87.34.139
   Spojené království – sever | 51.142.209.167 | 13.87.102.68
   Korea – střed | 52.231.28.253 | 52.231.32.85
   Korea – jih | 52.231.298.185 | 52.231.200.144
   
   
  

## <a name="example-nsg-configuration"></a>Příklad konfigurace NSG

Tento příklad ukazuje postup konfigurace pravidla NSG pro virtuální počítač k replikaci. 

- Pokud používáte pravidla NSG k řízení odchozí připojení, použijte "Povolit HTTPS odchozí" pravidla pro všechny požadované rozsahy IP adres.
- V příkladu se předpokládá, že umístění zdrojového virtuálního počítače je "Východ USA" a cílové umístění je "střed USA.

### <a name="nsg-rules---east-us"></a>Pravidla NSG - východní USA

1. Vytvoření pravidla, které odpovídají [rozsahů adres IP USA – východ](https://www.microsoft.com/download/confirmation.aspx?id=41653). To je potřeba, aby data je možné zapsat do mezipaměti účet úložiště z virtuálního počítače.
2. Vytvoření pravidel pro všechny rozsahy IP adres, které odpovídají Office 365 [ověřování a identita koncové body IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).
3. Vytvoření pravidla, která odpovídají do cílového umístění:

   **Umístění** | **Obnovení IP adresu serveru** |  **Obnovení monitorování IP adresa**
    --- | --- | ---
   Střed USA | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Pravidla NSG - střed USA 

Tato pravidla jsou vyžadována, takže je možné povolit replikaci z oblasti target zdrojovém oblast post-převzetí služeb při selhání:

* Pravidla, které odpovídají [rozsahy IP centrální USA](https://www.microsoft.com/download/confirmation.aspx?id=41653). Toto jsou požadované, tak, aby data je možné zapsat do mezipaměti účet úložiště z virtuálního počítače.

* Pravidla pro všechny rozsahy IP, které odpovídají Office 365 [ověřování a identita koncové body IP V4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).

* Pravidla, které odpovídají umístění zdroje:
    - Východ USA
    - Lokality obnovení IP adresa: 13.82.88.226
    - Obnovení monitorování IP adresu lokality: 104.45.147.24


## <a name="expressroutevpn"></a>ExpressRoute/VPN 

Pokud máte ExpressRoute nebo VPN připojení mezi místními a umístění Azure, postupujte podle pokynů v této části.

### <a name="forced-tunneling"></a>Vynucené tunelování

Obvykle můžete definovat výchozí trasa (0.0.0.0/0), který vynutí odchozí přenosy z Internetu do procházet skrz místní umístění. To nedoporučujeme. Provoz replikace a komunikace služby Site Recovery neměli ponechat Azure hranic. Řešení je přidat trasy definované uživatelem (udr) pro [tyto rozsahy IP](#outbound-connectivity-for-azure-site-recovery-ip-ranges) tak, aby provoz replikace nelze přejít na místě.

### <a name="connectivity"></a>Připojení 

Postupujte podle následujících pokynů pro připojení mezi cílové umístění a místní umístění:
- Pokud aplikace potřebuje k připojení k místní počítače nebo pokud jsou klienti, kteří připojují k aplikaci z místního přes VPN nebo ExpressRoute, zajistěte, abyste měli alespoň [připojení site-to-site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) mezi vaší cílové oblasti Azure a místního datového centra.

- Pokud očekáváte velké množství přenosu mezi cílových oblast Azure a místního datového centra, měli byste vytvořit další [připojení ExpressRoute](../expressroute/expressroute-introduction.md) mezi cílovou oblast Azure a místního datového centra.

- Pokud chcete zachovat IP adresy pro virtuální počítače po jejich převzetí služeb při selhání, zachovat připojení site na lokality nebo ExpressRoute cílová oblast v odpojeném stavu. Toto je zajistit, neexistuje žádný rozsah kolidovat mezi oblasti zdroj rozsahy IP adres a rozsahů IP cílová oblast.

### <a name="expressroute-configuration"></a>Konfigurace ExpressRoute
Postupujte podle těchto osvědčené postupy pro konfiguraci ExpressRoute:

- Vytvoření okruhu ExpressRoute v oblasti pro zdroj i cíl. Pak musíte vytvořit spojení mezi:
    - Virtuální síť zdroje a místní sítě prostřednictvím okruh ExpressRoute v oblasti zdroje.
    - Cílový virtuální sítě a místní sítě prostřednictvím okruh ExpressRoute v oblasti cíl.


- Jako součást standardní ExpressRoute můžete vytvořit okruhů ve stejné geopolitické oblasti. K vytvoření okruhy ExpressRoute v různých geopolitických oblastí, Azure ExpressRoute Premium je nutné, což zahrnuje přírůstkové náklady. (Pokud už používáte ExpressRoute Premium, že nejsou zpoplatněné.) Další podrobnosti najdete v tématu [dokumentu umístění ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) a [ExpressRoute ceny](https://azure.microsoft.com/pricing/details/expressroute/).

- Doporučujeme použít jiný rozsahy IP adres v zdrojové a cílové oblasti. Okruh ExpressRoute, nebudou moct připojit k dvou virtuálních sítí Azure stejné rozsahů IP adres ve stejnou dobu.

- Můžete vytvářet virtuální sítě pomocí stejné rozsahů IP v obou oblastech a pak vytvořte okruhy ExpressRoute v obou oblastí. V případě převzetí služeb při selhání události odpojte okruhu z virtuální sítě zdroje a připojte je okruh ve virtuální síti cíl.

 >[!IMPORTANT]
 > Pokud primární oblasti se úplně dolů, může selhat operace odpojení. Získání připojení ExpressRoute, nebude možné cílové virtuální síti.

## <a name="next-steps"></a>Další postup
Začněte chránit vaše úlohy [replikovat virtuální počítače Azure](site-recovery-azure-to-azure.md).
