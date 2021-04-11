---
title: Osvědčené postupy pro úložiště a zálohování
titleSuffix: Azure Kubernetes Service
description: Seznamte se s osvědčenými postupy pro úložiště, šifrování dat a zálohy ve službě Azure Kubernetes Service (AKS) od operátora clusteru.
services: container-service
ms.topic: conceptual
ms.date: 03/10/2021
ms.openlocfilehash: 9b3ee6fd7eea958a573743b21bf8940458e2a965
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104911"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro úložiště a zálohování ve službě Azure Kubernetes (AKS)

Při vytváření a správě clusterů ve službě Azure Kubernetes Service (AKS) vaše aplikace často potřebují úložiště. Ujistěte se, že rozumíte požadavkům na výkon a přístup k metodám, abyste mohli vybrat nejvhodnější úložiště pro vaši aplikaci. Velikost uzlu AKS může mít vliv na možnosti úložiště. Naplánujte způsob zálohování a testování procesu obnovení pro připojené úložiště.

Tento článek o osvědčených postupech se zaměřuje na požadavky na úložiště u operátorů clusteru. V tomto článku se naučíte:

> [!div class="checklist"]
> * Jaké typy úložiště jsou k dispozici.
> * Postup správného určení velikosti uzlů AKS pro výkon úložiště.
> * Rozdíly mezi dynamickým a statickým zřizováním svazků.
> * Způsoby zálohování a zabezpečení datových svazků.

## <a name="choose-the-appropriate-storage-type"></a>Zvolit vhodný typ úložiště

> **Osvědčené postupy**
> 
> Pochopení potřeb vaší aplikace pro výběr správného úložiště. Pro produkční úlohy Používejte vysoce výkonné úložiště zálohované na jednotku SSD. Pokud potřebujete víc souběžných připojení, naplánujte úložiště založené na síti.

Aplikace často vyžadují různé typy a rychlosti úložiště. Požádejte o následující otázky a určete nejvhodnější typ úložiště. 
* Potřebují vaše aplikace úložiště, které se připojuje k jednotlivým luskům?
* Potřebují vaše aplikace sdílení úložiště napříč více lusky? 
* Je úložiště pro přístup jen pro čtení k datům?
* Bude se úložiště používat k zápisu velkých objemů strukturovaných dat? 

Následující tabulka popisuje dostupné typy úložišť a jejich možnosti:

| Případ použití | Modul plug-in svazku | Čtení a zápis jednou | Velký počet jen pro čtení | Čtení a zápis mnoha | Podpora kontejneru Windows serveru |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Sdílená konfigurace       | Azure Files   | Yes | Yes | Yes | Yes |
| Data strukturovaných aplikací        | Disky Azure   | Yes | No  | No  | Yes |
| Nestrukturovaná data, operace systému souborů | [BlobFuse][blobfuse] | Yes | Yes | Yes | No |

AKS poskytuje dva primární typy zabezpečeného úložiště pro svazky, které jsou na discích Azure nebo soubory Azure. Používají se výchozí šifrování služby Azure Storage (SSE), které šifruje neaktivní neaktivní data. Disky nelze pomocí Azure Disk Encryption na úrovni uzlu AKS šifrovat.

Soubory Azure a disky Azure jsou dostupné na úrovních výkonu Standard a Premium:

- *Prémiové* disky
    - Zajištěné vysokým výkonem disků Solid-State (SSD). 
    - Doporučuje se pro všechny provozní úlohy.
- Disky *úrovně Standard*
    - Zajištěné pravidelnými otáčejícími se disky (HDD).
    - Vhodné pro archivované nebo zřídka používaná data.

Seznámení s požadavky na výkon aplikace a vzory přístupu k výběru vhodné úrovně úložiště. Další informace o velikostech Managed Disks a úrovních výkonu najdete v tématu [Azure Managed disks Overview][managed-disks] .

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Vytvoření a použití tříd úložiště k definování potřeb aplikace

Definujte typ úložiště, které chcete použít pro *třídy úložiště* Kubernetes. Na třídu úložiště se pak odkazuje ve specifikaci pod nebo nasazováním. Definice tříd úložiště společně spolupracují, aby vytvořily příslušné úložiště a připojili ho k luskům. 

