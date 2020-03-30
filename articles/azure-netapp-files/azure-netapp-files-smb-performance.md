---
title: Časté otázky týkající se výkonu SMB pro soubory Azure NetApp| Dokumenty společnosti Microsoft
description: Odpovědi na nejčastější dotazy týkající se výkonu SMB pro soubory Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: 24b3710861f0ee158619ae9103584dcdb181f3d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460445"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>Časté otázky týkající se výkonu SMB pro soubory Azure NetApp

Tento článek odpovídá na nejčastější dotazy týkající se doporučených postupů pro výkon SMB pro soubory Azure NetApp.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>Je ve sdílených složek SMB povolen vícekanálový smb SMB? 

Ano, Vícekanálový SMB je ve výchozím nastavení povolen, změna zavedená na začátku ledna 2020. Všechny sdílené složky SMB, které již dříve datují existující svazky SMB, mají tuto funkci povolenou a všechny nově vytvořené svazky budou mít také tuto funkci povolenou v době vytvoření. 

Jakékoli připojení SMB navázáno před povolením funkce bude nutné obnovit, aby bylo možné využít vícekanálové funkce SMB. Chcete-li obnovit, můžete odpojit a znovu připojit sdílenou složku SMB.

## <a name="is-rss-supported"></a>Je systém RSS podporován?

Ano, Soubory Azure NetApp podporuje škálování na straně příjmu (RSS).

S povolenou vícekanálovou službou SMB vytvoří klient SMB3 více připojení TCP k serveru SMB souborů Azure NetApp prostředněprostředně karta síťového rozhraní (NIC), která je schopná jednoho RSS. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Které verze systému Windows podporují vícekanálové verze SMB?

