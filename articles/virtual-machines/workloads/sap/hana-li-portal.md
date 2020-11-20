---
title: Správa velkých instancí Azure HANA prostřednictvím Azure Portal | Microsoft Docs
description: Popisuje způsob, jakým můžete pomocí portálu identifikovat a pracovat s velkými instancemi Azure HANA.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 550e22ac861b92994f2695594d09fc2935d273d1
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967750"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Řízení velkých instancí Azure HANA prostřednictvím webu Azure Portal
Tento dokument popisuje způsob, jakým se v [Azure Portal](https://portal.azure.com) prezentují [velké instance Hana](./hana-overview-architecture.md) a jaké aktivity je možné provádět prostřednictvím Azure Portal s velkými jednotkami instancí Hana, které jsou pro vás nasazené. Viditelnost velkých instancí HANA v Azure Portal k dispozici prostřednictvím poskytovatele prostředků Azure pro velké instance HANA, který je aktuálně ve verzi Public Preview.

## <a name="register-hana-large-instance-resource-provider"></a>Registrovat poskytovatele prostředků velké instance HANA
U vašeho předplatného Azure, které jste použili pro nasazení rozsáhlých instancí HANA, je obvykle zaregistrované pro poskytovatele prostředků rozsáhlých instancí HANA. Pokud ale nevidíte, že jste nasadili velké jednotky instancí HANA, měli byste zaregistrovat poskytovatele prostředků ve vašem předplatném Azure. Existují dva způsoby, jak registrovat poskytovatele prostředků velké instance HANA.

### <a name="register-through-cli-interface"></a>Registrovat přes rozhraní CLI
Musíte být přihlášeni k předplatnému Azure, které se používá pro nasazení rozsáhlých instancí HANA prostřednictvím rozhraní Azure CLI. Pomocí tohoto příkazu můžete (znovu) zaregistrovat poskytovatele velkých instancí HANA:
    
```azurecli
az provider register --namespace Microsoft.HanaOnAzure
```

Další informace najdete v článku [poskytovatelé a typy prostředků Azure](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli) .


### <a name="register-through-azure-portal"></a>Registrovat prostřednictvím Azure Portal
Můžete (znovu) zaregistrovat poskytovatele prostředků velké instance HANA prostřednictvím Azure Portal. Musíte si vypsat předplatné v Azure Portal a dvakrát kliknout na předplatné, které se použilo k nasazení velkých jednotek instancí HANA. Na stránce Přehled vašeho předplatného vyberte "poskytovatelé prostředků", jak vidíte níže, a do okna hledání zadejte "HANA". 

![Registrovat HLI RP prostřednictvím Azure Portal](./media/hana-li-portal/portal-register-hli-rp.png)

Na zobrazeném snímku obrazovky již byl zaregistrován poskytovatel prostředků. V případě, že poskytovatel prostředků ještě není zaregistrovaný, stiskněte znovu registrovat nebo zaregistrovat.

Další informace najdete v článku [poskytovatelé a typy prostředků Azure](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) .


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Zobrazení jednotek velkých instancí HANA v Azure Portal
Při odesílání žádosti o nasazení velké instance HANA budete požádáni o zadání předplatného Azure, ke kterému se připojujete i s velkými instancemi HANA. Doporučujeme použít stejné předplatné, které používáte k nasazení vrstvy aplikace SAP, která funguje s jednotkami velkých instancí HANA.
Když se nasadí první velký počet instancí HANA, vytvoří se v předplatném Azure, které jste odeslali v žádosti o nasazení pro velké instance HANA, novou [skupinu prostředků Azure](../../../azure-resource-manager/management/manage-resources-portal.md) .  V nové skupině prostředků se zobrazí seznam všech jednotek velkých instancí služby HANA, které jste nasadili v rámci konkrétního předplatného.

Pokud chcete najít novou skupinu prostředků Azure, vypište skupinu prostředků ve vašem předplatném procházením levého navigačního podokna Azure Portal.

![Snímek obrazovky, který zvýrazní možnost skupiny prostředků.](./media/hana-li-portal/portal-resource-group.png)

V seznamu skupin prostředků, které se zobrazí, možná budete muset filtrovat předplatné, které jste použili k nasazení velkých instancí HANA.

![Filtrovat skupiny prostředků v Azure Portal](./media/hana-li-portal/portal-filtering-subscription.png)

Po filtrování do správného předplatného může být stále dlouhý seznam skupin prostředků. Vyhledejte ho s následnou opravou **-TXXX** , kde xxx je tři číslice, např. **T050**. 

Jak jste našli skupinu prostředků, uveďte podrobnosti. Seznam, který jste dostali, může vypadat takto:

![Seznam HLI v Azure Portal](./media/hana-li-portal/portal-hli-units-list.png)

Všechny uvedené jednotky představují jednu jednotku velkých instancí HANA, která byla nasazena v rámci vašeho předplatného. V takovém případě se můžete podívat na osm různých jednotek s velkými instancemi HANA, které byly nasazeny v rámci vašeho předplatného.

Pokud jste v rámci stejného předplatného Azure nasadili několik tenantů rozsáhlých instancí HANA, najdete víc skupin prostředků Azure. 


## <a name="look-at-attributes-of-single-hli-unit"></a>Podívejte se na atributy jedné jednotky HLI.
V seznamu jednotek velkých instancí HANA můžete kliknout na jednu jednotku a získat podrobnosti o jedné jednotce velké instance HANA. 

Na obrazovce Přehled se po kliknutí na Zobrazit další zobrazí prezentace jednotky, která vypadá takto:

![Zobrazit přehled jednotky HLI](./media/hana-li-portal/portal-show-overview.png)

V zobrazených různých atributech vypadají tyto atributy špatně, které se liší od atributů virtuálních počítačů Azure. V záhlaví na levé straně se zobrazuje skupina prostředků, oblast Azure, název předplatného a ID a také některé značky, které jste přidali. Ve výchozím nastavení nemají jednotky velkých instancí HANA přiřazenou značku. Na pravé straně hlavičky je název jednotky uveden jako přiřazený po dokončení nasazení. Zobrazí se operační systém i IP adresa. Stejně jako u virtuálních počítačů se zobrazuje i typ jednotky velkých instancí HANA s počtem vláken procesoru a paměti. Další podrobnosti o různých jednotkách velkých instancí HANA jsou uvedené tady:

- [Dostupné skladové položky pro HLI](./hana-available-skus.md)
- [Architektura úložiště SAP HANA (velké instance)](./hana-storage-architecture.md) 

Další data na pravé dolní straně jsou revize razítka velké instance HANA. Možné hodnoty:

- Revize 3
- Revize 4

Revize 4 je nejnovější architektura vydaná pro velké instance HANA, která přináší větší vylepšení latence sítě mezi virtuálními počítači Azure a jednotkami velkých instancí HANA nasazenými v rámci revizních razítek nebo řádků.
Další velmi důležité informace najdete v pravém dolním rohu přehledu s názvem skupiny umístění blízkosti Azure, která se automaticky vytvoří pro každou nasazenou jednotku velkých instancí HANA. Tato skupina umístění blízkosti musí být odkazována při nasazení virtuálních počítačů Azure, které hostují aplikační vrstvu SAP. Pomocí [skupiny umístění blízkosti Azure](../../linux/co-location.md) přidružené k jednotce velkých instancí Hana se ujistěte, že jsou virtuální počítače Azure nasazené v těsné blízkosti jednotky velkých instancí Hana. Způsob, jakým se dají skupiny umístění blízkosti použít k vyhledání vrstvy aplikace SAP ve stejném datovém centru Azure jako revize 4 jednotky velkých instancí HANA, jsou popsané v tématu [skupiny umístění pro zajištění optimální latence sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md).

Další pole v pravém sloupci záhlaví informuje o stavu napájení jednotky velkých instancí HANA.

> [!NOTE]
> Stav napájení popisuje, zda je hardwarová jednotka zapnutá nebo vypnutá. Neposkytuje informace o tom, jak operační systém pracuje a je spuštěný. Při restartu velké jednotky instance HANA se zobrazí malý čas, kdy se stav jednotky změní na **zahájení** přechodu do stavu **zahájeno**. Ve stavu **spuštěno** znamená, že se operační systém spouští nebo že byl operační systém zcela spuštěn. V důsledku toho po restartování jednotky Nemůžete očekávat, že se okamžitě přihlašujete k jednotce, jakmile se stav přepne na **zahájeno**.
> 

Pokud stisknete tlačítko Zobrazit další, zobrazí se další informace. Jednou z dalších informací se zobrazuje revize razítka velké instance HANA, jednotka byla nasazena v. Přečtěte si článek [co je SAP HANA v Azure (velké instance)](./hana-overview-architecture.md) pro různé revize rozsáhlých razítek instancí Hana.

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Kontrolovat aktivity jedné jednotky velkých instancí HANA 
Kromě poskytnutí přehledu jednotek velkých instancí HANA můžete kontrolovat aktivity konkrétní jednotky. Protokol aktivit by mohl vypadat takto:

![Navigační podokno v Azure Portal](./media/hana-li-portal/portal-activity-list.png)

Jedna z hlavních aktivit zaznamenaných je restart jednotky. Uvedená data zahrnují stav aktivity, časové razítko aktivované aktivity, ID předplatného, ze kterého se aktivita aktivovala, a uživatele Azure, který aktivitu aktivoval. 

Další zaznamenaná aktivita se změní na jednotku v metadatech Azure meta. Kromě iniciace restartování můžete zobrazit aktivitu **HANAInstances zápisu**. Tento typ aktivity neprovede žádné změny v samotné jednotce velké instance HANA, ale provádí změny v metadatech jednotky v Azure. V případě uvedeného případu jsme přidali a odstranili značku (viz další oddíl).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Přidání a odstranění značky Azure pro jednotku velkých instancí HANA
Další možností je přidat [značku](../../../azure-resource-manager/management/tag-resources.md) do jednotky velkých instancí Hana. Způsob přiřazování značek se neliší od přiřazení značek k virtuálním počítačům. Stejně jako u virtuálních počítačů existují značky v metadatech Azure a u velkých instancí HANA mají stejná omezení jako značky pro virtuální počítače.

Odstraňování značek funguje stejným způsobem jako u virtuálních počítačů. Obě aktivity, použití a odstranění značky budou uvedeny v protokolu aktivit příslušné jednotky velkých instancí HANA.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>Zkontroluje vlastnosti jednotky velkých instancí HANA.
**Vlastnosti** oddílu obsahují důležité informace, které získáte, když jsou instance předány vám. Je to část, kde získáte všechny informace, které byste mohli vyžadovat v případech podpory nebo které potřebujete při nastavování konfigurace snímku úložiště. Vzhledem k tomu, že tato část je kolekcí dat kolem vaší instance, připojení instance k Azure a back-endu úložiště. Horní část oddílu vypadá takto:


![horní část vlastností HLI v Azure Portal](./media/hana-li-portal/portal-properties-top.png)

První pár datových položek, které jste viděli na obrazovce Přehled, už. Ale důležitou částí dat je ID okruhu ExpressRoute, který jste získali jako první nasazené jednotky, které jste předali. V některých případech podpory se může zobrazit výzva k zadání těchto dat. Důležité zadání dat se zobrazí v dolní části obrazovky. Zobrazená data jsou IP adresa Head úložiště NFS, která izoluje vaše úložiště pro vašeho **tenanta** v zásobníku velkých instancí Hana. Tato IP adresa je potřeba také při úpravách [konfiguračního souboru pro zálohy snímků úložiště](./hana-backup-restore.md#set-up-storage-snapshots). 

Při procházení dolů v podokně vlastností získáte další data, jako je jedinečné ID prostředku pro jednotku velké instance HANA, nebo ID předplatného, které bylo přiřazeno k nasazení.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Restartování velké jednotky instance HANA prostřednictvím Azure Portal
Při inicializaci restartování operačního systému Linux došlo k různým situacím, kdy operační systém nemohl úspěšně dokončit restart. Aby bylo možné vynutit restartování, je nutné otevřít žádost o služby, aby se operace společnosti Microsoft prováděla při napájení jednotky velké instance HANA. Funkce napájení jednotky velkých instancí HANA je teď integrovaná do Azure Portal. Stejně jako část jednotky velkých instancí HANA se zobrazí tlačítko pro restart v části data.

![Restartovat krok #1 v Azure Portal](./media/hana-li-portal/portal-restart-first-step.png)

Po stisknutí tlačítka restartovat se zobrazí dotaz, zda opravdu chcete jednotku restartovat. Jakmile ověříte, že stisknete tlačítko "Ano", jednotka se restartuje.

> [!NOTE]
> V procesu restartování se zobrazí malý čas, kdy se stav jednotky změní na **zahájení** přechodu do stavu **zahájeno**. Ve stavu **spuštěno** znamená, že se operační systém spouští nebo že byl operační systém zcela spuštěn. V důsledku toho po restartování jednotky Nemůžete očekávat, že se okamžitě přihlašujete k jednotce, jakmile se stav přepne na **zahájeno**.

> [!IMPORTANT]
> Závisí na velikosti paměti v jednotce velké instance služby HANA, restartování a restartování hardwaru a operační systém může trvat až jednu hodinu.


## <a name="open-a-support-request-for-hana-large-instances"></a>Otevření žádosti o podporu pro velké instance HANA
Z Azure Portal zobrazení jednotek velkých instancí HANA můžete vytvořit i žádosti o podporu speciálně pro velké jednotky instancí HANA. Při sledování odkazu na **novou žádost o podporu** 

![zahájit krok žádosti o službu #1 v Azure Portal](./media/hana-li-portal/portal-initiate-support-request.png)

Pokud chcete získat službu Velké instance SAP HANA uvedenou na další obrazovce, možná budete muset vybrat všechny služby, jak je znázorněno níže.

![Vybrat všechny služby v Azure Portal](./media/hana-li-portal/portal-create-service-request.png)

V seznamu služeb můžete najít službu **SAP HANA velká instance**. Při výběru této služby můžete vybrat konkrétní typy problémů, jak je znázorněno níže:


![Vyberte třídu problému v Azure Portal](./media/hana-li-portal/portal-select-problem-class.png)

V každém z různých typů problémů jste si nabídli výběr podtypů problémů, které je potřeba vybrat k dalšímu charakterizaci problému. Po výběru podtypu se teď dá předmět pojmenovat. Až budete s procesem výběru hotovi, můžete přejít na další krok vytvoření. V části **řešení** se odkazujete na dokumentaci týkající se velkých instancí Hana, která by mohla poskytnout ukazatel na řešení vašeho problému. Pokud nemůžete najít řešení pro váš problém v navrhované dokumentaci, přejdete k dalšímu kroku. V dalším kroku budete dotázáni, jestli se jedná o problém s virtuálními počítači nebo s jednotkami velkých instancí HANA. Tyto informace pomáhají směrovat žádost o podporu správným specialistům. 

![Podrobnosti případu podpory v Azure Portal](./media/hana-li-portal/portal-support-request-details.png)

Jak jste odpověděli na otázky a poskytli další podrobnosti, můžete přejít k dalšímu kroku, abyste si mohli prohlédnout žádost o podporu a odeslat ji.

## <a name="next-steps"></a>Další kroky

- [Jak monitorovat SAP HANA (velké instance) v Azure](./troubleshooting-monitoring.md)
- [Sledování a řešení potíží na straně HANA](./hana-monitor-troubleshoot.md)
