---
title: Řešení potíží se svazkem NFSv 4.1 protokolu Kerberos pro Azure NetApp Files | Microsoft Docs
description: Popisuje chybové zprávy a řešení, které vám můžou pomoct při řešení potíží s NFSv 4.1 protokolem Kerberos pro Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/12/2021
ms.author: b-juche
ms.openlocfilehash: 638607da02b1db4842cdc04f86a4fed1860c243f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98134309"
---
# <a name="troubleshoot-nfsv41-kerberos-volume-issues"></a>Řešení potíží se svazkem NFSv 4.1 protokolu Kerberos 

Tento článek popisuje řešení chybových podmínek, které můžete mít při vytváření nebo správě svazků protokolu Kerberos NFSv 4.1. 

## <a name="error-conditions-and-resolutions"></a>Chybové stavy a řešení

|     Chybové stavy    |     Řešení    |
|-|-|
|`Error allocating volume - Export policy rules does not match kerberosEnabled flag` | Azure NetApp Files nepodporuje protokol Kerberos pro NFSv3 svazky. Protokol Kerberos je podporován pouze pro protokol NFSv 4.1.  |
|`This NetApp account has no configured Active Directory   connections`  |  Nakonfigurujte službu Active Directory pro účet NetApp s poli **IP adresa služby KDC** a **název serveru AD**. Pokyny najdete v tématu [konfigurace Azure Portal](configure-kerberos-encryption.md#configure-the-azure-portal) . |
|`Mismatch between KerberosEnabled flag value and ExportPolicyRule's access type parameter values.`  | Azure NetApp Files nepodporuje převod jednoduchého svazku NFSv 4.1 na svazek Kerberos NFSv 4.1 a naopak. |
|`mount.nfs: access denied by server when mounting volume <SMB_SERVER_NAME-XXX.DOMAIN_NAME>/<VOLUME_NAME>` <br>  Příklad: `smb-test-64d9.contoso.com:/nfs41-vol101` | <ol><li> Zajistěte, aby byly záznamy A/PTR správně nastaveny a existovaly ve službě Active Directory pro název serveru `smb-test-64d9.contoso.com` . <br> Pokud se v klientovi NFS `nslookup` `smb-test-64d9.contoso.com` přeloží na IP adresu IP1 (to znamená `10.1.1.68` ), `nslookup` musí se IP1 přeložit jenom na jeden záznam (tj `smb-test-64d9.contoso.com` .). `nslookup` IP1 *nesmí* překládat na více názvů. </li>  <li>Nastavte hodnotu AES-256 pro účet počítače NFS typu `NFS-<Smb NETBIOS NAME>-<few random characters>` v AD pomocí PowerShellu nebo uživatelského rozhraní. <br> Příklady příkazů: <ul><li>`Set-ADComputer <NFS_MACHINE_ACCOUNT_NAME> -KerberosEncryptionType AES256` </li><li>`Set-ADComputer NFS-SMB-TEST-64 -KerberosEncryptionType AES256` </li></ul> </li> <li>Zajistěte, aby byl software NFS klienta, AD a Azure NetApp Files úložiště NFS synchronizovaný mezi sebou a byl v rozmezí 5 minut. </li>  <li>Pomocí příkazu Získejte lístek Kerberos na klienta NFS `kinit <administrator>` .</li> <li>Zkraťte název hostitele klienta NFS na méně než 15 znaků a znovu proveďte připojení sféry. </li><li>Restartujte klienta systému souborů NFS a `rpcgssd` službu následujícím způsobem. Příkaz se může lišit v závislosti na operačním systému.<br> RHEL 7: <br> `service nfs restart` <br> `service rpcgssd restart` <br> CentOS 8: <br> `systemctl enable nfs-client.target && systemctl start nfs-client.target` <br> Ubuntu <br> (Restartujte `rpc-gssd` službu.) <br> `sudo systemctl start rpc-gssd.service` </ul>| 
|`mount.nfs: an incorrect mount option was specified`   | Problém se může vztahovat k problému klienta NFS. Restartujte klienta systému souborů NFS.    |
|`Hostname lookup failed`   | Musíte vytvořit zónu zpětného vyhledávání na serveru DNS a pak přidat záznam PTR hostitelského počítače služby AD v této zóně zpětného vyhledávání. <br> Předpokládejme například, že IP adresa počítače AD je `10.1.1.4` , název hostitele počítače služby Active Directory (jak je nalezen pomocí příkazu hostname) `AD1` a název domény `contoso.com` . Záznam PTR přidaný do zóny zpětného vyhledávání by měl být `10.1.1.4 -> AD1.contoso.com` . |
|`Volume creation fails due to unreachable DNS server`  | K dispozici jsou dvě možná řešení: <br> <ul><li> Tato chyba označuje, že služba DNS není dostupná. Důvodem může být nesprávná IP adresa DNS nebo problém se sítí. Zkontrolujte IP adresu DNS zadanou v připojení AD a ujistěte se, že je IP adresa správná. </li> <li> Ujistěte se, že se AD a svazek nacházejí ve stejné oblasti a ve stejné virtuální síti. Pokud jsou v různých virtuální sítě, ujistěte se, že je mezi těmito dvěma virtuální sítěy navázán partnerský vztah virtuálních sítí. </li></ul> |
|Vytvoření svazku Kerberos NFSv 4.1 se nepovede s chybou, která je podobná následujícímu příkladu: <br> `Failed to enable NFS Kerberos on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". Failed to bind service principal name on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". SecD Error: server create fail join user auth.` |IP adresa služby KDC je chybná a byl vytvořen svazek Kerberos. Aktualizujte IP adresu služby KDC pomocí správné adresy. <br> Po aktualizaci IP adresy služby KDC nebude tato chyba pryč. Svazek je třeba znovu vytvořit. |

## <a name="next-steps"></a>Další kroky  

* [Konfigurace šifrování Kerberos NFSv4.1 pro Azure NetApp Files](configure-kerberos-encryption.md)
