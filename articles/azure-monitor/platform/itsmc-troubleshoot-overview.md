---
title: Řešení potíží v ITSM konektoru
description: Řešení potíží s konektorem pro správu služeb IT
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 2ffe7c8994d32917a08896c7d25f20d4adf09066
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98601901"
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

- Pokud se Log Analytics výstrahy aktivují, ale pracovní položky se nevytvoří v produktu ITSM, pokud se položky konfigurace nevytvoří/propojí s pracovními položkami nebo další informace, podívejte se na tyto zdroje:
   -  ITSMC: řešení zobrazuje [Souhrn připojení](itsmc-dashboard.md), pracovních položek, počítačů a dalších. Vyberte dlaždici, která má popisek **stavu konektoru** . Provedete to tak, že přejdete do **protokolu hledání** pomocí příslušného dotazu. Další informace najdete v záznamech protokolu s `LogType_S` `ERROR` .
   Můžete zobrazit podrobnosti o zprávách v tabulce – [tady](itsmc-dashboard-errors.md).
   - Stránka **hledání v protokolu** : zobrazí chyby a související informace přímo pomocí dotazu `*ServiceDeskLog_CL*` .

## <a name="common-symptoms---how-it-should-be-resolved"></a>Běžné příznaky – jak by se mělo vyřešit?

Níže uvedený seznam obsahuje běžné příznaky a jejich řešení:

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