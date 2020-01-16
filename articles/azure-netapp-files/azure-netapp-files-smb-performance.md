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
ms.date: 01/14/2020
ms.author: b-juche
ms.openlocfilehash: 6391a3eeead6a52371c11011a65f4b4de7260156
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76046419"
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

Pokud chcete zjistit, jestli vaše síťové adaptéry virtuálních počítačů Azure podporují RSS, spusťte příkaz `Get-SmbClientNetworkInterface` následujícím způsobem a zkontrolujte `RSS Capable`pole: 

![Podpora RSS pro virtuální počítač Azure](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Podporuje Azure NetApp Files funkci SMB Direct?

Ne, Azure NetApp Files nepodporuje funkci SMB Direct. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Jaká je výhoda vícekanálového protokolu SMB? 

Vícekanálový funkce protokolu SMB umožňuje klientovi SMB3 vytvořit fond připojení přes jednu síťovou kartu (NIC) nebo více síťových adaptérů a použít je k posílání požadavků na jednu relaci SMB. Na rozdíl od návrhu SMB1 a SMB2 vyžadují, aby klient navázal jedno připojení a odesílal všechny přenosy protokolu SMB pro danou relaci přes toto připojení. Toto jediné připojení omezuje výkon celkového protokolu, který je možné dosáhnout z jednoho klienta.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>Mám v mém klientovi nakonfigurovat více síťových adaptérů pro protokol SMB?

Ne. Klient SMB bude odpovídat počtu síťových adaptérů vráceného serverem SMB.  Každý svazek úložiště je přístupný z jednoho a pouze jednoho koncového bodu úložiště.  To znamená, že pro všechny relace SMB se bude používat jenom jedna síťová karta.  

Jak ukazuje výstup `Get-SmbClientNetworkInterace` níže, má virtuální počítač dvě síťová rozhraní--15 a 12.  Jak je uvedeno níže v příkazu `Get-SmbMultichannelConnection`, i když jsou k dispozici dva síťové adaptéry podporující kanály RSS, používá se v souvislosti se sdílenou složkou SMB jenom rozhraní 12. rozhraní 15 se nepoužívá.

![Síťové karty podporující RSS](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Podporuje se seskupování síťových adaptérů v Azure?

Seskupování síťových adaptérů se v Azure nepodporuje. I když na virtuálních počítačích Azure podporuje více síťových rozhraní, představují logické místo fyzické konstrukce. V takovém případě neposkytují odolnost proti chybám.  Šířka pásma dostupná pro virtuální počítač Azure se taky počítá pro samotný počítač a ne pro každé konkrétní síťové rozhraní.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Jaký je výkon jako u protokolu SMB vícekanálový?

Následující testy a grafy ukazují sílu rozhraní SMB vícekanálový na úlohách s jednou instancí.

### <a name="random-io"></a>Náhodné vstupně-výstupní operace  

Díky funkci SMB vícekanálový zakázanou na klientovi byly čistě 8-KiB testy čtení a zápisu provedeny pomocí FIO a pracovní sady 40-GiB.  Sdílená složka SMB byla mezi jednotlivými testy odpojena s přírůstkem nastavení počtu připojení klientů SMB na síťové rozhraní `1`,`4`,`8`,`16``set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>`. Testy ukazují, že výchozí nastavení `4` postačuje pro úlohy náročné na vstupně-výstupní operace. zvýšení `8` a `16` nemá žádný vliv. 

Příkaz `netstat -na | findstr 445` prokáže, že byla navázána další připojení s přírůstky `1` na `4` až `8` a `16`.  Během každého testu byla plně využita čtyři jádra procesoru pro protokol SMB, jak je potvrzené statistikou `Per Processor Network Activity Cycles` Perfmon (nezahrnuté v tomto článku).

![Náhodné vstupně-výstupní testy](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Virtuální počítač Azure nemá vliv na limity vstupně-výstupních operací úložiště SMB (ani NFS).  Jak vidíte níže, typ instance D16 má omezení 32 000 pro úložiště s mezipamětí a 25 600 pro neuložené IOPS úložiště.  Graf výše ale ukazuje mnohem více vstupně-výstupních operací přes protokol SMB.

![Porovnání náhodných vstupně-výstupních operací](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>Sekvenční v/v 

Testy podobné testům náhodných vstupně-výstupních operací byly provedeny pomocí 64-KiB sekvenčního vstupu/výstupu. I když se zvýšení počtu připojení klientů na síťové rozhraní RSS nad rámec 4 neprojeví v náhodných vstupně-výstupních operacích, neplatí to pro sekvenční vstupně-výstupní operace. Jak ukazuje následující graf, každé zvýšení je přidruženo k odpovídajícímu nárůstu propustnosti čtení. Propustnost zápisu trvalá v důsledku omezení šířky pásma sítě, které Azure používá pro jednotlivé typy a velikosti instancí. 

![Sekvenční vstupně-výstupní testy](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure umisťuje omezení přenosové rychlosti sítě na jednotlivé typy a velikosti virtuálních počítačů. Limit přenosové rychlosti se ukládá jenom na odchozí provoz. Počet síťových adaptérů přítomných na virtuálním počítači nemá žádný vliv na celkovou šířku pásma dostupnou pro daný počítač.  Například typ instance D16 má zavedený limit sítě 8000 MB/s (1 000 MiB/s).  Jak vidíte sekvenční graf výše, má tento limit vliv na odchozí přenos (zápisy), ale ne vícekanálový čtení.

![Porovnání sekvenčního vstupu a výstupu](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="is-advanced-networking-recommended"></a>Doporučuje se Pokročilá síť?

Pro maximální výkon se doporučuje nakonfigurovat pokročilé sítě tam, kde je to možné. Mějte na paměti následující skutečnosti:  

* Azure Portal pro virtuální počítače, které podporují tuto funkci, umožňuje rozšířené sítě ve výchozím nastavení.  Jiné metody nasazení, jako například Ansible a podobné konfigurační nástroje, ale nemusí.  Nepovedlo se povolit pokročilé sítě, které by mohly Hobble výkon počítače.  
* Pokud v síťovém rozhraní virtuálního počítače není povolená rozšířená síť kvůli nedostatku podpory pro typ nebo velikost instance, zůstane tato možnost zakázaná s většími typy instancí. V těchto případech budete potřebovat ruční zásah.

## <a name="are-jumbo-frames-supported"></a>Podporují se rámce typu Jumbo?

Virtuální počítače Azure nepodporují rámce typu Jumbo.

## <a name="is-smb-signing-supported"></a>Je podepisování SMB podporováno? 

Protokol SMB poskytuje základ pro sdílení souborů a tiskáren a další síťové operace, jako je vzdálená správa systému Windows. Aby nedocházelo k útokům prostředníkem, které mění pakety SMB při přenosu, podporuje protokol SMB digitální podepisování paketů SMB. 

Podepisování SMB se podporuje pro všechny verze protokolu SMB, které podporuje Azure NetApp Files. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Jaký je dopad podepisování SMB na výkon?  

Podepisování SMB má škodlivý účinek na výkon protokolu SMB. Kromě jiných potenciálních příčin snížení výkonu digitální podepisování každého paketu spotřebovává další procesor na straně klienta, jak ukazuje následující výstup Perfmon. V tomto případě se zobrazí základní hodnota 0 zodpovědná za SMB, včetně podepisování SMB.  Porovnání s čísly propustnosti nevícekanálových sekvenčních čtení v předchozí části ukazuje, že podepisování SMB snižuje celkovou propustnost z 875MiB/s na přibližně 250MiB/s. 

![Dopad na výkon podepisování SMB](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>Další kroky  

- [Nejčastější dotazy týkající se Azure NetApp Files](azure-netapp-files-faqs.md)
- Přečtěte si téma [Azure NetApp Files: spravované podnikové sdílení souborů pro úlohy SMB](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) týkající se použití sdílených složek smb s Azure NetApp Files.