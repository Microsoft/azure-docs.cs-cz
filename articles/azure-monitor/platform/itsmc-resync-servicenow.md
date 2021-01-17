---
title: Ruční oprava problémů s synchronizací ServiceNow
description: Resetujte připojení na ServiceNow, takže výstrahy v Microsoft Azure můžou znovu volat ServiceNow.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 2f0889732ac756e16465659ee2e5b52335d1148f
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539262"
---
# <a name="troubleshooting-problems-in-itsm-connector"></a>Řešení potíží v ITSM konektoru

Tento článek popisuje běžné problémy v konektoru ITSM a jejich řešení.

Azure Monitor výstrahy proaktivně upozorní na to, že se ve vašich datech monitorování nacházejí důležité podmínky. Umožňují identifikovat a řešit problémy předtím, než si ji uživatelé vašeho systému všimnete. Další informace o upozorňování najdete v tématu Přehled výstrah v Microsoft Azure.
Zákazník může vybrat, jak se má upozornit na výstrahu, ať už se jedná o poštu, SMS, Webhook, nebo i k automatizaci řešení. Další možností upozorňování je použití ITSM.
ITSM poskytuje možnost odesílat výstrahy do externího systému lístků, jako je například ServiceNow.

## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Vizualizujte a analyzujte data o incidentech a žádostech o změnu.

V závislosti na konfiguraci při nastavování připojení se ITSMC může synchronizovat až 120 dní a data žádosti o změnu. Schéma záznamu protokolu pro tato data je k dispozici v [části Další informace](./itsmc-synced-data.md) v tomto článku.

Pomocí řídicího panelu ITSMC můžete vizualizovat incident a data žádosti o změnu.

![Snímek obrazovky zobrazující řídicí panel ITSMC](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Řídicí panel také poskytuje informace o stavu konektoru, který můžete použít jako výchozí bod k analýze problémů s připojením.

Chcete-li získat další informace o šetření řídicího panelu, přečtěte si téma [šetření chyb pomocí řídicího panelu](./itsmc-dashboard.md).

### <a name="service-map"></a>Mapa služeb

Můžete také vizualizovat incidenty synchronizované s ovlivněnými počítači v Service Map.

Service Map automaticky zjišťuje komponenty aplikací v systémech Windows a Linux a mapuje komunikaci mezi službami. Umožňuje vám zobrazit vaše servery podle toho, jak jste si je rozmysleli: jako propojené systémy, které poskytují důležité služby. Service Map zobrazuje připojení mezi servery, procesy a porty v rámci libovolné architektury připojené k protokolu TCP. Kromě instalace agenta není nutná žádná konfigurace. Další informace najdete v tématu [použití Service map](../insights/service-map.md).

Pokud používáte Service Map, můžete zobrazit položky oddělení služeb vytvořené v řešeních ITSM, jak je znázorněno zde:

![Snímek obrazovky zobrazující Log Analytics obrazovku](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="troubleshoot-itsm-connections"></a>Řešení potíží s připojením ITSM

- Pokud se připojení nepřipojí k systému ITSM a při ukládání zprávy o připojení dojde k **chybě** , proveďte následující kroky:
   - Pro připojení ServiceNow, Cherwell a prov:  
     - Ujistěte se, že jste správně zadali uživatelské jméno, heslo, ID klienta a tajný klíč klienta pro každé připojení.  
     - Ujistěte se, že máte v odpovídajícím ITSM produktu dostatečná oprávnění pro připojení.  
   - Pro Service Manager připojení:  
     - Ujistěte se, že je webová aplikace úspěšně nasazená a že je vytvořené hybridní připojení. Pokud chcete ověřit, jestli se úspěšně navázalo připojení k místnímu Service Managermu počítači, přečtěte si adresu URL webové aplikace, jak je popsáno v dokumentaci pro vytvoření [hybridního připojení](./itsmc-connections-scsm.md#configure-the-hybrid-connection).  

- Pokud se data z ServiceNow nesynchronizují Log Analytics, ujistěte se, že instance ServiceNow není v režimu spánku. Instance ServiceNow dev někdy přecházejí do režimu spánku, pokud jsou nečinné po dlouhou dobu. Pokud to neudělá, nahlaste problém.
- Pokud se Log Analytics výstrahy aktivují, ale pracovní položky se nevytvoří v produktu ITSM, pokud se položky konfigurace nevytvoří/propojí s pracovními položkami nebo další informace, podívejte se na tyto zdroje:
   -  ITSMC: řešení zobrazuje souhrn připojení, pracovních položek, počítačů a dalších. Vyberte dlaždici, která má popisek **stavu konektoru** . Provedete to tak, že přejdete do **protokolu hledání** pomocí příslušného dotazu. Další informace najdete v záznamech protokolu s `LogType_S` `ERROR` .
   - Stránka **hledání v protokolu** : zobrazí chyby a související informace přímo pomocí dotazu `*ServiceDeskLog_CL*` .

### <a name="troubleshoot-service-manager-web-app-deployment"></a>Řešení potíží s nasazením Service Manager Web App

-   Pokud máte problémy s nasazením webové aplikace, ujistěte se, že máte oprávnění k vytváření a nasazování prostředků v rámci předplatného.
-   Pokud získáte **odkaz na objekt není nastaven na instanci objektu** při spuštění [skriptu](itsmc-service-manager-script.md), ujistěte se, že jste zadali platné hodnoty v části **Konfigurace uživatele** .
-   Pokud se vám nepodaří vytvořit obor názvů služby Service Bus Relay, ujistěte se, že je v předplatném registrovaný požadovaný poskytovatel prostředků. Pokud není zaregistrované, ručně vytvořte obor názvů služby Service Bus Relay z Azure Portal. Můžete ho také vytvořit při [vytváření hybridního připojení](./itsmc-connections-scsm.md#configure-the-hybrid-connection) v Azure Portal.

### <a name="how-to-manually-fix-sync-problems"></a>Ruční oprava problémů s synchronizací

Azure Monitor se můžou připojit k poskytovatelům IT služeb třetích stran (ITSM). ServiceNow je jedním z těchto poskytovatelů.

Z bezpečnostních důvodů možná budete muset aktualizovat ověřovací token, který se používá pro připojení ke službě ServiceNow.
K opětovné aktivaci připojení a aktualizaci tokenu použijte následující proces synchronizace:


1. Vyhledejte řešení v horním banneru hledání a pak vyberte relevantní řešení.

    ![Snímek obrazovky, který zobrazuje horní hlavičku hledání a kde vybrat relevantní řešení.](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. Na obrazovce řešení zvolte v filtru předplatného možnost Vybrat vše a potom filtr podle "partnera".

    ![Snímek obrazovky, který ukazuje, kde vybrat vše a kde filtrovat podle partnera](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Vyberte řešení pro připojení ITSM.
1. V nápisu vlevo vyberte připojení ITSM.

    ![Snímek obrazovky, který ukazuje, kde vybrat připojení ITSM](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Vyberte jednotlivé konektory ze seznamu. 
    1. Klikněte na název konektoru, aby se nakonfiguroval.
    1. Odstranit všechny konektory, které se už nepoužívají

    1. Aktualizujte pole podle [těchto definic](./itsmc-connections.md) na základě typu partnera.

    1. Klikněte na synchronizovat.

       ![Snímek obrazovky, který zvýrazní tlačítko synchronizovat.](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Klikněte na Uložit.

        ![Nové připojení](media/itsmc-resync-servicenow/save-8bit.png)

f.    Zkontrolujte oznámení a zjistěte, jestli proces začal.
