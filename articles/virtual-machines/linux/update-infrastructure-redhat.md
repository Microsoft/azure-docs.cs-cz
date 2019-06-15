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
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: 4315a849f3f117633f5f6a9d93c995ece9f527a3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077006"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat Update Infrastructure pro virtuální počítače na vyžádání Red Hat Enterprise Linuxem v Azure
 [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) umožňuje poskytovateli cloudu, jako je Azure, které zrcadlí obsahu úložiště hostované v systému Red Hat, vytvořte vlastní úložiště s týkající se Azure obsahu a ji dejte k dispozici pro virtuální počítače koncového uživatele.

Image Red Hat Enterprise Linux (RHEL) průběžných plateb (PAYG) jsou předem nakonfigurovaný pro přístup k Azure RHUI. Je potřeba žádná další konfigurace. Chcete-li získat nejnovější aktualizace, spusťte `sudo yum update` po RHEL instance je připravený. Tato služba je součástí poplatky za software RHEL průběžné platby.

Další informace pro Image RHEL v Azure, včetně publikování a zásady uchovávání informací jsou k dispozici [tady](./rhel-images.md).

Informace o zásadách podpory Red Hat pro všechny verze systému RHEL můžete najít na [Red Hat Enterprise Linux životního cyklu](https://access.redhat.com/support/policy/updates/errata) stránky.

## <a name="important-information-about-azure-rhui"></a>Důležité informace o Azure RHUI
* Azure RHUI je infrastruktura aktualizací, která podporuje všechny virtuální počítače RHEL průběžné platby, vytvořené v Azure. To nebrání je vaše virtuální počítače RHEL PAYG zaregistrovali na správce předplatného nebo satelitní nebo jiný zdroj aktualizací, ale uděláte s průběžnými PLATBAMI virtuálního počítače způsobí nepřímé double fakturace. Podívejte se na následující bod pro podrobnosti.
* Přístup ke RHUI hostovaných v Azure je součástí ceny image RHEL průběžné platby. Pokud můžete zrušit registraci virtuálního počítače s průběžnými PLATBAMI RHEL od RHUI hostovaných v Azure, který není převést virtuální počítač do typu přineste si – používání vlastní licence (BYOL) virtuálního počítače. Když si zaregistrujete stejný virtuální počítač s jiný zdroj aktualizací, můžou vám být účtovány _nepřímé_ dvakrát poplatky. Vám budou účtovány poprvé poplatek za software RHEL v Azure. Vám budou účtovány podruhé pro předplatných Red Hat, které byly zakoupeny dříve. Pokud je potřeba konzistentně použití aktualizace infrastruktury než RHUI hostovaných v Azure, vezměte v úvahu registrace k použití [imagí RHEL BYOS](https://aka.ms/rhel-byos).
* Výchozí chování RHUI je váš virtuální počítač s RHEL upgradovat na nejnovější podverzi, při spuštění `sudo yum update`.

    Například, pokud zřízení virtuálního počítače z image RHEL 7.4 PAYG a spusťte `sudo yum update`, skončíte se 7.6 virtuální počítač s RHEL (nejnovější dílčí verzi řady RHEL7).

    Chcete-li toto chování vyhnout, můžete přepnout na [rozšířenou podporu aktualizace kanály](#rhel-eus-and-version-locking-rhel-vms) nebo sestavení vlastní image, jak je popsáno v [vytvoření a nahrání virtuálního počítače založeného na Red Hat pro Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) článku. Pokud vytvoříte vlastní image, musíte se připojit k jiné aktualizační infrastruktury ([přímo do Red Hat obsahu serverů pro doručování](https://access.redhat.com/solutions/253273) nebo [serveru Red Hat satelitní](https://access.redhat.com/products/red-hat-satellite)).



* Image RHEL SAP PAYG v Azure (RHEL for SAP, RHEL for SAP HANA a RHEL for SAP Business Applications) jsou připojené k vyhrazené RHUI kanály, které zůstávají na konkrétní dílčí verze RHEL podle potřeby k certifikaci pro SAP.

* Přístup ke RHUI hostovaných v Azure je omezený na virtuální počítače v rámci [rozsahy IP adres datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653). Pokud jste připojení přes server proxy veškerý provoz virtuálního počítače přes místní síťové infrastruktury, musíte nastavit trasy definované uživatelem pro virtuální počítače s průběžnými PLATBAMI RHEL pro přístup k Azure RHUI.

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS a zamykání verze RHEL virtuálních počítačů
Někteří zákazníci mohou chcete zamknout své virtuální počítače RHEL do některých vydání vedlejší verze RHEL. Verze zámku můžete aktualizací úložiště tak, aby odkazoval na rozšířenou podporu aktualizace úložiště virtuálního počítače RHEL, aby konkrétní podverze. Operace uzamčení verze EUS můžete také vrátit zpět.

>[!NOTE]
> EUS nepodporuje funkce RHEL. To znamená, že při instalaci balíčku, který je obvykle k dispozici z kanálu funkce RHEL, nebudete mít k tomu, když jste na EUS. Životní cyklus Red Hat funkce produktu je podrobně popsán [tady](https://access.redhat.com/support/policy/updates/extras/).

V době psaní tohoto návodu, byla ukončena EUS podpory pro RHEL < = 7.3. V části "Red Hat Enterprise Linux delší podporu doplňky" v [Red Hat dokumentaci](https://access.redhat.com/support/policy/updates/errata/) další podrobnosti.
* RHEL 7.4 EUS podpora skončí 31. srpna 2019
* RHEL 7.5 EUS podpora skončí 30. dubna 2020
* RHEL 7.6 EUS podpora skončí 31. října 2020

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>Přepnout virtuální počítač s RHEL EUS (verze zámek na konkrétní dílčí verze)
Uzamknout virtuální počítač s RHEL do určité vedlejší verze (Spustit jako uživatel root) použijte následující pokyny:

>[!NOTE]
> To platí jenom pro RHEL verze, pro které je k dispozici EUS. V době psaní tohoto návodu jedná se o RHEL 7.2 7.6. Další podrobnosti jsou k dispozici na [Red Hat Enterprise Linux životního cyklu](https://access.redhat.com/support/policy/updates/errata) stránky.

1. Zakažte jiné EUS úložišť:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Přidáte EUS úložišť:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Zamknout releasever proměnné (Spustit jako uživatel root):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > RHEL dílčí verze na aktuální vydání vedlejší dojde k uzamčení podle výše uvedených pokynů. Zadejte konkrétní podverzi, pokud chcete upgradovat a uzamknout novější verze, která není na nejnovější verzi. Například `echo 7.5 > /etc/yum/vars/releasever` dojde k uzamčení vaše RHEL verze RHEL 7.5

1. Aktualizovat váš virtuální počítač s RHEL
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>Virtuální počítač s RHEL přepněte zpět do jiných EUS (odebrat zámek proti verze)
Jako kořenové spusťte následující příkaz:
1. Odeberte soubor releasever:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. Zakážete EUS úložišť:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. Aktualizovat váš virtuální počítač s RHEL
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

Pokud používáte starší image virtuálního počítače RHEL, například RHEL 7.4 (obrázku URN: `RedHat:RHEL:7.4:7.4.2018010506`), dojde k problémům s připojením k RHUI kvůli klientský certifikát SSL vypršela. Chyba se zobrazí může vypadat jako _"partner SSL odmítl vašeho certifikátu, protože platnost"_ nebo _"Chyba: Nelze načíst metadata úložiště (repomd.xml) pro úložiště:... Ověřte cestu a zkuste to znovu"_ . Chcete-li tento problém vyřešit, aktualizujte prosím balíček klienta RHUI na virtuálním počítači pomocí následujícího příkazu:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Alternativně systémem `sudo yum update` také aktualizovat balíček certifikátů klienta (v závislosti na vaší verzi RHEL), bez ohledu na "vypršela platnost certifikátu SSL" chyby, zobrazí se pro jiné úložiště. Pokud tato aktualizace je úspěšné, do jiných úložišť RHUI normální spojení mělo obnovit, tak bude moct spustit `sudo yum update` úspěšně.

Pokud narazíte na chybu 404 při spuštění `yum update`, zkuste následující aktualizace mezipaměti yumu:
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
* Další informace o Image Red Hat v Azure, přejděte [stránky dokumentace](./rhel-images.md).
* Informace o zásadách podpory Red Hat pro všechny verze systému RHEL můžete najít na [Red Hat Enterprise Linux životního cyklu](https://access.redhat.com/support/policy/updates/errata) stránky.
