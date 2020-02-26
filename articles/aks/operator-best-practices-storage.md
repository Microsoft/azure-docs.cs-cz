---
title: Osvědčené postupy – operátor - úložiště ve službě Azure Kubernetes služby (AKS)
description: Podívejte se na clusteru – operátor nejlepší postupy pro úložiště, šifrování dat a zálohování ve službě Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 6521655ded45f0a1d15c3ec40a44993d757b8854
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594664"
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

**Doprovodné materiály k osvědčeným postupům** – pochopení potřeb vaší aplikace k výběru správného úložiště. Použití vysoce výkonného, zálohují na disky SSD úložiště pro úlohy v produkčním prostředí. Plánování síťové úložiště, když je potřeba pro více souběžných připojení.

Aplikace často vyžadují různé typy a rychlosti úložiště. Potřebují vašich aplikací, které se připojí k jednotlivým podů, nebo je sdílet mezi více podů? Úložiště je pro přístup jen pro čtení k datům nebo zapisovat velké objemy strukturovaných dat? Tato úložiště musí určit nejvhodnější typ úložiště používat.

Následující tabulka uvádí typy úložiště k dispozici a jejich funkce:

| Případ použití | Modul plug-in svazku | Čtení/zápis | Jen pro čtení: n | Čtení a zápis mnoho | Podpora kontejneru Windows serveru |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Sdílená konfigurace       | Soubory Azure   | Ano | Ano | Ano | Ano |
| Strukturovaných dat        | Disky Azure   | Ano | Ne  | Ne  | Ano |
| Nestrukturovaných dat, operace systému souborů | [BlobFuse (Preview)][blobfuse] | Ano | Ano | Ano | Ne |

Dva primární typy úložiště k dispozici pro svazky ve službě AKS se zálohují na disky Azure nebo Azure Files. Pro zlepšení zabezpečení použít oba typy úložiště Azure Storage Service Encryption (SSE) ve výchozím nastavení, která šifruje neaktivní uložená data. Disky nelze aktuálně zašifrovaný službou Azure Disk Encryption na úrovni uzlu AKS.

Služba soubory Azure jsou aktuálně dostupné v rámci úrovně výkonu. Disky Azure jsou dostupné ve vrstvách Standard a Premium výkonu:

- Disky *Premium* jsou založené na vysoce výkonných discích Solid-State (SSD). Disky Premium se doporučují pro všechny úlohy v produkčním prostředí.
- *Standardní* disky jsou zajištěné pravidelnými otáčejícími se disky (HDD) a jsou vhodné pro archivované nebo zřídka používaná data.

Porozumět požadavkům na výkon aplikace a vzorce vybrat vhodnou úroveň přístupu. Další informace o velikostech Managed Disks a úrovních výkonu najdete v tématu [Azure Managed disks Overview][managed-disks] .

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Vytvoření a použití třídy úložiště k definování potřeby aplikace

