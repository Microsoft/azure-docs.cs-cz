---
title: Nasazení Azure Log Analytics Nozzle pro Cloud Foundry monitorování | Dokumentace Microsoftu
description: Podrobné pokyny k nasazení Cloud Foundry loggregator Nozzle pro Azure Log Analytics. Pomocí Nozzle monitorujte metriky stavu a výkonu systému Cloud Foundry.
services: virtual-machines-linux
documentationcenter: ''
author: ningk
manager: jeconnoc
editor: ''
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: c58c2b255d269aef7e8b3fea62d003ad0c16ef0a
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971244"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Nasazení Azure Log Analytics Nozzle pro monitorování systému Cloud Foundry

[Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/) je služba v Azure. Pomáhá shromažďovat a analyzovat data, která se generuje z vašeho cloudu a místních prostředích.

Log Analytics Nozzle (Nozzle) je komponenta Cloud Foundry (CR), která předává metriky z [Cloud Foundry loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) firehose ke službě Log Analytics. Nozzle můžete shromažďovat, zobrazit a analyzovat vaše CF systému stavu a výkonu metrik, mezi několika nasazeními.

V tomto dokumentu se dozvíte, jak k nasazení Nozzle pro CF prostředí a pak přístup k datům z konzoly pro Log Analytics.

## <a name="prerequisites"></a>Požadavky

Následující kroky jsou požadavky pro nasazení Nozzle.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. Nasazení prostředí CF nebo Pivotal Cloud Foundry v Azure

Nozzle můžete použít k nasazení CF opensourcových nebo nasazení Pivotal Cloud Foundry (PCF).

