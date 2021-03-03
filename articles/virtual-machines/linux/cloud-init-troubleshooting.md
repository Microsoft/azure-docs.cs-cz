---
title: Řešení potíží pomocí Cloud-init
description: Řešení potíží se zřizováním virtuálního počítače Azure pomocí Cloud-init.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: a18899ffc6b19be6226d9e0a3efd9a9519434601
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666218"
---
# <a name="troubleshooting-vm-provisioning-with-cloud-init"></a>Řešení potíží se zřizováním virtuálních počítačů pomocí cloudu – init

Pokud jste vytvořili generalizované vlastní image pomocí Cloud-init k zřizování, ale zjistili jste, že se virtuální počítač nevytvořil správně, budete muset vyřešit potíže s vlastními imagemi.

Některé příklady problémů se zřizováním:
- Virtuální počítač se zablokuje při vytváření na 40 minut a vytvoření virtuálního počítače se označí jako neúspěšné.
- `CustomData` nezpracovává se
- Dočasný disk se nepodařilo připojit.
- Uživatelé se nevytvoří nebo dojde k problémům s přístupem uživatele.
- Sítě nejsou správně nastavené.
- Záměna selhání souboru nebo oddílu

Tento článek popisuje, jak řešit potíže s Cloud-init. Podrobnější informace najdete v článku [Cloud-init hluboké podrobně](./cloud-init-deep-dive.md).

## <a name="step-1-test-the-deployment-without-customdata"></a>Krok 1: testování nasazení bez `customData`

Cloud-init může přijmout `customData` , která je předána, když se virtuální počítač vytvoří. Nejdřív byste měli zajistit, že nezpůsobují žádné problémy s nasazeními. Zkuste zřídit virtuální počítač bez předání jakékoli konfigurace. Pokud zjistíte, že se virtuální počítač nepovede zřídit, pokračujte v následujících krocích, pokud najdete konfiguraci, kterou jste provedli. Projděte si [Krok 4](). 

## <a name="step-2-review-image-requirements"></a>Krok 2: Kontrola požadavků na Image
Hlavní příčinou selhání zřizování virtuálních počítačů je, že image operačního systému nesplňuje požadavky na spuštění v Azure. Před tím, než se pokusíte zřídit v Azure, ujistěte se, že jsou vaše image správně připravené. 


Následující články ilustrují postup přípravy různých distribucí systému Linux, které jsou podporovány v Azure:

- [Distribuce založené na CentOS](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES a openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)
- [Ostatní: neschválené distribuce](create-upload-generic.md)

V případě [podporovaných imagí Azure Cloud-init](./using-cloud-init.md)již mají distribuce systému Linux všechny požadované balíčky a konfigurace k dispozici pro správné zřízení image v Azure. Pokud zjistíte, že se váš virtuální počítač nedaří vytvořit z vlastní řízené image, zkuste podporovanou bitovou kopii Azure Marketplace, která už je nakonfigurovaná pro Cloud-init, s volitelnou možností `customData` . Pokud `customData` správně funguje s obrázkem Azure Marketplace, bude se pravděpodobně jednat o problém s vaší naorganizované image.

## <a name="step-3-collect--review-vm-logs"></a>Krok 3: shromáždění & kontroly protokolů virtuálních počítačů

Když se virtuální počítač nepovede zřídit, Azure zobrazí stav "vytváření", po dobu 20 minut a pak virtuální počítač restartuje a před tím, než nakonec označí nasazení virtuálního počítače jako neúspěšné, počkejte dalších 20 minut, než ho nakonec označíte `OSProvisioningTimedOut` chybou.

I když je virtuální počítač spuštěný, budete potřebovat protokoly z virtuálního počítače, abyste zjistili, proč se zřizování nepovedlo.  Pokud chcete zjistit, proč se zřizování virtuálního počítače nepovedlo, nestavte virtuální počítač. Nechejte virtuální počítač spuštěný. Aby bylo možné shromažďovat protokoly, bude nutné, aby byl virtuální počítač v běžícím stavu spuštěný. Chcete-li shromáždit protokoly, použijte jednu z následujících metod:

- [Sériová konzola](../troubleshooting/serial-console-grub-single-user-mode.md)

