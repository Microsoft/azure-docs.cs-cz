---
title: Použití dynamického DNS k registraci názvů hostitelů v Azure | Dokumentace Microsoftu
description: Další informace o nastavení dynamického DNS k registraci názvů hostitelů na vlastní servery DNS.
services: dns
documentationcenter: na
author: subsarma
manager: vitinnan
editor: ''
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: subsarma
ms.openlocfilehash: c2ef842fd62ef060f06536d66387c3facd0627b5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60640374"
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Použití dynamického DNS k registraci názvů hostitelů na serveru DNS

[Azure nabízí řešení názvů](virtual-networks-name-resolution-for-vms-and-role-instances.md) pro virtuální počítače (VM) a instancí rolí. Když vaši rozlišení názvů musí být delší než možnosti dostupné ve výchozím nastavení Azure DNS, můžete zadat vlastní servery DNS. Použití vlastní servery DNS poskytuje schopnost přizpůsobit řešení DNS tak, aby odpovídala konkrétním potřebám. Například můžete potřebovat pro přístup k místním prostředkům přes řadiči domény služby Active Directory.

Když vaše vlastní servery DNS jsou hostované jako virtuální počítače Azure, můžete dál dotazy název hostitele pro stejnou virtuální síť do Azure, a vyřešte názvy hostitelů. Pokud nechcete, aby tato možnost dala použít, můžete zaregistrovat vaše názvy hostitelů virtuálních počítačů v serveru DNS použití dynamického DNS (DDNS). Azure nemá přihlašovací údaje přímo vytvářet záznamy na serverech DNS, takže alternativní opatření jsou často potřeba. Některé běžné scénáře s alternativami podle:

## <a name="windows-clients"></a>Klienti Windows
Bez připojené k doméně Windows pokusí nezabezpečené DDNS aktualizace při prvním spuštění nebo při změně jeho IP adresu. Název DNS je název hostitele primárního serveru DNS. Azure opustí prázdné přípona primárního serveru DNS, ale příponu ve virtuálním počítači, můžete nastavit prostřednictvím [uživatelské rozhraní](https://technet.microsoft.com/library/cc794784.aspx) nebo [Powershellu](/powershell/module/dnsclient/set-dnsclient).

Pomocí zabezpečeného DDNS registrace připojených k doméně klientů Windows jejich IP adresy s řadičem domény. Proces připojení k doméně nastaví primární příponu DNS na klientovi a vytváří a udržuje vztah důvěryhodnosti.

## <a name="linux-clients"></a>Klienti Linux
Klienti Linux obecně není zaregistrovat na serveru DNS při spuštění, předpokládají, že DHCP server dělá. Servery DHCP Azure není nutné přihlašovací údaje o registraci záznamů v serveru DNS. Můžete použít nástroj zvaný `nsupdate`, který je součástí balíčku vazby, můžete odeslat DDNS aktualizace. Protože protokol DDNS je standardizované. můžete použít `nsupdate` i pokud nepoužíváte vazby na serveru DNS.

Můžete použít háky, které jsou k dispozici klient DHCP k vytváření a údržbě položka názvu hostitele na serveru DNS. Během cyklu DHCP klient spustí skripty v */etc/dhcp/dhclient-exit-hooks.d/* . Háky můžete použít k registraci na novou IP adresu pomocí `nsupdate`. Příklad:

```bash
#!/bin/sh
requireddomain=mydomain.local

# only execute on the primary nic
if [ "$interface" != "eth0" ]
then
    return
fi

# When you have a new IP, perform nsupdate
if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
then
   host=`hostname`
   nsupdatecmds=/var/tmp/nsupdatecmds
     echo "update delete $host.$requireddomain a" > $nsupdatecmds
     echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
     echo "send" >> $nsupdatecmds

     nsupdate $nsupdatecmds
fi
```

Můžete také použít `nsupdate` příkaz k provedení zabezpečené DDNS aktualizuje. Například pokud používáte server DNS vytvořit vazbu, pár veřejného a privátního klíče je [generované](http://linux.yyz.us/nsupdate/). DNS server je [nakonfigurované](http://linux.yyz.us/dns/ddns-server.html) s veřejnou částí klíče, proto ji můžete ověřit podpis na žádost. K poskytování dvojice klíčů do `nsupdate`, použijte `-k` možnost, DDNS aktualizujte žádost o podepsání.

Při použití Windows DNS serveru, můžete použít ověřování protokolu Kerberos `-g` parametr `nsupdate`, ale není k dispozici ve verzi Windows `nsupdate`. K použití protokolu Kerberos, použijte `kinit` načíst přihlašovací údaje. For example, můžete načíst přihlašovací údaje [soubor keytab souboru](https://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)), pak `nsupdate -g` převezme přihlašovacích údajů z mezipaměti.

V případě potřeby můžete přidat příponu vyhledávání DNS k vašim virtuálním počítačům. Přípona DNS je zadán v */etc/resolv.conf* souboru. Většině distribucí Linuxu automaticky spravovat obsah tohoto souboru, takže obvykle nemůžete ji upravovat. Přípona však můžete přepsat pomocí klienta DHCP `supersede` příkazu. Pokud chcete přepsat tuto příponu, přidejte následující řádek, který */etc/dhcp/dhclient.conf* souboru:

```
supersede domain-name <required-dns-suffix>;
```
