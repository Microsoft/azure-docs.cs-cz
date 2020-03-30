---
title: Jak používat PerfInsights Linux v Microsoft Azure| Dokumenty společnosti Microsoft
description: Učí se, jak používat PerfInsights k řešení problémů s výkonem virtuálních počítačích v Linuxu.
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
ms.openlocfilehash: 19b2fcaed2c80d4ca52ada9f9f0898479e73bcf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266985"
---
# <a name="how-to-use-perfinsights"></a>Použití PerfInsights

[PerfInsights Linux](https://aka.ms/perfinsightslinuxdownload) je samoobslužný diagnostický nástroj, který shromažďuje a analyzuje diagnostická data a poskytuje sestavu, která vám pomůže řešit problémy s výkonem virtuálních strojů linuxu v Azure. PerfInsights lze spustit na podporovaných virtuálních počítačích jako samostatný nástroj nebo přímo z portálu pomocí [diagnostiky výkonu pro virtuální počítače Azure](performance-diagnostics.md).

Pokud dochází k problémům s výkonem s virtuálními počítači, před kontaktováním podpory spusťte tento nástroj.

## <a name="supported-troubleshooting-scenarios"></a>Podporované scénáře řešení potíží

PerfInsights může shromažďovat a analyzovat několik druhů informací. Následující části popisují běžné scénáře.

### <a name="quick-performance-analysis"></a>Rychlá analýza výkonu

Tento scénář shromažďuje základní informace, jako je například konfigurace úložiště a hardwaru virtuálního počítače, různé protokoly, včetně:

- Informace o operačním systému

- Informace o zařízení PCI

- Obecné protokoly hostovaného operačního es

- Konfigurační soubory

- Informace o úložišti

- Konfigurace virtuálního počítače Azure (shromážděná pomocí [služby metadat instance Azure)](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

- Seznam spuštěných procesů, využití disku, paměti a procesoru

- Informace o síti

Jedná se o pasivní kolekci informací, které by neměly mít vliv na systém.

>[!Note]
>Scénář rychlé analýzy výkonu je automaticky zahrnut do každého z následujících scénářů:

### <a name="performance-analysis"></a>Analýza výkonu

Tento scénář je podobný rychlé analýze výkonu, ale umožňuje zachytávání diagnostických informací po delší dobu.

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Jaký druh informací shromažďuje PerfInsights

Shromažďují se informace o virtuálním počítači linuxu, operačním systému, blokových zařízeních, vysokých zdrojích prostředků, konfiguraci a různých protokolech. Tady jsou další podrobnosti:

- Operační systém
  - Linux distribuce a verze
  - Informace o jádru
  - Informace o řidiči

- Hardware
  - Zařízení PCI`*`[ ]

- Procesy a paměť
  - Seznam procesů (název úlohy, použitá paměť, otevřené soubory)
  - Celková, dostupná a volná fyzická paměť
  - Celková, dostupná a volná odkládací paměť
  - Profilování zachycení procesoru a zpracovává využití procesoru v intervalu 5 sekund
  - Profilování zachycení využití paměti procesů v intervalu 5 sekund

- Síťové služby  
  - Seznam síťových adaptérů se statistikou adaptérů
  - Síťová směrovací tabulka
  - Otevřené porty a stav

- Úložiště
  - Seznam blokovaných zařízení
  - Seznam oddílů
  - Seznam přípojkových bodů
  - Informace o svazku MDADM
  - Informace o svazku LVM
  - Profilování zachycení na všech discích v intervalu 5 sekund

- Protokoly
  - /var/log/zprávy
  - /var/log/syslog
  - /var/log/kern.log
  - /var/log/cron.log
  - /var/log/boot.log
  - /var/log/yum.log
  - /var/log/dpkg.log
  - /var/log/cloud-init.log
  - /var/log/cloud-init-output.log
  - /var/log/gpu-manager.log
  - /var/log/waagent.log
  - /var/log/azure/[rozšiřující složka]/\*log\*
  - /var/opt/microsoft/omsconfig/omsconfig.log
  - /var/opt/microsoft/omsagent/log/omsagent.log
  - /etc/waagent.config
  - Výstup deníku za posledních pět dní

- [Metadata instance virtuálního počítače Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

>[!Note]
>[`*`] Informace pci ještě nejsou shromažďovány na distribucích Debianu a SLES

## <a name="run-the-perfinsights-linux-on-your-vm"></a>Spuštění Linuxu PerfInsights na vašem virtuálním počítači

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Co musím vědět, než spustím nástroj

#### <a name="tool-requirements"></a>Požadavky na nástroje

- Tento nástroj musí být spuštěn na virtuálním počítači, který má problém s výkonem.
- Python 2.7 musí být nainstalovaný na virtuálním počítači.

- V současné době jsou podporovány následující distribuce:

    | Distribuce               | Version                                         |
    |----------------------------|-------------------------------------------------|
    | Oracle Linux Server        | 6.10`*`[ ], 7.3, 7.6, 7.5 (Oracle-Database-Ee 13.8 marketplace image)|
    | CentOS                     | 6,5`*`[ ], 7,6                                    |
    | RHEL                       | 7.2, 7.5, 8.0 [`*`]                               |
    | Ubuntu                     | 14.04, 16.04, 18.04                               |
    | Debian                     | 8, 9, 10 [`*`]                                    |
    | SLES                       | 12 AKTUALIZACE`*`SP4 [ ]                                      |
    |                            |                                                   |

>[!Note]
>[`*`] Viz část [Známé problémy](#known-issues)

### <a name="known-issues"></a>Známé problémy

- RHEL 8 nemá ve výchozím nastavení nainstalovaný Python. Chcete-li spustit PerfInsights Linux, musíte nejprve nainstalovat Python 2.7

- Shromažďování informací agenta hosta může selhat na CentOS 6.x

- Informace o zařízeních PCI nejsou shromažďovány na distribucích založených na Debianu

- Informace o LVM jsou částečně shromažďovány v některých distribucích

### <a name="how-do-i-run-perfinsights"></a>Jak spustím PerfInsights

PerfInsights můžete spustit na virtuálním počítači instalací Azure Performance Diagnostics z webu Azure Portal. Můžete jej také spustit jako samostatný nástroj.

>[!Note]
>PerfInsights jednoduše shromažďuje a analyzuje data. Neprovádí žádné změny v systému.

#### <a name="install-and-run-perfinsights-from-the-azure-portal"></a>Instalace a spuštění PerfInsights z webu Azure Portal

Další informace o této možnosti naleznete v [tématu Diagnostika výkonu Azure](performance-diagnostics.md).  

#### <a name="run-perfinsights-in-standalone-mode"></a>Spuštění perfinsights v samostatném režimu

Chcete-li spustit nástroj PerfInsights, postupujte takto:

1. Stáhněte si [PerfInsights.tar.gz](https://aka.ms/perfinsightslinuxdownload) do složky ve virtuálním počítači a extrahujte obsah pomocí níže uvedených příkazů z terminálu.

   ```bash
   wget https://download.microsoft.com/download/9/F/8/9F80419C-D60D-45F1-8A98-718855F25722/PerfInsights.tar.gz
   ```

   ```bash
   tar xzvf PerfInsights.tar.gz
   ```

2. Přejděte do složky obsahující `perfinsights.py` soubor `perfinsights.py` a pak spusťte zobrazení dostupných parametrů příkazového řádku.

    ```bash
    cd <the path of PerfInsights folder>
    sudo python perfinsights.py
    ```

    ![Snímek obrazovky s výstupem příkazového řádku PerfInsights Linux](media/how-to-use-perfinsights-linux/perfinsights-linux-commandline.png)

    Základní syntaxe pro spuštění scénářů PerfInsights je:

    ```bash
    sudo python perfinsights.py -r <ScenarioName> -d [duration]<H | M | S> [AdditionalOptions]
    ```

    Pomocí níže uvedeného příkladu můžete spustit scénář rychlé analýzy výkonu po dobu 1 minuty a vytvořit výsledky ve složce /tmp/output:

    ```bash
    sudo python perfinsights.py -r quick -d 1M -a -o /tmp/output
    ```

    Níže uvedený příklad můžete použít ke spuštění scénáře analýzy výkonu po dobu 5 minut a nahrání výsledku tar ball na účet úložiště:

    ```bash
    sudo python perfinsights.py -r vmslow -d 300S -a -t <StorageAccountName> -k <StorageAccountKey> -i <full resource Uri of the current VM>
    ```

    >[!Note]
    >Před spuštěním scénáře perfInsights vyzve uživatele, aby souhlasil se sdílením diagnostických informací a souhlasem s eula. Pomocí **možnosti -a nebo --accept-disclaimer-and-share-diagnostics** tyto výzvy přeskočíte.
    >
    >Pokud máte aktivní lístek podpory s Microsoftem a spuštění PerfInsights na žádost pracovníka podpory, se kterým pracujete, nezapomeňte zadat číslo lístku podpory pomocí možnosti **-s nebo --support-request.**

Po dokončení spuštění se ve stejné složce jako PerfInsights zobrazí nový soubor tar, pokud není zadána žádná výstupní složka. Název souboru je **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.tar.gz.** Tento soubor můžete odeslat agentovi podpory k analýze nebo otevřít sestavu uvnitř souboru a zkontrolovat zjištění a doporučení.

## <a name="review-the-diagnostics-report"></a>Kontrola diagnostické sestavy

V souboru **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.tar.gz** najdete zprávu HTML, která podrobně popisuje zjištění PerfInsights. Chcete-li zprávu zkontrolovat, rozbalte soubor **PerformanceDiagnostics\_\_yyyy-MM-dd hh-mm-ss-fff.tar.gz** a otevřete soubor **PerfInsights Report.html.**

### <a name="overview-tab"></a>Karta Přehled

Karta **Přehled** obsahuje základní podrobnosti o spuštění a informace o virtuálním počítači. Karta **Zjištění** zobrazuje souhrn doporučení ze všech různých částí sestavy PerfInsights.

![Snímek obrazovky sestavy PerfInsights](media/how-to-use-perfinsights-linux/perfinsights-linux-overview.png)  
![Snímek obrazovky sestavy PerfInsights](media/how-to-use-perfinsights-linux/perfinsights-linux-findings-tab.png)

> [!NOTE]
> Zjištění zařazená do kategorie vysoká jsou známé problémy, které mohou způsobit problémy s výkonem. Zjištění zařazená do kategorie média představují neoptimální konfigurace, které nemusí nutně způsobovat problémy s výkonem. Zjištění zařazená do kategorie low jsou pouze informativní.

Projděte si doporučení a odkazy pro všechny vysoké a střední nálezy. Přečtěte si, jak mohou ovlivnit výkon, a také osvědčené postupy pro konfigurace optimalizované pro výkon.

### <a name="cpu-tab"></a>Karta CPU

Karta **CPU** poskytuje informace o spotřebě procesoru v celém systému během spuštění PerfInsights. Informace o vysoké období využití procesoru a top dlouho běžící procesoru spotřebitelé budou užitečné pro řešení problémů s vysokým procesorem.

![Snímek obrazovky s kartou Procesor sestavy PerfInsights](media/how-to-use-perfinsights-linux/perfinsights-linux-cpu-tab.png)

### <a name="storage-tab"></a>Karta Úložiště

V části **Zjištění** jsou zobrazena různá zjištění a doporučení týkající se úložiště.

**Bloková zařízení** a další související **oddíly,** jako jsou karty Oddíly , **LVM**a **MDADM,** popisují, jak jsou bloková zařízení konfigurována a vzájemně propojena.

![Snímek obrazovky s kartou úložiště](media/how-to-use-perfinsights-linux/perfinsights-linux-storage-tab.png)  
![Snímek obrazovky s kartou MDADM](media/how-to-use-perfinsights-linux/perfinsights-linux-mdadm-config.png)

### <a name="linux-tab"></a>Karta Linux

Karta **Linux** obsahuje informace o hardwaru a operačním systému spuštěném ve vašem virtuálním počítači. Podrobnosti zahrnují seznam spuštěných procesů a informace o agentovi hosta, PCI, procesoru, ovladačích a ovladačích LIS.

![Snímek obrazovky s kartou Linux](media/how-to-use-perfinsights-linux/perfinsights-linux-tab.png)

## <a name="next-steps"></a>Další kroky

Protokoly diagnostiky a sestavy můžete odeslat do podpory společnosti Microsoft k dalšímu prohledání. Při práci s pracovníky podpory společnosti Microsoft mohou požadovat, abyste přenesli výstup generovaný perfInsights, který vám pomůže s procesem řešení potíží.

Následující snímek obrazovky zobrazuje zprávu podobnou tomu, co se vám může zobrazit:

![Snímek obrazovky s ukázkovou zprávou z podpory Microsoftu](media/how-to-use-perfinsights-linux/support-email.png)

Podle pokynů ve zprávě získáte přístup k pracovnímu prostoru přenosu souborů. Pro další zabezpečení je třeba změnit heslo při prvním použití.

Po přihlášení najdete dialogové okno pro nahrání souboru **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.tar.gz,** který byl shromážděn společností PerfInsights.
