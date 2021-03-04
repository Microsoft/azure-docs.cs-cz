---
title: Funkce Azure Firewallu
description: Další informace o funkcích Azure Firewall
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: victorh
ms.openlocfilehash: b01a856c71375af507e2bf29297e64a6ce9412e5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741425"
---
# <a name="azure-firewall-features"></a>Funkce Azure Firewallu

[Azure firewall](overview.md) je spravovaná cloudová služba zabezpečení sítě, která chrání vaše prostředky Azure Virtual Network.

![Přehled brány firewall](media/overview/firewall-threat.png)

Azure Firewall obsahuje následující funkce:

- Integrovaná vysoká dostupnost
- Zóny dostupnosti
- Neomezená cloudová škálovatelnost
- Pravidla filtrování plně kvalifikovaných názvů domén aplikací
- Pravidla filtrování síťového provozu
- Značky plně kvalifikovaných názvů domén
- Značky služeb
- Analýza hrozeb
- Podpora pro odchozí SNAT
- Podpora DNAT u příchozích přenosů
- Několik veřejných IP adres
- Protokolování Azure Monitor
- Vynucené tunelování
- Webové kategorie (Preview)
- Certifikace

## <a name="built-in-high-availability"></a>Integrovaná vysoká dostupnost

Je integrovaná vysoká dostupnost, takže se nevyžadují žádné další nástroje pro vyrovnávání zatížení a nemusíte nic konfigurovat.

## <a name="availability-zones"></a>Zóny dostupnosti

