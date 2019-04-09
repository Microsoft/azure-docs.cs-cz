---
title: Velké instance Azure HANA řídit prostřednictvím webu Azure portal | Dokumentace Microsoftu
description: Popisuje, jak způsob, jak můžete identifikovat a pracovat s velkých instancích HANA Azure prostřednictvím portálu
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/02/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8240308b3e0955b1d4d3ef2e82cad215daf95b00
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009364"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Řízení velkých instancí Azure HANA prostřednictvím webu Azure Portal
Tento dokument popisuje způsob, jak [velkých instancích HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) jsou uvedeny v [webu Azure portal](https://portal.azure.com) a jaké činnosti mohou být prováděna prostřednictvím webu Azure portal s jednotkami velká Instance HANA, které jsou pro vás nasazeny. Viditelnost velkých instancích HANA na webu Azure portal je k dispozici prostřednictvím poskytovatele prostředků Azure pro velké instance HANA, která je aktuálně ve verzi public preview

## <a name="register-hana-large-instance-resource-provider"></a>Registrace poskytovatele prostředků velká Instance HANA
Vaše předplatné Azure, které jste používali pro velké Instance HANA nasazení je obvykle zaregistrované u poskytovatele prostředků HANA velké Instance. Ale pokud nevidíte, že jste nasadili velké Instance HANA jednotky, byste měli zaregistrovat poskytovatele prostředků ve vašem předplatném Azure. Existují dva způsoby, jak při registraci poskytovatele prostředků velké Instance HANA

### <a name="register-through-cli-interface"></a>Registrace přes rozhraní příkazového řádku
Musíte být přihlášení na vaše předplatné Azure použité pro nasazení velká Instance HANA přes rozhraní příkazového řádku Azure. Můžete (re) registraci poskytovatele velké Instance HANA pomocí tohoto příkazu:
    
    az provider register --namespace Microsoft.HanaOnAzure

Další informace najdete v článku [poskytovatelé a typy prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli)


### <a name="register-through-azure-portal"></a>Registrace pomocí webu Azure portal
Je to možné (re) registraci HANA velké Instance poskytovatele prostředků pomocí webu Azure portal. Budete muset seznamu vaše předplatné na webu Azure portal a dvakrát klikněte na předplatné, která byla použita k nasazení jednotek vaší velká Instance HANA. Jeden, ke kterému jste na stránce přehled vašeho předplatného vyberte "Poskytovatelů prostředků", jak je znázorněno níže a v okně hledání zadejte "HANA". 

![Zaregistrovat poskytovatele prostředků HLI prostřednictvím webu Azure portal](./media/hana-li-portal/portal-register-hli-rp.png)

Na snímku obrazovky je znázorněno byl už zaregistrovaný poskytovatel prostředků. V případě, že ještě není zaregistrovaný poskytovatel prostředků, stiskněte "přeregistrovat" nebo "register".

Další informace najdete v článku [poskytovatelé a typy prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell)


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Zobrazení jednotky velká Instance HANA na webu Azure Portal
Při odesílání požadavku nasazení velká Instance HANA, zobrazí se výzva k určení, které se připojujete k velké instance HANA i předplatné Azure. Se doporučuje, používat stejné předplatné, které používáte k nasazení, který funguje na jednotky velká Instance HANA aplikační vrstvě SAP.
Jako první se získávání nasazují velké instance HANA, nový [skupiny prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) se vytvoří v předplatném Azure, které jste odeslali v žádosti o nasazení pro váš velkých instancích HANA.  Nová skupina prostředků se zobrazí seznam všech vašich jednotek velká Instance HANA, které jste nasadili v rámci konkrétního předplatného.

Pokud chcete zjistit novou skupinu prostředků Azure, můžete seznam skupiny prostředků ve vašem předplatném tak, že přejdete do levého navigačního podokna na webu Azure portal

![Podokno navigace na webu Azure portal](./media/hana-li-portal/portal-resource-group.png)

V seznamu skupin prostředků můžete se dostat na seznam, možná budete muset vyfiltrujete předplatné, které jste použili jste nasadili velké instance HANA

![Filtrování skupin prostředků na webu Azure portal](./media/hana-li-portal/portal-filtering-subscription.png)

Po filtrování se můžete správné předplatné, stále může mít dlouhý seznam skupin prostředků. Vyhledejte jednu se po opravě z **- Txxx** tam, kde "xxx" jsou tři číslice, jako jsou **-T050**. 

Jak jste zjistili, skupinu prostředků, uveďte podrobnosti ho. Seznam, který jste dostali může vypadat:

![Seznam HLI na webu Azure portal](./media/hana-li-portal/portal-hli-units-list.png)

Všechny jednotky uvedené představují jednu jednotku velká Instance HANA, který byl nasazen v rámci vašeho předplatného. V takovém případě lze najít v osmi různých velká Instance HANA jednotkách, které byly nasazeny v rámci vašeho předplatného.

Pokud jste nasadili několik tenantů velká Instance HANA pod stejné předplatné Azure, zjistíte více skupin prostředků Azure 


## <a name="look-at-attributes-of-single-hli-unit"></a>Podívejte se na jednu jednotku HLI atributy
V seznamu jednotek velká Instance HANA můžete kliknout na jednu jednotku a získat podrobné informace o jednu jednotku velká Instance HANA. 

Na obrazovce Přehled získáte prezentace jednotky, který bude vypadat takto:

![Zobrazit přehled HLI jednotky](./media/hana-li-portal/portal-show-overview.png)

Pohledu na různé atributy, které jsou zobrazeny, tyto atributy vypadat po liší od atributů virtuálního počítače Azure. Na levé straně záhlaví ukazuje skupinu prostředků, oblast Azure, název předplatného a ID jakož i několik značek, které jste přidali. Velká Instance HANA jednotky mají ve výchozím nastavení žádné značky přiřazeny. Na pravé straně záhlaví je uveden název jednotky jako při nasazení bylo provedeno přiřazen. Operační systém se zobrazuje i IP adresu. Jako s virtuálními počítači HANA velké instance jednotku zadejte pomocí počet procesorů, vlákna a paměti se zobrazí také. Další informace o různých jednotek velká Instance HANA, můžete vidět tady:

- [Dostupné skladové položky pro HLI](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [Architektura úložiště SAP HANA (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

Další pole v pravém sloupci hlavičky informuje o stavu napájení HANA velké instance jednotky.

> [!NOTE]
> Stav napájení popisuje, zda jednotku hardwaru využívá k zapnutí nebo vypnutí. Informace o operačním systému se nahoru a spouštění poskytnout. Během restartování jednotka velká Instance HANA bude probíhat (krátkodobé používání) čas, kdy se stav jednotky změní na **počáteční** k přesunutí do stavu **spuštěno**. Ve stavu **spuštěno** znamená, že se spouští operační systém nebo, že operační systém se zahájilo úplně. V důsledku toho po restartu jednotky, nemůžete očekávat, že okamžitě přihlásit do jednotky, poté, co se přepne do stavu **spuštěno**.
> 


## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Kontrola činností celek velká Instance HANA 
Nad rámec poskytuje základní informace o jednotkách velká Instance HANA, můžete zkontrolovat aktivity konkrétní jednotce. Protokol aktivit může vypadat:

![Podokno navigace na webu Azure portal](./media/hana-li-portal/portal-activity-list.png)

Mezi hlavní činnosti zaznamenané jsou jednotky se restartuje. Data uvedená obsahují stav aktivity, časové razítko je teď aktivuje aktivitu, ID předplatného z který aktivita obdržela aktivuje a Azure uživatele, který aktivoval aktivity. 

Jiné aktivity, které se nahrávají se změny jednotek v Azure metadata. Kromě restart zahájen, můžete vidět aktivitu **zápisu HANAInstances**. Tento typ aktivity provádí velká Instance HANA jednotku, samotného žádné změny, ale je jak dokumentovat změny metadat jednotky v Azure. V případě uvedené jsme přidali a odstranit značku (viz další část).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Přidat a odstranit značku Azure na jednotku velká Instance HANA
Další možností je nutné je přidat [značka](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) na jednotku velká Instance HANA. Stejným způsobem jako získávání přiřazují značky se neliší od přiřazení značek k virtuálním počítačům. S virtuálními počítači značky existují v Azure metadat a pro velké instance HANA, jako mají stejná omezení jako značky pro virtuální počítače.

Odstranění značek funguje stejným způsobem jako s virtuálními počítači. Zobrazí se obě aktivity, použití a odstranění značky v protokolu aktivit se konkrétní jednotka velká Instance HANA.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>Zkontrolujte vlastnosti jednotky velká Instance HANA
V části **vlastnosti** obsahuje důležité informace, které získáte, když jsou instance předán vám. Je oddíl, kde získáte všechny informace, které by mohly vyžadovat podporu případů nebo který budete potřebovat při nastavování konfigurace úložiště snímků. V důsledku této části je kolekce dat kolem instanci, připojení k instanci Azure a back-endu úložiště. Horní části vypadá takto:


![horní část HLI vlastnosti na webu Azure portal](./media/hana-li-portal/portal-properties-top.png)

První několik položek dat, jste viděli v okně Přehled již. Ale důležité část dat je ID okruh ExpressRoute, které jste získali jako první nasazené jednotky byly předán. V některých případech podpory se můžou získat dotaz pro tato data. Položka důležitá data se zobrazí v dolní části na snímku obrazovky. Data zobrazená je IP adresa hlavní úložiště systému souborů NFS, který izoluje úložiště vašeho **tenanta** v zásobníku velká Instance HANA. Tato IP adresa je také potřeba při úpravách [konfigurační soubor pro ukládání snímků zálohy](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots). 

Jak se posunete dolů v podokně vlastností, získáte další data, jako je Identifikátor jedinečný prostředek pro velké Instance HANA jednotky nebo ID předplatného, které bylo přiřazeno k nasazení.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Restartujte jednotku velká Instance HANA prostřednictvím webu Azure portal
Zahajuje se restartování operačního systému Linux, byly různých situacích, kdy operační systém nelze dokončit restartování úspěšně. Pokud chcete vynutit restartování, potřebujete k otevření žádosti o službu má Microsoft operations provést restart power jednotky velká Instance HANA. Funkce power restartování jednotky velká Instance HANA je teď integrovaná v na webu Azure portal. Pokud jste v části Přehled velká Instance HANA jednotky, se zobrazí tlačítko pro restartování nad datové části

![Restartujte krok #1 na webu Azure portal](./media/hana-li-portal/portal-restart-first-step.png)

Jako jsou stisknutí tlačítka restartování, zobrazí se výzva, jestli Opravdu chcete restartovat jednotky. Jak můžete to potvrďte klepnutím na tlačítko "Ano", jednotka se restartuje.

> [!NOTE]
> Probíhá restartování, bude probíhat (krátkodobé používání) čas, kdy se stav jednotky změní na **počáteční** k přesunutí do stavu **spuštěno**. Ve stavu **spuštěno** znamená, že se spouští operační systém nebo, že operační systém se zahájilo úplně. V důsledku toho po restartu jednotky, nemůžete očekávat, že okamžitě přihlásit do jednotky, poté, co se přepne do stavu **spuštěno**.

> [!IMPORTANT]
> Závisí na množství paměti v jednotky velká Instance HANA, restartování a restartování hardwaru a operačního systému může trvat až jednu hodinu


## <a name="open-a-support-request-for-hana-large-instances"></a>Žádost o podporu pro velké instance HANA
Z portálu Azure zobrazení velká Instance HANA jednotek můžete vytvořit žádosti o podporu speciálně pro HANA velké Instance jednotka také. Jak budete postupovat podle odkazu **nová žádost o podporu** 

![Spustit krok žádosti o služby #1 na webu Azure portal](./media/hana-li-portal/portal-initiate-support-request.png)

Zajistí služba programu velké instance SAP HANA uvedené na další obrazovce, musíte vybrat "všechny služby" jak je znázorněno níže

![Vyberte všechny služby na webu Azure portal](./media/hana-li-portal/portal-create-service-request.png)

V seznamu služeb můžete najít službu **velkou instanci SAP HANA**. Při výběru této služby, můžete vybrat konkrétní problém typy, jak je znázorněno:


![Vyberte třídu problém na webu Azure portal](./media/hana-li-portal/portal-select-problem-class.png)

V rámci jednotlivých typů jiný problém dostanete výběr podtypy problému je nutné vybrat, chcete-li charakterizovat váš problém dále. Po výběru podtyp, teď můžete pojmenovat předmět. Po dokončení procesu výběru můžete přesunout k dalšímu kroku vytvoření. V **řešení** části jste odkázáno dokumentaci kolem velkých instancích HANA, které poskytují ukazatele na řešení problému. Pokud nemůžete najít řešení pro váš problém v dokumentaci k navrhované, přejdete k dalšímu kroku. V dalším kroku budete vyzváni, zda je problém s virtuálními počítači nebo s jednotkami velká Instance HANA. Tyto informace pomáhají směrovat žádosti o podporu o správné odborníky. 

![Podrobnosti o případu podpory na webu Azure portal](./media/hana-li-portal/portal-support-request-details.png)

Jak jste odpověděli na otázky a poskytuje další podrobnosti, můžete přejít na další krok k žádosti o podporu a odeslat ji.

## <a name="next-steps"></a>Další postup

- [Jak monitorovat SAP HANA (velké instance) v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [Sledování a řešení potíží na straně HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)

