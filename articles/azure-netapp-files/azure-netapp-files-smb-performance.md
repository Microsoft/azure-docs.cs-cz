---
title: Nejčastější dotazy týkající se výkonu protokolu SMB pro Azure NetApp Files | Microsoft Docs
description: Odpovědi na nejčastější dotazy týkající se výkonu protokolu SMB pro Azure NetApp Files.
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
ms.openlocfilehash: b01ab9787f86e6905f8d25ad4609385e3f6b6a5a
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91628477"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>Nejčastější dotazy týkající se výkonu protokolu SMB pro Azure NetApp Files

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se osvědčených postupů výkonu protokolu SMB pro Azure NetApp Files.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>Je ve sdílených složkách SMB povolená možnost vícekanálový protokol SMB? 

Ano, ve výchozím nastavení je povolená možnost SMB vícekanálový, jedná se o změnu, kterou zadáte na začátku ledna 2020. Všechny sdílené složky SMB, které mají předem dating existující svazky SMB, měly povolenu funkci a všechny nově vytvořené svazky budou mít také povolenou funkci v době vytváření. 

Všechna připojení SMB vytvořená před povolením této funkce bude nutné resetovat, aby bylo možné využívat vícekanálový funkce protokolu SMB. Pokud chcete resetovat, můžete sdílenou složku SMB odpojit a znovu připojit.

## <a name="is-rss-supported"></a>Podporuje se RSS?

Ano, Azure NetApp Files podporuje škálování na straně příjmu (RSS).

V případě, že je povolená funkce SMB vícekanálový, naváže klient SMB3 několik připojení TCP k Azure NetApp Files serveru SMB přes síťovou kartu (NIC), která podporuje jediný RSS. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Které verze systému Windows podporují vícekanálový protokol SMB?

