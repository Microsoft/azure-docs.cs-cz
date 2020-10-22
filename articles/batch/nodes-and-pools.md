---
title: Uzly a fondy v Azure Batch
description: Přečtěte si o výpočetních uzlech a fondech a o tom, jak se používají v Azure Batch pracovním postupu z hlediska vývoje.
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: a6422976f5362e9ff32cd41cc167a00441ab7aec
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371439"
---
# <a name="nodes-and-pools-in-azure-batch"></a>Uzly a fondy v Azure Batch

V pracovním postupu Azure Batch je *výpočetní uzel* (neboli *uzel*) virtuálním počítačem, který zpracovává část úlohy vaší aplikace. *Fond* je kolekce těchto uzlů, na kterých má vaše aplikace běžet. V tomto článku se dozvíte víc o uzlech a fondech spolu s důležitými informacemi při jejich vytváření a používání v pracovním postupu Azure Batch.

## <a name="nodes"></a>Uzly

Uzel je virtuální počítač Azure (VM) nebo virtuální počítač cloudové služby, který je vyhrazený pro zpracování části úlohy vaší aplikace. Velikost uzlu určuje počet jader procesoru, kapacita paměti a velikost místního systému souborů, který je přidělen k uzlu.

Fondy uzlů Windows nebo Linux můžete vytvořit pomocí služby Azure Cloud Services, imagí z webu [Azure Virtual Machines Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?filters=virtual-machine-images&page=1) nebo vlastních imagí, které připravíte.

Uzly mohou spustit libovolný spustitelný soubor nebo skript, který je podporován prostředím operačního systému uzlu. Spustitelné soubory nebo skripty zahrnují soubory \* . exe, \* . cmd, \* . bat a skripty PowerShellu (pro Windows) a binární soubory, prostředí a skripty Pythonu (pro Linux).

Součástí všech výpočetních uzlů ve službě Batch také jsou:

