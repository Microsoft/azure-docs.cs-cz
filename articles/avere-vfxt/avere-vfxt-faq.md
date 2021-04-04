---
title: Nejčastější dotazy – avere vFXT pro Azure
description: Tyto časté otázky použijte k rozhodnutí, jestli avere vFXT pro Azure vyhovuje vašim potřebám. Přečtěte si, jak avere vFXT pro Azure funguje s dalšími komponentami Azure.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.custom: references_regions
ms.openlocfilehash: 647a9792384a21dfc191371ef444cbdc098bc819
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92342361"
---
# <a name="avere-vfxt-for-azure-faq"></a>Avere vFXT for Azure – nejčastější dotazy

V tomto článku najdete odpovědi na otázky, které vám pomůžou rozhodnout, jestli je avere vFXT pro Azure pro vaše potřeby nejvhodnější. Poskytuje základní informace o avere vFXT a vysvětluje, jak funguje s ostatními komponentami Azure a s produkty od jiných dodavatelů.

## <a name="general"></a>Obecné

### <a name="what-is-avere-vfxt-for-azure"></a>Co je Avere vFXT for Azure?

Avere vFXT for Azure je vysoce výkonný systém souborů, který ukládá do mezipaměti aktivní data v Azure COMPUTE, aby bylo možné efektivně zpracovávat kritické úlohy.

### <a name="is-avere-vfxt-a-storage-solution"></a>Je avere řešení úložiště vFXT?

No. Avere vFXT for Azure je *mezipaměť* systému souborů, která se připojuje k prostředí úložiště, jako je například vaše služba EMC nebo NetApp, nebo kontejner objektů BLOB v Azure. Avere vFXT zjednodušuje požadavky na data od klientů a ukládá do mezipaměti data, která slouží ke zvýšení výkonu ve velkém měřítku a v čase. Avere vFXT sám neukládá data. Neobsahuje žádné informace o množství dat uložených na pozadí.

### <a name="is-avere-vfxt-a-tiering-solution"></a>Je avere vFXT řešení?

Avere vFXT pro Azure neumožňuje automaticky vyvrstvit data mezi horkou a studenou úrovní.  

### <a name="how-do-i-know-if-an-environment-is-right-for-avere-vfxt"></a>Návody zjistit, jestli je prostředí pro avere vFXT správné?

Nejlepším způsobem, jak se na tuto otázku představit, je požádat o to, aby zatížení bylo možné ukládat do mezipaměti? To znamená, že zatížení má vysoký poměr pro čtení a zápis? Příkladem je 80/20 nebo 70/30 čtení/zápisu.

Zvažte avere vFXT pro Azure, pokud máte analytický kanál založený na souborech, který běží na velkém počtu virtuálních počítačů Azure, a splňuje jednu nebo víc z těchto podmínek:

* Celkový výkon je pomalý nebo nekonzistentní z důvodu dlouhé doby přístupu k souboru (desítky nebo sekundy v závislosti na požadavcích). Tato latence není pro zákazníka přijatelná.

* Data potřebná ke zpracování jsou umístěna na konci prostředí sítě WAN a tato data jsou trvale přesunuta. Data můžou být v jiné oblasti Azure nebo v datovém centru zákazníka.

* Velký počet klientů požaduje data, například v clusteru HPC (High Performance Computing). Velký počet souběžných požadavků může zvýšit latenci.

* Zákazník chce spustit svůj aktuální kanál "tak, jak je" ve virtuálních počítačích Azure a potřebuje k škálovatelnosti řešení sdíleného úložiště (nebo ukládání do mezipaměti) založené na POSIX. Pomocí avere vFXT pro Azure nemusíte měnit architekt pracovního kanálu, abyste mohli nativně volat do úložiště objektů BLOB v Azure.

* Vaše aplikace HPC je založená na klientech NFSv3. (V některých případech může použít klienty SMB 2,1, ale výkon je omezený.)

Následující diagram vám může při odpovědi na tuto otázku odpovědět. Výše je váš pracovní postup v pravém horním rohu, což je pravděpodobnější, že řešení avere vFXT pro ukládání do mezipaměti Azure je pro vaše prostředí nejvhodnější.

