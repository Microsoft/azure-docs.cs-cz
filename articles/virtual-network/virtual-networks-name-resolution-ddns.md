---
title: Použití dynamického DNS k registraci názvů hostitelů v Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nastavit dynamickou službu DNS tak, aby registrovala názvy hostitelů na vlastních serverech DNS.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60640374"
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Použití dynamického DNS k registraci názvů hostitelů na vlastním serveru DNS

[Azure poskytuje překlad názvů](virtual-networks-name-resolution-for-vms-and-role-instances.md) pro virtuální počítače (VM) a instance rolí. Pokud vaše potřeby překladu názvů překročí možnosti poskytované výchozí službou DNS Azure, můžete poskytnout vlastní servery DNS. Použití vlastních serverů DNS vám dává možnost přizpůsobit řešení DNS tak, aby vyhovovalo vašim specifickým potřebám. Může být například nutné získat přístup k místním prostředkům prostřednictvím řadiče domény služby Active Directory.

Když jsou vaše vlastní servery DNS hostované jako virtuální počítače Azure, můžete předávat dotazy na názvy hostname pro stejnou virtuální síť do Azure a vyřešit názvy hostitelů. Pokud tuto možnost nechcete použít, můžete zaregistrovat názvy hostitelů virtuálních počítačů na serveru DNS pomocí dynamického DNS (DDNS). Azure nemá přihlašovací údaje k přímému vytváření záznamů na serverech DNS, takže jsou často potřeba alternativní uspořádání. Některé běžné scénáře s alternativami následují:

## <a name="windows-clients"></a>Klienti systému Windows
Klienti se systémem Windows, kteří nejsou připojeni k doméně, se pokoušejí o nezabezpečené aktualizace DDNS při jejich spuštění nebo při změně jejich adresy IP. Název DNS je název hostitele plus primární přípona DNS. Azure ponechá primární příponu DNS prázdnou, ale můžete nastavit příponu ve virtuálním počítači, prostřednictvím [uživatelského rozhraní](https://technet.microsoft.com/library/cc794784.aspx) nebo [Prostředí PowerShell](/powershell/module/dnsclient/set-dnsclient).

Klienti systému Windows přilehlí k doméně registrují své IP adresy u řadiče domény pomocí zabezpečeného DDNS. Proces připojení k doméně nastaví primární příponu DNS na straně klienta a vytvoří a udržuje vztah důvěryhodnosti.

## <a name="linux-clients"></a>Linuxoví klienti
Klienti Linuxu se při spuštění obvykle neregistrují u DNS serveru, předpokládají, že to server DHCP dělá. Servery DHCP azure nemají pověření k registraci záznamů na vašem serveru DNS. K odesílání aktualizací `nsupdate`DDNS můžete použít nástroj s názvem , který je součástí balíčku Bind. Vzhledem k tomu, že protokol `nsupdate` DDNS je standardizovaný, můžete jej použít i v případě, že na serveru DNS nepoužíváte vazbu.

Pomocí háčků poskytovaných klientem DHCP můžete vytvořit a udržovat položku názvu hostitele na serveru DNS. Během cyklu DHCP klient spustí skripty v *souboru /etc/dhcp/dhclient-exit-hooks.d/ .* Pomocí háčků můžete zaregistrovat novou IP `nsupdate`adresu pomocí aplikace . Například:

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

Příkaz můžete také `nsupdate` použít k provádění zabezpečených aktualizací DDNS. Pokud například používáte server DNS vazby, je generován [a pár](http://linux.yyz.us/nsupdate/)veřejného a soukromého klíče . Dns server je [nakonfigurován](http://linux.yyz.us/dns/ddns-server.html) s veřejnou součástí klíče, takže může ověřit podpis na požadavku. Chcete-li poskytnout dvojici klíčů , `nsupdate`použijte `-k` možnost pro žádost o aktualizaci DDNS, která má být podepsána.

Pokud používáte server DNS systému Windows, můžete použít `-g` ověřování `nsupdate`protokolem Kerberos s parametrem `nsupdate`v aplikaci , ale není k dispozici ve verzi systému Windows . Chcete-li použít protokol `kinit` Kerberos, použijte k načtení pověření. Můžete například načíst pověření ze [souboru](https://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html) `nsupdate -g` karty klíčů , pak je z mezipaměti vyzvednete.

V případě potřeby můžete do virtuálních počítačů přidat příponu vyhledávání DNS. Přípona DNS je zadána v souboru */etc/resolv.conf.* Většina distribucí Linuxu automaticky spravuje obsah tohoto souboru, takže ho obvykle nemůžete upravovat. Příponu však můžete přepsat pomocí příkazu klienta `supersede` DHCP. Chcete-li příponu přepsat, přidejte do souboru */etc/dhcp/dhclient.conf* následující řádek:

```
supersede domain-name <required-dns-suffix>;
```
