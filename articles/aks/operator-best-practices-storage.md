---
title: Osvědčené postupy pro úložiště a zálohování
titleSuffix: Azure Kubernetes Service
description: Seznamte se s osvědčenými postupy pro úložiště, šifrování dat a zálohy ve službě Azure Kubernetes Service (AKS) od operátora clusteru.
services: container-service
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 722fe393ad7637be20360463a4c3b6234224a036
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88653966"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro úložiště a zálohování ve službě Azure Kubernetes (AKS)

Při vytváření a správě clusterů ve službě Azure Kubernetes Service (AKS) vaše aplikace často potřebují úložiště. Je důležité pochopit požadavky na výkon a přístupové metody pro lusky, abyste mohli zajistit příslušné úložiště pro aplikace. Velikost uzlu AKS může mít vliv na tyto možnosti úložiště. Měli byste taky naplánovat způsob zálohování a testování procesu obnovení pro připojené úložiště.

Tento článek o osvědčených postupech se zaměřuje na požadavky na úložiště u operátorů clusteru. V tomto článku se naučíte:

> [!div class="checklist"]
> * Jaké typy úložiště jsou k dispozici
> * Postup správného určení velikosti uzlů AKS pro výkon úložiště
> * Rozdíly mezi dynamickým a statickým zřizováním svazků
> * Způsoby zálohování a zabezpečení datových svazků

## <a name="choose-the-appropriate-storage-type"></a>Zvolit vhodný typ úložiště

**Doprovodné materiály k osvědčeným postupům** – pochopení potřeb vaší aplikace k výběru správného úložiště. Pro produkční úlohy Používejte vysoce výkonné úložiště zálohované na jednotku SSD. Naplánujte síťové úložiště, když je potřeba víc souběžných připojení.

Aplikace často vyžadují různé typy a rychlosti úložiště. Potřebují vaše aplikace úložiště, které se připojuje k jednotlivým luskům, nebo se sdílí napříč více lusky? Je úložiště pro přístup k datům jen pro čtení nebo k zápisu velkých objemů strukturovaných dat? Toto úložiště potřebuje k určení nejvhodnějšího typu úložiště, které se má použít.

Následující tabulka popisuje dostupné typy úložišť a jejich možnosti:

| Případ použití | Modul plug-in svazku | Čtení a zápis jednou | Velký počet jen pro čtení | Čtení a zápis mnoha | Podpora kontejneru Windows serveru |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Sdílená konfigurace       | Azure Files   | Ano | Ano | Ano | Ano |
| Data strukturovaných aplikací        | Disky Azure   | Ano | Ne  | Ne  | Ano |
| Nestrukturovaná data, operace systému souborů | [BlobFuse][blobfuse] | Ano | Ano | Ano | Ne |

Dva primární typy úložiště poskytované pro svazky v AKS se zálohují na disky Azure nebo soubory Azure. Pro zvýšení zabezpečení oba typy úložiště ve výchozím nastavení používají šifrování služby Azure Storage (SSE), které šifruje neaktivní neaktivní data. Disky se momentálně nedají šifrovat pomocí Azure Disk Encryption na úrovni uzlu AKS.

Soubory Azure a disky Azure jsou dostupné na úrovních výkonu Standard a Premium:

- Disky *Premium* jsou založené na vysoce výkonných discích Solid-State (SSD). Prémiové disky se doporučují pro všechny produkční úlohy.
- *Standardní* disky jsou zajištěné pravidelnými otáčejícími se disky (HDD) a jsou vhodné pro archivované nebo zřídka používaná data.

Seznámení s požadavky na výkon aplikace a vzory přístupu k výběru vhodné úrovně úložiště. Další informace o velikostech Managed Disks a úrovních výkonu najdete v tématu [Azure Managed disks Overview][managed-disks] .

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Vytvoření a použití tříd úložiště k definování potřeb aplikace

