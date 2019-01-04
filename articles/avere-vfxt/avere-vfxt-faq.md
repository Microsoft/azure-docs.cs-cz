---
title: Časté otázky – Avere vFXT pro Azure
description: Nejčastější dotazy ohledně Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: dbd9eaf531dcad230c23d1b41110036102faf3df
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2018
ms.locfileid: "53652660"
---
# <a name="avere-vfxt-for-azure-faq"></a>Avere vFXT pro Azure – nejčastější dotazy

Tento článek obsahuje odpovědi na otázky, které vám můžou pomoct rozhodnout, jestli je vhodná pro vaše potřeby Avere vFXT pro Azure. Poskytuje základní informace o Avere vFXT a vysvětluje, jak to funguje s dalšími komponentami Azure a produkty od jiných dodavatelů. 

## <a name="general"></a>Obecné 

### <a name="what-is-avere-vfxt-for-azure"></a>Co je Avere vFXT pro Azure?

Avere vFXT pro Azure je systém souborů vysoce výkonné, který ukládá do mezipaměti aktivní data v Azure compute, aby umožňovala efektivní zpracování důležité úlohy.

### <a name="is-avere-vfxt-a-storage-solution"></a>Je Avere vFXT řešení úložiště?

Ne. Systém souborů je Avere vFXT *mezipaměti* , připojí se k prostředí, úložiště, jako je vaše EMC nebo NetApp NAS nebo kontejneru objektů blob v Azure. Avere vFXT zjednodušuje dat přicházejících od klientů a ukládá data, která slouží ke zlepšení výkonu ve velkém měřítku a v čase. Avere vFXT samotný neukládá data. Nemá žádné informace o objemu dat uložených za ní.

### <a name="is-avere-vfxt-a-tiering-solution"></a>Je Avere vFXT vrstvení řešení?

Avere vFXT dělá automaticky úroveň dat mezi horkou a studenou úrovní.  

### <a name="how-do-i-know-if-an-environment-is-right-for-avere-vfxt"></a>Jak poznám, jestli je vhodná pro Avere vFXT prostředí?

Nejlepší způsob, jak uvažovat o tuto otázku, je zeptat, "Je možné ukládat do mezipaměti pracovního vytížení?" To znamená, že zatížení má vysoký poměr čtení a zápis? Příkladem je 80/20 nebo 70/30 čtení/zápisu.

Pokud máte souborové analytického kanálu, na kterém běží ve velkém počtu virtuálních počítačů Azure a splňuje jeden nebo více z následujících podmínek, zvažte Avere vFXT pro Azure:

* Celkově je pomalý nebo nekonzistentní z důvodu dlouhé časy přístupu (desítky milisekund nebo sekund v závislosti na požadavcích). Latence klesne na nepřijatelnou zákazníkovi.

* Data potřebná pro zpracování se nachází na konci úplně prostředí sítě WAN a přesun dat trvale je nepraktické. Data může být v jiné oblasti Azure nebo v datacentru zákazníka.

* Velký počet klientů žádáte dat – například v clusteru s vysokovýkonným výpočetním prostředím (HPC). Velký počet souběžných požadavků můžete zvýšit latenci.

* Zákazník chce spouštět své aktuální kanál "tak, jak jsou" ve službě Azure virtual machines a potřeby POSIX základě sdíleného úložiště (nebo ukládání do mezipaměti) řešení pro zajištění škálovatelnosti. S použitím Avere vFXT pro Azure, nemusíte úprava architektury pracovních kanálu pro nativní volání do úložiště objektů Blob v Azure.

* Prostředí HPC aplikace je založená na NFSv3 klientů. (V některých případech může používat klienti SMB 2.1, ale výkon je omezený.)

Následující diagram zjednodušuje odpověď na tuto otázku. Čím blíž pracovního postupu je vpravo nahoře, tím je pravděpodobnější, že Avere řešení ukládání do mezipaměti je nejvhodnější pro vaše prostředí.

