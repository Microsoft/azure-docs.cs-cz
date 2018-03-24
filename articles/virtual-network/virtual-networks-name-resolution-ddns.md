---
title: Pomocí dynamického DNS k registraci názvů hostitelů
description: Tato stránka obsahuje údaje o tom, jak nastavit dynamického DNS zaregistrovat názvy hostitelů na serverech DNS.
services: dns
documentationcenter: na
author: GarethBradshawMSFT
manager: timlt
editor: ''
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: garbrad
ms.openlocfilehash: 5d62c40bfc909915fa222db12413634aa7ce7158
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="using-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Pomocí dynamického DNS zaregistrovat názvy hostitelů na serveru DNS
[Azure poskytuje překlad](virtual-networks-name-resolution-for-vms-and-role-instances.md) pro virtuální počítače (VM) a instancí rolí. Ale pokud vaše překlad musí přejít nad rámec těch, které poskytuje Azure, můžete zadat vlastní servery DNS. To vám umožňuje přizpůsobit řešení DNS tak, aby odpovídal vašim požadavkům. Potřebujete například přístup k místním prostředkům prostřednictvím řadiči domény služby Active Directory.

Pokud vaše vlastní servery DNS jsou hostované jako virtuální počítače Azure může předat dotazy název hostitele pro stejnou virtuální síť Azure přeložit názvy hostitelů. Pokud nechcete použít tuto trasu, můžete zaregistrovat vaše názvy hostitelů virtuálních počítačů v serveru DNS pomocí dynamického DNS.  Azure nemá možnost (například přihlašovací údaje), jsou často potřeba alternativní uspořádání přímo vytvářet záznamy v vaše servery DNS. Tady jsou některé běžné scénáře s alternativy.

## <a name="windows-clients"></a>Klienti systému Windows
Klienty připojené k jiné doméně systému Windows pokus zabezpečená aktualizace dynamického DNS (DDNS), když se budou spouštět nebo když se změní své IP adresy. Název DNS je název hostitele a primární příponu DNS. Azure zůstane prázdné primární příponu DNS, ale můžete tuto nastavit ve virtuálním počítači, pomocí [uživatelského rozhraní](https://technet.microsoft.com/library/cc794784.aspx) nebo [s použitím automatizace popsané tady](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix).

Klienty připojené k doméně systému Windows registrovat IP adresy s řadičem domény pomocí zabezpečené dynamické DNS. Proces připojení k doméně nastaví primární příponu DNS na straně klienta a vytvoří a udržuje vztah důvěryhodnosti.

## <a name="linux-clients"></a>Klienti Linux
Klienti Linux obecně není zaregistrovat na serveru DNS při spuštění, se předpokládá, že DHCP server dělá. Servery DHCP Azure nemají možnost nebo přihlašovací údaje, o registraci záznamů v serveru DNS.  Můžete použít nástroj nazvaný *nsupdate*, který je součástí balíčku vazby, k odeslání DNS dynamické aktualizace. Protože protokol dynamického DNS je standardizovaný, můžete použít *nsupdate* i když nepoužíváte vazby na serveru DNS.

Můžete použít háky, které jsou k dispozici klientem DHCP pro vytváření a údržbu položka názvu hostitele v serveru DNS. Během cyklu DHCP, klient spustí skripty v */etc/dhcp/dhclient-exit-hooks.d/*. To slouží k registraci novou IP adresu pomocí *nsupdate*. Příklad:

        #!/bin/sh
        requireddomain=mydomain.local

        # only execute on the primary nic
        if [ "$interface" != "eth0" ]
        then
            return
        fi

        # when we have a new IP, perform nsupdate
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

        
        

Můžete také *nsupdate* příkaz k provedení aktualizací zabezpečení dynamického DNS. Například pokud používáte server DNS vytvořit vazbu, páru veřejného a privátního klíče RSA je [generované](http://linux.yyz.us/nsupdate/).  DNS server je [nakonfigurované](http://linux.yyz.us/dns/ddns-server.html) s veřejné součástí klíče, takže to můžete ověřit podpis na žádost. Je nutné použít *-k* možnost zadat dvojici klíč k *nsupdate* v pořadí pro dynamické DNS aktualizujete žádost o podepsání.

Pokud používáte server DNS systému Windows, můžete použít ověřování pomocí protokolu Kerberos se *-g* parametr v *nsupdate* (není k dispozici v systému Windows verze *nsupdate*). Chcete-li to provést, použijte *kinit* se načíst přihlašovací údaje (například z [keytab souboru](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)). Potom *nsupdate -g* se vyzvedávat přihlašovací údaje z mezipaměti.

V případě potřeby můžete přidat příponu vyhledávání DNS pro virtuální počítače. Přípona DNS je uveden v */etc/resolv.conf* souboru. Většina distribucích systému Linux automaticky spravovat obsah tohoto souboru, takže obvykle nelze jej upravovat. Přípona však můžete přepsat pomocí klienta DHCP *nahrazují* příkaz. Chcete-li to provést, v */etc/dhcp/dhclient.conf*, přidejte:

        supersede domain-name <required-dns-suffix>;

