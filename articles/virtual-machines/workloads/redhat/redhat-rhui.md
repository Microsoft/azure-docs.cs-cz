---
title: Infrastruktura aktualizace Red Hat | Dokumentace Microsoftu
description: Další informace o Red Hat Update Infrastructure pro instance Red Hat Enterprise Linux na vyžádání v Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: dc4762cbda5ad2877d2d69953d2514dea17c8b46
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368902"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat Update Infrastructure pro virtuální počítače na vyžádání Red Hat Enterprise Linuxem v Azure
 RHUI ( [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) ) umožňuje poskytovatelům cloudu, jako je Azure, zrcadlit obsah úložiště hostovaného na Red Hat, vytvářet vlastní úložiště pomocí obsahu specifického pro Azure a zpřístupňuje je pro virtuální počítače koncových uživatelů.

Image Red Hat Enterprise Linux (RHEL) průběžných plateb (PAYG) jsou předem nakonfigurovaný pro přístup k Azure RHUI. Je potřeba žádná další konfigurace. Chcete-li získat nejnovější aktualizace, spusťte `sudo yum update` poté, co je instance RHEL připravena. Tato služba je součástí poplatky za software RHEL průběžné platby.

Další informace o RHEL imagí v Azure, včetně publikování a zásad uchovávání, jsou k dispozici [zde](./redhat-images.md).

Informace o zásadách podpory Red Hat pro všechny verze RHEL najdete na stránce [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata) .

