---
title: Řešení potíží v ITSM konektoru
description: Řešení potíží s konektorem pro správu služeb IT
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: fce20626d5e000c08b8a057671c06a3084534187
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896032"
---
# <a name="troubleshooting-problems-in-itsm-connector"></a>Řešení potíží v ITSM konektoru

Tento článek popisuje běžné problémy v konektoru ITSM a jejich řešení.

Azure Monitor výstrahy proaktivně upozorní na to, že se ve vašich datech monitorování nacházejí důležité podmínky. Umožňují identifikovat a řešit problémy předtím, než si ji uživatelé vašeho systému všimnete.
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

## <a name="common-symptoms---how-should-it-be-resolved"></a>Běžné příznaky – jak by se mělo vyřešit?

Níže uvedený seznam obsahuje běžné příznaky a jejich řešení:

* **Příznak**: v případě, že se připojení nepřipojí k systému ITSM a při **ukládání zprávy připojení** se zobrazí chyba.

    **Příčina**: příčinou může být jedna z možností:
    * Nesprávné přihlašovací údaje
     * Nedostatečná oprávnění
     * Webová aplikace by se měla správně nasadit.

    **Řešení**:
    * Pro připojení ServiceNow, Cherwell a prov:
        * Ujistěte se, že jste správně zadali uživatelské jméno, heslo, ID klienta a tajný klíč klienta pro každé připojení.  
        * ServiceNow: Ujistěte se, že máte v odpovídajícím produktu ITSM dostatečná oprávnění k tomu, aby bylo připojení [zadáno](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role).
  * Pro Service Manager připojení:  
      * Ujistěte se, že je webová aplikace úspěšně nasazená a že je vytvořené hybridní připojení. Pokud chcete ověřit, jestli se úspěšně navázalo připojení k místnímu Service Managermu počítači, přečtěte si adresu URL webové aplikace, jak je popsáno v dokumentaci pro vytvoření [hybridního připojení](./itsmc-connections-scsm.md#configure-the-hybrid-connection).  
* **Příznak**: jsou vytvořeny duplicitní pracovní položky.

    **Příčina**: příčinou může být jedna ze dvou možností:
    * Pro výstrahu je definována více než jedna akce ITSM.
    * Upozornění je vyřešeno.

    **Řešení**: může existovat dvě řešení:
    * Ujistěte se, že máte jednu skupinu akcí ITSM na jednu výstrahu.
    * Konektor ITSM nepodporuje aktualizaci stavu pracovních položek při vyřešení výstrahy. Je vytvořena nová vyřešená pracovní položka.
* **Příznak**: pracovní položky nejsou vytvořeny.

    **Příčina**: Tento příznak může být z několika důvodů:
    * Úprava kódu na straně ServiceNow
    * Nesprávná konfigurace oprávnění
    * Omezení rychlosti ServiceNow jsou příliš vysoká/nízká.
    * Platnost tokenu aktualizace vypršela.
    * Konektor ITSM se odstranil.

    **Řešení**: [řídicí panel](itsmc-dashboard.md) můžete zkontrolovat a zkontrolovat chyby v části stav konektoru. Projděte si [běžné chyby](itsmc-dashboard-errors.md) a zjistěte, jak chybu vyřešit.

* **Příznak**: Nejde vytvořit akci ITSM pro skupinu akcí.

    **Příčina**: u nově vytvořeného KONEKTORu ITSM se ještě dokončila počáteční synchronizace.

    **Řešení**: můžete si prohlédnout [běžné chyby uživatelského rozhraní](itsmc-dashboard-errors.md#ui-common-errors) a zjistit, jak chybu vyřešit.