Windows podporuje vícekanálový protokol SMB, protože systém Windows 2012 pro zajištění nejlepšího výkonu.  Podrobnosti najdete v tématu [nasazení vícekanálového protokolu SMB](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) a [základní informace o službě SMB vícekanálový](https://blogs.technet.microsoft.com/josebda/2012/06/28/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0/) . 


## <a name="does-my-azure-virtual-machine-support-rss"></a>Podporuje můj virtuální počítač Azure technologii RSS?

Pokud chcete zjistit, jestli vaše síťové adaptéry virtuálních počítačů Azure podporují RSS, spusťte příkaz takto `Get-SmbClientNetworkInterface` a zkontrolujte pole `RSS Capable` : 

![Snímek obrazovky, který zobrazuje výstup RSS pro virtuální počítač Azure.](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Podporuje Azure NetApp Files funkci SMB Direct?

Ne, Azure NetApp Files nepodporuje funkci SMB Direct. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Jaká je výhoda vícekanálového protokolu SMB? 

Vícekanálový funkce protokolu SMB umožňuje klientovi SMB3 vytvořit fond připojení přes jednu síťovou kartu (NIC) nebo více síťových adaptérů a použít je k posílání požadavků na jednu relaci SMB. Na rozdíl od návrhu SMB1 a SMB2 vyžadují, aby klient navázal jedno připojení a odesílal všechny přenosy protokolu SMB pro danou relaci přes toto připojení. Toto jediné připojení omezuje výkon celkového protokolu, který je možné dosáhnout z jednoho klienta.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>Mám v mém klientovi nakonfigurovat více síťových adaptérů pro protokol SMB?

No. Klient SMB bude odpovídat počtu síťových adaptérů vráceného serverem SMB.  Každý svazek úložiště je přístupný z jednoho a pouze jednoho koncového bodu úložiště.  To znamená, že pro všechny relace SMB se bude používat jenom jedna síťová karta.  

Jak `Get-SmbClientNetworkInterace` ukazuje výstup níže, má virtuální počítač 2 síťová rozhraní--15 a 12.  Jak je znázorněno v následujícím příkazu `Get-SmbMultichannelConnection` , i když jsou k dispozici dva síťové adaptéry podporující kanály RSS, používá se v připojení ke sdílené složce SMB jenom rozhraní 12. rozhraní 15 se nepoužívá.

![Uvedeném, která zobrazuje výstup pro síťové adaptéry podporující RSS.](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Podporuje se seskupování síťových adaptérů v Azure?

Seskupování síťových adaptérů se v Azure nepodporuje. I když na virtuálních počítačích Azure podporuje více síťových rozhraní, představují logické místo fyzické konstrukce. V takovém případě neposkytují odolnost proti chybám.  Šířka pásma dostupná pro virtuální počítač Azure se taky počítá pro samotný počítač a ne pro každé konkrétní síťové rozhraní.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Jaký je výkon jako u protokolu SMB vícekanálový?

Následující testy a grafy ukazují sílu rozhraní SMB vícekanálový na úlohách s jednou instancí.

### <a name="random-io"></a>Náhodné vstupně-výstupní operace  

V případě, že je v klientovi zakázaná funkce SMB, byla provedena čistě 4 testy čtení a zápisu KiB pomocí FIO a pracovní sady GiB 40.  Sdílená složka SMB byla mezi jednotlivými testy odpojena s přírůstekem počtu připojení klientů SMB podle nastavení síťového rozhraní RSS `1` ,,, `4` `8` `16` , `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>` . Testy ukazují, že výchozí nastavení `4` je dostatečné pro úlohy náročné na vstupně-výstupní operace; zvýšení na `8` a `16` mělo zanedbatelný účinek. 

Příkaz `netstat -na | findstr 445` ukázal, že další připojení byla vytvořena s přírůstky od `1` do `4` až do `8` `16` .  Čtyři jádra procesoru se během každého testu plně využila pro protokol SMB, jak potvrzují statistiky Perfmon `Per Processor Network Activity Cycles` (nezahrnuje se v tomto článku).

![Graf, který znázorňuje náhodné porovnání vstupně-výstupních operací s protokolem SMB vícekanálový.](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Virtuální počítač Azure nemá vliv na limity vstupně-výstupních operací úložiště SMB (ani NFS).  Jak je znázorněno v následujícím grafu, typ instance D32ds má omezení 308 000 pro úložiště s mezipamětí a 51 200 pro neuložené IOPS úložiště.  Graf výše ale ukazuje mnohem více vstupně-výstupních operací přes protokol SMB.

![Graf, který znázorňuje náhodný srovnávací vstupně-výstupní operace testu.](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>Sekvenční v/v 

Testy podobné testům náhodných vstupně-výstupních operací byly provedeny pomocí 64-KiB sekvenčních vstupně-výstupních operací. I když se zvýšení počtu připojení klientů na síťové rozhraní RSS nad rámec 4 neprojeví v náhodných vstupně-výstupních operacích, neplatí to pro sekvenční vstupně-výstupní operace. Jak ukazuje následující graf, každé zvýšení je přidruženo k odpovídajícímu nárůstu propustnosti čtení. Propustnost zápisu trvalá v důsledku omezení šířky pásma sítě, které Azure používá pro jednotlivé typy a velikosti instancí. 

![Graf, který zobrazuje porovnání testů propustnosti.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure umisťuje omezení přenosové rychlosti sítě na jednotlivé typy a velikosti virtuálních počítačů. Limit přenosové rychlosti se ukládá jenom na odchozí provoz. Počet síťových adaptérů přítomných na virtuálním počítači nemá žádný vliv na celkovou šířku pásma dostupnou pro daný počítač.  Například typ instance D32ds má zavedený limit sítě 16 000 MB/s (2 000 MiB/s).  Jak vidíte sekvenční graf výše, má tento limit vliv na odchozí přenos (zápisy), ale ne vícekanálový čtení.

![Graf, který ukazuje přírůstkový vstupně-výstupní porovnání testů.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="what-performance-is-expected-with-a-single-instance-with-a-1-tb-dataset"></a>Jaký výkon se očekává s jedinou instancí s datovou sadou 1 TB?

Pro zajištění podrobnějšího přehledu o úlohách pomocí směsí pro čtení a zápis se v následujících dvou grafech zobrazuje výkon jediného cloudu úrovně Ultra na úrovni služby 50 TB s datovou sadou 1 TB a s protokolem SMB vícekanálový 4. Použil se optimální IODeptha 16 a v/v byly použity parametry flexibilní v/v (FIO), které zajistí plné využití šířky pásma sítě ( `numjobs=16` ).

Následující graf znázorňuje výsledky 4k náhodných vstupně-výstupních operací s jednou instancí virtuálních počítačů a kombinací čtení/zápisu v 10% intervalech:

![Graf, který zobrazuje Windows 2019 Standard _D32ds_v4 4K test Random v/v.](../media/azure-netapp-files/smb-performance-standard-4k-random-io.png)

Následující graf znázorňuje výsledky sekvenčního vstupu a výstupu:

![Graf, který zobrazuje Sekvenční propustnost Windows 2019 Standard _D32ds_v4 64.](../media/azure-netapp-files/smb-performance-standard-64k-throughput.png)

## <a name="what-performance-is-expected-when-scaling-out-using-5-vms-with-a-1-tb-dataset"></a>Jaký výkon se očekává při horizontálním navýšení kapacity pomocí 5 virtuálních počítačů s datovou sadou o velikosti 1 TB?

Tyto testy s 5 virtuálními počítači používají stejné testovací prostředí jako jeden virtuální počítač, přičemž každý proces zapisuje do vlastního souboru.

Následující graf znázorňuje výsledky náhodných vstupně-výstupních operací:

![Graf, který zobrazuje Windows 2019 Standard _D32ds_v4 4K 5-instance randio IO test.](../media/azure-netapp-files/smb-performance-standard-4k-random-io-5-instances.png)

Následující graf znázorňuje výsledky sekvenčního vstupu a výstupu:

![Graf, který zobrazuje Windows 2019 Standard _D32ds_v4 64 KB-instance Sekvenční propustnost.](../media/azure-netapp-files/smb-performance-standard-64k-throughput-5-instances.png)

## <a name="how-do-you-monitor-hyper-v-ethernet-adapters-and-ensure-that-you-maximize-network-capacity"></a>Jak monitorovat adaptéry pro ethernetové technologie Hyper-V a zajistit maximální kapacitu sítě?  

Jedna strategie používaná při testování pomocí FIO je nastavena `numjobs=16` . Rozvětvení každé úlohy do 16 specifických instancí maximalizuje Microsoft Hyper-V síťový adaptér.

Aktivitu můžete u každého z adaptérů v nástroji Sledování výkonu systému Windows ověřit tak, že vyberete **sledování výkonu > přidat čítače > síťové rozhraní > Microsoft Hyper-V síťový adaptér**.

![Snímek obrazovky, který ukazuje sledování výkonu přidat rozhraní čítače.](../media/azure-netapp-files/smb-performance-performance-monitor-add-counter.png)

Po spuštění datového provozu na svazcích můžete monitorovat adaptéry v nástroji Sledování výkonu systému Windows. Pokud nepoužíváte všechny tyto 16 virtuální adaptéry, možná nemaximalizujete kapacitu šířky pásma sítě.

![Snímek obrazovky, který zobrazuje výstup sledování výkonu.](../media/azure-netapp-files/smb-performance-performance-monitor-output.png)

## <a name="is-accelerated-networking-recommended"></a>Doporučuje se akcelerované síťové služby?

Pro maximální výkon se doporučuje nakonfigurovat [urychlené síťové služby](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) tam, kde je to možné. Mějte na paměti následující skutečnosti:  

* Azure Portal ve výchozím nastavení povoluje akcelerované síťové služby pro virtuální počítače, které tuto funkci podporují.  Jiné metody nasazení, jako například Ansible a podobné konfigurační nástroje, ale nemusí.  Nepovedlo se povolit akcelerované síťové služby, může Hobble výkon počítače.  
* Pokud není v síťovém rozhraní virtuálního počítače povolená možnost akcelerované sítě kvůli nedostatku podpory pro typ nebo velikost instance, zůstane zakázaná s většími typy instancí. V těchto případech budete potřebovat ruční zásah.

## <a name="are-jumbo-frames-supported"></a>Podporují se rámce typu Jumbo?

Virtuální počítače Azure nepodporují rámce typu Jumbo.

## <a name="is-smb-signing-supported"></a>Je podepisování SMB podporováno? 

Protokol SMB poskytuje základ pro sdílení souborů a tiskáren a další síťové operace, jako je vzdálená správa systému Windows. Aby nedocházelo k útokům prostředníkem, které mění pakety SMB při přenosu, podporuje protokol SMB digitální podepisování paketů SMB. 

Podepisování SMB se podporuje pro všechny verze protokolu SMB, které podporuje Azure NetApp Files. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Jaký je dopad podepisování SMB na výkon?  

Podepisování SMB má škodlivý účinek na výkon protokolu SMB. Kromě jiných potenciálních příčin snížení výkonu digitální podepisování každého paketu spotřebovává další procesor na straně klienta, jak ukazuje následující výstup Perfmon. V tomto případě se zobrazí základní hodnota 0 zodpovědná za SMB, včetně podepisování SMB.  Porovnání s čísly propustnosti nevícekanálových sekvenčních čtení v předchozí části ukazuje, že podepisování SMB snižuje celkovou propustnost z 875MiB/s na přibližně 250MiB/s. 

![Graf, který zobrazuje dopad na výkon podepisování protokolu SMB.](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>Další kroky  

- [Nejčastější dotazy týkající se Azure NetApp Files](azure-netapp-files-faqs.md)
- Přečtěte si téma [Azure NetApp Files: spravované podnikové sdílení souborů pro úlohy SMB](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) týkající se použití sdílených složek smb s Azure NetApp Files.