![Diagram grafu znázorňující, že se pro avere vFXT lépe hodí zátěž pro čtení s tisíci klienty.](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>V jakém měřítku klientů má řešení avere vFXT smysl?

Řešení avere vFXT cache je postavené na zpracování stovek, tisíců nebo desítek tisíců výpočetních jader. Pokud máte několik počítačů běžících na lehké práci, avere vFXT není to správné řešení.

Běžní zákazníci avere vFXT spouštějí náročné úlohy od přibližně 1 000 jader procesoru. Tato prostředí můžou být velká až 50 000 jader nebo víc. Vzhledem k tomu, že je avere vFXT škálovatelný, můžete přidat uzly, které budou podporovat tyto úlohy, když se zvyšují tak, aby vyžadovaly vyšší propustnost nebo více IOPS.

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Kolik dat může avere úložiště prostředí vFXT?

Avere vFXT pro Azure je mezipaměť. Neukládá konkrétně data. Používá kombinaci paměti RAM a SSD k ukládání dat uložených v mezipaměti. Data se trvale ukládají do back-endového systému úložiště (například do systému NetApp NAS nebo kontejneru objektů BLOB). Systém avere vFXT neobsahuje informace o množství dat uložených za ním. Avere vFXT ukládá do mezipaměti pouze podmnožinu dat, kterou si klienti vyžádají.  

### <a name="what-regions-are-supported"></a>Které oblasti jsou podporovány?

Avere vFXT pro Azure se podporuje ve všech oblastech s výjimkou oblastí svrchovan (Čína, Německo). Ujistěte se, že oblast, kterou chcete použít, může podporovat velké množství výpočetních jader a instancí virtuálních počítačů potřebných k vytvoření clusteru avere vFXT.

### <a name="how-do-i-get-help-with-avere-vfxt"></a>Návody získat pomoc s vFXTem pro avere?

Specializovaná skupina pracovníků podpory nabízí pomoc s avere vFXT pro Azure. Podle pokynů v [tématu Získejte pomoc s vaším systémem](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) a otevřete z Azure Portal lístek podpory.

### <a name="is-avere-vfxt-highly-available"></a>Je avere vFXT vysoce dostupný?

Ano, avere vFXT se spouští výhradně jako řešení HA.

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>Podporuje avere vFXT pro Azure také další cloudové služby?

Ano, zákazníci můžou použít více než jednoho poskytovatele cloudu s clusterem avere vFXT. Podporuje standardní bloky AWS S3, zásobníky Google Cloud Services Standard a kontejnery objektů blob Azure.

> [!NOTE]
> Poplatek za software se vztahuje na použití avere vFXT s AWS nebo Google Cloud Storage. Za použití služby Azure Blob Storage se neúčtují žádné další poplatky za software.

## <a name="technical-compute"></a>Technický: COMPUTE

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>Můžete popište, co avere prostředí vFXT "vypadá jako"?

Avere vFXT je clusterované zařízení, které se skládá z několika virtuálních počítačů Azure. Knihovna Pythonu zpracovává vytváření, odstraňování a úpravy clusteru. Přečtěte si, [co je avere vFXT pro Azure?](avere-vfxt-overview.md) Další informace najdete v tématu.

### <a name="what-kind-of-azure-virtual-machines-does-avere-vfxt-run-on"></a>Na jaký druh virtuálních počítačů Azure se avere vFXT spouští?  

Avere vFXT pro cluster Azure používá virtuální počítače E32s_v3 Microsoft Azure.

<!-- ### Can I mix and match virtual machine types for my cluster?

No, you must choose one virtual machine type or the other.
    
### Can I move between virtual machine types?

Yes, there is a migration path to move from one VM type to the other. [Open a support ticket](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) to learn how.
-->

### <a name="does-the-avere-vfxt-environment-scale"></a>Je prostředí avere vFXT škálovat?

Cluster avere vFXT může být malý jako tři uzly virtuálních počítačů nebo jako 24 uzlů. Pokud se domníváte, že budete potřebovat cluster více než devět uzlů, kontaktujte technickou podporu Azure pro pomoc s plánováním. Větší počet uzlů vyžaduje větší architekturu nasazení.

### <a name="does-the-avere-vfxt-environment-autoscale"></a>Je prostředí avere vFXT "AutoScale"?

No. Velikost clusteru můžete škálovat nahoru a dolů, ale přidávání nebo odebírání uzlů clusteru je ruční krok.

### <a name="can-i-run-the-avere-vfxt-cluster-as-a-virtual-machine-scale-set"></a>Můžu cluster avere vFXT spustit jako sadu škálování virtuálního počítače?

Avere vFXT nepodporuje nasazení sady škálování virtuálního počítače. Několik integrovaných mechanismů podpory dostupnosti je navrženo pouze pro atomické virtuální počítače účastnící se clusteru.  

### <a name="can-i-run-the-avere-vfxt-cluster-on-low-priority-vms"></a>Můžu cluster avere vFXT spustit na virtuálních počítačích s nízkou prioritou?

Ne, systém vyžaduje základní stabilní sadu virtuálních počítačů.

### <a name="can-i-run-the-avere-vfxt-cluster-in-containers"></a>Můžu cluster avere vFXT spustit v kontejnerech?

Ne, avere vFXT musí být nasazené jako nezávislá aplikace.

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>Počítá se počet virtuálních počítačů avere vFXT na moji kvótu COMPUTE?

Ano. Ujistěte se, že v oblasti pro podporu clusteru máte dostatečnou kvótu.  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>Můžu spouštět počítače s clustery avere vFXT v různých zónách dostupnosti?

No. Model vysoké dostupnosti v avere vFXT v současné době nepodporuje jednotlivé členy clusteru avere vFXT nacházející se v různých zónách dostupnosti.

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>Můžu klonovat virtuální počítače s avere vFXT?

Ne, pokud chcete přidat nebo odebrat uzly v clusteru avere vFXT, musíte použít podporovaný skript Pythonu. Další informace najdete v článku [Správa clusteru avere vFXT](avere-vfxt-manage-cluster.md).

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>Je k dispozici "virtuální" cloudová verze softwaru, kterou můžu spustit v mém vlastním místním prostředí?

Ne, systém se nabídne jako clusterované zařízení a testuje se na určitých typech virtuálních počítačů. Toto omezení pomáhá zákazníkům vyhnout se vytváření systému, který nedokáže podporovat vysoce výkonné požadavky typického pracovního postupu avere vFXT.

## <a name="technical-disks"></a>Technické: disky

### <a name="what-types-of-disks-are-supported-for-the-azure-vms"></a>Jaké typy disků se podporují pro virtuální počítače Azure?

Avere vFXT for Azure může používat 1 TB nebo 4 TB konfigurace SSD. Konfiguraci Premium SSD můžete nasadit jako několik spravovaných disků.

### <a name="does-the-cluster-support-unmanaged-disks"></a>Podporuje cluster nespravované disky?

Ne, cluster vyžaduje spravované disky.

### <a name="does-the-system-support-local-attached-ssds"></a>Podporuje systém místní (připojená) SSD?

Avere vFXT pro Azure v současné době nepodporuje místní SSD. Disky používané pro avere vFXT musí být schopné vypnout a restartovat, ale místní připojené SSD v této konfiguraci je možné ukončit jenom.

### <a name="does-the-system-support-ultra-ssds"></a>Podporuje systém Ultra SSD?

Ne, systém podporuje jenom konfigurace SSD úrovně Premium.

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>Můžu oddělit prémiové SSD a později je znovu připojit, abyste zachovali obsah mezipaměti mezi použitím?

Odpojení a opětovné připojení SSD se nepodporuje. Metadata nebo obsah souboru ve zdroji se možná změnily mezi použitím, což může způsobit problémy s integritou dat.

### <a name="does-the-system-encrypt-the-cache"></a>Šifruje systém mezipaměť?

Data jsou rozložená na disky, ale nejsou šifrovaná. Samotné disky však mohou být zašifrovány. Další informace najdete v tématu [zabezpečení a používání zásad na virtuálních počítačích v Azure](../virtual-machines/security-policy.md#encryption).

## <a name="technical-networking"></a>Technické: sítě

### <a name="what-network-is-recommended"></a>Jaká síť se doporučuje?

Pokud používáte místní úložiště s avere vFXT, měli byste mít k dispozici síťové připojení 1 GB/s nebo vyšší mezi vaším úložištěm a clusterem. Pokud máte malé množství dat a jste ochotni kopírovat data do cloudu před spuštěním úloh, může stačit připojení k síti VPN.

> [!TIP]
> Pomaleji je síťové propojení, což povede ke zpomalení počátečního "studeného" čtení. Pomalé čtení zvyšují latenci pracovního kanálu.

### <a name="can-i-run-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>Můžu spustit avere vFXT v jiné virtuální síti než ve výpočetním clusteru?

Ano, avere systém vFXT můžete vytvořit v jiné virtuální síti. Podrobnosti najdete v tématu [Plánování systému avere vFXT](avere-vfxt-deploy-plan.md) .

### <a name="does-avere-vfxt-require-its-own-subnet"></a>Vyžaduje avere vFXT svou vlastní podsíť?

Ano. Avere vFXT běží výhradně jako cluster s vysokou dostupností (HA) a vyžaduje více IP adres pro provoz. Pokud je cluster ve vlastní podsíti, vyhnete se konfliktům IP adres, což může způsobit problémy při instalaci a normálním provozu. Podsíť clusteru může být ve virtuální síti používané jinými prostředky, pokud se nepřekrývají žádné IP adresy.

### <a name="can-i-run-avere-vfxt-on-infiniband"></a>Můžu spustit avere vFXT na InfiniBand?

Ne, avere vFXT používá pouze Ethernet/IP adresu.

### <a name="how-do-i-access-my-on-premises-nas-environment-from-avere-vfxt"></a>Návody přístup k místnímu prostředí NAS z avere vFXT?

Prostředí avere vFXT se podobá jakémukoli jinému virtuálnímu počítači Azure v tom, že vyžaduje směrování přístupu prostřednictvím síťové brány nebo sítě VPN do datového centra zákazníka (a zpátky). Pokud je k dispozici ve vašem prostředí, zvažte použití možnosti připojení Azure ExpressRoute.

### <a name="what-are-the-bandwidth-requirements-for-avere-vfxt"></a>Jaké jsou požadavky na šířku pásma pro avere vFXT?

Celkový požadavek na šířku pásma závisí na dvou faktorech:

* Množství dat, které se požaduje ze zdroje
* Tolerance pro latenci klientského systému při počátečním načítání dat  

V prostředích, která jsou citlivá na latenci, byste měli použít optické řešení s minimální rychlostí připojení 1 GB/s. Použijte ExpressRoute, pokud je k dispozici.  

### <a name="can-i-run-avere-vfxt-with-public-ip-addresses"></a>Můžu spustit avere vFXT s veřejnými IP adresami?

Ne, avere vFXT by se měla provozovat v síťovém prostředí zabezpečeném pomocí osvědčených postupů.

### <a name="can-i-restrict-internet-access-from-my-clusters-virtual-network"></a>Můžu omezit přístup k Internetu z virtuální sítě v clusteru?

Obecně platí, že v rámci své virtuální sítě můžete podle potřeby nakonfigurovat další zabezpečení, některá omezení ale můžou narušit činnost clusteru.

Například omezení odchozího internetového přístupu z vaší virtuální sítě způsobí problémy s clusterem, pokud nepřidáte pravidlo, které explicitně umožní přístup k AzureCloud. Tato situace je popsaná v [doplňkové dokumentaci k GitHubu](https://github.com/Azure/Avere/tree/master/src/vfxt/internet_access.md).

Pokud chcete získat pomoc s přizpůsobeným zabezpečením, obraťte se na podporu, jak je popsáno v [tématu Získání pomoci se systémem](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt)

## <a name="technical-back-end-storage-core-filers"></a>Technický: back-end úložiště (základní filers)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>Kolik základních filers podporuje jedno prostředí avere vFXT?

Cluster avere vFXT podporuje až 20 základních filers.

### <a name="how-does-the-avere-vfxt-environment-store-data"></a>Jak se v prostředí avere vFXT ukládají data?

Avere vFXT není úložiště. Je to mezipaměť, která čte a zapisuje data z více cílů úložiště s názvem Core filers. Data uložená na discích SSD úrovně Premium v avere vFXT jsou přechodný a nakonec se vyprázdní pro základní souborového úložiště back-endu.

### <a name="which-core-filers-does-avere-vfxt-support"></a>Jakou základní filers podporuje avere vFXT?

Obecně platí, že avere vFXT pro Azure podporuje následující systémy jako základní filers:

* Dell EMC Isilon (OneFS 7,1, 7,2, 8,0 a 8,1) 
* NetApp ONTAP (clusterový režim 9,4, 9,3, 9,2, 9.1 P1, 8.0-8.3) a (7. režim 7. *, 8.0-8.3)

* Kontejnery objektů BLOB v Azure (jenom místně redundantní úložiště)
* Bloky AWS S3
* Google Cloud – sady

### <a name="why-doesnt-avere-vfxt-support-all-nfs-filers"></a>Proč avere vFXT podporuje všechny filers systému souborů NFS?

I když všechny platformy systému souborů NFS splňují stejné standardy IETF, v praxi Každá implementace má vlastní adaptivní. Tyto podrobnosti mají vliv na to, jak avere vFXT komunikuje s úložným systémem. Podporované systémy jsou nejčastěji používanými platformami na webu Marketplace.

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>Podporuje avere vFXT úložiště privátních objektů (například SwiftStack)?

Avere vFXT nepodporuje úložiště privátních objektů.

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>Jak získám podporu konkrétní produkt úložiště?

Podpora je založena na množství poptávky v poli. Pokud je pro podporu řešení NAS k dispozici dostatek požadavků na výnosy, budeme zvážit. Podávat požadavky prostřednictvím podpory Azure.

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>Můžu používat úložiště objektů BLOB v Azure jako základní souborového?

Ano, avere vFXT pro Azure může použít kontejner objektů blob bloku jako Cloud Core souborového.

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Jaké jsou požadavky na účet úložiště pro objekt BLOB Core souborového?

Váš účet úložiště musí být účet pro obecné účely v2 (GPv2) a nakonfigurovaný jenom pro místně redundantní úložiště. Geograficky redundantní úložiště a redundantní úložiště v zóně se nepodporují.

Další podrobnosti o požadavcích na účet úložiště najdete v tématu [Azure Blob Storage Cloud Core souborového](avere-vfxt-add-storage.md#azure-blob-storage-cloud-core-filer) .

### <a name="can-i-use-archive-blob-storage"></a>Můžu použít archivní úložiště objektů BLOB?

No. Smlouva o úrovni služeb (SLA) pro úložiště archivu není kompatibilní s adresářem v reálném čase a potřebou přístupu k souborům v systému avere vFXT.

### <a name="can-i-use-cool-blob-storage"></a>Můžu použít studené úložiště objektů BLOB?

Pro avere vFXT pro Azure Core souborového se obvykle nedoporučuje používat úložiště objektů BLOB ve studené vrstvě. Studená úroveň nabízí nižší náklady na úložiště, ale vyšší provozní náklady. (Další podrobnosti najdete v tématu ceny za objekty [blob bloku](<https://azure.microsoft.com/pricing/details/storage/blobs/>) .) Pokud budou data k dispozici a upravována nebo odstraněna často, zvažte použití horké úrovně.

[Úrovně přístupu](../storage/blobs/storage-blob-storage-tiers.md#cool-access-tier) poskytují další informace o tom, kdy může být vhodné používat jako vFXT Core Storage úložiště s chladnou vrstvou.

### <a name="how-do-i-encrypt-the-blob-container"></a>Návody zašifrovat kontejner objektů BLOB?

Šifrování objektů blob můžete nakonfigurovat buď v Azure (upřednostňovaná), nebo na úrovni avere vFXT Core souborového.  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>Můžu pro objekt BLOB Core souborového použít vlastní šifrovací klíč?

Ve výchozím nastavení se data šifrují pomocí klíčů spravovaných Microsoftem pro úložiště objektů blob, tabulek a front Azure a také souborů Azure. Můžete využít vlastní klíč k šifrování pro úložiště objektů BLOB a soubory Azure. Pokud se rozhodnete použít šifrování vFXT avere, musíte použít klíč generovaný avere a uložit ho místně.

## <a name="purchasing"></a>Nákup

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Návody získat licencování avere vFXT pro Azure?

Získání licence avere vFXT pro Azure je snadno Azure Marketplace. Zaregistrujte si účet Azure a pak postupujte podle pokynů v tématu [nasazení clusteru avere vFXT](avere-vfxt-deploy.md) a vytvořte cluster avere vFXT.

### <a name="how-much-does-avere-vfxt-cost"></a>Kolik stojí avere vFXT?

V Azure se pro používání clusterů avere vFXT neúčtují žádné další licenční poplatky. Zákazníci zodpovídají za úložiště a další poplatky za využití Azure.

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>Můžou být virtuální počítače s avere vFXT spuštěné s nízkou prioritou?

Ne, clustery avere vFXT vyžadují službu Always On. Clustery je možné vypnout, pokud není potřeba.

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s avere vFXT pro Azure, přečtěte si tyto články, abyste se dozvěděli, jak naplánovat a nasadit vlastní systém:

* [Plánování systému Avere vFXT](avere-vfxt-deploy-plan.md)
* [Přehled nasazení](avere-vfxt-deploy-overview.md)
* [Příprava na vytvoření clusteru avere vFXT](avere-vfxt-prereqs.md)
* [Nasazení clusteru Avere vFXT](avere-vfxt-deploy.md)

Další informace o možnostech a případech použití pro avere vFXT najdete v [avere vFXT pro Azure](https://azure.microsoft.com/services/storage/avere-vfxt/).