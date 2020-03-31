---
title: Red Hat Update Infrastruktura | Dokumenty společnosti Microsoft
description: Další informace o infrastruktuře red hatových aktualizací pro instance Red Hat Enterprise Linux na vyžádání v Microsoft Azure
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
ms.openlocfilehash: aa9fd230f59b5e46576e78beb0436c85449d3c5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256908"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Infrastruktura aktualizací Red Hat pro virtuální počítače Red Hat Enterprise Linux na vyžádání v Azure
 [Infrastruktura aktualizací Red Hat](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) umožňuje poskytovatelům cloudu, jako je Azure, zrcadlit obsah úložiště hostovaného red hatem, vytvářet vlastní úložiště s obsahem specifickým pro Azure a zpřístupnit ho virtuálním počítačům koncových uživatelů.

Image Red Hat Enterprise Linux (RHEL) Pay-As-You-Go (PAYG) jsou předkonfigurované pro přístup k Azure RHUI. Není nutná žádná další konfigurace. Chcete-li získat nejnovější `sudo yum update` aktualizace, spusťte po připravenou instanci RHEL. Tato služba je součástí softwarových poplatků RHEL PAYG.

Další informace o ibi RHEL v Azure, včetně zásad publikování a uchovávání informací, jsou k dispozici [zde](./redhat-images.md).

