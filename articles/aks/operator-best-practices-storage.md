---
title: Doporučené postupy pro ukládání a zálohování
titleSuffix: Azure Kubernetes Service
description: Seznamte se s doporučenými postupy operátora clusteru pro úložiště, šifrování dat a zálohování ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 90abf2e36fd46c707904d87f00362091fe931743
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668105"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Doporučené postupy pro ukládání a zálohování ve službě Azure Kubernetes Service (AKS)

Při vytváření a správě clusterů ve službě Azure Kubernetes Service (AKS) vaše aplikace často potřebují úložiště. Je důležité pochopit potřeby výkonu a metody přístupu pro pody, abyste mohli poskytnout příslušné úložiště aplikacím. Velikost uzlu AKS může mít vliv na tyto možnosti úložiště. Měli byste také naplánovat způsoby zálohování a testování procesu obnovení připojeného úložiště.

Tento článek osvědčených postupů se zaměřuje na důležité informace o úložišti pro operátory clusteru. V tomto článku se dozvíte:

> [!div class="checklist"]
> * Jaké typy úložišť jsou k dispozici
> * Jak správně velikost uzlů AKS pro výkon úložiště
> * Rozdíly mezi dynamickým a statickým zřizováním svazků
> * Způsoby zálohování a zabezpečení datových svazků

## <a name="choose-the-appropriate-storage-type"></a>Zvolte vhodný typ úložiště

**Pokyny pro osvědčené postupy** – seznamte se s potřebami vaší aplikace a vyberte správné úložiště. Používejte vysoce výkonné úložiště s s ssd pro produkční úlohy. Naplánujte síťové úložiště, pokud je potřeba více souběžných připojení.

Aplikace často vyžadují různé typy a rychlosti úložiště. Potřebují vaše aplikace úložiště, které se připojuje k jednotlivým podům nebo je sdíleno mezi více pody? Je úložiště pro přístup jen pro čtení k datům nebo k zápisu velkého množství strukturovaných dat? Tyto potřeby úložiště určují nejvhodnější typ úložiště, který se má použít.

V následující tabulce jsou uvedeny dostupné typy úložišť a jejich možnosti:

| Případ použití | Modul plug-in pro objem | Čtení/zápis jednou | Mnoho lidí jen pro čtení | Čtení/zápis mnoho | Podpora kontejnerů systému Windows Server |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Sdílená konfigurace       | Soubory Azure   | Ano | Ano | Ano | Ano |
| Strukturovaná data aplikací        | Disky Azure   | Ano | Ne  | Ne  | Ano |
| Nestrukturovaná data, operace systému souborů | [BlobFuse][blobfuse] | Ano | Ano | Ano | Ne |

Dva primární typy úložiště poskytované pro svazky v AKS jsou zálohovány Azure Disky nebo Soubory Azure. Chcete-li zlepšit zabezpečení, oba typy úložiště používají Azure Storage Service Encryption (SSE) ve výchozím nastavení, který šifruje data v klidovém stavu. Disky nelze aktuálně šifrovat pomocí šifrování disku Azure na úrovni uzlu AKS.

Soubory Azure jsou aktuálně dostupné ve standardní úrovni výkonu. Disky Azure jsou dostupné ve standardních a prémiových úrovních výkonu:

- *Disky Premium* jsou zálohovány vysoce výkonnými disky SSD (SSD). Disky Premium se doporučují pro všechny produkční úlohy.
- *Standardní* disky jsou zálohovány běžnými rotujícími disky (HDD) a jsou vhodné pro archivní nebo zřídka přistupovaná data.

Seznamte se s potřebami výkonu aplikace a vzory přístupu a zvolte příslušnou úroveň úložiště. Další informace o velikostech a úrovních výkonu spravovaných disků najdete v tématu [Přehled spravovaných disků Azure][managed-disks]

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Vytvoření a použití tříd úložiště k definování potřeb aplikace

