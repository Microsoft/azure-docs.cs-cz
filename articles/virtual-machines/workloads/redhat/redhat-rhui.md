---
title: Infrastruktura aktualizace Red Hat | Microsoft Docs
description: Přečtěte si o infrastruktuře aktualizací Red Hat pro instance Red Hat Enterprise Linux na vyžádání v Microsoft Azure
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 02/10/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 968377ed09996b9a717e0739a3de8355d1c8d88d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677144"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Infrastruktura aktualizace Red Hat pro virtuální počítače na vyžádání Red Hat Enterprise Linux v Azure
 RHUI ( [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) ) umožňuje poskytovatelům cloudu, jako je Azure, zrcadlit obsah úložiště hostovaného na Red Hat, vytvářet vlastní úložiště pomocí obsahu specifického pro Azure a zpřístupňuje je pro virtuální počítače koncových uživatelů.

Red Hat Enterprise Linux (RHEL) image s průběžnými platbami (PAYG) jsou předem nakonfigurované pro přístup k Azure RHUI. Není nutná žádná další konfigurace. Pokud chcete získat nejnovější aktualizace, spusťte `sudo yum update` po přípravě instance RHEL. Tato služba je součástí poplatků za software RHEL PAYG.

Další informace o RHEL imagí v Azure, včetně publikování a zásad uchovávání, jsou k dispozici [zde](./redhat-images.md).

Informace o zásadách podpory Red Hat pro všechny verze RHEL najdete na stránce [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata) .

