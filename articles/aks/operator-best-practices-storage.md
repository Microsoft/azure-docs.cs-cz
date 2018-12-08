---
title: Osvědčené postupy – operátor - úložiště ve službě Azure Kubernetes služby (AKS)
description: Podívejte se na clusteru – operátor nejlepší postupy pro úložiště, šifrování dat a zálohování ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: iainfou
ms.openlocfilehash: 691decb88188a428edfeab1ea9e99c48876b6d9f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109714"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro ukládání a zálohování ve službě Azure Kubernetes Service (AKS)

Jak vytvořit a spravovat clustery ve službě Azure Kubernetes Service (AKS), vaše aplikace často potřebují úložiště. Je důležité porozumět požadavkům na výkon a přístup k metodám pro podů může poskytnout vhodnou k aplikacím. Velikost uzlů AKS může mít vliv na tyto možnosti úložiště. Měli byste naplánovat také způsoby, jak zálohovat a otestovat proces obnovení pro připojené úložiště.

Tento článek o osvědčených postupech se zaměřuje na aspekty volby úložiště pro operátory clusteru. V tomto článku se dozvíte:

> [!div class="checklist"]
> * Jaké typy služby storage jsou k dispozici
> * Postup pro nastavení správné velikosti uzlů AKS pro výkon úložiště
> * Rozdíly mezi statickými a dynamickými zřizování svazků
> * Způsoby, jak zálohovat a zabezpečení datové svazky

## <a name="choose-the-appropriate-storage-type"></a>Zvolte typ odpovídající úložiště

**Osvědčené postupy pro moduly** -o potřebách vaší aplikace a vyberte správné úložiště. Použití vysoce výkonného, zálohují na disky SSD úložiště pro úlohy v produkčním prostředí. Plánování síťové úložiště, když je potřeba pro více souběžných připojení.

Aplikace často vyžadují různé typy a rychlosti úložiště. Potřebují vašich aplikací, které se připojí k jednotlivým podů, nebo je sdílet mezi více podů? Úložiště je pro přístup jen pro čtení k datům nebo zapisovat velké objemy strukturovaných dat? Tato úložiště musí určit nejvhodnější typ úložiště používat.

Následující tabulka uvádí typy úložiště k dispozici a jejich funkce:

| Případ použití | Modul plug-in svazku | Čtení/zápis | Jen pro čtení: n | Čtení a zápis mnoho |
|----------|---------------|-----------------|----------------|-----------------|
| Sdílená konfigurace       | Soubory Azure   | Ano | Ano | Ano |
| Strukturovaných dat        | Disky Azure   | Ano | Ne  | Ne  |
| Data aplikací, sdílené složky jen pro čtení | [Dysk (preview)][dysk] | Ano | Ano | Ne  |
| Nestrukturovaných dat, operace systému souborů | [BlobFuse (preview)][blobfuse] | Ano | Ano | Ano |

Dva primární typy úložiště k dispozici pro svazky ve službě AKS se zálohují na disky Azure nebo Azure Files. Pro zlepšení zabezpečení použít oba typy úložiště Azure Storage Service Encryption (SSE) ve výchozím nastavení, která šifruje neaktivní uložená data. Disky nelze aktuálně zašifrovaný službou Azure Disk Encryption na úrovni uzlu AKS.

Služba soubory Azure jsou aktuálně dostupné v rámci úrovně výkonu. Disky Azure jsou dostupné ve vrstvách Standard a Premium výkonu:

- *Premium* vysoce výkonných discích SSD (Solid-State Drive) se zálohují na disky. Disky Premium se doporučují pro všechny úlohy v produkčním prostředí.
- *Standardní* disků jsou zálohovány pomocí běžných rotujících disků (HDD) a jsou vhodné pro archivaci nebo zřídka používaná data.

Porozumět požadavkům na výkon aplikace a vzorce vybrat vhodnou úroveň přístupu. Další informace o velikosti Managed Disks a úrovně výkonu najdete v tématu [Přehled služby Azure Managed Disks][managed-disks]

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Vytvoření a použití třídy úložiště k definování potřeby aplikace

