---
title: Velikosti virtuálních počítačů pro Azure Cloud Services | Microsoft Docs
description: Zobrazuje seznam různých velikostí virtuálních počítačů (a ID) pro webové a pracovní role Azure Cloud Service.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: 2549cb0408c9dad3e92f2cec9625757de45a10dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82086245"
---
# <a name="sizes-for-cloud-services"></a>Velikosti pro Cloud Services
Toto téma popisuje dostupné velikosti a možnosti pro instance rolí cloudové služby (webové role a role pracovního procesu). Poskytuje taky požadavky na nasazení, které vám pomají vědět, kdy plánujete tyto prostředky používat. Každá velikost má ID, které jste umístili do [souboru definice služby](cloud-services-model-and-package.md#csdef). Ceny za jednotlivé velikosti jsou k dispozici na stránce [Cloud Services ceny](https://azure.microsoft.com/pricing/details/cloud-services/) .

> [!NOTE]
> Pokud chcete zobrazit související omezení Azure, přečtěte si téma [limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) .
>
>

## <a name="sizes-for-web-and-worker-role-instances"></a>Velikosti instancí webových rolí a rolí pracovních procesů
Azure nabízí na výběr několik standardních velikostí. Tady je několik aspektů, které je třeba při volbě velikosti zvážit:

* Virtuální počítače D-series slouží ke spouštění aplikací, které potřebují vyšší výpočetní výkon a krátkodobý vysoký diskový výkon. Virtuální počítače D-series mají rychlejší procesory, vyšší poměr paměti na jádro a jednotky SSD pro dočasný disk. Podrobnosti najdete v oznámení na blogu Azure: [Nové velikosti virtuálních počítačů D-Series](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).
* Dv3-Series, Dv2-Series, následné až do původní řady D-Series, nabízí výkonnější procesor. Procesor v Dv2-series je asi o 35 % rychlejší než procesor v D-series. Využívá nejnovější generaci procesorů Intel Xeon® E5-2673 v3 (Haswell) s frekvencí 2,4 GHz, kterou může technologie Intel Turbo Boost 2.0 zvýšit až na 3,1 GHz. Řada Dv2-series má stejnou konfiguraci paměti a disku jako řada D.
* Virtuální počítače G-series nabízejí nejvíce paměti a spouštějí se na hostitelích s procesory Intel Xeon E5 V3.
* Virtuální počítače řady A-Series je možné nasadit na různé typy hardwaru a procesory. Velikost se omezuje na základě hardwaru a nabízí konzistentní výkon procesoru pro spuštěnou instanci bez ohledu na hardware, na kterém je nasazený. Pokud chcete zjistit fyzický hardware, na kterém je virtuální počítač dané velikosti nasazený, zadejte dotaz na virtuální hardware přímo z virtuálního počítače.
* Velikost A0 využívá fyzický hardware na maximum. Pouze pro tuto konkrétní velikost můžou výkon vašich spuštěných úloh ovlivnit jiná zákaznická nasazení. Relativní výkon je popsaný níže jako očekávaný základ, přičemž se dá očekávat variabilita přibližně 15 procent.

Velikost virtuálního počítače má vliv na cenu. Velikost také určuje kapacitu zpracování, paměti a úložiště virtuálního počítače. Náklady na úložiště se počítají samostatně na základě využitých stránek v účtu úložiště. Podrobnosti najdete v [podrobnostech o cenách Cloud Services](https://azure.microsoft.com/pricing/details/cloud-services/) a [Azure Storage cenách](https://azure.microsoft.com/pricing/details/storage/).

Následující aspekty mohou pomoci při rozhodování o velikosti:

* Velikosti A8-A11 a H-series se také označují jako *náročné na výpočetní výkon*. Hardware pro hostování těchto velikostí je navržený a optimalizovaný pro úlohy náročné na výpočty a síťový provoz, včetně clusterovaného vysokovýkonného výpočetního prostředí (HPC), například pro modelování a simulace. Řady A8-A11-series používají Intel Xeon E5-2670 @ 2,6 GHz a H-series používá Intel Xeon E5-2667 v3 @ 3,2 GHz. Podrobné informace a informace o použití těchto velikostí najdete v tématu s [vysokým výkonem výpočetních virtuálních počítačů](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Řady Dv3-Series, Dv2-Series, řady D-Series, řady D-Series jsou ideální pro aplikace, které vyžadují rychlejší procesory, lepší výkon místních disků nebo mají vyšší nároky na paměť. Nabízejí výkonnou kombinaci pro mnoho podnikových aplikací.
* Někteří z fyzických hostitelů v datových centrech Azure nemusí podporovat větší velikosti virtuálních počítačů, například A5–A11. V důsledku toho se může zobrazit chybová zpráva **s konfigurací virtuálního počítače {Name} se** nepovedlo nebo **se nepovedlo vytvořit virtuální počítač {Name}** při změně velikosti existujícího virtuálního počítače na novou velikost. vytváření nového virtuálního počítače ve virtuální síti vytvořené před 16. dubna 2013; nebo přidání nového virtuálního počítače do existující cloudové služby. Alternativní řešení pro každý scénář nasazení najdete v tématu [Chyba: "Nepodařilo se nakonfigurovat virtuální počítač"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) na fóru podpory.
* Vaše předplatné může také omezovat počet jader, které můžete v určitých rodinách velikostí nasadit. S požadavkem na zvýšení této kvóty se obraťte na podporu Azure.

## <a name="performance-considerations"></a>Otázky výkonu
Vytvořili jsme koncept výpočetní jednotky Azure (ACU), aby bylo možné porovnat výpočetní výkon (CPU) napříč SKU Azure a zjistit, která SKU s největší největší úrovní vyhovuje vašim požadavkům na výkon.  Jednotka ACU je aktuálně stanovená tak, že malý virtuální počítač (Standard_A1) má 100 ACU a ostatní jednotky SKU jsou pak ohodnoceny podle relativního výsledku standardního srovnávacího testu.

> [!IMPORTANT]
> ACU je jenom informativní údaj. Výsledek v případě konkrétních úloh může být odlišný.
>
>

<br>

| Rodina SKU | ACU na jádro |
| --- | --- |
| [ExtraSmall](#a-series) |50 |
| [Malé ExtraLarge](#a-series) |100 |
| [A5 – 7](#a-series) |100 |
| [A8-A11](#a-series) |225* |
| [A v2](#av2-series) |100 |
| [Trojrozměrné](#d-series) |160 |
| [D V2](#dv2-series) |160 – 190 * |
| [D V3](#dv3-series) |160 – 190 * |
| [E V3](#ev3-series) |160 – 190 * |
| [Věcn](#g-series) |180 - 240* |
| [Y](#h-series) |290 - 300* |

Hodnoty ACU s hvězdičkou označují použití technologie Intel® Turbo, která může zvýšit frekvenci procesoru podle aktuální potřeby. Množství nárůst se může lišit v závislosti na velikosti virtuálního počítače, úlohy a dalších úlohách spuštěných na stejném hostiteli.

## <a name="size-tables"></a>Tabulky velikostí
Následující tabulky pro jednotlivé velikosti virtuálních počítačů uvádějí poskytované kapacity.

* Kapacita úložiště je v jednotkách GiB, tj. 1024^3 bajtů. Při porovnávání diskové kapacity měřené v GB (1000^3 B) s kapacitou měřenou v GiB (1024^3) pamatujte, že údaj v GiB je číselně menší. Například 1023 GiB = 1098,4 GB
* Propustnost disku se měří v počtu V/V operací za sekundu (IOPS) a v MB/s, kde 1 MB/s = 10^6 bajtů/s.
* Disky pro ukládání dat můžou fungovat v režimu s mezipamětí, nebo bez ní. Pro diskové operace s mezipamětí je možné nastavit mezipaměť na hostiteli **jen na čtení** nebo **na čtení i zápis**. Pro diskové operace bez mezipaměti je mezipaměť na hostiteli nastavená na **žádná**.
* Maximální šířka pásma sítě je maximální přenosová kapacita vyhrazená a přidělená danému typu virtuálního počítače. Maximální šířka pásma slouží pro orientaci při výběru správného typu virtuálního počítače, aby byla zajištěna dostatečná síťová kapacita. Při přechodu mezi nízkou, střední, vysokou a vysokou úrovní se propustnost zvyšuje odpovídajícím způsobem. Skutečný výkon sítě bude záviset na mnoha faktorech včetně zatížení aplikací a sítě a síťového nastavení aplikace.

## <a name="a-series"></a>A-Series
| Velikost            | Procesorová jádra | Paměť: GiB  | Dočasné úložiště: GiB       | Max. počet NIC / Šířka pásma sítě |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| ExtraSmall      | 1         | 0,768        | 20                   | 1 / malá |
| Malý           | 1         | 1.75         | 225                  | 1 / střední |
| Střední          | 2         | 3,5          | 490                  | 1 / střední |
| Velký           | 4         | 7            | 1000                 | 2 / vysoká |
| ExtraLarge      | 8         | 14           | 2040                 | 4 / vysoká |
| A5              | 2         | 14           | 490                  | 1 / střední |
| A6              | 4         | 28           | 1000                 | 2 / vysoká |
| Jednou              | 8         | 56           | 2040                 | 4 / vysoká |

## <a name="a-series---compute-intensive-instances"></a>A-series – Instance náročné na výpočetní výkon
Informace a informace o použití těchto velikostí najdete v tématu [vysoké výkon výpočetních virtuálních počítačů](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

| Velikost            | Procesorová jádra | Paměť: GiB  | Dočasné úložiště: GiB       | Max. počet NIC / Šířka pásma sítě |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| A8             |8          | 56           | 1817                 | 2 / vysoká |
| A9             |16         | 112          | 1817                 | 4 / velmi vysoká |
| A10             |8          | 56           | 1817                 | 2 / vysoká |
| A11             |16         | 112          | 1817                 | 4 / velmi vysoká |

\*Podporuje RDMA

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
| Standard_D15_v2 | 20        | 140          | 1 000                | 8 / velmi vysoká |

## <a name="dv3-series"></a>Dv3-series

| Velikost            | Procesorová jádra | Paměť: GiB   | Dočasné úložiště (SSD): GiB       | Max. počet NIC / Šířka pásma sítě |
|---------------- | --------- | ------------- | -------------------- | ---------------------------- |
| Standard_D2_v3  | 2         | 8             | 50                   | 2 / střední |
| Standard_D4_v3  | 4         | 16            | 100                  | 2 / vysoká |
| Standard_D8_v3  | 8         | 32            | 200                  | 4 / vysoká |
| Standard_D16_v3 | 16        | 64            | 400                  | 8 / velmi vysoká |
| Standard_D32_v3 | 32        | 128           | 800                  | 8 / velmi vysoká |
| Standard_D48_v3 | 48        | 192           | 1200                 | 8 / velmi vysoká |
| Standard_D64_v3 | 64        | 256           | 1600                 | 8 / velmi vysoká |

## <a name="ev3-series"></a>Ev3-series

| Velikost            | Procesorová jádra | Paměť: GiB   | Dočasné úložiště (SSD): GiB       | Max. počet NIC / Šířka pásma sítě |
|---------------- | --------- | ------------- | -------------------- | ---------------------------- |
| Standard_E2_v3  | 2         | 16            | 50                   | 2 / střední |
| Standard_E4_v3  | 4         | 32            | 100                  | 2 / vysoká |
| Standard_E8_v3  | 8         | 64            | 200                  | 4 / vysoká |
| Standard_E16_v3 | 16        | 128           | 400                  | 8 / velmi vysoká |
| Standard_E32_v3 | 32        | 256           | 800                  | 8 / velmi vysoká |
| Standard_E48_v3 | 48        | 384           | 1200                 | 8 / velmi vysoká |
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
Virtuální počítače Azure H-series jsou další generací počítačů s vysokým výpočetním výkonem a jsou zaměřené na výpočetně vysoce náročné úlohy, jako je např. molekulární modelování, výpočty dynamiky tekutin a podobně. Tyto 8 a 16 jader virtuálních počítačů jsou postavené na technologii Intel Haswell E5-2667 V3 procesor, která nabízí DDR4 paměť a místní úložiště založené na SSD.

Řada H-series nabízí kromě značného výkonu procesorů i řadu možností přímého přístupu do paměti vzdáleného počítače (RDMA) s nízkou latencí díky technologii FDR InfiniBand, a množství konfigurací paměti pro podporu požadavků na vysoce paměťově náročné výpočetní operace.

| Velikost            | Procesorová jádra | Paměť: GiB  | Dočasné úložiště (SSD): GiB       | Max. počet NIC / Šířka pásma sítě |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_H8     | 8         | 56           | 1000                 | 8 / vysoká |
| Standard_H16    | 16        | 112          | 2000                 | 8 / velmi vysoká |
| Standard_H8m    | 8         | 112          | 1000                 | 8 / vysoká |
| Standard_H16m   | 16        | 224          | 2000                 | 8 / velmi vysoká |
| Standard_H16r*  | 16        | 112          | 2000                 | 8 / velmi vysoká |
| Standard_H16mr* | 16        | 224          | 2000                 | 8 / velmi vysoká |

\*Podporuje RDMA

## <a name="configure-sizes-for-cloud-services"></a>Konfigurovat velikosti pro Cloud Services
Velikost virtuálního počítače instance role můžete zadat jako součást modelu služby, který je popsán v [definičním souboru služby](cloud-services-model-and-package.md#csdef). Velikost role určuje počet jader procesoru, kapacitu paměti a velikost místního systému souborů, která je přidělena spuštěné instanci. Vyberte velikost role na základě požadavku na prostředky vaší aplikace.

Tady je příklad nastavení velikosti role Standard_D2 pro instanci webové role:

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2">
...
</WorkerRole>
```

## <a name="changing-the-size-of-an-existing-role"></a>Změna velikosti stávající role

V případě, že dojde ke změnám vašich úloh nebo k dispozici nové velikosti virtuálních počítačů, možná budete chtít změnit velikost vaší role. Pokud to chcete udělat, musíte změnit velikost virtuálního počítače v souboru definice služby (jak vidíte výše), znovu zabalit cloudovou službu a nasadit ji.

>[!TIP]
> Pro vaši roli můžete chtít použít jiné velikosti virtuálních počítačů v různých prostředích (např. testování vs produkce). Jedním ze způsobů, jak to provést, je vytvořit v projektu více souborů definice služby (. csdef) a pak pomocí nástroje CSPack vytvořit různé balíčky cloudových služeb pro každé prostředí během automatizovaného sestavování. Další informace o prvcích balíčku Cloud Services a o tom, jak je vytvořit, najdete v tématu [co je model cloudových služeb a jak ho mám zabalit?](cloud-services-model-and-package.md)
>
>

## <a name="get-a-list-of-sizes"></a>Získat seznam velikostí
Seznam velikostí můžete získat pomocí PowerShellu nebo REST API. REST API [najdete tady](/previous-versions/azure/reference/dn469422(v=azure.100)). Následující kód je příkaz prostředí PowerShell, který vypíše všechny dostupné velikosti pro Cloud Services. 

```powershell
Get-AzureRoleSize | where SupportedByWebWorkerRoles -eq $true | select InstanceSize, RoleSizeLabel
```

## <a name="next-steps"></a>Další kroky
* Další informace o [Limitech, kvótách a omezeních předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md)
* Přečtěte si další informace [o vysoce výkonných výpočetních virtuálních](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) počítačích pro úlohy HPC.