> [!IMPORTANT]
> RHUI je určena jenom pro image s průběžnými platbami (PAYG). U vlastních a zlatých imagí, označovaných také jako přizpůsobené předplatné (BYOS), musí být systém připojen k RHSM nebo satelitu, aby mohl přijímat aktualizace. Další podrobnosti najdete v [článku o Red Hat](https://access.redhat.com/solutions/253273) .


## <a name="important-information-about-azure-rhui"></a>Důležité informace o Azure RHUI

* Azure RHUI je infrastruktura aktualizací, která podporuje všechny virtuální počítače s PAYG RHEL vytvořené v Azure. To vám nebrání v registraci virtuálních počítačů s PAYG RHEL pomocí Správce předplatného nebo jiného zdroje aktualizací, ale pokud to uděláte s virtuálním počítačem s PAYG, výsledkem bude nepřímá fakturace. Podrobnosti najdete v následujícím bodu.
* Přístup k RHUI hostovanému v Azure je obsažený v ceně image RHEL PAYG. Pokud zrušíte registraci virtuálního počítače s PAYG RHEL z RHUI hostovaného v Azure, které virtuální počítač nepřevede na typ vlastní licence (BYOL) virtuálního počítače. Pokud zaregistrujete stejný virtuální počítač s jiným zdrojem aktualizací, můžou se vám účtovat _nepřímé_ dvojité poplatky. Účtují se vám první poplatky za poplatek za software Azure RHEL. U předplatných Red Hat, které jste si koupili dříve, se vám účtuje podruhé. Pokud trvale potřebujete použít jinou než hostovanou infrastrukturu, než je Azure Hosted, zvažte registraci pro použití [BYOS imagí RHEL](./byos.md).

* Image RHEL SAP PAYG v Azure (RHEL pro SAP, RHEL for SAP HANA a RHEL for SAP Business Applications) se připojují k vyhrazeným kanálům RHUI, které v konkrétní verzi RHEL zůstávají jako požadavky na certifikaci SAP.

* Přístup k RHUI hostovanému v Azure se omezí jenom na virtuální počítače v [rozsahu IP adres datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653). Pokud provádíte proxy server pomocí místní síťové infrastruktury, možná budete muset nastavit uživatelsky definované trasy pro virtuální počítače s RHEL PAYG pro přístup k Azure RHUI. V takovém případě bude potřeba přidat trasy definované uživatelem pro _všechny_ IP adresy RHUI.


## <a name="image-update-behavior"></a>Chování aktualizace obrázku

Od dubna 2019 nabízí Azure image RHEL připojené k úložištím rozšířené aktualizace (EUS) ve výchozím nastavení a RHEL image, které jsou ve výchozím nastavení připojené k běžným úložištím (mimo EUS). Další podrobnosti o RHEL EUS najdete v [dokumentaci životního cyklu verze](https://access.redhat.com/support/policy/updates/errata) Red Hat a v [dokumentaci k EUS](https://access.redhat.com/articles/rhel-eus). Výchozí chování `sudo yum update` se bude lišit v závislosti na tom, ze kterého image RHEL jste zřídili, protože různé image jsou připojené k různým úložištím.

Úplný seznam imagí můžete spustit `az vm image list --publisher redhat --all` pomocí Azure CLI.

### <a name="images-connected-to-non-eus-repositories"></a>Image připojené k úložištím bez EUS

Pokud zřídíte virtuální počítač z image RHEL, která je připojená k EUS úložištích, budete při spuštění upgradu na nejnovější podverzi RHEL `sudo yum update` . Pokud například zřídíte virtuální počítač z image RHEL 7,4 PAYG a spustíte `sudo yum update` ji, skončíte s virtuálním počítačem RHEL 7,8 (nejnovější podverze v rodině RHEL7).

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

Pokud zřídíte virtuální počítač z image RHEL, která je připojená k úložištím EUS, nebudete při spuštění upgradovat na nejnovější podverzi RHEL `sudo yum update` . Důvodem je to, že image připojené k úložištím EUS jsou taky zamčené na jejich konkrétní dílčí verzi.

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

V době psaní tohoto zápisu skončila podpora EUS pro RHEL <= 7,4. Další podrobnosti najdete v části "Rozšířená údržba Red Hat Enterprise Linux" v [dokumentaci k Red Hat](https://access.redhat.com/support/policy/updates/errata/#Long_Support) .
* RHEL 7,4 EUS Podpora končí 31. srpna 2019
* RHEL 7,5 EUS Podpora končí 30. dubna 2020
* RHEL 7,6 EUS Podpora končí na 31. května 2021
* RHEL 7,7 EUS Podpora končí 30. srpna 2021

### <a name="switch-a-rhel-vm-7x-to-eus-version-lock-to-a-specific-minor-version"></a>Přepnutí virtuálního počítače s RHEL 7. x na EUS (pro zámek verze na konkrétní dílčí verzi)
Pomocí následujících pokynů můžete uzamknout virtuální počítač RHEL 7. x do konkrétní dílčí verze (Spustit jako kořen):

>[!NOTE]
> To platí jenom pro RHEL verze 7. x, pro které je k dispozici EUS. V době psaní tohoto zápisu sem patří RHEL 7.2-7.7. Další podrobnosti jsou k dispozici na stránce [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata) .

1. Zakázat úložiště bez EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Přidat úložišť EUS:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Uzamknout `releasever` proměnnou (Spustit jako kořen):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > Výše uvedená instrukce zamkne RHEL dílčí vydání na aktuální dílčí verzi. Pokud chcete upgradovat a uzamknout novější dílčí verzi, která není nejnovější, zadejte konkrétní dílčí verzi. Například `echo 7.5 > /etc/yum/vars/releasever` bude vaše verze RHEL uzamčena na RHEL 7,5.

1. Aktualizace virtuálního počítače s RHEL
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-8x-to-eus-version-lock-to-a-specific-minor-version"></a>Přepnutí virtuálního počítače s RHEL 8. x na EUS (pro zámek verze na konkrétní dílčí verzi)
Pomocí následujících pokynů můžete uzamknout virtuální počítač RHEL 8. x do konkrétní dílčí verze (Spustit jako kořen):

>[!NOTE]
> To platí jenom pro RHEL verze 8. x, pro které je EUS k dispozici. V době psaní tohoto textu to zahrnuje RHEL 8.1-8.2. Další podrobnosti jsou k dispozici na stránce [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata) .

1. Zakázat úložiště bez EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel8'
    ```

1. Získání konfiguračního souboru úložišť EUS:
    ```bash
    wget https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel8-eus.config
    ```

1. Přidat úložišť EUS:
    ```bash
    yum --config=rhui-microsoft-azure-rhel8-eus.config install rhui-azure-rhel8-eus
    ```

1. Uzamknout `releasever` proměnnou (Spustit jako kořen):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > Výše uvedená instrukce zamkne RHEL dílčí vydání na aktuální dílčí verzi. Pokud chcete upgradovat a uzamknout novější dílčí verzi, která není nejnovější, zadejte konkrétní dílčí verzi. Například `echo 8.1 > /etc/yum/vars/releasever` bude vaše verze RHEL uzamčena na RHEL 8,1.

    >[!NOTE]
    > Pokud existují problémy s oprávněními pro přístup k releasever, můžete soubor upravit pomocí příkazu nano/etc/yum/Vars/releaseve a přidat podrobnosti o verzi image a uložit (' Ctrl + o ' a pak stisknout klávesu CTRL + x ').  

1. Aktualizace virtuálního počítače s RHEL
    ```bash
    sudo yum update
    ```


### <a name="switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock"></a>Přepnutí virtuálního počítače se systémem RHEL 7. x zpět na jiný než EUS (odebrání zámku verze)
Spusťte následující příkaz jako kořen:
1. Odeberte `releasever` soubor:
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

### <a name="switch-a-rhel-8x-vm-back-to-non-eus-remove-a-version-lock"></a>Přepnout virtuální počítač s RHEL 8. x zpátky na jiný než EUS (odebrat zámek verze)
Spusťte následující příkaz jako kořen:
1. Odeberte `releasever` soubor:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. Zakázat úložiště EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel8-eus'
   ```

1. Získání konfiguračního souboru pravidelného úložiště:
    ```bash
    wget https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel8.config
    ```

1. Přidat úložišť EUS:
    ```bash
    yum --config=rhui-microsoft-azure-rhel8.config install rhui-azure-rhel8
    ```
    
1. Aktualizace virtuálního počítače s RHEL
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>IP adresy pro servery pro doručování obsahu RHUI

RHUI je k dispozici ve všech oblastech, kde jsou k dispozici RHEL obrázky na vyžádání. V současné době zahrnuje všechny veřejné oblasti uvedené na stránce [řídicího panelu stavu Azure](https://azure.microsoft.com/status/) , státní správu usa a Microsoft Azure (Německo) oblasti.

Pokud používáte konfiguraci sítě k dalšímu omezení přístupu z virtuálních počítačů s RHEL PAYG, ujistěte se, že pro `yum update` práci v závislosti na prostředí, ve kterém se nacházíte, jsou povolené následující IP adresy:


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
>[!NOTE]
>Nové image vlády Azure USA, od ledna 2020, budou používat veřejnou IP adresu uvedenou v části globální hlavička Azure výše.

>[!NOTE]
>Všimněte si také, že Azure Německo je zastaralá ve prospěch veřejných oblastí Německa. Doporučení pro zákazníky Azure v Německu je začít ukazovat na veřejné RHUI pomocí kroků uvedených [tady](#manual-update-procedure-to-use-the-azure-rhui-servers).

## <a name="azure-rhui-infrastructure"></a>Infrastruktura Azure RHUI


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Aktualizace certifikátu klienta RHUI s vypršenou platností na virtuálním počítači

Pokud používáte starší image virtuálního počítače s RHEL, třeba RHEL 7,4 (Image URN: `RedHat:RHEL:7.4:7.4.2018010506` ), dojde k problémům s připojením k RHUI v důsledku certifikátu klienta TLS/SSL s vypršenou platností. Chyba, kterou vidíte, může vypadat jako _"partner SSL odmítl váš certifikát jako neplatný"_ nebo _"Chyba: nejde načíst metadata úložiště (repomd.xml) pro úložiště:... Ověřte prosím cestu a zkuste to znovu_. Pokud chcete tento problém vyřešit, aktualizujte prosím na virtuálním počítači balíček klienta RHUI pomocí následujícího příkazu:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Případně může běžet `sudo yum update` také aktualizace balíčku klientského certifikátu (v závislosti na vaší verzi RHEL), a to i v případě, že se zobrazí chyba "certifikát SSL s vypršelou platností" v případě jiných úložišť. Pokud je tato aktualizace úspěšná, měla by se obnovit normální připojení k ostatním úložištím RHUI, takže budete moct `sudo yum update` úspěšně spustit.

Pokud při běhu služby spustíte chybu 404 `yum update` , zkuste aktualizovat Yumu cache pomocí následujícího:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Řešení potíží s připojením do Azure RHUI
Pokud dochází k problémům s připojením k Azure RHUI z virtuálního počítače Azure RHEL PAYG, postupujte takto:

1. Zkontrolujte konfiguraci virtuálního počítače pro koncový bod Azure RHUI:

    1. Ověřte, zda `/etc/yum.repos.d/rh-cloud.repo` soubor obsahuje odkaz na `rhui-[1-3].microsoft.com` v `baseurl` `[rhui-microsoft-azure-rhel*]` části souboru. Pokud k tomu používáte novou službu Azure RHUI, budete používat.

    1. Pokud odkazuje na umístění s následujícím vzorem, `mirrorlist.*cds[1-4].cloudapp.net` je vyžadována aktualizace konfigurace. Používáte starý snímek virtuálního počítače a potřebujete ho aktualizovat, aby odkazoval na nový Azure RHUI.

1. Přístup k RHUI hostovanému v Azure je omezený na virtuální počítače v [rozsahu IP adres datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653).

1. Pokud používáte novou konfiguraci, ověřili jste, že se virtuální počítač připojuje z rozsahu IP adres Azure a stále se nemůžete připojit k Azure RHUI, zajistěte si případ podpory v Microsoft nebo Red Hat.

### <a name="infrastructure-update"></a>Aktualizace infrastruktury

V září 2016 jsme nasadili aktualizovaný RHUI Azure. V dubnu 2017 jsme ukončili starou službu Azure RHUI. Pokud jste v září 2016 nebo novější používali image RHEL PAYG (nebo jejich snímky), automaticky se připojujete k novému RHUI Azure. Pokud máte ale na svých virtuálních počítačích starší snímky, musíte ručně aktualizovat jejich konfiguraci pro přístup k Azure RHUI, jak je popsáno v následující části.

Nové servery Azure RHUI se nasazují s využitím [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). V Traffic Manager může být jeden koncový bod (rhui-1.microsoft.com) použit libovolným virtuálním počítačem bez ohledu na oblast.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Postup ruční aktualizace pro použití serverů Azure RHUI
Tento postup je k dispozici pouze pro referenci. Image PAYG pro RHEL už mají správnou konfiguraci pro připojení k Azure RHUI. Pokud chcete konfiguraci ručně aktualizovat tak, aby používala servery Azure RHUI, proveďte následující kroky:

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
* Pokud chcete vytvořit Red Hat Enterprise Linux virtuální počítač z image Azure Marketplace PAYG a používat Azure Hosted RHUI, pokračujte na [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RHEL_6).
* Další informace o obrázcích Red Hat v Azure najdete na [stránce s dokumentací](./redhat-images.md).
* Informace o zásadách podpory Red Hat pro všechny verze RHEL najdete na stránce [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata) .
