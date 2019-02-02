---
title: Infrastruktura aktualizace Red Hat | Dokumentace Microsoftu
description: Další informace o Red Hat Update Infrastructure pro instance Red Hat Enterprise Linux na vyžádání v Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: jeconnoc
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 1/7/2019
ms.author: borisb
ms.openlocfilehash: 4505dcf5d9407a609bcf97c56835ff186607127d
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55563726"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat Update Infrastructure pro virtuální počítače na vyžádání Red Hat Enterprise Linuxem v Azure
 [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) umožňuje poskytovateli cloudu, jako je Azure, které zrcadlí obsahu úložiště hostované v systému Red Hat, vytvořte vlastní úložiště s týkající se Azure obsahu a ji dejte k dispozici pro virtuální počítače koncového uživatele.

Image Red Hat Enterprise Linux (RHEL) průběžných plateb (PAYG) jsou předem nakonfigurovaný pro přístup k Azure RHUI. Je potřeba žádná další konfigurace. Chcete-li získat nejnovější aktualizace, spusťte `sudo yum update` po RHEL instance je připravený. Tato služba je součástí poplatky za software RHEL průběžné platby.

Další informace pro Image RHEL v Azure, včetně publikování a zásady uchovávání informací jsou k dispozici [tady](./rhel-images.md).

