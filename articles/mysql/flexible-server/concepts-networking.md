---
title: Přehled sítí – Azure Database for MySQL flexibilního serveru
description: Seznamte se s možnostmi připojení a sítě v možnosti nasazení flexibilního serveru pro Azure Database for MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/23/2020
ms.openlocfilehash: ec835073a1fe447490f6965fe41478319a47f503
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105106832"
---
# <a name="connectivity-and-networking-concepts-for-azure-database-for-mysql---flexible-server-preview"></a>Koncepce připojení a sítě pro Azure Database for MySQL – flexibilní Server (Preview)

Tento článek popisuje možnosti veřejného a privátního připojení pro váš server. Seznámíte se podrobněji s koncepty sítě pro Azure Database for MySQL flexibilní Server, abyste mohli bezpečně vytvořit server v Azure.

> [!IMPORTANT]
> Azure Database for MySQL – flexibilní Server je ve verzi Preview.

## <a name="choosing-a-networking-option"></a>Výběr možnosti sítě
Máte dvě možnosti sítě pro Azure Database for MySQL flexibilní Server. Možnosti jsou **privátní přístup (Integration VNET)** a **veřejný přístup (povolených IP adres)**. Při vytváření serveru musíte vybrat jednu z možností. 

> [!NOTE]
> Možnost sítě po vytvoření serveru se nedá změnit. 

* **Privátní přístup (Integration VNET)** – flexibilní Server můžete nasadit do [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md). Virtuální sítě Azure poskytují soukromou a zabezpečenou síťovou komunikaci. Prostředky ve virtuální síti můžou komunikovat prostřednictvím privátních IP adres.

   Vyberte možnost integrace virtuální sítě, pokud chcete následující funkce:
   * Připojte se z prostředků Azure ve stejné virtuální síti nebo ve [virtuální síti s partnerským vztahem](../../virtual-network/virtual-network-peering-overview.md) k vašemu flexibilnímu serveru.
   * Pomocí VPN nebo ExpressRoute se připojte z jiných prostředků než Azure k flexibilnímu serveru.
   * Žádný veřejný koncový bod

* **Veřejný přístup (povolených IP adres)** – flexibilní Server je přístupný prostřednictvím veřejného koncového bodu. Veřejný koncový bod je veřejně přeložitelný adresa DNS. Fráze "povolené IP adresy" odkazuje na rozsah IP adres, které se rozhodnete udělit oprávnění k přístupu k serveru. Tato oprávnění se nazývají **pravidla brány firewall**. 

   Metodu veřejného přístupu vyberte, pokud chcete následující funkce:
   * Připojení z prostředků Azure, které nepodporují virtuální sítě
   * Připojení z prostředků mimo Azure, které nejsou připojené přes VPN nebo ExpressRoute 
   * Flexibilní Server má veřejný koncový bod.