Typ úložiště používáte je definován pomocí Kubernetes *třídy úložiště*. Třídy úložiště se pak odkazuje ve specifikaci pod nebo nasazení. Tyto definice spolupracují a vytvořit odpovídající úložiště a připojte ho k pody. Další informace najdete v tématu [třídy úložiště ve službě AKS][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>Velikost uzlů pro požadavky na úložiště

**Osvědčené postupy pro moduly** – velikost každého uzlu podporuje maximální počet disků. Jiný uzel velikosti také nabízejí různé množství místní úložiště a šířku pásma sítě. Plán pro vaše požadavky na aplikace nasadit odpovídající velikost uzlů.

Uzly AKS spouštět jako virtuální počítače Azure. Různé typy a velikosti virtuálního počítače jsou k dispozici. Všechny velikosti virtuálních počítačů poskytuje různé množství základní prostředky, jako je například procesor a paměť. Tyto velikosti virtuálních počítačů mají maximální počet disků, které je možné připojit. Výkon úložiště se také liší velikostí virtuálních počítačů pro maximální místní a připojené disky IOPS (vstupně výstupní operace za sekundu).

Pokud vaše aplikace vyžadují Azure Disks jako řešení úložiště, plánování a zvolte příslušný uzel velikost virtuálního počítače. Množství CPU a paměti není jediným faktorem při zvolte velikost virtuálního počítače. Možnosti úložiště jsou také důležité. Například i *Standard_B2ms* a *Standard_DS2_v2* velikosti virtuálních počítačů obsahovat podobné objem prostředků procesoru a paměti. Jejich potenciální výkon úložiště se liší, jak je znázorněno v následující tabulce:

| Typ uzlu a velikost | Virtuální procesory | Paměť (GiB) | Max. datových disků | Maximální počet mezipamětí vstupně-výstupních diskových | Maximální propustnost bez vyrovnávací paměti (MB/s) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1,920                  | 22.5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6,400                  | 96                             |

Tady *Standard_DS2_v2* umožňuje double počtu připojených disků a poskytuje tři až čtyřikrát množství propustnost vstupně-výstupních operací a disku. Pokud pouze podívali se na základní výpočetní prostředky a porovnání nákladů, můžete se rozhodnout *Standard_B2ms* virtuálního počítače upravit velikost a výkon nízký úložiště a omezení. Práce s vaším vývojářským týmem aplikace porozumět jejich požadavky na kapacitu a výkon úložiště. Výběr vhodné velikosti virtuálního počítače pro uzly AKS, aby splňovaly nebo překračovaly jejich požadavkům na výkon. Pravidelně směrného plánu aplikace podle potřeby upravit velikost virtuálního počítače.

Další informace o dostupných velikostech virtuálních počítačů najdete v tématu [velikostí pro virtuální počítače s Linuxem v Azure][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Dynamické zřizování svazků

**Osvědčené postupy pro moduly** – Pokud chcete snížit režii na správu a umožňují škálování, nemáte staticky vytvořit a přiřadit svazky trvalé. Dynamické zřizování použijte. Ve třídách úložiště Definujte zásady odpovídající reclaim minimalizovat náklady na úložiště nepotřebné odstraněný pody.

Pokud potřebujete připojit úložiště podů, použijte trvalé svazky. Tyto svazky trvalé lze vytvořit ručně nebo dynamicky. Ruční vytvoření trvalého svazků přidá režie na správu a omezuje vaší schopnost škálování. Použití dynamické trvalý svazek zřizování, které umožňuje zjednodušit správu úložišť a umožňují aplikacím rozvíjet a škálovat podle potřeby.

![Trvalý svazek deklarací identity v clusteru služby Kubernetes v Azure (AKS)](media/concepts-storage/persistent-volume-claims.png)

Trvalý svazek deklarace identity (PVC) umožňuje vytvářet dynamicky úložiště podle potřeby. Základní disky Azure jsou vytvořeny jako podů o ně požádat. V definici pod požádáte o svazku se vytvoří a připojí k cestu navrženého připojení

Koncepty o tom, jak dynamicky vytvářet a používat svazky, naleznete v tématu [trvalé deklarací svazky][aks-concepts-storage-pvcs].

Tyto svazky v akci najdete v tématu Jak dynamicky vytvořit a použít trvalý svazek s [Azure Disks] [ dynamic-disks] nebo [Azure Files][dynamic-files].

V rámci definic třídy úložiště, nastavte odpovídající *reclaimPolicy*. Tato reclaimPolicy řídí chování základní prostředek služby Azure storage při pod se odstraní a trvalý svazek již může být nutná. Tento základní prostředek spravovat úložiště můžete odstranit nebo uchovávají pro použití s budoucí pod. Můžete nastavit reclaimPolicy *zachovat* nebo *odstranit*. O vašich potřebách aplikace a implementovat pravidelné kontroly pro úložiště, které chcete-li minimalizovat množství zrušení využité úložiště, který je používán a účtuje se uchovávají.

Další informace o možnostech třídy úložiště, najdete v části [úložiště uvolnit zásady][reclaim-policy].

## <a name="secure-and-back-up-your-data"></a>Zabezpečení a zálohovat data

**Osvědčené postupy pro moduly** – zálohujete svá data pomocí vhodného nástroje pro váš typ úložiště, jako je například Heptio Ark nebo Azure Site Recovery. Ověření integrity a zabezpečení, tyto zálohy.

Při ukládání vašich aplikací a využívat data se ukládají na discích nebo v souborech, je potřeba provést pravidelného zálohování nebo snímky tato data. Disky Azure můžete použít předdefinované snímku technologie. Budete muset hook pro vaše aplikace k vyprázdnění zápisy na disk před provedením operace vytvoření snímku. [Heptio Ark] [ heptio-ark] můžete zálohovat trvalé svazky spolu s prostředky další clusteru a konfiguracemi. Pokud to není možné [odebrání stavu z vašich aplikací][remove-state], zálohování dat z trvalého svazky a pravidelně testujeme funkčnost operací obnovení k ověření integrity dat a procesy nezbytné.

Principy omezení různé přístupy k zálohování dat a pokud je potřeba uvést vaše data před snímku. Zálohování dat není nutně umožnit obnovení prostředí pro vaše aplikace nasazení clusteru. Další informace o těchto scénářích najdete v tématu [osvědčené postupy pro obchodní kontinuity podnikových procesů a zotavení po havárii ve službě AKS][best-practices-multi-region].

## <a name="next-steps"></a>Další postup

Tento článek zaměřuje na úložiště osvědčené postupy ve službě AKS. Další informace o základní informace o úložišti v Kubernetes najdete v tématu [koncepty úložiště pro aplikace ve službě AKS][aks-concepts-storage].

<!-- LINKS - External -->
[heptio-ark]: https://github.com/heptio/ark
[dysk]: https://github.com/Azure/kubernetes-volume-drivers/tree/master/flexvolume/dysk
[blobfuse]: https://github.com/Azure/azure-storage-fuse

<!-- LINKS - Internal -->
[aks-concepts-storage]: concepts-storage.md
[vm-sizes]: ../virtual-machines/linux/sizes.md
[dynamic-disks]: azure-disks-dynamic-pv.md
[dynamic-files]: azure-files-dynamic-pv.md
[reclaim-policy]: concepts-storage.md#storage-classes
[aks-concepts-storage-pvcs]: concepts-storage.md#persistent-volume-claims
[aks-concepts-storage-classes]: concepts-storage.md#storage-classes
[managed-disks]: ../virtual-machines/linux/managed-disks-overview.md
[best-practices-multi-region]: operator-best-practices-multi-region.md
[remove-state]: operator-best-practices-multi-region.md#remove-service-state-from-inside-containers