Azure Firewall se dá nakonfigurovat během nasazení, aby se zvýšila dostupnost více Zóny dostupnosti. Díky Zóny dostupnosti se dostupnost zvyšuje až 99,99% doby provozu. Další informace najdete v tématu Azure Firewall [smlouva SLA (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). Smlouva SLA o 99,99% provozu se nabízí, když jsou vybrané dvě nebo víc Zóny dostupnosti.

K určité zóně taky můžete přidružit Azure Firewall jenom z důvodů blízkosti, a to pomocí smlouvy SLA pro službu Standard 99,95%.

Pro bránu firewall, která je nasazená v zóně dostupnosti, se neúčtují žádné další náklady. U příchozích a odchozích přenosů dat přidružených k Zóny dostupnosti jsou však přidané náklady. Další informace najdete v tématu [Podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/).

Azure Firewall Zóny dostupnosti jsou k dispozici v oblastech, které podporují Zóny dostupnosti. Další informace najdete v tématu [oblasti, které podporují zóny dostupnosti v Azure](../availability-zones/az-region.md) .

> [!NOTE]
> Zóny dostupnosti lze nakonfigurovat pouze během nasazování. Existující bránu firewall nemůžete nakonfigurovat tak, aby zahrnovala Zóny dostupnosti.

Další informace o Zóny dostupnosti najdete v tématu [oblasti a zóny dostupnosti v Azure](../availability-zones/az-overview.md) .

## <a name="unrestricted-cloud-scalability"></a>Neomezená cloudová škálovatelnost

Bránu Azure Firewall můžete vertikálně škálovat tak, jak to vyžadují změny v síťovém provozu, takže nemusíte platit za dimenzování podle špiček v datovém toku.

## <a name="application-fqdn-filtering-rules"></a>Pravidla filtrování plně kvalifikovaných názvů domén aplikací

Odchozí provoz HTTP/S můžete omezit nebo provoz Azure SQL na zadaný seznam plně kvalifikovaných názvů domény (FQDN), včetně zástupných karet. Tato funkce nevyžaduje ukončení protokolu TLS.

## <a name="network-traffic-filtering-rules"></a>Pravidla filtrování síťového provozu

Můžete centrálně *vytvořit pravidla* *filtrování sítě pomocí* zdrojové a cílové IP adresy, portu a protokolu. Brána Azure Firewall je plně stavová, takže dokáže odlišit legitimní pakety pro různé typy spojení. Pravidla jsou vynucována a protokolována napříč různými předplatnými a virtuálními sítěmi.

## <a name="fqdn-tags"></a>Značky plně kvalifikovaných názvů domén

[Plně kvalifikovaný název domény](fqdn-tags.md) usnadňuje povolení známého síťového provozu služby Azure přes bránu firewall. Řekněme například, že chcete povolit síťové přenosy z webu Windows Update přes bránu firewall. Můžete vytvořit pravidlo aplikace a zahrnout značku webu Windows Update. Teď je možný síťový přenos z webu Windows Update přes vaši bránu firewall.

## <a name="service-tags"></a>Značky služeb

[Značka služby](service-tags.md) představuje skupinu předpon IP adres, které vám pomůžou minimalizovat složitost vytváření pravidel zabezpečení. Nemůžete vytvořit vlastní značku služby ani určit, které IP adresy jsou zahrnuty v rámci značky. Předpony adres zahrnuté ve značce služby spravuje Microsoft, a pokud se adresy změní, automaticky značku služby aktualizuje.

## <a name="threat-intelligence"></a>Analýza hrozeb

Filtrování na základě [logiky hrozeb](threat-intel.md)lze povolit pro bránu firewall pro upozornění a zamítnutí provozu z/do známých škodlivých IP adres a domén. Zdrojem těchto IP adres a domén je kanál analýzy hrozeb Microsoftu.

## <a name="outbound-snat-support"></a>Podpora pro odchozí SNAT

Veškeré IP adresy pro odchozí provoz z virtuálních sítí se překládají na veřejnou IP adresu brány Azure Firewall na základě zdroje (SNAT). Můžete identifikovat a povolit provoz pocházející z vaší virtuální sítě do vzdálených internetových cílů. Azure Firewall nesnat, pokud je cílová IP adresa privátního rozsahu IP adres na [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). 

Pokud vaše organizace používá pro privátní sítě rozsah veřejných IP adres, Azure Firewall na jednu z privátních IP adres firewallu v AzureFirewallSubnet. Azure Firewall **můžete nakonfigurovat tak,** aby nesnat na svůj rozsah veřejných IP adres. Další informace najdete v tématu [Azure firewall rozsahy privátních IP adres SNAT](snat-private-range.md).

## <a name="inbound-dnat-support"></a>Podpora DNAT u příchozích přenosů

Příchozí internetový síťový provoz na veřejnou IP adresu brány firewall se přeloží (překlad cílové síťové adresy) a filtruje na privátní IP adresy ve virtuálních sítích.

## <a name="multiple-public-ip-addresses"></a>Několik veřejných IP adres

Pomocí brány firewall můžete přidružit [několik veřejných IP adres](deploy-multi-public-ip-powershell.md) (až 250).

To umožňuje následující scénáře:

- **DNAT** – můžete přeložit několik standardních instancí portů na servery back-end. Například pokud máte dvě veřejné IP adresy, můžete pro obě IP adresy překládat port TCP 3389 (RDP).
- **SNAT** – pro odchozí připojení SNAT jsou k dispozici další porty, což snižuje potenciál vyčerpání portů SNAT. V tuto chvíli Azure Firewall náhodně vybere zdrojovou veřejnou IP adresu, která se má použít pro připojení. Pokud ve své síti využíváte následné filtrování, musíte povolit všechny veřejné IP adresy přidružené k vaší bráně firewall. Pro zjednodušení této konfigurace zvažte použití [předpony veřejných IP adres](../virtual-network/public-ip-address-prefix.md) .

## <a name="azure-monitor-logging"></a>Protokolování Azure Monitor

Všechny události jsou integrované s Azure Monitor, což umožňuje archivaci protokolů do účtu úložiště, streamování událostí do centra událostí nebo jejich odeslání do protokolů Azure Monitor. Ukázky protokolu Azure Monitor najdete v tématu [protokoly Azure monitor pro Azure firewall](./firewall-workbook.md).

Další informace najdete v tématu [kurz: monitorování Azure firewall protokolů a metrik](./firewall-diagnostics.md). 

Azure Firewall sešit nabízí flexibilní plátno pro Azure Firewall analýzu dat. Můžete ho použít k vytvoření bohatých vizuálních sestav v rámci Azure Portal. Další informace najdete v tématu [monitorování protokolů pomocí Azure firewall sešitu](firewall-workbook.md).

## <a name="forced-tunneling"></a>Vynucené tunelování

Azure Firewall můžete nakonfigurovat tak, aby směroval veškerý provoz vázaný na Internet na určený další segment směrování a nemuseli jít přímo na Internet. Můžete mít například místní hraniční bránu firewall nebo jiné síťové virtuální zařízení (síťové virtuální zařízení) pro zpracování síťového provozu před předáním na Internet. Další informace najdete v tématu [Azure firewall vynucené tunelování](forced-tunneling.md).

## <a name="web-categories-preview"></a>Webové kategorie (Preview)

Webové kategorie správcům umožňují povolit nebo odepřít přístup uživatelů k kategoriím webu, jako jsou například weby hazardních her, weby sociálních médií a další. Webové kategorie jsou zahrnuté ve Azure Firewall Standard, ale je lépe vyladěná v Azure Firewall Premium Preview. Na rozdíl od možností kategorií webu ve standardní SKU, které odpovídají kategorii na základě plně kvalifikovaného názvu domény, skladová položka Premium odpovídá kategorii podle celé adresy URL pro přenos HTTP i HTTPS. Další informace o Azure Firewall Premium Preview najdete v tématu [Azure firewall funkce Premium Preview](premium-features.md).

Pokud například Azure Firewall zachytí požadavek HTTPS pro `www.google.com/news` , je očekávána následující kategorizace: 

- Firewall Standard – bude prověřena pouze část plně kvalifikovaného názvu domény, takže `www.google.com` bude zařazena do kategorie jako *vyhledávací modul*. 

- Firewall Premium – bude prověřena úplná adresa URL, takže `www.google.com/news` bude zařazena do kategorie *zpráv*.

Kategorie jsou seřazené na základě závažnosti v oblasti **zodpovědnosti**, **vysoké šířky pásma**, **používání firmy**, **ztráty produktivity**, **Obecné procházení** a **Nezařazeno do kategorie**.

### <a name="categorization-change"></a>Změna kategorizace

Změnu kategorizace si můžete vyžádat v těchto případech:

 - Zamyslete se plně kvalifikovaný název domény nebo adresa URL v jiné kategorii. 
 
nebo 

- má navrhovanou kategorii pro plně kvalifikovaný název domény (Nezařazeno do kategorie) nebo adresu URL.

Vítá vás žádost o odeslání žádosti [https://aka.ms/azfw-webcategories-request](https://aka.ms/azfw-webcategories-request) .

### <a name="category-exceptions"></a>Výjimky kategorie

Můžete vytvořit výjimky pro pravidla vaší webové kategorie. Vytvořte samostatnou kolekci pravidel povolení nebo odepření s vyšší prioritou v rámci skupiny kolekce pravidel. Můžete například nakonfigurovat kolekci pravidel, která umožňuje `www.linkedin.com` s prioritou 100, s kolekcí pravidel, která zakazuje **sociální sítě** s prioritou 200. Tím se vytvoří výjimka pro předdefinovanou webovou kategorii **sítě pro sociální sítě** .



## <a name="certifications"></a>Certifikace

Azure Firewall je obor platebních karet (PCI), ovládací prvky SOC (Service Organization Controls), Mezinárodní organizace pro normalizaci (ISO) a ICSA Labs splňující předpisy. Další informace najdete v tématu [Azure firewall certifikace dodržování předpisů](compliance-certifications.md).

## <a name="next-steps"></a>Další kroky

- [Logika zpracování pravidel Azure Firewall](rule-processing.md)