Informace o zásadách podpory Red Hat pro všechny verze RHEL naleznete na stránce [životního cyklu Red Hat Enterprise Linux.](https://access.redhat.com/support/policy/updates/errata)

> [!IMPORTANT]
> RHUI je určen pouze pro obrázky pay-as-you-go (PAYG). Pro vlastní a zlaté obrázky, známé také jako bring-your-own-subscription (BYOS), systém musí být připojen k RHSM nebo satelitu, aby bylo možné přijímat aktualizace. Další podrobnosti najdete v článku red [hat.](https://access.redhat.com/solutions/253273)


## <a name="important-information-about-azure-rhui"></a>Důležité informace o Azure RHUI

* Azure RHUI je aktualizační infrastruktura, která podporuje všechny virtuální počítače RHEL PAYG vytvořené v Azure. To nebrání registraci vašich virtuálních účtů PAYG RHEL u Správce předplatného nebo satelitu nebo jiného zdroje aktualizací, ale pokud tak učiníte s virtuálním počítačem PAYG, bude to mít za následek nepřímou dvojitou fakturaci. Podrobnosti naleznete v následujícím bodě.
* Přístup k RHUI hostovaného v Azure je součástí ceny image RHEL PAYG. Pokud zrušíte registraci virtuálního počítače PAYG RHEL z RHUI hostovaného v Azure, který nepřevede virtuální počítač na typ virtuálního počítače typu přenést vlastní licenci (BYOL). Pokud zaregistrujete stejný virtuální virtuální ms u jiného zdroje aktualizací, může vám vzniknout _nepřímé dvojnásobné_ poplatky. Poplatky za software Azure RHEL vám budou účtovány poprvé. Za předchozí zakoupená předplatná Red Hat vám budou účtována druhá. Pokud konzistentně potřebujete používat infrastrukturu aktualizací než RHUI hostovaná v Azure, zvažte registraci k použití [bitových kopií RHEL BYOS](./byos.md).

* Image RHEL SAP PAYG v Azure (RHEL pro SAP, RHEL pro SAP HANA a RHEL pro obchodní aplikace SAP) jsou připojeny k vyhrazeným kanálům RHUI, které zůstávají na konkrétní dílčí verzi RHEL podle potřeby pro certifikaci SAP.

* Přístup k RHUI hostovaného v Azure je omezený na virtuální počítače v rozsahu [IP adres datového centra Azure](https://www.microsoft.com/download/details.aspx?id=41653). Pokud proxyjete veškerý provoz virtuálních počítačích prostřednictvím místní síťové infrastruktury, možná budete muset nastavit uživatelem definované trasy pro virtuální počítače RHEL PAYG pro přístup k Azure RHUI. V takovém případě bude nutné přidat uživatelem definované trasy pro _všechny_ IP adresy RHUI.


## <a name="image-update-behavior"></a>Chování aktualizace obrázku

Od dubna 2019 nabízí Azure image RHEL, které jsou ve výchozím nastavení připojené k úložištím rozšířené podpory aktualizací (EUS), a image RHEL, které se ve výchozím nastavení připojí k běžným úložištím (mimo EUS). Další podrobnosti o RHEL EUS jsou k dispozici v [dokumentaci k životnímu cyklu verze](https://access.redhat.com/support/policy/updates/errata) red hatu a [v dokumentaci eus](https://access.redhat.com/articles/rhel-eus). Výchozí chování `sudo yum update` se bude lišit v závislosti na tom, ze kterého obrazu RHEL jste zřídit, jako různé obrázky jsou připojeny k různým úložištím.

Úplný seznam bitových `az vm image list --publisher redhat --all` obrázků spusťte pomocí příkazového příkazového příkazu Azure.

### <a name="images-connected-to-non-eus-repositories"></a>Obrázky připojené k úložištím mimo EUS

Pokud zřídíte virtuální ho virtuálního zařízení z bitové kopie RHEL, která je připojena k úložištím `sudo yum update`mimo EUS, budete při spuštění upgradováni na nejnovější dílčí verzi RHEL . Například pokud zřídíte virtuální ho virtuálního zařízení z obrázku RHEL 7.4 PAYG a spustit `sudo yum update`, skončíte s RHEL 7.7 Virtuální hod (nejnovější dílčí verze v rodině RHEL7).

Obrázky, které jsou připojeny k úložištím mimo EUS, nebudou obsahovat číslo dílčí verze ve skladové jednotce. Skladová položka je třetí prvek v URN (úplný název obrázku). K úložištím mimo EUS jsou například připojeny všechny následující obrázky:

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

Všimněte si, že sku jsou buď 7-LVM nebo 7-RAW. Dílčí verze je uvedena ve verzi (čtvrtý prvek v URN) těchto obrázků.

### <a name="images-connected-to-eus-repositories"></a>Obrázky připojené k úložištím EUS

Pokud zřídíte virtuální ho virtuálního zařízení z bitové kopie RHEL, která je připojena k úložištím EUS, nebudete při spuštění `sudo yum update`upgradováni na nejnovější dílčí verzi RHEL . Je tomu tak proto, že obrázky připojené k úložištím EUS jsou také uzamčeny verzí pro jejich konkrétní dílčí verzi.

Obrázky připojené k úložištím EUS budou obsahovat dílčí číslo verze ve skladové jednotce. K repozitářům EUS jsou například připojeny všechny následující obrázky:

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS a virtuální zařízení RHEL pro zamykání verzí

Úložiště rozšířené podpory aktualizací (EUS) jsou k dispozici zákazníkům, kteří mohou chtít po zřízení virtuálního zařízení zamknout své virtuální ms RHEL na určitou dílčí verzi RHEL. Můžete uzamknout virtuální počítač RHEL na konkrétní dílčí verzi aktualizací úložišť tak, aby ukazovaly na úložiště podpory rozšířené aktualizace. Můžete také vrátit zpět operaci eus verze uzamčení.

>[!NOTE]
> EUS není podporována v rhel extras. To znamená, že pokud instalujete balíček, který je obvykle k dispozici z kanálu RHEL Extras, nebudete tak moci učinit v eus. Red Hat Extras Životní cyklus produktu je podrobně [popsán zde](https://access.redhat.com/support/policy/updates/extras/).

V době psaní tohoto článku skončila podpora EUS pro <RHEL = 7.4. Další podrobnosti najdete v části "Red Hat Enterprise Linux Longer Support Add-Ons" v [dokumentaci k Red Hatu.](https://access.redhat.com/support/policy/updates/errata/)
* Rhel 7.4 Podpora EUS končí 31.8.2019
* RHEL 7.5 PODPORA EUS končí 30.dubna 2020
* RHEL 7.6 Podpora EUS končí 31.října 2020
* RHEL 7.7 Podpora EUS končí 30.srpna 2021

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>Přepnutí virtuálního zařízení RHEL na EUS (zámek verze na konkrétní dílčí verzi)
Pomocí následujících pokynů uzamkněte virtuální hod RHEL na konkrétní dílčí verzi (spustit jako root):

>[!NOTE]
> To platí pouze pro verze RHEL, pro které je EUS k dispozici. V době psaní tohoto článku to zahrnuje RHEL 7.2-7.7. Další podrobnosti jsou k dispozici na stránce [životního cyklu Red Hat Enterprise Linux.](https://access.redhat.com/support/policy/updates/errata)

1. Zakázat repo repo prodejů mimo EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Přidat repo repo eus:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Uzamknout proměnnou `releasever` (spustit jako root):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > Výše uvedená instrukce uzamkne dílčí verzi RHEL na aktuální dílčí verzi. Zadejte konkrétní dílčí verzi, pokud chcete upgradovat a zamknout na pozdější dílčí verzi, která není nejnovější. Například `echo 7.5 > /etc/yum/vars/releasever` uzamkne verzi RHEL na RHEL 7.5

1. Aktualizace virtuálního počítače RHEL
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>Přepnutí virtuálního virtuálního zařízení RHEL zpět na jiné než EUS (odebrání zámku verze)
Spusťte následující jako root:
1. Odeberte `releasever` soubor:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. Zakázat repo repo souspolečnosti EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. Konfigurace virtuálního počítače RHEL
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```

1. Aktualizace virtuálního počítače RHEL
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>IP adresy pro servery pro doručování obsahu RHUI

RHUI je k dispozici ve všech oblastech, kde jsou k dispozici obrázky RHEL na vyžádání. V současné době zahrnuje všechny veřejné oblasti uvedené na stránce [řídicího panelu stavu Azure,](https://azure.microsoft.com/status/) Azure US Government a Microsoft Azure Germany.

Pokud používáte konfiguraci sítě k dalšímu omezení přístupu z virtuálních počítačů RHEL PAYG, ujistěte se, že v závislosti na prostředí, ve které se právě pohybujete, mohou `yum update` pracovat následující IP adresy:


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


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Aktualizace vypršela certifikát klienta RHUI na virtuálním počítači

Pokud používáte starší bitovou kopii virtuálního počítače RHEL, například RHEL 7.4 (image URN: `RedHat:RHEL:7.4:7.4.2018010506`), dojde k problémům s připojením k RHUI kvůli klientském certifikátu TLS/SSL, který vypršela. Chyba, kterou vidíte, může vypadat jako _"SSL peer odmítl váš certifikát jako vypršela"_ nebo _"Chyba: Nelze načíst metadata úložiště (repomd.xml) pro repozitář: ... Ověřte jeho cestu a akci opakujte._ Chcete-li tento problém překonat, aktualizujte balíček klienta RHUI na virtuálním počítači pomocí následujícího příkazu:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Alternativně může `sudo yum update` spuštění také aktualizovat balíček klientského certifikátu (v závislosti na verzi RHEL), a to navzdory chybám "vypršela platnost certifikátu SSL", které se zobrazí u jiných úložišť. Pokud je tato aktualizace úspěšná, normální připojení k jiným úložištím RHUI `sudo yum update` by mělo být obnoveno, takže budete moci úspěšně spustit.

Pokud při spuštění počítače narazíte na `yum update`chybu 404 , zkuste aktualizovat mezipaměť mlamů následujícím:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Poradce při potížích s připojením k Azure RHUI
Pokud narazíte na problémy s připojením k Azure RHUI z vašeho virtuálního počítače Azure RHEL PAYG, postupujte takto:

1. Zkontrolujte konfiguraci virtuálního počítače pro koncový bod Azure RHUI:

    1. Zkontrolujte, `/etc/yum.repos.d/rh-cloud.repo` zda soubor `rhui-[1-3].microsoft.com` obsahuje `baseurl` odkaz `[rhui-microsoft-azure-rhel*]` v části souboru. Pokud ano, používáte nový Azure RHUI.

    1. Pokud odkazuje na umístění s následujícím `mirrorlist.*cds[1-4].cloudapp.net`vzorem , je vyžadována aktualizace konfigurace. Používáte starý snímek virtuálního počítače a je potřeba ho aktualizovat tak, aby ukazoval na nový Azure RHUI.

1. Přístup k RHUI hostovaného v Azure je omezený na virtuální počítače v rámci [rozsahů IP adres datového centra Azure](https://www.microsoft.com/download/details.aspx?id=41653).

1. Pokud používáte novou konfiguraci, ověřili jste, že se virtuální počítač připojuje z oblasti IP azure a pořád se nemůžete připojit k Azure RHUI, nasuňte případ podpory u Microsoftu nebo Red Hatu.

### <a name="infrastructure-update"></a>Aktualizace infrastruktury

V září 2016 jsme nasadili aktualizovaný Azure RHUI. V dubnu 2017 jsme vypnuli starý Azure RHUI. Pokud jste používali image RHEL PAYG (nebo jejich snímky) od září 2016 nebo novější, automaticky se připojujete k novému Azure RHUI. Pokud však máte starší snímky na virtuálních počítačích, budete muset ručně aktualizovat jejich konfiguraci pro přístup k Azure RHUI, jak je popsáno v následující části.

Nové servery Azure RHUI se nasazují pomocí [Azure Traffic Manageru](https://azure.microsoft.com/services/traffic-manager/). Ve Správci provozu může jeden koncový bod (rhui-1.microsoft.com) používat libovolný virtuální virtuální virtuální ms bez ohledu na oblast.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Postup ruční aktualizace pro použití serverů Azure RHUI
Tento postup je uveden pouze pro referenci. Image RHEL PAYG už mají správnou konfiguraci pro připojení k Azure RHUI. Chcete-li ručně aktualizovat konfiguraci tak, aby používala servery Azure RHUI, proveďte následující kroky:

- Pro RHEL 6:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- Pro RHEL 7:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

- Pro RHEL 8:
    1. Vytvoření konfiguračního souboru:
        ```bash
        vi rhel8.config
        ```
    1. Přidejte do konfiguračního souboru následující obsah:
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
* Pokud chcete vytvořit virtuální počítač Red Hat Enterprise Linux z image Payg Azure Marketplace a používat RHUI hostované v Azure, přejděte na [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/).
* Další informace o ibi Red Hatu v Azure najdete na [stránce dokumentace](./redhat-images.md).
* Informace o zásadách podpory Red Hat pro všechny verze RHEL naleznete na stránce [životního cyklu Red Hat Enterprise Linux.](https://access.redhat.com/support/policy/updates/errata)
