---
title: Nasazení služby Azure Log Analytics trysek pro monitorování Cloud Foundry
description: Podrobné pokyny k nasazení Cloud Foundry trysku loggregator pro Azure Log Analytics. Pomocí trysky můžete monitorovat stav Cloud Foundry systému a metriky výkonu.
services: virtual-machines
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: 54001c47d03b686a8e7c1f59f1e53d405e3bc506
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557382"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Nasazení služby Azure Log Analytics trysek pro Cloud Foundry sledování systému

[Azure monitor](https://azure.microsoft.com/services/log-analytics/) je služba v Azure. Pomáhá shromažďovat a analyzovat data vygenerovaná z vašich cloudových a místních prostředí.

Log Analytics tryska (trysek) je komponenta Cloud Foundry (CF), která přepošle metriky z [Cloud Foundry loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) firehose na protokoly Azure monitor. Pomocí této trysky můžete shromažďovat, zobrazovat a analyzovat stav systému CF a metriky výkonu napříč různými nasazeními.

V tomto dokumentu se dozvíte, jak nasadit trysek do svého prostředí CF a pak přistupovat k datům z konzoly Azure Monitor Logs.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Předpoklady

Následující kroky jsou požadavky na nasazení trysek.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. nasazení Cloud Foundry CF nebo Pivoted pro prostředí v Azure

Můžete použít trysku s nasazením Open Source CF nebo PCF (Pivoted Cloud Foundry).

* [Nasazení Cloud Foundry v Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Nasazení Pivot Cloud Foundry v Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Nainstalujte nástroje příkazového řádku CF pro nasazení trysek.

Tryska běží jako aplikace v prostředí CF. K nasazení aplikace potřebujete CF CLI.

Tato tryska taky potřebuje oprávnění k přístupu k loggregator firehose a k řadiči cloudu. Chcete-li vytvořit a nakonfigurovat uživatele, potřebujete službu uživatelského účtu a ověřování (UAA).

* [Instalace Cloud Foundry CLI](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Instalace klienta příkazového řádku Cloud Foundry UAA](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Před nastavením klienta příkazového řádku architektury UAA se ujistěte, že je nainstalovaná služba RubyGems.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. vytvoření pracovního prostoru Log Analytics v Azure

Pracovní prostor Log Analytics můžete vytvořit ručně nebo pomocí šablony. Šablona nasadí nastavení předem nakonfigurovaných zobrazení a výstrah klíčových ukazatelů výkonu pro konzolu Azure Monitorch protokolů. 

#### <a name="to-create-the-workspace-manually"></a>Postup ručního vytvoření pracovního prostoru:

1. V Azure Portal vyhledejte seznam služeb v Azure Marketplace a pak vyberte Log Analytics pracovní prostory.
2. Vyberte **vytvořit** a pak vyberte volby pro následující položky:

   * **Log Analytics pracovní prostor**: zadejte název pracovního prostoru.
   * **Předplatné**: Pokud máte více předplatných, vyberte ten, který se shoduje s vaším nasazením CF.
   * **Skupina prostředků**: můžete vytvořit novou skupinu prostředků, nebo použít stejnou pro nasazení CF.
   * **Umístění**: zadejte umístění.
   * **Cenová úroveň**: dokončete výběr kliknutím na **tlačítko OK** .

Další informace najdete v tématu [Začínáme s protokoly Azure monitor](../azure-monitor/overview.md).

#### <a name="to-create-the-log-analytics-workspace-through-the-monitoring-template-from-azure-market-place"></a>Pokud chcete vytvořit pracovní prostor Log Analytics pomocí šablony monitorování z místa na trhu Azure:

1. Otevřete Azure Portal.
1. Klikněte na symbol "+" nebo "vytvořit prostředek" v levém horním rohu.
1. Zadejte "Cloud Foundry" v okně hledání vyberte "Cloud Foundry monitorování řešení".
1. Načetla se úvodní stránka šablony řešení Cloud Foundry, kliknutím na vytvořit otevřete okno šablony.
1. Zadejte požadované parametry:
    * **Předplatné**: vyberte předplatné Azure pro pracovní prostor Log Analytics, obvykle stejně jako nasazení Cloud Foundry.
    * **Skupina prostředků**: Vyberte existující skupinu prostředků nebo vytvořte novou pro Log Analytics pracovní prostor.
    * **Umístění skupiny prostředků**: vyberte umístění skupiny prostředků.
    * **OMS_Workspace_Name**: zadejte název pracovního prostoru, pokud pracovní prostor neexistuje, šablona vytvoří novou.
    * **OMS_Workspace_Region**: vyberte umístění pro pracovní prostor.
    * **OMS_Workspace_Pricing_Tier**: vyberte skladovou položku pracovního prostoru Log Analytics. Podívejte se na informace o [cenách](https://azure.microsoft.com/pricing/details/log-analytics/) pro referenci.
    * **Právní podmínky**: klikněte na právní podmínky a potom klikněte na vytvořit, aby se právní období přijalo.
1. Po zadání všech parametrů klikněte na vytvořit a nasaďte šablonu. Po dokončení nasazení se stav zobrazí na kartě oznámení.


## <a name="deploy-the-nozzle"></a>Nasazení trysky

Existuje několik různých způsobů, jak nasadit trysek: jako dlaždici PCF nebo jako aplikaci v podobě CF.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Nasazení trysek jako dlaždice správce OPS PCF

Postupujte podle pokynů pro [instalaci a konfiguraci služby Azure Log Analytics trysek pro PCF](https://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). Toto je zjednodušený přístup. na dlaždici Správce OPS PCF se automaticky nakonfiguruje a nasadí trysk. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>Ruční nasazení trysek jako aplikace CF

Pokud nepoužíváte nástroj PCF OPS Manager, nasaďte trysku jako aplikaci. Následující části popisují tento proces.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Přihlaste se k nasazení CF jako správce prostřednictvím CF CLI.

Spusťte následující příkaz:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN" je název domény CF. Můžete ho načíst tak, že ve svém souboru manifestu nasazení CF vyhledáte "SYSTEM_DOMAIN". 

"CF_User" je název správce CF. Název a heslo můžete načíst tak, že v části "SCIM" vyhledáte název a "cf_admin_password" v souboru manifestu nasazení CF.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Vytvoření uživatele CF a udělení požadovaných oprávnění

Spusťte následující příkazy:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN" je název domény CF. Můžete ho načíst tak, že ve svém souboru manifestu nasazení CF vyhledáte "SYSTEM_DOMAIN".

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Stáhnout nejnovější verzi Log Analyticsch trysek

Spusťte následující příkaz:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Nastavení proměnných prostředí

Nyní můžete nastavit proměnné prostředí v souboru manifest. yml v aktuálním adresáři. Následující příklad ukazuje manifest aplikace pro trysek. Nahraďte hodnoty konkrétními informacemi z Log Analytics pracovního prostoru.

```
OMS_WORKSPACE             : Log Analytics workspace ID: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_KEY                   : OMS key: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to Azure Monitor logs. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to Azure Monitor logs. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to Azure Monitor logs. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to Azure Monitor logs as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to Azure Monitor logs. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>Vložení aplikace z vývojového počítače

Ujistěte se, že jste pod složkou OMS-Log-Analytics-firehose-trysek. Spusťte následující příkaz:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Ověření instalace trysek

### <a name="from-apps-manager-for-pcf"></a>Ze Správce aplikací (pro PCF)

1. Přihlaste se k nástroji OPS Manager a ujistěte se, že se na řídicím panelu instalace zobrazuje dlaždice.
2. Přihlaste se k aplikaci Správce aplikací, ujistěte se, že je místo, které jste vytvořili pro tuto trysku, uvedené v sestavě využití, a ověřte, že je stav normální.

### <a name="from-your-development-computer"></a>Z vývojového počítače

V okně CF CLI zadejte:
```
cf apps
```
Ujistěte se, že je aplikace pro trysek OMS spuštěná.

## <a name="view-the-data-in-the-azure-portal"></a>Zobrazit data v Azure Portal

Pokud jste nasadili řešení monitorování prostřednictvím šablony umístění na trhu, přejděte na Azure Portal a najděte řešení. Řešení najdete ve skupině prostředků, kterou jste zadali v šabloně. Klikněte na řešení, přejděte do konzoly Log Analytics, předkonfigurovaná zobrazení jsou uvedena s hlavními Cloud Foundry systémových klíčových ukazatelů výkonu, dat aplikací, výstrah a metrik stavu virtuálních počítačů. 

Pokud jste pracovní prostor Log Analytics vytvořili ručně, vytvořte zobrazení a výstrahy pomocí následujících kroků:

### <a name="1-import-the-oms-view"></a>1. Import zobrazení OMS

Na portálu OMS přejděte k **zobrazení Náhled**  >  **importu** návrháře  >  a vyberte jeden ze souborů omsview. Vyberte například *Cloud Foundry. omsview* a uložte zobrazení. Na stránce **Přehled** se teď zobrazí dlaždice. Vyberte ho a podívejte se na vizuální metriky.

Tato zobrazení můžete přizpůsobit nebo vytvořit nová zobrazení prostřednictvím **návrháře zobrazení**.

*Cloud Foundry. omsview* je verze Preview šablony zobrazení Cloud Foundry OMS. Toto je plně nakonfigurovaná výchozí šablona. Pokud máte návrhy nebo názory na šablonu, odešlete je do [části problém](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. vytvoření pravidel upozornění

Můžete [vytvořit výstrahy](../azure-monitor/alerts/alerts-overview.md)a podle potřeby upravit dotazy a prahové hodnoty. Následující jsou doporučené výstrahy:

| Vyhledávací dotaz                                                                  | Generovat výstrahu na základě | Description                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type = CF_ValueMetric_CL Origin_s = BBS Name_s = "doména. CF-aplikace"                   | Počet výsledků < 1   | **BBS. Domain.cf – aplikace** určuje, jestli je doména CF-Apps aktuální. To znamená, že žádosti o aplikace z cloudového kontroleru se synchronizují do BBS. LRPsDesired (Diegu-požadovaný AIs) pro provedení. Žádná data neobdržena znamená, že doména CF-Apps není v určeném časovém intervalu v aktuálním stavu. |
| Type = CF_ValueMetric_CL Origin_s = REP Name_s = UnhealthyCell Value_d>1            | Počet výsledků > 0   | U Dieguch buněk znamená 0 v pořádku a 1 znamená špatný stav. Nastavte výstrahu, pokud v určeném časovém intervalu dojde k detekci více Diegu buněk v pořádku. |
| Type = CF_ValueMetric_CL Origin_s = "Bosh-hm-re-" Name_s = "System. Health" Value_d = 0 | Počet výsledků > 0 | 1 znamená, že je systém v pořádku a 0 znamená, že systém není v pořádku. |
| Type = CF_ValueMetric_CL Origin_s = route_emitter Name_s = ConsulDownMode Value_d>0 | Počet výsledků > 0   | Consul vygeneruje svůj stav pravidelně. 0 znamená, že systém je v pořádku a 1 znamená, že modul pro vystavování tras zjistí, že Consul je mimo provoz. |
| Type = CF_CounterEvent_CL Origin_s = DopplerServer (Name_s = "TruncatingBuffer. DroppedMessages" nebo Name_s = "Doppler. shedEnvelopes") Delta_d>0 | Počet výsledků > 0 | Rozdílový počet zpráv záměrně vyřazených z Doppler z důvodu protitlaku. |
| Type = CF_LogMessage_CL SourceType_s = LGR MessageType_s = ERR                      | Počet výsledků > 0   | Loggregator vygeneruje **LGR** pro indikaci problémů s procesem protokolování. Příkladem takového problému je, že výstup zprávy protokolu je příliš vysoký. |
| Type = CF_ValueMetric_CL Name_s = slowConsumerAlert                               | Počet výsledků > 0   | Když tryska obdrží pomalé upozornění zákazníků z loggregator, pošle ValueMetricu **slowConsumerAlert** do Azure monitor protokolů. |
| Type = CF_CounterEvent_CL Job_s = trysek Name_s = eventsLost Delta_d>0              | Počet výsledků > 0   | Pokud rozdílový počet ztracených událostí dosáhne prahové hodnoty, znamená to, že může dojít k potížím s trysek. |

## <a name="scale"></a>Měřítko

Můžete škálovat trysku a loggregator.

### <a name="scale-the-nozzle"></a>Škálování trysky

Měli byste začít s alespoň dvěma instancemi této trysky. Firehose distribuuje zatížení napříč všemi instancemi trysky.
Aby se zajistilo, že tryska bude mít přenos dat z firehose, nastavte výstrahu **slowConsumerAlert** (uvedená v předchozí části "Vytvoření pravidel upozornění"). Jakmile budete upozorněni, postupujte podle [pokynů pro pomalou trysku](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) a určete, zda je nutné škálování provést.
K navýšení kapacity trysek použijte Správce aplikací nebo CF CLI, abyste zvýšili čísla instancí nebo prostředky paměti nebo disku pro trysky.

### <a name="scale-the-loggregator"></a>Škálování loggregator

Loggregator pošle zprávu protokolu **LGR** , která indikuje problémy s procesem protokolování. Výstrahu můžete monitorovat, abyste zjistili, jestli je potřeba škálovat loggregator.
Pro horizontální navýšení kapacity loggregator zvětšete velikost vyrovnávací paměti Doppler nebo přidejte další instance Doppler serveru v manifestu CF. Další informace najdete v [pokynech pro škálování loggregator](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Aktualizace

Chcete-li aktualizovat trysek pomocí novější verze, Stáhněte si novou verzi nové trysky, postupujte podle kroků v předchozí části nasazení trysek a znovu aplikaci nahrajte.

### <a name="remove-the-nozzle-from-ops-manager"></a>Odebrat trysku z nástroje OPS Manager

1. Přihlaste se ke Správci OPS.
2. Vyhledejte dlaždici **Microsoft Azure Log Analytics trysek pro PCF** .
3. Vyberte ikonu uvolnění paměti a potvrďte odstranění.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Odebrat trysku z vývojového počítače

V okně CF CLI zadejte:
```
cf delete <App Name> -r
```

Pokud tuto trysku odeberete, data na portálu OMS se automaticky neodeberou. Vyprší na základě vašeho nastavení uchovávání protokolů Azure Monitor.

## <a name="support-and-feedback"></a>Podpora a zpětná vazba

Pro službu Azure Log Analytics tryska je otevřený zdroj. Odešlete své dotazy a zpětnou vazbu do [části GitHub](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Pokud chcete otevřít žádost o podporu Azure, vyberte jako kategorie služby možnost virtuální počítač se spuštěným Cloud Foundry. 

## <a name="next-step"></a>Další krok

Z PCF 2.0 se metriky výkonu virtuálních počítačů přenáší do služby Azure Log Analytics na základě předávacích systémových metrik a integrují se do pracovního prostoru Log Analytics. Už nepotřebujete agenta Log Analytics pro metriky výkonu virtuálního počítače. Můžete však stále používat agenta Log Analytics ke shromažďování informací o syslogu. Agent Log Analytics je nainstalován jako doplněk Bosh k virtuálním počítačům CF. 

Podrobnosti najdete v tématu [nasazení agenta Log Analytics do nasazení Cloud Foundry](https://github.com/Azure/oms-agent-for-linux-boshrelease).