* [Nasazení Cloud Foundry v Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Nasazení Pivotal Cloud Foundry v Azure](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Instalace CF příkazového řádku nástrojů pro nasazení Nozzle

Nozzle spouští jako aplikace CF prostředí. Je třeba CF rozhraní příkazového řádku pro nasazení aplikace.

Nozzle musí také přístupová oprávnění k loggregator firehose a kontroler cloudu. Vytvoření a konfigurace uživatele, musíte službu uživatelský účet a ověřování (UAA).

* [Instalace Cloud Foundry rozhraní příkazového řádku](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Instalace Cloud Foundry UAA klienta příkazového řádku](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

Před nastavením klienta příkazového řádku UAA, ujistěte se, že je nainstalovaný Rubygems.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. Vytvoření pracovního prostoru Log Analytics v Azure

Pracovní prostor Log Analytics můžete vytvořit ručně nebo pomocí šablony. Šablona nasadí nastavení předem nakonfigurovaná zobrazení OMS klíčového ukazatele výkonu a upozornění na konzole OMS. 

#### <a name="to-create-the-workspace-manually"></a>Ruční vytvoření pracovního prostoru:

1. Na webu Azure Portal v seznamu služeb na webu Azure Marketplace vyhledejte a vyberte Log Analytics.
2. Vyberte **vytvořit**a podle potřeby změňte hodnoty následujících položek:

   * **Pracovní prostor OMS**: Zadejte název pro váš pracovní prostor.
   * **Předplatné**: Pokud máte více předplatných, vyberte ten, který je stejný jako vaše nasazení CF.
   * **Skupina prostředků**: můžete vytvořit novou skupinu prostředků nebo použijte to samé s nasazením CF.
   * **Umístění**: Zadejte umístění.
   * **Cenová úroveň**: vyberte **OK** dokončete.

Další informace najdete v tématu [Začínáme se službou Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).

#### <a name="to-create-the-oms-workspace-through-the-oms-monitoring-template-from-azure-market-place"></a>Chcete-li vytvořit pracovní prostor OMS pomocí šablony monitorování OMS z Tržiště Azure:

1. Otevřete Azure portal.
2. Klikněte na znaménko "+" nebo "Vytvořit prostředek" v levém horním rohu.
3. V okně hledání zadejte "Cloud Foundry" a vyberte "Cloud Foundry monitorování řešení OMS".
4. Cloud Foundry OMS, je načten monitorování front-stránka šablony řešení, klikněte na možnost "Vytvořit", spusťte okno šablonu.
5. Zadejte požadované parametry:
    * **Předplatné**: Vyberte předplatné Azure pro pracovní prostor OMS, obvykle stejným způsobem pracovat s Cloud Foundry nasazení.
    * **Skupina prostředků**: Vyberte existující skupinu prostředků nebo vytvořte nový pracovní prostor OMS.
    * **Umístění skupiny prostředků**: Vyberte umístění skupiny prostředků.
    * **OMS_Workspace_Name**: Zadejte název pracovního prostoru, pokud pracovní prostor neexistuje, tato šablona vytvoří nový.
    * **OMS_Workspace_Region**: Vyberte umístění pro pracovní prostor.
    * **OMS_Workspace_Pricing_Tier**: Vyberte skladovou Položku pracovního prostoru OMS. Zobrazit [doprovodné materiály k cenám](https://azure.microsoft.com/pricing/details/log-analytics/) pro referenci.
    * **Právní podmínky**: klikněte na právní podmínky, klepněte na tlačítko "Vytvořit" aby přijal právní podmínky.
- Jakmile jsou zadány všechny parametry, klikněte na možnost "Vytvořit" k nasazení šablony. Když se nasazení dokončí, stav se zobrazí na kartě oznámení.


## <a name="deploy-the-nozzle"></a>Nasazení Nozzle

Existuje několik různých způsobů, jak nasadit Nozzle: jako dlaždici PCF nebo jako aplikace CF.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Nasazení Nozzle jako dlaždice správce PCF Ops

Uvedený postup [instalace a konfigurace Azure Log Analytics Nozzle pro PCF](http://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html). Toto je zjednodušený přístup, automaticky nakonfiguruje a push nozzle dlaždice správce PCF Ops. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>Ruční nasazení Nozzle jako aplikace CF

Pokud nepoužíváte PCF Ops Manager, nasazení Nozzle jako aplikace. Následující části popisují tento proces.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>Přihlaste se k nasazení CF jako správce pomocí rozhraní CLI CF

Spusťte následující příkaz:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN" je název vaší domény CF. To můžete načíst tak, že "SYSTEM_DOMAIN" v souboru manifestu nasazení CF. 

"CF_User" je CF jméno správce. Uživatelské jméno a heslo můžete načíst tak, že v části "scim" vyhledávání, vyhledávání pro název a "cf_admin_password" v souboru manifestu nasazení CF.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Vytvořte CF uživatele a udělit požadovaná oprávnění

Spusťte následující příkazy:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN" je název vaší domény CF. To můžete načíst tak, že "SYSTEM_DOMAIN" v souboru manifestu nasazení CF.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>Stáhněte si nejnovější verzi Log Analytics Nozzle

Spusťte následující příkaz:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Nastavení proměnných prostředí

Nyní můžete nastavit proměnné prostředí v souboru manifest.yml v aktuálním adresáři. Následuje ukázka pro Nozzle manifestu aplikace. Nahraďte hodnoty konkrétní informace o pracovním prostoru Log Analytics.

```
OMS_WORKSPACE             : Log Analytics workspace ID: open OMS portal from your Log Analytics workspace, select Settings, and select connected sources.
OMS_KEY                   : OMS key: open OMS portal from your Log Analytics workspace, select Settings, and select connected sources.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to Log Analytics. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to Log Analytics. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to Log Analytics. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to Log Analytics as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to Log Analytics. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>Předejte aplikaci z vývojového počítače

Ujistěte se, že jste v rámci oms-log-analytics-firehose-nozzle složky. Spusťte následující příkaz:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Ověření instalace Nozzle

### <a name="from-apps-manager-for-pcf"></a>Ze Správce aplikací (pro PCF)

1. Přihlaste se k Ops správce a ujistěte se, že se zobrazí na dlaždici na řídicím panelu instalace.
2. Přihlaste se na správce aplikace, ujistěte se, že místo, které jste vytvořili pro Nozzle je uveden v sestavě využití a potvrďte, že stav je normální.

### <a name="from-your-development-computer"></a>Z vývojového počítače

V okně CF rozhraní příkazového řádku zadejte:
```
cf apps
```
Ujistěte se, že je OMS Nozzle aplikace spuštěna.

## <a name="view-the-data-in-the-oms-portal"></a>Zobrazení dat na portálu OMS

Pokud jste nasadili prostřednictvím řešení pro monitorování OMS šablona Marketplace, přejděte na web Azure portal a nachází řešení OMS. Řešení můžete najít ve skupině prostředků, které jste zadali v šabloně. Klikněte na řešení, přejděte na "OMS konzoly", jsou uvedeny předem nakonfigurovaná zobrazení, klíčové ukazatele výkonu systému nejvyšší Cloud Foundry, data aplikací, výstrahy a metriky stavu virtuálního počítače. 

Pokud jste vytvořili pracovní prostor OMS ručně, postupujte podle následujících kroků a vytvořte výstrahy a zobrazení:

### <a name="1-import-the-oms-view"></a>1. Importovat zobrazení OMS

Z portálu OMS vyhledejte **Návrhář zobrazení** > **Import** > **Procházet**a vyberte jednu z omsview soubory. Vyberte například *cloudu Foundry.omsview*a toto zobrazení uložíte. Nyní se zobrazí na dlaždici na **přehled** stránky. Vyberte ho, abyste viděli vizualizovaný metriky.

Můžete přizpůsobit tato zobrazení nebo vytvořit nová zobrazení prostřednictvím **Návrhář zobrazení**.

*"Cloud Foundry.omsview"* je verze preview zobrazení šablony Cloud Foundry OMS. Toto je plně nakonfigurované výchozí šablona. Pokud máte nějaké návrhy nebo zpětné vazby o šabloně, pošlete je [vydat části](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues).

### <a name="2-create-alert-rules"></a>2. Vytváření pravidel upozornění

Je možné [vytvoření výstrahy](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts)a podle potřeby upravte dotazy a prahové hodnoty. Následující jsou doporučené výstrahy:

| Vyhledávací dotaz                                                                  | Generovat výstrahu na základě | Popis                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Typ = CF_ValueMetric_CL Origin_s = bbs Name_s = "Domain.cf aplikace"                   | Počet výsledků < 1   | **BBS. Aplikace Domain.cf** značí, zda je aktuální domény aplikace cf. To znamená, že aplikace CF požadavky z cloudu řadiče jsou synchronizovány do bbs. LRPsDesired (AIs požadovaného Diegu) pro spuštění. Nepřišla žádná data znamená, že aplikace cf domény není aktuální v zadané časové okno. |
| Typ = CF_ValueMetric_CL Origin_s = zástupce Name_s = UnhealthyCell Value_d > 1            | Počet výsledků > 0   | Diego buněk 0 znamená, že jsou v pořádku a 1 znamená, že není v pořádku. Nastavte upozornění, pokud více buněk není v pořádku Diegu nalezených v zadané časové okno. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | Počet výsledků > 0 | 1 znamená, že systém je v pořádku a 0 znamená, že v systému není v pořádku. |
| Typ = CF_ValueMetric_CL Origin_s = route_emitter Name_s ConsulDownMode Value_d = > 0 | Počet výsledků > 0   | Konzul pravidelně vydává její dobrý stav. 0 znamená, že systém je v pořádku a 1 znamená, že vysílače trasy zjistí, že konzul dolů. |
| Typ = CF_CounterEvent_CL Origin_s Delta_d DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" nebo Name_s="doppler.shedEnvelopes") = > 0 | Počet výsledků > 0 | Rozdílová počet zpráv podle Doppler záměrně vynechaný kvůli protitlak. |
| Typ = CF_LogMessage_CL SourceType_s = LGR MessageType_s = ERR                      | Počet výsledků > 0   | Vysílá Loggregator **LGR** případné problémy s procesem protokolování. Příkladem takových problém při výstupní zprávy protokolu je příliš vysoká. |
| Typ = CF_ValueMetric_CL Name_s = slowConsumerAlert                               | Počet výsledků > 0   | Když se Nozzle obdrží upozornění na pomalé příjemce od loggregator, odešle **slowConsumerAlert** ValueMetric ke službě Log Analytics. |
| Typ = CF_CounterEvent_CL Job_s = nozzle Name_s = ztracené Delta_d události > 0              | Počet výsledků > 0   | Pokud počet delta ke ztrátě událostí, které dosáhne prahové hodnoty, znamená to, že nozzle máte problém se spuštěním. |

## <a name="scale"></a>Měřítko

Je možné škálovat Nozzle a loggregator.

### <a name="scale-the-nozzle"></a>Škálování Nozzle

Měli byste začít s aspoň dvě instance Nozzle. Firehose distribuuje zatížení napříč všemi instancemi Nozzle.
Pokud chcete mít jistotu, Nozzle je dokáže dodat s přenosy dat z firehose, nastavte **slowConsumerAlert** upozornění (uvedené v předchozí části "Vytvoření pravidla upozornění"). Po upozornění můžete podle pokynů [pokyny pro pomalé Nozzle](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) k určení, jestli je škálování potřeba.
Vertikálně navýšit kapacitu Nozzle, pomocí aplikace správce nebo rozhraní příkazového řádku CF zvýšení čísla instance nebo prostředky paměti a disku pro Nozzle.

### <a name="scale-the-loggregator"></a>Škálování loggregator

Odešle Loggregator **LGR** zprávy protokolu případné problémy s procesem protokolování. Můžete monitorovat výstrahu, kterou chcete určit, zda loggregator musí vertikálně navyšovat kapacitu.
Vertikální navýšení kapacity loggregator, zvětšete velikost vyrovnávací paměti Doppler nebo přidat dalšího serveru pro Doppler instance v manifestu CF. Další informace najdete v tématu [pokyny pro škálování loggregator](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Aktualizace

Aktualizovat Nozzle s novější verzí, stáhněte si novou verzi Nozzle, postupujte podle kroků v předchozí části "Nasazení the Nozzle" a nasdílejte aplikaci znovu.

### <a name="remove-the-nozzle-from-ops-manager"></a>Odebrání správce operací Nozzle

1. Přihlaste se na správce Ops.
2. Vyhledejte **Microsoft Azure Log Analytics Nozzle pro PCF** dlaždici.
3. Vyberte ikonu uvolňování paměti a potvrďte odstranění.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Odeberte Nozzle z vývojového počítače

V okně rozhraní příkazového řádku CF zadejte:
```
cf delete <App Name> -r
```

Pokud odeberete Nozzle, se automaticky odeberou data v portálu OMS. Vypršení platnosti podle nastavení uchovávání dat Log Analytics.

## <a name="support-and-feedback"></a>Podpora a zpětná vazba

Azure Log Analytics Nozzle je open source. Posílat dotazy a zpětnou vazbu, která [části GitHub](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues). Otevřete žádost o podporu Azure, zvolte "Virtuální počítač s Cloud Foundry" jako kategorie služby. 

## <a name="next-step"></a>Další krok

Z PCF2.0 jsou metriky výkonu virtuálních počítačů k Azure log analytics nozzle přenáší systémové metriky předávání a integrovat do pracovního prostoru OMS. Už nepotřebujete agenta OMS pro metriky výkonu virtuálních počítačů. Agenta OMS ale můžete použít ke shromažďování informací Syslog. Agenta OMS je nainstalován jako doplněk Bosh k vašim virtuálním počítačům CF. 

Podrobnosti najdete v tématu [agenta OMS nasazení do nasazení Cloud Foundry](https://github.com/Azure/oms-agent-for-linux-boshrelease).
