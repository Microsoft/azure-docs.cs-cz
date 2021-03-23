---
title: Řešení potíží s cíli úložiště systému souborů NFS pro mezipaměť HPC Azure
description: Tipy pro předcházení chybám konfigurace a další problémy, které můžou způsobit selhání při vytváření cíle úložiště NFS
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 03/18/2020
ms.author: v-erkel
ms.openlocfilehash: bb17918774d23dbeb2747fa55eefc4956812e254
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775693"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>Řešení potíží s cílovým úložištěm a konfigurací serveru NFS

Tento článek obsahuje řešení některých běžných chyb konfigurace a dalších problémů, které by mohly zabránit mezipaměti prostředí Azure HPC v přidání úložného systému NFS jako cíle úložiště.

Tento článek obsahuje podrobné informace o tom, jak kontrolovat porty a jak povolit kořenový přístup k systému NAS. Obsahuje taky podrobné informace o méně běžných problémech, které by mohly způsobit selhání vytvoření cíle úložiště NFS.

> [!TIP]
> Před použitím tohoto průvodce si přečtěte [předpoklady pro cílení úložiště NFS](hpc-cache-prerequisites.md#nfs-storage-requirements).

Pokud zde řešení problému není zahrnuto, [otevřete prosím lístek podpory](hpc-cache-support-ticket.md) , aby vám služba a podpora Microsoftu mohla spolupracovat, abyste mohli problém prozkoumat a vyřešit.

## <a name="check-port-settings"></a>Kontrolovat nastavení portu

Mezipaměť prostředí Azure HPC potřebuje přístup pro čtení a zápis k několika portům UDP/TCP na back-endovém systému úložiště NAS. Zajistěte, aby tyto porty byly dostupné v systému NAS, a také to, že provoz těchto portů je povolený prostřednictvím všech bran firewall mezi systémem úložiště a podsítí mezipaměti. Pro ověření této konfigurace může být nutné pracovat s bránou firewall a správci sítě pro vaše datové centrum.

Porty se liší pro systémy úložišť od různých dodavatelů, proto při nastavování cíle úložiště ověřte požadavky vašeho systému.

Obecně platí, že mezipaměť potřebuje přístup k těmto portům:

| Protokol | Port  | Služba  |
|----------|-------|----------|
| TCP/UDP  | 111   | rpcbind  |
| TCP/UDP  | 2049  | NFS      |
| TCP/UDP  | 4045  | nlockmgr |
| TCP/UDP  | 4046  | připojeno   |
| TCP/UDP  | 4047  | status   |

Pokud chcete zjistit konkrétní porty potřebné pro váš systém, použijte následující ``rpcinfo`` příkaz. Tento příkaz vypíše porty a naformátuje příslušné výsledky v tabulce. (Použijte IP adresu vašeho systému místo *<storage_IP>* termínu.)

Tento příkaz můžete vydat ze všech klientů se systémem Linux, na kterých je nainstalována infrastruktura systému souborů NFS. Pokud používáte klienta v podsíti clusteru, může vám také pomáhat ověřit připojení mezi podsítí a systémem úložiště.

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

Zajistěte, aby všechny porty vrácené ``rpcinfo`` dotazem umožňovaly neomezený provoz z podsítě mezipaměti HPC Azure.

Tato nastavení ověřte na samotném serveru NAS a také na všech branách firewall mezi systémem úložiště a podsítí mezipaměti.

## <a name="check-root-access"></a>Kontrolovat kořenový přístup

Mezipaměť HPC Azure potřebuje přístup k exportům vašeho systému úložiště, aby bylo možné vytvořit cíl úložiště. Konkrétně připojí exporty jako ID uživatele 0.

Různé systémy úložiště umožňují přístup k tomuto přístupu pomocí různých metod:

* Servery Linux se obecně přidávají ``no_root_squash`` do exportované cesty v ``/etc/exports`` .
* Systémy NetApp a EMC obvykle kontrolují přístup s pravidly exportu, která jsou vázaná na konkrétní IP adresy nebo sítě.

Pokud používáte pravidla exportu, pamatujte, že mezipaměť může použít několik různých IP adres z podsítě mezipaměti. Povolí přístup z celé řady možných IP adres podsítě.

> [!NOTE]
> I když mezipaměť vyžaduje kořenový přístup k back-endovému úložnému systému, můžete omezit přístup pro klienty, kteří se připojují přes mezipaměť. Podrobnosti najdete v tématu [řízení přístupu klienta](access-policies.md#root-squash) .

Spolupracujte se svým dodavatelem úložiště NAS, abyste povolili správnou úroveň přístupu pro mezipaměť.

### <a name="allow-root-access-on-directory-paths"></a>Povolení kořenového přístupu na cestách adresářů
<!-- linked in prereqs article -->

Pro systémy NAS, které exportují hierarchické adresáře, vyžaduje mezipaměť prostředí Azure HPC kořenový přístup k jednotlivým úrovním exportu.

Systém může například zobrazit tři podobné exporty:

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

Export ``/ifs/accounting/payroll`` je podřízenou položkou ``/ifs/accounting`` a ``/ifs/accounting`` je podřízenou položkou ``/ifs`` .

Pokud přidáte ``payroll`` exportovat jako cíl úložiště mezipaměti HPC, mezipaměť ve skutečnosti připojí ``/ifs/`` a přístup k adresáři mzdy z tohoto místa. Takže mezipaměť HPC Azure potřebuje k ``/ifs`` exportu přístup rootem ``/ifs/accounting/payroll`` .

Tento požadavek souvisí s tím, jak mezipaměť indexuje soubory, a zabraňuje kolizím souborů pomocí popisovačů souborů, které poskytuje systém úložiště.

Systém NAS s hierarchickými exporty může pro stejný soubor poskytnout různé obslužné rutiny souborů, pokud je soubor načten z různých exportů. Klient může například připojit ``/ifs/accounting`` soubor a získat k němu přístup ``payroll/2011.txt`` . Další klient připojí ``/ifs/accounting/payroll`` a přistupuje k souboru ``2011.txt`` . V závislosti na tom, jak systém úložiště přiřazuje popisovače souborů, můžou tito dva klienti obdržet stejný soubor s různými popisovači souborů (jeden pro ``<mount2>/payroll/2011.txt`` a jeden pro ``<mount3>/2011.txt`` ).

Back-endové úložné systémy uchovávají interní aliasy pro popisovače souborů, ale mezipaměť prostředí Azure HPC nemůže určit, které popisovače souborů v indexu odkazují na stejnou položku. Proto je možné, že mezipaměť může mít jiné zápisy do mezipaměti pro stejný soubor a změny použít nesprávně, protože neví, že se jedná o stejný soubor.

Aby nedošlo k této možné kolizi souborů v několika exportech, mezipaměť prostředí Azure HPC automaticky připojovat k nejbližšímu dostupnému exportu v cestě ( ``/ifs`` v příkladu) a používá popisovač souborů daný z tohoto exportu. Pokud více exportů používá stejnou základní cestu, Azure HPC cache potřebuje k této cestě přístup root.

<!-- ## Enable export listing

The NAS must list its exports when the Azure HPC Cache queries it.

On most NFS storage systems, you can test this by sending the following query from a Linux client: ``showmount -e <storage IP address>``

Use a Linux client from the same virtual network as your cache, if possible.

If that command doesn't list the exports, the cache will have trouble connecting to your storage system. Work with your NAS vendor to enable export listing.  -->

## <a name="adjust-vpn-packet-size-restrictions"></a>Upravit omezení velikosti paketů sítě VPN
<!-- link in prereqs article and configuration article -->

Pokud máte síť VPN mezi mezipamětí a zařízením NAS, může síť VPN blokovat 1500 paketů Ethernet s plnou velikostí. K tomuto problému může dojít, pokud se velké výměny mezi serverem NAS a instancí Azure HPC cache nedokončily, ale menší aktualizace fungují podle očekávání.

Neexistuje jednoduchý způsob, jak zjistit, jestli má váš systém tento problém, pokud neznáte podrobnosti o konfiguraci sítě VPN. Tady je několik metod, které vám můžou při kontrole tohoto problému.

* Pomocí monitorování paketů na obou stranách sítě VPN můžete zjistit, které přenosy paketů proběhly úspěšně.
* Pokud vaše síť VPN povoluje příkazy příkazu příkazového testu, můžete otestovat odesílání paketů s plnou velikostí.

  Pomocí těchto možností spusťte na serveru NAS příkaz pro zadání příkazu k odeslání. (Místo *<storage_IP>* hodnotu použijte IP adresu systému úložiště.)

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  Jedná se o možnosti v příkazu:

  * ``-M do`` – Nefragmentovat
  * ``-c 1`` – Odeslat jenom jeden paket
  * ``-s 1472`` – Nastavte velikost datové části na 1472 bajtů. Toto je maximální velikost datové části pro 1500 bajt po zúčtování pro režii sítě Ethernet.

  Úspěšná odpověď vypadá takto:

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  Pokud se příkaz if nezdařil z 1472 bajtů, pravděpodobně došlo k potížím s velikostí paketů.

Chcete-li tento problém vyřešit, bude pravděpodobně nutné nakonfigurovat připojení MSS na síti VPN, aby vzdálený systém správně rozpoznal maximální velikost rámce. Další informace najdete v [dokumentaci k parametrům VPN Gateway IPSec/IKE](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) .

V některých případech může pomáhat Změna nastavení jednotky MTU pro mezipaměť prostředí Azure HPC na 1400. Pokud však omezíte jednotku MTU v mezipaměti, musíte také omezit nastavení jednotky MTU pro klienty a back-endové systémy úložiště, které pracují s mezipamětí. Podrobnosti najdete v tématu [Konfigurace dalších nastavení mezipaměti HPC Azure](configuration.md#adjust-mtu-value) .

## <a name="check-for-acl-security-style"></a>Kontrolovat styl zabezpečení seznamu ACL

Některé systémy NAS používají hybridní styl zabezpečení, který kombinuje seznamy řízení přístupu (ACL) s tradičním zabezpečením POSIX nebo UNIX.

Pokud váš systém hlásí svůj styl zabezpečení jako UNIX nebo POSIX bez zahrnutí zkratky "ACL", tento problém vás neovlivní.

Pro systémy, které používají seznamy ACL, musí mezipaměť prostředí Azure HPC sledovat další hodnoty specifické pro uživatele, aby bylo možné řídit přístup k souborům. To se provádí povolením mezipaměti pro přístup. Není k dispozici žádný ovládací prvek pro přístup do mezipaměti, ale můžete otevřít lístek podpory a požádat ho, aby byl povolen pro ovlivněné cíle úložiště v systému cache.

## <a name="next-steps"></a>Další kroky

Pokud máte problém, který se v tomto článku nevyřešil, [otevřete lístek podpory](hpc-cache-support-ticket.md) , který vám umožní získat odbornou pomoc.
