---
title: Časté otázky – Avere vFXT pro Azure
description: Nejčastější dotazy týkající se Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 186b2c048a9de42318e4af287393d731a4eb16f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153458"
---
# <a name="avere-vfxt-for-azure-faq"></a>Avere vFXT for Azure – nejčastější dotazy

Tento článek odpovídá na otázky, které vám můžou pomoct rozhodnout, jestli avere vFXT pro Azure je ta pravá pro vaše potřeby. Poskytuje základní informace o Avere vFXT a vysvětluje, jak funguje s jinými součástmi Azure a s produkty od externích dodavatelů.

## <a name="general"></a>Obecné

### <a name="what-is-avere-vfxt-for-azure"></a>Co je Avere vFXT for Azure?

Avere vFXT for Azure je vysoce výkonný souborový systém, který ukládá aktivní data do výpočetních prostředků Azure pro efektivní zpracování kritických úloh.

### <a name="is-avere-vfxt-a-storage-solution"></a>Je Avere vFXT úložné řešení?

Ne. Avere vFXT pro Azure je *mezipaměti* systému souborů, která se připojuje k prostředí úložiště, jako je vaše EMC nebo NetApp NAS nebo kontejner objektů blob Azure. Avere vFXT zjednodušuje požadavky na data od klientů a ukládá data, která slouží ke zlepšení výkonu ve velkém měřítku a v průběhu času. Avere vFXT sám neukládá data. Nemá žádné informace o množství dat uložených za ním.

### <a name="is-avere-vfxt-a-tiering-solution"></a>Je Avere vFXT vrstvené řešení?

Avere vFXT pro Azure automaticky nevrství data mezi horkými a chladnými vrstvami.  

### <a name="how-do-i-know-if-an-environment-is-right-for-avere-vfxt"></a>Jak poznám, že je prostředí pro Avere vFXT to pravé?

Nejlepší způsob, jak přemýšlet o této otázce, je zeptat se: "Je zatížení cacheable?" To znamená, že pracovní zátěž má vysoký poměr čtení k zápisu? Příkladem je 80/20 nebo 70/30 čtení/zápisy.

Zvažte Avere vFXT pro Azure, pokud máte analytický kanál založený na souborech, který běží přes velký počet virtuálních počítačů Azure a splňuje jednu nebo více z následujících podmínek:

* Celkový výkon je pomalý nebo nekonzistentní z důvodu dlouhé doby přístupu k souborům (desítky milisekund nebo sekund, v závislosti na požadavcích). Tato latence je pro zákazníka nepřijatelná.

* Data potřebná pro zpracování jsou umístěna na vzdáleném konci prostředí WAN a jejich trvalé přesunutí dat je nepraktické. Data mohou být v jiné oblasti Azure nebo v datovém centru zákazníka.

* O data žádá značný počet klientů – například v clusteru s vysoce výkonným výpočetním výkonem (HPC). Velký počet souběžných požadavků může zvýšit latenci.

* Zákazník chce spustit svůj aktuální kanál "tak, jak je" ve virtuálních počítačích Azure a potřebuje řešení sdíleného úložiště (nebo ukládání do mezipaměti) založené na POSIX pro škálovatelnost. Pomocí Avere vFXT pro Azure, není potřeba přepracovat pracovní kanál pro nativní volání úložiště objektů blob Azure.

* Vaše HPC aplikace je založena na klientech NFSv3. (V některých případech může používat klienty SMB 2.1, ale výkon je omezený.)

Následující diagram vám může pomoci odpovědět na tuto otázku. Čím blíže je váš pracovní postup k pravému hornímu, tím je pravděpodobnější, že řešení ukládání do mezipaměti Avere vFXT pro Azure je vhodné pro vaše prostředí.

