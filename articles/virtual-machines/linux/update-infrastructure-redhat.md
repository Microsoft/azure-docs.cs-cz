---
title: Infrastruktura aktualizace Red Hat | Dokumentace Microsoftu
description: Další informace o Red Hat Update Infrastructure pro instance Red Hat Enterprise Linux na vyžádání v Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: efc76616151776bc2f766f92ff9503413c6037d0
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774268"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat Update Infrastructure pro virtuální počítače na vyžádání Red Hat Enterprise Linuxem v Azure
 [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) umožňuje poskytovateli cloudu, jako je Azure, které zrcadlí obsahu úložiště hostované v systému Red Hat, vytvořte vlastní úložiště s týkající se Azure obsahu a ji dejte k dispozici pro virtuální počítače koncového uživatele.

Image Red Hat Enterprise Linux (RHEL) průběžných plateb (PAYG) jsou předem nakonfigurovaný pro přístup k Azure RHUI. Je potřeba žádná další konfigurace. Chcete-li získat nejnovější aktualizace, spusťte `sudo yum update` po RHEL instance je připravený. Tato služba je součástí poplatky za software RHEL průběžné platby.

Další informace o RHEL imagí v Azure, včetně publikování a zásad uchovávání, jsou k dispozici [zde](./rhel-images.md).

Informace o zásadách podpory Red Hat pro všechny verze RHEL najdete na stránce [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata) .