Informace o zásadách podpory Red Hat pro všechny verze systému RHEL můžete najít na [Red Hat Enterprise Linux životního cyklu](https://access.redhat.com/support/policy/updates/errata) stránky.

## <a name="important-information-about-azure-rhui"></a>Důležité informace o Azure RHUI
* Azure RHUI aktuálně podporuje pouze nejnovější podverzi v každé rodině RHEL (počítače RHEL6 nebo RHEL7). Pokud chcete upgradovat instanci virtuálního počítače RHEL připojené k RHUI nejnovější dílčí verzi, spusťte `sudo yum update`.

    Například, pokud zřízení virtuálního počítače z image RHEL 7.4 PAYG a spusťte `sudo yum update`, skončíte se 7.6 virtuální počítač s RHEL (nejnovější dílčí verzi řady RHEL7).

    Chcete-li toto chování vyhnout, musíte sestavit vlastní image, jak je popsáno v [vytvoření a nahrání virtuálního počítače založeného na Red Hat pro Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) článku. Je nutné se připojit k jiné aktualizační infrastruktury ([přímo do Red Hat obsahu serverů pro doručování](https://access.redhat.com/solutions/253273) nebo [serveru Red Hat satelitní](https://access.redhat.com/products/red-hat-satellite)).

* Přístup ke RHUI hostovaných v Azure je součástí ceny image RHEL průběžné platby. Pokud můžete zrušit registraci virtuálního počítače s průběžnými PLATBAMI RHEL od RHUI hostovaných v Azure, který není převést virtuální počítač do typu přineste si – používání vlastní licence (BYOL) virtuálního počítače. Když si zaregistrujete stejný virtuální počítač s jiný zdroj aktualizací, můžou vám být účtovány _nepřímé_ dvakrát poplatky. Vám budou účtovány poprvé poplatek za software RHEL v Azure. Vám budou účtovány podruhé pro předplatných Red Hat, které byly zakoupeny dříve. Pokud je potřeba konzistentně použití aktualizace infrastruktury než RHUI hostovaných v Azure, zvažte vytvoření a nasazení vlastních imagí (BYOL-type). Tento proces je popsán v [vytvoření a nahrání virtuálního počítače založeného na Red Hat pro Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

* Image RHEL SAP PAYG v Azure (RHEL for SAP, RHEL for SAP HANA a RHEL for SAP Business Applications) jsou připojené k vyhrazené RHUI kanály, které zůstávají na konkrétní dílčí verze RHEL podle potřeby k certifikaci pro SAP.

* Přístup ke RHUI hostovaných v Azure je omezený na virtuální počítače v rámci [rozsahy IP adres datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653). Pokud jste připojení přes server proxy veškerý provoz virtuálního počítače přes místní síťové infrastruktury, musíte nastavit trasy definované uživatelem pro virtuální počítače s průběžnými PLATBAMI RHEL pro přístup k Azure RHUI.

### <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS a zamykání verze RHEL virtuálních počítačů
Někteří zákazníci mohou chcete zamknout své virtuální počítače RHEL do některých vydání vedlejší verze RHEL. Verze zámku můžete aktualizací úložiště tak, aby odkazoval na rozšířenou podporu aktualizace úložiště virtuálního počítače RHEL, aby konkrétní podverze. Pomocí následujících pokynů k uzamčení virtuální počítač s RHEL do určité dílčí verze:

>[!NOTE]
> To platí jenom pro RHEL verze, pro které je k dispozici EUS. V době psaní tohoto návodu jedná se o RHEL 7.2 7.6. Další podrobnosti jsou k dispozici na [Red Hat Enterprise Linux životního cyklu](https://access.redhat.com/support/policy/updates/errata) stránky.

1. Zakažte jiné EUS úložišť:
    ```bash
    sudo yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Přidáte EUS úložišť:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Zámek releasever proměnné:
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > RHEL dílčí verze na aktuální vydání vedlejší dojde k uzamčení podle výše uvedených pokynů. Zadejte konkrétní podverzi, pokud chcete upgradovat a uzamknout novější verze, která není na nejnovější verzi. Například `echo 7.5 > /etc/yum/vars/releasever` dojde k uzamčení vaše RHEL verze RHEL 7.5

1. Aktualizovat váš virtuální počítač s RHEL
    ```bash
    sudo yum update
    ```

### <a name="the-ips-for-the-rhui-content-delivery-servers"></a>IP adresy serverů pro doručování obsahu RHUI

RHUI je k dispozici ve všech oblastech, kde jsou k dispozici Image RHEL na vyžádání. Nyní zahrnuje všech veřejných oblastech uvedených na [řídicí panel stavu Azure](https://azure.microsoft.com/status/) stránky, Azure US Government a oblasti Microsoft Azure Germany.

Pokud používáte konfiguraci sítě pro další omezení přístupu z virtuální počítače RHEL s průběžnými PLATBAMI, ujistěte se, že následující IP adresy jsou povoleny pro `yum update` pro práci v závislosti na tom jste v prostředí:


```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="rhui-azure-infrastructure-update"></a>Aktualizace infrastruktury RHUI Azure

V září 2016 jsme nasadili aktualizované RHUI Azure. V dubnu 2017 jsme vypnout staré RHUI Azure. Pokud používáte Image RHEL PAYG (nebo jejich snímky) od září 2016 nebo novější, se automaticky připojujete nové RHUI Azure. Pokud ale máte starší snímky na virtuálních počítačích, musíte ručně aktualizovat svou konfiguraci pro přístup k Azure RHUI, jak je popsáno v následující části.

Nasazení nových serverů Azure RHUI se používají [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). V Traffic Manageru jeden koncový bod (rhui 1.microsoft.cz) můžete využívat jakýkoli virtuální počítač, bez ohledu na oblast.

### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Aktualizovat certifikát s prošlou platností klienta RHUI na virtuálním počítači

Pokud používáte starší image virtuálního počítače RHEL, například RHEL 7.4 (obrázku URN: `RedHat:RHEL:7.4:7.4.2018010506`), dojde k problémům s připojením k RHUI kvůli klientský certifikát SSL vypršela. Chyba se zobrazí může vypadat jako _"partner SSL odmítl vašeho certifikátu, protože platnost"_. Chcete-li tento problém vyřešit, aktualizujte prosím balíček klienta RHUI na virtuálním počítači pomocí následujícího příkazu:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*-microsoft-*'
```

Alternativně systémem `sudo yum update` také aktualizovat balíček certifikátů klienta (v závislosti na vaší verzi RHEL), bez ohledu na "vypršela platnost certifikátu SSL" chyby, zobrazí se pro jiné úložiště. Pokud tato aktualizace je úspěšné, do jiných úložišť RHUI normální spojení mělo obnovit, tak bude moct spustit `sudo yum update` úspěšně.


### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Potíže s připojením k Azure RHUI
Pokud máte potíže s připojením k Azure RHUI z vašeho virtuálního počítače Azure RHEL průběžné platby, postupujte podle těchto kroků:

1. Zkontrolujte konfiguraci role virtuálního počítače pro koncový bod Azure RHUI:

    a. Zkontrolujte, zda `/etc/yum.repos.d/rh-cloud.repo` soubor obsahuje odkaz na `rhui-[1-3].microsoft.com` v `baseurl` z `[rhui-microsoft-azure-rhel*]` část souboru. Pokud tomu tak, že používáte nové RHUI Azure.

    b. Pokud odkazuje na umístění s následujícím vzorem `mirrorlist.*cds[1-4].cloudapp.net`, vyžaduje se aktualizace konfigurace. Použití starého snímku virtuálního počítače a budete muset aktualizovat tak, aby odkazoval na nové RHUI Azure.

1. Přístup ke RHUI hostovaných v Azure je omezený na virtuální počítače v rámci [rozsahy IP adres datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653).

1. Pokud používáte novou konfiguraci, ověření, že virtuální počítač se připojuje z rozsahu IP adres Azure a pořád nemůžete připojit k Azure RHUI, soubor případ podpory s Microsoftem nebo Red Hat.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Postup ruční aktualizace pro používání Azure RHUI serverů
Tento postup je k dispozici pouze pro referenci. Image RHEL PAYG už máte správnou konfiguraci pro připojení k Azure RHUI. Chcete-li ručně aktualizovat konfiguraci pro používání Azure RHUI serverů, proveďte následující kroky:

1. Stáhněte veřejný klíč podpisu pomocí curl.

   ```bash
   curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc
   ```

1. Ověřte platnost staženého klíče.

   ```bash
   gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
   ```

1. Zkontrolujte výstup a potom ověřte, `keyid` a `user ID packet`.

   ```bash
   Version: GnuPG v1.4.7 (GNU/Linux)
   :public key packet:
           version 4, algo 1, created 1446074508, expires 0
           pkey[0]: [2048 bits]
           pkey[1]: [17 bits]
           keyid: EB3E94ADBE1229CF
   :user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
   :signature packet: algo 1, keyid EB3E94ADBE1229CF
           version 4, created 1446074508, md5len 0, sigclass 0x13
           digest algo 2, begin of digest 1a 9b
           hashed subpkt 2 len 4 (sig created 2015-10-28)
           hashed subpkt 27 len 1 (key flags: 03)
           hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
           hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
           hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
           hashed subpkt 30 len 1 (features: 01)
           hashed subpkt 23 len 1 (key server preferences: 80)
           subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
           data: [2047 bits]
   ```

1. Nainstalujte veřejný klíč.

   ```bash
   sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
   sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
   ```

1. Stáhnout, ověřit a instalace klienta Správce balíčků RPM (RPM).

    >[!NOTE]
    >Změna verze balíčku. Pokud ručně připojit k Azure RHUI, získáte nejnovější verzi balíčku klienta pro každou řadu RHEL zřizování nejnovější image z galerie.

   a. Stáhněte.

    - Pro RHEL 6:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.2-74.noarch.rpm
        ```

    - Pro RHEL 7:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.2-74.noarch.rpm
        ```

   b. Ověření.

   ```bash
   rpm -Kv azureclient.rpm
   ```

   c. Zkontrolujte výstup, který se ujistěte, že podpis balíčku je v pořádku.

   ```bash
   azureclient.rpm:
       Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
       Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
       V3 RSA/SHA256 Signature, key ID be1229cf: OK
       MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
   ```

   d. Nainstalujte ot. / min.

    ```bash
    sudo rpm -U azureclient.rpm
    ```

1. Po dokončení, ověřte, že se dá dostat Azure RHUI z virtuálního počítače.

## <a name="next-steps"></a>Další postup
* Vytvoření virtuálního počítače s Red Hat Enterprise Linuxem z image Azure Marketplace s průběžnými PLATBAMI a použít RHUI hostovaných v Azure, přejděte [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/).
* Další informace o Image Red Hat v Azure, přejděte [stránky dokumentace](./rhel-images.md).
* Informace o zásadách podpory Red Hat pro všechny verze systému RHEL můžete najít na [Red Hat Enterprise Linux životního cyklu](https://access.redhat.com/support/policy/updates/errata) stránky.
