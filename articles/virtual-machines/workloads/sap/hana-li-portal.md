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
ms.date: 04/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b186aa2692033a774d1d8f294315fcc0f5e874d5
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58763133"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Řízení velkých instancích HANA Azure prostřednictvím webu Azure portal
Tento dokument popisuje způsob, jak [velkých instancích HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) jsou uvedeny v [webu Azure portal](https://portal.azure.com) a jaké činnosti mohou být prováděna prostřednictvím webu Azure portal s jednotkami velká Instance HANA, které jsou pro vás nasazeny. Viditelnost velkých instancích HANA na webu Azure portal je k dispozici prostřednictvím poskytovatele prostředků Azure pro velké instance HANA, která je aktuálně ve verzi public preview

## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Zobrazení jednotky velká Instance HANA na webu Azure Portal
Při odesílání požadavku nasazení velká Instance HANA, zobrazí se výzva k určení, které se připojujete k velké instance HANA i předplatné Azure. Doporučujeme používat stejné předplatné, které používáte k nasazení, který funguje na jednotky velká Instance HANA aplikační vrstvě SAP.
Jako první se získávání nasazují velké instance HANA, nový [skupiny prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) se vytvoří v předplatném Azure, které jste odeslali v žádosti o nasazení pro váš velkých instancích HANA.  Nová skupina prostředků se zobrazí seznam všech vašich jednotek velká Instance HANA, které jste nasadili v rámci konkrétního předplatného.

Pokud chcete zjistit novou skupinu prostředků Azure, můžete seznam skupiny prostředků ve vašem předplatném tak, že přejdete do levého navigačního podokna na webu Azure portal

![Podokno navigace na webu Azure portal](./media/hana-li-portal/portal-resource-group.png)

V seznamu skupin prostředků můžete se dostat na seznam, možná budete muset vyfiltrujete předplatné, které jste použili jste nasadili velké instance HANA

![Filtrování skupin prostředků na webu Azure portal](./media/hana-li-portal/portal-filtering-subscription.png)

Po filtrování se můžete správné předplatné, stále může mít dlouhý seznam skupin prostředků. Vyhledejte jednu se po opravě z **- Txxx** tam, kde "xxx" jsou tři číslice, jako jsou **-T050**. 

Jak jste zjistili, skupinu prostředků, uveďte podrobnosti ho. Seznam, který jste dostali může vypadat:

![Seznam HLI na webu Azure portal](./media/hana-li-portal/portal-hli-units-list.png)

Všechny jednotky uvedené představují jednu jednotku velká Instance HANA, který byl nasazen v rámci vašeho předplatného. V takovém případě lze najít v osmi různých velká Instance HANA jednotkách, které byly nasazeny v rámci vašeho předplatného.


## <a name="look-at-attributes-of-single-hli-unit"></a>Podívejte se na jednu jednotku HLI atributy
V seznamu jednotek velká Instance HANA můžete kliknout na jednu jednotku a získat podrobné informace o jednu jednotku velká Instance HANA. 

Na obrazovce Přehled získáte prezentace jednotky, který bude vypadat takto:

![Zobrazit přehled HLI jednotky](./media/hana-li-portal/portal-show-overview.png)

Pohledu na různé atributy, které jsou zobrazeny, tyto atributy vypadat po liší od atributů virtuálního počítače Azure. Na levé straně na straně záhlaví ukazuje skupinu prostředků, oblast Azure, název předplatného a ID jakož i několik značek, které jste přidali. Velká Instance HANA jednotky mají ve výchozím nastavení žádné značky přiřazeny. Na pravé straně záhlaví je uveden název jednotky jako při nasazení bylo provedeno přiřazen. Operační systém se zobrazuje i IP adresu. Jako s virtuálními počítači HANA velké instance jednotku zadejte pomocí počet procesorů, vlákna a paměti se zobrazí také. Další informace o různých jednotek velká Instance HANA, můžete vidět tady:

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

První několik položek dat, jste viděli v okně Přehled již. Ale důležité část dat je ID okruh ExpressRoute, které získáte, když jste získali první nasazené jednotek předán. V některých případech podpory se můžou získat dotaz pro tato data. Položka důležitá data se zobrazí v dolní části na snímku obrazovky. Data zobrazená je IP adresa hlavní úložiště systému souborů NFS, který izoluje úložiště vašeho **tenanta** v zásobníku velká Instance HANA. Tato IP adresa je také potřeba při úpravách [konfigurační soubor pro ukládání snímků zálohy](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots). 

Jak se posunete dolů v podokně vlastností, získáte další data, jako je Identifikátor jedinečný prostředek pro velké Instance HANA jednotky nebo ID předplatného, které bylo přiřazeno k nasazení.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Restartujte jednotku velká Instance HANA prostřednictvím webu Azure portal
Zahajuje se restartování operačního systému Linux, byly různých situacích, kdy operační systém nelze dokončit restartování úspěšně. Pokud chcete vynutit restartování, potřebujete k otevření žádosti o službu má Microsoft operations provést restart power jednotky velká Instance HANA. Funkce power restartování jednotky velká Instance HANA je teď integrovaná v na webu Azure portal. Pokud jste v části Přehled velká Instance HANA jednotky, se zobrazí tlačítko pro restartování nad datové části

![Restartujte krok #1 na webu Azure portal](./media/hana-li-portal/portal-restart-first-step.png)

Jako jsou stisknutí tlačítka restartování, zobrazí se výzva, jestli Opravdu chcete restartovat jednotky. Jak můžete to potvrďte klepnutím na tlačítko "Ano", jednotka se restartuje.

> [!NOTE]
> Probíhá restartování, bude probíhat (krátkodobé používání) čas, kdy se stav jednotky změní na **počáteční** k přesunutí do stavu **spuštěno**. Ve stavu **spuštěno** znamená, že se spouští operační systém nebo, že operační systém se zahájilo úplně. V důsledku toho po restartu jednotky, nemůžete očekávat, že okamžitě přihlásit do jednotky, poté, co se přepne do stavu **spuštěno**.


## <a name="open-a-support-request-for-hana-large-instances"></a>Žádost o podporu pro velké instance HANA
Z portálu Azure zobrazení velká Instance HANA jednotek můžete vytvořit žádosti o podporu speciálně pro HANA velké Instance jednotka také. Jak budete postupovat podle odkazu **nová žádost o podporu** 

![Spustit krok žádosti o služby #1 na webu Azure portal](./media/hana-li-portal/portal-initiate-support-request.png)

Zajistí služba programu velké instance SAP HANA uvedené na další obrazovce, musíte vybrat "všechny služby" jak je znázorněno níže

![Vyberte všechny služby na webu Azure portal](./media/hana-li-portal/portal-create-service-request.png)

V seznamu služeb můžete najít službu **velkou instanci SAP HANA**. Při výběru této služby, můžete vybrat konkrétní problém typy, jak je znázorněno:


![Vyberte třídu problém na webu Azure portal](./media/hana-li-portal/portal-select-problem-class.png)

V rámci jednotlivých typů jiný problém dostanete výběr podtypy problému je nutné vybrat, chcete-li charakterizovat váš problém dále. Po výběru podtyp, teď můžete pojmenovat předmět. Po dokončení procesu výběru můžete přesunout k dalšímu kroku vytvoření. V **řešení** části jste odkázáno dokumentaci kolem velkých instancích HANA, které poskytují ukazatele na řešení problému. Pokud nemůžete najít řešení pro váš problém v dokumentaci k navrhované, přejdete k dalšímu kroku. V dalším kroku budete vyzváni, zda je problém s virtuálními počítači nebo s jednotkami velká Instance HANA. To pomáhá směrovat žádosti o podporu o správné odborníky. 

![Podrobnosti o případu podpory na webu Azure portal](./media/hana-li-portal/portal-support-request-details.png)

Jak jste odpověděli na otázky a poskytuje další podrobnosti, můžete přejít na další krok k žádosti o podporu a odeslat ji.

## <a name="next-steps"></a>Další postup

- [Jak monitorovat SAP HANA (velké instance) v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [Sledování a řešení potíží na straně HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)

