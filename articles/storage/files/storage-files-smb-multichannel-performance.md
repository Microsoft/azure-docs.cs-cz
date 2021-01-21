---
title: Vícekanálový výkon SMB – soubory Azure
description: Přečtěte si o výkonu vícekanálového protokolu SMB.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: cbded0a9c905bb488e1bae0f92d777e2e7ed7441
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630849"
---
# <a name="smb-multichannel-performance"></a>Výkon funkce SMB Multichannel

Azure Files SMB vícekanálový (Preview) umožňuje klientovi SMB 3. x navazovat více síťových připojení ke sdíleným složkám Premium v účtu úložiště souborů. Protokol SMB 3,0 zavedl v klientech Windows Server 2012 a Windows 8 vícekanálový funkce protokolu SMB. Z tohoto důvodu může libovolný klient Azure File SMB 3. x, který podporuje službu SMB vícekanálový, využít funkci pro sdílené složky Azure Premium. Pro povolení vícekanálového protokolu SMB v účtu úložiště se neúčtují žádné další náklady.

## <a name="benefits"></a>Výhody

Vícekanálový protokol SMB souborů Azure umožňuje klientům používat víc síťových připojení, která poskytují vyšší výkon při snižování nákladů na vlastnictví. Vyšší výkon se dosahuje prostřednictvím agregace šířky pásma přes více síťových adaptérů a využitím podpory škálování na straně příjmu (RSS) pro síťové adaptéry k distribuci vstupně-výstupních operací napříč více procesory.

- **Vyšší propustnost**: více připojení umožňuje paralelní přenos dat více než několika cest, což výrazně přináší úlohy, které využívají větší velikost souborů s větším počtem vstupně-výstupních operací, a vyžadují vysokou propustnost z jednoho virtuálního počítače nebo menší sady virtuálních počítačů. Některé z těchto úloh zahrnují média a zábavu pro vytváření obsahu nebo překódování, genomiku a analýzu rizik finančních služeb.
- **Vyšší IOPS**: schopnost síťové karty RSS umožňuje efektivní distribuci zatížení napříč více procesory s více připojeními. To pomáhá dosáhnout vyšší škály vstupně-výstupních operací a efektivního využití procesorů virtuálních počítačů. To je užitečné pro úlohy, které mají malé vstupně-výstupní operace, například databázové aplikace.
- Odolnost **proti chybám sítě**: více připojení zmírnit riziko výpadku, protože klienti už nespoléhají na jednotlivá připojení.
- **Automatická konfigurace**: když je na klientech a účtech úložiště povolená možnost SMB vícekanálový, umožňuje dynamické zjišťování existujících připojení a v případě potřeby může vytvořit další cesty připojení.
- **Optimalizace nákladů**: úlohy můžou dosáhnout vyšší škály z jednoho virtuálního počítače nebo malé sady virtuálních počítačů při připojení ke sdíleným složkám Premium. To může snížit celkové náklady na vlastnictví tím, že se sníží počet virtuálních počítačů potřebných ke spuštění a správě úlohy.

Další informace o vícekanálovém protokolu SMB najdete v [dokumentaci k Windows](/azure-stack/hci/manage/manage-smb-multichannel).

