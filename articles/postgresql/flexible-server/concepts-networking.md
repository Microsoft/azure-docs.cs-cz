---
title: Přehled sítí – Azure Database for PostgreSQL – flexibilní Server
description: Seznamte se s možnostmi připojení a sítě v možnosti nasazení flexibilního serveru pro Azure Database for PostgreSQL
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 38dd103189446e287f3d62d93344ed89a364d238
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708777"
---
# <a name="networking-overview---azure-database-for-postgresql---flexible-server"></a>Přehled sítí – Azure Database for PostgreSQL – flexibilní Server

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

Tento článek popisuje princip připojení a sítě pro Azure Database for PostgreSQL flexibilní Server. 

## <a name="choosing-a-networking-option"></a>Výběr možnosti sítě
Máte dvě možnosti sítě pro Azure Database for PostgreSQL flexibilní Server. Možnosti jsou **privátní přístup (Integration VNET)** a **veřejný přístup (povolených IP adres)**. Při vytváření serveru musíte vybrat jednu z možností. 

> [!NOTE]
> Možnost sítě po vytvoření serveru se nedá změnit. 

* **Privátní přístup (Integration VNET)** – flexibilní Server můžete nasadit do [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md). Virtuální sítě Azure poskytují soukromou a zabezpečenou síťovou komunikaci. Prostředky ve virtuální síti můžou komunikovat prostřednictvím privátních IP adres.

   Vyberte možnost integrace virtuální sítě, pokud chcete následující funkce:
   * Připojení z prostředků Azure ve stejné virtuální síti k flexibilnímu serveru pomocí privátních IP adres
   * Pomocí VPN nebo ExpressRoute se připojte z jiných prostředků než Azure k flexibilnímu serveru.
   * Flexibilní Server nemá žádný veřejný koncový bod.

* **Veřejný přístup (povolených IP adres)** – flexibilní Server je přístupný prostřednictvím veřejného koncového bodu. Veřejný koncový bod je veřejně přeložitelný adresa DNS. Fráze "povolené IP adresy" odkazuje na rozsah IP adres, které se rozhodnete udělit oprávnění k přístupu k serveru. Tato oprávnění se nazývají **pravidla brány firewall**. 

   Metodu veřejného přístupu vyberte, pokud chcete následující funkce:
   * Připojení z prostředků Azure, které nepodporují virtuální sítě
   * Připojení z prostředků mimo Azure, které nejsou připojené přes VPN nebo ExpressRoute 
   * Flexibilní Server má veřejný koncový bod.

