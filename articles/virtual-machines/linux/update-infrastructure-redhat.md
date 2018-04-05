---
title: Red Hat aktualizace infrastruktury | Microsoft Docs
description: Další informace o Red Hat aktualizace infrastruktury pro instance Red Hat Enterprise Linux na vyžádání v Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: timlt
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/10/2017
ms.author: borisb
ms.openlocfilehash: fe354c6c9415c1525527dd6b95d1b3a481b5a2de
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat aktualizace infrastruktury pro na vyžádání Red Hat Enterprise Linux virtuálních počítačů v Azure
 [Red Hat aktualizace infrastruktury](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) umožňuje poskytovatelů cloudu, jako je Azure k zrcadlení obsahu hostované Red Hat úložiště, vytvořte vlastní úložiště s specifické pro Azure obsahu a zpřístupní ji pro virtuální počítače koncového uživatele.

Red Hat Enterprise Linux (RHEL) průběžné platby (srážek daně ze MZDY) Image pocházet předem nakonfigurovaný pro přístup k Azure RHUI. Je potřeba žádná další konfigurace. Chcete-li získat nejnovější aktualizace, spusťte `sudo yum update` po vaší instance RHEL je připraven. Tato služba je součástí softwaru poplatky RHEL srážek daně ze MZDY.

