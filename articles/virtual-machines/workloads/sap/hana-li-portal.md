---
title: Řízení velkých instancí Azure HANA prostřednictvím portálu Azure | Dokumenty společnosti Microsoft
description: Popisuje způsob, jakým můžete identifikovat velké instance Azure HANA a pracovat s nimi prostřednictvím portálu.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c14ff9c4f6d2bc2b1a62d1874d01950d09491c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70099815"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Řízení velkých instancí Azure HANA prostřednictvím webu Azure Portal
Tento dokument popisuje způsob, jakým [hana velké instance](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) jsou prezentovány na [webu Azure Portal](https://portal.azure.com) a jaké aktivity lze provádět prostřednictvím portálu Azure s hana velké instance jednotky, které jsou nasazeny za vás. Viditelnost velkých instancí HANA na portálu Azure je poskytovaná prostřednictvím poskytovatele prostředků Azure pro velké instance HANA, který je momentálně ve verzi Public Preview.

## <a name="register-hana-large-instance-resource-provider"></a>Zaregistrovat zprostředkovatele prostředků velké instance HANA
Vaše předplatné Azure, které jste používali pro nasazení velké instance HANA, je obvykle registrované pro zprostředkovatele prostředků velké instance HANA. Pokud ale nevidíte nasazené jednotky velké instance HANA, měli byste zaregistrovat poskytovatele prostředků ve vašem předplatném Azure. Existují dva způsoby registrace zprostředkovatele prostředků velké instance HANA

### <a name="register-through-cli-interface"></a>Registrace prostřednictvím rozhraní ROZHRANÍ ROZHRANÍ ROZHRANÍ
Musíte být přihlášeni k předplatnému Azure, který se používá pro nasazení velké instance HANA prostřednictvím rozhraní Azure CLI. Pomocí tohoto příkazu můžete (znovu)zaregistrovat zprostředkovatele velké instance HANA:
    
    az provider register --namespace Microsoft.HanaOnAzure

Další informace najdete v článku [Poskytovatelé a typy prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli)


### <a name="register-through-azure-portal"></a>Registrace přes portál Azure
Zprostředkovatele prostředků velké instance HANA můžete (znovu) zaregistrovat prostřednictvím portálu Azure. Musíte uvést své předplatné na webu Azure Portal a poklepat na předplatné, které se používá k nasazení jednotek velké instance HANA. Jeden jste na stránce přehled u předplatného, vyberte "Zprostředkovatelé prostředků", jak je znázorněno níže a zadejte "HANA" do vyhledávacího okna. 

![Registrace HLI RP prostřednictvím portálu Azure](./media/hana-li-portal/portal-register-hli-rp.png)

Na zobrazeném snímku obrazovky byl poskytovatel prostředků již zaregistrován. V případě, že poskytovatel prostředků ještě není registrován, stiskněte tlačítko "re-register" nebo "register".

Další informace najdete v článku [Poskytovatelé a typy prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell)


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Zobrazení jednotek velké instance HANA na webu Azure Portal
Při odesílání požadavku na nasazení velké instance HANA budete vyzváni k zadání předplatného Azure, které se připojujete k velkým instancím HANA. Doporučujeme použít stejné předplatné, které používáte k nasazení aplikační vrstvy SAP, která funguje proti jednotkám velké instance HANA.
Při nasazování vašich prvních velkých instancí HANA se vytvoří nová [skupina prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) v předplaceně Azure, které jste odeslali v žádosti o nasazení pro velké instance HANA.  Nová skupina prostředků zobrazí seznam všech jednotek velké instance HANA, které jste nasadili v konkrétním předplatném.

Chcete-li najít novou skupinu prostředků Azure, uveďte skupinu prostředků ve vašem předplatném procházením levého navigačního podokna portálu Azure Portal.

![Navigační podokno na webu Azure Portal](./media/hana-li-portal/portal-resource-group.png)

V seznamu skupin prostředků, které jsou uvedeny, možná budete muset filtrovat na předplatné, které jste použili hana velké instance nasazeny

![Filtrování skupin prostředků na webu Azure Portal](./media/hana-li-portal/portal-filtering-subscription.png)

Po filtrování na správné předplatné, stále může mít dlouhý seznam skupin prostředků. Podívejte se na jeden s post-fix **-Txxx,** kde "xxx" jsou tři číslice, jako **-T050**. 

Při nalezení skupiny prostředků uveďte podrobnosti. Seznam, který jste obdrželi, může vypadat takto:

![Seznam HLI na webu Azure Portal](./media/hana-li-portal/portal-hli-units-list.png)

Všechny uvedené jednotky představují jednu jednotku velké instance HANA, která byla nasazena ve vašem předplatném. V takovém případě se podíváte na osm různých jednotek velké instance HANA, které byly nasazeny ve vašem předplatném.

Pokud jste nasadili několik tenantů velké instance HANA v rámci stejného předplatného Azure, najdete několik skupin prostředků Azure 


## <a name="look-at-attributes-of-single-hli-unit"></a>Podívejte se na atributy jediné jednotky HLI
V seznamu jednotek velké instance HANA můžete kliknout na jednu jednotku a získat podrobnosti o jedné jednotce velké instance HANA. 

Na obrazovce s přehledem se po kliknutí na tlačítko Zobrazit více zobrazuje prezentace jednotky, která vypadá takto:

![Zobrazit přehled jednotky HLI](./media/hana-li-portal/portal-show-overview.png)

Při pohledu na různé zobrazené atributy tyto atributy vypadají sotva jinak než atributy virtuálních virtuálních počítačů Azure. V záhlaví na levé straně se zobrazí skupina prostředků, oblast Azure, název předplatného a ID a také některé značky, které jste přidali. Ve výchozím nastavení nemají jednotky velké instance HANA přiřazeny žádné značky. Na pravé straně záhlaví je název jednotky uveden jako přiřazený při dokončení nasazení. Operační systém je zobrazen, stejně jako IP adresa. Stejně jako u virtuálních počítače hana velké instance typ jednotky s počtem podprocesů procesoru a paměti je zobrazen také. Další podrobnosti o různých jednotkách velké instance HANA jsou uvedeny zde:

- [Dostupné skladové položky pro HLI](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [Architektura úložiště SAP HANA (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

Další data na pravé dolní straně je revize razítka velké instance HANA. Možné hodnoty:

- Revize 3
- Revize 4

Revize 4 je nejnovější architektura uvolněná hana velké instance s hlavními vylepšeními latence sítě mezi virtuálními počítači Azure a HANA velké instance jednotky nasazené v revizi 4 razítka nebo řádky.
Další velmi důležité informace se nachází v pravém dolním rohu přehledu s názvem skupiny umístění bezkontaktní komunikace Azure, která se automaticky vytvoří pro každou nasazenou jednotku velké instance HANA. Tato skupina umístění bezkontaktní komunikace je třeba odkazovat při nasazování virtuálních počítačích Azure, které jsou hostitelem aplikační vrstvy SAP. Pomocí [skupiny umístění bezkontaktní komunikace Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) přidružené k jednotce velké instance HANA se ujistěte, že virtuální počítače Azure jsou nasazené v těsné blízkosti jednotky velké instance HANA. Způsob, jakým lze skupiny umístění bezkontaktní komunikace použít k vyhledání aplikační vrstvy SAP ve stejném datovém centru Azure jako hostované jednotky hana velké instance revize 4, je popsán ve [skupinách umístění Azure Proximity Placement Pro optimální latenci sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md).

Další pole v pravém sloupci záhlaví informuje o stavu napájení jednotky velké instance HANA.

> [!NOTE]
> Stav napájení popisuje, zda je hardwarová jednotka zapnuta nebo vypnuta. Neposkytuje informace o operačním systému, který je v provozu. Při restartování jednotky velké instance HANA dojde k malé době, kdy se stav jednotky změní na **Začíná se** přesunout do stavu **Spuštěno**. Být ve stavu **Spuštěno** znamená, že operační ho spouští nebo že operační ho byl spuštěn úplně. V důsledku toho nelze po restartování jednotky očekávat, že se okamžitě přihlásíte k jednotce, jakmile se stav přepne na **Spuštěno**.
> 

Pokud stisknete tlačítko "Zobrazit více", zobrazí se další informace. Jedna další informace zobrazuje revizi razítka velké instance HANA, jednotka byla nasazena. Podívejte se na článek [Co je SAP HANA v Azure (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) pro různé revize razítek velké instance HANA

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Kontrola aktivit jedné jednotky velké instance HANA 
Kromě přehledu jednotek velké instance HANA můžete zkontrolovat aktivity konkrétní jednotky. Protokol aktivit může vypadat takto:

![Navigační podokno na webu Azure Portal](./media/hana-li-portal/portal-activity-list.png)

Jednou z hlavních zaznamenaných činností jsou restartování jednotky. Uvedená data zahrnují stav aktivity, časové razítko, které se aktivovala, ID předplatného, ze kterého se aktivita aktivovala, a uživatele Azure, který aktivitu spustil. 

Další aktivita, která se zaznamenává, jsou změny jednotky v meta datech Azure. Kromě zahájení restartování můžete zobrazit aktivitu **Write HANAInstances**. Tento typ aktivity neprovádí žádné změny na samotné jednotce velké instance HANA, ale dokumentuje změny meta dat jednotky v Azure. V uvedeném případě jsme přidali a odstranili značku (viz další část).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Přidání a odstranění značky Azure do jednotky velké instance HANA
Další možností, kterou máte, je přidání [značky](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) do jednotky velké instance HANA. Způsob, jakým jsou značky stále přiřazovány, se neliší od přiřazování značek virtuálním kvitům. Stejně jako u virtuálních počítačů existují značky v meta datech Azure a pro velké instance HANA mají stejná omezení jako značky pro virtuální počítače.

Odstranění značek funguje stejným způsobem jako u virtuálních měn. Obě aktivity, použití a odstranění značky budou uvedeny v protokolu aktivit konkrétní jednotky velké instance HANA.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>Kontrola vlastností jednotky velké instance HANA
Oddíl **Vlastnosti** obsahuje důležité informace, které získáte, když jsou vám instance předány. Jedná se o část, kde získáte všechny informace, které můžete potřebovat v případech podpory nebo které potřebujete při nastavování konfigurace snímku úložiště. Jako taková tato část je kolekce dat kolem instance, připojení instance k Azure a back-end úložiště. Horní část sekce vypadá takto:


![horní část vlastností HLI na webu Azure Portal](./media/hana-li-portal/portal-properties-top.png)

Prvních několik datových položek, které jste viděli na obrazovce přehledu již. Ale důležitou část dat je ExpressRoute Circuit ID, které jste získali jako první nasazené jednotky byly předány. V některých případech podpory můžete být požádáni o tato data. V dolní části snímku obrazovky se zobrazí důležitá položka dat. Zobrazená data je IP adresa vedoucíúložiště nfs, která izoluje úložiště do **vašeho tenanta** v zásobníku velké instance HANA. Tato adresa IP je také potřebná při úpravě [konfiguračního souboru pro zálohování snímků úložiště](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots). 

Při posouvání dolů v podokně vlastností získáte další data, jako je jedinečné ID prostředku pro jednotku velké instance HANA nebo ID předplatného, které bylo přiřazeno k nasazení.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Restartování jednotky velké instance HANA prostřednictvím portálu Azure
Zahájení restartování operačního systému Linux, tam byly různé situace, kdy OS nemohl dokončit restart úspěšně. Chcete-li vynutit restartování, je nutné otevřít požadavek na službu, aby operace společnosti Microsoft provedly restartování jednotky velké instance HANA. Funkce restartování napájení jednotky velké instance HANA je teď integrovaná do portálu Azure. Jak jste v části přehledu jednotky Velké instance HANA, zobrazí se tlačítko pro restartování v horní části datové části

![Krok #1 restartování na webu Azure Portal](./media/hana-li-portal/portal-restart-first-step.png)

Při stisknutí tlačítka restart budete dotázáni, zda opravdu chcete jednotku restartovat. Jak potvrzujete stisknutím tlačítka "Ano", jednotka se restartuje.

> [!NOTE]
> V procesu restartování dojde k malé době, kdy se stav jednotky změní na **Začíná** se přesunout do stavu **Spuštěno**. Být ve stavu **Spuštěno** znamená, že operační ho spouští nebo že operační ho byl spuštěn úplně. V důsledku toho nelze po restartování jednotky očekávat, že se okamžitě přihlásíte k jednotce, jakmile se stav přepne na **Spuštěno**.

> [!IMPORTANT]
> V závislosti na velikosti paměti v jednotce velké instance HANA může restartování a restartování hardwaru a operačního systému trvat až jednu hodinu.


## <a name="open-a-support-request-for-hana-large-instances"></a>Otevření žádosti o podporu pro velké instance HANA
Z portálu Azure zobrazení jednotek velké instance HANA, můžete vytvořit požadavky na podporu speciálně pro jednotku velké instance HANA také. Jak budete postupovat podle odkazu **Nová žádost o podporu** 

![krok #1 iniciovat požadavek na službu na webu Azure Portal](./media/hana-li-portal/portal-initiate-support-request.png)

Chcete-li získat služby velkých instancí SAP HANA uvedené na další obrazovce, možná budete muset vybrat "Všechny služby", jak je znázorněno níže

![Výběr všech služeb na Webu Azure Portal](./media/hana-li-portal/portal-create-service-request.png)

V seznamu služeb najdete službu **SAP HANA Large Instance**. Při výběru této služby můžete vybrat konkrétní typy problémů, jak je znázorněno:


![Vybrat třídu problému na webu Azure Portal](./media/hana-li-portal/portal-select-problem-class.png)

V rámci každého z různých typů problémů je vám nabídnut výběr problémových podtypů, které je třeba vybrat, abyste problém dále charakterizovali. Po výběru podtypu můžete předmět pojmenovat. Po dokončení procesu výběru můžete přejít k dalšímu kroku vytvoření. V části **Řešení** se odkazuje na dokumentaci kolem hana velké instance, které mohou poskytnout ukazatel na řešení vašeho problému. Pokud v navrhované dokumentaci nemůžete najít řešení problému, přejděte k dalšímu kroku. V dalším kroku budete dotázáni, zda je problém s virtuálními virtuálními soudy nebo s jednotkami velké instance HANA. Tyto informace pomáhají nasměrovat žádost o podporu na správné specialisty. 

![Podrobnosti o případu podpory na webu Azure Portal](./media/hana-li-portal/portal-support-request-details.png)

Jak jste odpověděli na otázky a poskytli další podrobnosti, můžete přejít k dalšímu kroku, abyste si mohli zkontrolovat žádost o podporu a odeslat ji.

## <a name="next-steps"></a>Další kroky

- [Jak sledovat SAP HANA (velké instance) v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [Sledování a řešení potíží na straně HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)

