---
title: Oblast Azure
description: Přečtěte si o oblastech pro spouštění virtuálních počítačů v Azure.
author: mimckitt
ms.author: mimckitt
ms.reviewer: cynthnn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.openlocfilehash: e874a76cc447158127da8482f23bf03d56961818
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102504605"
---
# <a name="regions-for-virtual-machines-in-azure"></a>Oblasti pro virtuální počítače v Azure

Je důležité pochopit, jak a kde se virtuální počítače (VM) v Azure provozují, a také vědět, jaké máte možnosti pro zajištění maximálního výkonu, dostupnosti a redundance. Tento článek poskytuje přehled funkcí redundance a dostupnosti Azure.


## <a name="what-are-azure-regions"></a>Co jsou oblasti Azure?
Azure funguje v několika datových centrech po celém světě. Tato datová centra jsou seskupená do v geografických oblastí. To vám poskytuje flexibilitu při výběru místa pro sestavení vaší aplikace. 

Prostředky Azure vytvoříte v definovaných geografických oblastech, jako jsou Západní USA, Severní Evropa nebo jihovýchodní Asie. [Seznam oblastí a jejich umístění](https://azure.microsoft.com/regions/) si můžete prohlédnout. V každé oblasti existuje několik datových center, která zajišťují redundanci a dostupnost. Tento přístup vám dává lepší flexibilitu při návrhu aplikací pro vytváření virtuálních počítačů, které jsou nejblíže vašim uživatelům, a splnění všech právních a daňových požadavků.

## <a name="special-azure-regions"></a>Speciální oblasti Azure
Azure má některé zvláštní oblasti, které můžete chtít použít při vytváření aplikací pro dodržování předpisů nebo právní účely. Mezi tyto speciální oblasti patří:

* **US Gov – Iowa** a **US Gov – Virginie**.
  * Fyzická a logická síťově izolovaná instance Azure pro partnery a úřady státní správy USA, která je obsluhovaná prověřenými občany USA. Zahrnuje další certifikace dodržování předpisů, jako je [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) a [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA). Další informace o službě [Azure Government](https://azure.microsoft.com/features/gov/)
* **Severní Čína** a **Východní Čína**
  * Tyto oblasti jsou dostupné prostřednictvím jedinečného partnerství mezi společnostmi Microsoft a 21Vianet, kdy společnost Microsoft nespravuje tato datová centra přímo. Přečtěte si další informace o [Azure Čína 21Vianet](https://www.windowsazure.cn/).
* **Střední Německo** a **Severovýchodní Německo**
  * Tyto oblasti jsou k dispozici prostřednictvím modelu důvěryhodného data, kde se data zákazníků nacházejí v Německu pod kontrolou T-Systems, Telekom společnosti, která slouží jako důvěryhodný datový zplnomocněnec.

## <a name="region-pairs"></a>Párování oblastí
Každá oblast Azure je spárovaná s jinou oblastí na stejném území (jako je USA, Evropa nebo Asie). Tento přístup umožňuje replikaci prostředků (jako je třeba úložiště virtuálních počítačů) napříč daným územím, která by měla snížit pravděpodobnost, že přírodní katastrofy, občanské nepokoje, výpadky napájení nebo fyzické výpadky sítě ovlivní obě oblasti současně. Mezi další výhody párování oblastí patří:

* V případě rozsáhlejšího výpadku Azure je z každého páru jedna oblast určená jako prioritní. To pomáhá zkrátit čas potřebný k obnovení aplikací. 
* Plánované aktualizace Azure se pro spárované oblasti nasazují po jedné. Tím se minimalizují prostoje a riziko výpadku aplikací.
* Pro daňové účely a účely vymáhání zákonů jsou data nadále uložená ve stejné zeměpisné oblasti jako pár oblastí (s výjimkou oblasti Brazílie – jih).

Mezi příklady párů oblasti patří:

| Primární | Sekundární |
|:--- |:--- |
| USA – západ |East US |
| Severní Evropa |West Europe |
| Southeast Asia |Východní Asie |

Úplný [seznam spárovaných oblastí najdete tady](../best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="feature-availability"></a>Dostupnost funkcí
Některé služby nebo funkce virtuálních počítačů jsou dostupné jenom v některých oblastech, třeba konkrétní velikosti virtuálních počítačů nebo typy úložišť. Existují také globální služby Azure, které nevyžadují výběr konkrétní oblasti, jako je [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), [Traffic Manager](../traffic-manager/traffic-manager-overview.md) nebo [Azure DNS](../dns/dns-overview.md). Možnost zkontrolovat [dostupnost služeb Azure v jednotlivých oblastech](https://azure.microsoft.com/regions/#services) vám pomůže při navrhování prostředí pro vaše aplikace. [V každé oblasti můžete také programově zadat dotaz na podporované velikosti a omezení virtuálních počítačů](../azure-resource-manager/templates/error-sku-not-available.md).

## <a name="storage-availability"></a>Dostupnost úložiště
Znalost území a oblastí Azure je důležitá, pokud zvažujete dostupné možnosti replikace úložiště. Možnosti replikace závisejí na typu úložiště.

**Spravované disky Azure**
* Místně redundantní úložiště (LRS)
  * Replikuje data třikrát v rámci oblasti, ve které jste vytvořili účet úložiště.

**Disky založené na účtu úložiště**
* Místně redundantní úložiště (LRS)
  * Replikuje data třikrát v rámci oblasti, ve které jste vytvořili účet úložiště.
* Zónově redundantní úložiště (ZRS)
  * Replikuje data třikrát v rámci dvou nebo tří zařízení, a to v jedné oblasti nebo napříč dvěma oblastmi.
* Geograficky redundantní úložiště (GRS)
  * Replikuje data do sekundární oblasti, která je od primární oblasti vzdálená stovky kilometrů.
* Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)
  * Replikuje data do sekundární oblasti (stejně jako u GRS), ale k datům v sekundárním umístění poskytuje přístup jenom pro čtení.

V následující tabulce najdete stručný přehled rozdílů jednotlivých typů replikace:

| Strategie replikace | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Data se replikují napříč různými zařízeními. |No |Yes |Yes |Yes |
| Data je možné načíst ze sekundárního i primárního umístění. |No |No |No |Yes |
| Počet kopií dat uchovávaných na samostatných uzlech |3 |3 |6 |6 |

Další informace o [možnostech replikace služby Azure Storage najdete tady](../storage/common/storage-redundancy.md). Další informace o spravovaných discích najdete v tématu [Přehled služby Azure Managed Disks](./managed-disks-overview.md).

### <a name="storage-costs"></a>Náklady na úložiště
Ceny se liší v závislosti na zvolené dostupnosti a typu úložiště.

**Spravované disky Azure**
* Premium Managed Disks jsou zajištěny Solid-State jednotkami (SSD) a standardními Managed Disksmi, které jsou zálohovány pomocí běžných rotujících disků. Spravované disky úrovně Premium i Standard se účtují na základě zřízené kapacity disku.

**Nespravované disky**
* Premium Storage se zálohuje pomocí Solid-Statech jednotek (SSD) a účtuje se na základě kapacity disku.
* Storage úrovně Standard se zálohuje pomocí běžných rotujících disků a účtuje se na základě využité kapacity a požadované dostupnosti úložiště.
  * Za přenos dat geografické replikace se u RA-GRS účtují navíc náklady na šířku pásma pro replikaci těchto dat do jiné oblasti Azure.

Informace o cenách pro různé typy úložišť a možnosti dostupnosti najdete v tématu [Ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [oblasti Azure](https://azure.microsoft.com/global-infrastructure/regions/).