![Graf diagram udávající, že zatížení s tisíci klienty je vhodnější pro Avere vFXT](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>V jakém rozsahu klientů dává řešení Avere vFXT největší smysl?

Řešení mezipaměti Avere vFXT je vytvořeno tak, aby zpracovávalo stovky, tisíce nebo desítky tisíc výpočetních jader. Pokud máte několik strojů, které pracují na lehkou váhu, Avere vFXT není tím správným řešením.

Typickí zákazníci Avere vFXT spouštějí náročné úlohy od přibližně 1 000 procesorových jader. Tato prostředí mohou být až 50 000 jader nebo více. Vzhledem k tomu, že Avere vFXT je škálovatelná, můžete přidat uzly pro podporu těchto úloh, jak rostou, aby vyžadovaly větší propustnost nebo více viopů.

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Kolik dat může prostředí Avere vFXT ukládat?

Avere vFXT pro Azure je mezipaměť. Neukládá data konkrétně. Používá kombinaci paměti RAM a SSD pro ukládání dat uložených v mezipaměti. Data jsou trvale uložena v back-endovém úložném systému (například v systému NetApp NAS nebo v kontejneru objektů blob). Systém Avere vFXT nemá informace o množství dat uložených za ním. Avere vFXT ukládá pouze do mezipaměti podmnožinu těchto dat, které klienti požadují.  

### <a name="what-regions-are-supported"></a>Jaké oblasti jsou podporovány?

Avere vFXT pro Azure se podporuje ve všech oblastech s výjimkou suverénních oblastí (Čína, Německo). Ujistěte se, že oblast, kterou chcete použít, podporuje velké množství výpočetních jader a instance virtuálních her potřebné k vytvoření clusteru Avere vFXT.

### <a name="how-do-i-get-help-with-avere-vfxt"></a>Jak získám pomoc s Avere vFXT?

Specializovaná skupina pracovníků podpory nabízí pomoc s Avere vFXT pro Azure. Podle pokynů v [části Získání nápovědy k vašemu systému otevřete](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) lístek podpory z webu Azure Portal.

### <a name="is-avere-vfxt-highly-available"></a>Je Avere vFXT vysoce dostupný?

Ano, Avere vFXT běží výhradně jako řešení HA.

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>Podporuje Avere vFXT pro Azure také další cloudové služby?

Ano, zákazníci mohou používat více než jednoho poskytovatele cloudu s clusterem Avere vFXT. Podporuje standardní kontejnery AWS S3, standardní kontejnery Google Cloud Services a kontejnery objektů blob Azure.

> [!NOTE]
> Za použití Avere vFXT s AWS nebo google cloudovým úložištěm se vztahuje softwarový poplatek. Za používání úložiště objektů blob Azure se neplatí žádný další softwarový poplatek.

## <a name="technical-compute"></a>Technické: Výpočetní výkon

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>Můžete popsat, jak "vypadá prostředí Avere vFXT"?

Avere vFXT je clusterované zařízení vyrobené z více virtuálních počítačů Azure. Knihovna Pythonu zpracovává vytváření, odstraňování a úpravy clusteru. Přečtěte [si, co je Avere vFXT pro Azure?](avere-vfxt-overview.md)

### <a name="what-kind-of-azure-virtual-machines-does-avere-vfxt-run-on"></a>Na jakém druhu virtuálních počítačů Azure běží Avere vFXT?  

Cluster Avere vFXT pro Azure používá microsoft azure E32s_v3 virtuální počítače.

<!-- ### Can I mix and match virtual machine types for my cluster?

No, you must choose one virtual machine type or the other.
    
### Can I move between virtual machine types?

Yes, there is a migration path to move from one VM type to the other. [Open a support ticket](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) to learn how.
-->

### <a name="does-the-avere-vfxt-environment-scale"></a>Má prostředí Avere vFXT škálování?

Cluster Avere vFXT může být malý jako tři uzly virtuálního počítače nebo až 24 uzlů. Pokud se domníváte, že potřebujete cluster s více než devíti uzly, obraťte se na technickou podporu Azure. Větší počet uzlů vyžaduje větší architekturu nasazení.

### <a name="does-the-avere-vfxt-environment-autoscale"></a>Má prostředí Avere vFXT "automatické škálování"?

Ne. Velikost clusteru můžete škálovat nahoru a dolů, ale přidání nebo odebrání uzlů clusteru je ruční krok.

### <a name="can-i-run-the-avere-vfxt-cluster-as-a-virtual-machine-scale-set"></a>Můžu spustit cluster Avere vFXT jako škálovací sadu virtuálních strojů?

Avere vFXT nepodporuje nasazení škálovací sady virtuálních strojů. Několik integrované mechanismy podpory dostupnosti jsou určeny pouze pro atomické virtuální chody účastnící se clusteru.  

### <a name="can-i-run-the-avere-vfxt-cluster-on-low-priority-vms"></a>Můžu spustit cluster Avere vFXT na virtuálních počítačích s nízkou prioritou?

Ne, systém vyžaduje základní stabilní sadu virtuálních počítačů.

### <a name="can-i-run-the-avere-vfxt-cluster-in-containers"></a>Můžu spustit cluster Avere vFXT v kontejnerech?

Ne, Avere vFXT musí být nasazenjako nezávislá aplikace.

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>Počítají se virtuální počítače Avere vFXT do mé výpočetní kvóty?

Ano. Ujistěte se, že máte dostatečnou kvótu v oblasti pro podporu clusteru.  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>Mohu spustit clusterové počítače Avere vFXT v různých zónách dostupnosti?

Ne. Model vysoké dostupnosti v Avere vFXT aktuálně nepodporuje jednotlivé členy clusteru Avere vFXT umístěné v různých zónách dostupnosti.

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>Mohu klonovat virtuální počítače Avere vFXT?

Ne, k přidání nebo odebrání uzlů v clusteru Avere vFXT je nutné použít podporovaný skript Pythonu. Další informace naleznete [v části Správa clusteru Avere vFXT](avere-vfxt-manage-cluster.md).

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>Existuje verze softwaru "VM", kterou lze spustit ve vlastním místním prostředí?

Ne, systém je nabízen jako clusterované zařízení a testován na konkrétních typech virtuálních počítačů. Toto omezení pomáhá zákazníkům vyhnout se vytváření systému, který nemůže podporovat požadavky na vysoký výkon typického pracovního postupu Avere vFXT.

## <a name="technical-disks"></a>Technické: Disky

### <a name="what-types-of-disks-are-supported-for-the-azure-vms"></a>Jaké typy disků jsou podporované pro virtuální počítače Azure?

Avere vFXT pro Azure můžete použít 1 TB nebo 4 TB premium SSD konfigurace. Konfiguraci ssd premium lze nasadit jako více spravovaných disků.

### <a name="does-the-cluster-support-unmanaged-disks"></a>Podporuje cluster nespravované disky?

Ne, cluster vyžaduje spravované disky.

### <a name="does-the-system-support-local-attached-ssds"></a>Podporuje systém místní (připojené) ssd disy?

Avere vFXT pro Azure aktuálně nepodporuje místní ssd disy. Disky používané pro Avere vFXT musí být možné vypnout a restartovat, ale místní připojené SSD v této konfiguraci lze pouze ukončit.

### <a name="does-the-system-support-ultra-ssds"></a>Podporuje systém ultra SSD?

Ne, systém podporuje pouze prémiové konfigurace SSD.

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>Mohu odpojit své prémiové Disky SSD a později je znovu připojit, aby se mezi použitím zachoval obsah mezipaměti?

Odpojení a opětovné připojení ssd dispozicí není podporováno. Metadata nebo obsah souboru ve zdroji se mohly změnit mezi použitím, což může způsobit problémy s integritou dat.

### <a name="does-the-system-encrypt-the-cache"></a>Šifruje systém mezipaměť?

Data jsou prokládána na discích, ale nejsou šifrována. Samotné disky však mohou být šifrovány. Další informace najdete v [tématu Zabezpečení a používání zásad na virtuálních počítačích v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/security-policy#encryption).

## <a name="technical-networking"></a>Technical: Networking

### <a name="what-network-is-recommended"></a>Jaká síť se doporučuje?

Pokud používáte místní úložiště s Avere vFXT, měli byste mít 1 Gb/s nebo lepší síťové připojení mezi úložištěm a clusterem. Pokud máte malé množství dat a jste ochotni zkopírovat data do cloudu před spuštěním úloh, připojení VPN může být dostačující.

> [!TIP]
> Čím pomalejší je síťové spojení, tím pomalejší bude počáteční "studené" čtení. Pomalé čtení zvýšit latenci kanálu práce.

### <a name="can-i-run-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>Můžu spustit Avere vFXT v jiné virtuální síti, než je můj výpočetní cluster?

Ano, systém Avere vFXT můžete vytvořit v jiné virtuální síti. Podrobnosti [načlánekněte na plán systému Avere vFXT.](avere-vfxt-deploy-plan.md)

### <a name="does-avere-vfxt-require-its-own-subnet"></a>Vyžaduje Avere vFXT vlastní podsíť?

Ano. Avere vFXT běží striktně jako cluster s vysokou dostupností (HA) a vyžaduje více IP adres pro provoz. Pokud je cluster ve vlastní podsíti, vyhnete se riziku konfliktů adres IP, což může způsobit problémy při instalaci a normálním provozu. Podsíť clusteru může být ve virtuální síti používané jinými prostředky, pokud se žádné adresy IP nepřekrývají.

### <a name="can-i-run-avere-vfxt-on-infiniband"></a>Mohu spustit Avere vFXT na InfiniBandu?

Ne, Avere vFXT používá pouze Ethernet/IP.

### <a name="how-do-i-access-my-on-premises-nas-environment-from-avere-vfxt"></a>Jak se dostanu k místnímu prostředí NAS z Avere vFXT?

Prostředí Avere vFXT je jako každý jiný virtuální počítač Azure v tom, že vyžaduje směrovaný přístup přes síťovou bránu nebo VPN do zákaznického datového centra (a zpět). Zvažte použití připojení Azure ExpressRoute, pokud je k dispozici ve vašem prostředí.

### <a name="what-are-the-bandwidth-requirements-for-avere-vfxt"></a>Jaké jsou požadavky na šířku pásma pro Avere vFXT?

Celkový požadavek na šířku pásma závisí na dvou faktorech:

* Množství požadovaných dat od zdroje
* Tolerance klientského systému pro latenci při počátečním načítání dat  

Pro prostředí citlivá na latenci byste měli použít řešení vláken s minimální rychlostí připojení 1 Gb/s. Použijte ExpressRoute, pokud je k dispozici.  

### <a name="can-i-run-avere-vfxt-with-public-ip-addresses"></a>Mohu spustit Avere vFXT s veřejnými IP adresami?

Ne, Avere vFXT je určen k provozu v síťovém prostředí zabezpečeném pomocí osvědčených postupů.

### <a name="can-i-restrict-internet-access-from-my-clusters-virtual-network"></a>Je možné omezit přístup k internetu z virtuální sítě clusteru?

Obecně můžete nakonfigurovat další zabezpečení ve virtuální síti podle potřeby, ale některá omezení mohou narušit provoz clusteru.

Například omezení odchozího přístupu k internetu z vaší virtuální sítě způsobuje problémy pro cluster, pokud také nepřidáte pravidlo, které explicitně umožňuje přístup k AzureCloudu. Tato situace je popsána v [doplňkové dokumentaci na GitHubu](https://github.com/Azure/Avere/tree/master/src/vfxt/internet_access.md).

Chcete-li získat pomoc s přizpůsobeným zabezpečením, obraťte se na podporu, jak je popsáno v [seznamu Získat pomoc se systémem](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt).

## <a name="technical-back-end-storage-core-filers"></a>Technické: Back-end úložiště (základní filery)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>Kolik základních filerů podporuje jedno prostředí Avere vFXT?

Cluster Avere vFXT podporuje až 20 základních filerů.

### <a name="how-does-the-avere-vfxt-environment-store-data"></a>Jak prostředí Avere vFXT ukládá data?

Avere vFXT není úložiště. Je to cache, která čte a zapisuje data z více cílů úložiště nazývaných základní filery. Data uložená na prémiových ssd discích v Avere vFXT jsou přechodná a nakonec jsou vyprázdněna do úložiště souborů back-end core.

### <a name="which-core-filers-does-avere-vfxt-support"></a>Které základní filery Avere vFXT podporuje?

Obecně platí, že Avere vFXT pro Azure podporuje následující systémy jako základní filery:

* Dell EMC Isilon (OneFS 7.1, 7.2, 8.0 a 8.1) 
* NetApp ONTAP (clusterovaný režim 9.4, 9.3, 9.2, 9.1P1, 8.0-8.3) a (7-Mode 7.*, 8.0-8.3)

* Kontejnery objektů blob Azure (jenom místně redundantní úložiště)
* AWS S3 kbelíky
* Google Cloud kbelíky

### <a name="why-doesnt-avere-vfxt-support-all-nfs-filers"></a>Proč Avere vFXT nepodporuje všechny filery NFS?

Ačkoli všechny nfs platformy splňují stejné standardy IETF, v praxi každá implementace má své vlastní vtípky. Tyto podrobnosti ovlivňují interakci Avere vFXT se systémem úložiště. Podporované systémy jsou nejpoužívanější platformy na trhu.

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>Podporuje Avere vFXT úložiště soukromých objektů (například SwiftStack)?

Avere vFXT nepodporuje úložiště soukromých objektů.

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>Jak mohu získat konkrétní úložný produkt pod podporou?

Podpora je založena na výši poptávky v terénu. Pokud existuje dostatek požadavků na podporu řešení NAS, zvážíme to. Pořizuj si požadavky prostřednictvím podpory Azure.

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>Můžu použít azure blob úložiště jako základní filer?

Ano, Avere vFXT pro Azure můžete použít kontejner blob bloku jako filer jádra cloudu.

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Jaké jsou požadavky na účet úložiště pro filer jádra objektu blob?

Váš účet úložiště musí být účet pro obecné účely v2 (GPv2) a nakonfigurovaný pouze pro místně redundantní úložiště. Geograficky redundantní úložiště a zónově redundantní úložiště nejsou podporovány.

Další informace o požadavcích na účet úložiště dat načtete v [centru cloudového úložiště Azure Blob Storage.](avere-vfxt-add-storage.md#azure-blob-storage-cloud-core-filer)

### <a name="can-i-use-archive-blob-storage"></a>Můžu použít archivní úložiště objektů blob?

Ne. Smlouva o úrovni služeb (SLA) pro úložiště archivu není kompatibilní s potřebami přístupu k adresářům a souborům v reálném čase v systému Avere vFXT.

### <a name="can-i-use-cool-blob-storage"></a>Můžu použít skvělé úložiště objektů blob?

Úložiště objektů blob úrovně cool se obvykle nedoporučuje pro avere vFXT pro základní filer Azure. Úroveň Cool nabízí nižší náklady na úložiště, ale vyšší provozní náklady. (Další podrobnosti najdete v [tématu Blokování cen objektů blob.)](<https://azure.microsoft.com/pricing/details/storage/blobs/>) Pokud budou data často upravována a upravována nebo smazána, zvažte použití horké úrovně.

[Úrovně přístupu](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers#cool-access-tier) poskytuje další informace o tom, kdy může mít smysl používat úložiště úrovně Cool jako základní filer vFXT.

### <a name="how-do-i-encrypt-the-blob-container"></a>Jak zašifruji kontejner objektů blob?

Šifrování objektů blob můžete nakonfigurovat buď v Azure (upřednostňované) nebo na úrovni základního fileru Avere vFXT.  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>Můžu použít vlastní šifrovací klíč pro filer jádra objektu blob?

Ve výchozím nastavení se data šifrují prostřednictvím klíčů spravovaných microsoftem pro úložiště objektů Blob, Tabulka a Fronty Azure a soubory Azure. Můžete si přinést vlastní klíč pro šifrování pro úložiště objektů Blob a soubory Azure. Pokud se rozhodnete použít šifrování Avere vFXT, musíte použít klíč generovaný Avere a uložit jej místně.

## <a name="purchasing"></a>Nákup

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Jak získám licencování Avere vFXT pro Azure?

Získání licence Avere vFXT pro Azure je snadné prostřednictvím Azure Marketplace. Zaregistrujte si účet Azure a postupujte podle pokynů v [nasazení clusteru Avere vFXT](avere-vfxt-deploy.md) a vytvořte cluster Avere vFXT.

### <a name="how-much-does-avere-vfxt-cost"></a>Kolik stojí Avere vFXT?

V Azure neexistuje žádný další licenční poplatek za použití clusterů Avere vFXT. Zákazníci jsou zodpovědní za poplatky za úložiště a další poplatky za spotřebu Azure.

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>Lze virtuální chod Avere vFXT spustit s nízkou prioritou?

Ne, clustery Avere vFXT vyžadují službu "vždy zapnuto". Clustery lze vypnout, když není potřeba.

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s Avere vFXT pro Azure, přečtěte si tyto články, kde se dozvíte, jak naplánovat a nasadit vlastní systém:

* [Plánování systému Avere vFXT](avere-vfxt-deploy-plan.md)
* [Přehled nasazení](avere-vfxt-deploy-overview.md)
* [Příprava na vytvoření clusteru Avere vFXT](avere-vfxt-prereqs.md)
* [Nasazení clusteru Avere vFXT](avere-vfxt-deploy.md)

Další informace o možnostech a případech použití pro Avere vFXT najdete na webu [Avere vFXT for Azure](https://azure.microsoft.com/services/storage/avere-vfxt/).