Následující vlastnosti se uplatní bez ohledu na to, jestli se rozhodnete použít privátní přístup nebo možnost veřejného přístupu:
* Připojení z povolených IP adres se musí ověřit na serveru MySQL s platnými přihlašovacími údaji.
* [Šifrování připojení](#tls-and-ssl) je k dispozici pro síťový provoz.
* Server má plně kvalifikovaný název domény (FQDN). Pro vlastnost hostname v připojovacích řetězcích doporučujeme použít místo IP adresy plně kvalifikovaný název domény.
* Obě možnosti ovládají přístup na úrovni serveru, ne na úrovni databáze nebo tabulky. Pro řízení databáze, tabulky a dalšího přístupu k objektům byste použili vlastnosti rolí MySQL.


## <a name="private-access-vnet-integration"></a>Privátní přístup (integrace virtuální sítě)
Privátní přístup s integrací Virtual Network (VNET) poskytuje soukromou a zabezpečenou komunikaci pro váš flexibilní Server MySQL.

### <a name="virtual-network-concepts"></a>Koncepty virtuální sítě
Tady je několik konceptů, se kterými se můžete seznámit při používání virtuálních sítí se servery MySQL Flexible.

* **Virtuální síť** – Azure Virtual Network (VNET) obsahuje privátní adresní prostor IP adres, který je nakonfigurovaný pro vaše použití. Další informace o virtuálních sítích Azure najdete v článku [Přehled služby azure Virtual Network](../../virtual-network/virtual-networks-overview.md) .

    Vaše virtuální síť musí být ve stejné oblasti Azure jako flexibilní Server.

* **Delegovaná podsíť** – virtuální síť obsahuje podsítě (dílčí sítě). Podsítě umožňují rozdělit virtuální síť do menších adresních prostorů. Prostředky Azure se nasazují do konkrétních podsítí v rámci virtuální sítě. 

   Flexibilní Server MySQL musí být v podsíti, která je **delegovaná** jenom pro použití jenom MySQL flexibilního serveru. Toto delegování znamená, že danou podsíť můžou využívat pouze flexibilní servery Azure Database for MySQL. V delegované podsíti nemůžou být žádné jiné typy prostředků Azure. Podsíť můžete delegovat přiřazením její vlastnosti delegování jako Microsoft. DBforMySQL/flexibleServers.

* **Skupiny zabezpečení sítě (NSG)** Pravidla zabezpečení ve skupinách zabezpečení sítě umožňují filtrovat typ síťového provozu, který může přecházet do podsítí a síťových rozhraní virtuální sítě. Další informace najdete v článku [Přehled skupiny zabezpečení sítě](../../virtual-network/network-security-groups-overview.md) .

* **Partnerský vztah virtuální sítě** Partnerské vztahy virtuálních sítí umožňují bezproblémové připojení dvou nebo více virtuálních sítí v Azure. Partnerské virtuální sítě se pro účely připojení jeví jako jedna. Přenos dat mezi virtuálními počítači ve virtuálních sítích s navázaným partnerským vztahem používá páteřní infrastrukturu Microsoftu. Provoz mezi klientskou aplikací a flexibilním serverem v virtuální sítě s partnerským vztahem je směrován jenom přes soukromou síť Microsoftu a je izolovaný jenom na tuto síť.

Flexibilní Server podporuje partnerský vztah virtuálních sítí ve stejné oblasti Azure. Partnerský vztah virtuální sítě napříč oblastmi není **podporován**. Další informace najdete v [konceptech partnerských vztahů virtuálních sítí](../../virtual-network/virtual-network-peering-overview.md) .

### <a name="connecting-from-peered-vnets-in-same-azure-region"></a>Připojení ze virtuální sítě s partnerským vztahem ve stejné oblasti Azure
Pokud se klientská aplikace, která se pokouší připojit k flexibilnímu serveru, nachází ve virtuální síti s partnerským vztahem, nemusí být schopná se připojit pomocí flexibilního serveru ServerName, protože nemůže přeložit název DNS flexibilního serveru z virtuální sítě s partnerským vztahem. Tuto chybu můžete vyřešit dvěma způsoby:
* Použít privátní IP adresu (doporučeno pro scénář pro vývoj a testování) – tuto možnost lze použít pro účely vývoje nebo testování. Pomocí nástroje Nslookup můžete zpětně vyhledat privátní IP adresu pro flexibilní servername (plně kvalifikovaný název domény) a použít privátní IP adresu pro připojení z klientské aplikace. Použití privátní IP adresy pro připojení k flexibilnímu serveru se nedoporučuje pro produkční použití, protože se může změnit během plánované nebo neplánované události.
* Použít zónu Privátní DNS (doporučeno pro produkční prostředí) – Tato možnost je vhodná pro produkční účely. Zřizujete [privátní ZÓNU DNS](../../dns/private-dns-getstarted-portal.md) a propojíte ji s klientskou virtuální sítí. V privátní zóně DNS přidáte [záznam a](../../dns/dns-zones-records.md#record-types) pro váš flexibilní Server pomocí jeho privátní IP adresy. Pak můžete použít záznam A a připojit se z klientské aplikace ve virtuální síti s partnerským vztahem k flexibilnímu serveru.

### <a name="connecting-from-on-premises-to-flexible-server-in-virtual-network-using-expressroute-or-vpn"></a>Připojení z místního prostředí k flexibilnímu serveru v Virtual Network pomocí ExpressRoute nebo VPN
Pro úlohy, které vyžadují přístup k flexibilnímu serveru ve virtuální síti z místní sítě, budete potřebovat [ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute/) nebo [VPN](/azure/architecture/reference-architectures/hybrid-networking/vpn/) a virtuální síť [připojená k místnímu](/azure/architecture/reference-architectures/hybrid-networking/)prostředí. V případě tohoto nastavení budete vyžadovat službu DNS pro překládání, pokud se chcete připojit z klientské aplikace (jako je MySQL Workbench) běžící na místní virtuální síti. Tento server DNS zodpovídá za překlad všech dotazů DNS prostřednictvím služby pro přeposílání na úrovni serveru na službu DNS zadanou v Azure [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

Ke správné konfiguraci potřebujete tyto prostředky:

- Místní síť
- Flexibilní Server MySQL zřízený s privátním přístupem (Integration VNet)
- Virtuální síť [připojená k místnímu](/azure/architecture/reference-architectures/hybrid-networking/) prostředí
- Použití [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md) serveru DNS nasazeného v Azure

Pak můžete pomocí flexibilního servername (FQDN) se připojit z klientské aplikace v partnerské virtuální síti nebo v místní síti k flexibilnímu serveru.

### <a name="unsupported-virtual-network-scenarios"></a>Nepodporované scénáře virtuální sítě
* Veřejný koncový bod (nebo veřejná IP adresa nebo DNS) – flexibilní Server nasazený do virtuální sítě nemůže mít veřejný koncový bod.
* Po nasazení flexibilního serveru do virtuální sítě a podsítě ho nemůžete přesunout do jiné virtuální sítě ani podsítě. Virtuální síť nejde přesunout do jiné skupiny prostředků nebo předplatného.
* Velikost podsítě (adresní prostory) nejde zvýšit, pokud v podsíti existují prostředky.
* Partnerský vztah virtuální sítě napříč oblastmi se nepodporuje.

Naučte se, jak povolit privátní přístup (Integration VNET) pomocí [Azure Portal](how-to-manage-virtual-network-portal.md) nebo [Azure CLI](how-to-manage-virtual-network-cli.md).

> [!NOTE]
> Pokud používáte vlastní server DNS, musíte k překladu plně kvalifikovaného názvu domény Azure Database for MySQL flexibilního serveru použít službu DNS pro přeposílání. Další informace najdete [v tématu Překlad názvů, který používá vlastní server DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) .

## <a name="public-access-allowed-ip-addresses"></a>Veřejný přístup (povolené IP adresy)
Mezi charakteristiky metody veřejného přístupu patří:
* Jenom IP adresy, které povolíte, mají oprávnění pro přístup k vašemu flexibilnímu serveru MySQL. Ve výchozím nastavení nejsou povoleny žádné IP adresy. Můžete přidat IP adresy během vytváření serveru nebo později.
* Server MySQL má veřejně přeložitelný název DNS.
* Flexibilní Server není v jedné z vašich virtuálních sítí Azure.
* Síťový provoz do a ze serveru nepřekračuje privátní síť. Provoz používá obecné internetové cesty.

### <a name="firewall-rules"></a>Pravidla brány firewall
Udělení oprávnění k IP adrese se nazývá pravidlo brány firewall. Pokud se pokus o připojení dostane z IP adresy, kterou jste nepovolili, zobrazí se v původním klientovi chyba.


### <a name="allowing-all-azure-ip-addresses"></a>Povolení všech IP adres Azure
Pokud pro vaši službu Azure není k dispozici pevná odchozí IP adresa, můžete zvážit povolení připojení ze všech IP adres datových center Azure.

> [!IMPORTANT]
> Možnost **povolení veřejného přístupu ze služeb Azure a prostředků v rámci Azure** nakonfiguruje bránu firewall tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup jenom na autorizované uživatele.

Naučte se, jak povolit a spravovat veřejný přístup (povolené IP adresy) pomocí [Azure Portal](how-to-manage-firewall-portal.md) nebo [Azure CLI](how-to-manage-firewall-cli.md).


### <a name="troubleshooting-public-access-issues"></a>Řešení potíží s veřejným přístupem
Pokud se přístup k serverové službě Microsoft Azure Database for MySQL nechová podle očekávání, zvažte následující body:

* **Změny v seznamu povolených se zatím neprojevily:** Změny konfigurace brány firewall serveru Azure Database for MySQL se projeví až po dobu pěti minut.

* **Ověřování se nezdařilo:** Pokud uživatel nemá oprávnění k serveru Azure Database for MySQL nebo je použito nesprávné heslo, připojení k serveru Azure Database for MySQL je odepřeno. Při vytváření nastavení brány firewall jsou jenom klienti s příležitostí k pokusu o připojení k vašemu serveru. Každý klient musí stále zadat potřebná zabezpečovací pověření.

* **IP adresa dynamického klienta:** Pokud máte připojení k Internetu s dynamickým IP adresou a máte potíže s připojením přes bránu firewall, můžete vyzkoušet jedno z následujících řešení:

   * Zeptejte se poskytovatele internetových služeb (ISP) na rozsah IP adres přiřazený ke klientským počítačům, které přistupují k serveru Azure Database for MySQL, a pak přidejte rozsah IP adres jako pravidlo brány firewall.
   * Místo toho Získejte statické IP adresy pro klientské počítače a pak přidejte statickou IP adresu jako pravidlo brány firewall.
  
* **Pro formát IPv6 není k dispozici pravidlo brány firewall:** Pravidla brány firewall musí být ve formátu IPv4. Pokud zadáte pravidla brány firewall ve formátu protokolu IPv6, zobrazí se chyba ověřování.


## <a name="hostname"></a>Název hostitele
Bez ohledu na to, jakou možnost sítě zvolíte, doporučujeme při připojování k flexibilnímu serveru vždycky použít plně kvalifikovaný název domény (FQDN) jako název hostitele. IP adresa serveru nezaručuje, že zůstane statická. Použití plně kvalifikovaného názvu domény vám pomůže vyhnout se provádění změn v připojovacím řetězci. 

Příklad
* Doporučil `hostname = servername.mysql.database.azure.com`
* Pokud je to možné, vyhněte se použití `hostname = 10.0.0.4` (privátní adresa) nebo `hostname = 40.2.45.67` (veřejná IP adresa).


## <a name="tls-and-ssl"></a>TLS a SSL
Azure Database for MySQL flexibilní Server podporuje připojení klientských aplikací ke službě MySQL pomocí protokolu TLS (Transport Layer Security). TLS je průmyslový standardní protokol, který zajišťuje šifrovaná síťová připojení mezi databázovým serverem a klientskými aplikacemi. TLS je aktualizovaný protokol SSL (Secure Sockets Layer) (SSL).

Azure Database for MySQL flexibilní Server podporuje pouze šifrovaná připojení pomocí protokolu TLS 1,2 (Transport Layer Security). Všechna příchozí připojení přes protokol TLS 1.0 a TLS 1.1 se zamítnou. Verzi TLS pro připojení k Azure Database for MySQL flexibilnímu serveru nemůžete zakázat ani změnit. Další informace najdete v tématu Jak se [připojit pomocí protokolu SSL/TLS](how-to-connect-tls-ssl.md) . 


## <a name="next-steps"></a>Další kroky
* Informace o povolení privátního přístupu (integrace virtuální sítě) pomocí webu [Azure Portal](how-to-manage-virtual-network-portal.md) nebo [Azure CLI](how-to-manage-virtual-network-cli.md)
* Informace o povolení veřejného přístupu (povolené IP adresy) pomocí webu [Azure Portal](how-to-manage-firewall-portal.md) nebo [Azure CLI](how-to-manage-firewall-cli.md)
* Naučte se [používat protokol TLS](how-to-connect-tls-ssl.md) .