> [!IMPORTANT]
> RHUI je určena jenom pro image s průběžnými platbami (PAYGO). U vlastních a zlatých imagí, označovaných také jako přizpůsobené předplatné (BYOS), musí být systém připojen k RHSM nebo satelitu, aby mohl přijímat aktualizace. Další podrobnosti najdete v [článku o Red Hat](https://access.redhat.com/solutions/253273) .


## <a name="important-information-about-azure-rhui"></a>Důležité informace o Azure RHUI

* Azure RHUI je infrastruktura aktualizací, která podporuje všechny virtuální počítače s PAYG RHEL vytvořené v Azure. To vám nebrání v registraci virtuálních počítačů s PAYG RHEL pomocí Správce předplatného nebo jiného zdroje aktualizací, ale pokud to uděláte s virtuálním počítačem s PAYG, výsledkem bude nepřímá fakturace. Podrobnosti najdete v následujícím bodu.
* Přístup ke RHUI hostovaných v Azure je součástí ceny image RHEL průběžné platby. Pokud můžete zrušit registraci virtuálního počítače s průběžnými PLATBAMI RHEL od RHUI hostovaných v Azure, který není převést virtuální počítač do typu přineste si – používání vlastní licence (BYOL) virtuálního počítače. Pokud zaregistrujete stejný virtuální počítač s jiným zdrojem aktualizací, můžou se vám účtovat _nepřímé_ dvojité poplatky. Vám budou účtovány poprvé poplatek za software RHEL v Azure. Vám budou účtovány podruhé pro předplatných Red Hat, které byly zakoupeny dříve. Pokud trvale potřebujete použít jinou než hostovanou infrastrukturu, než je Azure Hosted, zvažte registraci pro použití [BYOS imagí RHEL](./byos.md).

* Image RHEL SAP PAYG v Azure (RHEL pro SAP, RHEL for SAP HANA a RHEL for SAP Business Applications) se připojují k vyhrazeným kanálům RHUI, které v konkrétní verzi RHEL zůstávají jako požadavky na certifikaci SAP.

* Přístup k RHUI hostovanému v Azure se omezí jenom na virtuální počítače v [rozsahu IP adres datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653). Pokud jste připojení přes server proxy veškerý provoz virtuálního počítače přes místní síťové infrastruktury, musíte nastavit trasy definované uživatelem pro virtuální počítače s průběžnými PLATBAMI RHEL pro přístup k Azure RHUI. V takovém případě bude potřeba přidat trasy definované uživatelem pro _všechny_ IP adresy RHUI.


## <a name="image-update-behavior"></a>Chování aktualizace obrázku

Od dubna 2019 nabízí Azure image RHEL připojené k úložištím rozšířené aktualizace (EUS) ve výchozím nastavení a RHEL image, které jsou ve výchozím nastavení připojené k běžným úložištím (mimo EUS). Další podrobnosti o RHEL EUS najdete v [dokumentaci životního cyklu verze](https://access.redhat.com/support/policy/updates/errata) Red Hat a v [dokumentaci k EUS](https://access.redhat.com/articles/rhel-eus). Výchozí chování `sudo yum update` se bude lišit v závislosti na RHEL imagi, kterou jste zřídili, protože různé image jsou připojené k různým úložištím.

Pro úplný seznam imagí spusťte `az vm image list --publisher redhat --all` pomocí Azure CLI.

### <a name="images-connected-to-non-eus-repositories"></a>Image připojené k úložištím bez EUS

Pokud zřídíte virtuální počítač z image RHEL, která je připojená k EUS úložištích, budete při spuštění `sudo yum update`upgradováni na nejnovější podverzi RHEL. Pokud například zřídíte virtuální počítač z image RHEL 7,4 PAYG a spustíte `sudo yum update`, skončíte s virtuálním počítačem RHEL 7,7 (nejnovější podverze v rodině RHEL7).

Obrázky, které jsou připojené k úložištím, která nejsou EUS, v SKU nebudou obsahovat číslo dílčí verze. SKU je třetí prvek v názvu URN (celý název obrázku). Například všechny následující image jsou připojené k úložištím bez EUS:

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

Všimněte si, že skladové položky jsou buď 7 – LVM nebo 7-RAW. Podverze je uvedena ve verzi (čtvrtém elementu v názvu URN) těchto imagí.

### <a name="images-connected-to-eus-repositories"></a>Obrázky připojené k úložištím EUS

Pokud zřídíte virtuální počítač z image RHEL, která je připojená k úložištím EUS, při spuštění `sudo yum update`nebudete upgradovat na nejnovější podverzi RHEL. Důvodem je to, že image připojené k úložištím EUS jsou taky zamčené na jejich konkrétní dílčí verzi.

Obrázky připojené k úložištím EUS budou obsahovat číslo dílčí verze v SKU. Například všechny následující image jsou připojené k úložištím EUS:

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS a uzamykání verzí – RHEL virtuální počítače

Úložiště EUS (Extended Update support) jsou k dispozici zákazníkům, kteří můžou chtít po zřízení virtuálního počítače uzamknout své virtuální počítače RHEL na určitou RHEL dílčí verzi. Můžete zamknout virtuální počítač RHEL na určitou dílčí verzi tak, že aktualizujete úložiště, aby odkazoval na úložiště podpory rozšířené aktualizace. Operaci uzamčení verze EUS můžete také vrátit zpět.

>[!NOTE]
> EUS se v RHEL Extras nepodporuje. To znamená, že pokud instalujete balíček, který je obvykle dostupný z kanálu RHEL Extras, nebudete ho moct v EUS použít. Životní cyklus produktu Red Hat Extras je [zde](https://access.redhat.com/support/policy/updates/extras/)podrobně popsán.

V době psaní tohoto zápisu skončila podpora EUS pro RHEL < = 7,4. Další podrobnosti najdete v části "Red Hat Enterprise Linux delší podporované doplňky" v [dokumentaci k Red Hat](https://access.redhat.com/support/policy/updates/errata/) .
* RHEL 7,4 EUS Podpora končí 31. srpna 2019
* RHEL 7,5 EUS Podpora končí 30. dubna 2020
* RHEL 7,6 EUS Podpora končí 31. října 2020
* RHEL 7,7 EUS Podpora končí 30. srpna 2021

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>Přepnutí virtuálního počítače s RHEL na EUS (pro zámek verze na konkrétní dílčí verzi)
Následující pokyny použijte k uzamknutí virtuálního počítače s RHEL na určitou dílčí verzi (Spustit jako kořen):

>[!NOTE]
> To platí jenom pro verze RHEL, pro které je EUS k dispozici. V době psaní tohoto zápisu sem patří RHEL 7.2-7.7. Další podrobnosti jsou k dispozici na stránce [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata) .

1. Zakázat úložiště bez EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Přidat úložišť EUS:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Uzamknout proměnnou releasever (Spustit jako kořen):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > Výše uvedená instrukce zamkne RHEL dílčí vydání na aktuální dílčí verzi. Pokud chcete upgradovat a uzamknout novější dílčí verzi, která není nejnovější, zadejte konkrétní dílčí verzi. `echo 7.5 > /etc/yum/vars/releasever` například uzamkne vaši verzi RHEL na RHEL 7,5

1. Aktualizace virtuálního počítače s RHEL
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>Přepnutí virtuálního počítače s RHEL zpět na jiný než EUS (odebrání zámku verze)
Spusťte následující příkaz jako kořen:
1. Odeberte soubor releasever:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. Zakázat úložiště EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. Konfigurace virtuálního počítače s RHEL
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```

1. Aktualizace virtuálního počítače s RHEL
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>IP adresy serverů pro doručování obsahu RHUI

RHUI je k dispozici ve všech oblastech, kde jsou k dispozici Image RHEL na vyžádání. V současné době zahrnuje všechny veřejné oblasti uvedené na stránce [řídicího panelu stavu Azure](https://azure.microsoft.com/status/) , státní správu usa a Microsoft Azure (Německo) oblasti.

Pokud k dalšímu omezení přístupu z virtuálních počítačů RHEL PAYG používáte konfiguraci sítě, zajistěte, aby `yum update` fungovaly v závislosti na prostředí, ve kterém jste:


```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193
13.72.14.155
52.244.249.194

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="azure-rhui-infrastructure"></a>Infrastruktura Azure RHUI


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Aktualizovat certifikát s prošlou platností klienta RHUI na virtuálním počítači

Pokud používáte starší image virtuálního počítače s RHEL, třeba RHEL 7,4 (Image URN: `RedHat:RHEL:7.4:7.4.2018010506`), dojde k problémům s připojením k RHUI z důvodu certifikátu klienta protokolu SSL s vypršenou platností. Chyba, kterou vidíte, může vypadat jako _"partner SSL odmítl váš certifikát jako neplatný"_ nebo _"Chyba: nejde načíst metadata úložiště (repomd. XML) pro úložiště:... Ověřte prosím cestu a zkuste to znovu_. Pokud chcete tento problém vyřešit, aktualizujte prosím na virtuálním počítači balíček klienta RHUI pomocí následujícího příkazu:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Případně může běžet `sudo yum update` taky aktualizovat balíček klientského certifikátu (v závislosti na vaší verzi RHEL), a to i v případě, že dojde k chybám "certifikát SSL s vypršelou platností" pro další úložiště. Pokud je tato aktualizace úspěšná, měla by se obnovit normální připojení k ostatním úložištím RHUI, takže budete moct úspěšně spustit `sudo yum update`.

Pokud při spuštění `yum update`narazíte na chybu 404, zkuste aktualizovat mezipaměť Yumu následujícím způsobem:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Potíže s připojením k Azure RHUI
Pokud máte potíže s připojením k Azure RHUI z vašeho virtuálního počítače Azure RHEL průběžné platby, postupujte podle těchto kroků:

1. Zkontrolujte konfiguraci role virtuálního počítače pro koncový bod Azure RHUI:

    1. Ověřte, zda `/etc/yum.repos.d/rh-cloud.repo` soubor obsahuje odkaz na `rhui-[1-3].microsoft.com` v `baseurl` části `[rhui-microsoft-azure-rhel*]` souboru. Pokud tomu tak, že používáte nové RHUI Azure.

    1. Pokud odkazuje na umístění s následujícím vzorem, `mirrorlist.*cds[1-4].cloudapp.net`je vyžadována aktualizace konfigurace. Použití starého snímku virtuálního počítače a budete muset aktualizovat tak, aby odkazoval na nové RHUI Azure.

1. Přístup k RHUI hostovanému v Azure je omezený na virtuální počítače v [rozsahu IP adres datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653).

1. Pokud používáte novou konfiguraci, ověření, že virtuální počítač se připojuje z rozsahu IP adres Azure a pořád nemůžete připojit k Azure RHUI, soubor případ podpory s Microsoftem nebo Red Hat.

### <a name="infrastructure-update"></a>Aktualizace infrastruktury

V září 2016 jsme nasadili aktualizované RHUI Azure. V dubnu 2017 jsme vypnout staré RHUI Azure. Pokud používáte Image RHEL PAYG (nebo jejich snímky) od září 2016 nebo novější, se automaticky připojujete nové RHUI Azure. Pokud ale máte starší snímky na virtuálních počítačích, musíte ručně aktualizovat svou konfiguraci pro přístup k Azure RHUI, jak je popsáno v následující části.

Nové servery Azure RHUI se nasazují s využitím [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). V Traffic Manageru jeden koncový bod (rhui 1.microsoft.cz) můžete využívat jakýkoli virtuální počítač, bez ohledu na oblast.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Postup ruční aktualizace pro používání Azure RHUI serverů
Tento postup je k dispozici pouze pro referenci. Image RHEL PAYG už máte správnou konfiguraci pro připojení k Azure RHUI. Chcete-li ručně aktualizovat konfiguraci pro používání Azure RHUI serverů, proveďte následující kroky:

- Pro RHEL 6:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- Pro RHEL 7:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

- Pro RHEL 8:
    1. Vytvořte konfigurační soubor:
        ```bash
        vi rhel8.config
        ```
    1. Do konfiguračního souboru přidejte následující obsah:
        ```bash
        [rhui-microsoft-azure-rhel8]
        name=Microsoft Azure RPMs for Red Hat Enterprise Linux 8
        baseurl=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-2.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-3.microsoft.com/pulp/repos/microsoft-azure-rhel8
        enabled=1
        gpgcheck=1
        gpgkey=https://rhelimage.blob.core.windows.net/repositories/RPM-GPG-KEY-microsoft-azure-release sslverify=1
        ```
    1. Uložte soubor a spusťte následující příkaz:
        ```bash
        dnf --config rhel8.config install 'rhui-azure-rhel8'
        ```
    1. Aktualizace virtuálního počítače
        ```bash
        sudo dnf update
        ```


## <a name="next-steps"></a>Další kroky
* Pokud chcete vytvořit Red Hat Enterprise Linux virtuální počítač z image Azure Marketplace PAYG a používat Azure Hosted RHUI, pokračujte na [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/).
* Další informace o obrázcích Red Hat v Azure najdete na [stránce s dokumentací](./redhat-images.md).
* Informace o zásadách podpory Red Hat pro všechny verze RHEL najdete na stránce [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata) .