- Standardní [struktura složek](files-and-directories.md) a přidružené [proměnné prostředí](jobs-and-tasks.md), které jsou úkolu k dispozici.
- Nastavení **brány firewall**, která jsou nakonfigurována pro řízení přístupu.
- [Vzdálený přístup](error-handling.md#connect-to-compute-nodes) k uzlům Windows (protokol RDP (Remote Desktop Protocol) (RDP)) i Linux (Secure Shell (SSH)) (Pokud [nevytvoříte fond se zakázaným vzdáleným přístupem](pool-endpoint-configuration.md)).

Ve výchozím nastavení můžou uzly vzájemně komunikovat, ale nemůžou komunikovat s virtuálními počítači, které nejsou součástí stejného fondu. Pokud chcete, aby uzly komunikovaly bezpečně s ostatními virtuálními počítači nebo v místní síti, můžete fond zřídit [v podsíti virtuální sítě Azure (VNET)](batch-virtual-network.md). Když to uděláte, k vašim uzlům můžete přistup prostřednictvím veřejných IP adres. Tyto veřejné IP adresy vytvoří služba Batch a můžou se měnit po dobu života fondu. Můžete také [vytvořit fond se statickými veřejnými IP adresami](create-pool-public-ip.md) , které řídíte, což zajistí, že se neočekávaně nezmění.

## <a name="pools"></a>Fondy

Fond je kolekce uzlů, na kterých běží vaše aplikace.

Fondy Azure Batch jsou postavené na základní platformě Azure Compute. Poskytují rozsáhlé přidělení, instalaci aplikací, distribuci dat, monitorování stavu a flexibilní úpravy ([škálování](#automatic-scaling-policy)) počtu výpočetních uzlů v rámci fondu.

Každému uzlu, který je přidán do fondu, je přiřazen jedinečný název a IP adresa. Pokud je uzel odebrán z fondu, veškeré změny provedené v operačním systému nebo souborech jsou ztraceny a jeho název a IP adresa jsou uvolněny pro pozdější použití. Jestliže uzel opustí fond, je jeho životnost ukončena.

Fond může být používán pouze účtem Batch, ve kterém byl vytvořen. Účet Batch může vytvořit více fondů pro splnění požadavků na prostředky aplikací, které spustí.

Fond lze vytvořit ručně nebo automaticky pomocí služby Batch při zadání práce, která má být provedena. Při vytváření fondu můžete zadat následující atributy:

- [Operační systém a verze uzlu](#operating-system-and-version)
- [Typ uzlu a cílový počet uzlů](#node-type-and-target)
- [Velikost uzlu](#node-size)
- [Zásada automatického škálování](#automatic-scaling-policy)
- [Zásady plánování úkolů](#task-scheduling-policy)
- [Stav komunikace](#communication-status)
- [Spustit úlohy](#start-tasks)
- [Balíčky aplikací](#application-packages)
- [Konfigurace virtuální sítě a brány firewall](#virtual-network-vnet-and-firewall-configuration)
- [Doba platnosti](#pool-and-compute-node-lifetime)

> [!IMPORTANT]
> Účty Batch mají výchozí kvótu, která omezuje počet jader v účtu Batch. Počet jader odpovídá počtu výpočetních uzlů. Výchozí kvóty a pokyny pro [navýšení kvóty](batch-quota-limit.md#increase-a-quota) najdete v článku [Kvóty a omezení služby Azure Batch](batch-quota-limit.md). Pokud váš fond nedosahuje cílového počtu uzlů, může být důvodem právě kvóta jader.

## <a name="operating-system-and-version"></a>Operační systém a verze

Při vytváření fondu služby Batch zadáte konfiguraci virtuálního počítače Azure a typ operačního systému, který chcete spustit na každém výpočetním uzlu ve fondu.

## <a name="configurations"></a>Konfigurace

Ve Batch jsou k dispozici dva typy konfigurací fondů.

### <a name="virtual-machine-configuration"></a>Konfigurace virtuálního počítače

**Konfigurace virtuálního počítače** určuje, že se fond skládá z virtuálních počítačů Azure. Tyto virtuální počítače mohou být vytvořené na základě image Linuxu nebo Windows.

Při vytváření fondu založeného na konfiguraci virtuálního počítače je nutné zadat nejen velikost uzlů a zdroj imagí použitých pro jejich vytvoření, ale také **referenční image virtuálního počítače** a **SKU agenta uzlu** služby Batch, které se budou na uzly instalovat. Další informace o zadávání těchto vlastností fondů najdete v článku [Zřízení linuxových výpočetních uzlů ve fondech Azure Batch](batch-linux-nodes.md) Volitelně můžete k virtuálním počítačům ve fondu vytvořeným z imagí z webu Marketplace připojit jeden nebo více prázdných datových disků nebo datové disky zahrnout do vlastních imagí používaných k vytvoření virtuálních počítačů. Při zahrnutí datových disků je potřeba připojit a naformátovat disky z virtuálního počítače, aby je bylo možné použít.

### <a name="cloud-services-configuration"></a>Konfigurace Cloud Services

**Konfigurace Cloud Services** určuje, že se fond skládá z uzlů Azure Cloud Services. Cloud Services poskytuje *jenom*výpočetní uzly Windows.

Operační systémy, které jsou k dispozici pro fondy konfigurace služby Cloud Services, jsou uvedeny v [matici kompatibility verzí hostovaného operačního systému Azure a sad SDK](../cloud-services/cloud-services-guestos-update-matrix.md). Při vytváření fondu, který obsahuje uzly Cloud Services, je nutné zadat velikost uzlu a jeho *rodinu operačních systémů* (což určuje, které verze rozhraní .NET jsou nainstalovány s operačním systémem). Cloud Services se do Azure nasadí rychleji než virtuální počítače s Windows. Pokud chcete fondy výpočetních uzlů Windows, můžete zjistit, že služba Cloud Services představuje výhodu z hlediska času nasazení.

Podobně jako u rolí pracovního procesu v rámci služby Cloud Services můžete zadat *verzi operačního systému* (další informace o rolích pracovního procesu najdete v článku [Přehled služby Cloud Services](../cloud-services/cloud-services-choose-me.md)). Doporučujeme, abyste `Latest (*)` pro *verzi operačního systému* určili, že se uzly automaticky upgradují a že se pro nové vydané verze nevyžadovala žádná práce. Hlavním případem použití s výběrem konkrétní verze operačního systému scénář zajištění kompatibility aplikací, který umožní testovat zpětnou kompatibilitu, než se povolí aktualizace verze. Po ověření bude možné aktualizovat *verzi operačního systému* pro fond a nainstalovat novou bitovou kopii operačního systému. Všechny spuštěné úlohy budou přerušeny a znovu zařazeny do fronty.

### <a name="node-agent-skus"></a>SKU agenta uzlu

Když vytvoříte fond, je nutné vybrat odpovídající **nodeAgentSkuId**, v závislosti na operačním systému základní image vašeho disku VHD. Můžete získat mapování dostupných ID SKU agenta uzlu na odkazy na image operačního systému voláním operace [seznam podporovaných SKU agenta uzlu](/rest/api/batchservice/list-supported-node-agent-skus) .

### <a name="custom-images-for-virtual-machine-pools"></a>Vlastní image pro fondy virtuálních počítačů

Informace o tom, jak vytvořit fond s vlastními imagemi, najdete v tématu [použití Galerie sdílených imagí k vytvoření vlastního fondu](batch-sig-images.md).

Případně můžete vytvořit vlastní fond virtuálních počítačů pomocí prostředku [spravované image](batch-custom-images.md) . Informace o přípravě vlastních imagí Linuxu z virtuálních počítačů Azure najdete v tématu popisujícím [vytvoření image virtuálního počítače nebo virtuálního pevného disku](../virtual-machines/linux/capture-image.md). Informace o přípravě vlastních imagí Windows z virtuálních počítačů Azure najdete v tématu popisujícím [vytvoření spravované image zobecněného virtuálního počítače v Azure](../virtual-machines/windows/capture-image-resource.md).

### <a name="container-support-in-virtual-machine-pools"></a>Podpora kontejnerů ve fondech virtuálních počítačů

Při vytváření fondu konfigurace virtuálních počítačů pomocí rozhraní API služby Batch můžete ve fondu nastavit spouštění úloh v kontejnerech Dockeru. V současné době musíte fond vytvořit pomocí image, která podporuje kontejnery Dockeru. Použijte Windows Server 2016 Datacenter s použitím image kontejnerů z webu Azure Marketplace nebo zadejte vlastní image virtuálního počítače, která zahrnuje Docker Community Edition nebo Enterprise Edition a všechny požadované ovladače. Nastavení fondu musí obsahovat [konfiguraci kontejneru](/rest/api/batchservice/pool/add), která při vytvoření fondu zkopíruje image kontejneru do virtuálních počítačů. Úlohy spouštěné ve fondu pak můžou odkazovat na image kontejneru a možnosti spuštění kontejneru.

Další informace najdete v tématu věnovaném [spuštění kontejnerových aplikací Dockeru ve službě Azure Batch](batch-docker-container-workloads.md).

## <a name="node-type-and-target"></a>Typ uzlu a cíl

Při vytváření fondu můžete určit typy uzlů, které chcete, a cílové číslo pro každé z nich. Existují dva typy uzlů:

- **Vyhrazené uzly.** Vyhrazené výpočetní uzly jsou rezervované pro vaše úlohy. Jsou dražší než uzly s nízkou prioritou, ale nabízejí záruku toho, že nikdy nedojde k jejich zrušení.
- **Uzly s nízkou prioritou.** Uzly s nízkou prioritou mají tu výhodu, že ke spouštění úloh služby Batch využívají nadbytečnou kapacitu v Azure. Uzly s nízkou prioritou jsou méně nákladné za hodinu než vyhrazené uzly a umožňují úlohy, které vyžadují významný výpočetní výkon. Další informace najdete v tématu [Použití virtuálních počítačů s nízkou prioritou ve službě Batch](batch-low-pri-vms.md).

Uzly s nízkou prioritou se můžou zacházet, pokud Azure nemá dostatek nadbytečné kapacity. Pokud dojde ke zrušení uzlu během spouštění úloh, tyto úlohy se znovu zařadí do fronty, a jakmile bude uzel zase dostupný, znovu se spustí. Uzly s nízkou prioritou jsou dobrou volbou pro úlohy s flexibilním časem dokončení a pro úkony distribuované mezi velký počet uzlů. Než se rozhodnete používat pro svůj scénář uzly s nízkou prioritou, ujistěte se, že všechny práce ztracené v důsledku ztracený budou minimální a dají se snadno vytvořit znovu.

Ve stejném fondu můžete mít výpočetní uzly s nízkou prioritou i vyhrazené uzly. Každý typ uzlu má své vlastní nastavení cíle, pro které můžete zadat požadovaný počet uzlů.

Počet výpočetních uzlů se označuje jako *cílový* z toho důvodu, že v některých situacích nemusí váš fond dosáhnout požadovaného počtu uzlů. Fond třeba nemusí dosáhnout cílového nastavení v případě, že dřív dosáhne [základní kvóty](batch-quota-limit.md) daného účtu služby Batch. Nebo je možné, že fond nedosáhne cíle, pokud jste použili vzorec automatického škálování pro fond, který omezuje maximální počet uzlů.

Informace o cenách pro uzly s nízkou a vyhrazenou prioritou najdete v tématu [ceny služby Batch](https://azure.microsoft.com/pricing/details/batch/).

## <a name="node-size"></a>Velikost uzlu

Při vytváření fondu Azure Batch máte na výběr téměř ze všech řad a velikostí virtuálních počítačů, které jsou v Azure k dispozici. Azure nabízí řadu velikostí virtuálních počítačů pro různé úlohy, včetně specializovaných velikostí s podporou [prostředí HPC](../virtual-machines/sizes-hpc.md) nebo [grafického procesoru](../virtual-machines/sizes-gpu.md). 

Další informace najdete v tématu [Výběr velikosti virtuálních počítačů pro výpočetní uzly ve fondu Azure Batch](batch-pool-vm-sizes.md).

## <a name="automatic-scaling-policy"></a>Zásada automatického škálování

Pro dynamické úlohy můžete pro fond použít zásadu automatického škálování. Služba Batch bude pravidelně vyhodnocovat vzorec a dynamicky přizpůsobí počet uzlů v rámci fondu podle aktuálního zatížení a využití prostředků ve vašem výpočetním scénáři. To umožňuje snížit celkové náklady na běh aplikace, protože se využívají pouze prostředky, které jsou nutné, a aktuálně nepotřebné se uvolňují.

Automatické škálování můžete zapnout napsáním [vzorce automatického škálování](batch-automatic-scaling.md#autoscale-formulas) a jeho přidružením k fondu. Služba Batch používá tento vzorec k určování cílového počtu uzlů ve fondu pro další interval škálování (ten můžete nakonfigurovat). Nastavení automatického škálování můžete pro fond zadat při jeho vytvoření, nebo můžete škálování povolit ve fondu později. Také můžete aktualizovat nastavení škálování ve fondu, v němž je škálování povoleno.

Například, možná úloha vyžaduje, abyste odeslali velký počet úkolů, které se mají spustit. Fondu můžete přiřadit vzorec škálování na základě aktuálního počtu úkolů čekajících ve frontě a rychlosti dokončování těchto úkolů v úloze, který přizpůsobí počet uzlů ve fondu. Služba Batch vzorec pravidelně vyhodnocuje a mění velikost fondu na základě vytížení a dalších nastavení vzorce. Služba podle potřeby přidává uzly, když ve frontě čeká velký počet úkolů, a naopak uzly odebírá, když ve frontě nejsou žádné úkoly nebo žádné úkoly právě neběží.

Vzorec škálování může být založen na následujících metrikách:

- **Časové metriky** jsou založeny na statistikách shromažďovaných každých pět minut po zadaný počet hodin.
- **Metriky prostředků** jsou založeny na využití procesoru, šířky pásma a paměti a na počtu uzlů.
- **Metriky úkolů** jsou založeny na stavu úkolů, jako je například *Aktivní* (zařazený do fronty), *Spuštěný* nebo *Dokončený*.

Když automatické škálování snižuje počet výpočetních uzlů ve fondu, je nutné zvážit, jak naložit s úkoly, které v okamžiku snižování již běží. V rámci této služby poskytuje funkce Batch [*možnost zrušení přidělení uzlu*](/rest/api/batchservice/pool/removenodes#computenodedeallocationoption) , kterou můžete zahrnout do vzorců. Můžete například zadat, že spuštěné úkoly se mají okamžitě zastavit a pak znovu zařadit do fronty pro provedení na jiném uzlu, nebo nechat dokončit před odebráním uzlu z fondu. Všimněte si, že nastavení možnosti zrušení přidělení uzlu jako `taskcompletion` nebo `retaineddata` zabrání operacím změny velikosti fondu, dokud nebudou dokončeny všechny úlohy nebo dokud nevyprší platnost všech dob uchovávání úkolů.

Další informace o automatickém škálování aplikace naleznete v tématu [Automatické škálování výpočetních uzlů ve fondu Azure Batch](batch-automatic-scaling.md).

> [!TIP]
> Chcete-li maximalizovat využití výpočetních prostředků, nastavte cílový počet uzlů na konci úlohy na hodnotu nula, ale povolte dokončení spouštěných úkolů.

## <a name="task-scheduling-policy"></a>Zásady plánování úkolů

Možnost konfigurace [maximálního počtu úkolů na uzel](batch-parallel-node-tasks.md) určuje maximální počet úkolů, které lze spustit souběžně na jednotlivých výpočetních uzlech v rámci fondu.

Výchozí konfigurace určuje, že na uzlu běží současně jen jeden úkol, ale existují scénáře, kdy je výhodné mít na uzlu spuštěno ve stejnou dobu dva a více úkolů. Jaké výhody vám může přinést více úkolů na jednom uzlu je popsáno v části [příklad scénáře](batch-parallel-node-tasks.md#example-scenario) v článku [souběžné úkoly na uzlu](batch-parallel-node-tasks.md).

Můžete také zadat *Typ výplně*, který určuje, zda dávka rovnoměrně rozšíří úlohy napříč všemi uzly ve fondu, nebo sbalí každý uzel s maximálním počtem úkolů před přiřazením úkolů jinému uzlu.

## <a name="communication-status"></a>Stav komunikace

Ve většině scénářů pracují úkoly nezávisle a nepotřebují mezi sebou komunikovat. Existují však aplikace, ve kterých úkoly musí komunikovat, například [scénáře MPI](batch-mpi.md).

Fond můžete nakonfigurovat tak, aby umožňoval **komunikaci mezi uzly** , aby uzly v rámci fondu mohly komunikovat za běhu. Pokud je komunikace mezi uzly povolena, uzly ve fondech z konfigurace služby Cloud Services mohou vzájemně komunikovat na portech vyšších než 1100. Fondy z konfigurace virtuálního počítače neomezují provoz na žádném portu.

Povolení komunikace mezi uzly má vliv i na umístění uzlů v clusterech a může omezit maximální počet uzlů ve fondu z důvodu omezení nasazení. Pokud aplikace nevyžaduje komunikaci mezi uzly, služba Batch můžete fondu přidělit potenciálně velký počet uzlů z mnoha různých clusterů a datových center, aby umožnila zvýšený výkon paralelního zpracování.

## <a name="start-tasks"></a>Spustit úlohy

V případě potřeby můžete přidat [spouštěcí úkol](jobs-and-tasks.md#start-task) , který se spustí na každém uzlu, když se tento uzel připojí k fondu, a pokaždé, když se uzel restartuje nebo obnoví z image. Spouštěcí úkol je obzvlášť užitečný pro přípravu výpočetních uzlů k provádění úkolů, jako je instalace aplikací, které budou vaše úkoly na výpočetních uzlech spouštět.

## <a name="application-packages"></a>Balíčky aplikací

Můžete určit balíčky aplikací pro nasazení do výpočetních uzlů ve fondu. Balíčky aplikací poskytují možnost zjednodušeného nasazení a správy verzí aplikace, která provádí vaše úlohy. Balíčky aplikací, které pro fond určíte, se nainstalují na každý uzel, který se do daného fondu připojí, a také pokaždé, když se uzel restartuje nebo obnoví z image.

Další informace o používání balíčků aplikací k nasazení aplikací do uzlů služby Batch najdete v tématu [Nasazení aplikací do výpočetních uzlů pomocí balíčků aplikací služby Batch](batch-application-packages.md).

## <a name="virtual-network-vnet-and-firewall-configuration"></a>Konfigurace virtuální sítě a brány firewall

Při zřizování fondu výpočetních uzlů ve službě Batch můžete k fondu přidružit podsíť [virtuální sítě](../virtual-network/virtual-networks-overview.md) Azure. Pokud chcete použít virtuální síť Azure klientské rozhraní API služby Batch musí používat ověřování pomocí Azure Active Directory (AD). Podpora služby Azure AD ve službě Azure Batch je zdokumentovaná v tématu [Ověřování řešení služby Batch pomocí Active Directory](batch-aad-auth.md).

### <a name="vnet-requirements"></a>Požadavky na virtuální síť

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

Další informace o nastavení fondu Batch ve virtuální síti najdete v tématu [Vytvoření fondu virtuálních počítačů s vlastní virtuální sítí](batch-virtual-network.md).

> [!TIP]
> Aby se zajistilo, že se veřejné IP adresy používané pro přístup k uzlům nemění, můžete [vytvořit fond s určenými veřejnými IP adresami, které ovládáte](create-pool-public-ip.md).

## <a name="pool-and-compute-node-lifetime"></a>Životnost fondu a výpočetního uzlu

Při návrhu řešení Azure Batch musíte určit, jak a kdy se mají fondy vytvářet a jak dlouho jsou výpočetní uzly v rámci těchto fondů udržovány dostupné.

Na jednom konci spektra můžete vytvořit fond pro každou úlohu, kterou odešlete, a odstranit jej, jakmile se dokončí provádění příslušných úkolů. Tím se maximalizuje využití, protože uzly jsou přiděleny pouze v případě potřeby a jsou vypnuty, jakmile jsou nečinné. To znamená, že úloha musí čekat, až budou uzly přiděleny. je důležité si uvědomit, že úlohy mají naplánované spuštění, jakmile budou uzly jednotlivě přiděleny a spouštěcí úkol byl dokončen. Služba Batch před přiřazením úkolů k uzlům *nečeká*, až budou všechny uzly v rámci fondu dostupné. Tím je zajištěno maximální využití všech dostupných uzlů.

Na druhém konci spektra, pokud je nejvyšší prioritou okamžité spuštění úloh, můžete fond vytvořit s předstihem, aby jeho uzly byly k dispozici před odesláním úloh. V tomto scénáři se mohou úkoly spustit okamžitě, ale uzly mohou „nečinně sedět“ a čekat na jejich přiřazení.

Kombinovaný přístup se obvykle používá ke zpracování proměnné, ale průběžné načítání. Můžete mít fond, ve kterém je odesláno více úloh, a může škálovat počet uzlů nahoru nebo dolů podle zatížení úlohy. Toto přizpůsobování kapacity můžete provádět reaktivně, na základě aktuálního zatížení, nebo proaktivně, pokud lze zatížení předpovídat. Další informace najdete v tématu [zásady automatického škálování](#automatic-scaling-policy).

## <a name="security-with-certificates"></a>Zabezpečení pomocí certifikátů

Při šifrování nebo dešifrování citlivých informací pro úkoly, jako je klíč pro [účet Azure Storage](accounts.md#azure-storage-accounts), je obvykle třeba použít certifikáty. Z toho důvodu můžete na uzly nainstalovat certifikáty. Šifrované tajné klíče jsou předány na úkoly prostřednictvím parametrů příkazového řádku nebo vložené v jednom prostředků úkolu a nainstalované certifikáty lze použít pro jejich dešifrování.

K přidání certifikátu do účtu služby Batch můžete použít operaci [Přidat certifikát](/rest/api/batchservice/certificate/add) (Batch REST) nebo metodu [CertificateOperations.CreateCertificate](/dotnet/api/microsoft.azure.batch.certificateoperations) (Batch .NET). Poté můžete certifikát přidružit k novému nebo existujícímu fondu.

Pokud je certifikát přidružený k fondu, služba Batch nainstaluje certifikát na každý uzel ve fondu. Služba Batch nainstaluje příslušné certifikáty při spuštění uzlu před spuštěním jakékoli úlohy (včetně úlohy [Spustit úlohu](jobs-and-tasks.md#start-task) a [Správce úloh](jobs-and-tasks.md#job-manager-task)).

Pokud přidáváte certifikát do existujícího fondu, musíte restartovat jeho výpočetní uzly, aby se certifikát na uzlech používal.

## <a name="next-steps"></a>Další kroky

- Seznamte [se s úlohami a](jobs-and-tasks.md)úlohami.