Další informace najdete v tématu [třídy úložiště v AKS][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>Velikost uzlů pro potřeby úložiště

> **Osvědčené postupy**
> 
> Velikost každého uzlu podporuje maximální počet disků. Různé velikosti uzlů také poskytují různé objemy místního úložiště a šířky pásma sítě. Pro účely nasazení správné velikosti uzlů je vhodné plán vhodně naplánovat.

Uzly AKS se spouštějí jako různé typy a velikosti virtuálních počítačů Azure. Každá velikost virtuálního počítače poskytuje:
* Jiné množství základních prostředků, jako je například procesor a paměť. 
* Maximální počet disků, které lze připojit. 

Výkon úložiště se taky mění mezi velikostmi virtuálních počítačů pro maximální místní a připojené diskové IOPS (vstupně-výstupní operace za sekundu).

Pokud aplikace vyžaduje jako své řešení úložiště disky Azure, strategize velikost virtuálního počítače pro příslušné uzly. Funkce úložiště a objemy procesoru a paměti hrají důležitou roli při rozhodování o velikosti virtuálního počítače. 

Například Přestože velikosti virtuálních počítačů *Standard_B2ms* i *Standard_DS2_v2* zahrnují podobné množství prostředků procesoru a paměti, jejich potenciální výkon úložiště se liší:

| Typ a velikost uzlu | Virtuální procesory | Paměť (GiB) | Max. datových disků | Maximální počet necachených vstupně-výstupních operací disku | Maximální propustnost při neukládání do mezipaměti (MB/s) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1 920                  | 22.5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6 400                  | 96                             |

V tomto příkladu *Standard_DS2_v2* nabízí dvakrát tolik připojených disků a tři až čtyřikrát za sekundu množství vstupně-výstupních operací a propustnosti disku. Pokud jste porovnali jenom základní výpočetní prostředky a porovnané náklady, možná jste vybrali velikost virtuálního počítače *Standard_B2ms* s nízkým výkonem a omezeními úložiště. 

Pracujte s vývojovým týmem vaší aplikace, abyste pochopili kapacitu úložiště a požadavky na výkon. Vyberte odpovídající velikost virtuálního počítače pro uzly AKS, které splňují nebo překračují jejich požadavky na výkon. Pravidelné standardní aplikace pro přizpůsobení velikosti virtuálních počítačů podle potřeby.

Další informace o dostupných velikostech virtuálních počítačů najdete v tématu [velikosti pro virtuální počítače Linux v Azure][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Dynamické zřizování svazků

> **Osvědčené postupy** 
>
> Chcete-li snížit režijní náklady na správu a povolit škálování, vyhněte se statickému vytváření a přiřazování trvalých svazků. Použijte dynamické zřizování. V třídách úložiště Definujte příslušné zásady uvolnění, aby se minimalizovaly nepotřebné náklady na úložiště, když se odstraní lusky.

K připojení úložiště k luskům použijte trvalé svazky. Trvalé svazky lze vytvořit ručně nebo dynamicky. Ruční vytvořením trvalých svazků přidáte režijní náklady na správu a omezíte možnosti škálování. Místo toho je možné dynamicky zřídit trvalý svazek a zjednodušit tak správu úložiště a umožní vašim aplikacím růst a škálování podle potřeby.

![Deklarace trvalých svazků v clusteru Azure Kubernetes Services (AKS)](media/concepts-storage/persistent-volume-claims.png)

Deklarace trvalého objemu (PVC) umožňuje dynamicky vytvářet úložiště podle potřeby. Základní disky Azure se vytvářejí v případě, že si je požádají. V definici pod požádejte o vytvoření svazku, který se připojí k určené cestě připojení.

Koncepce, jak dynamicky vytvářet a používat svazky, najdete v tématu [deklarace trvalých svazků][aks-concepts-storage-pvcs].

Pokud se chcete podívat na tyto svazky v akci, přečtěte si téma postup dynamického vytvoření a použití trvalého svazku s [disky Azure][dynamic-disks] nebo [soubory Azure][dynamic-files].

Jako součást definic vaší třídy úložiště nastavte odpovídající *reclaimPolicy*. Tento reclaimPolicy řídí chování základního prostředku úložiště Azure při odstranění seznamu pod. Základní prostředek úložiště lze buď odstranit, nebo uchovat pro budoucí použití pod. Nastavte reclaimPolicy na hodnotu *uchovat* nebo *Odstranit*. 

Porozumět potřebám vaší aplikace a implementujte pravidelné kontroly uchovávaného úložiště, abyste minimalizovali množství nevyužitého a fakturovaného úložiště.

Další informace o možnostech třídy úložiště najdete v tématu [zásady pro redeklaraci úložiště][reclaim-policy].

## <a name="secure-and-back-up-your-data"></a>Zabezpečení a zálohování dat

> **Osvědčené postupy** 
> 
> Data zálohujte pomocí vhodného nástroje pro typ úložiště, například Velero nebo Azure Backup. Ověřte integritu a zabezpečení těchto záloh.

Pokud vaše aplikace ukládají a spotřebovávají data trvalá na discích nebo v souborech, je třeba provést pravidelná zálohování nebo snímky těchto dat. Disky Azure můžou používat integrované technologie snímků. Před provedením operace snímku může vaše aplikace potřebovat vyprázdnit zápisy na disk. [Velero][velero] může zálohovat trvalé svazky spolu s dalšími prostředky a konfiguracemi clusteru. Pokud nemůžete [Odebrat stav z vašich aplikací][remove-state], zálohujte data z trvalých svazků a pravidelně otestujte operace obnovení, abyste ověřili integritu dat a požadované procesy.

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
