---
title: Použití dynamického DNS k registraci názvů hostitelů v Azure | Microsoft Docs
description: Přečtěte si, jak nastavit dynamickou službu DNS k registraci názvů hostitelů ve vlastních serverech DNS.
services: dns
documentationcenter: na
author: subsarma
manager: vitinnan
editor: ''
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: subsarma
ms.openlocfilehash: ad91eb94aedcdd0e4e715162e3ae064a1d2fb1ea
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98220418"
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Použití dynamického DNS k registraci názvů hostitelů na vlastním serveru DNS

[Azure poskytuje překlad názvů](virtual-networks-name-resolution-for-vms-and-role-instances.md) pro virtuální počítače a instance rolí. V případě, že váš překlad adres potřebuje překročit možnosti poskytované výchozím DNS Azure, můžete poskytnout vlastní servery DNS. Použití vlastních serverů DNS vám dává možnost přizpůsobit vaše řešení DNS tak, aby vyhovovalo vašim konkrétním potřebám. Například může být potřeba získat přístup k místním prostředkům přes řadič domény služby Active Directory.

Když jsou vaše vlastní servery DNS hostované jako virtuální počítače Azure, můžete předávat dotazy na název hostitele pro stejnou virtuální síť do Azure a vyřešit tak názvy hostitelů. Pokud tuto možnost nechcete použít, můžete zaregistrovat názvy hostitelů virtuálních počítačů na serveru DNS pomocí dynamického DNS (DDNS). Azure nemá přihlašovací údaje k přímému vytváření záznamů na serverech DNS, takže je často potřeba alternativní uspořádání. Některé běžné scénáře s dalšími alternativami následují:

## <a name="windows-clients"></a>Klienti Windows
Klienti systému Windows, kteří nejsou připojeni k doméně, se při spuštění pokoušejí nezabezpečené aktualizace DDNS nebo se změní jejich IP adresa. Název DNS je název hostitele a přípona primárního serveru DNS. Azure ponechá příponu primárního serveru DNS prázdnou, ale můžete nastavit příponu na virtuálním počítači prostřednictvím [uživatelského rozhraní](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794784(v=ws.10)) nebo [PowerShellu](/powershell/module/dnsclient/set-dnsclient).

Klienti systému Windows připojené k doméně registrují své IP adresy s řadičem domény pomocí zabezpečeného DDNS. Proces připojení k doméně nastaví primární příponu DNS na klientovi a vytvoří a udržuje vztah důvěryhodnosti.

## <a name="linux-clients"></a>Klienti Linux
Klienti se systémem Linux se většinou neregistrují společně se serverem DNS při spuštění, předpokládá, že je server DHCP. Servery DHCP v Azure nemají přihlašovací údaje k registraci záznamů na serveru DNS. K odeslání aktualizací DDNS můžete použít nástroj s názvem `nsupdate` , který je součástí balíčku BIND. Vzhledem k tomu, že je protokol DDNS standardizovaný, můžete použít `nsupdate` i v případě, že na serveru DNS nepoužíváte BIND.

K vytvoření a údržbě položky hostname na serveru DNS můžete použít zavěšení, které poskytuje klient DHCP. V průběhu cyklu DHCP klient spustí skripty v */etc/DHCP/dhclient-Exit-Hooks.d/*. K registraci nové IP adresy můžete použít háky pomocí `nsupdate` . Například:

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

Pomocí příkazu můžete také `nsupdate` provádět zabezpečené aktualizace DDNS. Pokud například používáte server DNS BIND, je vygenerován pár klíčů veřejného a soukromého klíče ( `http://linux.yyz.us/nsupdate/` ). Server DNS je nakonfigurován ( `http://linux.yyz.us/dns/ddns-server.html` ) s veřejnou částí klíče, aby mohl ověřit podpis na žádosti. Chcete-li zadat dvojici klíčů `nsupdate` , použijte `-k` možnost pro podepsání žádosti o aktualizaci DDNS.

Pokud používáte server DNS systému Windows, můžete použít ověřování pomocí protokolu Kerberos s `-g` parametrem v `nsupdate` , ale není k dispozici ve verzi systému Windows `nsupdate` . K použití protokolu Kerberos použijte `kinit` k načtení přihlašovacích údajů. Můžete například načíst přihlašovací údaje ze [souboru keytab](https://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)a pak z mezipaměti znovu spustit `nsupdate -g` přihlašovací údaje.

V případě potřeby můžete k virtuálním počítačům přidat příponu vyhledávání DNS. Přípona DNS je zadaná v souboru */etc/resolv.conf* . Většina systému Linux distribuce automaticky spravuje obsah tohoto souboru, takže ho většinou nemůžete upravovat. Příponu však můžete přepsat pomocí příkazu klienta DHCP `supersede` . Pokud chcete příponu přepsat, přidejte do souboru */etc/DHCP/dhclient.conf* následující řádek:

```
supersede domain-name <required-dns-suffix>;
```