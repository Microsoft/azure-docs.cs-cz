---
title: Jak používat PerfInsights Linux v Microsoft Azure | Microsoft Docs
description: Naučte se používat PerfInsights k odstraňování potíží s výkonem virtuálních počítačů s Linux.
services: virtual-machines-linux'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 7/10/2019
ms.author: genli
ms.openlocfilehash: f785f44b88c7f0d5f0b6f43114070888bb23146d
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302482"
---
# <a name="how-to-use-perfinsights"></a>Použití PerfInsights

[PerfInsights Linux](https://aka.ms/perfinsightslinuxdownload) je samoobslužný Nástroj pro diagnostiku, který shromažďuje a analyzuje diagnostická data, a poskytuje zprávu, která vám může pomoct řešit problémy s výkonem virtuálních počítačů se systémem Linux v Azure. PerfInsights je možné spustit na podporovaných virtuálních počítačích jako samostatný nástroj nebo přímo z portálu pomocí [diagnostiky výkonu pro virtuální počítače Azure](performance-diagnostics.md).

Pokud dochází k problémům s výkonem s virtuálními počítači, než se obrátíte na podporu, spusťte tento nástroj.

## <a name="supported-troubleshooting-scenarios"></a>Podporované scénáře řešení potíží

PerfInsights může shromažďovat a analyzovat několik druhů informací. V následujících částech najdete běžné scénáře.

### <a name="quick-performance-analysis"></a>Rychlá analýza výkonu

Tento scénář shromažďuje základní informace, jako je například úložiště a konfigurace hardwaru virtuálního počítače, různé protokoly, včetně:

- Informace o operačním systému

- Informace o zařízení PCI

- Obecné protokoly hostovaného operačního systému

- Konfigurační soubory

- Informace o úložišti

- Konfigurace virtuálního počítače Azure (shromážděné pomocí [Azure instance metadata Service](../windows/instance-metadata-service.md))

- Seznam spuštěných procesů, disku, paměti a využití CPU

- Informace o síti

Jedná se o pasivní shromažďování informací, které by neměly mít vliv na systém.

>[!Note]
>Scénář rychlé analýzy výkonu je automaticky zahrnutý v každém z následujících scénářů:

### <a name="performance-analysis"></a>Analýza výkonu

Tento scénář je podobný rychlé analýze výkonu, ale umožňuje zachytit diagnostické informace po delší dobu.

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Jaký druh informací shromažďuje služba PerfInsights

Shromažďují se informace o virtuálním počítači Linux, operačním systému, blokování zařízení, uživateli s vysokými prostředky, konfiguraci a různých protokolech. Tady jsou další podrobnosti:

- Operační systém
  - Distribuce a verze systému Linux
  - Informace o jádru
  - Informace o ovladači

- Hardware
  - Zařízení PCI [ `*` ]

- Procesy a paměť
  - Seznam procesů (název úlohy, využitá paměť, otevřené soubory)
  - Celková, dostupná a volná fyzická paměť
  - Celková, dostupná a volná paměť swap
  - Profilace zachycení procesoru a procesů využití procesoru v intervalu 5 sekund
  - Profilace zachycení procesu využití paměti v intervalu 5 sekund

- Sítě  
  - Seznam síťových adaptérů s adaptéry Statistika
  - Tabulka směrování sítě
  - Otevřené porty a stav

- Storage
  - Seznam blokovaných zařízení
  - Seznam oddílů
  - Seznam přípojných bodů
  - Informace o svazku MDADM
  - Informace o svazku LVM
  - Profilace zachycení na všech discích v intervalu 5 sekund

- Protokoly
  - /var/log/messages
  - /var/log/syslog
  - /var/log/kern.log
  - /var/log/cron.log
  - /var/log/boot.log
  - /var/log/yum.log
  - /var/log/dpkg.log
  - /var/log/sysstat nebo/var/log/SA [ `**` ]
  - /var/log/cloud-init.log
  - /var/log/cloud-init-output.log
  - /var/log/gpu-manager.log
  - /var/log/waagent.log
  - /var/log/Azure/[složka rozšíření]/ \* protokol\*
  - /var/opt/microsoft/omsconfig/omsconfig.log
  - /var/opt/microsoft/omsagent/log/omsagent.log
  - /etc/waagent.config
  - Výstup journalctl za posledních pět dní

- [Metadata instance virtuálního počítače Azure](../windows/instance-metadata-service.md)

>[!Note]
>[ `*` ] Informace o PCI ještě nejsou shromažďovány v Debian a SLES distribucích.
> 
>[ `**` ]/var/log/sysstat nebo/var/log/SA obsahuje soubory sestavy aktivity systému (správní oblast), které jsou shromažďovány balíčkem sysstat. Pokud balíček sysstat není na virtuálním počítači nainstalovaný, poskytuje nástroj PerfInsights doporučení k jeho instalaci.

## <a name="run-the-perfinsights-linux-on-your-vm"></a>Spuštění PerfInsights Linux na vašem VIRTUÁLNÍm počítači

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Co musím vědět před spuštěním nástroje

#### <a name="tool-requirements"></a>Požadavky na nástroj

- Tento nástroj se musí spustit na virtuálním počítači, který má problém s výkonem.
- Na virtuálním počítači musí být nainstalovaný Python 3. x nebo Python 2,7.

- V současné době jsou podporovány následující distribuce:

    | Distribuce               | Verze                                         |
    |----------------------------|-------------------------------------------------|
    | Server Oracle Linux        | 6,10 [ `*` ], 7,3, 7,6, 7,5 |
    | CentOS                     | 6,5 [ `*` ], 7,6                                    |
    | RHEL                       | 7,6, 7,7, 7,8, 7,9                                |
    | Ubuntu                     | 14,04, 16,04, 18,04, 20,04                               |
    | Debian                     | 8, 9, 10 [ `*` ]                                    |
    | SLES                       | 12 SP4 [ `*` ]                                      |
    |                            |                                                   |

>[!Note]
>[ `*` ] Podívejte se na část [známé problémy](#known-issues) .

### <a name="known-issues"></a>Známé problémy

- RHEL 8 nemá ve výchozím nastavení nainstalovaný Python. Pokud chcete spustit PerfInsights Linux, musíte nejdřív nainstalovat Python 2,7.

- Shromažďování informací agenta hosta může selhat v CentOS 6. x

- Informace o zařízeních sběrnice PCI nejsou shromažďovány v distribucích založených na Debian.

- Informace LVM se částečně shromažďují u některých distribucí.

### <a name="how-do-i-run-perfinsights"></a>Návody PerfInsights spustit

PerfInsights můžete na virtuálním počítači spustit pomocí instalace diagnostiky výkonu Azure z Azure Portal. Můžete ho také spustit jako samostatný nástroj.

>[!Note]
>PerfInsights jednoduše shromažďuje a analyzuje data. Neprovádí žádné úpravy systému.

#### <a name="install-and-run-perfinsights-from-the-azure-portal"></a>Instalace a spuštění PerfInsights z Azure Portal

Další informace o této možnosti najdete v tématu [Azure Performance Diagnostics](performance-diagnostics.md).  

#### <a name="run-perfinsights-in-standalone-mode"></a>Spustit PerfInsights v samostatném režimu

Chcete-li spustit nástroj PerfInsights, postupujte podle následujících kroků:

1. Stáhněte si [PerfInsights. tar. gz](https://aka.ms/perfinsightslinuxdownload) do složky na virtuálním počítači a extrahujte obsah pomocí níže uvedených příkazů z terminálu.

   ```bash
   wget https://download.microsoft.com/download/9/F/8/9F80419C-D60D-45F1-8A98-718855F25722/PerfInsights.tar.gz
   ```

   ```bash
   tar xzvf PerfInsights.tar.gz
   ```

2. Přejděte do složky, která obsahuje `perfinsights.py` soubor, a potom spusťte příkaz `perfinsights.py` pro zobrazení dostupných parametrů příkazového řádku.

    ```bash
    cd <the path of PerfInsights folder>
    sudo python perfinsights.py
    ```

    ![Snímek obrazovky s výstupem příkazového řádku PerfInsights Linux](media/how-to-use-perfinsights-linux/perfinsights-linux-commandline.png)

    Základní syntaxe pro spouštění scénářů PerfInsights je:

    ```bash
    sudo python perfinsights.py -r <ScenarioName> -d [duration]<H | M | S> [AdditionalOptions]
    ```

    Pomocí níže uvedeného příkladu můžete spustit scénář rychlého analýzy výkonu po dobu 1 minuty a vytvořit výsledky ve složce/TMP/output:

    ```bash
    sudo python perfinsights.py -r quick -d 1M -a -o /tmp/output
    ```

    Pomocí níže uvedeného příkladu můžete spustit scénář analýzy výkonu po dobu 5 minut a odeslat do účtu úložiště výslednou kuličku funkce tar:

    ```bash
    sudo python perfinsights.py -r vmslow -d 300S -a -t <StorageAccountName> -k <StorageAccountKey> -i <full resource Uri of the current VM>
    ```

    >[!Note]
    >Před spuštěním scénáře PerfInsights vyzve uživatele, aby souhlasil se sdílením diagnostických informací a souhlasil se smlouvou EULA. Tyto výzvy přeskočíte pomocí možnosti **-a nebo--Accept-inment-and-Share-Diagnostics** .
    >
    >Pokud máte aktivní lístek podpory s Microsoftem a provozujete PerfInsights na žádost pro inženýra podpory, se kterým pracujete, ujistěte se, že jste zadali číslo lístku podpory pomocí možnosti **-s nebo--support-Request** .

Až se spuštění dokončí, ve stejné složce jako PerfInsights se zobrazí nový soubor tar, pokud není zadaná žádná výstupní složka. Název souboru je **PerformanceDiagnostics \_ yyyy-MM-DD \_ HH-MM-SS-FFF. tar. gz.** Tento soubor můžete poslat agentovi podpory k analýze nebo otevřít sestavu v souboru, abyste mohli zkontrolovat zjištění a doporučení.

## <a name="review-the-diagnostics-report"></a>Kontrola diagnostické sestavy

V rámci souboru **PerformanceDiagnostics \_ yyyy-MM-DD \_ HH-MM-SS-FFF. tar. gz** můžete najít sestavu HTML, která podrobně popisuje nálezy PerfInsights. Chcete-li sestavu zkontrolovat, rozbalte soubor **PerformanceDiagnostics \_ yyyy-MM-DD \_ HH-MM-SS-FFF. tar. gz** a otevřete soubor **PerfInsights Report.html** .

### <a name="overview-tab"></a>Karta Přehled

Karta **Přehled** poskytuje základní podrobnosti o spuštění a informace o virtuálním počítači. Na kartě **nálezy** se zobrazí souhrn doporučení ze všech různých částí sestavy PerfInsights.

![Snímek obrazovky s kartou přehled sestavy PerfInsights](media/how-to-use-perfinsights-linux/perfinsights-linux-overview.png)  
![Snímek obrazovky s kartou Linux sestavy PerfInsights](media/how-to-use-perfinsights-linux/perfinsights-linux-findings-tab.png)

> [!NOTE]
> Nálezy zařazené do kategorie vysoká jsou známé problémy, které mohou způsobit problémy s výkonem. Nálezy kategorizované jako střední představuje neoptimální konfigurace, které nemusí nutně způsobovat problémy s výkonem. Nálezy zařazené do kategorie nízká jsou informativní pouze informativní příkazy.

Přečtěte si doporučení a odkazy pro všechna vysoká a střední zjištění. Seznamte se s tím, jak mohou ovlivnit výkon, a také o osvědčených postupech pro konfigurace optimalizované pro výkon.

### <a name="cpu-tab"></a>Karta CPU

Karta **CPU** poskytuje informace o spotřebě procesoru v rámci systému během PerfInsights spuštění. Informace o vysokém využití procesoru a hlavních dlouhotrvajících vzdávajících PROCESORech budou užitečné při řešení problémů s vysokými nároky na procesor.

![Snímek obrazovky s kartou PerfInsights pro sestavy procesoru](media/how-to-use-perfinsights-linux/perfinsights-linux-cpu-tab.png)

### <a name="storage-tab"></a>Karta úložiště

V části **nálezy** se zobrazují různé závěry a doporučení týkající se úložiště.

**Bloková zařízení** a další související oddíly, jako jsou **oddíly**, **LVM** a **MDADM** , popisují, jak jsou nakonfigurované a související bloková zařízení.

![Snímek obrazovky s kartou úložiště](media/how-to-use-perfinsights-linux/perfinsights-linux-storage-tab.png)  
![Snímek obrazovky s kartou MDADM](media/how-to-use-perfinsights-linux/perfinsights-linux-mdadm-config.png)

### <a name="linux-tab"></a>Karta Linux

Karta **Linux** obsahuje informace o hardwaru a operačním systému běžícím na vašem virtuálním počítači. Podrobnosti obsahují seznam spuštěných procesů a informace o ovladačích hostovaného agenta, PCI, procesoru, ovladačů a systému LIS.

![Snímek obrazovky s kartou Linux](media/how-to-use-perfinsights-linux/perfinsights-linux-tab.png)

## <a name="next-steps"></a>Další kroky

Protokoly diagnostiky a sestavy můžete odeslat podpora Microsoftu k dalšímu přezkoumání. Při práci s podpora Microsoftu zaměstnanci si můžou vyžádat, abyste předávali výstup, který je vygenerovaný PerfInsights, aby pomohl procesu řešení potíží.

Na následujícím snímku obrazovky se zobrazí zpráva podobná tomu, co se vám může zobrazit:

![Snímek obrazovky ukázkové zprávy z podpora Microsoftu](media/how-to-use-perfinsights-linux/support-email.png)

Postupujte podle pokynů ve zprávě pro přístup k pracovnímu prostoru přenosu souborů. Pro zvýšení zabezpečení je nutné při prvním použití změnit heslo.

Po přihlášení se zobrazí dialogové okno pro nahrání souboru **PerformanceDiagnostics \_ yyyy-MM-DD \_ HH-MM-SS-FFF. tar. gz** , který byl shromážděn pomocí PerfInsights.