Tato funkce poskytuje vyšší výkon pro vícevláknové aplikace, ale obvykle neumožňuje aplikacím v jednom vlákně. Další podrobnosti najdete v části [porovnání výkonu](#performance-comparison) .

## <a name="limitations"></a>Omezení

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Regionální dostupnost

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="configuration"></a>Konfigurace

Možnost protokolu SMB vícekanálový funguje jenom v případě, že je funkce povolená na straně klienta (klienta) i na straně služby (účet Azure Storage).

V klientech Windows je ve výchozím nastavení povolená možnost SMB vícekanálový. Konfiguraci můžete ověřit spuštěním následujícího příkazu PowerShellu: 

`Get-smbClientConfiguration | select EnableMultichannel`.
 
V účtu úložiště Azure budete muset povolit vícekanálový protokol SMB. Viz [Povolení vícekanálového protokolu SMB (Preview)](storage-files-enable-smb-multichannel.md).

### <a name="disable-smb-multichannel"></a>Zakázat vícekanálový protokol SMB
Ve většině scénářů, zejména u vícevláknových úloh, by klienti měli vidět Vylepšený výkon pomocí protokolu SMB vícekanálový. U některých konkrétních scénářů, jako jsou například úlohy s jedním vláknem nebo pro účely testování, můžete chtít zakázat funkci SMB vícekanálový. Další podrobnosti najdete v tématu [porovnání výkonu](#performance-comparison) .

## <a name="verify-smb-multichannel-is-configured-correctly"></a>Ověření, jestli je protokol SMB vícekanálový správně nakonfigurovaný

1. Vytvořte soubor prémiové sdílené složky nebo použijte existující.
1. Ujistěte se, že váš klient podporuje funkci SMB vícekanálový (jeden nebo víc síťových adaptérů má povolené škálování na straně příjmu). Další podrobnosti najdete v [dokumentaci k Windows](/azure-stack/hci/manage/manage-smb-multichannel) .
1. Připojte sdílení souborů ke klientovi.
1. Vygenerujte zatížení vaší aplikace.
    Nástroj pro kopírování, jako je například Robocopy/MT nebo jakýkoli nástroj pro výkon, jako je například DiskSpd, se může vygenerovat zatížení.
1. Otevřete PowerShell jako správce a použijte tento příkaz: `Get-SmbMultichannelConnection |fl`
1. Vyhledat vlastnosti **MaxChannels** a **CurrentChannels**

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG" alt-text="Snímek obrazovky s výsledky Get-SMBMultichannelConnection." lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG":::

## <a name="performance-comparison"></a>Porovnání výkonu

Existují dvě kategorie vzorů úlohy čtení a zápisu – jeden podproces a vícevláknové. Většina úloh používá více souborů, ale může se jednat o konkrétní případy použití, kdy zatížení funguje s jedním souborem ve sdílené složce. Tato část se zabývá různými případy použití a dopadem na výkon každého z nich. Obecně platí, že většina úloh pracuje s více vlákny a distribuuje úlohy do více souborů, takže by měly sledovat významná vylepšení výkonu pomocí protokolu SMB vícekanálový.

- **Vícevláknové/více souborů**: v závislosti na vzoru zatížení byste měli vidět výrazné zlepšení výkonu při čtení a zápisu iOS přes více kanálů. Zisky z hlediska výkonu se liší od dvou po 4x po dobu IOPS, propustnosti a latence. Pro tuto kategorii by měla být pro nejlepší výkon povolená možnost SMB vícekanálový.
- **Vícevláknový/jeden soubor**: pro většinu případů použití v této kategorii budou mít úlohy možnost mít vícekanálový protokol SMB povolený, zejména v případě, že má úloha průměrnou vstupně-výstupní velikost > ~ 16 KB. Několik ukázkových scénářů, které využívají funkci SMB vícekanálový, je zálohování nebo obnovení jednoho velkého souboru. Výjimkou je, že chcete zakázat vícekanálový protokol SMB, pokud je vaše úloha malým systémem IOs velmi silná. V takovém případě můžete sledovat mírnou ztrátu výkonu z 10%. V závislosti na případu použití zvažte rozprostření zatížení mezi více soubory nebo funkci zakažte. Podrobnosti najdete v části věnované [konfiguraci](#configuration) .
- **Jeden nebo více souborů s jedním vláknem**: pro většinu úloh s jedním vláknem jsou z důvodu nedostatku paralelismu k dispozici minimální výkonnostní výhody, obvykle dochází k mírnému snížení výkonu na ~ 10%, pokud je povolená vícekanálový protokol SMB. V takovém případě je ideálním řešením pro použití protokolu SMB vícekanálový s jednou výjimkou. Pokud zatížení s jedním vláknem může distribuovat zatížení mezi více soubory a používá průměrnou velikost vstupně-výstupních operací (> ~ 16 MB/s), měli byste mít mírné výhody související s výkonem ze vícekanálového protokolu SMB.

### <a name="performance-test-configuration"></a>Konfigurace testu výkonnosti

V grafech v tomto článku se použila následující konfigurace: jediný virtuální počítač se systémem D32s v3 s jedním síťovým ADAPTÉRem podporujícím technologii RSS se čtyřmi kanály. Načítání bylo vygenerováno pomocí diskspd.exe, více vláken s hloubkou vstupně-výstupních operací 10 a náhodným IOs s různými velikostmi v/v.

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost úložiště v mezipaměti a dočasné úložiště: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku neuloženého v mezipaměti: IOPS/MB/s | Maximální počet síťových karet|Očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|---|---|
| [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8|16000 |

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG" alt-text="Snímek obrazovky, který zobrazuje konfiguraci testu výkonu." lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG":::

### <a name="mutli-threadedmultiple-files-with-smb-multichannel"></a>Multi – vícevláknové/více souborů s využitím protokolu SMB vícekanálový

Načítání bylo vygenerováno pro 10 souborů s různými velikostmi v/v. Výsledky testů pro horizontální navýšení kapacity ukázaly výrazné vylepšení v důsledku výsledků testů vstupně-výstupních operací i propustnosti s povolenou funkcí SMB Výsledky jsou znázorněné v následujících diagramech:

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png" alt-text="Diagram výkonu" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png" alt-text="Diagram výkonu propustnosti." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png":::

- Na jednom síťovém rozhraní se pro čtení zjistilo zvýšení výkonu 2x-3x a pro zápisy se zvyšuje počet 3x-4x z hlediska vstupně-výstupních operací a propustnosti.
- Počet vstupně-výstupních operací s protokolem SMB a propustnost pro dosažení limitů virtuálních počítačů i s jedním síťovým ADAPTÉRem a čtyřmi omezeními kanálu
- Vzhledem k tomu, že výstup (nebo čtení do úložiště) není měřený, propustnost čtení překročila limit publikovaných virtuálních počítačů 16 000 MB/s (2 GiB/s). Test dosáhl >2,7 GiB/s. Příchozí (nebo zápis do úložiště) i nadále podléhají omezením virtuálních počítačů.
- Rozprostře zatížení mezi více soubory, které jsou povoleny pro podstatná vylepšení.

Vzorový příkaz, který se použil při tomto testování, je: 

`diskspd.exe -W300 -C5 -r -w100 -b4k -t8 -o8 -Sh -d60 -L -c2G -Z1G z:\write0.dat z:\write1.dat z:\write2.dat z:\write3.dat z:\write4.dat z:\write5.dat z:\write6.dat z:\write7.dat z:\write8.dat z:\write9.dat `.

### <a name="multi-threadedsingle-file-workloads-with-smb-multichannel"></a>Úlohy s více vlákny nebo s jedním souborem s protokolem SMB vícekanálový

Zatížení bylo vygenerováno pro jeden GiB soubor s 128. V případě, že je povolená funkce SMB vícekanálový, se ve většině případů ve většině případů standardně provedl test škálování s více vlákny nebo s jedním. Výsledky jsou znázorněné v následujících diagramech:

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png" alt-text="Diagram výkonu IOPS" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png" alt-text="Diagram výkonu propustnosti jednoho souboru." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png":::

- V jednom síťovém adaptéru s větším průměrem vstupně-výstupních operací (> ~ 16 KB) byly významné vylepšení čtení i zápisu.
- U menších velikostí v/v byl při povolení funkce SMB vícekanálový malý vliv na 10% na výkon. To může být zmírnit rozšiřováním zatížení více souborů nebo zakázáním této funkce.
- Výkon se pořád váže  [omezeními na jeden soubor](storage-files-scale-targets.md#file-level-limits).

## <a name="optimizing-performance"></a>Optimalizace výkonu

Následující tipy vám mohou přispět k optimalizaci výkonu:

- Ujistěte se, že váš účet úložiště a váš klient jsou společně umístěné ve stejné oblasti Azure, aby se snížila latence sítě.
- Používejte vícevláknové aplikace a rozprostření zatížení mezi více souborů.
- Výhody z hlediska výkonu vícekanálového nárůstu protokolu SMB s počtem souborů distribuují zatížení.
- Výkon sdílené složky Premium je vázaný na velikost zřízené sdílené složky (IOPS/odchozí/příchozí/příchozí) a omezení na jeden soubor. Podrobnosti najdete v tématu [Principy zřizování pro sdílené složky prémiových souborů](understanding-billing.md#provisioned-model).
- Maximální výkon jednoho klienta virtuálního počítače je stále vázaný na omezení virtuálních počítačů. [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) například může podporovat maximální šířku pásma 16 000 MB/s (nebo peering –), odchozí z virtuálního počítače (zápisy do úložiště) se měří, příchozí přenos dat (čtení z úložiště) není. Výkon sdílené složky závisí na omezeních sítě počítačů, procesorech, interních dostupných úložištích šířky pásma sítě, velikostech v/v, paralelismuách a dalších faktorech.
- Počáteční test je obvykle zahříváním, zrušením jeho výsledků a opakováním testu.
- Pokud je výkon omezen jedním klientem a zatížení je stále pod zřízenými omezeními sdílení, je možné dosáhnout vyššího výkonu rozšířením zatížení více klientů.

### <a name="the-relationship-between-iops-throughput-and-io-sizes"></a>Vztah mezi vstupně-výstupními operacemi za sekundu, propustností a v/v

**Propustnost = velikost vstupně-výstupních operací * IOPS**

Vyšší počet vstupně-výstupních operací jednotky zvyšují propustnost a budou mít vyšší latenci, což vede k menšímu počtu čistých IOPS. Menší vstupně-výstupní operace budou mít vyšší IOPS, ale budou mít za následek nižší propustnost a latence.

## <a name="next-steps"></a>Další kroky

- [Povolení funkce SMB vícekanálový na účtu úložiště účtů (Preview)](storage-files-enable-smb-multichannel.md)
- Další informace o vícekanálovém protokolu SMB najdete v [dokumentaci k Windows](/azure-stack/hci/manage/manage-smb-multichannel) .