Následující vlastnosti se uplatní bez ohledu na to, jestli se rozhodnete použít privátní přístup nebo možnost veřejného přístupu:
* Připojení z povolených IP adres se musí ověřit na serveru PostgreSQL s platnými přihlašovacími údaji.
* [Šifrování připojení](#tls-and-ssl) je k dispozici pro síťový provoz.
* Server má plně kvalifikovaný název domény (FQDN). Pro vlastnost hostname v připojovacích řetězcích doporučujeme použít místo IP adresy plně kvalifikovaný název domény.
* Obě možnosti ovládají přístup na úrovni serveru, ne na úrovni databáze nebo tabulky. Můžete použít vlastnosti rolí PostgreSQL k řízení databáze, tabulky a dalších přístupů k objektům.


## <a name="private-access-vnet-integration"></a>Privátní přístup (integrace virtuální sítě)
Privátní přístup s integrací Virtual Network (VNET) poskytuje soukromou a zabezpečenou komunikaci pro váš PostgreSQL flexibilní Server.

### <a name="virtual-network-concepts"></a>Koncepty virtuální sítě
Tady je několik konceptů, se kterými se můžete seznámit při používání virtuálních sítí s PostgreSQL flexibilními servery.

* **Virtuální síť** – Azure Virtual Network (VNET) obsahuje privátní adresní prostor IP adres, který je nakonfigurovaný pro vaše použití. Další informace o virtuálních sítích Azure najdete v článku [Přehled služby azure Virtual Network](../../virtual-network/virtual-networks-overview.md) .

    Vaše virtuální síť musí být ve stejné oblasti Azure jako flexibilní Server.


* **Delegovaná podsíť** – virtuální síť obsahuje podsítě (dílčí sítě). Podsítě umožňují rozdělit virtuální síť do menších adresních prostorů. Prostředky Azure se nasazují do konkrétních podsítí v rámci virtuální sítě. 

   Váš PostgreSQL flexibilní Server musí být v podsíti, která je **delegovaná** jenom pro PostgreSQL flexibilní použití serveru. Toto delegování znamená, že tuto podsíť můžou používat jenom Azure Database for PostgreSQL flexibilní servery. V delegované podsíti nemůžou být žádné jiné typy prostředků Azure. Podsíť můžete delegovat přiřazením její vlastnosti delegování jako Microsoft. DBforPostgreSQL/flexibleServers.

* **Skupiny zabezpečení sítě (NSG)** Pravidla zabezpečení ve skupinách zabezpečení sítě umožňují filtrovat typ síťového provozu, který může přecházet do podsítí a síťových rozhraní virtuální sítě. Další informace najdete v článku [Přehled skupiny zabezpečení sítě](../../virtual-network/network-security-groups-overview.md) .


### <a name="unsupported-virtual-network-scenarios"></a>Nepodporované scénáře virtuální sítě
* Veřejný koncový bod (nebo veřejná IP adresa nebo DNS) – flexibilní Server nasazený do virtuální sítě nemůže mít veřejný koncový bod.
* Po nasazení flexibilního serveru do virtuální sítě a podsítě ho nemůžete přesunout do jiné virtuální sítě ani podsítě. Virtuální síť nejde přesunout do jiné skupiny prostředků nebo předplatného.
* Velikost podsítě (adresní prostory) nejde zvýšit, pokud v podsíti existují prostředky.
* Partnerský vztah virtuální sítě napříč oblastmi se nepodporuje.

Přečtěte si, jak vytvořit flexibilní Server s privátním přístupem (Integration VNet) v [Azure Portal](how-to-manage-virtual-network-portal.md) nebo [v rozhraní příkazového řádku Azure](how-to-manage-virtual-network-cli.md).

## <a name="public-access-allowed-ip-addresses"></a>Veřejný přístup (povolené IP adresy)
Mezi charakteristiky metody veřejného přístupu patří:
* Oprávnění pro přístup k PostgreSQL flexibilnímu serveru mají jenom IP adresy, které povolíte. Ve výchozím nastavení nejsou povoleny žádné IP adresy. Můžete přidat IP adresy během vytváření serveru nebo později.
* Váš server PostgreSQL má veřejně přeložitelný název DNS.
* Flexibilní Server není v jedné z vašich virtuálních sítí Azure.
* Síťový provoz do a ze serveru nepřekračuje privátní síť. Provoz používá obecné internetové cesty.

### <a name="firewall-rules"></a>Pravidla brány firewall
Udělení oprávnění k IP adrese se nazývá pravidlo brány firewall. Pokud se pokus o připojení dostane z IP adresy, kterou jste nepovolili, zobrazí se v původním klientovi chyba.

Naučte se, jak vytvořit flexibilní Server s veřejným přístupem (povolenými IP adresami) v [Azure Portal](how-to-manage-firewall-portal.md) nebo [v rozhraní příkazového řádku Azure](how-to-manage-firewall-cli.md).


### <a name="allowing-all-azure-ip-addresses"></a>Povolení všech IP adres Azure
Pokud pro vaši službu Azure není k dispozici pevná odchozí IP adresa, můžete zvážit povolení připojení ze všech IP adres datových center Azure.

> [!IMPORTANT]
> Možnost **povolení veřejného přístupu ze služeb Azure a prostředků v rámci Azure** nakonfiguruje bránu firewall tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup jenom na autorizované uživatele.

### <a name="troubleshooting-public-access-issues"></a>Řešení potíží s veřejným přístupem
Vezměte v úvahu následující body, pokud se přístup k Microsoft Azure databázi pro službu serveru PostgreSQL nechová podle očekávání:

* **Změny v seznamu povolených se zatím neprojevily:** Změny konfigurace brány firewall serveru Azure Database for PostgreSQL se projeví až po dobu pěti minut.

* **Ověřování se nezdařilo:** Pokud uživatel nemá oprávnění k serveru Azure Database for PostgreSQL nebo je použito nesprávné heslo, připojení k serveru Azure Database for PostgreSQL je odepřeno. Při vytváření nastavení brány firewall jsou jenom klienti s příležitostí k pokusu o připojení k vašemu serveru. Každý klient musí stále zadat potřebná zabezpečovací pověření.

* **IP adresa dynamického klienta:** Pokud máte připojení k Internetu s dynamickým IP adresou a máte potíže s připojením přes bránu firewall, můžete vyzkoušet jedno z následujících řešení:

   * Zeptejte se poskytovatele internetových služeb (ISP) na rozsah IP adres přiřazený ke klientským počítačům, které přistupují k serveru Azure Database for PostgreSQL, a pak přidejte rozsah IP adres jako pravidlo brány firewall.
   * Místo toho Získejte statické IP adresy pro klientské počítače a pak přidejte statickou IP adresu jako pravidlo brány firewall.


## <a name="hostname"></a>Název hostitele
Bez ohledu na to, jakou možnost sítě zvolíte, doporučujeme při připojování k flexibilnímu serveru vždycky použít plně kvalifikovaný název domény (FQDN) jako název hostitele. IP adresa serveru nezaručuje, že zůstane statická. Použití plně kvalifikovaného názvu domény vám pomůže vyhnout se provádění změn v připojovacím řetězci. 

Příklad
* Doporučil `hostname = servername.postgres.database.azure.com`
* Pokud je to možné, vyhněte se použití `hostname = 10.0.0.4` (privátní adresa) nebo `hostname = 40.2.45.67` (veřejná adresa).


## <a name="tls-and-ssl"></a>TLS a SSL
Azure Database for PostgreSQL – flexibilní Server podporuje připojení klientských aplikací ke službě PostgreSQL pomocí protokolu TLS (Transport Layer Security). TLS je průmyslový standardní protokol, který zajišťuje šifrovaná síťová připojení mezi databázovým serverem a klientskými aplikacemi. TLS je aktualizovaný protokol SSL (SSL (Secure Sockets Layer)).

Azure Database for PostgreSQL – flexibilní Server podporuje pouze šifrovaná připojení pomocí Transport Layer Security. Budou odepřena všechna příchozí připojení s TLS 1,0 a TLS 1,1. 

## <a name="next-steps"></a>Další kroky
* Přečtěte si, jak vytvořit flexibilní Server s **privátním přístupem (Integration VNET)** v [Azure Portal](how-to-manage-virtual-network-portal.md) nebo [v rozhraní příkazového řádku Azure](how-to-manage-virtual-network-cli.md).
* Naučte se, jak vytvořit flexibilní Server s **veřejným přístupem (povolenými IP adresami)** v [Azure Portal](how-to-manage-firewall-portal.md) nebo [v rozhraní příkazového řádku Azure](how-to-manage-firewall-cli.md).