- Před vytvořením virtuálního počítače [Povolte diagnostiku spouštění](/previous-versions/azure/virtual-machines/linux/tutorial-monitor#enable-boot-diagnostics) a pak je [Zobrazte](/previous-versions/azure/virtual-machines/linux/tutorial-monitor#view-boot-diagnostics) během spouštění.

- [Spuštěním AZ VM oprava](../troubleshooting/repair-linux-vm-using-azure-virtual-machine-repair-commands.md) připojte a připojte disk s operačním systémem, který vám umožní shromáždit tyto protokoly:
```bash
/var/log/cloud-init*
/var/log/waagent*
/var/log/syslog*
/var/log/rsyslog*
/var/log/messages*
/var/log/kern*
/var/log/dmesg*
/var/log/boot*
```
Pokud chcete začít s počátečním řešením potíží, začněte s protokoly Cloud-init a zjistěte, kde k selhání došlo, a pak použijte další protokoly pro rozsáhlou podrobně a poskytněte další přehledy. 
* /var/log/cloud-init.log
* /var/log/cloud-init-output.log
* Sériové/spouštěcí protokoly

Ve všech protokolech spusťte hledání "neúspěšné", "varování", "upozornění", "Err", "Chyba", "Chyba". Doporučuje se nastavit konfiguraci tak, aby ignorovala hledání malých a velkých písmen. 

> [!TIP]
> Pokud řešíte potíže s vlastní imagí, měli byste zvážit přidání uživatele během bitové kopie. Pokud se zřizování nepodaří nastavit uživatele správce, můžete se i nadále přihlašovat k operačnímu systému.

## <a name="analyzing-the-logs"></a>Analýza protokolů

Tady jsou další podrobnosti o tom, co se má hledat v každém protokolu Cloud-init.

### <a name="varlogcloud-initlog"></a>/var/log/cloud-init.log

Ve výchozím nastavení se všechny události Cloud-init s prioritou ladění nebo vyšší zapisují do `/var/log/cloud-init.log` . To poskytuje podrobné protokoly každé události, ke které došlo při inicializaci Cloud-init. 

Například:

```console
2019-10-10 04:51:25,321 - util.py[DEBUG]: Failed mount of '/dev/sr0' as 'auto': Unexpected error while running command.
Command: ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpLIrklc']
Exit code: 32
Reason: -
Stdout:
Stderr: mount: unknown filesystem type 'udf'
2020-01-31 00:21:53,352 - DataSourceAzure.py[WARNING]: /dev/sr0 was not mountable
```


Po nalezení chyby nebo upozornění si přečtěte zpátky v protokolu Cloud-init, abyste zjistili, co se v Cloud-init pokoušelo vyzkoušet před tím, než dojde k chybě nebo upozornění. V mnoha případech Cloud-init spustí příkazy operačního systému nebo provedl operace zřizování před chybou, což vám poskytne přehled o tom, proč se v protokolech objevily chyby. Následující příklad ukazuje, že se Cloud-init pokusil připojit zařízení přímo před tím, než dojde k chybě.

```output
2019-10-10 04:51:24,010 - util.py[DEBUG]: Running command ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpXXXXX'] with allowed return codes [0] (shell=False, capture=True)
```

Pokud máte přístup ke [konzole sériového portu](../troubleshooting/serial-console-grub-single-user-mode.md), můžete se pokusit znovu spustit příkaz, který se snaží spustit Cloud-init.

Protokolování `/var/log/cloud-init.log` lze také překonfigurovat v rámci/etc/cloud/cloud.cfg.d/05_logging. cfg. Další podrobnosti o protokolování Cloud-init najdete v dokumentaci ke službě [Cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/logging.html). 

### <a name="varlogcloud-init-outputlog"></a>/var/log/cloud-init-output.log

Můžete získat informace z nástroje `stdout` a `stderr` během [fází Cloud-init](cloud-init-deep-dive.md). To obvykle zahrnuje informace o směrovací tabulce, informace o síti, informace o ověřování klíčů hostitele SSH `stdout` a `stderr` pro každou fázi Cloud-init a časovou razítko pro každou fázi. V případě potřeby `stderr` a `stdout` protokolování lze překonfigurovat z `/etc/cloud/cloud.cfg.d/05_logging.cfg` .

### <a name="serialboot-logs"></a>Sériové/spouštěcí protokoly 

Cloud-init má několik závislostí, které jsou popsány v článku vyžadované požadavky na bitové kopie v Azure, jako jsou například síťové služby, úložiště, možnost připojit bitovou kopii ISO a připojit a naformátovat dočasný disk. Kterákoli z těchto zpráv může způsobovat chyby a způsobit selhání Cloud-init. Pokud například virtuální počítač nemůže získat zapůjčení DHCP, Cloud-init selže.

Pokud stále nemůžete izolovat příčinu, proč se Cloud-init nepodařilo zřídit, je nutné pochopit, co fáze Cloud-init a kdy se moduly spouštějí. Další podrobnosti najdete v článku o [začnete hlubší do cloudu a inicializaci](cloud-init-deep-dive.md) .


## <a name="step-4-investigate-why-the-configuration-isnt-being-applied"></a>Krok 4: zjištění, proč se konfigurace nepoužívá
Nejedná se o každé selhání v Cloud-init, které způsobí závažné selhání zřizování. Pokud například používáte `runcmd` modul v konfiguraci Cloud-init, nenulový ukončovací kód z příkazu, na kterém běží, způsobí, že se zřizování virtuálního počítače nezdaří. Důvodem je to, že se spustí po základních funkcích zřizování, ke kterým dochází v prvních třech fázích Cloud-init. Pokud chcete řešit potíže, proč se konfigurace nepoužila, zkontrolujte protokoly v kroku 3 a moduly Cloud-init ručně. Například:

- `runcmd` – spouští se skripty bez chyb? Spusťte konfiguraci ručně z terminálu, aby se zajistilo, že budou fungovat podle očekávání.
- Instalují se balíčky – virtuální počítač má přístup k úložištím balíčků?
- Měli byste taky ověřit `customData` konfiguraci dat, která byla k virtuálnímu počítači k dispozici, v umístění `/var/lib/cloud/instances/<unique-instance-identifier>/user-data.txt` .


## <a name="next-steps"></a>Další kroky

Pokud stále nemůžete izolovat, proč Cloud-init nespouštěla konfiguraci, je nutné pozorovat přesněji na to, co se děje v každé fázi Cloud-init a kdy se moduly spouštějí. Další informace najdete v tématu [začnete hlouběji do konfigurace Cloud-init](./cloud-init-deep-dive.md) .