Systém Windows podporuje vícekanálový systém SMB od roku 2012, aby umožnil nejlepší výkon.  Podrobnosti [najdete v tématu Nasazení vícekanálového kanálu SMB](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) a [základy vícekanálového kanálu SMB.](https://blogs.technet.microsoft.com/josebda/2012/06/28/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0/) 


## <a name="does-my-azure-virtual-machine-support-rss"></a>Podporuje můj virtuální počítač Azure rss?

Pokud chcete zjistit, jestli vaše síťové karty virtuálního počítače Azure podporují službu RSS, spusťte příkaz `Get-SmbClientNetworkInterface` následujícím způsobem a zaškrtněte pole `RSS Capable`: 

![Podpora RSS pro virtuální počítač Azure](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Podporuje soubory Azure NetApp službu SMB Direct?

Ne, soubory Azure NetApp nepodporují službu SMB Direct. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Jaká je výhoda vícekanálového smb? 

Vícekanálová funkce SMB umožňuje klientovi SMB3 vytvořit fond připojení přes jednu kartu síťového rozhraní (NIC) nebo více síťových karet a použít je k odesílání požadavků na jednu relaci SMB. Naopak podle záměru SMB1 a SMB2 vyžadují klienta navázat jedno připojení a odeslat všechny přenosy SMB pro danou relaci přes toto připojení. Toto jediné připojení omezuje celkový výkon protokolu, kterého lze dosáhnout z jednoho klienta.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>Mám nakonfigurovat více nic na mém klienta pro SMB?

Ne. Klient SMB bude odpovídat počtu nic vrácených serverem SMB.  Každý svazek úložiště je přístupný z jednoho a pouze jednoho koncového bodu úložiště.  To znamená, že pro daný vztah SMB bude použita pouze jedna žádná z komunikačních nik.  

Jak ukazuje `Get-SmbClientNetworkInterace` výstup níže, virtuální počítač má dvě síťová rozhraní - 15 a 12.  Jak je uvedeno `Get-SmbMultichannelConnection`níže pod příkazem , i když existují dva NICS podporující RSS, pouze rozhraní 12 se používá ve spojení se sdílenou složky SMB; rozhraní 15 se nepoužívá.

![NICS s podporou RSS](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Je v Azure podporováno seskací moduly nic?

Seskatouje se v Azure sesklivé nic. I když více síťových rozhraní jsou podporovány na virtuálních počítačích Azure, představují logické spíše než fyzické konstrukce. Jako takové neposkytují žádnou odolnost proti chybám.  Šířka pásma dostupná virtuálnímu počítači Azure se také vypočítá pro samotný počítač a ne pro jakékoli jednotlivé síťové rozhraní.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Jaký je výkon pro SMB Multichannel?

Následující testy a grafy ukazují výkon Vícekanálový SMB na úlohy jedné instance.

### <a name="random-io"></a>Náhodné vstupně-nosné  

S Vícekanálovým smb zakázáným na straně klienta byly provedeny čisté testy čtení a zápisu 8 KiB pomocí FIO a pracovní sady 40-GiB.  Sdílená položka SMB byla odpojena mezi jednotlivými testy s přírůstky počtu připojení `1`klienta`8``16`SMB podle nastavení síťového rozhraní RSS v , `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>``4`, . Testy ukazují, že výchozí `4` nastavení je dostatečná pro vstupně-v intenzivní úlohy; a `8` `16` neměl žádný účinek. 

Příkaz `netstat -na | findstr 445` prokázal, že byla navázána další `1` `4` připojení `8` s `16`přírůstky od do do a do .  Čtyři cpu jádra byly plně využity pro SMB během každého `Per Processor Network Activity Cycles` testu, jak potvrzuje statistika perfmon (není zahrnuta v tomto článku.)

![Náhodné V/O testy](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Virtuální počítač Azure nemá vliv na omezení vstupně-videa úložiště SMB (ani NFS).  Jak je znázorněno níže, typ instance D16 má omezenou rychlost 32 000 pro vstupně-příkazové zařízení úložiště uložené v mezipaměti a 25 600 pro vstupně-příkazové paměťové vstupně-upy bez mezipaměti.  Výše uvedený graf však ukazuje výrazně více vstupně-do/o přes SMB.

![Náhodné porovnání vstupně-va/v](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>Sekvenční va 

Testy podobné výše popsaným náhodným vstupně-a v.o testům byly provedeny s 64-KiB sekvenčními V/O. Přestože zvýšení počtu připojení klienta na rss síťové rozhraní nad 4 ' neměl žádný znatelný vliv na náhodné V/O, totéž se nevztahuje na sekvenční V/O. Jak ukazuje následující graf, každé zvýšení je spojeno s odpovídajícím zvýšením propustnostčtení. Propustnost zápisu zůstala plochá z důvodu omezení šířky pásma sítě, která azure umístil pro každý typ nebo velikost instance. 

![Sekvenční vstupně-v.a.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure klade omezení síťové sazby pro každý typ nebo velikost virtuálního počítače. Limit sazby je uložen pouze pro odchozí provoz. Počet nic přítomných na virtuálním počítači nemá žádný vliv na celkovou šířku pásma, která je k dispozici pro stroj.  Například typ instance D16 má vynucený síťový limit 8000 Mb/s (1 000 MiB/s).  Jak ukazuje sekvenční graf výše, limit ovlivňuje odchozí provoz (zápisy), ale ne vícekanálové čtení.

![Sekvenční porovnání vstupně-va/V](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="is-accelerated-networking-recommended"></a>Doporučuje se zrychlená síť?

Chcete-li dosáhnout maximálního výkonu, doporučujeme tam, kde je to možné, nakonfigurovat [akcelerované sítě.](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) Mějte na paměti následující skutečnosti:  

* Portál Azure ve výchozím nastavení umožňuje zrychlené vytváření sítí pro virtuální počítače podporující tuto funkci.  Jiné metody nasazení, jako je například Ansible a podobné konfigurační nástroje, však nemusí.  Nepovolit akceleraci sítě může kulhat výkon počítače.  
* Pokud zrychlená síť není povolena v síťovém rozhraní virtuálního počítače z důvodu jeho nedostatečné podpory pro typ nebo velikost instance, zůstane zakázána s většími typy instancí. V těchto případech budete potřebovat ruční zásah.

## <a name="are-jumbo-frames-supported"></a>Jsou podporovány rámce jumbo?

Jumbo rámce nejsou podporovány s virtuálními počítači Azure.

## <a name="is-smb-signing-supported"></a>Je podepisování SMB podporováno? 

Protokol SMB poskytuje základ pro sdílení souborů a tiskařů a další síťové operace, jako je vzdálená správa systému Windows. Aby se zabránilo útokům prostředníkem, které upravují pakety SMB při přenosu, protokol SMB podporuje digitální podepisování paketů SMB. 

Podepisování SMB je podporováno pro všechny verze protokolu SMB, které jsou podporované soubory Azure NetApp. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Jaký je dopad podpisu smb na výkon?  

Podepisování SMB má škodlivý vliv na výkon SMB. Mezi další potenciální příčiny snížení výkonu digitální podepisování každého paketu spotřebovává další procesor na straně klienta jako výstup perfmon níže ukazuje. V tomto případě se jádro 0 zobrazí jako odpovědné za SMB, včetně podepisování SMB.  Porovnání s nevícekanálovými čísly propustností pročtení sekvenčního čtení v předchozí části ukazuje, že podepisování SMB snižuje celkovou propustnost z 875MiB/s na přibližně 250 MiB/s. 

![Dopad na výkon podepisování smb](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>Další kroky  

- [Nejčastější dotazy k souborům Azure NetApp](azure-netapp-files-faqs.md)
- Podívejte se na [Azure NetApp Files: Managed Enterprise File Shares for SMB Workloads](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) about using SMB file shares with Azure NetApp Files.