Typ úložiště, který použijete, je definovaný pomocí *tříd úložiště*Kubernetes. Třídy úložiště se pak odkazuje ve specifikaci pod nebo nasazení. Tyto definice spolupracují a vytvořit odpovídající úložiště a připojte ho k pody. Další informace najdete v tématu [třídy úložiště v AKS][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>Velikost uzlů pro požadavky na úložiště

**Doprovodné materiály k osvědčeným postupům** – velikost jednotlivých uzlů podporuje maximální počet disků. Jiný uzel velikosti také nabízejí různé množství místní úložiště a šířku pásma sítě. Plán pro vaše požadavky na aplikace nasadit odpovídající velikost uzlů.

Uzly AKS spouštět jako virtuální počítače Azure. Různé typy a velikosti virtuálního počítače jsou k dispozici. Všechny velikosti virtuálních počítačů poskytuje různé množství základní prostředky, jako je například procesor a paměť. Tyto velikosti virtuálních počítačů mají maximální počet disků, které je možné připojit. Výkon úložiště se také liší velikostí virtuálních počítačů pro maximální místní a připojené disky IOPS (vstupně výstupní operace za sekundu).

Pokud vaše aplikace vyžadují Azure Disks jako řešení úložiště, plánování a zvolte příslušný uzel velikost virtuálního počítače. Množství CPU a paměti není jediným faktorem při zvolte velikost virtuálního počítače. Možnosti úložiště jsou také důležité. Mezi například *Standard_B2ms* i *Standard_DS2_v2* velikosti virtuálních počítačů patří podobné množství prostředků procesoru a paměti. Jejich potenciální výkon úložiště se liší, jak je znázorněno v následující tabulce:

| Typ uzlu a velikost | Virtuální procesory | Paměť (GiB) | Max. datových disků | Maximální počet mezipamětí vstupně-výstupních diskových | Maximální propustnost bez vyrovnávací paměti (MB/s) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1,920                  | 22.5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6,400                  | 96                             |

V tomto případě *Standard_DS2_v2* umožňuje dvojnásobek počtu připojených disků a poskytuje tři až ČTYŘIKRÁT množství IOPS a propustnosti disku. Pokud jste si prohlédli jenom základní výpočetní prostředky a porovnané náklady, můžete si vybrat velikost virtuálního počítače *Standard_B2ms* a snížit výkon a omezení úložiště. Práce s vaším vývojářským týmem aplikace porozumět jejich požadavky na kapacitu a výkon úložiště. Výběr vhodné velikosti virtuálního počítače pro uzly AKS, aby splňovaly nebo překračovaly jejich požadavkům na výkon. Pravidelně směrného plánu aplikace podle potřeby upravit velikost virtuálního počítače.

Další informace o dostupných velikostech virtuálních počítačů najdete v tématu [velikosti pro virtuální počítače Linux v Azure][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Dynamické zřizování svazků

**Doprovodné materiály k osvědčeným postupům** – ke snížení režijních nákladů na správu a umožňují škálování, nevytvářejte staticky a přiřazujte trvalé svazky. Dynamické zřizování použijte. Ve třídách úložiště Definujte zásady odpovídající reclaim minimalizovat náklady na úložiště nepotřebné odstraněný pody.

Pokud potřebujete připojit úložiště podů, použijte trvalé svazky. Tyto svazky trvalé lze vytvořit ručně nebo dynamicky. Ruční vytvoření trvalého svazků přidá režie na správu a omezuje vaší schopnost škálování. Použití dynamické trvalý svazek zřizování, které umožňuje zjednodušit správu úložišť a umožňují aplikacím rozvíjet a škálovat podle potřeby.

![Trvalý svazek deklarací identity v clusteru služby Kubernetes v Azure (AKS)](media/concepts-storage/persistent-volume-claims.png)

Trvalý svazek deklarace identity (PVC) umožňuje vytvářet dynamicky úložiště podle potřeby. Základní disky Azure jsou vytvořeny jako podů o ně požádat. V definici pod požádáte o svazku se vytvoří a připojí k cestu navrženého připojení

Koncepce, jak dynamicky vytvářet a používat svazky, najdete v tématu [deklarace trvalých svazků][aks-concepts-storage-pvcs].

Pokud se chcete podívat na tyto svazky v akci, přečtěte si téma postup dynamického vytvoření a použití trvalého svazku s [disky Azure][dynamic-disks] nebo [soubory Azure][dynamic-files].

Jako součást definic vaší třídy úložiště nastavte odpovídající *reclaimPolicy*. Tato reclaimPolicy řídí chování základní prostředek služby Azure storage při pod se odstraní a trvalý svazek již může být nutná. Tento základní prostředek spravovat úložiště můžete odstranit nebo uchovávají pro použití s budoucí pod. ReclaimPolicy může nastavit, aby se *zachoval* nebo *odstranil*. O vašich potřebách aplikace a implementovat pravidelné kontroly pro úložiště, které chcete-li minimalizovat množství zrušení využité úložiště, který je používán a účtuje se uchovávají.

Další informace o možnostech třídy úložiště najdete v tématu [zásady pro redeklaraci úložiště][reclaim-policy].

## <a name="secure-and-back-up-your-data"></a>Zabezpečení a zálohovat data

**Doprovodné materiály k osvědčeným postupům** – zálohujte data pomocí vhodného nástroje pro typ úložiště, například Velero nebo Azure Site Recovery. Ověření integrity a zabezpečení, tyto zálohy.

Při ukládání vašich aplikací a využívat data se ukládají na discích nebo v souborech, je potřeba provést pravidelného zálohování nebo snímky tato data. Disky Azure můžete použít předdefinované snímku technologie. Před provedením operace snímku možná budete muset vyhledat své aplikace a vyprázdnit zápisy na disk. [Velero][velero] může zálohovat trvalé svazky spolu s dalšími prostředky a konfiguracemi clusteru. Pokud nemůžete [Odebrat stav z vašich aplikací][remove-state], zálohujte data z trvalých svazků a pravidelně otestujte operace obnovení, abyste ověřili integritu dat a požadované procesy.

Principy omezení různé přístupy k zálohování dat a pokud je potřeba uvést vaše data před snímku. Zálohování dat není nutně umožnit obnovení prostředí pro vaše aplikace nasazení clusteru. Další informace o těchto scénářích najdete v tématu [osvědčené postupy pro zajištění kontinuity podnikových procesů a zotavení po havárii v AKS][best-practices-multi-region].

## <a name="next-steps"></a>Další kroky

Tento článek zaměřuje na úložiště osvědčené postupy ve službě AKS. Další informace o základech úložiště v Kubernetes najdete v tématu [Koncepty úložiště pro aplikace v AKS][aks-concepts-storage].

<!-- LINKS - External -->
[velero]: https://github.com/heptio/velero
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
