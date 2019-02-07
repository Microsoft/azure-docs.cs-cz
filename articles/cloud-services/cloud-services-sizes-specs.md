---
title: Velikosti virtuálních počítačů pro Azure Cloud services | Dokumentace Microsoftu
description: Obsahuje seznam různých velikostí virtuálních počítačů (a ID) pro Azure cloud service webových a pracovních rolí.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: jpconnock
editor: ''
ms.assetid: 1127c23e-106a-47c1-a2e9-40e6dda640f6
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 07/18/2017
ms.author: jeconnoc
ms.openlocfilehash: a2af7ffde336016ae89a1ab03d753ed11b901e05
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55818698"
---
# <a name="sizes-for-cloud-services"></a>Velikosti pro Cloud Services
Toto téma popisuje dostupné velikosti a možnosti pro instance rolí cloudové služby (webové role a role pracovního procesu). Také poskytuje důležité informace o nasazení je potřeba vědět při plánování použití těchto prostředků. Jednotlivé velikosti má ID, kam si ukládáte váš [definiční soubor služby](cloud-services-model-and-package.md#csdef). Ceny platné pro jednotlivé velikosti jsou k dispozici na [ceny Cloud Services](https://azure.microsoft.com/pricing/details/cloud-services/) stránky.

> [!NOTE]
> Související omezení v Azure najdete v tématu [předplatné Azure a limity, kvóty a omezení](../azure-subscription-service-limits.md)
>
>

## <a name="sizes-for-web-and-worker-role-instances"></a>Velikosti instancí webových a pracovních rolí
Azure nabízí na výběr několik standardních velikostí. Tady je několik aspektů, které je třeba při volbě velikosti zvážit:

* Virtuální počítače D-series slouží ke spouštění aplikací, které potřebují vyšší výpočetní výkon a krátkodobý vysoký diskový výkon. Virtuální počítače D-series mají rychlejší procesory, vyšší poměr paměti na jádro a jednotky SSD pro dočasný disk. Podrobnosti najdete v oznámení na blogu Azure: [Nové velikosti virtuálních počítačů D-Series](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).
* Dv3-series, řada Dv2-series, nástupce původní D-series, nabízí výkonnější procesor. Procesor v Dv2-series je asi o 35 % rychlejší než procesor v D-series. Využívá nejnovější generaci procesorů Intel Xeon® E5-2673 v3 (Haswell) s frekvencí 2,4 GHz, kterou může technologie Intel Turbo Boost 2.0 zvýšit až na 3,1 GHz. Řada Dv2-series má stejnou konfiguraci paměti a disku jako řada D.
* Virtuální počítače G-series nabízejí nejvíce paměti a spouštějí se na hostitelích s procesory Intel Xeon E5 V3.
* Virtuální počítače řady A-series je možné nasadit na různé typy hardwaru a procesorů. Velikost je omezována, v závislosti na hardwaru a nabízí konzistentní výkon procesoru pro spuštěné instance bez ohledu na hardware, který je nasazen na. Pokud chcete zjistit fyzický hardware, na kterém je virtuální počítač dané velikosti nasazený, zadejte dotaz na virtuální hardware přímo z virtuálního počítače.
* Velikost A0 využívá fyzický hardware na maximum. Pouze pro tuto konkrétní velikost můžou výkon vašich spuštěných úloh ovlivnit jiná zákaznická nasazení. Relativní výkon je popsaný níže jako očekávaný základ, přičemž se dá očekávat variabilita přibližně 15 procent.

Velikost virtuálního počítače má vliv na cenu. Velikost také určuje kapacitu zpracování, paměti a úložiště virtuálního počítače. Náklady na úložiště se počítají samostatně na základě využitých stránek v účtu úložiště. Podrobnosti najdete v tématu [podrobnosti o cenách služby Cloud Services](https://azure.microsoft.com/pricing/details/cloud-services/) a [ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Následující aspekty mohou pomoci při rozhodování o velikosti:

* Velikosti A8-A11 a H-series se také označují jako *náročné na výpočetní výkon*. Hardware pro hostování těchto velikostí je navržený a optimalizovaný pro úlohy náročné na výpočty a síťový provoz, včetně clusterovaného vysokovýkonného výpočetního prostředí (HPC), například pro modelování a simulace. Řady A8-A11-series používají Intel Xeon E5-2670 @ 2,6 GHz a H-series používá Intel Xeon E5-2667 v3 @ 3,2 GHz. Podrobné informace a požadavky týkající se použití těchto velikostí najdete v tématu [vysoce výkonné výpočetní velikosti virtuálních počítačů](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Dv3-series, řada Dv2-series, D-series, G-series jsou ideální pro aplikace, které vyžadují rychlejší CPU, lepší výkon místních disků nebo mají vysoké nároky na paměť. Nabízejí výkonnou kombinaci pro mnoho podnikových aplikací.
* Někteří z fyzických hostitelů v datových centrech Azure nemusí podporovat větší velikosti virtuálních počítačů, například A5–A11. V důsledku toho může zobrazit chybová zpráva **nepovedlo se nakonfigurovat virtuální počítač {název počítače}** nebo **nepovedlo se vytvořit virtuální počítač {název počítače}** při změně velikosti stávajícího virtuálního počítače na novou velikost; Vytvoření nového virtuálního počítače ve virtuální síti vytvořené před 16. dubna 2013; nebo přidávání nového virtuálního počítače do existující cloudové služby. Zobrazit [Chyba: "Nepovedlo se nakonfigurovat virtuální počítač"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) na fóru podpory pro řešení pro všechny scénáře nasazení.
* Vaše předplatné může také omezovat počet jader, které můžete v určitých rodinách velikostí nasadit. S požadavkem na zvýšení této kvóty se obraťte na podporu Azure.

## <a name="performance-considerations"></a>Otázky výkonu
Jsme vytvořili koncept výpočetní jednotky Azure (ACU) poskytnout způsob porovnání výkonu (CPU) jednotlivých SKU v Azure a tím určíte, které skladová jednotka je nejpravděpodobněji odpovídají výkon potřebuje.  Jednotka ACU je aktuálně stanovená tak, že malý virtuální počítač (Standard_A1) má 100 ACU a ostatní jednotky SKU jsou pak ohodnoceny podle relativního výsledku standardního srovnávacího testu.

> [!IMPORTANT]
> ACU je jenom informativní údaj. Výsledek v případě konkrétních úloh může být odlišný.
>
>

<br>

| Rodina SKU | ACU na jádro |
| --- | --- |
| [ExtraSmall](#a-series) |50 |
| [ExtraLarge (krátkodobé používání)](#a-series) |100 |
| [A5-7](#a-series) |100 |
| [A8-A11](#a-series) |225* |
| [A v2](#av2-series) |100 |
| [D](#d-series) |160 |
| [D v2](#dv2-series) |160 - 190* |
| [D v3](#dv3-series) |160 - 190* |
| [E v3](#ev3-series) |160 - 190* |
| [G](#g-series) |180 - 240* |
| [H](#h-series) |290 - 300* |

Hodnoty ACU s hvězdičkou označují použití technologie Intel® Turbo, která může zvýšit frekvenci procesoru podle aktuální potřeby. Množství nárůst se může lišit v závislosti na velikosti virtuálního počítače, úlohy a dalších úlohách spuštěných na stejném hostiteli.

## <a name="size-tables"></a>Tabulky velikostí
Následující tabulky pro jednotlivé velikosti virtuálních počítačů uvádějí poskytované kapacity.

* Kapacita úložiště je v jednotkách GiB, tj. 1024^3 bajtů. Při porovnávání diskové kapacity měřené v GB (1000^3 B) s kapacitou měřenou v GiB (1024^3) pamatujte, že údaj v GiB je číselně menší. Například 1023 GiB = 1098,4 GB
* Propustnost disku se měří v počtu V/V operací za sekundu (IOPS) a v MB/s, kde 1 MB/s = 10^6 bajtů/s.
* Disky pro ukládání dat můžou fungovat v režimu s mezipamětí, nebo bez ní. Pro diskové operace s mezipamětí je možné nastavit mezipaměť na hostiteli **jen na čtení** nebo **na čtení i zápis**. Pro diskové operace bez mezipaměti je mezipaměť na hostiteli nastavená na **žádná**.
* Maximální šířka pásma sítě je maximální přenosová kapacita vyhrazená a přidělená danému typu virtuálního počítače. Maximální šířka pásma slouží pro orientaci při výběru správného typu virtuálního počítače, aby byla zajištěna dostatečná síťová kapacita. Při přechodu mezi nízkou, střední, vysokou a velmi vysokou propustnost zvýší odpovídajícím způsobem. Skutečný výkon sítě bude záviset na mnoha faktorech včetně zatížení aplikací a sítě a síťového nastavení aplikace.

## <a name="a-series"></a>A-Series
| Velikost            | Procesorová jádra | Paměť: GiB  | Dočasné úložiště: GiB       | Max. počet NIC / Šířka pásma sítě |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| ExtraSmall      | 1         | 0,768        | 20                   | 1 / malá |
| Krátkodobé používání           | 1         | 1,75         | 225                  | 1 / střední |
| Střednědobé používání          | 2         | 3,5          | 490                  | 1 / střední |
| Dlouhodobé používání           | 4         | 7            | 1000                 | 2 / vysoká |
| ExtraLarge      | 8         | 14           | 2040                 | 4 / vysoká |
| A5              | 2         | 14           | 490                  | 1 / střední |
| A6              | 4         | 28           | 1000                 | 2 / vysoká |
| A7              | 8         | 56           | 2040                 | 4 / vysoká |

## <a name="a-series---compute-intensive-instances"></a>A-series – Instance náročné na výpočetní výkon
Informace a požadavky týkající se použití těchto velikostí najdete v tématu [vysoce výkonné výpočetní velikosti virtuálních počítačů](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

| Velikost            | Procesorová jádra | Paměť: GiB  | Dočasné úložiště: GiB       | Max. počet NIC / Šířka pásma sítě |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| A8*             |8          | 56           | 1817                 | 2 / vysoká |
| A9*             |16         | 112          | 1817                 | 4 / velmi vysoká |
| A10             |8          | 56           | 1817                 | 2 / vysoká |
| A11             |16         | 112          | 1817                 | 4 / velmi vysoká |

\*Podpora RDMA

## <a name="av2-series"></a>Av2-series

| Velikost            | Procesorová jádra | Paměť: GiB  | Dočasné úložiště (SSD): GiB       | Max. počet NIC / Šířka pásma sítě |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_A1_v2  | 1         | 2            | 10                   | 1 / střední                 |
| Standard_A2_v2  | 2         | 4            | 20                   | 2 / střední                 |
| Standard_A4_v2  | 4         | 8            | 40                   | 4 / vysoká                     |
| Standard_A8_v2  | 8         | 16           | 80                   | 8 / vysoká                     |
| Standard_A2m_v2 | 2         | 16           | 20                   | 2 / střední                 |
| Standard_A4m_v2 | 4         | 32           | 40                   | 4 / vysoká                     |
| Standard_A8m_v2 | 8         | 64           | 80                   | 8 / vysoká                     |


## <a name="d-series"></a>D-series
| Velikost            | Procesorová jádra | Paměť: GiB  | Dočasné úložiště (SSD): GiB       | Max. počet NIC / Šířka pásma sítě |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1     | 1         | 3,5          | 50                   | 1 / střední |
| Standard_D2     | 2         | 7            | 100                  | 2 / vysoká |
| Standard_D3     | 4         | 14           | 200                  | 4 / vysoká |
| Standard_D4     | 8         | 28           | 400                  | 8 / vysoká |
| Standard_D11    | 2         | 14           | 100                  | 2 / vysoká |
| Standard_D12    | 4         | 28           | 200                  | 4 / vysoká |
| Standard_D13    | 8         | 56           | 400                  | 8 / vysoká |
| Standard_D14    | 16        | 112          | 800                  | 8 / velmi vysoká |

## <a name="dv2-series"></a>Dv2-series
| Velikost            | Procesorová jádra | Paměť: GiB  | Dočasné úložiště (SSD): GiB       | Max. počet NIC / Šířka pásma sítě |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1_v2  | 1         | 3,5          | 50                   | 1 / střední |
| Standard_D2_v2  | 2         | 7            | 100                  | 2 / vysoká |
| Standard_D3_v2  | 4         | 14           | 200                  | 4 / vysoká |
| Standard_D4_v2  | 8         | 28           | 400                  | 8 / vysoká |
| Standard_D5_v2  | 16        | 56           | 800                  | 8 / velmi vysoká |
| Standard_D11_v2 | 2         | 14           | 100                  | 2 / vysoká |
| Standard_D12_v2 | 4         | 28           | 200                  | 4 / vysoká |
| Standard_D13_v2 | 8         | 56           | 400                  | 8 / vysoká |
| Standard_D14_v2 | 16        | 112          | 800                  | 8 / velmi vysoká |
| Standard_D15_v2 | 20        | 140          | 1 000                | 8 / velmi vysoká |

## <a name="dv3-series"></a>Dv3-series

| Velikost            | Procesorová jádra | Paměť: GiB   | Dočasné úložiště (SSD): GiB       | Max. počet NIC / Šířka pásma sítě |
|---------------- | --------- | ------------- | -------------------- | ---------------------------- |
| Standard_D2_v3  | 2         | 8             | 50                   | 2 / střední |
| Standard_D4_v3  | 4         | 16            | 100                  | 2 / vysoká |
| Standard_D8_v3  | 8         | 32            | 200                  | 4 / vysoká |
| Standard_D16_v3 | 16        | 64            | 400                  | 8 / velmi vysoká |
| Standard_D32_v3 | 32        | 128           | 800                  | 8 / velmi vysoká |
| Standard_D64_v3 | 64        | 256           | 1600                 | 8 / velmi vysoká |

## <a name="ev3-series"></a>Ev3-series

| Velikost            | Procesorová jádra | Paměť: GiB   | Dočasné úložiště (SSD): GiB       | Max. počet NIC / Šířka pásma sítě |
|---------------- | --------- | ------------- | -------------------- | ---------------------------- |
| Standard_E2_v3  | 2         | 16            | 50                   | 2 / střední |
| Standard_E4_v3  | 4         | 32            | 100                  | 2 / vysoká |
| Standard_E8_v3  | 8         | 64            | 200                  | 4 / vysoká |
| Standard_E16_v3 | 16        | 128           | 400                  | 8 / velmi vysoká |
| Standard_E32_v3 | 32        | 256           | 800                  | 8 / velmi vysoká |
| Standard_E64_v3 | 64        | 432           | 1600                 | 8 / velmi vysoká |


## <a name="g-series"></a>G-series
| Velikost            | Procesorová jádra | Paměť: GiB  | Dočasné úložiště (SSD): GiB       | Max. počet NIC / Šířka pásma sítě |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_G1     | 2         | 28           | 384                  |1 / vysoká |
| Standard_G2     | 4         | 56           | 768                  |2 / vysoká |
| Standard_G3     | 8         | 112          | 1 536                |4 / velmi vysoká |
| Standard_G4     | 16        | 224          | 3 072                |8 / velmi vysoká |
| Standard_G5     | 32        | 448          | 6 144                |8 / velmi vysoká |

## <a name="h-series"></a>H-series
Virtuální počítače Azure H-series jsou další generací počítačů s vysokým výpočetním výkonem a jsou zaměřené na výpočetně vysoce náročné úlohy, jako je např. molekulární modelování, výpočty dynamiky tekutin a podobně. Tyto 8 a 16 jader virtuální počítače jsou postavené na technologii procesorů Intel Haswell E5-2667 V3 paměti DDR4 a místní úložiště založené na jednotkách SSD.

Řada H-series nabízí kromě značného výkonu procesorů i řadu možností přímého přístupu do paměti vzdáleného počítače (RDMA) s nízkou latencí díky technologii FDR InfiniBand, a množství konfigurací paměti pro podporu požadavků na vysoce paměťově náročné výpočetní operace.

| Velikost            | Procesorová jádra | Paměť: GiB  | Dočasné úložiště (SSD): GiB       | Max. počet NIC / Šířka pásma sítě |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_H8     | 8         | 56           | 1000                 | 8 / vysoká |
| Standard_H16    | 16        | 112          | 2000                 | 8 / velmi vysoká |
| Standard_H8m    | 8         | 112          | 1000                 | 8 / vysoká |
| Standard_H16m   | 16        | 224          | 2000                 | 8 / velmi vysoká |
| Standard_H16r*  | 16        | 112          | 2000                 | 8 / velmi vysoká |
| Standard_H16mr* | 16        | 224          | 2000                 | 8 / velmi vysoká |

\*Podpora RDMA

## <a name="configure-sizes-for-cloud-services"></a>Konfigurace velikosti pro Cloud Services
Velikost instance role virtuálního počítače můžete zadat jako součást modelu služby popsaného [definiční soubor služby](cloud-services-model-and-package.md#csdef). Velikost role určuje počet jader procesoru, kapacita paměti a velikost místního systému souborů, který je přidělen spuštěné instance. Zvolte velikost role, které jsou založené na požadavku prostředek vaší aplikace.

Tady je příklad pro nastavení velikosti role bude Standard_D2 instance webové Role:

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2">
...
</WorkerRole>
```

## <a name="changing-the-size-of-an-existing-role"></a>Změna velikosti existující roli

Jako povaze změny úloh nebo nové velikosti virtuálních počítačů, které jsou k dispozici můžete změnit velikost role. Uděláte to tak, změna velikosti virtuálního počítače v souboru definice služby (jak je uvedeno výše), nové zabalení cloudové služby a jeho nasazení. Není možné změnit velikost virtuálního počítače přímo z portálu nebo Powershellu.

>[!TIP]
> Můžete chtít použít různé velikosti virtuálních počítačů pro vaši roli v různých prostředích (např.) produkční vs test). Můžete provést například jde vytvořit více definice služby (.csdef) soubory v projektu, pak vytvořte jinou cloudovou službu balíčky pro každé prostředí během vašeho automatizované sestavení pomocí nástroje CSPack. Další informace o prvcích balíček cloudové služby a jak je lze vytvořit, naleznete v tématu [modelu co je cloud services a jak ho balíček?](cloud-services-model-and-package.md)
>
>

## <a name="get-a-list-of-sizes"></a>Získat seznam velikostí
Chcete-li získat seznam velikostí můžete použít PowerShell nebo rozhraní REST API. Rozhraní REST API je zdokumentován [tady](https://msdn.microsoft.com/library/azure/dn469422.aspx). Následující kód je příkaz prostředí PowerShell, který zobrazí seznam všech dostupných velikostí pro cloudové služby. 

```powershell
Get-AzureRoleSize | where SupportedByWebWorkerRoles -eq $true | select InstanceSize, RoleSizeLabel
```

## <a name="next-steps"></a>Další postup
* Další informace o [Limitech, kvótách a omezeních předplatného a služeb Azure](../azure-subscription-service-limits.md)
* Další informace [o vysoce výkonné výpočetní velikosti virtuálních počítačů](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pro úlohy v prostředí HPC.
