---
title: Řešení potíží s pomalým zálohování souborů a složek ve službě Azure Backup | Microsoft Docs
description: Poskytuje pokyny k odstraňování problémů při určování příčin problémů s výkonem Azure Backup
services: backup
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
ms.assetid: e379180a-db13-4e0c-90e4-28e5dd6f5b14
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 6ed1e2d7bfc08afe135cb85995fdebaa30202c23
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Řešení potíží s pomalým zálohováním souborů a složek ve službě Azure Backup
Tento článek obsahuje pokyny k odstraňování problémů při diagnostice příčinu pomalý výkon zálohování pro soubory a složky, pokud používáte Azure Backup. Pokud používáte Azure Backup agent k zálohování souborů, proces zálohování může trvat déle než obvykle. Toto zpoždění může být způsobena jednou nebo více z následujících akcí:

* [V počítači, který je zálohovaných existují kritické.](#cause1)
* [Jiný proces nebo antivirový software narušuje proces zálohování Azure.](#cause2)
* [Agent zálohování je spuštěn na virtuální počítač Azure (VM).](#cause3)  
* [Zálohujete velký počet (miliony) soubory.](#cause4)

Než začnete, řešení potíží, doporučujeme stáhnout a nainstalovat [nejnovější verze agenta Azure Backup](http://aka.ms/azurebackup_agent). Provedeme časté aktualizace agenta zálohování různé problémy, přidat funkce, a zlepšit výkon.

Také důrazně doporučujeme, abyste si prošli [nejčastější dotazy týkající se služby Azure Backup](backup-azure-backup-faq.md) a ujistěte se, nejsou některé běžné problémy s konfigurací vyskytují.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Příčina: Kritické body v počítači
Kritická místa v počítači, který je zálohovaných může způsobit zpoždění. Například počítači umožňuje čtení nebo zápisu na disk nebo dostupnou šířku pásma k odesílání dat přes síť, může způsobit kritická místa.

Systém Windows nabízí integrované nástroj, který se nazývá [sledování výkonu](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) ke zjištění těchto kritická místa.

Tady jsou některé čítače výkonu a rozsahy, které může být užitečné při diagnostikování kritická místa pro optimální zálohy.

| Čítač | Status |
| --- | --- |
| Logický Disk (fyzického disku)--% nečinnosti |• 100 % nečinnosti na 50 % nečinnosti = stavu v pořádku</br>• 49 % nečinnosti 20 % nečinnosti = upozornění nebo monitorování</br>• 19 % nečinnosti 0 % nečinnosti = kritický nebo mimo specifikace |
| Logický Disk (fyzického disku)--% střední Doba disku čtení nebo zápisu |• 0,001 ms k 0,015 ms = stavu v pořádku</br>• 0,015 ms na 0,025 ms = upozornění nebo monitorování</br>• 0.026 ms nebo již = kritický nebo mimo specifikace |
| Logický Disk (fyzického disku)--aktuální délka fronty disku (pro všechny instance) |80 požadavků po dobu více než 6 minut |
| Paměť – bajty stránkovaného fondu jiný |• Méně než 60 % fondu spotřebované = stavu v pořádku<br>• 61 % až 80 % fondu spotřebované = upozornění nebo monitorování</br>• Větší než 80 % fondu spotřebované = kritický nebo mimo specifikace |
| Paměť – bajty stránkovaného fondu |• Méně než 60 % fondu spotřebované = stavu v pořádku</br>• 61 % až 80 % fondu spotřebované = upozornění nebo monitorování</br>• Větší než 80 % fondu spotřebované = kritický nebo mimo specifikace |
| Paměť – dostupný počet megabajtů |• 50 % volné paměti k dispozici nebo více = stavu v pořádku</br>• 25 % volné paměti k dispozici = monitorování</br>• 10 % volné paměti k dispozici = upozornění</br>• Méně než 100 MB nebo 5 % volné paměti k dispozici = kritický nebo mimo specifikace |
| Procesor –\%času procesoru (všechny instance) |• Méně než 60 % spotřebované = stavu v pořádku</br>• 61 % až 90 % spotřebované = monitorování nebo upozornění</br>• 91 až 100 % spotřebované = kritický |

> [!NOTE]
> Pokud zjistíte, že infrastruktury který, doporučujeme defragmentaci disky pravidelně pro lepší výkon.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Příčina: Jiného procesu nebo antivirový software zasahovala do činnosti Azure Backup
Jsme viděli několik instancí, kde jiné procesy v systému Windows negativně ovlivňovat výkon proces agenta Azure Backup. Například pokud používáte agenta Azure Backup a jinou aplikací zálohování dat, nebo pokud antivirový software běží a má zámek na soubory zálohovat, násobek zamkne na souborů může způsobit kolizí. V této situaci může dojít k selhání zálohování nebo úlohy může trvat déle, než se očekávalo.

Nejlepší doporučení v tomto scénáři je vypnout jinou zálohování aplikaci a zkontrolujte, zda změnil čas zálohování pro Azure Backup agent. A ujistěte se, že několik úloh zálohování nejsou spuštěné ve stejnou dobu je obvykle, aby s nimi neovlivňovaly navzájem dostatečná.

Pro antivirové programy doporučujeme, abyste vyloučili následující soubory a umístění:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe jako proces
* Složky C:\Program Files\Microsoft Azure Recovery Services Agent\
* Cesta k pomocnému umístění (Pokud nepoužíváte standardní umístění)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Příčina: Zálohování agenta spuštěného na virtuální počítač Azure
Pokud používáte agenta zálohování na virtuálním počítači, bude výkon nižší než při jeho spuštění na fyzickém počítači. Očekává se z důvodu omezení IOPS.  Však můžete optimalizovat výkon přepínání datových jednotkách, které jsou zálohovány do služby Azure Premium Storage. Pracujeme na řešení tohoto problému a opravu bude k dispozici v budoucí verzi.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Příčina: Zálohování velký počet (miliony) souborů
Přesunutí velký objem dat bude trvat déle než přesouvání menší objem dat. V některých případech se týká čas zálohování nejen velikost dat, ale také počet soubory nebo složky. To platí hlavně když probíhá zálohování milióny malých souborů (několik bajty k několika kilobajtů).

K tomuto chování dochází, protože když jste zálohování dat a ani ji přesunout do Azure, Azure je současně katalogizaci vaše soubory. V některých scénářích výjimečných operace katalogu může trvat déle než obvykle.

Následující ukazatele vám může pomoct pochopit kritická místa a odpovídajícím způsobem pracovat na další kroky:

* **Uživatelské rozhraní se zobrazuje průběh pro přenos dat**. Data se stále přenosu. Šířku pásma sítě nebo velikosti dat může být příčinou zpoždění.
* **Uživatelského rozhraní není zobrazující průběh pro přenos dat**. Otevřete protokoly umístěnými na C:\Microsoft Azure Recovery Services Agent\Temp a pak vyhledejte položku FileProvider::EndData v protokolech. Tato položka označuje, že přenos dat bylo dokončeno probíhá operace katalogu. Nemáte zrušení úloh zálohování. Místo toho počkejte trochu déle na dokončení operace katalogu. Pokud potíže potrvají, obraťte se na [podporu Azure](https://portal.azure.com/#create/Microsoft.Support).
