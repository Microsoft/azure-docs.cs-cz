---
title: Registrace názvy hostitelů v Azure pomocí dynamického DNS | Microsoft Docs
description: Zjistěte, jak nastavit dynamického DNS zaregistrovat názvy hostitelů na serverech DNS.
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
ms.openlocfilehash: bbbce45b7c321fd4934374c76f2a4421b125d46f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Použití dynamického DNS zaregistrovat názvy hostitelů na serveru DNS

[Azure poskytuje překlad](virtual-networks-name-resolution-for-vms-and-role-instances.md) pro virtuální počítače (VM) a instancí rolí. Pokud vaše překlad musí být delší než možnosti poskytované ve výchozím nastavení Azure DNS, můžete zadat vlastní servery DNS. Pomocí vlastní servery DNS poskytuje schopnost přizpůsobit řešení DNS tak, aby odpovídal vašim požadavkům. Potřebujete například přístup k místním prostředkům prostřednictvím řadiči domény služby Active Directory.

Pokud vaše vlastní servery DNS jsou hostované jako virtuální počítače Azure, může předat dotazy název hostitele pro stejnou virtuální síť Azure přeložit názvy hostitelů. Pokud nechcete, aby tuto možnost použít, můžete zaregistrovat vaše názvy hostitelů virtuálních počítačů v serveru DNS pomocí dynamického DNS (DDNS). Azure nemá pověření, které přímo alternativní uspořádání často je třeba vytvořit záznamy v vaše servery DNS. Některé běžné scénáře s alternativami podle:

## <a name="windows-clients"></a>Klienti systému Windows
Klienty připojené k jiné doméně systému Windows pokusí zabezpečená DDNS aktualizace, když se budou spouštět, nebo když se změní své IP adresy. Název DNS je název hostitele a primární příponu DNS. Azure zůstane prázdné primární příponu DNS, ale příponu ve virtuálním počítači, můžete nastavit pomocí [uživatelské rozhraní](https://technet.microsoft.com/library/cc794784.aspx) nebo [prostředí PowerShell](/powershell/module/dnsclient/set-dnsclient).

Klienty připojené k doméně systému Windows registrovat IP adresy s řadičem domény pomocí zabezpečené DDNS. Proces připojení k doméně nastaví primární příponu DNS na straně klienta a vytvoří a udržuje vztah důvěryhodnosti.

## <a name="linux-clients"></a>Klienti Linux
Klienti Linux obecně není zaregistrovat na serveru DNS při spuštění, se předpokládá, že DHCP server dělá. Servery DHCP Azure nemají přihlašovací údaje o registraci záznamů v serveru DNS. Můžete použít nástroj nazvaný `nsupdate`, který je součástí balíčku vazby, odeslání DDNS aktualizací. Protože protokol DDNS je standardizovaný, můžete použít `nsupdate` i když nepoužíváte vazby na serveru DNS.

Můžete použít háky, které jsou k dispozici klientem DHCP pro vytváření a údržbu položka názvu hostitele v serveru DNS. Během cyklu DHCP, klient spustí skripty v */etc/dhcp/dhclient-exit-hooks.d/*. Háky můžete zaregistrovat nové IP adresy pomocí `nsupdate`. Příklad:

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

Můžete také `nsupdate` příkaz k provedení zabezpečené DDNS aktualizací. Například pokud používáte server DNS vytvořit vazbu, páru veřejného a privátního klíče RSA je [generované](http://linux.yyz.us/nsupdate/). DNS server je [nakonfigurované](http://linux.yyz.us/dns/ddns-server.html) s veřejné součástí klíče, takže to můžete ověřit podpis na žádost. K poskytování dvojici klíč k `nsupdate`, použijte `-k` možnost pro DDNS aktualizujete žádost o podepsání.

Pokud používáte server DNS systému Windows, můžete použít ověřování pomocí protokolu Kerberos se `-g` parametr v `nsupdate`, ale není k dispozici v systému Windows verze `nsupdate`. Chcete-li používat protokol Kerberos, použijte `kinit` načíst přihlašovací údaje. For example, můžete načíst přihlašovací údaje z [keytab soubor](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)), pak `nsupdate -g` převezme přihlašovací údaje z mezipaměti.

V případě potřeby můžete přidat příponu vyhledávání DNS pro virtuální počítače. Přípona DNS je uveden v */etc/resolv.conf* souboru. Většina distribucích systému Linux automaticky spravovat obsah tohoto souboru, takže obvykle nelze jej upravovat. Přípona však můžete přepsat pomocí klienta DHCP `supersede` příkaz. Pokud chcete přepsat příponou, přidejte následující řádek na */etc/dhcp/dhclient.conf* souboru:

```
supersede domain-name <required-dns-suffix>;
```
