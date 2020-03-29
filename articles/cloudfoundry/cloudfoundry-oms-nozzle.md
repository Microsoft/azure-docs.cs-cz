---
title: Nasazení trysky Azure Log Analytics pro monitorování slévárna cloudu
description: Podrobné pokyny k nasazení loggregační trysky loggregátoru cloudové slévárny pro Azure Log Analytics. Pomocí trysky můžete sledovat metriky stavu a výkonu systému Cloud Foundry.
services: virtual-machines-linux
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: bf6691310ec964a1d6293f3a60c151e3d6f8e641
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277363"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Nasazení trysky Azure Log Analytics pro monitorování systému Cloud Foundry

[Azure Monitor](https://azure.microsoft.com/services/log-analytics/) je služba v Azure. Pomáhá shromažďovat a analyzovat data generovaná z vašeho cloudu a místních prostředí.

Tryska Log Analytics (tryska) je součást Cloud Foundry (CF), která předává metriky z [loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) firehose cloudové složky do protokolů Azure Monitor. S tryskou můžete shromažďovat, zobrazovat a analyzovat metriky stavu a výkonu systému CF napříč několika nasazeními.

V tomto dokumentu se dozvíte, jak nasadit trysku do prostředí CF a pak získat přístup k datům z konzoly protokolů Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Požadavky

Následující kroky jsou předpoklady pro nasazení trysky.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. Nasazení prostředí CF nebo Pivotal Cloud Foundry v Azure

Trysku můžete použít s nasazením CF s otevřeným zdrojovým kódem nebo s nasazením Pivotal Cloud Foundry (PCF).

* [Nasazení Cloud Foundry v Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Nasazení pivotal Cloud Foundry v Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Nainstalujte nástroje příkazového řádku CF pro nasazení trysky

Tryska běží jako aplikace v prostředí CF. K nasazení aplikace potřebujete cli CF.

Tryska také potřebuje přístupové oprávnění k loggregator firehose a cloudcontroller. Chcete-li vytvořit a nakonfigurovat uživatele, potřebujete službu Uživatelský účet a ověřování (UAA).

* [Instalace cli cloudové slévárna](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Instalace klienta příkazového řádku Cloud Foundry UAA](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Před nastavením klienta příkazového řádku UAA se ujistěte, že je nainstalovánrubyGems.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. Vytvoření pracovního prostoru Analýzy protokolů v Azure

Pracovní prostor Analýzy protokolů můžete vytvořit ručně nebo pomocí šablony. Šablona nasadí nastavení předkonfigurovaných zobrazení klíčových ukazatelů výkonu a výstrah pro konzolu protokolů Azure Monitor. 

#### <a name="to-create-the-workspace-manually"></a>Ruční vytvoření pracovního prostoru:

1. Na webu Azure Portal prohledejte seznam služeb na Azure Marketplace a pak vyberte Log Analytics pracovní prostory.
2. Vyberte **Vytvořit**a pak vyberte volby pro následující položky:

   * **Pracovní prostor Log Analytics**: Zadejte název pracovního prostoru.
   * **Předplatné**: Pokud máte více předplatných, vyberte ten, který je stejný jako vaše nasazení CF.
   * **Skupina prostředků**: Můžete vytvořit novou skupinu prostředků nebo použít stejnou skupinu s nasazením CF.
   * **Umístění**: Zadejte umístění.
   * **Cenová úroveň**: K dokončení vyberte **OK.**

Další informace najdete [v tématu Začínáme s protokoly Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).

#### <a name="to-create-the-log-analytics-workspace-through-the-monitoring-template-from-azure-market-place"></a>Vytvoření pracovního prostoru Log Analytics prostřednictvím šablony monitorování z webu Azure Market:

1. Otevřete Azure Portal.
1. Klikněte na znaménko "+" nebo "Vytvořit zdroj" v levém horním rohu.
1. Zadejte "Cloud Foundry" ve vyhledávacím okně, vyberte "Cloud Foundry Monitoring Solution".
1. Cloud Foundry monitorovací řešení šablony přední strana je načtena, klikněte na tlačítko "Vytvořit" pro spuštění šablony blade.
1. Zadejte požadované parametry:
    * **Předplatné**: Vyberte předplatné Azure pro pracovní prostor Log Analytics, obvykle stejné s nasazením Cloud Foundry.
    * **Skupina prostředků**: Vyberte existující skupinu prostředků nebo vytvořte novou pro pracovní prostor Log Analytics.
    * **Umístění skupiny prostředků**: Vyberte umístění skupiny prostředků.
    * **OMS_Workspace_Name**: Zadejte název pracovního prostoru, pokud pracovní prostor neexistuje, vytvoří šablona nový.
    * **OMS_Workspace_Region**: Vyberte umístění pracovního prostoru.
    * **OMS_Workspace_Pricing_Tier**: Vyberte skladovou položku pracovního prostoru Analýzy protokolů. Viz pokyny pro [stanovení cen.](https://azure.microsoft.com/pricing/details/log-analytics/)
    * **Právní podmínky**: Klikněte na Právní podmínky a potom kliknutím na tlačítko "Vytvořit" přijmete právní termín.
1. Po zadání všech parametrů klikněte na tlačítko "Vytvořit" pro nasazení šablony. Po dokončení nasazení se stav zobrazí na kartě oznámení.


## <a name="deploy-the-nozzle"></a>Nasazení trysky

Existuje několik různých způsobů nasazení trysky: jako dlaždice PCF nebo jako aplikace CF.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Nasazení trysky jako dlaždice SPRÁVCE operačních operací PCF

Podle pokynů [nainstalujte a nakonfigurujte trysku Azure Log Analytics pro PCF](https://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). Jedná se o zjednodušený přístup, dlaždice správce PCF Ops automaticky nakonfiguruje a posune trysku. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>Ruční nasazení trysky jako aplikace CF

Pokud nepoužíváte SPRÁVCE OPERAČNÍCH OPERACÍ PCF, nasaďte trysku jako aplikaci. Následující části popisují tento proces.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Přihlaste se k nasazení CF jako správce prostřednictvím CF CLI

Spusťte následující příkaz:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN" je vaše CF doménové jméno. Můžete načíst hledáním "SYSTEM_DOMAIN" v souboru manifestu nasazení CF. 

"CF_User" je název správce CF. Název a heslo můžete načíst vyhledáním části "scim" a vyhledáním názvu a "cf_admin_password" v souboru manifestu nasazení CF.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Vytvoření uživatele CF a udělení požadovaných oprávnění

Spusťte následující příkazy:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN" je vaše CF doménové jméno. Můžete načíst hledáním "SYSTEM_DOMAIN" v souboru manifestu nasazení CF.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Stáhněte si nejnovější verzi trysky Log Analytics

Spusťte následující příkaz:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Nastavení proměnných prostředí

Nyní můžete nastavit proměnné prostředí v souboru manifest.yml v aktuálním adresáři. Následující ukazuje manifest aplikace pro trysku. Nahraďte hodnoty konkrétními informacemi o pracovním prostoru Analýzy protokolů.

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

### <a name="push-the-application-from-your-development-computer"></a>Stlačení aplikace z vývojového počítače

Ujistěte se, že jste pod složkou oms-log-analytics-firehose-hubice. Spusťte následující příkaz:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Ověření instalace trysky

### <a name="from-apps-manager-for-pcf"></a>Ze Správce aplikací (pro PCF)

1. Přihlaste se ke Správci operací a ujistěte se, že se dlaždice zobrazuje na panelu instalace.
2. Přihlaste se ke Správci aplikací, ujistěte se, že místo, které jste vytvořili pro trysku, je uvedeno v sestavě využití, a potvrďte, že stav je normální.

### <a name="from-your-development-computer"></a>Z vašeho vývojového počítače

V okně CF CLI zadejte:
```
cf apps
```
Zkontrolujte, zda je spuštěna aplikace OMS Nozzle.

## <a name="view-the-data-in-the-azure-portal"></a>Zobrazení dat na webu Azure Portal

Pokud jste nasadili řešení monitorování prostřednictvím šablony tržiště, přejděte na portál Azure a vyhledejte řešení. Řešení najdete ve skupině prostředků, kterou jste zadali v šabloně. Klikněte na řešení, přejděte na "konzolu analýzy protokolů", jsou uvedena předem nakonfigurovaná zobrazení s nejlepšími ukazateli klíčů k systému Cloud Foundry, daty aplikací, výstrahami a metrikami stavu virtuálních her. 

Pokud jste pracovní prostor Analýzy protokolů vytvořili ručně, vytvořte zobrazení a výstrahy následujícím postupem:

### <a name="1-import-the-oms-view"></a>1. Import zobrazení OMS

Na portálu OMS přejděte na **Zobrazit návrháře** > **importovat** > **procházet**a vyberte jeden ze souborů omsview. Vyberte například *soubor Cloud Foundry.omsview*a uložte zobrazení. Nyní se na stránce **Přehled** zobrazí dlaždice. Výběrem zobrazíte vizualizované metriky.

Tato zobrazení můžete přizpůsobit nebo vytvořit nová zobrazení pomocí **návrháře zobrazení**.

*"Cloud Foundry.omsview"* je předběžná verze šablony zobrazení Cloud Foundry OMS. Toto je plně nakonfigurovaná výchozí šablona. Pokud máte návrhy nebo zpětnou vazbu k šabloně, odešlete je do [části s problémem](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Vytvoření pravidel výstrah

Můžete [vytvořit výstrahy](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts)a podle potřeby přizpůsobit dotazy a prahové hodnoty. Doporučujeme upozornění:

| Vyhledávací dotaz                                                                  | Generovat výstrahu založenou na | Popis                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | Počet výsledků < 1   | **bbs. Domain.cf aplikace** označuje, jestli je doména cf-apps aktuální. To znamená, že požadavky na aplikace CF z cloudového řadiče jsou synchronizovány do bbs. LRPsDesired (Diego požadované AIs) pro provedení. Žádná přijatá data znamená, že cf-apps Doména není v zadaném časovém okně aktuální. |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=Nezdravýbuňka Value_d>1            | Počet výsledků > 0   | Pro Diego buňky, 0 znamená zdravé, a 1 znamená nezdravé. Nastavte výstrahu, pokud je v zadaném časovém okně zjištěno více buněk Nezdravé Diego. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Počet výsledků > 0 | 1 znamená, že systém je zdravý a 0 znamená, že systém není zdravý. |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | Počet výsledků > 0   | Konzul pravidelně vydává svůj zdravotní stav. 0 znamená, že systém je zdravý a 1 znamená, že emitor trasy detekuje, že konzul je vypnutý. |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="ZkráceníBuffer.DroppedMessages" nebo Name_s="doppler.shedEnvelopes") Delta_d>0 | Počet výsledků > 0 | Rozdílový počet zpráv záměrně klesl do Doppler kvůli zpětnému tlaku. |
| Typ=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | Počet výsledků > 0   | Loggregator vyzařuje **LGR** k označení problémů s procesem protokolování. Příkladem takového problému je, když výstup zprávy protokolu je příliš vysoká. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | Počet výsledků > 0   | Když tryska obdrží pomalé upozornění příjemce z loggregator, odešle **slowConsumerAlert** ValueMetric do protokolů Azure Monitor. |
| Type=CF_CounterEvent_CL Job_s=tryska Name_s=eventsLost Delta_d>0              | Počet výsledků > 0   | Pokud rozdílový počet ztracených událostí dosáhne prahové hodnoty, znamená to, že tryska může mít problém se spuštěním. |

## <a name="scale"></a>Škálování

Můžete škálovat trysku a loggregator.

### <a name="scale-the-nozzle"></a>Měřítko trysky

Měli byste začít s alespoň dvěma instancemi trysky. Požární hadice distribuuje zatížení mezi všechny instance trysky.
Chcete-li se ujistit, že tryska může držet krok s datovým provozem z požární hadice, nastavte **upozornění slowConsumerAlert** (uvedené v předchozí části "Vytvořit pravidla výstrahy"). Po upozornění postupujte podle [pokynů pro pomalé trysky](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) a zjistěte, zda je potřeba škálování.
Chcete-li zvýšit kapacitu trysky, použijte Správce aplikací nebo CF CLI zvýšit čísla instancí nebo paměť nebo disk prostředky pro trysku.

### <a name="scale-the-loggregator"></a>Škálování loggregator

Loggregator odešle zprávu protokolu **LGR** k označení problémů s procesem protokolování. Můžete sledovat výstrahu k určení, zda je třeba vertikálně navýšit kapacitu loggregator.
Chcete-li vertikálně navýšit kapacitu loggregator, buď zvětšit velikost vyrovnávací paměti Doppler nebo přidat další instance Serveru Doppler v manifestu CF. Další informace naleznete [v pokynech pro změnu velikosti loggregator](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Aktualizace

Chcete-li trysku aktualizovat novější verzí, stáhněte si novou verzi trysky, postupujte podle pokynů v předchozí části Nasazení trysky a znovu posuňte aplikaci.

### <a name="remove-the-nozzle-from-ops-manager"></a>Odebrání trysky ze Správce operací

1. Přihlaste se ke Správci operací.
2. Vyhledejte dlaždici **Microsoft Azure Log Analytics pro PCF.**
3. Vyberte ikonu odpadků a potvrďte odstranění.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Odebrání trysky z vývojového počítače

V okně CF CLI zadejte:
```
cf delete <App Name> -r
```

Pokud odeberete trysku, data na portálu OMS nebudou automaticky odebrána. Vyprší na základě nastavení uchovávání protokolů Azure Monitor.

## <a name="support-and-feedback"></a>Podpora a zpětná vazba

Azure Log Analytics Tryska je open source. Pošlete své dotazy a zpětnou vazbu do [sekce GitHub](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Chcete-li otevřít žádost o podporu Azure, zvolte jako kategorii služeb "Virtuální počítač se spuštěnou cloudovou slévárnou". 

## <a name="next-step"></a>Další krok

Z PCF2.0 metriky výkonu virtuálních počítačů se přenesou do huby Azure Log Analytics pomocí systémových metrik Předávání a integrované do pracovního prostoru Log Analytics. Už nepotřebujete agenta Analýzy protokolů pro metriky výkonu virtuálního trhu. Agent aanalýza protokolu však můžete stále používat agenta Log Analytics ke shromažďování informací sprotokolem Syslog. Agent Log Analytics se nainstaluje jako doplněk Bosh do vašich virtuálních počítačů CF. 

Podrobnosti najdete [v tématu Nasazení agenta Log Analytics do nasazení Cloud Foundry](https://github.com/Azure/oms-agent-for-linux-boshrelease).