Typ úložiště, které používáte, je definován pomocí *tříd úložiště*Kubernetes . Třída úložiště je pak odkazována ve specifikaci podu nebo nasazení. Tyto definice spolupracují na vytvoření vhodného úložiště a jeho připojení k podům. Další informace naleznete [v tématu Storage třídy v AKS][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>Velikost uzlů pro potřeby úložiště

**Pokyny pro osvědčené postupy** – velikost každého uzlu podporuje maximální počet disků. Různé velikosti uzlů také poskytují různé objemy místního úložiště a šířku pásma sítě. Naplánujte pro vaše požadavky aplikace nasadit odpovídající velikost uzlů.

Uzly AKS běží jako virtuální počítače Azure. K dispozici jsou různé typy a velikosti virtuálního počítače. Každá velikost virtuálního počítače poskytuje různé množství základních prostředků, jako je procesor a paměť. Tyto velikosti virtuálních počítačů mají maximální počet disků, které lze připojit. Výkon úložiště se také liší mezi velikostmi virtuálních počítačů pro maximální místní a připojené diskové vstupně-výstupní operace (vstupní a výstupní operace za sekundu).

Pokud vaše aplikace vyžadují Azure Disky jako jejich řešení úložiště, naplánujte a zvolte vhodnou velikost virtuálního počítače uzlu. Množství procesoru a paměti není jediným faktorem, když zvolíte velikost virtuálního počítače. Možnosti úložiště jsou také důležité. Například *velikosti Standard_B2ms* a *Standard_DS2_v2* virtuálních počítače obsahují podobné množství prostředků procesoru a paměti. Jejich potenciální výkon úložiště se liší, jak je znázorněno v následující tabulce:

| Typ a velikost uzlu | Virtuální procesory | Paměť (GiB) | Max. datových disků | Maximální počet viopů disku bez mezipaměti | Maximální propustnost bez mezipaměti (Mb/s) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1,920                  | 22.5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6,400                  | 96                             |

Zde *Standard_DS2_v2* umožňuje zdvojnásobit počet připojených disků a poskytuje třikrát až čtyřikrát množství viposlužby a propustnost disku. Pokud jste se podívali jenom na základní výpočetní prostředky a porovnání nákladů, můžete zvolit *Standard_B2ms* velikost virtuálního počítače a mají nízký výkon úložiště a omezení. Spolupracujte s týmem pro vývoj aplikací a seznamte se s jejich potřeby v oblasti kapacity a výkonu. Zvolte vhodnou velikost virtuálního počítače pro uzly AKS, abyste vyhověli nebo překročili jejich potřeby výkonu. Pravidelně směrné aplikace upravit velikost virtuálního počítače podle potřeby.

Další informace o dostupných velikostech virtuálních počítačů najdete [v tématu Velikosti pro virtuální počítače s Linuxem v Azure][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Dynamicky zřizovací svazky

**Pokyny pro osvědčené postupy** – chcete-li snížit režii správy a umožňují škálování, nevytvářejte a nepřiřazujte trvalé svazky. Použijte dynamické zřizování. Ve třídách úložiště definujte příslušnou zásadu vrácení, abyste minimalizovali nepotřebné náklady na úložiště po odstranění podů.

Když potřebujete připojit úložiště k podům, použijete trvalé svazky. Tyto trvalé svazky lze vytvořit ručně nebo dynamicky. Ruční vytváření trvalých svazků zvyšuje režii na správu a omezuje možnost škálování. Pomocí dynamického trvalého zřizování svazků můžete zjednodušit správu úložiště a podle potřeby umožnit, aby vaše aplikace rostly a škálovaly.

![Trvalé deklarace svazku v clusteru Služeb Azure Kubernetes (AKS)](media/concepts-storage/persistent-volume-claims.png)

Trvalá deklarace svazku (PVC) umožňuje dynamicky vytvářet úložiště podle potřeby. Podkladové disky Azure se vytvoří jako pody, které je požadují. V definici podu požadujete vytvoření svazku a připojení k určené cestě připojení.

Koncepty dynamického vytváření a používání svazků naleznete v tématu [Trvalé deklarace svazků][aks-concepts-storage-pvcs].

Pokud chcete tyto svazky zobrazit v akci, přečtěte si, jak dynamicky vytvořit a použít trvalý svazek s [Disky Azure][dynamic-disks] nebo [Soubory Azure][dynamic-files].

Jako součást definice třídy úložiště nastavte příslušné *zásady reclaimpolicy*. Toto zásady rekultivace řídí chování základního prostředku úložiště Azure při odstranění podu a trvalý svazek již nemusí být vyžadován. Základní prostředek úložiště lze odstranit nebo zachovat pro použití s budoucí pod. Zásady reclaimpolicy lze nastavit *zachovat* nebo *odstranit*. Seznamte se s potřebami aplikací a implementujte pravidelné kontroly úložiště, které je zachováno, aby se minimalizovalo množství nevyužitého úložiště, které se používá a účtuje.

Další informace o možnostech třídy úložiště najdete v [tématu zásady obnovení úložiště][reclaim-policy].

## <a name="secure-and-back-up-your-data"></a>Zabezpečení a zálohování dat

**Pokyny pro osvědčené postupy** – Zálohujte data pomocí vhodného nástroje pro váš typ úložiště, jako je Velero nebo Azure Site Recovery. Ověřte integritu a zabezpečení těchto záloh.

Když vaše aplikace ukládají a spotřebovávají data trvalá na discích nebo v souborech, je třeba provádět pravidelné zálohování nebo snímky těchto dat. Disky Azure můžou používat integrované technologie snímků. Možná budete muset vyhledat aplikace pro vyprázdnění zápisů na disk před provedením operace snímek. [Velero][velero] může zálohovat trvalé svazky spolu s dalšími prostředky clusteru a konfiguracemi. Pokud nemůžete [odebrat stav z vašich aplikací][remove-state], zálohovat data z trvalých svazků a pravidelně testovat operace obnovení k ověření integrity dat a požadovaných procesů.

Seznamte se s omezeními různých přístupů k zálohování dat a pokud potřebujete utišit data před snímek. Zálohování dat nemusí nutně umožňují obnovit aplikační prostředí nasazení clusteru. Další informace o těchto scénářích naleznete v [tématu Doporučené postupy pro kontinuitu provozu a zotavení po havárii v AKS][best-practices-multi-region].

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřil na osvědčené postupy úložiště v AKS. Další informace o základech úložiště v Kubernetes najdete v [tématu Koncepty úložiště pro aplikace v AKS][aks-concepts-storage].

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
