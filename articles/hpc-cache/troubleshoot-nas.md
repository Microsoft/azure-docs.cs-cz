---
title: Poradce při potížích s cíli úložiště služby Azure HPC Cache NFS
description: Tipy, jak se vyhnout chybám konfigurace a dalším problémům, které mohou způsobit selhání při vytváření cíle úložiště systému souborů NFS
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: rohogue
ms.openlocfilehash: c88ffb9e87bc0688cc87b816efaa8e101e23407c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77652084"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>Poradce při potížích s konfigurací naserveru NAS a cílovými problémy úložiště systému NFS

Tento článek poskytuje řešení pro některé běžné chyby konfigurace a další problémy, které by mohly zabránit Azure HPC Cache z přidání systému úložiště nfs jako cíl úložiště.

Tento článek obsahuje podrobnosti o kontrole portů a povolení root přístupu k systému NAS. Obsahuje také podrobné informace o méně běžných problémech, které mohou způsobit selhání vytvoření cíle úložiště systému nfs.

> [!TIP]
> Před použitím této příručky si přečtěte [požadavky pro cíle úložiště služby NFS](hpc-cache-prereqs.md#nfs-storage-requirements).

Pokud zde není zahrnuto řešení vašeho problému, [otevřete lístek podpory,](hpc-cache-support-ticket.md) aby s vámi služba a podpora společnosti Microsoft mohly spolupracovat na prošetření a vyřešení problému.

## <a name="check-port-settings"></a>Kontrola nastavení portu

Azure HPC Cache potřebuje přístup pro čtení a zápis k několika portům UDP/TCP v back-endovém úložném systému NAS. Ujistěte se, že tyto porty jsou přístupné v systému NAS a také že provoz je povolen pro tyto porty prostřednictvím všech bran firewall mezi systémem úložiště a podsítí mezipaměti. K ověření této konfigurace může být nutné pracovat se správci brány firewall a sítě pro datové centrum.

Porty se liší pro systémy úložiště od různých dodavatelů, proto při nastavování cíle úložiště zkontrolujte požadavky systému.

Obecně platí, že mezipaměť potřebuje přístup k těmto portům:

| Protocol (Protokol) | Port  | Služba  |
|----------|-------|----------|
| Protokol TCP/UDP  | 111   | rpcbind  |
| Protokol TCP/UDP  | 2049  | NFS      |
| Protokol TCP/UDP  | 4045  | nlockmgr řekl: |
| Protokol TCP/UDP  | 4046  | připnout   |
| Protokol TCP/UDP  | 4047  | status   |

Chcete-li se dozvědět konkrétní porty ``rpcinfo`` potřebné pro váš systém, použijte následující příkaz. Tento příkaz níže uvádí porty a formátuje příslušné výsledky v tabulce. (Místo *<storage_IP>* termín použijte IP adresu vašeho systému.)

Tento příkaz můžete vydat z libovolného klienta Linuxu, který má nainstalovanou infrastrukturu systému NFS. Pokud používáte klienta uvnitř podsítě clusteru, může také pomoci ověřit připojení mezi podsítí a systémem úložiště.

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

Ujistěte se, že všechny ``rpcinfo`` porty vrácené dotazem umožňují neomezený provoz z podsítě mezipaměti Azure HPC.

Zkontrolujte tato nastavení jak na samotném NAS, tak na všech firewallech mezi úložným systémem a podsítí mezipaměti.

## <a name="check-root-access"></a>Kontrola přístupu ke kořenům

Azure HPC Cache potřebuje přístup k exportu vašeho systému úložiště k vytvoření cíle úložiště. Konkrétně připojí exporty jako ID uživatele 0.

Různé systémy úložiště používají různé metody, které umožňují tento přístup:

* Linuxové servery obvykle ``no_root_squash`` přidávají ``/etc/exports``do exportované cesty v .
* Systémy NetApp a EMC obvykle řídí přístup pomocí pravidel exportu, která jsou vázána na konkrétní IP adresy nebo sítě.

Pokud používáte pravidla exportu, nezapomeňte, že mezipaměť může používat více různých IP adres z podsítě mezipaměti. Povolit přístup z celého rozsahu možných adres IP podsítě.

Spolupracujte s dodavatelem úložiště NAS a povolte správnou úroveň přístupu pro mezipaměť.

### <a name="allow-root-access-on-directory-paths"></a>Povolit kořenový přístup na cesty adresářů
<!-- linked in prereqs article -->

Pro systémy NAS, které exportují hierarchické adresáře, potřebuje Azure HPC Cache root přístup ke každé úrovni exportu.

Systém může například zobrazit tři exporty, jako jsou tyto:

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

Export ``/ifs/accounting/payroll`` je dítě ``/ifs/accounting``, ``/ifs/accounting`` a je sám ``/ifs``dítě .

Pokud přidáte ``payroll`` export jako cíl úložiště mezipaměti HPC, ``/ifs/`` mezipaměť se skutečně připojí a přistupuje k adresáři mezd. Takže Azure HPC Cache ``/ifs`` potřebuje root přístup ``/ifs/accounting/payroll`` k přístupu k exportu.

Tento požadavek souvisí se způsobem, jakým mezipaměti indexuje soubory a zabraňuje kolizím souborů pomocí popisovačů souborů, které poskytuje úložný systém.

Systém NAS s hierarchickými exporty může poskytnout různé popisovače souborů pro stejný soubor, pokud je soubor načten z různých exportů. Klient může například ``/ifs/accounting`` připojit a ``payroll/2011.txt``získat k němu přístup . Jiný klient ``/ifs/accounting/payroll`` připojí a přistupuje k souboru ``2011.txt``. V závislosti na tom, jak systém úložiště přiřadí popisovače souborů, mohou tito ``<mount2>/payroll/2011.txt`` dva klienti ``<mount3>/2011.txt``obdržet stejný soubor s různými popisovači souborů (jeden pro a jeden pro).

Systém úložiště back-end uchovává interní aliasy pro popisovače souborů, ale Azure HPC Cache nemůže zjistit, které popisovače souborů v jeho rejstříku odkazují na stejnou položku. Takže je možné, že mezipaměť může mít různé zápisy uložené v mezipaměti pro stejný soubor a použít změny nesprávně, protože neví, že se jedná o stejný soubor.

Chcete-li se vyhnout této možné kolizi souborů pro soubory ve více exportech, Azure HPC Cache automaticky připojí nejmělčí dostupné export v cestě (v``/ifs`` příkladu) a používá popisovač souboru daný z tohoto exportu. Pokud více exportů používá stejnou základní cestu, Azure HPC Cache potřebuje root přístup k této cestě.

## <a name="enable-export-listing"></a>Povolit výpis exportu
<!-- link in prereqs article -->

NAS musí uvést své exporty, když azure hpc cache dotazy.

Na většině systémů úložiště nfs, můžete otestovat odesláním následující dotaz z klienta Linux:``showmount -e <storage IP address>``

Pokud je to možné, použijte klienta Linux ze stejné virtuální sítě jako vaši mezipaměť.

Pokud tento příkaz neuvádí exporty, bude mít mezipaměť potíže s připojením k vašemu úložnému systému. Spolupracujte s dodavatelem NAS a povolte exportní zápis.

## <a name="adjust-vpn-packet-size-restrictions"></a>Úprava omezení velikosti paketů VPN
<!-- link in prereqs article -->

Pokud máte VPN mezi mezipamětí a zařízením NAS, může vpn blokovat plnohodnotné 1500bajtové ethernetové pakety. Tento problém může mít, pokud velké výměny mezi NAS a Azure HPC Cache instance nedokončí, ale menší aktualizace fungovat podle očekávání.

Neexistuje jednoduchý způsob, jak zjistit, zda váš systém má tento problém, pokud neznáte podrobnosti o konfiguraci VPN. Zde je několik metod, které vám mohou pomoci zkontrolovat tento problém.

* Pomocí programů pro sledování paketů na obou stranách sítě VPN můžete zjistit, které pakety se úspěšně přenášejí.
* Pokud vaše VPN umožňuje příkazy ping, můžete otestovat odeslání paketu v plné velikosti.

  Spusťte příkaz ping přes VPN na NAS s těmito možnostmi. (Místo<*storage_IP>* hodnotu použijte IP adresu vašeho úložného systému.)

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  Toto jsou možnosti v příkazu:

  * ``-M do``- Netříštit
  * ``-c 1``- Odeslat pouze jeden paket
  * ``-s 1472``- Nastavte velikost datové části na 1472 bajtů. Toto je maximální velikost datové části pro paket 1500 bajtů po účtování režie sítě Ethernet.

  Úspěšná odpověď vypadá takto:

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  Pokud příkaz ping selže s 1472 bajty, možná budete muset nakonfigurovat upínání MSS v síti VPN, aby vzdálený systém správně zjistil maximální velikost rámce. Další informace najdete v dokumentaci k [parametrům brány VPN APsec/IKE.](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec)

## <a name="check-for-acl-security-style"></a>Kontrola stylu zabezpečení acl

Některé systémy NAS používají hybridní styl zabezpečení, který kombinuje seznamy řízení přístupu (ACL) s tradičním zabezpečením POSIX nebo UNIX.

Pokud váš systém hlásí svůj styl zabezpečení jako UNIX nebo POSIX bez zahrnutí zkratky "ACL", tento problém se vás netýká.

Pro systémy, které používají aklů, Azure HPC Cache musí sledovat další hodnoty specifické pro uživatele, aby bylo možné řídit přístup k souborům. To se provádí povolením mezipaměti přístupu. Neexistuje ovládací prvek pro uživatele, který by zapnul mezipaměť pro přístup, ale můžete otevřít lístek podpory a požádat o jeho povolení pro ovlivněné cíle úložiště v systému mezipaměti.

## <a name="next-steps"></a>Další kroky

Pokud máte problém, který nebyl v tomto článku vyřešen, [otevřete lístek podpory](hpc-cache-support-ticket.md) a získejte odbornou pomoc.
