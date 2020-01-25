---
title: Shromažďovat protokol aktivit Azure s nastavením diagnostiky (Preview) – Azure Monitor | Microsoft Docs
description: Pomocí nastavení diagnostiky předáte protokoly aktivit Azure, abyste Azure Monitor protokoly, úložiště Azure nebo Azure Event Hubs.
author: bwren
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 01/21/2020
ms.openlocfilehash: dff4901f1488406ed1259d1411a6b05b949382cb
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715841"
---
# <a name="update-to-azure-activity-log-collection-and-export"></a>Aktualizace shromažďování a exportu protokolů aktivit Azure
[Protokol aktivit Azure](platform-logs-overview.md) je [protokol platformy](platform-logs-overview.md) , který poskytuje přehled o událostech na úrovni předplatného, ke kterým došlo v Azure. Metoda odeslání položek protokolu aktivit do [centra událostí nebo účtu úložiště](activity-log-export.md) nebo do [pracovního prostoru Log Analytics](activity-log-collect.md) se změnila na použití [nastavení diagnostiky](diagnostic-settings.md). Tento článek popisuje rozdíl mezi metodami a vymazáním nastavení starší verze v přípravě na možnost změnit nastavení diagnostiky.


## <a name="differences-between-methods"></a>Rozdíly mezi metodami

### <a name="advantages"></a>Výhody
Použití nastavení diagnostiky má oproti aktuálním metodám následující výhody:

- Konzistentní způsob shromažďování všech protokolů platforem.
- Shromažďování protokolů aktivit napříč několika předplatnými a klienty.
- Vyfiltruje shromažďování, aby se shromáždily jenom protokoly pro konkrétní kategorie.
- Shromáždí všechny kategorie protokolů aktivit. Některé kategorie nejsou shromažďovány pomocí starší metody.
- Rychlejší latence pro přijímání protokolů. Předchozí metoda má latenci přibližně 15 minut, zatímco nastavení diagnostiky se přidá pouze přibližně 1 minutu.

### <a name="considerations"></a>Požadavky
Než tuto funkci povolíte, zvažte následující podrobnosti o shromažďování protokolů aktivit pomocí nastavení diagnostiky.

- Nastavení uchování pro shromažďování protokolu aktivit do služby Azure Storage bylo odebráno, což znamená, že data budou ukládána po neomezenou dobu, dokud je neodeberete.
- V současné době můžete pomocí Azure Portal vytvořit jenom nastavení diagnostiky na úrovni předplatného. Chcete-li použít jiné metody, jako je například PowerShell nebo CLI, můžete vytvořit šablonu Správce prostředků.


### <a name="differences-in-data"></a>Rozdíly v datech
Nastavení diagnostiky shromažďuje stejná data jako předchozí metody použité ke shromáždění protokolu aktivit s následujícími rozdíly:

Následující sloupce byly odebrány. Náhrada pro tyto sloupce je v jiném formátu, takže možná budete muset upravit dotazy protokolů, které je používají. Ve schématu můžete stále vidět odebrané sloupce, ale nebudou naplněny daty.

| Odebraný sloupec | Sloupec pro nahrazení |
|:---|:---|
| ActivityStatus    | Položka ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

Byl přidán následující sloupec:

- Authorization_d
- Claims_d
- Properties_d

## <a name="work-with-legacy-settings"></a>Práce se staršími nastaveními
Nastavení starší verze pro shromažďování protokolu aktivit budou fungovat i v případě, že se nerozhodnete nahradit nastavením diagnostiky. Pomocí následující metody můžete spravovat profil protokolu pro předplatné.

1. V nabídce **Azure monitor** v Azure Portal vyberte **Protokol aktivit**.
3. Klikněte na **Nastavení diagnostiky**.

   ![Nastavení diagnostiky](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Klikněte na fialový banner pro starší verze prostředí.

    ![Starší verze prostředí](media/diagnostic-settings-subscription/legacy-experience.png)


Podrobnosti o používání starších metod shromažďování najdete v následujících článcích.

- [Shromažďování a analýza protokolů aktivit Azure v pracovním prostoru Log Analytics v Azure Monitor](activity-log-collect.md)
- [Shromažďování protokolů aktivit Azure do Azure Monitor napříč klienty Azure Active Directory](activity-log-collect-tenants.md)
- [Export protokolu aktivit Azure do úložiště nebo do Azure Event Hubs](activity-log-export.md)

## <a name="disable-existing-settings"></a>Zakázat existující nastavení
Existující kolekci aktivity byste měli zakázat předtím, než ji povolíte pomocí nastavení diagnostiky. U obou povolených může být výsledkem duplicitní data.

### <a name="disable-collection-into-log-analytics-workspace"></a>Zakázat shromažďování do Log Analyticsho pracovního prostoru

1. Otevřete nabídku **Log Analytics pracovní prostory** v Azure Portal a vyberte pracovní prostor pro shromáždění protokolu aktivit.
2. V části **zdroje dat pracovního prostoru** v nabídce pracovního prostoru vyberte **Protokol aktivit Azure**.
3. Klikněte na předplatné, které chcete odpojit.
4. Po zobrazení výzvy k potvrzení výběru klikněte na **Odpojit** a pak na **Ano** .

### <a name="disable-log-profile"></a>Zakázat profil protokolu

1. Pomocí postupu popsaného v tématu [práce se staršími verzemi](#work-with-legacy-settings) otevřete nastavení starší verze.
2. Zakažte všechny aktuální kolekce do úložiště nebo Center událostí.



## <a name="activity-log-monitoring-solution"></a>Řešení monitorování protokolu aktivit
Řešení Azure Log Analytics monitoring zahrnuje několik dotazů a zobrazení protokolů pro analýzu záznamů protokolu aktivit ve vašem pracovním prostoru Log Analytics. Toto řešení využívá data protokolu shromážděná v pracovním prostoru Log Analytics a bude i nadále fungovat bez jakýchkoli změn, pokud shromáždíte protokol aktivit pomocí nastavení diagnostiky. Podrobnosti o tomto řešení najdete v tématu [řešení monitorování analýzy protokolů aktivit](activity-log-collect.md#activity-logs-analytics-monitoring-solution) .

## <a name="next-steps"></a>Další kroky

* [Další informace o protokolu aktivit](../../azure-resource-manager/management/view-activity-logs.md)
* [Další informace o nastavení diagnostiky](diagnostic-settings.md)
