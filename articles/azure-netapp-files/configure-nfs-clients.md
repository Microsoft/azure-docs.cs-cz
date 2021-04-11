---
title: Konfigurace klienta NFS pro Azure NetApp Files | Microsoft Docs
description: Popisuje postup konfigurace klientů systému souborů NFS pro použití s Azure NetApp Files.
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
ms.topic: how-to
ms.date: 11/09/2020
ms.author: b-juche
ms.openlocfilehash: e0b86a7014af42f2ffb067c2de797f270a5b1855
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967468"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Konfigurace klienta NFS pro Azure NetApp Files

Konfigurace klienta NFS, která je popsaná v tomto článku, je součástí instalace, když [nakonfigurujete šifrování protokolu Kerberos nfsv 4.1](configure-kerberos-encryption.md) nebo [vytvoříte svazek se dvěma protokoly](create-volumes-dual-protocol.md). K použití s Azure NetApp Files je k dispozici celá řada distribucí systému Linux. Tento článek popisuje konfigurace pro dvě z běžně používaných prostředí: RHEL 8 a Ubuntu 18,04. 

## <a name="requirements-and-considerations"></a>Požadavky a předpoklady  

Bez ohledu na to, jaký systém Linux používáte, jsou potřeba následující konfigurace:

* Nakonfigurujte klienta NTP, aby nedocházelo k problémům s časovým zkosením.
* Nakonfigurujte záznamy DNS klienta Linux pro překlad IP adres.  
    Tato konfigurace musí zahrnovat záznam "A" (dopředného) a záznam PTR (obrácený). 
* V případě připojení k doméně vytvořte účet počítače pro klienta se systémem Linux v cílové službě Active Directory (který se vytvoří během příkazu pro spojení sféry). 
    > [!NOTE] 
    > `$SERVICEACCOUNT`Proměnná použitá v následujících příkazech by měla být uživatelským účtem s oprávněním nebo delegováním k vytvoření účtu počítače v cílové organizační jednotce.

## <a name="rhel-8-configuration"></a>Konfigurace RHEL 8 

Tato část popisuje konfigurace RHEL vyžadované pro šifrování pomocí protokolu Kerberos NFSv 4.1 a duální protokol.  

Příklady v této části používají následující název domény a IP adresu:  

* Název domény: `contoso.com`
* Privátní IP adresa: `10.6.1.4`

### <a name="rhel-8-configuration-if-you-are-using-nfsv41-kerberos-encryption"></a><a name="rhel8_nfsv41_kerberos"></a>Konfigurace RHEL 8, pokud používáte šifrování pomocí protokolu Kerberos NFSv 4.1

1. Nakonfigurujte `/etc/resolv.conf` pomocí správného serveru DNS.  

    Například:  

    `[root@reddoc cbs]# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver 10.6.1.4(private IP)`   

2. Přidejte záznam klienta NFS do serveru DNS pro zónu dopředného a zpětného vyhledávání DNS.

3. K ověření DNS použijte následující příkazy z klienta NFS:   

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`

4. Instalovat balíčky:   

    `yum update`   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony nfs-utils`

5.  Nakonfigurujte klienta NTP.  

    RHEL 8 ve výchozím nastavení používá Chrony. Podle pokynů pro konfiguraci v části [použití `Chrony` sady ke konfiguraci programu NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp).

6.  Připojte se k doméně služby Active Directory:  

    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`

    Například: 

    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`
    
    Ujistěte se, že `default_realm` je v nástroji nastavená zadaná sféra `/etc/krb5.conf` .  V takovém případě jej přidejte do `[libdefaults]` části souboru, jak je znázorněno v následujícím příkladu:
    
    `default_realm = CONTOSO.COM`

7. Restartujte všechny služby systému souborů NFS:  
 
    `systemctl start nfs-*`   
    `systemctl restart rpc-gssd.service`

    Restartování brání chybovému stavu `“mount.nfs: an incorrect mount option was specified”` při připojení Kerberos.

8. Spusťte `kinit` příkaz s uživatelským účtem, abyste získali lístky: 
 
    `sudo kinit $SERVICEACCOUNT@DOMAIN`  

    Například:   

    `sudo kinit ad_admin@CONTOSO.COM`


### <a name="rhel-8-configuration-if-you-are-using-dual-protocol"></a>Konfigurace RHEL 8, pokud používáte duální protokol

Následující kroky jsou volitelné. Postup je třeba provést pouze v případě, že je mapování uživatele použito v klientovi NFS: 