Typ úložiště, který použijete, je definovaný pomocí *tříd úložiště* Kubernetes. Na třídu úložiště se pak odkazuje ve specifikaci pod nebo nasazováním. Tyto definice společně vytvoří vhodné úložiště a připojí je k luskům. Další informace najdete v tématu [třídy úložiště v AKS][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>Velikost uzlů pro potřeby úložiště

**Doprovodné materiály k osvědčeným postupům** – velikost jednotlivých uzlů podporuje maximální počet disků. Různé velikosti uzlů také poskytují různé objemy místního úložiště a šířky pásma sítě. Plánování vaší aplikace požaduje nasazení příslušné velikosti uzlů.

Uzly AKS se spouštějí jako virtuální počítače Azure. K dispozici jsou různé typy a velikosti virtuálního počítače. Každá velikost virtuálního počítače poskytuje různé množství základních prostředků, jako je například procesor a paměť. Tyto velikosti virtuálních počítačů mají maximální počet disků, které je možné připojit. Výkon úložiště se taky mění mezi velikostmi virtuálních počítačů pro maximální místní a připojené diskové IOPS (vstupně-výstupní operace za sekundu).

Pokud vaše aplikace jako řešení úložiště vyžadují disky Azure, naplánujte a vyberte vhodnou velikost virtuálního počítače uzlu. Množství procesoru a paměti není jediným faktorem, když zvolíte velikost virtuálního počítače. Možnosti úložiště jsou také důležité. Mezi například *Standard_B2ms* i *Standard_DS2_v2* velikosti virtuálních počítačů patří podobné množství prostředků procesoru a paměti. Jejich potenciální výkon úložiště je jiný, jak je znázorněno v následující tabulce:

| Typ a velikost uzlu | Virtuální procesory | Paměť (GiB) | Max. datových disků | Maximální počet necachených vstupně-výstupních operací disku | Maximální propustnost při neukládání do mezipaměti (MB/s) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1 920                  | 22.5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6 400                  | 96                             |

V tomto případě *Standard_DS2_v2* umožňuje dvojnásobek počtu připojených disků a poskytuje tři až ČTYŘIKRÁT množství IOPS a propustnosti disku. Pokud jste si prohlédli jenom základní výpočetní prostředky a porovnané náklady, můžete si vybrat velikost virtuálního počítače *Standard_B2ms* a snížit výkon a omezení úložiště. Pracujte s vývojovým týmem vaší aplikace, abyste pochopili kapacitu úložiště a požadavky na výkon. Vyberte odpovídající velikost virtuálního počítače pro uzly AKS, které splňují nebo překračují jejich požadavky na výkon. Pravidelné standardní aplikace pro přizpůsobení velikosti virtuálních počítačů podle potřeby.

Další informace o dostupných velikostech virtuálních počítačů najdete v tématu [velikosti pro virtuální počítače Linux v Azure][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Dynamické zřizování svazků

**Doprovodné materiály k osvědčeným postupům** – ke snížení režijních nákladů na správu a umožňují škálování, nevytvářejte staticky a přiřazujte trvalé svazky. Použijte dynamické zřizování. V třídách úložiště Definujte příslušné zásady uvolnění, aby se minimalizovaly nepotřebné náklady na úložiště, když se odstraní lusky.

Pokud potřebujete připojit úložiště k luskům, použijte trvalé svazky. Tyto trvalé svazky lze vytvořit ručně nebo dynamicky. Ruční vytvoření trvalých svazků přináší režijní náklady na správu a omezí možnost škálování. Pomocí dynamického zřizování svazků můžete zjednodušit správu úložiště a umožníte vašim aplikacím růst a škálování podle potřeby.

![Deklarace trvalých svazků v clusteru Azure Kubernetes Services (AKS)](media/concepts-storage/persistent-volume-claims.png)

Deklarace trvalého objemu (PVC) umožňuje dynamicky vytvářet úložiště podle potřeby. Základní disky Azure se vytvářejí v případě, že si je požádají. V definici pod vyžádáte svazek, který se má vytvořit a připojit k určené cestě připojení.

Koncepce, jak dynamicky vytvářet a používat svazky, najdete v tématu [deklarace trvalých svazků][aks-concepts-storage-pvcs].

Pokud se chcete podívat na tyto svazky v akci, přečtěte si téma postup dynamického vytvoření a použití trvalého svazku s [disky Azure][dynamic-disks] nebo [soubory Azure][dynamic-files].

Jako součást definic vaší třídy úložiště nastavte odpovídající *reclaimPolicy*. Tento reclaimPolicy řídí chování podkladového prostředku služby Azure Storage, když se odstraní pole pod a trvalý svazek už nemusí být potřeba. Základní prostředek úložiště je možné odstranit, případně uchovat pro použití s budoucím pod. ReclaimPolicy může nastavit, aby se *zachoval* nebo *odstranil*. Seznamte se s požadavky vaší aplikace a implementujte pravidelné kontroly úložiště, které jsou zachované, aby se minimalizovalo množství nevyužitého úložiště, které se používá a účtuje.

Další informace o možnostech třídy úložiště najdete v tématu [zásady pro redeklaraci úložiště][reclaim-policy].

## <a name="secure-and-back-up-your-data"></a>Zabezpečení a zálohování dat

**Doprovodné materiály k osvědčeným postupům** – zálohujte data pomocí vhodného nástroje pro typ úložiště, například Velero nebo Azure Backup. Ověřte integritu a zabezpečení těchto záloh.

Pokud vaše aplikace ukládají a spotřebovávají data trvalá na discích nebo v souborech, je třeba provést pravidelná zálohování nebo snímky těchto dat. Disky Azure můžou používat integrované technologie snímků. Před provedením operace snímku možná budete muset vyhledat své aplikace a vyprázdnit zápisy na disk. [Velero][velero] může zálohovat trvalé svazky spolu s dalšími prostředky a konfiguracemi clusteru. Pokud nemůžete [Odebrat stav z vašich aplikací][remove-state], zálohujte data z trvalých svazků a pravidelně otestujte operace obnovení, abyste ověřili integritu dat a požadované procesy.

Seznamte se s omezeními různých přístupů k zálohování dat a v případě, že před snímkem potřebujete data uvést do nečinnosti. Zálohování dat nemusí nutně umožnit obnovení prostředí aplikace pro nasazení clusteru. Další informace o těchto scénářích najdete v tématu [osvědčené postupy pro zajištění kontinuity podnikových procesů a zotavení po havárii v AKS][best-practices-multi-region].

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřuje na osvědčené postupy pro úložiště v AKS. Další informace o základech úložiště v Kubernetes najdete v tématu [Koncepty úložiště pro aplikace v AKS][aks-concepts-storage].

<!-- LINKS - External -->
[velero]: https://github.com/heptio/velero
[blobfuse]: https://github.com/Azure/azure-storage-fuse

<!-- LINKS - Internal -->
[aks-concepts-storage]: concepts-storage.md
[vm-sizes]: ../virtual-machines/sizes.md
[dynamic-disks]: azure-disks-dynamic-pv.md
[dynamic-files]: azure-files-dynamic-pv.md
[reclaim-policy]: concepts-storage.md#storage-classes
[aks-concepts-storage-pvcs]: concepts-storage.md#persistent-volume-claims
[aks-concepts-storage-classes]: concepts-storage.md#storage-classes
[managed-disks]: ../virtual-machines/managed-disks-overview.md
[best-practices-multi-region]: operator-best-practices-multi-region.md
[remove-state]: operator-best-practices-multi-region.md#remove-service-state-from-inside-containers