## <a name="important-information-about-azure-rhui"></a>Důležité informace o Azure RHUI
* Azure RHUI aktuálně podporuje pouze nejnovější dílčí verzi v každé skupině RHEL (RHEL6 nebo RHEL7). Chcete-li upgradovat instance virtuálních počítačů systému RHEL připojené k RHUI nejnovější podverzi, spusťte `sudo yum update`.

    Například, pokud zřídit virtuální počítač z bitové kopie systému RHEL 7.2 srážek daně ze MZDY a spusťte `sudo yum update`, v níž se RHEL 7.4 virtuálních počítačů (na nejnovější podverzi řady RHEL7).

    Chcete-li tomu zabránit, budete muset vytvořit vlastní image, jak je popsáno v [vytvoření a nahrání virtuálního počítače na základě Red Hat pro Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) článku. Pak je nutné se připojit k jiné aktualizace infrastruktury ([přímo do Red Hat obsahu doručení servery](https://access.redhat.com/solutions/253273) nebo [serveru Red Hat satelitní](https://access.redhat.com/products/red-hat-satellite)).

* Přístup k Azure hostovaná RHUI je součástí ceny RHEL srážek daně ze MZDY bitové kopie. Pokud jste se zrušit registraci virtuálních počítačů systému RHEL srážek daně ze MZDY z RHUI hostovaných v Azure, není převést virtuální počítač do typu přineste si vlastníte license (BYOL) virtuálního počítače. Když si zaregistrujete stejného virtuálního počítače s jiný zdroj aktualizací, může dojít _nepřímých_ dvakrát poplatky. Že se vám účtovat pro poplatek softwaru Azure RHEL poprvé. Při druhém Red Hat odběry, které byly zakoupeny dříve jste účtovat. Pokud potřebujete konzistentně použití aktualizace infrastruktury než RHUI hostovaných v Azure, zvažte vytvoření a nasazení vlastních bitových kopií (BYOL-type). Tento proces je popsán v [vytvoření a nahrání virtuálního počítače na základě Red Hat pro Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

* Dvě třídy RHEL srážek daně ze MZDY obrázků v Azure (RHEL pro SAP HANA) a RHEL pro SAP podnikové aplikace jsou připojené k vyhrazené RHUI kanály, které zůstanou na konkrétní verze RHELU menší podle potřeby pro certifikaci SAP. 

* Přístup k Azure hostovaná RHUI je omezený na virtuálních počítačích v rámci [rozsahy IP adres Azure datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Pokud jste proxy všechny přenosy virtuálních počítačů prostřednictvím místní síťové infrastruktury, možná budete muset nastavit trasy definované uživatelem pro virtuální počítače RHEL srážek daně ze MZDY pro přístup k Azure RHUI.

### <a name="the-ips-for-the-rhui-content-delivery-servers"></a>IP adresy pro servery RHUI doručování obsahu

RHUI je k dispozici ve všech oblastech, kde jsou k dispozici na vyžádání RHEL bitové kopie. Aktuálně zahrnuje všechny veřejné oblasti uvedené na [řídicí panel Azure stav](https://azure.microsoft.com/status/) stránku, Azure US Government a oblasti Microsoft Azure v Německu. 

Pokud používáte konfiguraci sítě a dál omezit přístup z virtuálních počítačů systému RHEL srážek daně ze MZDY, ujistěte se, že následující adresy IP jsou povoleny pro `yum update` pro práci v závislosti na prostředí můžete začít: 

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

V září 2016 jsme nasadili aktualizované RHUI Azure. V dubna 2017 jsme vypnout staré RHUI Azure. Pokud používáte Image RHEL srážek daně ze MZDY (nebo jejich snímky) od září 2016 nebo novější, automaticky se připojujete k nové RHUI Azure. Pokud však máte starší snímky na virtuální počítače, budete muset ručně aktualizovat své konfiguraci pro přístup k Azure RHUI, jak je popsáno v následující části.

Nové servery Azure RHUI se nasadí s [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). V Traffic Manageru jeden koncový bod (rhui 1.microsoft.cz) lze použít všechny virtuálním počítačem, bez ohledu na oblast. 

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Řešení potíží s připojením k Azure RHUI
Pokud máte potíže s připojením k Azure RHUI ze svého virtuálního počítače Azure RHEL srážek daně ze MZDY, postupujte takto:

1. Zkontrolujte konfiguraci virtuálních počítačů pro koncový bod Azure RHUI:

    a. Zkontrolujte, zda `/etc/yum.repos.d/rh-cloud.repo` soubor obsahuje odkaz na `rhui-[1-3].microsoft.com` v `baseurl` z `[rhui-microsoft-azure-rhel*]` část souboru. Pokud k tomu, že používáte novou RHUI Azure.

    b. Odkazuje na umístění s následující vzor `mirrorlist.*cds[1-4].cloudapp.net`, je vyžadována aktualizace konfigurace. Používáte staré snímek virtuálního počítače a budete muset aktualizovat tak, aby odkazoval na nový RHUI Azure.

2. Přístup k Azure hostovaná RHUI je omezený na virtuální počítače v rámci [IP rozsahy datové centrum Azure] (https://www.microsoft.com/download/details.aspx?id=41653).
 
3. Pokud používáte novou konfiguraci, ověřili virtuální počítač se připojuje z rozsahu Azure IP a pořád nemůžete připojit k Azure RHUI, soubor případu podpory společnosti Microsoft nebo Red Hat.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Postup ruční aktualizace pro používání Azure RHUI serverů
Tento postup se poskytuje jenom pro referenci. Bitové kopie systému RHEL srážek daně ze MZDY už máte správnou konfiguraci pro připojení k Azure RHUI. Ručně aktualizovat konfiguraci pro používání Azure RHUI serverů, proveďte následující kroky:

1. Stáhněte si podpis veřejného klíče prostřednictvím curl.

   ```bash
   curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
   ```

2. Ověřte platnost stažené klíče.

   ```bash
   gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
   ```

3. Zkontrolujte výstup a potom ověřte `keyid` a `user ID packet`.

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

4. Nainstalujte veřejný klíč.

   ```bash
   sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
   sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
   ```

5. Stáhnout, ověření a instalace klienta Manager balíček RPM (RPM).
    
    >[!NOTE]
    >Verze balíčku změnit. Pokud ručně připojit k Azure RHUI, můžete najít nejnovější verzi balíčku klienta pro každou skupinu RHEL podle zřizování nejnovější bitové kopie z galerie.
  
   a. Stáhněte. 
   
    - Pro RHEL 6:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.1-32.noarch.rpm 
        ```
    
    - Pro RHEL 7:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.1-19.noarch.rpm  
        ```

   b. Ověření.

   ```bash
   rpm -Kv azureclient.rpm
   ```

   c. Zkontrolujte výstup zajistit, že podpis balíčku je OK.

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

6. Po dokončení, ověřte, že vám přístup Azure RHUI z virtuálního počítače.

## <a name="next-steps"></a>Další postup
Chcete-li vytvořit virtuální počítač Red Hat Enterprise Linux z Azure Marketplace srážek daně ze MZDY bitové kopie a použít RHUI hostovaných v Azure, přejděte na [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). 