1. [Pokud používáte oddíl nfsv 4.1 šifrování Kerberos](#rhel8_nfsv41_kerberos) , dokončete všechny kroky popsané v konfiguraci RHEL 8.   

2. Do souboru/etc/hosts přidejte statický záznam DNS pro použití plně kvalifikovaného názvu domény (FQDN) pro vaši reklamu místo používání IP adresy v konfiguračním souboru SSSD:  

    `cat /etc/hosts`   
    `10.6.1.4 winad2016.contoso.com`

3. Přidejte k doménám další část k překladu identifikátorů ze serveru AD LDAP:    

    `[root@reddoc cbs]# cat /etc/sssd/sssd.conf`   
    `[sssd]`   
    `domains = contoso.com, contoso-ldap (new entry added for LDAP as id_provider)`   
    `config_file_version = 2`   
    `services = nss, pam, ssh, sudo (ensure nss is present in this list)`   
 
    `[domain/contoso-ldap] (Copy the following lines. Modify as per your domain name.)`   
    `auth_provider = krb5`   
    `chpass_provider = krb5`   
    `id_provider = ldap`   
    `ldap_search_base = dc=contoso,dc=com(your domain)`   
    `ldap_schema = rfc2307bis`   
    `ldap_sasl_mech = GSSAPI`   
    `ldap_user_object_class = user`   
    `ldap_group_object_class = group`   
    `ldap_user_home_directory = unixHomeDirectory`   
    `ldap_user_principal = userPrincipalName`   
    `ldap_account_expire_policy = ad`   
    `ldap_force_upper_case_realm = true`   
    `ldap_user_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_group_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_sasl_authid = REDDOC$ (ensure $ at the end you can get this from “klist -kte” command)`   
    `krb5_server = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `krb5_realm = CONTOSO.COM (domain name in caps)`   
    `krb5_kpasswd = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `use_fully_qualified_names = false`   
 
    `[domain/contoso.com]  (Do not edit or remove any of the following information. This information is automatically generated during the realm join process.)`   
    `ad_domain = contoso.com`   
    `krb5_realm = CONTOSO.COM`   
    `realmd_tags = manages-system joined-with-adcli`   
    `cache_credentials = True`   
    `id_provider = ad`   
    `krb5_store_password_if_offline = True`   
    `default_shell = /bin/bash`   
    `ldap_id_mapping = True`   
    `use_fully_qualified_names = True`   
    `fallback_homedir = /home/%u@%d`   
    `access_provider = ad`   

4. Ujistěte se `/etc/nsswitch.conf` , že máte `sss` položku:   

    `cat /etc/nsswitch.conf`   
    `passwd: sss files systemd`   
    `group: sss files systemd`   
    `netgroup: sss files`   

5. Restartujte `sssd` službu a vymažte mezipaměť:   

    `service sssd stop`   
    `rm -f /var/lib/sss/db/*`   
    `service sssd start`   
 
6. Otestujte, abyste zajistili integraci klienta se serverem LDAP:   

    `[root@red81 cbs]# id ldapuser1`   
    `uid=1234(ldapuser1) gid=1111(ldapgroup1) groups=1111(ldapgroup1)`   

## <a name="ubuntu-configuration"></a>Konfigurace Ubuntu   

Tato část popisuje konfigurace Ubuntu vyžadované pro šifrování pomocí protokolu Kerberos NFSv 4.1 a duální protokol. 

Příklady v této části používají následující název domény a IP adresu:  

* Název domény: `contoso.com`
* Privátní IP adresa: `10.6.1.4`

1. Nakonfigurujte `/etc/resolv.conf` pomocí správného serveru DNS:

    `root@ubuntu-rak:/home/cbs# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver <private IP address of DNS server>`   

2. Přidejte záznam klienta NFS na serveru DNS pro zónu dopředného a zpětného vyhledávání DNS.
 
    K ověření DNS použijte následující příkazy z klienta NFS:

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`   

3. Instalovat balíčky:
 
    `apt-get update`   
    `apt-get install -y realmd packagekit sssd adcli samba-common chrony krb5-user nfs-common`
    
    Po zobrazení výzvy zadejte `$DOMAIN.NAME` `CONTOSO.COM` jako výchozí sféru protokolu Kerberos zadání (například velkými písmeny).

4. Restartujte službu `rpc-gssd.service` : 

    `sudo systemctl start rpc-gssd.service`

5. Ubuntu 18,04 ve výchozím nastavení používá Chrony. Podle pokynů pro konfiguraci v [Ubuntu Bionic: použití Chrony ke konfiguraci NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp).

6. Připojte se k doméně služby Active Directory:   
 
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`
 
    Například:    
    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`

7. `kinit`K získání lístků s uživatelem postupujte takto: 
 
    `sudo kinit $SERVICEACCOUNT`   
 
    Například:    
    `sudo kinit ad_admin`  

### <a name="ubuntu-configuration-if-you-are-using-dual-protocol"></a>Konfigurace Ubuntu, pokud používáte duální protokol  

Následující kroky jsou volitelné.  Postup je třeba provést pouze v případě, že chcete použít mapování uživatele v klientovi NFS:  

1. Spuštěním následujícího příkazu upgradujte nainstalované balíčky:   
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    Následující příklad používá ukázkové hodnoty. Když vás příkaz vyzve k zadání vstupu, měli byste zadat vstup na základě vašeho prostředí. 

    `base dc=contoso,dc=com uri ldap://10.20.0.4:389/ ldap_version 3 rootbinddn cn=admin,cn=Users,dc=contoso,dc=com pam_password ad`   

2. Spusťte následující příkaz pro restartování a povolení služby:

    `sudo systemctl restart nscd && sudo systemctl enable nscd`   

V následujícím příkladu se dotazuje serveru služby AD LDAP z klienta LDAP Ubuntu na uživatele LDAP `‘hari1’` : 

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   


## <a name="next-steps"></a>Další kroky  

* [Vytvoření svazku NFS pro službu Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Vytvoření svazku se dvěma protokoly pro Azure NetApp Files](create-volumes-dual-protocol.md)