![Diagram znázorňující, že čtení náročná na výkon při načítání tisíců klientů jsou lepší vhodné pro Avere vFXT](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>Na jaké škálování klientů nemá Avere vFXT řešení smysl nejvíce?

Řešení Avere vFXT mezipaměti je určený pro zpracování stovky, tisíce nebo desítek tisíců výpočetních jader. Pokud máte několik počítače, které běží lehké práce, není Avere vFXT má to pravé řešení.

Typické Avere vFXT zákazníci spouštět náročných úloh, počínaje přibližně 1 000 jader procesoru. Tato prostředí můžou být velké až 50 000 jader nebo více. Protože Avere vFXT je škálovatelná, můžete přidat uzly pro podporu těchto úloh s růstem tak, aby vyžadovala větší propustnost nebo Další vstupně-výstupních operací.

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Kolik dat můžete ukládat prostředí vFXT Avere?

Mezipaměť je Avere vFXT. To není konkrétně data ukládat. Kombinace paměti RAM a vlastnosti jednotek SSD používá k ukládání dat uložených v mezipaměti. Data se trvale uložena v back endové úložné systému (například NetApp NAS systému nebo kontejner objektů blob). Systém vFXT Avere nemá informace o objemu dat uložených za ní. Avere vFXT ukládá do mezipaměti pouze podmnožinu dat, který klienti požadují.  

### <a name="what-regions-are-supported"></a>Jaké oblasti jsou podporovány?

Od 1. listopadu 2018 se podporuje Avere vFXT pro Azure ve všech oblastech kromě suverénních oblastech (Čína, Německo) a oblasti státní správy. Ujistěte se, že oblast, kterou chcete použít může podporovat velký počet výpočetních jader a instancí virtuálních počítačů, které jsou potřeba k vytvoření clusteru vFXT Avere.

### <a name="how-do-i-get-help-with-avere-vfxt"></a>Jak získat pomoc s Avere vFXT?

Skupina specializované podpory nabízí pomoc s Avere vFXT pro Azure. Postupujte podle pokynů v [získat nápovědu k systému](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) otevřete lístek podpory na webu Azure Portal. 

### <a name="is-avere-vfxt-highly-available"></a>Je Avere vFXT s vysokou dostupností?

Ano, Avere vFXT spouští výhradně jako řešení s vysokou DOSTUPNOSTÍ.

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>Avere vFXT pro Azure také podporuje jinými cloudovými službami?

Ano, zákazníci mohou pomocí více než jednoho poskytovatele cloudu s clusterem vFXT Avere. Podporuje kontejnery standardní AWS S3, standardní kontejnerů Google Cloud Services a kontejnery objektů blob v Azure. 

> [!NOTE] 
> Poplatek za software platí pro použití Avere vFXT v AWS a Google Cloud, ale ne s Azure.

## <a name="technical-compute"></a>Technické: Compute

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>Můžete je popisují, jaké Avere vFXT prostředí "vypadá jako"?

Avere vFXT je clusterovaný zařízení z několika virtuálních počítačů Azure. Knihovna Python zpracovává clusteru vytváření, odstraňování a změny. Čtení [novinky Avere vFXT pro Azure?](avere-vfxt-overview.md) Další informace. 

### <a name="what-kind-of-azure-virtual-machines-does-avere-vfxt-run-on"></a>Jaké virtuální počítače Azure Avere vFXT ke spuštění?  

Avere vFXT pro Azure cluster používá Microsoft Azure E32s_v3 nebo D16s_v3 virtuálních počítačů. 

### <a name="can-i-mix-and-match-virtual-machine-types-for-my-cluster"></a>Můžete kombinovat a párovat typy virtuálních počítačů pro cluster?

Ne, musíte zvolit jeden typ virtuálního počítače z nich.
    
### <a name="can-i-move-between-virtual-machine-types"></a>Můžu přesunout mezi typy virtuálních počítačů?

Ano, je migrační přesunout z jednoho typu virtuálního počítače na druhý. [Vytvořit lístek podpory](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) další jak.

### <a name="does-the-avere-vfxt-environment-scale"></a>Škálování prostředí vFXT Avere?

Avere vFXT clusteru může být malá jako tři uzly virtuálních počítačů nebo velké až 24 uzly. Obraťte se na technickou podporu Azure o pomoc s plánováním, pokud se domníváte, že potřebujete více než devíti uzlů clusteru. Větší počet uzlů vyžaduje větší architektury nasazení.

### <a name="does-the-avere-vfxt-environment-autoscale"></a>Podporuje prostředí vFXT Avere "Automatické škálování"?

Ne. Velikost clusteru můžete škálovat nahoru a dolů, ale přidání nebo odebrání uzlů clusteru je provedení ručního kroku.

### <a name="can-i-run-the-avere-vfxt-cluster-as-a-virtual-machine-scale-set"></a>Můžete spustit Avere vFXT cluster jako škálovací sada virtuálních počítačů?

Avere vFXT nepodporuje nasazení škálovací sady virtuálních počítačů. Několik mechanismů dostupnosti integrované podpory jsou určeny pouze pro atomické virtuální počítače součástí clusteru.  

### <a name="can-i-run-the-avere-vfxt-cluster-on-low-priority-vms"></a>Můžete spustit Avere vFXT cluster na virtuálních počítačích s nízkou prioritou

Ne, bude systém vyžadovat základní spouští stabilní sada virtuálních počítačů.

### <a name="can-i-run-the-avere-vfxt-cluster-in-containers"></a>Můžete spustit Avere vFXT cluster v kontejnerech?

Ne, musí být nasazený Avere vFXT jako aplikace nezávislých.

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>Proveďte vFXT Avere počtu virtuálních počítačů proti Moje kvóta výpočetních prostředků?

Ano. Ujistěte se, že máte dostatečnou kvótu v oblasti pro podporu clusteru.  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>Můžete spustit Avere vFXT clusteru počítače v různých zón dostupnosti?

Ne. Model vysoké dostupnosti v tuto chvíli vFXT Avere nepodporuje jednotlivé Avere vFXT clusteru členy umístěné v různých zón dostupnosti.

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>Můžete naklonovat Avere vFXT virtuálních počítačů?

Ne, musíte použít podporovaný skript v jazyce Python pro přidávání a odebírání uzlů v clusteru vFXT Avere. Další informace najdete v článku [Správa clusteru vFXT Avere](avere-vfxt-manage-cluster.md).  

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>Je "Virtuální počítač" verzi softwaru, který lze spustit ve vlastní místní prostředí?

Ne, je systém nabízí jako clusterové zařízení a otestovali na typy konkrétního virtuálního počítače. Toto omezení pomáhá předejít vytvoření systému, které nemůžou podporovat vysoce výkonné požadavky obvyklý pracovní postup vFXT Avere. 

## <a name="technical-disks"></a>Technické: Disky

### <a name="what-types-of-disks-are-supported-for-the-azure-vms"></a>Jaké typy disků jsou podporovány pro virtuální počítače Azure?

1 TB nebo 4 TB úrovně premium SSD konfigurace můžete použít Avere vFXT pro Azure. Konfigurace premium SSD můžete nasadit jako několik spravovaných disků.

### <a name="does-the-cluster-support-unmanaged-disks"></a>Podporuje cluster nespravovaných disků?

Ne, cluster vyžaduje spravované disky.

### <a name="does-the-system-support-local-attached-ssds"></a>Podporuje systém místní jednotky SSD (připojené)?

Avere vFXT pro Azure v současné době nepodporuje místní jednotky SSD. Disky používané pro Avere vFXT musí být schopen vypne a restartuje, ale místní připojené disky SSD v této konfiguraci lze pouze ukončit.

### <a name="does-the-system-support-ultra-ssds"></a>Podporuje systém ultra SSD?

Ne, systém podporuje premium SSD pouze konfigurace.

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>Můžete odpojit Moje premium SSD disky a znovu připojte později k zachování obsahu mezipaměti mezi použitím?

Odpojení a opětovné SSD disky se nepodporuje. Mezi používá, které by mohly způsobit problémy s integritou dat mohly změnit soubor metadat nebo obsah ve zdroji.

### <a name="does-the-system-encrypt-the-cache"></a>Šifruje systém mezipaměti?

Data jsou rozdělená mezi disky, ale nejsou šifrována. Disky se však mohou být šifrována. Další informace najdete v tématu [zabezpečené a použití zásady na virtuálních počítačích v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/security-policy#encryption).

## <a name="technical-networking"></a>Technické: Sítě

### <a name="what-network-is-recommended"></a>Jaké sítě je doporučeno?

Pokud používáte místní úložiště s Avere vFXT, měli byste mít 1 GB/s nebo vyšší síťové připojení. Pokud máte malé množství dat a jste ochotni ke zkopírování dat do cloudu před spuštěním úlohy, může být připojení k síti VPN dostatečná. 

> [!TIP] 
> Nižší je síťového propojení, tím pomaleji se bude počáteční studené operace čtení. Pomalá operace čtení zvýšit latenci kanálu práce. 

### <a name="can-i-run-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>Můžu spustit Avere vFXT v jiné virtuální sítě než výpočetní cluster?

Ano, můžete vytvořit váš systém vFXT Avere v jiné virtuální sítě. Čtení [plánování vašeho systému vFXT Avere](avere-vfxt-deploy-plan.md) podrobnosti.

### <a name="does-avere-vfxt-require-its-own-subnet"></a>Vyžaduje Avere vFXT jeho vlastní podsíti?

Ano. Avere vFXT běží výhradně jako clusteru s vysokou DOSTUPNOSTÍ a vyžaduje několik IP adres provoz. Pokud se cluster nachází ve vlastní podsíti, nevznikalo riziko, IP adresa je v konfliktu, které mohou způsobovat problémy s instalací a normálního provozu. Podsítě clusteru může být v rámci existující virtuální síť tak dlouho, dokud žádné IP adresy překrývají.

### <a name="can-i-run-avere-vfxt-on-infiniband"></a>Můžete spustit Avere vFXT na InfiniBand?

Ne, Avere vFXT používá jenom Ethernet/IP.

### <a name="how-do-i-access-my-on-premises-nas-environment-from-avere-vfxt"></a>Jak získám přístup Moje NAS v místním prostředí z Avere vFXT?

Prostředí vFXT Avere je stejně jako jakýkoli jiný virtuální počítač Azure vyžaduje směrované přístup přes bránu sítě nebo VPN do datového centra zákazníka (a zpět). Zvažte použití připojení Azure ExpressRoute, pokud je k dispozici ve vašem prostředí.

### <a name="what-are-the-bandwidth-requirements-for-avere-vfxt"></a>Jaké jsou požadavky na šířku pásma pro Avere vFXT?

Požadavek na celkové šířky pásma, závisí na dva faktory: 

* Množství dat, které jsou požadovány ze zdroje 
* Klientský systém proti chybám pro latenci během počátečního načítání dat  

Pro prostředí s nízkou latenci používejte fiber řešení s rychlostí 1 GB/s minimální odkaz. Použijte ExpressRoute, pokud je k dispozici.  

### <a name="can-i-run-avere-vfxt-with-public-ip-addresses"></a>Můžete spustit Avere vFXT pomocí veřejné IP adresy?

Ne, Avere vFXT smyslem je provozována v síťovém prostředí zabezpečené prostřednictvím osvědčené postupy.  

## <a name="technical-back-end-storage-core-filers"></a>Technické: Back endové úložné (křížového jader)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>Kolik jader křížového podporuje jedno prostředí vFXT Avere?

Avere vFXT cluster podporuje až 20 křížového core. 

### <a name="how-does-the-avere-vfxt-environment-store-data"></a>Jak prostředí vFXT Avere ukládání dat?

Avere vFXT není úložiště. Je mezipaměť, která čte a zapisuje data z více cílů úložiště volá základní filtrech. Data uložená na premium SSD disky ve Avere vFXT je přechodná a nakonec se vyprázdní na jádro back-end souborového úložiště.

### <a name="which-core-filers-does-avere-vfxt-support"></a>Jaké základní křížového Avere vFXT podporuje?

Obecně řečeno Avere vFXT pro Azure podporuje tyto systémy jako základní křížového: 

* Dell EMC Isilon (OneFS 7.1, 7.2, 8.0 a 8.1) 
* NetApp ONTAP (režim 9.4, 9.3, 9.2, v clusteru 9.1P1 8.0 8.3) a (7 režimu 7.* 8.0 8.3) 
* Kontejnery objektů blob v Azure (pouze na místně redundantní úložiště) 
* AWS S3 intervalů 
* Google Cloud intervalů

### <a name="why-doesnt-avere-vfxt-support-all-nfs-filers"></a>Proč Avere vFXT nepodporuje všechny křížového systému souborů NFS?

I když všechny platformy systému souborů NFS splňovat stejné standardy organizace IETF, v praxi každý implementace má svůj vlastní adaptivní. Tyto podrobnosti ovlivňují, jak Avere vFXT komunikuje s systému úložiště. Podporované systémy jsou nejčastěji používaná platforem na webu Marketplace.

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>Podporuje Avere vFXT privátní objekt úložiště (například SwiftStack)?

Avere vFXT nepodporuje privátní objekt úložiště.

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>Jak získám produktu konkrétní úložiště v rámci podpory?

Podpora je založena na množství poptávky v poli. Pokud nejsou k dispozici dostatek výnosy podle požadavků na podporu řešení NAS, My zvážíme jeho. Proveďte požadavky prostřednictvím podpory Azure.

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>Můžete použít úložiště objektů Blob v Azure jako filtr core?

Ano, můžete použít Avere vFXT pro Azure kontejner objektů blob bloku jako filtr základní cloudové.  

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Jaké jsou požadavky na účet úložiště pro filtr základní objekt blob?

Musí být váš účet úložiště pro obecné účely v2 (GPv2) účtu a nakonfigurována pro jenom místně redundantní úložiště. Geograficky redundantní úložiště a zónově redundantní úložiště se nepodporují.

### <a name="can-i-use-archive-blob-storage"></a>Můžete použít úložiště objektů blob v archivní?

Ne. Smlouvu o úrovni služeb (SLA) pro archiv služby storage není kompatibilní s adresáře v reálném čase a požadavky na přístup souboru Avere vFXT systému. 

### <a name="can-i-use-cool-blob-storage"></a>Můžete použít cool blob storage?

Můžete použít studená vrstva přístupu, ale mějte na paměti, že bude mnohem větší rychlost operací. 

### <a name="how-do-i-encrypt-the-blob-container"></a>Jak šifrují kontejner objektů blob?

Můžete nakonfigurovat šifrování objektů blob v Azure (upřednostňováno) nebo na úrovni souborového Avere vFXT core.  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>Můžete použít vlastní šifrovací klíč pro filtr základní objekt blob?

Ve výchozím nastavení se data šifrují pomocí klíčů spravovaných microsoftem pro úložiště objektů Blob v Azure, tabulky a fronty a soubory Azure. Pro úložiště objektů Blob a službou soubory Azure můžete přineste si vlastní klíč k šifrování. Pokud se rozhodnete používat Avere vFXT šifrování, musíte použít generovaný Avere klíč a uložte ho místně. 

## <a name="purchasing"></a>Nákup

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Jak získat Avere vFXT pro licencování Azure?

Začínáme Avere vFXT licenci Azure je snadné v Tržišti Azure Marketplace. Zaregistrujte si účet Azure a pak postupujte podle pokynů v [nasazení clusteru vFXT Avere](avere-vfxt-deploy.md) k vytvoření clusteru vFXT Avere. 

### <a name="how-much-does-avere-vfxt-cost"></a>Kolik stojí Avere vFXT?

V Azure se neúčtují žádné další licenční poplatky pro použití Avere vFXT clusterů. Zákazníci odpovídají za úložiště a další poplatky za využití Azure.

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>Může běžet Avere vFXT virtuálních počítačů s nízkou prioritou?

Ne, vyžadují "always on" Avere vFXT clustery služby. Clustery můžete vypnout, když není potřeba. 

## <a name="next-steps"></a>Další postup

Abyste mohli začít s Avere vFXT pro Azure, přečtěte si tyto články a zjistěte, jak naplánovat a nasadit vlastní systém:

* [Plánování Avere vFXT systému](avere-vfxt-deploy-plan.md)
* [Přehled nasazení](avere-vfxt-deploy-overview.md)
* [Příprava na vytvoření clusteru vFXT Avere](avere-vfxt-prereqs.md)
* [Nasazení clusteru Avere vFXT](avere-vfxt-deploy.md)

Další informace o funkcích a případech použití pro Avere vFXT, najdete v tématu [Avere vFXT pro Azure](https://azure.microsoft.com/services/storage/avere-vfxt/).
