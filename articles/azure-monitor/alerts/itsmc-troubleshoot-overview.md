---
title: Řešení problémů v ITSMC
description: Naučte se řešit běžné problémy ve službě IT Service Management Connector.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 991d37b097587e8e4a524c4fc8bc21e9b6af20b5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100610439"
---
# <a name="troubleshoot-problems-in-it-service-management-connector"></a>Řešení problémů v konektoru pro správu služeb IT

Tento článek popisuje běžné problémy v oddělení IT Service Management Connector (ITSMC) a jeho řešení.

Azure Monitor aktivně upozorní na výstrahy, když najde důležité podmínky v datech monitorování. Tyto výstrahy vám pomůžou identifikovat a řešit problémy předtím, než si ji uživatelé vašeho systému všimnete.

Můžete vybrat, jak chcete dostávat upozornění. Můžete zvolit poštu, SMS nebo Webhook, nebo dokonce automatizovat řešení. 

Alternativou je oznámení prostřednictvím ITSMC. ITSMC nabízí možnost odesílat výstrahy do externího systému lístků, jako je například ServiceNow.

## <a name="use-the-dashboard-to-analyze-incident-and-change-request-data"></a>Pomocí řídicího panelu můžete analyzovat data o incidentech a žádostech o změnu.

V závislosti na konfiguraci při nastavování připojení se ITSMC může synchronizovat až 120 dní a data žádosti o změnu. Pokud chcete získat schéma záznamu protokolu pro tato data, podívejte se na [data synchronizovaná v článku o produktu ITSM](./itsmc-synced-data.md) .

Pomocí řídicího panelu ITSMC můžete vizualizovat incident a data žádosti o změnu.

![Snímek obrazovky zobrazující řídicí panel ITSMC](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Řídicí panel také poskytuje informace o stavu konektoru. Tyto informace můžete použít jako výchozí bod k analýze problémů s připojením. Další informace najdete v tématu o [chybě při šetření pomocí řídicího panelu](./itsmc-dashboard.md).

## <a name="use-service-map-to-visualize-incidents"></a>Použití Service Map k vizualizaci incidentů

Můžete také vizualizovat incidenty synchronizované s ovlivněnými počítači v Service Map.

Service Map automaticky zjišťuje komponenty aplikací v systémech Windows a Linux a mapuje komunikaci mezi službami. Umožňuje vám zobrazit vaše servery podle toho, jak jste si je rozmysleli: jako propojené systémy, které poskytují důležité služby. 

Service Map zobrazuje připojení mezi servery, procesy a porty v rámci libovolné architektury připojené k protokolu TCP. Kromě instalace agenta není nutná žádná konfigurace. Další informace najdete v tématu [použití Service map](../vm/service-map.md).

Pokud používáte Service Map, můžete zobrazit položky oddělení služeb vytvořené v řešeních správy služeb IT (ITSM), jak je znázorněno v tomto příkladu:

![Snímek obrazovky zobrazující Log Analytics obrazovku](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="resolve-problems"></a>Řešení problémů

V následujících částech najdete běžné příznaky, možné příčiny a řešení. 

### <a name="a-connection-to-the-itsm-system-fails-and-you-get-an-error-in-saving-connection-message"></a>Připojení k ITSM systému se nepovede a zobrazí se zpráva "Chyba při ukládání připojení".

**Příčina**: příčinou může být jedna z následujících možností:

* Přihlašovací údaje jsou nesprávné.
* Oprávnění nejsou dostatečná.
* Webová aplikace byla nesprávně nasazena.

**Řešení**:

* Pro připojení ServiceNow, Cherwell a prov:
  * Ujistěte se, že jste správně zadali uživatelské jméno, heslo, ID klienta a tajný klíč klienta pro každé připojení.  
  * V případě ServiceNow se ujistěte, že máte [dostatečná oprávnění](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role) k odpovídajícímu produktu ITSM.

* Pro Service Manager připojení:  
  * Ujistěte se, že je webová aplikace úspěšně nasazená a že je vytvořené hybridní připojení. Pokud chcete ověřit, jestli se úspěšně navázalo připojení k místnímu Service Managermu počítači, vyhledejte adresu URL webové aplikace, jak je popsáno v [dokumentaci pro vytvoření hybridního připojení](./itsmc-connections-scsm.md#configure-the-hybrid-connection).  

### <a name="duplicate-work-items-are-created"></a>Vytvoří se duplicitní pracovní položky.

**Příčina**: příčinou může být jedna z těchto dvou možností:

* Pro výstrahu je definována více než jedna akce ITSM.
* Upozornění je vyřešeno.

**Řešení**: může existovat dvě řešení:

* Ujistěte se, že máte jednu skupinu akcí ITSM na jednu výstrahu.
* ITSMC nepodporuje aktualizace stavu pracovních položek při vyřešení výstrahy. Vytvořte novou vyřešenou pracovní položku.

### <a name="work-items-are-not-created"></a>Pracovní položky nejsou vytvořeny

**Příčina**: Tento příznak může mít několik příčin:

* Kód byl změněn na straně ServiceNow.
* Oprávnění jsou nesprávně nakonfigurovaná.
* Omezení rychlosti ServiceNow jsou příliš vysoká nebo příliš nízká.
* Platnost obnovovacího tokenu vypršela.
* ITSMC se odstranil.

**Řešení**: Zkontrolujte [řídicí panel](itsmc-dashboard.md) a zkontrolujte chyby v části stav konektoru. Pak zkontrolujte [běžné chyby a jejich řešení](itsmc-dashboard-errors.md).

### <a name="you-cant-create-an-itsm-action-for-an-action-group"></a>Akci ITSM nejde vytvořit pro skupinu akcí.

**Příčina**: nově vytvořená instance ITSMC ještě pro dokončení počáteční synchronizace.

**Řešení**: Projděte si [běžné chyby a jejich řešení](itsmc-dashboard-errors.md).