## <a name="important-information-about-azure-rhui"></a>Důležité informace o Azure RHUI
* Azure RHUI je infrastruktura aktualizací, která podporuje všechny virtuální počítače s PAYG RHEL vytvořené v Azure. To vám nebrání v registraci virtuálních počítačů s PAYG RHEL pomocí Správce předplatného nebo jiného zdroje aktualizací, ale pokud to uděláte s virtuálním počítačem s PAYG, výsledkem bude nepřímá fakturace. Podrobnosti najdete v následujícím bodu.
* Přístup ke RHUI hostovaných v Azure je součástí ceny image RHEL průběžné platby. Pokud můžete zrušit registraci virtuálního počítače s průběžnými PLATBAMI RHEL od RHUI hostovaných v Azure, který není převést virtuální počítač do typu přineste si – používání vlastní licence (BYOL) virtuálního počítače. Když si zaregistrujete stejný virtuální počítač s jiný zdroj aktualizací, můžou vám být účtovány _nepřímé_ dvakrát poplatky. Vám budou účtovány poprvé poplatek za software RHEL v Azure. Vám budou účtovány podruhé pro předplatných Red Hat, které byly zakoupeny dříve. Pokud trvale potřebujete použít jinou než hostovanou infrastrukturu, než je Azure Hosted, zvažte registraci pro použití [BYOS imagí RHEL](https://aka.ms/rhel-byos).
* Výchozím chováním RHUI je při spuštění `sudo yum update`upgradovat virtuální počítač s RHEL na nejnovější dílčí verzi.

    Například, pokud zřízení virtuálního počítače z image RHEL 7.4 PAYG a spusťte `sudo yum update`, skončíte se 7.6 virtuální počítač s RHEL (nejnovější dílčí verzi řady RHEL7).

    Chcete-li se tomuto chování vyhnout, můžete přejít na [kanály podpory rozšířené aktualizace](#rhel-eus-and-version-locking-rhel-vms) nebo sestavit vlastní image, jak je popsáno v článku [Vytvoření a nahrání virtuálního počítače se systémem Red Hat pro Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) . Pokud vytváříte vlastní image, budete ji muset připojit k jiné infrastruktuře aktualizace ([přímo na servery pro doručování obsahu Red Hat](https://access.redhat.com/solutions/253273) nebo [Red Hat satelitní Server](https://access.redhat.com/products/red-hat-satellite)).



* Image RHEL SAP PAYG v Azure (RHEL pro SAP, RHEL for SAP HANA a RHEL for SAP Business Applications) se připojují k vyhrazeným kanálům RHUI, které v konkrétní verzi RHEL zůstávají jako požadavky na certifikaci SAP.

* Přístup ke RHUI hostovaných v Azure je omezený na virtuální počítače v rámci [rozsahy IP adres datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653). Pokud jste připojení přes server proxy veškerý provoz virtuálního počítače přes místní síťové infrastruktury, musíte nastavit trasy definované uživatelem pro virtuální počítače s průběžnými PLATBAMI RHEL pro přístup k Azure RHUI.

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS a uzamykání verzí – RHEL virtuální počítače
Někteří zákazníci si můžou chtít uzamknout své virtuální počítače s RHEL na určitou dílčí verzi RHEL. Můžete zamknout virtuální počítač RHEL na určitou dílčí verzi tak, že aktualizujete úložiště, aby odkazoval na úložiště podpory rozšířené aktualizace. Operaci uzamčení verze EUS můžete také vrátit zpět.

>[!NOTE]
> EUS se v RHEL Extras nepodporuje. To znamená, že pokud instalujete balíček, který je obvykle dostupný z kanálu RHEL Extras, nebudete ho moct v EUS použít. Životní cyklus produktu Red Hat Extras je [zde](https://access.redhat.com/support/policy/updates/extras/)podrobně popsán.

V době psaní tohoto zápisu skončila podpora EUS pro RHEL < = 7,3. Další podrobnosti najdete v části "Red Hat Enterprise Linux delší podporované doplňky" v [dokumentaci k Red Hat](https://access.redhat.com/support/policy/updates/errata/) .
* RHEL 7,4 EUS Podpora končí 31. srpna 2019
* RHEL 7,5 EUS Podpora končí 30. dubna 2020
* RHEL 7,6 EUS Podpora končí 31. října 2020

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>Přepnutí virtuálního počítače s RHEL na EUS (pro zámek verze na konkrétní dílčí verzi)
Následující pokyny použijte k uzamknutí virtuálního počítače s RHEL na určitou dílčí verzi (Spustit jako kořen):

>[!NOTE]
> To platí jenom pro verze RHEL, pro které je EUS k dispozici. V době psaní tohoto zápisu sem patří RHEL 7.2-7.6. Další podrobnosti jsou k dispozici na stránce [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata) .

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
    > Výše uvedená instrukce zamkne RHEL dílčí vydání na aktuální dílčí verzi. Pokud chcete upgradovat a uzamknout novější dílčí verzi, která není nejnovější, zadejte konkrétní dílčí verzi. Například `echo 7.5 > /etc/yum/vars/releasever` bude vaše verze RHEL uzamčena na RHEL 7,5

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
13.72.14.155
52.244.249.194

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="azure-rhui-infrastructure"></a>Infrastruktura Azure RHUI


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Aktualizovat certifikát s prošlou platností klienta RHUI na virtuálním počítači

Pokud používáte starší image virtuálního počítače s RHEL, třeba RHEL 7,4 (Image urn: `RedHat:RHEL:7.4:7.4.2018010506`), dojde k problémům s připojením k RHUI z důvodu certifikátu klienta protokolu SSL s vypršenou platností. Chyba, kterou vidíte, může vypadat jako _"při vypršení platnosti certifikátu byl zamítnutý partner SSL"_ nebo _"Chyba: Nejde načíst metadata úložiště (repomd. XML) pro úložiště:... Ověřte prosím cestu a zkuste to znovu_. Pokud chcete tento problém vyřešit, aktualizujte prosím na virtuálním počítači balíček klienta RHUI pomocí následujícího příkazu:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Případně může běžet `sudo yum update` také aktualizace balíčku klientského certifikátu (v závislosti na vaší verzi RHEL), a to i v případě, že se zobrazí chyba "certifikát SSL s vypršelou platností" v případě jiných úložišť. Pokud je tato aktualizace úspěšná, měla by se obnovit normální připojení k ostatním úložištím RHUI, takže budete moct úspěšně spustit `sudo yum update` .

Pokud při běhu služby spustíte chybu `yum update`404, zkuste aktualizovat Yumu cache pomocí následujícího:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Potíže s připojením k Azure RHUI
Pokud máte potíže s připojením k Azure RHUI z vašeho virtuálního počítače Azure RHEL průběžné platby, postupujte podle těchto kroků:

1. Zkontrolujte konfiguraci role virtuálního počítače pro koncový bod Azure RHUI:

    1. Zkontrolujte, zda `/etc/yum.repos.d/rh-cloud.repo` soubor obsahuje odkaz na `rhui-[1-3].microsoft.com` v `baseurl` z `[rhui-microsoft-azure-rhel*]` část souboru. Pokud tomu tak, že používáte nové RHUI Azure.

    1. Pokud odkazuje na umístění s následujícím vzorem `mirrorlist.*cds[1-4].cloudapp.net`, vyžaduje se aktualizace konfigurace. Použití starého snímku virtuálního počítače a budete muset aktualizovat tak, aby odkazoval na nové RHUI Azure.

1. Přístup ke RHUI hostovaných v Azure je omezený na virtuální počítače v rámci [rozsahy IP adres datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653).

1. Pokud používáte novou konfiguraci, ověření, že virtuální počítač se připojuje z rozsahu IP adres Azure a pořád nemůžete připojit k Azure RHUI, soubor případ podpory s Microsoftem nebo Red Hat.

### <a name="infrastructure-update"></a>Aktualizace infrastruktury

V září 2016 jsme nasadili aktualizované RHUI Azure. V dubnu 2017 jsme vypnout staré RHUI Azure. Pokud používáte Image RHEL PAYG (nebo jejich snímky) od září 2016 nebo novější, se automaticky připojujete nové RHUI Azure. Pokud ale máte starší snímky na virtuálních počítačích, musíte ručně aktualizovat svou konfiguraci pro přístup k Azure RHUI, jak je popsáno v následující části.

Nasazení nových serverů Azure RHUI se používají [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). V Traffic Manageru jeden koncový bod (rhui 1.microsoft.cz) můžete využívat jakýkoli virtuální počítač, bez ohledu na oblast.

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

## <a name="next-steps"></a>Další postup
* Vytvoření virtuálního počítače s Red Hat Enterprise Linuxem z image Azure Marketplace s průběžnými PLATBAMI a použít RHUI hostovaných v Azure, přejděte [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/).
* Další informace o obrázcích Red Hat v Azure najdete na [stránce](./rhel-images.md)s dokumentací.
* Informace o zásadách podpory Red Hat pro všechny verze RHEL najdete na stránce [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata) .
